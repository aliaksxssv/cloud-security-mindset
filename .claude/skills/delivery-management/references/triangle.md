# The Iron Triangle — Cost, Time, Features

Every project balances three constraints: **cost**, **time**, and **scope/features** — with **quality** sitting in the middle, affected by all three. You cannot fix all three corners; something must float. Which corner floats defines your delivery paradigm.

## Classic (predictive) approach

- **Fixed:** scope/features — the full specification is committed up front.
- **Floats:** time and cost (and, in practice, quality).
- The plan is the contract. When reality diverges, the project slips deadlines, burns budget, or silently degrades quality to protect the promised feature list.
- Works when requirements are genuinely stable and the work is well understood.

## Agile (adaptive) approach

- **Fixed:** time and cost — stable teams, fixed cadence (sprints/releases).
- **Floats:** scope/features — the backlog is re-negotiated continuously; the most valuable items ship first.
- **Quality is non-negotiable.** It is protected by the working agreement (definition of done), not traded away.
- The estimate is the forecast, not the contract. Value delivered per timebox is the measure of progress.

## The practical rule

When pressure rises — a new request, a slipped dependency, an underestimate — **cut scope, never quality, never sustainability**. Ask: "Which features move out of this timebox?" not "How late can we work?" Descoping is a prioritization conversation (see `rice.md`), not a failure.

## Choosing your mode: Cynefin

Before picking predictive or adaptive, ask where the problem lives:

| Domain | Nature | Approach |
|---|---|---|
| **Clear** | Obvious cause→effect, best practices exist | Sense → categorize → respond. Predictive planning is fine; just execute. |
| **Complicated** | Cause→effect knowable with analysis/expertise | Sense → analyze → respond. Predictive works with good experts; adaptive still reduces risk. |
| **Complex** | Cause→effect visible only in retrospect | **Probe → sense → respond.** Adaptive is mandatory: short feedback loops, experiments, emergent scope. Most product development lives here. |
| **Chaotic** | No perceivable cause→effect | Act → sense → respond. Stabilize first; process discussions come later. |
| **Disorder** | You don't know which domain you're in | Break the problem down until the parts land in a known domain. |

Misdiagnosis is the classic failure: treating a complex problem as complicated ("we just need a better plan") produces detailed plans that die on contact with reality. If the team keeps being "surprised", you are in the complex domain — shorten the feedback loop and let scope float.
