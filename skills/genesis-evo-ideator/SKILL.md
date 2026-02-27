---
name: genesis-evo-ideator
description: |
  Core orchestration skill for genesis — runs the memetic evolution loop.
  Dispatches to genesis-generator, genesis-critic, and genesis-refiner agents.
  Not user-invocable; called by /genesis command.
user-invocable: false
---

# Genesis Evolution Loop

You are the orchestrator for a memetic startup idea evolution. You receive a resolved config from the /genesis command and drive the full evolution loop.

## Input

You receive a config object with these fields (passed as part of the prompt from the command):

- `prompt`: user's idea direction (string)
- `rounds`: number of evolution rounds (int)
- `population`: ideas per generation (int)
- `elite`: how many top ideas survive each round (int)
- `strategy`: weight map name — one of bootstrapper, growth, moat, uniform
- `weights`: resolved 8-key weight map (object)
- `constraints`: object with optional keys: budget, timeline, skills, must, no, solo
- `discover`: boolean — whether interactive discovery was used

## Setup

1. Generate `run_id`: `run-YYYYMMDD-HHMMSS` (use current timestamp)
2. Create directory: `.genesis/runs/<run_id>/`
3. Write `config.json` with the full config object
4. Initialize tracking variables:
   - `all_ideas`: empty array
   - `all_scores`: empty object (idea_id → scorecard)
   - `active_ids`: empty array (currently competing ideas)
   - `eliminated_ids`: empty array
   - `iteration`: 0

## Evolution Loop

Run for `rounds` iterations (1-based). Each round follows this sequence:

### Round 1 Only: GENERATE

1. Read the generate prompt template from `@${CLAUDE_PLUGIN_ROOT}/lib/prompts/generate.md`
2. Build the prompt by replacing placeholders:
   - `{PROMPT}` → config.prompt
   - `{POPULATION_SIZE}` → config.population
   - `{CONSTRAINTS_BLOCK}` → formatted constraints (see Constraints Formatting below)
3. Dispatch to **genesis-generator** agent via Task tool:
   ```
   subagent_type: "genesis-generator"
   model: "sonnet"
   prompt: <built prompt>
   ```
4. **JSON Validation**: parse the response as a JSON array
   - If invalid: re-dispatch with prefix "Your previous output was not valid JSON. Output ONLY the JSON array, no markdown fences, no explanation."
   - If still invalid after 1 retry: STOP with error "Generator failed to produce valid JSON after retry"
5. Assign sequential IDs (`idea-001`, `idea-002`, ...) if not already set
6. Add all ideas to `all_ideas`, set `active_ids` to their IDs
7. Print: `[1/{rounds}] GENERATE: {count} ideas created`

### Every Round: CRITIQUE

1. Collect active ideas from `all_ideas` (filter by `active_ids`)
2. Read the critique prompt template from `@${CLAUDE_PLUGIN_ROOT}/lib/prompts/critique.md`
3. Build the prompt by replacing placeholders:
   - `{IDEAS_JSON}` → JSON.stringify(active ideas)
   - `{CONSTRAINTS_BLOCK}` → formatted constraints
   - `{WEIGHTS_BLOCK}` → formatted strategy weights with explanation: "These weights reflect the '{strategy}' strategy. Higher-weighted dimensions matter more."
4. Dispatch to **genesis-critic** agent via Task tool:
   ```
   subagent_type: "genesis-critic"
   model: "sonnet"
   prompt: <built prompt>
   ```
5. **JSON Validation**: parse the response as a JSON object (idea_id → scorecard)
   - Retry pattern same as GENERATE
   - If still invalid: use empty scores for this round, continue
6. Store all scorecards in `all_scores`
7. Print: `[{round}/{rounds}] CRITIQUE: {count} ideas scored`

### Every Round: ARBITER (in-skill, no agent)

The arbiter step is deterministic logic you execute directly — no agent dispatch.

