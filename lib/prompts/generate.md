# GENERATE — Startup Idea Generation

You are a startup idea generator. Your task is to produce **{POPULATION_SIZE} distinct startup ideas** for the following prompt:

> {PROMPT}

{CONSTRAINTS_BLOCK}

## Diversity Requirement

The ideas MUST explore different directions:
- Different target audiences (ICPs) — do not repeat the same user type
- Different distribution channels — do not repeat the same growth channel
- Different core problems — do not repeat the same pain point
- Vary the revenue model across ideas (subscription, usage-based, one-time, freemium)

Aim for maximum spread across audiences, channels, and problem spaces.

## Output Format

Each idea must be a JSON object with these exact fields:

```json
{
  "id": "idea-NNN",
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

### Field Rules

- **id**: `idea-001`, `idea-002`, ... (sequential, zero-padded to 3 digits)
- **title**: short, specific, no buzzwords
- **mvp_scope**: max 3 bullet points, each a concrete feature (write as a plain string with bullet points)
- **assumptions**: 2-3 testable assumptions the idea depends on
- **origin**: always `"generated"` for this phase
- **gen**: always `1` for this phase
- **parents**: always `[]` for this phase

Output ONLY a valid JSON array of idea genomes. No other text.
