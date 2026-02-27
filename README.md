# Genesis

Evolve startup ideas through AI-powered natural selection. 3 agent personas — Generator, Critic, Refiner — compete in a memetic algorithm to find your best idea.

## Installation

### From skill7.dev

Download the genesis pack and install:

```bash
claude plugin add genesis.zip
```

### From source

```bash
git clone https://github.com/heurema/genesis.git
claude plugin add ./genesis
```

## Usage

```bash
# Basic: explore a direction
/genesis "AI tools for solo developers"

# With speed preset
/genesis "B2B SaaS for restaurants" --preset deep

# With strategy (scoring weights)
/genesis "developer productivity" --strategy bootstrapper

# Interactive discovery mode (asks 5 questions about your constraints)
/genesis "SaaS ideas" --discover

# Full constraint control
/genesis "fintech tools" --budget 500 --timeline 4 --skills "python,react" --solo --no "regulated,marketplace"
```

## Features

- **Memetic algorithm**: ideas evolve through GENERATE → CRITIQUE → SELECT → REFINE → VARIATE
- **3 agent personas**: Startup Visionary (divergent), Bootstrapper Analyst (convergent), Pragmatic CTO (execution)
- **8-dimension scoring**: feasibility, speed to value, differentiation, market size, distribution, moats, risk, clarity
- **Strategy presets**: bootstrapper, growth, moat, uniform — each weights dimensions differently
- **Speed presets**: quick (2 rounds), standard (3), deep (5)
- **Interactive discovery**: `--discover` runs a 5-question preflight to set constraints
- **Constraint enforcement**: budget, timeline, skills, must-have/must-not, solo-founder mode
- **Actionable output**: validation experiments + week-1 execution plan + tech stack

## Output

Each run produces:

- **Winner**: best idea with full execution blueprint
- **Runner-up**: second best with condensed details
- **Validation experiments**: cheapest ways to test the idea
- **Week-1 plan**: concrete tasks to start immediately
- **Risk factors**: most likely failure modes

Runs are saved to `.genesis/runs/<run-id>/` with config, state, final results, and a human-readable summary.

## How the Algorithm Works

1. **GENERATE**: Generator creates N diverse ideas covering different audiences, channels, and problems
2. **CRITIQUE**: Critic scores each idea on 8 dimensions with confidence levels and kill criteria
3. **ARBITER**: Constraint violations eliminated, ideas ranked by weighted score, elites selected with diversity slot
4. **REFINE**: Refiner improves each elite by addressing weakest dimensions, produces execution blueprint
5. **VARIATE** (round 2+): Fresh random ideas injected to prevent premature convergence

## Speed Presets

| Preset | Rounds | Ideas | Elite | Typical Time |
|--------|--------|-------|-------|-------------|
| quick | 2 | 4 | 2 | ~3 min |
| standard | 3 | 6 | 2 | ~5 min |
| deep | 5 | 8 | 3 | ~10 min |

## Strategy Presets

| Strategy | Focus | Top Weights |
|----------|-------|------------|
| bootstrapper | Speed to revenue | feasibility (0.20), speed_to_value (0.20) |
| growth | Scale potential | market_size (0.20), distribution (0.20) |
| moat | Defensibility | moats (0.20), differentiation (0.20) |
| uniform | Balanced | all dimensions equal (0.125) |

## Requirements

- Claude Code CLI
- No external dependencies (pure Claude Code plugin)

## License

MIT