1. **Enforce constraints**: for each active idea's scorecard, check `constraint_violation`. If `true` → move to `eliminated_ids`, remove from `active_ids`. Print count eliminated.

2. **Compute weighted scores**: for each remaining active idea:
   ```
   weighted_score = sum(scores[dim] * weights[dim]) for each dimension
   ```
   Store as `weighted_scores` map (idea_id → float).

3. **Rank**: sort active ideas by weighted_score descending.

4. **Select elites**: take top `elite` count.

5. **Diversity slot**: if the top 2 ideas share the same `icp` field, swap #2 with the next idea that has a different `icp`. This prevents premature convergence.

6. Update `active_ids` to selected elites only. Move all others to `eliminated_ids`.

7. Print: `[{round}/{rounds}] ARBITER: {eliminated} eliminated, {active} elite selected`

### Every Round: REFINE

For each elite idea (sequentially, one at a time):

1. Find the idea's scorecard from `all_scores`
2. Compute weak areas: sort the 8 dimensions by `score * weight` ascending, take bottom 3. Format as: "- {dimension}: scored {score}/10 (weight: {weight})"
3. Find `best_score`: max weighted_score among all active ideas this round
4. Read the refine prompt template from `@${CLAUDE_PLUGIN_ROOT}/lib/prompts/refine.md`
5. Build the prompt by replacing placeholders:
   - `{IDEA_JSON}` → JSON.stringify(idea)
   - `{SCORES_JSON}` → JSON.stringify(scorecard)
   - `{WEAK_AREAS}` → formatted weak areas string
   - `{BEST_SCORE}` → best_score as string
   - `{CONSTRAINTS_BLOCK}` → formatted constraints
6. Dispatch to **genesis-refiner** agent via Task tool:
   ```
   subagent_type: "genesis-refiner"
   model: "sonnet"
   prompt: <built prompt>
   ```
7. **JSON Validation**: parse the response as a JSON object with `genome`, `tech_stack`, `week1_tasks`, `changes` keys
   - Retry pattern same as GENERATE
   - If still invalid: keep original idea unchanged, continue
8. Add the refined idea (from `genome` field) to `all_ideas`. Store the full refiner output (with tech_stack, week1_tasks, changes) separately — you will need it for the final output.
9. Replace the original idea's ID in `active_ids` with the refined idea's ID

Print: `[{round}/{rounds}] REFINE: {count} ideas improved`

### Every Round: RE-SCORE

After refining, re-score the refined ideas:

1. Collect only the newly refined ideas
2. Dispatch to **genesis-critic** using the same critique prompt pattern, but with only the refined ideas
3. JSON validate + retry
4. Store new scorecards in `all_scores`
5. Print: `[{round}/{rounds}] RE-SCORE: {count} refined ideas scored`

### Round 2+ Only: VARIATE

1. Collect titles of all active ideas
2. Read the variate prompt template from `@${CLAUDE_PLUGIN_ROOT}/lib/prompts/variate.md`
3. Build the prompt by replacing placeholders:
   - `{PROMPT}` → config.prompt
   - `{EXISTING_TITLES}` → bullet list of active idea titles
   - `{CONSTRAINTS_BLOCK}` → formatted constraints
4. Dispatch to **genesis-generator** agent via Task tool:
   ```
   subagent_type: "genesis-generator"
   model: "sonnet"
   prompt: <built prompt>
   ```
5. JSON validate + retry
6. Assign sequential IDs continuing from the current max
7. Add new ideas to `all_ideas` and `active_ids`
8. Print: `[{round}/{rounds}] VARIATE: {count} fresh ideas added`

### End of Round

After each round, write `state.json` to the run directory with the current state:
```json
{
  "run_id": "<run_id>",
  "iteration": <current_round>,
  "active_ids": [...],
  "eliminated_ids": [...],
  "idea_count": <total>,
  "best_weighted_score": <float>
}
```

## Finalization

After all rounds complete:

