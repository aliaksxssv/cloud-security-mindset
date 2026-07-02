# Prioritization with RICE

RICE scores each project, epic, or feature so the backlog can be ordered by expected value per unit of effort:

```
RICE score = (Reach × Impact × Confidence) ÷ Effort
```

## The four factors

**Reach — how many people/events does this affect per time period?**
Use a real number from data, with an explicit period: "1,200 customers per quarter", "300 signups per month", "45 support tickets per week". Same period for every item being compared.

**Impact — how much does it move the goal for each person reached?**
Estimated on a fixed scale (resist inventing intermediate values):

| Score | Meaning |
|---|---|
| 3 | Massive impact |
| 2 | High impact |
| 1 | Medium impact |
| 0.5 | Low impact |
| 0.25 | Minimal impact |

Anchor Impact to an outcome metric, not to how impressive the feature sounds (see `outcome-vs-output.md`).

**Confidence — how sure are you about the Reach and Impact estimates?**

| Score | Meaning |
|---|---|
| 100% | High — data behind both Reach and Impact |
| 80% | Medium — data behind one, informed judgment on the other |
| 50% | Low — educated guesses |
| < 50% | Moonshot — don't score it; run a **spike** first (see `decomposition.md`) to raise confidence |

**Effort — how many person-months of total team work (product, design, engineering)?**
Whole numbers; use 0.5 as the minimum. Effort is the only factor in the denominator — the classic mistake is underestimating it for "exciting" work.

## Worked example

| Item | Reach (users/q) | Impact | Confidence | Effort (p-m) | RICE |
|---|---|---|---|---|---|
| Self-serve password reset | 900 | 1 | 100% | 1 | **900** |
| Redesigned onboarding flow | 1,500 | 2 | 80% | 4 | **600** |
| AI-powered report builder | 400 | 3 | 50% | 6 | **100** |

The flashy AI feature ranks last — not because it's a bad idea, but because it is uncertain and expensive *right now*. The right move is a spike to raise its Confidence, then re-score.

## Process

1. List candidates at comparable granularity (don't score an epic against a bug fix).
2. Fill Reach from data; if there is no data, that's a Confidence penalty, not an excuse to guess high.
3. Score Impact and Confidence with the fixed scales; estimate Effort with the delivery team, not for them.
4. Sort by score, then **sanity-check the order** — RICE informs the decision, it doesn't make it. Strategy, dependencies, and commitments may justify overrides, but write the override reason down.
5. Re-score when new information arrives (a completed spike, real usage data, a changed goal).

## Pitfalls

- **False precision:** the score has one significant digit of real accuracy. 620 vs 580 is a tie; 900 vs 100 is a decision.
- **Confidence inflation:** everyone believes in their own idea. Ask "what data do we have?" — no data means ≤50%.
- **Impact = enthusiasm:** tie Impact to a named metric and expected movement, or it becomes a popularity contest.
- **Scoring to justify a decision already made:** if the ranking will be ignored, skip the ritual and state the strategic call honestly.
- **Effort as solo guess:** effort estimates made without the people doing the work are systematically low.
