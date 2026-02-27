---
name: genesis
description: Evolve startup/product ideas using a memetic algorithm with scoring, selection, and refinement
allowed-tools:
  - Read
  - Write
  - Bash
  - Task
  - Glob
  - AskUserQuestion
---

# /genesis — Startup Idea Evolution

Parse the user's arguments from `$ARGUMENTS` and run the genesis evolution loop.

## Argument Parsing

Parse `$ARGUMENTS` for:

**Positional (required):**
- `prompt` — the idea direction / domain to explore (everything not a flag)

**Speed preset:**
- `--preset quick|standard|deep` (default: `standard`)

**Strategy preset:**
- `--strategy bootstrapper|growth|moat` (default: `uniform`)

**Discovery mode:**
- `--discover` — run interactive preflight before evolution

**Override flags (override preset values):**
- `--rounds N` — number of evolution rounds
- `--population N` — ideas per generation
- `--elite N` — how many top ideas survive per round

**Constraint flags:**
- `--budget N` — max budget in USD
- `--timeline N` — max weeks to first revenue
- `--skills LIST` — founder skills (comma-separated)
- `--must LIST` — required elements (comma-separated)
- `--no LIST` — forbidden elements (comma-separated)
- `--solo` — must be solo-buildable

If no prompt is provided, ask the user: "What direction should I explore? (e.g., 'AI tools for solo devs', 'B2B SaaS for restaurants')"

## Preset Resolution

Resolve the speed preset to concrete parameters:

| Preset | rounds | population | elite |
|--------|--------|-----------|-------|
| quick | 2 | 4 | 2 |
| standard | 3 | 6 | 2 |
| deep | 5 | 8 | 3 |

Apply any `--rounds`, `--population`, `--elite` overrides on top of the preset.

## Strategy Resolution

Read strategy weight maps from `@${CLAUDE_PLUGIN_ROOT}/lib/schemas/dimensions.md`.

Resolve the strategy name to the corresponding full 8-key weight map:

- `bootstrapper` → feasibility: 0.20, speed_to_value: 0.20, differentiation: 0.10, market_size: 0.10, distribution: 0.15, moats: 0.05, risk: 0.05, clarity: 0.15
- `growth` → feasibility: 0.10, speed_to_value: 0.10, differentiation: 0.10, market_size: 0.20, distribution: 0.20, moats: 0.10, risk: 0.05, clarity: 0.15
- `moat` → feasibility: 0.10, speed_to_value: 0.05, differentiation: 0.20, market_size: 0.15, distribution: 0.10, moats: 0.20, risk: 0.05, clarity: 0.15
- `uniform` (default) → all dimensions: 0.125

## Discovery Mode

If `--discover` is set and the user has NOT provided explicit constraint flags, run a 5-question interactive preflight using AskUserQuestion. Ask ONE question at a time:

1. **Budget:** "What's your budget for building the MVP?"
   - Options: "$0 (sweat equity only)", "Under $500", "$500-$2000", "$2000+"

2. **Timeline:** "When do you need first revenue?"
   - Options: "2 weeks", "1 month", "3 months", "No rush"

3. **Skills:** "What can you build yourself?"
   - Options (multi-select): "Frontend (React/Vue/etc)", "Backend (Python/Node/Go)", "Mobile (iOS/Android)", "Data/ML"

4. **Solo?** "Are you building this alone?"
   - Options: "Yes, solo founder", "Have a co-founder", "Can hire freelancers"

5. **Dealbreakers:** "Anything you want to avoid?"
   - Options: "Marketplace/two-sided", "Hardware", "Regulated industries (fintech/health)", "Enterprise sales"

Map answers to constraint flags:
- Budget → `--budget` value
- Timeline → `--timeline` value in weeks
- Skills → `--skills` list
- Solo "Yes" → `--solo`
- Dealbreakers → `--no` list

## Execution

Print the resolved config:

```
Genesis v0.1.0

Prompt: {prompt}
Preset: {preset} ({rounds} rounds, {population} ideas, {elite} elite)
Strategy: {strategy}
Constraints: {formatted constraints or "none"}

Starting evolution...
```

Then invoke the genesis-evo-ideator skill with the full resolved config:

@${CLAUDE_PLUGIN_ROOT}/skills/genesis-evo-ideator/SKILL.md

Pass the config as structured data in the skill invocation:
- prompt
- rounds
- population
- elite
- strategy (name)
- weights (full 8-key map)
- constraints (object with active constraint keys)
- discover (boolean)
