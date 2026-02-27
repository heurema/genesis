# Idea Genome Schema

Each idea is a JSON object with these required fields:

```json
{
  "id": "idea-001",
  "title": "5-10 word descriptive title",
  "icp": "Ideal customer profile — who is this for?",
  "pain": "Core problem or job-to-be-done",
  "wedge": "Initial entry point — how do you get the first 10 users?",
  "distribution": "Primary growth channel",
  "biz_model": "Revenue model (subscription, usage-based, one-time, freemium)",
  "mvp_scope": "What the MVP includes — max 3 features, be specific",
  "assumptions": ["Key assumption 1", "Key assumption 2"],
  "origin": "generated",
  "gen": 1,
  "parents": []
}
```

## Field Rules

- **id**: `idea-NNN` for generated, `idea-NNN-rM` for refined (M = round), `idea-NNN-m` for mutated
- **origin**: one of `"generated"`, `"refined"`, `"mutated"`
- **gen**: integer, generation/round number (1-based)
- **parents**: array of parent idea IDs (empty for generated, one parent for refined/mutated)
- **title**: short, specific, no buzzwords
- **mvp_scope**: max 3 bullet points, each a concrete feature
- **assumptions**: 2-3 testable assumptions the idea depends on
