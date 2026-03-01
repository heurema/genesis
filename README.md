# Genesis

> Evolve startup ideas through AI-powered natural selection.

Genesis runs a memetic algorithm over your idea direction: a Startup Visionary generates candidates, a Bootstrapper Analyst scores them across 8 dimensions, and a Pragmatic CTO refines the survivors into execution blueprints. Ideas that violate your constraints are eliminated before refinement. After the configured number of rounds, you get a winner and runner-up — each with a problem statement, monetization model, MVP scope, validation experiments, week-1 task list, and tech stack.

```
/genesis "AI tools for solo developers" --preset deep --strategy bootstrapper
/genesis "B2B SaaS for restaurants" --discover
/genesis "developer productivity" --budget 500 --timeline 4 --skills "python,react" --solo
```

## Quick Start

<!-- INSTALL:START — auto-synced from emporium/INSTALL_REFERENCE.md -->
```bash
claude plugin marketplace add heurema/emporium
claude plugin install genesis@emporium
```
<!-- INSTALL:END -->

First run — pick a direction and let the defaults handle the rest:

```bash
/genesis "developer productivity tools"
```

For interactive constraint setup, use discovery mode:

```bash
/genesis "SaaS ideas" --discover
```

## Key Features

- **Memetic evolution loop**: ideas go through GENERATE → CRITIQUE → ARBITER → REFINE → RE-SCORE, with fresh VARIATE injections on round 2+ to prevent convergence
- **8-dimension scoring with strategy weights**: four strategies (bootstrapper, growth, moat, uniform) shift the emphasis to what matters for your stage and goals
- **Hard constraint enforcement**: budget, timeline, required skills, solo-founder mode, must-have and must-not elements are enforced by a deterministic arbiter before any refinement compute runs
- **Actionable winner output**: every surviving idea includes a tech stack, a week-1 execution plan, validation experiments, and the most likely failure modes
- **Discovery mode**: five guided questions translate your situation into the correct constraint flags without memorizing CLI syntax

## Privacy & Data

Genesis makes no network calls. All agent dispatches stay inside Claude Code. Run artifacts are written to `.genesis/runs/<run-id>/` on your local filesystem (config.json, state.json, final.json, summary.md). Nothing leaves your machine.

## Requirements

- Claude Code CLI
- No external dependencies

## Documentation

- [How It Works](docs/how-it-works.md) — architecture, agents, evolution loop, algorithm details
- [Reference](docs/reference.md) — all flags, preset tables, output format, troubleshooting

## Links

- [skill7.dev listing](https://skill7.dev/creative/genesis)
- [GitHub](https://github.com/heurema/genesis)

## License

MIT
