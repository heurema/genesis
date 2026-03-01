# Genesis Reference

## Command Syntax

```
/genesis "<direction>" [--preset quick|standard|deep] [--strategy bootstrapper|growth|moat|uniform]
          [--discover]
          [--rounds N] [--population N] [--elite N]
          [--budget N] [--timeline N] [--skills LIST]
          [--must LIST] [--no LIST] [--solo]
```

The direction argument is required. If omitted, the command asks for it interactively.

## Flags

### Positional

| Argument | Description |
|----------|-------------|
| `"direction"` | Idea domain or direction to explore. Any text that is not a flag. Examples: `"AI tools for solo devs"`, `"B2B SaaS for restaurants"`, `"developer productivity"` |

### Speed Presets

| Flag | Values | Default |
|------|--------|---------|
| `--preset` | `quick`, `standard`, `deep` | `standard` |

Presets control rounds, population size, and elite count:

| Preset | Rounds | Population | Elite |
|--------|--------|------------|-------|
| `quick` | 1 | 5 | 2 |
| `standard` | 2 | 8 | 3 |
| `deep` | 3 | 12 | 4 |

### Strategy Presets

| Flag | Values | Default |
|------|--------|---------|
| `--strategy` | `bootstrapper`, `growth`, `moat`, `uniform` | `uniform` |

Strategies shift scoring weights across 8 dimensions:

| Strategy | Emphasis | Top-weighted dimensions |
|----------|----------|------------------------|
| `bootstrapper` | Speed to revenue | feasibility (0.20), speed_to_value (0.20) |
| `growth` | Scale potential | market_size (0.20), distribution (0.20) |
| `moat` | Defensibility | differentiation (0.20), moats (0.20) |
| `uniform` | Balanced | all dimensions equal (0.125) |

### Discovery Mode

| Flag | Description |
|------|-------------|
| `--discover` | Runs a 5-question interactive preflight before evolution begins. Asks one question at a time via `AskUserQuestion`. Maps answers to constraint flags automatically. Use this if you want to set constraints without memorizing flag syntax. |

Discovery questions:
1. Budget — maps to `--budget`
2. Timeline to first revenue — maps to `--timeline`
3. Skills you can build yourself — maps to `--skills`
4. Solo or team — maps to `--solo`
5. Dealbreakers — maps to `--no`

If you provide explicit constraint flags alongside `--discover`, the explicit flags take precedence.

### Override Flags

These override the values set by `--preset`:

| Flag | Description |
|------|-------------|
| `--rounds N` | Number of evolution rounds |
| `--population N` | Ideas generated per round |
| `--elite N` | Ideas that survive selection each round |

### Constraint Flags

All constraints are enforced as hard requirements. Ideas violating any constraint are eliminated by the ARBITER before refinement.

| Flag | Description | Example |
|------|-------------|---------|
| `--budget N` | Maximum build budget in USD | `--budget 500` |
| `--timeline N` | Weeks to first revenue | `--timeline 4` |
| `--skills LIST` | Comma-separated founder skills | `--skills "python,react"` |
| `--must LIST` | Required elements in the idea | `--must "api,b2b"` |
| `--no LIST` | Forbidden elements | `--no "regulated,marketplace"` |
| `--solo` | Must be buildable and operable by one person | `--solo` |

## Usage Examples

Explore a direction with defaults:

```
/genesis "AI tools for solo developers"
```

Quick scan — fastest run, fewest agents calls:

```
/genesis "productivity tools" --preset quick
```

Deep exploration with bootstrapper weighting — best for revenue-first founders:

```
/genesis "B2B SaaS for restaurants" --preset deep --strategy bootstrapper
```

Guided constraint setup via 5 questions:

```
/genesis "developer tools" --discover
```

Full explicit constraints — no discovery needed:

```
/genesis "fintech tools" --budget 500 --timeline 4 --skills "python,react" --solo --no "regulated,marketplace"
```

Growth strategy focused on scale potential:

```
/genesis "consumer productivity" --strategy growth --preset standard
```

Override preset population while keeping preset rounds and elite:

```
/genesis "SaaS ideas" --preset standard --population 12
```

## Scoring Dimensions