1. **Final ranking**: compute weighted scores for all active ideas, sort descending
2. **Select winner and runner-up**: top 2 by weighted score
3. **Find their refiner outputs**: look up the tech_stack, week1_tasks, changes for the winner and runner-up

4. **Write final.json**:
```json
{
  "run_id": "<run_id>",
  "best_idea": { "genome": {...}, "scorecard": {...}, "weighted_score": N, "tech_stack": [...], "week1_tasks": [...] },
  "runner_up": { "genome": {...}, "scorecard": {...}, "weighted_score": N, "tech_stack": [...], "week1_tasks": [...] },
  "iterations": N,
  "stop_reason": "max_rounds",
  "total_ideas_generated": N,
  "eliminated_count": N
}
```

5. **Write summary.md** using the Final Output Format below.

6. **Print the final output** to the user (same content as summary.md).

## Final Output Format

```markdown
## Winner: {title}

**Score:** {weighted_score}/10 | **Strategy:** {strategy} | **Rounds:** {iterations}

**ICP:** {icp}
**Problem:** {pain}
**Wedge:** {wedge}
**Monetization:** {biz_model}
**Distribution:** {distribution}

### MVP Scope
{mvp_scope}

### Why It Won
{For each of the top 3 scoring dimensions: "- **{dimension}** ({score}/10): {one sentence why}"}

### Validation Experiments
{Numbered list from scorecard.derisk_experiments}

### Week-1 Execution Plan
{Numbered list from refiner.week1_tasks}

### Tech Stack
{Bullet list from refiner.tech_stack}

### Risk Factors
- **Most likely failure:** {scorecard.failure_reason}
- **Kill criteria:** {scorecard.kill_criteria joined, or "None identified"}

---

## Runner-Up: {title}

**Score:** {weighted_score}/10

{Same format as winner but condensed — ICP, Problem, MVP Scope, top 2 experiments}

---

*Run saved to `.genesis/runs/{run_id}/`*
*{total_ideas_generated} ideas generated, {eliminated_count} eliminated, {iterations} rounds of evolution*
```

## Constraints Formatting

When building the `{CONSTRAINTS_BLOCK}` placeholder, format active constraints as:

```
## Constraints

The following constraints are hard requirements. Ideas violating any constraint must be flagged.

{If budget:} - **Budget:** Maximum ${budget} USD
{If timeline:} - **Timeline:** Must reach first revenue within {timeline} weeks
{If skills:} - **Skills:** Founder can build with: {skills joined by ", "}
{If must:} - **Must include:** {must joined by ", "}
{If no:} - **Must NOT include:** {no joined by ", "}
{If solo:} - **Solo founder:** Must be buildable and operable by one person
```

If no constraints are active, use: `## Constraints\n\nNo specific constraints provided. Evaluate ideas on their own merit.`

## JSON Validation Pattern

After every agent dispatch, follow this pattern:

1. Read the agent's response text
2. Try to parse as JSON
   - Strip markdown code fences if present (```json ... ```)
   - Strip any text before the first `[` or `{` and after the last `]` or `}`
3. If valid JSON: proceed
4. If invalid: re-dispatch to the same agent with: "Your previous output was not valid JSON. Output ONLY the JSON, no markdown fences, no explanation. Here is what you need to produce: {brief description of expected format}"
5. If still invalid after 1 retry: log a warning, skip this phase or use fallback data, continue the loop

Never crash the evolution loop on a single JSON parse failure. Degrade gracefully.

## Important Rules

- **Agents are stateless**: each dispatch is independent. Pass all needed context in the prompt.
- **No history passing**: agents never see previous rounds. This prevents context rot.
- **Sequential refinement**: refine ideas one at a time, not in parallel. Each refinement is isolated.
- **File IO is your job**: agents produce JSON, you write all files to `.genesis/runs/`.
- **Progress updates**: print a status line after each phase so the user sees progress.
- **All state in run directory**: if the process is interrupted, `.genesis/runs/<run_id>/state.json` has the latest state.
