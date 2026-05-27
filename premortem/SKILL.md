---
name: premortem
description: Prospective failure analysis — assume the change/plan/launch has already failed catastrophically and work backwards to enumerate the most plausible failure modes before committing. Use when the user is about to ship, merge, deploy, launch, sign, or commit to a non-trivial decision; or when the user explicitly says "premortem", "pre-mortem", "what could go wrong", "before we ship", "stress test this", "kill criteria", "if this fails how". Operates at the DECISION or PLAN level — for adversarial review of specific code, use /paranoid-review or /codex challenge instead. Distinct from /investigate (post-failure root cause): premortem is prospective; investigate is post-mortem.
---

# Premortem

> Gary Klein's premortem inversion: instead of asking "will this work?", assume it already failed and reverse-engineer the headline.

## When to Fire

- User is about to **ship, merge, deploy, launch, or commit** to a decision that is hard to reverse.
- A plan is being finalized and the user is asking for a final check ("looks good", "anything else", "ready to send").
- Explicit invocation: "premortem", "pre-mortem", "what could go wrong", "stress test this".
- Proactively suggest (do not invoke silently) when the change is irreversible OR blast radius > one module OR user has expressed prior uncertainty.

## Anti-patterns — do NOT fire

- Reversible local changes (one file, can be reverted in 30 seconds).
- Code already shipped — use `/investigate` for post-failure analysis.
- User just wants encouragement, not stress-testing. Ask if unclear.

## Workflow

### Step 1: Lock the artifact

Get explicit confirmation of *what* is being premortem'd. One sentence. If it's a plan file or PR, read it first. If it's a decision, restate it in your own words.

> "Premortem target: [shipping X / merging PR #Y / launching Z by date D]. Confirm before I proceed."

Do not skip this. A premortem on the wrong artifact is wasted thought.

### Step 2: Elicit what's already been considered

Before generating new failure modes, ask once:

> "What failure modes have you already thought about and either mitigated or accepted?"

Without this, the skill cannot produce *new* insight — it just lists the user's existing worries back to them, which is theater. If the user says "none, that's why I'm asking" — proceed. If they list 2–3 — those become the floor, not the output.

### Step 3: Time-shift to the failure

> "It is [3 months / 6 months / 1 year — pick what matches the change's blast radius] from now. The thing failed. The headline reads: ____. What does it say?"

Generate **5–7 distinct failure headlines** that go *beyond* the ones the user already named in Step 2. Each must be a *different causal story*, not variations of the same one. Examples of distinct-vs-variant:

- "Users churned because onboarding was confusing" vs "Users churned because the empty state was confusing" → SAME cause (UX), one headline.
- "Users churned because onboarding was confusing" vs "Users churned because price went up 30%" vs "We never reached the target users because the channel didn't work" → THREE distinct causes.

### Step 4: For each headline, fill the triplet

| Failure | Probability (L/M/H) | Severity (L/M/H) | Leading indicator we'd see *before* the headline |
|--------|-----|-----|-----|
| ... | ... | ... | ... |

The leading indicator is the most important column. It's what makes a premortem actionable instead of just anxious.

### Step 5: Force the prioritization

Identify:
- **The 1–2 highest expected-loss failures** (probability × severity).
- **The 1 failure with the cheapest leading indicator to monitor** (sometimes a low-probability failure is worth monitoring because the canary is free).

### Step 6: Propose mitigations or kill criteria

For each prioritized failure, propose ONE of:
- **Mitigation:** a specific change to the artifact that reduces probability OR severity.
- **Monitor:** the leading indicator to watch, with a threshold.
- **Kill criterion:** the observation that should cause us to revert / pause / pivot, defined *in advance* so it can't be rationalized away later.

Mitigations that add complexity proportional to the failure's expected loss are net-negative. Say so if that's the case.

### Step 7: Verdict

Close with one of three:
- **Ship as-is** — failure modes considered, none cross the threshold for action.
- **Ship with [specific mitigations / monitors / kill criteria]** — enumerate.
- **Don't ship yet** — one of the failures is plausible enough that the artifact needs structural change first. Name what.

Never close with vague "be careful" or "watch for issues". A premortem that ends without a decision is theater.

## Quality bar

- **No generic failures.** "Performance might degrade" is not a finding. "P99 latency on /checkout will exceed 2s when promo traffic spikes because the new join query isn't indexed on `user_id`" is a finding.
- **Failures must be specific to THIS artifact.** If the same headline could be written about any product launch, it's noise.
- **Disagreement is the goal.** If every failure mode aligns with the user's existing concerns, the premortem failed — you only surfaced what they already feared. Push for at least one failure the user hasn't thought of.

## When the user dismisses a finding

If the user pushes back on a failure mode ("that won't happen", "we've handled that"), do NOT immediately drop it. Invoke [[steelman]] — evaluate whether the dismissal contains new information (e.g., a mitigation you didn't know about) or is just pattern-matching to "this won't happen to me". Only drop the finding if the new information is concrete; otherwise hold it and ask what specifically prevents it.

## Related skills

- For *post-failure* root cause analysis → `/investigate`
- For *code-level* adversarial review → [[paranoid-review]]
- For *decision-frame* alternatives → [[mental-models]] (inversion, second-order)
- For *plan-level* critique with personas → `/plan-ceo-review`, `/plan-eng-review`
- For *handling pushback on findings* → [[steelman]]
