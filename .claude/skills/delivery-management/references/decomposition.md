# Decomposition — Splitting Work for Better Planning

## Why decompose

Smaller work items flow faster, get feedback sooner, and carry less risk: an estimate for a small slice can be wrong by 100% and cost you days; the same error on a monolith costs a quarter. Decomposition is also what makes prioritization real — you can only defer scope (see `triangle.md`) if the scope comes apart.

**The golden rule: split vertically.** Every slice must cut through the whole stack and be independently valuable and demoable — a thin end-to-end sliver of the product, not a layer ("the database part", "the API part"). If a slice can't be shown to a user or stakeholder, it's a task, not a slice.

## Planning altitude

Keep three levels of planning, each answering a different question:

- **Portfolio — Why?** Strategic bets and outcomes (see `okr.md`).
- **Product/Program — What?** Epics and features that serve the Why, ordered by value (see `rice.md`).
- **Team — How?** Stories and slices small enough to flow through an iteration.

Decomposition is the act of translating one level into the next. An item that can't be traced upward to a Why is output for its own sake (see `outcome-vs-output.md`).

## Splitting pattern catalog

Work through the catalog in order; the first pattern that produces two or more valuable slices wins. For each pattern: what it is, when to use it, and the helpful question to ask.

### Business-behavior based

**1. Business Rules** — split by the rules the story enforces; ship the simple rule first, the exotic rules later.
*Use when* a story hides several policies ("validate the order" = stock rule + credit rule + fraud rule).
*Ask:* What are all the rules implied here? Which single rule covers most cases?

**2. Use Case Scenarios** — split by scenario: happy path first, then each alternate/exception path.
*Use when* the story covers one interaction with many branches.
*Ask:* What is the mainstream scenario? What can go differently?

**3. Role** — split by the persona performing it: one role's version per slice.
*Use when* several user types touch the same functionality with different needs.
*Ask:* Which role gets the most value? Can other roles wait or use the first role's version?

**4. Acceptance Criteria** — each (cluster of) acceptance criteria becomes its own slice.
*Use when* the AC list is long and criteria are separately demoable.
*Ask:* Which criteria are essential for the first demo? Which are refinements?

**5. Workflow** — split a multi-step business process by its steps: begin-and-end first, middle steps later (or manual at first).
*Use when* the story spans a workflow (submit → review → approve → publish).
*Ask:* What is the thinnest walk through the whole workflow? Which steps can start manual?

**6. Operations** — split CRUD-style stories by operation: Create first, then Read, Update, Delete as separate slices.
*Use when* the story says "manage X".
*Ask:* Which operation delivers value alone? (Usually Create + Read.)

### Data based

**7. Data Boundaries** — split by data segment: one country, one currency, one product category first.
*Use when* the data has natural partitions with different handling.
*Ask:* Which segment covers the core business? Which segments add only edge cases?

**8. Zero > One > Many** — handle none, then exactly one, then many.
*Use when* the story involves collections (attachments, accounts, recipients).
*Ask:* What does the feature look like with just one item? Is "many" really needed now?

**9. Dummy then Dynamic Data** — ship first with hardcoded/static data, then wire up the real source.
*Use when* the data plumbing is expensive but the experience can be validated without it.
*Ask:* Would stakeholders learn something from a version with canned data?

**10. Variations in Data** — split by data variety: one language/format/type first, the rest later.
*Use when* the story must handle heterogeneous inputs.
*Ask:* Which single variation unlocks the main use case?

### MVP

**11. MVP** — extract the minimum end-to-end slice that lets real users get real value (and lets you measure it — see `outcome-vs-output.md`), deferring everything else.
*Use when* facing a large feature or new product area.
*Ask:* What is the smallest thing that would change user behavior? What are we assuming, and what's the cheapest slice that tests it?

### Technology based

**12. Core & Enhance** — ship the bare core capability, then enhancements as separate slices.
*Ask:* Strip every nicety — what remains that still works?

**13. Major Effort** — when several similar slices share one big enabling effort, put the effort into the first slice and keep the rest cheap.
*Ask:* Which slice, done first, makes all the others small?

**14. Low Fidelity / High Fidelity** — a rough version first (simple UI, approximate results), polish as a later slice.
*Ask:* Would a rough version already be usable or testable?

**15. Transient then Persistent** — work in-memory/per-session first; add storage as its own slice.
*Ask:* Is persistence needed to demonstrate the behavior?

**16. Interface Variations** — one interface first (web before mobile, UI before API), others later.
*Ask:* Which surface do the primary users actually use?

**17. Platform Options** — one platform/browser/OS first, the rest as follow-ups.
*Ask:* Which platform covers most of the audience?

**18. Defer Error Handling / Logging** — happy path first; robust error handling, retries, and observability as explicit follow-up slices (schedule them — deferred is not deleted).
*Ask:* What is the minimum failure behavior we can live with for a first release?

**19. Defer System Qualities** — meet functional behavior first; performance, scale, and hardening as separate, named slices.
*Ask:* What quality level is enough for the current user count?

**20. Manual then Automated** — run the process manually (concierge/wizard-of-oz) first; automate once the process is validated.
*Ask:* Could a human perform this behind the scenes for the first N users?

### Spikes

**21. Spike** — a time-boxed research/investigation story used when the team is too uncertain about implementation to split or estimate. The deliverable is *knowledge*, framed up front as the **1–3 questions** the spike must answer — not code.
*Use when* unknowns block splitting or estimation, or Confidence in a RICE score is below 50% (see `rice.md`).
*Ask:* What are the 1–3 questions we need answered? What's the smallest experiment that answers them? Time-box it and stop when the questions are answered.

## How to run a decomposition session

1. State the outcome the epic serves (Why) — if unclear, fix that first.
2. Walk the catalog top-down; try 2–3 patterns and compare the splits, don't stop at the first.
3. Check every slice: vertical? demoable? valuable on its own? small enough (days, not weeks)?
4. Order the slices by value and learning (see `rice.md`); the first slice should be the one that teaches you the most.
5. Park deferred slices explicitly in the backlog — deferred scope is a decision, not an accident.
6. Anything still too uncertain becomes a spike with its 1–3 questions written down.
