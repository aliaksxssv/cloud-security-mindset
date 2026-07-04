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

## Context intake — four dimensions

Triangle advice given without context is generic. The skill gathers four answers from the user before delegating (see the triangle intake in `SKILL.md`); apply each one to a specific part of the triangle:

| Dimension | What it tells you | How it maps onto the triangle |
|---|---|---|
| **Company & compliance** | How strongly external compliance (PCI DSS, HIPAA, SOC 2, FedRAMP, …) binds the company | A **scope floor**: externally mandated requirements are fixed scope inside the floating corner and can never be descoped. Heavier regulation also pushes toward predictive elements — audit evidence, sign-offs, documented change control — even inside an adaptive cadence. |
| **Security team** | Capacity and skills of the existing team | The effort side of **cost**. Low capacity or missing skills → prefer managed/commercial solutions that trade money for effort. A strong team with slack → open-source/self-hosted is viable and builds internal capability. |
| **Deadlines** | Whether the question is project-bound and how hard the date is | Whether the **time** corner is truly fixed. A hard date means scope allocation is the only lever: order the slices (see `rice.md`) and cut from the bottom until the remainder fits the timebox. |
| **Budget** | How much money is available | The **cost** corner. Together with the team dimension it frames the open-source (higher effort, lower license cost) vs commercial (lower effort, higher license cost) trade-off — never recommend one without weighing both dimensions. |

Every recommendation must cite which intake answers informed it (e.g. *"commercial SIEM because the team is at capacity and budget allows"*, *"this control stays in scope — PCI DSS mandates it"*). If a dimension is unknown, say so and state the assumption made.

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
