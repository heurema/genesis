# Scoring Dimensions

8 dimensions for evaluating startup ideas. Scores are integers 0-10.

| Dimension | Description | 0 means | 10 means |
|-----------|-------------|---------|----------|
| feasibility | Can a solo dev build MVP? | Requires large team/infra | One person, one weekend |
| speed_to_value | Time to first paying customer | Years | Days |
| differentiation | Unique vs existing solutions | Exact clone exists | Nothing like it |
| market_size | Potential revenue scale | <$10k/year ceiling | $10M+ TAM |
| distribution | Organic growth channels available | No obvious channel | Built-in virality |
| moats | Defensibility over time | Zero switching costs | Strong network effects |
| risk | Inverse of failure probability | Almost certain to fail | Very safe bet |
| clarity | How well-defined the concept is | Vague handwavy | Crystal clear spec |

## Strategy Weight Maps

All weights sum to 1.0. Used for weighted score computation: `score = sum(dimension_score * weight)`.

### bootstrapper
```json
{
  "feasibility": 0.20, "speed_to_value": 0.20, "differentiation": 0.10,
  "market_size": 0.10, "distribution": 0.15, "moats": 0.05, "risk": 0.05, "clarity": 0.15
}
```

### growth
```json
{
  "feasibility": 0.10, "speed_to_value": 0.10, "differentiation": 0.10,
  "market_size": 0.20, "distribution": 0.20, "moats": 0.10, "risk": 0.05, "clarity": 0.15
}
```

### moat
```json
{
  "feasibility": 0.10, "speed_to_value": 0.05, "differentiation": 0.20,
  "market_size": 0.15, "distribution": 0.10, "moats": 0.20, "risk": 0.05, "clarity": 0.15
}
```

### uniform
```json
{
  "feasibility": 0.125, "speed_to_value": 0.125, "differentiation": 0.125,
  "market_size": 0.125, "distribution": 0.125, "moats": 0.125, "risk": 0.125, "clarity": 0.125
}
```
