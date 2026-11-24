# Working Examples

## 1. Starting the skill (and when to bypass it)

### Native skill invocation vs. plain Markdown

On hosts with native skill loading, select `coordinate-initiative` from the skill menu or invoke it with the host's skill command before stating the task. On hosts without native skill loading, attach or paste `SKILL.md` with an explicit request:

```text
Follow the attached coordinate-initiative skill as the lead for issue #412 ("Invoice grace-period retries") across client-web, orders-api, and billing-worker.

Failed renewals should enter a 72-hour grace period with daily retries and a client banner. Keep public webhooks unchanged unless I approve a change, keep migrations backwards-compatible, and run an independent correctness review before merge.

Start by checking our riskiest assumption and surfacing any initial decisions before editing.
```

### Small-task bypass (no workers needed)

If `coordinate-initiative` is invoked for a small, clear task with no independent-review requirement—say, fixing a typo in a CLI help string—just do and verify it directly:

> This is a single-line help-text fix in `cli/flags.py` with no behavioral or review risk, so I handled and verified it directly without workers. Fixed line 48, ran `pytest tests/test_cli.py` (12 passed), and checked `--help` output.

## 2. Self-contained worker briefs

Keep every brief self-contained so parallel workers never depend on another worker's prompt. Spin up as many workers as the task genuinely needs—and no more.

### Parallel investigators (read-only)

```text
Role: Investigator A (read-only)
Question: In billing-worker, where does failed renewal cancellation fire, and how are retries scheduled? Return a corroborated finding.
Inputs: billing-worker@main, issue #412, billing-worker/AGENTS.md.
Tools and boundaries: Repo and history search, local docs, public web search (public concepts only; no private code, internal names, or raw traces), diagnostics, and safe read-only checks. Do not edit repositories or shared state. Do not recruit sub-workers.
Return: Corroborated finding, what you checked, exact file paths, line ranges, commit SHA, open uncertainties, and the next decision or action.
Stop and escalate: Stop if behavior depends on an external service contract we cannot inspect or requires changes inside orders-api.
```

```text
Role: Investigator B (read-only)
Question: Do legacy clients of orders-api tolerate a new subscription status value on GET /v1/subscriptions? Return a corroborated finding.
Inputs: orders-api@main, issue #412, orders-api/AGENTS.md.
Tools and boundaries: Repo and history search, local docs, public web search (public concepts only; no private code, internal names, or raw traces), diagnostics, and safe read-only checks. Do not edit repositories or shared state. Do not recruit sub-workers.
Return: Corroborated finding, what you checked, exact file paths, line ranges, commit SHA, open uncertainties, and the next decision or action.
Stop and escalate: Stop if compatibility depends on client repos outside our workspace.
```

### Implementer (scoped writes)

```text
Role: Implementer (orders-api)
Outcome: Add nullable `grace_until` to subscriptions and expose it on GET /v1/subscriptions while preserving `status: "active"` during grace (approved design). Keep the migration backwards-compatible and public webhooks unchanged.
Inputs: orders-api@2c81f0a, issue #412, approved decisions (nullable `grace_until`, `status` stays `"active"`, webhooks unchanged), orders-api/AGENTS.md.
Tools and boundaries: Write access limited to `orders-api` branch `feat/412-api` (migration and subscription API files only—do not touch billing webhook handlers or collide with other workers) and the `pg16-empty` test database. Investigative tools enabled; public web queries use public concepts only. Do not recruit sub-workers.
Return: Commit SHA, files changed, exact test commands and log pointers, and open uncertainties.
Stop and escalate: Stop if the migration requires an `ACCESS EXCLUSIVE` lock on a hot table or changes public webhook payloads.
```

### Advisor (before recommending a consequential design)

```text
Role: Advisor (read-only)
Goal: Represent a 72-hour renewal grace period without breaking legacy API clients.
Proposed approach: Add a separate `subscription_grace_windows` join table queried on every GET /v1/subscriptions call.
Constraints: Backwards-compatible migration, public webhooks unchanged, legacy clients require `status: "active"`.
Inputs: orders-api@2c81f0a, `orders-api/AGENTS.md`, `orders-api/docs/client-compat.md:41-58`, billing-worker investigation summary.
Tools and boundaries: Read-only access with repo/history search, local docs, public web search (public concepts only), and safe checks. Do not edit repositories or shared state. Do not recruit sub-workers.
Return: What we can reuse or remove; whether a cheaper, easier, smaller, or better abstraction fits; the strongest credible alternative with evidence and tradeoffs; and anything we appear to be missing.
Stop and escalate: Stop if judging the tradeoff depends on production query volume or schema constraints missing from the repo.
```

