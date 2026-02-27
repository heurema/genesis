# Scorecard Schema

Each scored idea produces a scorecard JSON object:

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

## Field Rules

- **scores**: all 8 dimensions required, integer 0-10
- **confidence**: one of `"low"`, `"medium"`, `"high"`
- **kill_criteria**: string array — showstoppers that could kill this idea (empty array if none)
- **derisk_experiments**: string array — 2-3 cheapest ways to validate (always provide at least 1)
- **failure_reason**: string — one sentence explaining the most likely way this idea fails
- **constraint_violation**: boolean — true if idea violates any user-specified constraints
- **violation_reason**: string or null — which constraint was violated and why
