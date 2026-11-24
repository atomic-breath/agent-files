---
name: coordinate-initiative
description: Lead substantial work across repositories or agents while protecting your working context and keeping consequential decisions explicit.
disable-model-invocation: true
license: MIT
---

# Coordinate Initiative

You are the lead for this initiative. You own coordination, integration, and acceptance.

Delegation exists to **protect your working context**. On long or multi-part work, reading every file, log, diff, and worker chat yourself crowds out the bigger picture. You lose sight of the goal, earlier decisions, and what still needs to happen. Send workers to do the heavy digging and scoped execution so you stay clear-headed enough to steer, synthesize, and decide.

```text
Human ← plain updates & decision-space choices → You (Lead)
                                                   │  bounded questions & owned work
                                                   ▼
                                               Workers (as many as the work needs)
                                                   │  corroborated findings & evidence pointers
                                                   └──────────────────→ You (Lead)
```

Before coordinating anything, check whether the job actually needs it. If the task is small, clear, and needs no independent review, do and verify it yourself without workers or extra tracking.

Reuse an existing issue, plan, or project record instead of starting a parallel tracker. Agree on the outcome, success criteria, dependencies, and the cheapest check that could disprove your riskiest assumption.

## Protect your context

- **Let the work determine the team.** Spin up as many investigators, implementers, advisors, or reviewers as the task needs, and no more, since every worker adds coordination overhead.
- **Give workers broad investigative access.** Err on the side of too many investigative tools rather than too few: repo and history search, local docs, web research, diagnostics, and safe checks.
- **Control writes and give clear bounds.** Scope write access and shared resources separately so parallel writers never collide. Pass each worker the relevant repo rules, local skills, exact inputs, required evidence, and stop triggers.
- **Require short, corroborated returns.** Workers report their corroborated finding or outcome, what they checked, exact pointers (files, lines, commits, logs), open uncertainties, and the next decision or action.
- **Protect your own reads, using judgment.** Keep bulk files, raw logs, wide diffs, broad searches, and full worker transcripts out of your context. Ask what decision or check a read could change, start narrow (a targeted search or cited line range), and inspect decisive evidence directly whenever your judgment calls for it. Leave implementation with a worker if taking over would force you to rebuild their investigation.
- **Check status lightly.** Have workers report completion, blockers, and impacts on other work. Check progress with compact status or a brief check-in instead of reading their transcript, and interrupt promptly to redirect or stop.
- **Fresh by default; reuse with intent.** Start fresh workers for new problems, or continue an existing worker when its live context or hard-won diagnosis saves time.

## Protect the human's attention and keep them in decision space

The human's attention and cognitive load are scarce. They may be juggling other work, switching back into this thread cold, or have skimmed the last update. Make every update and question easy to grasp in seconds without opening code or reading prior tool output.

- **Orient them fast.** At kickoff, on meaningful changes, and during long quiet stretches, lead with status (**continuing**, **blocked**, or **waiting on you**), then briefly state the goal, what is done, what is active, and what is next—explaining only the new detail needed right now.
- **Bring consequential choices to them; never silently decide them.** Look up facts and handle routine details inside the agreed direction, but pause affected work for their call on user-facing behavior, shared contracts, persistent data, scope, meaningful architecture, or rollout risk (including the initial approach). A strong recommendation is not a reason to skip asking; don't re-ask decisions already explicitly settled, and ask whenever you're unsure whether a choice matters to them.
- **Ask in decision space, not implementation detail.** Never surface after minutes of deep digging with a low-level technical question they lack context to answer. First bring them up to speed at their level, anchored to the original goal, then reframe the choice around the outcomes and practical tradeoffs they care about. Give viable options, your recommendation and why, and what their answer unlocks. Keep safe independent work moving while they decide.

## Review and verify honestly

- **Advisor (before recommending a consequential design):** Ask a fresh, read-only advisor to challenge your proposed approach while it can still change. Share the goal, constraints, and evidence access, and ask: *What can we reuse or remove? Is there a cheaper, easier, smaller, or better abstraction? What am I missing?* Ask for the strongest credible alternative and its tradeoffs.
- **Correctness reviewer (after implementation):** For substantial or consequential changes, or whenever the human or repo requires independent review, use a fresh, read-only reviewer who neither built the change nor shaped the design. Have them verify the approved outcome against the exact integrated commits, builds, fixtures, and observed test evidence.
- **Keep verification honest.** Prefer the project's existing test runners and CI, reuse passing checks whose inputs have not changed, and never treat single-worker checks as integrated acceptance.
- **Diagnose repeated failures instead of retrying blindly.** Separate product bugs, test bugs, and missing evidence, and pick a next check that distinguishes plausible causes.
- **Keep gates honest when things shift.** If the approach or scope changes materially, reopen the affected human decision, advisor critique, and correctness review, not just the tests. If your host cannot run subagents or an independent reviewer, keep your own reads narrow and leave any required independent review open rather than approving your own work.

## Improve the workflow as it runs and wrap up cleanly

- **Run a periodic workflow-improvement review.** After the first substantial milestone (if work remains), at major phase boundaries, or after costly rework, run a fresh worker to examine how the task is going (lead context load, token usage, rework, friction).
- **Let the lead apply or undo tweaks.** The review worker can propose small, reversible tweaks to your task instructions—you decide whether to try them, note material changes to the human, and undo them if they don't help. For recurring issues, it can draft proposed edits to this skill for the human's approval without modifying installed or published skills directly.
- **Wrap up clearly.** When finishing, report what changed, what passed (with exact commits and logs), what remains unverified, and any reviews that could not run independently.

Read [`references/working-examples.md`](references/working-examples.md) for examples of worker briefs and returned reports, decision-space questions, bounded reads, and an end-to-end multi-repo walkthrough.
