# Working Examples

These examples illustrate the spirit of the workflow, not a fixed template. Adapt the format to whatever is clearest for the task.

## 1. Small-task bypass (no workers needed)

If the work is small, clear, and needs no independent review—like fixing a typo in a CLI help string—skip workers and extra tracking:

> This is a single-line help-text fix in `cli/flags.py` with no behavioral or review risk, so I handled and verified it directly. Fixed line 48, ran `pytest tests/test_cli.py` (12 passed), and checked `--help` output.

## 2. Worker briefs (varied styles, always self-contained)

Keep every brief self-contained so parallel workers never depend on another worker's prompt. Spin up as many workers as the task needs, and no more.

### Parallel investigators (plain prose + structured examples)

> **Investigator A (read-only, `billing-worker@main`):** Trace where failed renewal cancellation fires and how retries are currently scheduled for issue #412. Follow `billing-worker/AGENTS.md`. Use whatever search, docs, web research, or safe read-only checks you need without editing repos or shared state. Return a short, corroborated finding with exact file paths, line ranges, commit SHA, any open uncertainty, and what decision it informs. Stop and report back if behavior depends on an external contract we can't inspect or crosses into `orders-api`.

```text
Role: Investigator B (read-only)
Question: Do legacy clients of orders-api tolerate a new subscription status value on GET /v1/subscriptions? Return a corroborated finding.
Inputs: orders-api@main, issue #412, orders-api/AGENTS.md.
Tools and bounds: Full investigative access (repo/history search, local docs, web research, diagnostics, safe read-only checks). Do not edit repos or shared state.
Return: Short finding, what you checked, exact file:line pointers and commit SHA, uncertainties, and next decision.
Stop if: Compatibility depends on client repos outside our workspace.
```

### Implementer (scoped writes)

