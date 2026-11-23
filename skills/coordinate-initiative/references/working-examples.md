# Working Examples

## Starting the skill

On tools with native skill loading, select `coordinate-initiative` from the skill menu or invoke it with the tool's native skill command before stating the task. On tools without native skill loading, paste or attach `SKILL.md` with an explicit request like this one.

```text
Follow the attached coordinate-initiative skill as the lead for issue #412 ("Invoice grace-period retries") across client-web, orders-api, and billing-worker.

Failed renewals should enter a 72-hour grace period with daily retries and a client banner. Keep public webhooks unchanged without my approval, keep migrations backwards-compatible, and run an independent acceptance review before merge.

Start by checking our riskiest assumption and surfacing any initial decisions before editing.
```

## Copyable brief and role hints

```text
Role: Investigator (read-only)
Question: In billing-worker, where does failed renewal cancellation fire, and how are retries scheduled?
Inputs: billing-worker@main, issue #412, billing-worker/AGENTS.md.
Tools and boundaries: Repo and history search, local docs, public web search using public concepts only, and safe checks in a temp directory. Do not edit repositories or shared state.
Return: Short outcome, what you checked, exact file paths, line ranges, commit SHA, open uncertainties, and the next decision or action.
Stop and escalate: Stop if behavior depends on an external service contract we cannot inspect or affects orders-api.
```

Adapt that brief for other roles while keeping local skills and tool boundaries steady. For an **implementer**, replace Question with the owned outcome, replace the read-only permission line with authorized owned write scope for its exclusive branch and fixtures, and require commit SHA plus exact test commands and logs. For an **advisor**, supply the goal, proposed path, constraints, and evidence pointers, and ask what to reuse or remove plus the strongest simpler alternative and tradeoffs. For an **acceptance reviewer**, keep it read-only and pass the approved outcome, exact integrated revisions, and observed test logs. For a **workflow-improvement worker**, pass the prior experiment, baseline, and authorized trace or checkpoint pointers, and allow at most one reversible candidate note outside the repo or a versioned durable patch proposal when recurring failures warrant it.

## Three-repo rollout and checkpoint

Issue #412 adds a 72-hour grace period for failed renewals across `client-web`, `orders-api`, and `billing-worker`. At kickoff, your riskiest assumption is whether older API clients accept a new subscription status, and you face an early decision on whether public webhooks should change during grace. You ask the human for that webhook decision while sending read-only investigators to check client compatibility and current worker cancellation.

The API investigator reports that two legacy clients reject unknown status values and cites `orders-api/docs/client-compat.md:41-58`. You read just those 18 lines to verify. Once the human keeps webhooks unchanged (D-1), a fresh read-only advisor challenges your proposed join table and recommends a single nullable `grace_until` column instead, which the human approves (D-2).

With both decisions settled, you re-brief the `billing-worker` investigator into an implementation role with authorized write access and exclusive ownership of branch `feat/412-worker` and its test database, and start a fresh implementer on `orders-api` with exclusive branch ownership. After `orders-api` and `billing-worker` finish Milestone 1 with `client-web` and integration still ahead, you run a fresh workflow-improvement worker, which spots repeated dependency installs in the trace and writes one reversible cache note outside the repos with a baseline, improvement target, and rollback trigger. At the checkpoint, you decide to try that note on the upcoming `client-web` assignment and note the change in your next human update.

```markdown
# Checkpoint — Issue #412 (Invoice grace-period retries)

Outcome: Failed renewals get a 72-hour grace period with daily retries and a client banner, and public webhooks stay unchanged unless approved.
Decisions:
- D-1 (Human approved) Keep public webhooks unchanged during grace and emit `subscription.canceled` only after grace expires.
- D-2 (Human approved) Add nullable `grace_until` on `GET /v1/subscriptions` and keep `status: "active"` for older clients.
State and ownership:
- `orders-api` (`feat/412-api`, commit `4e9b210`) Migration and API field done, ownership released.
- `billing-worker` (`feat/412-worker`, commit `9a3f118`) Daily retry loop done, ownership released.
- `client-web` (`feat/412-ui`, not started) Queued next for a fresh implementer.
Evidence:
- Legacy clients reject unknown status values and require preserving `status: "active"` (`orders-api/docs/client-compat.md:41-58`, `orders-api@2c81f0a`).
- `orders-api` unit and migration suite passed on `4e9b210` (`pg16-empty`) in `artifacts/orders-api-4e9b210-test.log`.
- `billing-worker` integration suite passed on `9a3f118` (`worker-clock-mock`) in `artifacts/billing-worker-9a3f118-test.log`.
- Workflow trial candidate recorded in `.scratch/workflow-candidate-1.md` outside tracked repos and approved to try on `client-web`.
Open gaps: `client-web` implementation, cross-repo integration run, and fresh acceptance review are pending.
Next check: Start `client-web`, check the cache trial against its baseline and rollback trigger when it finishes, verify all three commits for drift, run `e2e-grace-v1`, and send the integrated results to a fresh acceptance reviewer.
```

When `client-web` finishes, you check the cache trial at the checkpoint. Because dependency setup dropped below the baseline with no cache errors, you keep the note for the remaining work instead of rolling it back. Before integration, `orders-api@main` takes an unrelated hotfix, so you rebase and recheck only `orders-api` while keeping unchanged `billing-worker` results tied to `9a3f118`. You then run the integrated `e2e-grace-v1` suite on all three exact commits and hand the outcome and `artifacts/e2e-grace-v1.log` to a fresh read-only acceptance reviewer. It catches a manual-payment path that fails to clear `grace_until`. After a narrow fix and a fresh integrated rerun, the same reviewer verifies the fix and signs off.
