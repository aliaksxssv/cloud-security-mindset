---
name: delivery-management
description: Project delivery advisor. Use when planning projects or releases, setting goals or OKRs, prioritizing features or backlogs, decomposing epics/projects into smaller slices, negotiating scope/time/cost trade-offs, or coaching delivery leads and teams on leadership and feedback.
---

# Delivery Management

Ground every delivery conversation in these principles. They answer most quick questions on their own — load reference files only through the delegation protocol below.

## Core principles

1. **Flex scope, not time or quality.** In adaptive delivery the triangle is inverted: time and cost are fixed, scope/features float. Under pressure, cut scope — never quality.
2. **Maximize outcome, not output.** Success is the value delivered (behavior or business result changed), not the number of tasks done. Fewer tasks with more value beats many tasks with low value.
3. **Set goals as OKRs, 3×3.** Three Objectives (inspiring, "where do we want to go?"), each with three Key Results (measurable, "how do we know we're getting there?"). Cascade from the top down; never create team OKRs without company OKRs above them.
4. **Prioritize with RICE.** Score = Reach × Impact × Confidence ÷ Effort. Low confidence means run a spike before committing, not guess harder.
5. **Split work small, but always vertical.** Every slice must be independently valuable and demoable. Use the splitting-pattern catalog; when uncertain, time-box a spike framed as 1–3 questions.
6. **Lead as a servant leader.** Listen first, persuade rather than direct, grow people. Adapt the style to the person's DISC profile.

## Decision map

| Situation | Framework | Reference |
|---|---|---|
| Scope/deadline/budget negotiation, "can we add X and keep the date?" | Iron triangle (predictive vs adaptive), Cynefin to pick the mode | `references/triangle.md` |
| Roadmap or sprint success criteria, "are we delivering value?" | Outcome vs output | `references/outcome-vs-output.md` |
| Quarterly goals, alignment, tracking progress | OKRs (3×3, cascade, Admin Week, traffic-light tracking) | `references/okr.md` |
| Ordering a backlog, choosing between features/projects | RICE scoring | `references/rice.md` |
| Epic/project too big, planning granularity, unknowns | Splitting patterns, spikes, MVP, planning altitude | `references/decomposition.md` |
| Leadership style, difficult conversations, giving feedback | Servant leadership (10 attributes), DISC adaptation, STAR/AR | `references/servant-leadership.md` |

## Delegation protocol — keep the main context lean

Substantial framework work MUST run in a sub-agent, not in the main conversation. This includes: drafting OKRs, RICE-scoring a backlog, decomposing an epic or project, preparing a coaching or feedback conversation, or producing a delivery plan.

Use the Agent tool (`general-purpose`), one sub-agent per deliverable. The sub-agent prompt must contain:

1. **Reference path(s)** — the absolute path of the specific file(s) from the decision map above (this skill lives at `.claude/skills/delivery-management/` in the repo root; resolve to an absolute path before spawning).
2. **The user's inputs** — backlog items, goal statements, epic description, team context. Pass them verbatim; the sub-agent cannot see the conversation.
3. **The expected output format** — e.g. "return a RICE table sorted by score with a one-line rationale per row", "return 3 Objectives with 3 measurable KRs each", "return the epic split into vertical slices, naming the pattern used for each".

The sub-agent reads the references, does the work, and returns only the finished deliverable. Relay it to the user; never paste reference files wholesale into the conversation.

**Exception:** one-line factual questions ("what are the RICE factors?", "name the servant-leadership attributes") may be answered from the core principles above or by reading a single reference directly — no sub-agent needed.