> **Implementer (`orders-api@2c81f0a`, branch `feat/412-api`):** Add a nullable `grace_until` column on `subscriptions` and expose it on `GET /v1/subscriptions` while keeping `status: "active"` during grace (approved design for issue #412). Keep the migration backwards-compatible and leave public webhooks unchanged. Follow `orders-api/AGENTS.md`. Limit edits to migration and subscription API files on `feat/412-api` and use the `pg16-empty` test database so you don't collide with other workers. Return your commit SHA, files changed, exact test commands and log pointers, and any open uncertainties. Stop if the migration needs an `ACCESS EXCLUSIVE` lock on a hot table or affects webhook payloads.

### Advisor (before recommending a consequential design)

> **Advisor (read-only, `orders-api@2c81f0a`):** We need to represent a 72-hour renewal grace period without breaking legacy API clients that require `status: "active"` (`orders-api/docs/client-compat.md:41-58`), keeping migrations backwards-compatible and public webhooks unchanged. Follow `orders-api/AGENTS.md`. I'm considering a separate `subscription_grace_windows` join table queried on every `GET /v1/subscriptions` call. Challenge this before I recommend it: what can we reuse or remove? Is there a cheaper, easier, smaller, or better abstraction? What am I missing? Give me the strongest credible alternative with concrete evidence and tradeoffs.

### Correctness reviewer (fresh, read-only)

```text
Role: Correctness reviewer (read-only)
Approved outcome: Issue #412 — 72-hour grace period, daily retries, immediate client banner after first failed charge, unchanged public webhooks, nullable `grace_until` with `status: "active"` during grace.
Integrated artifact: `orders-api@8f1c209`, `billing-worker@9a3f118`, `client-web@4d20e7a`.
Inputs: `orders-api/AGENTS.md`, `billing-worker/AGENTS.md`, `client-web/AGENTS.md`, and each repo's local review skills.
Observed evidence: `artifacts/e2e-grace-v1.log`, per-repo test logs, migration diff.
Return: Pass/fail verdict per approved requirement, exact commits and logs checked, concrete defects or coverage gaps with file:line pointers, and residual risks.
Stop if: Observed logs don't match the stated commits or integration fixtures cannot be verified.
```

### Workflow-improvement worker (periodic)

> **Workflow-improvement worker (read-only on repos):** Milestone 1 (`orders-api` and `billing-worker`) just wrapped with `client-web` and integration still ahead. Review our accessible traces and logs (following `orders-api/AGENTS.md` and `billing-worker/AGENTS.md`) for where context, tokens, or time went—noisy reads, rework, or slow loops. Propose small, reversible tweaks I can try on upcoming work, or draft a proposed diff for this skill if you spot a recurring skill-level gap (don't edit installed or published skills). If things are running cleanly, say so.

## 3. What a good worker report looks like (good vs. bad)

Compact, verifiable returns are what actually keep the lead's context clear.

**Good return (short, corroborated, actionable):**
> **Finding:** Legacy v1 clients crash on unknown subscription statuses, so `status` must stay `"active"` during grace.
> **Checked:** Inspected the v1 API compatibility contract, client deserialization tests, and git blame on the status enum (`orders-api@2c81f0a`).
> **Pointers:** `orders-api/docs/client-compat.md:41-58` (strict enum allowlist); `orders-api/tests/test_v1_contract.py:112-129` (fails on any value outside `active`/`past_due`/`canceled`).
> **Uncertainty:** One mobile client repo isn't in this workspace, though the server-side contract explicitly covers it.
> **Next decision:** Represent grace via a separate field (like `grace_until`) or a separate table instead of a new status value.

**Bad return (vague, pointerless—forces the lead to re-investigate):**
> I looked through `orders-api` and retry handling looks mostly safe, though some older clients might be picky about status fields. Let me know if you want me to check anything else.

**Bad return (raw dump—floods the lead's context):**
> *(Pastes 300 lines of `git log`, full contents of `client-compat.md`, and entire pytest output into the reply.)*

## 4. Keeping the human in decision space

Write updates like a sharp colleague, not a rigid form. Lead with status (**continuing**, **blocked**, or **waiting on you**), orient fast, and reframe technical details into outcomes.

### Reframing an implementation question (bad vs. good)

**Bad (drops the human into raw implementation detail cold):**
> Do you want me to revise the tone mapping to use a full gradient curve or a two-point linear adjustment?

**Good (brings them up to speed in plain prose, asks in decision space):**
> **Waiting on you.** You asked to make dark portraits easier to see without ruining outdoor shots. On your test photos, lifting dark shadows strongly enough to brighten faces also washes out bright skies. I recommend protecting the sky, which brightens faces moderately while leaving the deepest shadows a bit darker; the alternative is prioritizing faces even when bright skies blow out. Which should the default favor? I'm holding the pipeline default until you choose.

### Kickoff orientation (respecting settled constraints)

When the human already said *"keep public webhooks unchanged unless I approve a change,"* carry that forward without re-asking it and surface only genuinely open calls:

> **Continuing investigation (one early call for `client-web`).** Issue #412 (72-hour grace period across `orders-api`, `billing-worker`, and `client-web`, keeping public webhooks unchanged). Two investigators are running in parallel on cancellation behavior in `billing-worker` and legacy client compatibility in `orders-api`. Before UI work starts, should the warning banner appear immediately after the first failed charge (recommended—gives customers the full 72 hours to update their card) or wait until 24 hours remain (quieter for transient declines, less time to act)?

### Partial block vs. full block

**Partial block (safe work keeps moving):**
> **Waiting on you for `portal-web` (other work continuing).** Issue #518 (trial-expiry banner): `accounts-api` (`c31d804`) is done and passing, and `notify-jobs` is moving ahead since reminder emails don't depend on banner UX. For `portal-web`, should the banner be dismissible for the current session (recommended—matches our other alerts) or pinned across pages until the trial converts (harder to ignore, more intrusive)? Once you pick, I'll kick off `portal-web`.

**Full block:**
> **Blocked.** Issue #603 (move invoice exports from Bucket A to Bucket B): all three export jobs read `EXPORT_BUCKET` (`infra/env.toml:14-29`), so the code change is small, but Bucket B doesn't exist in staging yet. Creating it depends on whether we backfill the last 90 days of PDFs (recommended—keeps old support links on one bucket) or send only new exports to Bucket B (zero backfill, requires dual-read fallback for 90 days). Everything else is paused until you choose.

## 5. Protecting your context in practice (bad vs. good reads)

- **Bad lead read:** Running `cat artifacts/e2e-grace-v1.log` (4,000 lines) or opening an implementer's full worker chat to see why a test failed.
- **Good targeted read:** Searching `artifacts/e2e-grace-v1.log` for the failing request ID with a few lines of context, or reading just the 25 cited lines of `orders-api/migrations/042_grace_until.sql:12-36` when an implementer flags a table lock that directly informs your rollout call.
- **Fresh verifier instead of transcript reading:** When an implementer claims a flaky cross-repo race is fixed without explaining the root cause, don't audit its whole chat or take the summary on faith. Search the saved log for the failing request ID first; if the cause is still unclear, send a fresh read-only verifier to check it.
- **Light status checks & narrow follow-ups:**
  - *Quiet worker:* "Status check: send your last verified fact, next step, and any blocker in three lines."
  - *Vague investigator reply:* "Confirm whether `retry_invoice` runs inside the billing transaction and cite the exact file and line range."

## 6. End-to-end three-repo walkthrough

Issue #412 adds a 72-hour grace period across `client-web`, `orders-api`, and `billing-worker` (keeping public webhooks unchanged).

1. **Parallel investigation:** Launch Investigators A and B in parallel and ask the human about banner timing. Investigator B returns the compact report above citing `orders-api/docs/client-compat.md:41-58`; you read just those 18 lines to confirm.
2. **Advisor before recommending:** Before recommending a join table, a fresh read-only advisor challenges it and proposes a single nullable `grace_until` column on `subscriptions`. You present both options in decision space; the human approves `grace_until` and immediate banner display.
3. **Scoped implementation:** Continue Investigator A into implementing `billing-worker`—granting write scope on branch `feat/412-worker` so it keeps its diagnosis—while starting a fresh implementer on `orders-api` (`feat/412-api`, `pg16-empty`).
4. **Milestone workflow review:** After `orders-api` and `billing-worker` wrap, run a fresh workflow-improvement worker while `client-web` and integration remain ahead. It spots slow, repeated cold dependency installs and proposes using a local package cache; you try it on `client-web`, note the change in your update, and undo it if it breaks installs or stops helping. (If it spots a recurring skill-level gap instead, it returns a proposed diff for this skill for human approval.)
5. **Rebase, selective reuse, and independent correctness review:** `orders-api@main` takes an unrelated hotfix, so you rebase `feat/412-api` to `8f1c209` and rerun `orders-api` tests while reusing unchanged `billing-worker` results (`9a3f118`). You run the integrated `e2e-grace-v1` suite across `orders-api@8f1c209`, `billing-worker@9a3f118`, and `client-web@4d20e7a` and hand `artifacts/e2e-grace-v1.log` to a fresh read-only correctness reviewer.
6. **Catch, narrow fix, and verified sign-off:** The reviewer catches a manual-payment path in `orders-api` that fails to clear `grace_until`. An implementer fixes it in `orders-api@c4d9012` with a regression test, you rerun the integrated suite (`artifacts/e2e-grace-v2.log`, all green), and the same reviewer verifies the new commit and log and signs off.
7. **Mid-flight scope change:** If the human instead says mid-task *"Actually, emit a new `subscription.grace_started` webhook"*, don't just patch tests—reopen the advisor critique, human contract approval, implementation, and fresh correctness review.
8. **Unavailable reviewer fallback:** If your host cannot run a fresh correctness reviewer after step 5, leave the required review open instead of approving it yourself:

> **Correctness review open (merge held).** The `e2e-grace-v1` suite passed across `orders-api@8f1c209`, `billing-worker@9a3f118`, and `client-web@4d20e7a` (`artifacts/e2e-grace-v1.log`), but no independent reviewer was available on this host. Holding merge until a fresh reviewer (human or separate session) verifies the approved outcome against these exact commits and logs.
