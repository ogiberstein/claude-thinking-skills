---
name: paranoid-review
description: Adversarial code review focused on what breaks under hostile or unlucky conditions — malicious input, race conditions, partial failures, retry storms, resource exhaustion, trust-boundary violations, and silent failure modes. Use when the user says "paranoid review", "what could break", "edge cases", "stress this code", "hostile input", "review under load", "what would an attacker do". Distinct from /review (structural diff review), /codex (external-model second opinion), and /cso (infrastructure security audit) — this is single-model code-level edge-case hunting on a specific diff or file.
---

# Paranoid Review

> The code works for the happy path. This skill assumes the path is hostile.

## When to Fire

- User explicitly asks: "paranoid review", "what could break", "stress this", "edge cases", "hostile input".
- A diff touches: input parsing, auth, payments, retries, async operations, data migrations, anything user-facing.
- User is about to merge and wants a final adversarial pass *before* /codex (cheap pre-filter) or *after* /codex (deeper second pass).

## Anti-patterns — do NOT fire

- Pure refactors with no behavior change (run /review instead).
- Speculation about code you haven't read. **Findings must cite `file:line` for code-present issues, or follow the ABSENCE format (see Output Format) for missing-code issues. No findings without a concrete location.**
- "Just in case" hardening for failure modes that can't actually occur given upstream invariants.

## Required Inputs

Before starting, you must have ONE of:
- A specific diff (`git diff`, PR, or commit range).
- A list of files to review.
- A specific function or module.

If the user says "review my code" without scope, ask which. Reviewing an entire repo paranoid-style is incoherent.

## Handling large diffs

If the scope is >500 lines or >10 files, do NOT try to review holistically — quality collapses. Instead:

1. **Group by surface area:** input handlers, async paths, auth/authz, data persistence, external calls. Review each group as a sub-pass.
2. **Sequence by blast radius:** start with the group whose failure has the largest impact (usually auth or data writes).
3. **Stop the user mid-flow:** after each sub-pass, surface findings before continuing. Don't deliver a 50-finding report at the end.

If even one group is >500 lines, ask the user to narrow scope further. Paranoid review is depth-first, not breadth-first.

## The Six Lenses

Run each lens against the code in order. Skip lenses that don't apply, but say so explicitly.

### Lens 1 — Hostile Input

For every external input (HTTP body/params, file upload, env var, IPC, queue message, DB read from user-writable table):

- **Type confusion:** What if it's the wrong type? `null`, `undefined`, array-when-object, string-when-number, recursive structures.
- **Size:** No bound? Try 10MB. Try 10GB.
- **Encoding:** UTF-8 vs UTF-16, normalization (Turkish I, emoji ZWJ), control chars, BOM, percent-encoding double-pass.
- **Injection:** SQL, command, template, log injection, header injection, path traversal, prototype pollution.
- **Adversarial structure:** ReDoS (catastrophic regex backtracking), zip bombs, billion laughs, polyglots.

### Lens 2 — Concurrency

- **Race conditions:** Check-then-act, read-modify-write, double-submit, idempotency.
- **Ordering:** Does correctness depend on operations completing in a specific order? Is that guaranteed?
- **Atomicity:** Is the multi-step operation actually atomic? What state does it leave behind if the process dies between steps 2 and 3?
- **Lock scope:** Is the critical section too narrow (race) or too wide (deadlock, perf)?
- **Reentrancy & signals:** Can the function be called while it's already running (signal handlers, async callbacks, recursive triggers)? Does it mutate shared state without a guard?

### Lens 3 — Partial Failure

Networks fail mid-call. Disks fill. Processes get OOM-killed.

- **Mid-operation crash:** What does the system look like? Is the state recoverable?
- **Network split:** Caller thinks request failed; server processed it. Idempotency key? Retry-safe?
- **Retry storms:** Does the failure path retry? Does the retry path retry? Exponential backoff with jitter?
- **Cascading timeouts:** Service A calls B calls C. C times out at 30s, B at 10s, A at 5s. Which times out first?
- **Half-applied writes:** Multi-table writes without transactions; cache vs DB drift; search index lag.

### Lens 4 — Resource Exhaustion

- **Memory:** Unbounded buffers? Streams that buffer the whole response? Caches without eviction?
- **File handles / connections:** Opened in loops without close? Pool exhaustion under load?
- **CPU:** Quadratic loops on user-controlled N? Compression bombs? Regex catastrophic backtracking?
- **DB:** N+1 queries? Missing indexes on filter/join columns? Long-running queries blocking writes?
- **DoS amplification:** Does one cheap request trigger an expensive operation? Webhook fanout?

### Lens 5 — Trust Boundaries

- **Auth:** Is every endpoint that handles user data actually authenticated? Authorization checked at the *right* layer?
- **Data lineage:** Data from user input flowing to a privileged sink (exec, eval, SQL, template, file path, redirect URL) without sanitization at the boundary.
- **Cross-tenant:** Can user A read/write user B's data via parameter manipulation, ID guessing, IDOR?
- **Internal vs external:** Is "internal" code actually only reachable internally? VPN, network policy, SSRF.
- **LLM trust boundaries:** If the diff sends user input to an LLM or uses LLM output in a privileged context — prompt injection, output-as-code, tool-call confusion. Note: `/review` is the primary tool for this; flag any findings here but defer the deep audit to `/review`.

### Lens 6 — Observability Failures

- **Silent failures:** Empty catch blocks, ignored Promise rejections, `_, err := f()` without checking, fire-and-forget background tasks.
- **Logs without context:** "error occurred" with no user ID, request ID, or stack.
- **Metrics gaps:** Failure modes from lenses 1–5 — would we actually *see* them in production telemetry, or only learn from a user complaint?
- **Alert fatigue:** Alerts that page on conditions that don't require action.

## Output Format

For findings tied to specific code:

```
[Lens N] file:line — One-sentence scenario.
Impact: What breaks if this happens (data corruption / outage / leak / DoS / silent wrong answer).
Repro: Concrete inputs or sequence that triggers it.
Fix: Specific code change. If the fix is non-trivial, sketch the shape.
```

For findings about *absence of code* (no rate limiting anywhere, no idempotency key on retry path, no auth on internal endpoint):

```
[Lens N] ABSENCE: <what's missing>, scope: <which file(s) or module should have it>
Impact: ...
Repro: ...
Fix: ...
```

Absence findings are valid but must be specific about where the missing code *should* live. "No rate limiting" alone is too vague; "no rate limiting on POST /api/checkout — should be in `middleware/rateLimit.ts` upstream of the handler" is a finding.

End with a **prioritized list**: which 1–3 findings should block merge, which are nice-to-have, which are noted-but-acceptable given the system's actual threat model.

## Calibration

This skill should produce **fewer, sharper findings than a generic linter**. If you find 20 things, the signal-to-noise is wrong — re-rank and drop the bottom half. A merge-blocking finding must be defensible: "this fails in production within N weeks if traffic doubles" or "this is an attacker primitive". Speculation without a concrete repro is not a finding.

## Related skills

- For *external-model* second opinion → `/codex challenge`
- For *structural* diff review (SQL safety, LLM boundaries) → `/review`
- For *infrastructure* security (secrets, supply chain, CI/CD) → `/cso`
- For *prospective* failure analysis at decision level → [[premortem]]
