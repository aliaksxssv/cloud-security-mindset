# Outcome over Output — Maximize Value, Not Task Count

**Output** is the number of things done: tasks closed, features shipped, story points burned. Bricks moved onto the truck.

**Outcome** is what changed because of the work: a user behavior, a business metric, a risk removed. The reason the bricks were needed at all.

A team can have spectacular output and zero outcome. Delivering **fewer tasks with more value** always beats delivering many tasks with low value — the goal is to maximize the value delivered per unit of capacity, not to keep everyone busy.

## Before starting any piece of work, ask

1. **Who changes their behavior when this ships?** (user, customer, internal team)
2. **What do they do differently?** Be concrete: "support agents resolve tickets without escalating", not "better UX".
3. **How will we measure it?** Name the metric and the expected direction before writing code.
4. **What is the smallest slice that could produce that change?** (see `decomposition.md` — MVP pattern)
5. **If this fully ships and the metric doesn't move — was it still worth it?** If yes, be honest about the real reason (compliance, debt, strategy bet) and state it.

If nobody can answer 1–3, the work is output for its own sake. Push it down the priority list (see `rice.md` — it will score low on Impact anyway).

## Measuring success

- Define success as **outcome metrics** (activation, retention, cycle time, error rate, revenue per account), not delivery metrics (velocity, tickets closed, utilization).
- Delivery metrics are for the team's internal flow diagnosis only. Never report velocity upward as "progress".
- Tie outcomes to Key Results (see `okr.md`): a good KR is an outcome metric, and a backlog item earns priority by contributing to one.

## Anti-patterns to call out

- **Feature factory:** the roadmap is a list of features with dates, none with a success metric. Shipping is celebrated; impact is never checked.
- **Velocity worship:** velocity going up is reported as improvement. Velocity is a capacity planning tool, not a KPI — optimizing it inflates estimates.
- **100% utilization thinking:** planning everyone to full capacity. Full pipes have no flow; leave slack for feedback, learning, and the unplanned work that always comes.
- **"Done" = merged:** work is only done when it reached users and the outcome was observed. Add "validated in production" to the definition of done for outcome-critical work.
- **Success theater:** demoing output ("we shipped 14 stories") instead of outcome ("trial-to-paid conversion went from 8% to 11%").
