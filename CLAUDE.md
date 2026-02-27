# Genesis — Startup Idea Evolution

Evolve startup ideas using a memetic algorithm with 3 AI agent personas.

## Quick Start

```
/genesis "AI tools for solo developers"
/genesis "B2B SaaS for restaurants" --preset deep --strategy bootstrapper
/genesis "developer productivity" --discover --solo
```

## Speed Presets

| Preset | Rounds | Population | Elite | Time |
|--------|--------|-----------|-------|------|
| `quick` | 2 | 4 | 2 | ~3 min |
| `standard` | 3 | 6 | 2 | ~5 min |
| `deep` | 5 | 8 | 3 | ~10 min |

## Strategy Presets

| Strategy | Focus | Best For |
|----------|-------|----------|
| `bootstrapper` | Speed to revenue, feasibility | Solo founders, tight budgets |
| `growth` | Market size, distribution | Scale-oriented founders |
| `moat` | Defensibility, differentiation | Long-term competitive advantage |
| `uniform` | Balanced (default) | Exploring without bias |

## Constraint Flags

```
--budget N        Max budget in USD
--timeline N      Max weeks to first revenue
--skills LIST     Your skills (comma-separated)
--must LIST       Required elements
--no LIST         Forbidden elements
--solo            Must be solo-buildable
--discover        Interactive 5-question preflight
```

## Output

Runs are saved to `.genesis/runs/<run-id>/`:
- `config.json` — run parameters
- `state.json` — evolution state
- `final.json` — winner + runner-up with full data
- `summary.md` — human-readable results

## How It Works

3 AI personas compete in an evolution loop:
1. **Generator** (Startup Visionary) — creates diverse ideas
2. **Critic** (Bootstrapper Analyst) — scores on 8 dimensions
3. **Refiner** (Pragmatic CTO) — improves winners with execution plans

Each round: GENERATE → CRITIQUE → SELECT → REFINE → VARIATE
