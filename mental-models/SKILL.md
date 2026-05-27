---
name: mental-models
description: Apply a Munger-style lattice of mental models to a STRATEGIC OR BUSINESS decision, plan, or trade-off (hire, pivot, price, partnership, prioritization, scope, capital allocation, hiring, metric design). Picks the 2–3 models that most change the conclusion rather than listing all of them. Use when the user says "apply mental models", "Munger", "second-order effects", "inversion", "first principles on this", "what models apply". NOT for code-level decisions — use /paranoid-review or /codex challenge for those. Distinct from lateral-thinking (cross-domain divergence — finds non-obvious connections): this skill is convergent, applying known frameworks as decision lenses.
---

# Mental Models

> Charlie Munger: "You've got to have models in your head and you've got to array your experience — both vicarious and direct — on this latticework of models. The first rule is that you can't really know anything if you just remember isolated facts."

## When to Fire

- User is making a decision and wants framework-driven analysis (not gut feel, not pure data).
- Explicit invocation: "mental models", "Munger", "inversion", "second-order", "first principles" *applied to a decision*.
- A plan or strategic choice is on the table where the obvious answer might be wrong.

## When NOT to Fire

- Code-level questions — use [[paranoid-review]] or `/review`.
- Cross-domain creative thinking — use `/lateral-thinking` instead (it's divergent; this skill is convergent).
- Pure factual questions with a single correct answer.
- Premortem on a specific change — use [[premortem]].

## The Lattice

Don't apply all of them. Pick the 2–3 that most change the conclusion. Models are tools — using all of them on every problem is the mark of an amateur.

### Inversion
"Instead of asking how to achieve X, ask: what would *guarantee* failure?"
Then avoid those. Often easier than positive planning.
Example: "How do we keep top talent?" → "What would make our best people quit fastest?"

### Second- and Third-Order Effects
The first-order effect is what you intended. The second-order is what other actors do in response. The third-order is what happens to the system when many actors do that.
Ask: "And then what?" three times.
Example: "Cap rents." → 1st: rents capped. 2nd: landlords stop maintaining. 3rd: housing stock degrades, supply shrinks.

### Opportunity Cost
The cost of any choice is the next-best alternative you didn't pick. If the alternative isn't articulated, the cost is invisible — and probably underestimated.
Force: "If we don't do X, what do we do with the resources?"

### Base Rates
"What fraction of similar attempts succeed?"
Most plans assume the inside view (this one is different) and ignore the outside view (most plans like this fail at rate R).
If R is high, the plan needs an explicit account of why it's not the median attempt.

### Incentives (Whose payoff structure?)
Look at every actor in the system — including yourself. What gets each one rewarded? Is the proposed change aligned or fighting incentives?
Munger: "Never, ever, think about something else when you should be thinking about the power of incentives."

### Via Negativa
Improvement by removal, not addition. What can be cut, deleted, simplified, stopped?
Most complex systems improve more from subtraction than from new features.

### Margin of Safety (Graham/Buffett)
If your estimate is wrong by X%, does the decision still work? If a buffer of zero, the plan assumes perfect execution. That's a planning failure regardless of skill.

### Circle of Competence
Are we deciding inside the area where we have genuine signal, or outside it? Outside-circle decisions need either (a) someone inside the circle, or (b) explicit acknowledgment that the decision is uncertain and we should buy optionality, not commit.

### Lollapalooza (Convergent forces)
When multiple independent factors push the same direction, expect non-linear outcomes. Big wins and big disasters usually involve 3–5 forces converging. Look for the alignment, not the single cause.

### Asymmetric Payoffs
What's the worst case? The best case? If downside is bounded and upside is large (or vice versa), the EV calculation is dominated by the tail, not the mean.

### Goodhart's Law
"When a measure becomes a target, it ceases to be a good measure." Any time the decision involves metrics, incentives, OKRs, or KPIs — what behavior does the metric reward, and how does the system game it once people optimize for the number rather than the underlying goal?

### Hanlon's Razor
"Never attribute to malice what is adequately explained by stupidity (or incompetence, or misalignment of context)." Particularly relevant in postmortems, partner disputes, and "why did they do that" decisions. Stops you from over-modeling an adversary when the explanation is process failure.

### Path Dependence
Where you can go next depends on where you've been. Some decisions foreclose options that can't be reopened (publishing data, hiring senior staff, choosing a tech stack, public commitments). "Just rewrite it" usually fails because the system's *current shape* encodes history that the rewrite forgets.

## Workflow

### Step 1: Restate the decision

Strip jargon. What is actually being chosen between? If you can't reduce it to "should we do A or B (or C)", clarify with the user.

### Step 2: Pick the lenses (2–3, not all)

Scan the list. Which models are most likely to flip the conclusion? Heuristics:
- Big irreversible commitment → **Margin of Safety, Asymmetric Payoffs, Path Dependence**.
- Multi-party system → **Incentives, Second-Order, Hanlon's Razor**.
- Crowded space / common idea → **Base Rates, Opportunity Cost**.
- Stuck on positive framing → **Inversion**.
- Plan adds complexity → **Via Negativa**.
- Outside familiar domain → **Circle of Competence**.
- Decision involves metrics / OKRs / incentive design → **Goodhart's Law, Incentives**.
- "Why did X happen / why did they do that" → **Hanlon's Razor, Incentives**.
- Proposed rewrite, migration, or replacement → **Path Dependence, Opportunity Cost**.

### Step 3: Apply each chosen lens

For each:
- **What it surfaces** in this specific decision.
- **Whether it changes the conclusion** (and if so, how).
- **What new information** the lens makes you want to gather.

Be specific. "Incentives matter" is not analysis. "The sales team is compensated on closed deals not retained revenue, so they'll oversell and the churn rate in 6 months will be the constraint" is analysis.

### Step 4: Synthesize

One of:
- **The chosen lenses all agree** → "Decision X looks right, and here's the case from multiple angles."
- **The lenses disagree** → name the disagreement; usually means the decision rests on a specific empirical question. Identify it.
- **One lens dominates** → "Model M is the binding constraint here." Decide based on it.

Never end with "all the models say think carefully." That's not useful.

### Step 5: Identify what would change your mind — REQUIRED

This step is non-optional. State, in advance, the **specific** observation that would flip the conclusion. Not "if the data showed X was wrong" — what *data point*, in what *direction*, from what *source*?

If you cannot name a flip condition, the analysis was confirmation-shaped, not truth-shaped — go back to Step 2 and pick different lenses, or admit the decision rests on values rather than evidence and say so.

## Calibration

- Pick few models, apply them deeply. Listing all ten models with one line each is a magazine article, not analysis.
- Models exist to be discarded when they don't apply. "Inversion didn't change anything here, dropping it" is a healthy output.
- The goal is to *change at least one belief*. If applying the lattice leaves every prior unchanged, either you skipped a model or the decision didn't need this skill.

## Related skills

- For *cross-domain* analogies and non-obvious connections → `/lateral-thinking`
- For *prospective failure analysis* on a specific change → [[premortem]]
- For *honest disagreement* on a position → [[steelman]]
- For *strategic plan critique* with personas → `/plan-ceo-review`
