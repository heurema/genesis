# How Genesis Works

## Overview

Genesis applies a memetic algorithm to startup ideation. A memetic algorithm combines evolutionary selection (survival of the fittest) with local search (individual refinement). The three AI agents map directly onto these roles: Generator produces variation, Critic provides fitness evaluation, and Refiner performs local search on each survivor.

The orchestration lives in the `genesis-evo-ideator` skill, which is invoked by the `/genesis` command and drives the full loop. The skill is not user-invocable directly.

## Components

### /genesis command

The entry point. Parses all flags, resolves presets to concrete parameters, optionally runs the 5-question discovery preflight, prints the resolved config, and hands off a structured config object to the `genesis-evo-ideator` skill.

### genesis-evo-ideator skill

The orchestrator. Holds all mutable state: the idea pool, scorecard map, active and eliminated idea sets, and round counter. Dispatches to each agent via the Task tool, handles JSON validation and retries, runs the deterministic ARBITER step itself (no agent), writes all run files, and formats the final output. It is the only component with write access to `.genesis/runs/`.

### genesis-generator (Startup Visionary)

Model: claude-sonnet-4-6. Generates diverse startup idea genomes as a JSON array. Enforces a diversity rule: each idea must differ from the others in target audience, distribution channel, or problem addressed. Quality bar: every idea must be specific enough to start building in a weekend. Will never suggest marketplaces, social networks, or hardware products. Used in both GENERATE (round 1) and VARIATE (rounds 2+) phases.

### genesis-critic (Bootstrapper Analyst)

Model: claude-sonnet-4-6. Scores a batch of ideas and returns a JSON object keyed by idea ID. For each idea it produces a scorecard: integer scores (0-10) on all 8 dimensions, a `constraint_violation` boolean, `kill_criteria` array, `derisk_experiments` array, and a `failure_reason` string. Applies the provided strategy weights context when calibrating. A score of 7+ means genuinely good — the critic is calibrated to be strict.

### genesis-refiner (Pragmatic CTO)

Model: claude-sonnet-4-6. Takes one idea and its scorecard, identifies the 2-3 weakest dimensions by weighted score, and produces a refined genome plus `tech_stack`, `week1_tasks`, and a `changes` explanation. The refined idea gets a new ID (parent id + `-r{round}` suffix). Respects skills constraints — will not suggest technologies the founder cannot use.

## Data Flow

```
/genesis command
    → resolves config (presets + overrides + constraints)
    → [--discover] 5-question preflight via AskUserQuestion
    → invokes genesis-evo-ideator skill

genesis-evo-ideator
    → creates .genesis/runs/<run_id>/
    → writes config.json

For each round:
    [Round 1] GENERATE
        → genesis-generator → JSON array of N ideas
        → JSON validation + optional retry
        → ideas added to pool, active_ids set

    CRITIQUE
        → genesis-critic → JSON object (id → scorecard)
        → JSON validation + optional retry
        → scorecards stored in all_scores

    ARBITER (deterministic, no agent)
        → eliminate constraint violations
        → compute weighted scores per strategy weights
        → rank, select elite count
        → diversity slot: if top-2 share same ICP, swap #2 for next different ICP
        → update active_ids

    REFINE (sequential, one idea at a time)
        → for each elite: genesis-refiner → {genome, tech_stack, week1_tasks, changes}
        → JSON validation + optional retry
        → refined idea added to pool, active_ids updated

    RE-SCORE
        → genesis-critic on refined ideas only
        → scorecards updated in all_scores

    [Round 2+] VARIATE
        → genesis-generator with existing titles as exclusion context
        → fresh ideas added to pool and active_ids

    → writes state.json

Finalization
    → final ranking by weighted score
    → selects winner + runner-up
    → writes final.json
    → writes summary.md
    → prints final output
```

## Algorithm Details

### Weighted Scoring

The arbiter computes a weighted score for each idea:

```
weighted_score = sum(dimension_score[d] * weight[d]) for d in 8 dimensions
```

Scores are integers 0-10 from the critic. Weights are floats summing to 1.0, sourced from the resolved strategy map.

### Diversity Slot

To prevent premature convergence, the arbiter checks whether the top-2 ranked ideas share the same `icp` (ideal customer profile) field. If they do, it replaces the #2 slot with the highest-ranked idea that has a different ICP. This ensures the elite pool is not just variations of the same customer segment.

### VARIATE Injection

On rounds 2 and beyond, after refinement and re-scoring, the Generator is asked to produce fresh ideas distinct from all current active titles. These are injected into the active pool and scored in the next round's CRITIQUE phase. This prevents the algorithm from getting stuck in a local optimum.

### JSON Validation and Graceful Degradation

Every agent response goes through a validation pipeline: strip markdown fences → strip leading/trailing non-JSON text → attempt parse. On failure, the orchestrator retries once with an explicit correction prompt. If the second attempt also fails, the loop degrades gracefully: GENERATE failure aborts with an error, CRITIQUE failure uses empty scores for that round, REFINE failure keeps the original idea unchanged. The loop never crashes on a single bad response.

### Agent Isolation

Agents are stateless. Each dispatch receives a self-contained prompt with all needed context (ideas, scorecards, constraints, weights). Agents never see previous rounds or each other's outputs directly. This prevents context rot over long runs and keeps each agent's reasoning focused.

## Limitations

- Genesis does not parallelize agent calls. REFINE processes elite ideas sequentially. On deep presets with large populations, run time scales linearly with elite count per round.
- The Generator is permanently biased against marketplaces, social networks, and hardware — these exclusions are baked into the agent system prompt, not a runtime flag.
- JSON retry is limited to one attempt per phase. Persistent model output issues (e.g., model refusing to output raw JSON) will cause graceful degradation, not recovery.
- Run state is written to `.genesis/runs/` in the current working directory. If invoked from different directories, runs will not share a single location.
