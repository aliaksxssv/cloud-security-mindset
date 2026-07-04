---
name: delivery-management
description: Project delivery advisor. Use when planning projects or releases, setting goals or OKRs, prioritizing features or backlogs, decomposing epics/projects into smaller slices, negotiating scope/time/cost trade-offs, or coaching delivery leads and teams on leadership and feedback. ALSO invoked at the start of the very first conversation via CLAUDE.md handoff when no self-assessment file exists at memory/delivery/self-assessment.md — runs a skippable self-assessment of current state and constraints (company & external compliance, security team size & skills, budget posture) — and whenever the user says "run self-assessment", "resume assessment", "update the assessment", or answers a previously skipped assessment question.
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
| Scope/deadline/budget negotiation, "can we add X and keep the date?" | Iron triangle (predictive vs adaptive), Cynefin to pick the mode — run the **triangle intake** below first | `references/triangle.md` |
| Roadmap or sprint success criteria, "are we delivering value?" | Outcome vs output | `references/outcome-vs-output.md` |
| Quarterly goals, alignment, tracking progress | OKRs (3×3, cascade, Admin Week, traffic-light tracking) | `references/okr.md` |
| Ordering a backlog, choosing between features/projects | RICE scoring | `references/rice.md` |
| Epic/project too big, planning granularity, unknowns | Splitting patterns, spikes, MVP, planning altitude | `references/decomposition.md` |
| Leadership style, difficult conversations, giving feedback | Servant leadership (10 attributes), DISC adaptation, STAR/AR | `references/servant-leadership.md` |

> **DISC disambiguation:** `references/servant-leadership.md` covers adapting to *other people's* DISC profiles when leading or coaching them. The agent's own voice is governed separately by the `disc-persona` skill (`.claude/skills/disc-persona/`), which already carries a servant-leadership stance — don't conflate the two.

## Self-assessment — current state & constraints

Delivery advice is only as good as its context. The self-assessment captures three stable dimensions in project memory at `memory/delivery/self-assessment.md` (git-tracked, shared with the team):

1. **Company & compliance** — industry, and how strongly external compliance (PCI DSS, HIPAA, SOC 2, FedRAMP, …) binds the company. Regulated requirements become non-negotiable scope.
2. **Security team** — size, capacity, and skills; determines effort feasibility and build-vs-buy.
3. **Budget posture** — appetite for commercial tooling vs open-source; steers recommendations.

(Deadlines are deliberately absent — they are per-project and asked fresh in the triangle intake below.)

### When it runs

- **CLAUDE.md gate handoff** — at the start of the very first conversation, when `memory/delivery/self-assessment.md` does not exist. Same session-start-gate treatment as `disc-persona` and `security-compass`.
- **On demand** — "run self-assessment", "resume assessment", "update the assessment", or the user volunteering an answer to a previously skipped question.
- **Pulled in by need** — when a deliverable (triangle negotiation, RICE scoring, …) requires a dimension that was skipped: ask just that one question, then update the file.

### Flow

1. **Summarize existing state via subagent** (skip this step when the file doesn't exist yet). Spawn an `Explore` subagent: *"Read `<REPO_ROOT>/memory/delivery/self-assessment.md`. Return (a) a ≤3-line summary of the answered sections and (b) the bullet list under `## Open questions`, verbatim. Nothing else."* The full file never enters main context.
2. **Ask** — one `AskUserQuestion` call covering only the unanswered/skipped dimensions. Every question MUST include a **"Skip for now"** option; skipping is always legitimate — never pressure the user.
3. **Persist via subagent.** Spawn a `general-purpose` subagent, passing the answers verbatim: it creates `memory/delivery/` if needed and writes/updates the file in the format below, returning a one-line confirmation plus the remaining open questions.
4. **Highlight the way back.** Close with one line naming the skipped dimensions and how to resume — e.g. *"Skipped: budget posture. Say 'resume assessment' anytime — or I'll ask when a decision actually needs it."*

### File format

```markdown
# Delivery self-assessment — current state & constraints

## Company & compliance
<answer>
_Last updated: YYYY-MM-DD_

## Security team
<answer>
_Last updated: YYYY-MM-DD_

## Budget posture
<answer>
_Last updated: YYYY-MM-DD_

## Open questions
- <dimension> — skipped YYYY-MM-DD
```

Sections are updated **in place** (current-state facts, not a log). A skipped dimension keeps `_skipped YYYY-MM-DD_` as its body plus a bullet under `## Open questions`; both are removed once answered.

## Delegation protocol — keep the main context lean

Substantial framework work MUST run in a sub-agent, not in the main conversation. This includes: drafting OKRs, RICE-scoring a backlog, decomposing an epic or project, preparing a coaching or feedback conversation, or producing a delivery plan.

Use the Agent tool (`general-purpose`), one sub-agent per deliverable. The sub-agent prompt must contain:

1. **Reference path(s)** — the absolute path of the specific file(s) from the decision map above (this skill lives at `.claude/skills/delivery-management/` in the repo root; resolve to an absolute path before spawning).
2. **The user's inputs** — backlog items, goal statements, epic description, team context. Pass them verbatim; the sub-agent cannot see the conversation.
3. **The expected output format** — e.g. "return a RICE table sorted by score with a one-line rationale per row", "return 3 Objectives with 3 measurable KRs each", "return the epic split into vertical slices, naming the pattern used for each".

The sub-agent reads the references, does the work, and returns only the finished deliverable. Relay it to the user; never paste reference files wholesale into the conversation.

### Triangle intake — gather context before spawning

Scope/time/cost negotiations need the self-assessment facts plus per-project specifics, and the sub-agent cannot ask the user for them itself. Before spawning a triangle sub-agent:

1. **Pull the self-assessment** via the Explore-subagent summary (see the self-assessment flow above). If a dimension the negotiation needs is skipped or clearly stale, ask just that question now and persist the answer (flow steps 2–3).
2. **Ask the per-project questions** in the same `AskUserQuestion` call: **deadlines** (only when the question is project-bound; fixes the time corner and drives how scope is allocated into the timebox) and a **project budget envelope** if it differs from the general budget posture. These are per-project — never persisted to the assessment file.
3. **Pass everything verbatim** into the sub-agent prompt alongside the `references/triangle.md` path — assessment facts + per-project answers are "the user's inputs" in the protocol above.

**Exception:** one-line factual questions ("what are the RICE factors?", "name the servant-leadership attributes") may be answered from the core principles above or by reading a single reference directly — no sub-agent needed.