The Critic scores every idea on these 8 dimensions. Scores are integers 0-10.

| Dimension | What it measures | 0 | 10 |
|-----------|-----------------|---|----|
| `feasibility` | Can a solo dev build the MVP? | Requires a large team | One person, one weekend |
| `speed_to_value` | Time to first paying customer | Years | Days |
| `differentiation` | Uniqueness vs existing solutions | Exact clone | Nothing like it |
| `market_size` | Revenue potential ceiling | <$10k/year TAM | $10M+ TAM |
| `distribution` | Organic growth channels | No obvious channel | Built-in virality |
| `moats` | Defensibility over time | Zero switching costs | Strong network effects |
| `risk` | Inverse of failure probability | Almost certain to fail | Very safe bet |
| `clarity` | How well-defined the concept is | Vague and handwavy | Crystal-clear spec |

## Output Format

Genesis prints a structured summary to the terminal and writes the same content to `.genesis/runs/<run-id>/summary.md`.

### Terminal Output Structure

```
## Winner: <title>

Score: <weighted>/10 | Strategy: <strategy> | Rounds: <N>

ICP: <ideal customer profile>
Problem: <pain being solved>
Wedge: <initial entry point>
Monetization: <business model>
Distribution: <primary channel>

### MVP Scope
<3-feature maximum scope description>

### Why It Won
- <top dimension> (<score>/10): <one sentence>
- <dimension 2> (<score>/10): <one sentence>
- <dimension 3> (<score>/10): <one sentence>

### Validation Experiments
1. <cheapest test>
2. <second test>
...

### Week-1 Execution Plan
1. <concrete task>
2. <concrete task>
3. <concrete task>

### Tech Stack
- <technology>
- <technology>
...

### Risk Factors
- Most likely failure: <failure_reason from scorecard>
- Kill criteria: <criteria or "None identified">

---

## Runner-Up: <title>

Score: <weighted>/10

<ICP, Problem, MVP Scope, top 2 experiments — condensed>

---

Run saved to .genesis/runs/<run-id>/
<N> ideas generated, <N> eliminated, <N> rounds of evolution
```

### Run Directory Contents

Each run creates a directory at `.genesis/runs/<run-id>/` containing:

| File | Contents |
|------|----------|
| `config.json` | Full resolved config: prompt, rounds, population, elite, strategy, weights, constraints |
| `state.json` | Latest round state: active IDs, eliminated IDs, best weighted score, iteration count |
| `final.json` | Winner and runner-up with genome, scorecard, weighted score, tech stack, week-1 tasks |
| `summary.md` | Human-readable final output (same as terminal output) |

Run IDs follow the format `run-YYYYMMDD-HHMMSS`.

## Troubleshooting

**The command asks for a direction even though I provided one.**
The direction must come before any flags. Flags are parsed by prefix (`--`); everything else is treated as the direction. Make sure the direction is quoted if it contains spaces: `/genesis "my direction" --preset quick`.

**An idea I expected to survive was eliminated.**
The ARBITER eliminates any idea where the Critic set `constraint_violation: true`. Check whether your constraints (`--budget`, `--no`, `--skills`, `--solo`, `--timeline`) are too restrictive for the direction. Run with `--discover` to calibrate constraints interactively, or remove constraint flags to see which ideas the algorithm prefers unconstrained.

**Run ends with "Generator failed to produce valid JSON after retry".**
The Generator agent produced invalid JSON twice in a row during the initial GENERATE phase, which is the only unrecoverable failure. This is rare. Try again — model output is non-deterministic and a retry usually succeeds.

**Output seems to converge to similar ideas across rounds.**
The diversity slot mechanism handles ICP overlap but cannot prevent all convergence. Use a higher `--population` value (`--population 15`) to increase genetic diversity, or try a different direction with more explicit variation in the prompt (e.g., add "explore different audiences and channels" to the direction string).

**I want to resume an interrupted run.**
Genesis does not currently support resuming a run. The `state.json` in the run directory captures the last completed round, but re-entry is not implemented. Start a new run with the same flags — quick preset recovers faster.

**The runner-up is very similar to the winner.**
This can happen on small populations where few ideas survive selection. Increase `--population` or use `--preset deep` to give the algorithm more material to work with.
