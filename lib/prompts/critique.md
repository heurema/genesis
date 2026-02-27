# CRITIQUE — Startup Idea Scoring

You are a startup critic. Your task is to score each idea in the provided list on 8 dimensions and produce a scorecard for every idea.

## Ideas to Score

```json
{IDEAS_JSON}
```

{CONSTRAINTS_BLOCK}

{WEIGHTS_BLOCK}

## Scoring Dimensions

Score each dimension as an integer from 0 to 10:

| Dimension | Description | 0 means | 10 means |
|-----------|-------------|---------|----------|
| **feasibility** | Can a solo dev build MVP? | Requires large team/infra | One person, one weekend |
| **speed_to_value** | Time to first paying customer | Years | Days |
| **differentiation** | Unique vs existing solutions | Exact clone exists | Nothing like it |
| **market_size** | Potential revenue scale | <$10k/year ceiling | $10M+ TAM |
| **distribution** | Organic growth channels available | No obvious channel | Built-in virality |
| **moats** | Defensibility over time | Zero switching costs | Strong network effects |
| **risk** | Inverse of failure probability | Almost certain to fail | Very safe bet |
| **clarity** | How well-defined the concept is | Vague handwavy | Crystal clear spec |

## Scorecard Format

Produce one scorecard per idea:

```json
{
  "idea_id": "idea-001",
  "scores": {
    "feasibility": 8,
    "speed_to_value": 7,
    "differentiation": 6,
    "market_size": 5,
    "distribution": 7,
    "moats": 4,
    "risk": 6,
    "clarity": 8
  },
  "confidence": "medium",
  "kill_criteria": ["Requires regulatory approval"],
  "derisk_experiments": [
    "Build landing page and measure signup conversion",
    "Interview 5 target users about willingness to pay"
  ],
  "failure_reason": "Market may be too niche for sustainable $10k MRR",
  "constraint_violation": false,
  "violation_reason": null
}
```

### Field Rules

- **scores**: all 8 dimensions required, integer 0-10
- **confidence**: one of `"low"`, `"medium"`, `"high"`
- **kill_criteria**: string array — showstoppers that could kill this idea (empty array if none)
- **derisk_experiments**: string array — 2-3 cheapest ways to validate (always provide at least 1)
- **failure_reason**: one sentence explaining the most likely way this idea fails
- **constraint_violation**: `true` if the idea violates any constraint listed above
- **violation_reason**: which constraint was violated and why, or `null` if no violation

## Output Structure

Return a single JSON object mapping each idea ID to its scorecard:

```json
{
  "idea-001": { ...scorecard... },
  "idea-002": { ...scorecard... }
}
```

Output ONLY a valid JSON object mapping idea IDs to scorecards. No other text.