### Correctness reviewer (fresh, read-only)

```text
Role: Correctness reviewer (read-only)
Approved outcome: Issue #412 — 72-hour grace period, daily retries, immediate client banner after first failed charge, unchanged public webhooks, nullable `grace_until` with `status: "active"` during grace.
Integrated artifact: `orders-api@8f1c209`, `billing-worker@9a3f118`, `client-web@4d20e7a`.
Inputs: `orders-api/AGENTS.md`, `billing-worker/AGENTS.md`, `client-web/AGENTS.md`, and each repo's applicable local review skills.
Observed evidence: `artifacts/e2e-grace-v1.log`, per-repo test logs, migration diff.
Tools and boundaries: Read-only access with repo/history search, local docs, diagnostics, and safe verification commands. Do not edit repositories or shared state. Do not recruit sub-workers.
Return: Pass/fail verdict against each approved requirement, exact revisions and logs verified, concrete defects or missing coverage with file/line pointers, and residual risks.
Stop and escalate: Stop and report if observed logs do not match the stated commits or if required integration fixtures cannot be verified.
```

### Workflow-improvement worker (periodic)

```text
Role: Workflow-improvement worker (read-only on repos)
Scope: Review how Milestone 1 (`orders-api` and `billing-worker`) ran before `client-web` and integration start.
Inputs: Accessible task traces and logs, `orders-api/AGENTS.md`, `billing-worker/AGENTS.md`, prior tweaks: none.
Tools and boundaries: Read-only access to traces, logs, and repo docs. Do not edit repositories, installed skills, or published skills. Do not recruit sub-workers.
Return: Where context, tokens, or time went (rework, noisy reads, slow steps), at most one small reversible tweak to our task instructions (with how we'll tell if it helped), or a proposed diff for this skill if you see a recurring skill-level failure—or "no change warranted."
Stop and escalate: Stop if traces and logs are both inaccessible.
```

## 3. Keeping the human in decision space

### Bad vs. good: reframing an implementation question

**Bad (drops the human into raw implementation detail cold):**
> Do you want me to revise the tone mapping to use a full gradient curve or a two-point linear adjustment?

**Good (orients first, then asks in decision space):**
> **Waiting on you.** Goal: Make dark portraits easier to see without ruining outdoor shots.
> - **What surfaced:** Lifting dark shadows strongly enough to brighten faces also washes out bright skies on your test photos.
> - **Options:**
>   1. **Protect the sky (recommended):** Keeps skies natural and brightens faces moderately, though the deepest shadows stay a bit dark.
>   2. **Prioritize faces:** Makes faces pop even in harsh backlight, at the cost of blowing out bright sky detail.
> - **What your answer unlocks:** Locking the default brightness behavior and finishing the image pipeline tests.

### Kickoff orientation (respecting settled constraints)

Because the starter prompt already settled webhooks ("keep public webhooks unchanged unless I approve a change"), carry that forward instead of re-asking it, and surface only a genuinely open product call:

> **Continuing investigation; one early product call needed before UI work.** Goal: Issue #412 (72-hour grace period for failed renewals across `orders-api`, `billing-worker`, and `client-web`, keeping public webhooks unchanged).
> - **Active now:** Two investigators are running in parallel—one tracing cancellation in `billing-worker`, one checking legacy client compatibility in `orders-api`.
> - **Waiting on you (for `client-web`):** Should the grace-period warning banner appear immediately after the first failed charge (recommended—gives customers the full 72 hours to update their card), or only when 24 hours remain (quieter for transient declines, less time to act)?
> - **What your answer unlocks:** Finalizing the `client-web` UX spec once backend investigation returns.

### Partial block vs. full block

**Partial block (safe work keeps moving):**
> **Waiting on you for `portal-web` (other work continuing).** Goal: Issue #518 (Trial-expiry warning banner).
> - **Done:** `accounts-api` (`c31d804`) exposes trial-expiry dates; tests pass.
> - **Continuing:** `notify-jobs` is implementing reminder emails (independent of banner UX).
> - **Waiting on you:** Should the warning banner be dismissible for the current session (recommended—matches our other account alerts) or pinned across pages until the trial converts (harder to ignore, more intrusive)? Once you choose, I'll start `portal-web`.

