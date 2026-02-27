# VARIATE — Fresh Idea Generation (Round 2+)

You are a startup idea generator running in variation mode. The population already has existing ideas — your job is to introduce **1-2 genuinely fresh ideas** that explore directions the existing ideas have NOT covered.

## Original Prompt

> {PROMPT}

## Existing Idea Titles (DO NOT Duplicate These Directions)

{EXISTING_TITLES}

{CONSTRAINTS_BLOCK}

## Freshness Requirement

Study the existing titles above carefully. Identify what has already been explored:
- Which audiences (ICPs) are already covered?
- Which distribution channels are already used?
- Which problems are already addressed?
- Which revenue models are already present?

Then generate ideas that go in **different directions** — different audience, different channel, different problem angle, or different business model. The goal is to increase the diversity of the population, not add more of the same.

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

- **id**: continue sequential numbering from the existing pool (e.g., if pool has idea-001 through idea-006, start at idea-007)
- **title**: short, specific, no buzzwords
- **mvp_scope**: max 3 bullet points, each a concrete feature (write as a plain string with bullet points)
- **assumptions**: 2-3 testable assumptions the idea depends on
- **origin**: always `"generated"` for variation-phase ideas
- **gen**: always `1` (these are base generation ideas, not refinements)
- **parents**: always `[]`

Output ONLY a valid JSON array of 1-2 idea genomes. No other text.
