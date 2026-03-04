# Genesis

<div align="center">

**Evolve startup ideas through memetic natural selection**

![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-5b21b6?style=flat-square)
![Version](https://img.shields.io/badge/version-0.1.0-5b21b6?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-5b21b6?style=flat-square)

```bash
claude plugin marketplace add heurema/emporium
claude plugin install genesis@emporium
```

</div>

## What it does

Generating startup ideas is easy; generating ones that actually fit your constraints is not. Genesis runs a memetic algorithm over your direction: a Startup Visionary generates candidates, a Bootstrapper Analyst scores them across 8 dimensions, and a Pragmatic CTO refines the survivors into execution blueprints. Ideas that violate your constraints are eliminated before refinement runs. After the configured number of rounds, you get a winner and runner-up — each with a problem statement, monetization model, MVP scope, validation experiments, week-1 task list, and tech stack.

## Install

<!-- INSTALL:START — auto-synced from emporium/INSTALL_REFERENCE.md -->
```bash
claude plugin marketplace add heurema/emporium
claude plugin install genesis@emporium
```
<!-- INSTALL:END -->

<details>
<summary>Manual install from source</summary>

```bash
git clone https://github.com/heurema/genesis
cd genesis
claude plugin install .
```

</details>

## Quick start

Pick a direction and let the defaults handle the rest:

```bash
/genesis "developer productivity tools"
```

Use discovery mode for guided constraint setup:

```bash
/genesis "SaaS ideas" --discover
```

## Commands

| Command | Flag | Description |
|---------|------|-------------|
| `/genesis "<direction>"` | | Run evolution with defaults |
| | `--preset <deep\|fast\|lean>` | Control generation depth and round count |
| | `--strategy <bootstrapper\|growth\|moat\|uniform>` | Shift scoring weights to match your stage |
| | `--discover` | Answer 5 questions to set constraints interactively |
| | `--budget <number>` | Max launch budget in USD |
| | `--timeline <weeks>` | Max weeks to first revenue |
| | `--skills "<csv>"` | Required tech skills (e.g. `"python,react"`) |
| | `--solo` | Enforce single-founder constraints |

## Features

- **Memetic evolution loop**: ideas go through GENERATE → CRITIQUE → ARBITER → REFINE → RE-SCORE, with fresh VARIATE injections on round 2+ to prevent convergence
- **8-dimension scoring with strategy weights**: four strategies (bootstrapper, growth, moat, uniform) shift the emphasis to what matters for your stage and goals
- **Hard constraint enforcement**: budget, timeline, required skills, solo-founder mode, must-have and must-not elements are enforced by a deterministic arbiter before any refinement compute runs
- **Actionable winner output**: every surviving idea includes a tech stack, a week-1 execution plan, validation experiments, and the most likely failure modes
- **Discovery mode**: five guided questions translate your situation into the correct constraint flags without memorizing CLI syntax

## Requirements

- Claude Code CLI
- No external dependencies

## Privacy

Genesis makes no network calls. All agent dispatches stay inside Claude Code. Run artifacts are written to `.genesis/runs/<run-id>/` on your local filesystem (config.json, state.json, final.json, summary.md). Nothing leaves your machine.

## See also

- [skill7.dev listing](https://skill7.dev/creative/genesis)
- [heurema/emporium](https://github.com/heurema/emporium) — plugin registry
- [How it works](docs/how-it-works.md) — architecture, agents, evolution loop, algorithm details
- [Reference](docs/reference.md) — all flags, preset tables, output format, troubleshooting
- [GitHub](https://github.com/heurema/genesis)

## License

[MIT](LICENSE)