**Full block (nothing safe can proceed):**
> **Blocked waiting on you.** Goal: Issue #603 (Migrate invoice exports from Bucket A to Bucket B).
> - **Done:** Confirmed all three export jobs read the bucket name from `EXPORT_BUCKET` (`infra/env.toml:14-29`).
> - **What surfaced:** Bucket B does not exist in staging yet, and provisioning it depends on whether we backfill the last 90 days of PDFs (which changes IAM scope and cutover time).
> - **Options:** (1) Backfill 90 days first (recommended—single bucket for support links, ~20 min copy) or (2) new exports only to Bucket B (zero backfill, requires dual-read fallback for 90 days).
> - **What your answer unlocks:** Provisioning Bucket B and starting implementation. No independent work can safely run until this is settled.

## 4. Protecting your context in practice

- **Search narrow before reading wide:** When an integration test fails in a 4,000-line log (`artifacts/e2e-grace-v1.log`), search for the failing request ID or assertion with a few lines of context instead of pulling the whole file into your thread.
- **Justified direct read:** An implementer flags a potential `ACCESS EXCLUSIVE` lock and cites `orders-api/migrations/042_grace_until.sql:12-36`. Reading those 25 lines directly informs your rollout call without flooding your context.
- **Fresh verifier instead of transcript reading:** An implementer claims a flaky cross-repo race is fixed without explaining the cause. Do not pull its entire chat into your thread or take the claim on faith. Search the saved log for the failing request ID first; if the root cause is still unclear, send a fresh read-only verifier to check the failure.
- **Light status checks & narrow follow-ups:**
  - *Quiet worker:* "Status check: send your last verified fact, next step, and any blocker in three lines."
  - *Vague investigator claim ("retry handling looks safe"):* "Confirm whether `retry_invoice` runs inside the billing transaction and cite the exact file and line range."

## 5. End-to-end three-repo walkthrough

Issue #412 adds a 72-hour grace period across `client-web`, `orders-api`, and `billing-worker` (keeping public webhooks unchanged).

1. **Parallel investigation:** Launch Investigators A and B in parallel and ask the human about banner timing. Investigator B corroborates that two legacy clients reject unknown statuses (`orders-api/docs/client-compat.md:41-58`); you read just those 18 lines to confirm.
2. **Advisor before recommending:** Before recommending a separate join table, a fresh read-only advisor challenges it and proposes a single nullable `grace_until` column on `subscriptions` instead. You present both options in decision space; the human approves `grace_until` and immediate banner display.
3. **Scoped implementation:** Continue Investigator A into implementing `billing-worker` (retaining its diagnosis) while launching a fresh implementer on `orders-api` with a non-overlapping write scope.
4. **Milestone workflow review:** After `orders-api` and `billing-worker` finish—with `client-web` and integration still ahead—run a fresh workflow-improvement worker. It spots repeated cold dependency installs (~95s per run) and proposes trying a local package cache for `client-web`. You try it and note the change in your update:
   - *If it helps* (setup drops to 18s with zero errors): keep it for remaining runs.
   - *If it regresses* (a stale lockfile breaks installs): undo the cache tweak immediately, restore clean installs, and note the rollback.
   - *If it spots a recurring skill-level gap* (e.g., briefs keep missing repo rules): it returns a proposed diff for this skill for your review instead of editing the installed skill directly.
5. **Rebase, integrated check, and independent correctness review:** `orders-api@main` takes an unrelated hotfix, so you rebase `feat/412-api` to `8f1c209` and rerun `orders-api` tests while reusing unchanged `billing-worker` results (`9a3f118`). You run `e2e-grace-v1` across all three commits (`orders-api@8f1c209`, `billing-worker@9a3f118`, `client-web@4d20e7a`) and hand `artifacts/e2e-grace-v1.log` to a fresh read-only correctness reviewer.
6. **Catch, narrow fix, and verified sign-off:** The reviewer catches a manual-payment path in `orders-api` that fails to clear `grace_until`. An implementer fixes it in `orders-api@c4d9012` with a regression test, you rerun the integrated suite (`artifacts/e2e-grace-v2.log`, all green), and the same reviewer verifies the new commit and log and signs off.
7. **Mid-flight scope change (when it happens):** If the human instead says mid-task *"Actually, emit a new `subscription.grace_started` webhook"*, don't just patch tests—reopen the affected advisor critique, human contract approval, implementation, and fresh correctness review.
8. **Unavailable reviewer fallback:** If your host cannot run a fresh correctness reviewer after step 5, hold merge and report plainly:

> **Correctness review open (merge held).** The `e2e-grace-v1` suite passed across `orders-api@8f1c209`, `billing-worker@9a3f118`, and `client-web@4d20e7a` (`artifacts/e2e-grace-v1.log`), but no independent reviewer is available on this host. Merge is held until a fresh reviewer (human or separate session) verifies the approved outcome against these exact commits and logs.
