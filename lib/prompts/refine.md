# REFINE — Idea Improvement

You are a startup advisor. Your task is to improve one idea based on its critique scores, targeting the weakest dimensions.

## Idea to Refine

```json
{IDEA_JSON}
```

## Critique Scorecard

```json
{SCORES_JSON}
```

## Weakest Areas (Weighted Priority)

The following dimensions are dragging down this idea's weighted score most. Focus your improvements here:

{WEAK_AREAS}

## Benchmark

The best-scoring idea in this generation has a weighted score of **{BEST_SCORE}**. Use this as your target bar.

{CONSTRAINTS_BLOCK}

## What to Do

1. Diagnose why each weak area scored low — read the original idea carefully.
2. Rework the idea to address those weaknesses without discarding what works.
3. Keep the core insight but sharpen the ICP, tighten the MVP scope, improve the distribution channel, or change the wedge as needed.
4. The constraints above are hard guardrails — the refined idea must not violate any of them.

## Output Format

Return a JSON object with these exact top-level keys:

```json
{
  "genome": {
    "id": "idea-NNN-r1",
    "title": "Refined 5-10 word title",
    "icp": "Sharpened ICP",
    "pain": "Core problem",
    "wedge": "Improved entry point",
    "distribution": "Improved growth channel",
    "biz_model": "Revenue model",
    "mvp_scope": "Tighter MVP — max 3 features",
    "assumptions": ["Updated assumption 1", "Updated assumption 2"],
    "origin": "refined",
    "gen": 2,
    "parents": ["idea-NNN"]
  },
  "tech_stack": ["tool or language 1", "tool or language 2"],
  "week1_tasks": [
    "Concrete task to complete in day 1-2",
    "Concrete task to complete in day 3-5",
    "Concrete task to complete in day 6-7"
  ],
  "changes": "One paragraph explaining what was changed and why — reference the weak dimensions by name"
}
```

### Field Rules

- **genome.id**: append `-r{N}` to parent id (e.g., `idea-003-r1` for round 1 refinement)
- **genome.origin**: always `"refined"`
- **genome.gen**: parent gen + 1
- **genome.parents**: array containing the parent idea's id
- **tech_stack**: 2-5 concrete tools, languages, or services (no hand-wavy categories)
- **week1_tasks**: exactly 3 tasks, each actionable and completable within the stated timeframe
- **changes**: required — must explicitly name which weak dimensions were addressed

Output ONLY valid JSON. No other text.
