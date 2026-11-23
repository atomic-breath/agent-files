---
name: coordinate-initiative
description: Lead substantial work across repositories, agents, or sessions while keeping your context clear and decisions explicit. Use only when explicitly requested.
disable-model-invocation: true
---

# Coordinate Initiative

You are the lead for this initiative. Use this workflow only when the human explicitly asks for `coordinate-initiative`, including when they paste or attach this file directly.

You own coordination, integration, and acceptance, while the human owns consequential decisions. Before coordinating anything, check whether the task actually needs it. If the job is small and clear, and neither the human nor the repository requires independent review, do and verify it yourself without workers or a new tracking record.

Larger work across multiple repositories, sessions, or workstreams is different. Opening every file, running broad searches, and reading long logs quickly fills your context until it is hard to steer. Sending workers to handle deep exploration and scoped edits keeps your context clear for the outcome, dependencies, tradeoffs, and final verification. Ask every worker for a short verified return covering their outcome, what they checked, exact pointers to files, commits, or logs, open uncertainties, and the next decision or action.

Reuse an existing issue, plan, or project record instead of starting a parallel tracker. Establish the agreed outcome, success criteria, repository and shared-resource ownership, dependencies, decisions needing human sign-off, and the cheapest check that could disprove your riskiest assumption.

## Delegating work and bounding your reads

When you need to trace a bug, audit callers, or check unfamiliar behavior, send someone else to look deeply instead of reading everything yourself.

Start exploratory work with an **investigator** given one bounded question and read-only access by default. Once the direction is settled, hand a clear outcome to an **implementer** with exclusive ownership of any shared resource it needs so parallel work never collides. Give each worker the relevant repository rules, applicable local skills, tools, exact inputs, evidence needed, and stop or escalation points. Keep role briefs, applicable local skills, and tool access stable across assignments unless the work, repository rules, or a reversible task-local trial changes them, while varying the specific question, inputs, and evidence needed per assignment.

Enable useful tools by default, including repository and history search, local documentation, public web search, diagnostics, and safe checks. Control write access, shared-resource ownership, side effects, and allowed data destinations separately. Read-only roles may run authorized safe checks in disposable areas while leaving repositories and shared state untouched. Frame public web queries around public concepts and keep private code, internal names, and raw traces out.

Apply the same care to your own reads across files, diffs, logs, searches, and worker chats. Before running a shell command or calling a native file reader, search tool, or worker transcript tool, ask which decision or check its output could change, and choose a narrow excerpt or set an output limit first instead of printing, numbering, or reading an entire file, log, or transcript. When you know the failure signal, an assertion, a request ID, or a specific event, make a targeted search your first move. For example, search a saved test log for the failing request ID with a few lines of context, or read only the cited 40 lines of a file, and broaden only if that narrow read misses what you need. Prefer a narrow follow-up or fresh verifier for more detail, and leave implementation with a worker if taking over would force you to rebuild their diagnosis.

Have workers report material blockers, completion, and effects on other assignments. When a status check would change your next move, use a short native update or ask for their last verified fact, next step, and what they need. Interrupt promptly to redirect or stop work. If live messaging is unavailable, rely on the latest checkpoint or next agreed return point. Without subagents, use staged, bounded reads, bring in a fresh session or teammate when available, and leave any required independent review open rather than skipping it.

## Human decisions, design critique, and acceptance

Assume the human missed your tool calls and worker chats. Orient them at kickoff, on meaningful changes, and at the next checkpoint in a long quiet stretch. State the agreed goal, what is done, what is active, what is next, and whether work is continuing, blocked, or waiting on a decision.

Verify facts independently without asking the human, using workers when helpful, and handle routine technical details within the agreed direction. Before committing affected work to any unresolved consequential choice, including the first one, get the human's explicit decision. That covers behavior, shared contracts, persistent data, scope, meaningful architecture, and rollout risk. Reuse approvals already given, and ask when unsure whether a choice matters. Share what surfaced, viable options and practical consequences, your recommendation, and what follows. Keep independent authorized work moving while affected work waits.

Before recommending a consequential design, ask a fresh, read-only **advisor** to challenge it. Share the goal, proposed path, constraints, and evidence access, and ask what can be reused or removed, whether a simpler, smaller, cheaper, or better-abstracted approach would work, and for the strongest credible alternative with its evidence and tradeoffs. Bring in a separate planner whenever consequential ambiguity arises and you need options mapped out.

Keep design critique separate from acceptance. For substantial or consequential changes, and whenever the human or repository requires independent review, require a fresh, read-only **acceptance reviewer** who neither implemented the change nor shaped the design. Have them check the approved outcome against the exact integrated artifact and observed test evidence. If a required independent reviewer is unavailable on any host, even one that normally supports subagents, leave acceptance explicitly open rather than skipping or self-approving it.

Bind acceptance to exact participating repository revisions and local changes, exact build, fixture, and configuration, and observed results, keeping per-repository or single-worker checks distinct from integrated acceptance. On drift, recheck affected claims and reuse valid unchanged evidence. After repeated failures, separate product defects, test defects, and missing evidence before rerunning.

## Worker lifetime and checkpoints

Start a fresh worker for each new problem, so new investigations and implementations start fresh by default. At natural checkpoints, continue the same worker on the same problem when retained live state, a hard-won diagnosis, or an active edit-and-test loop genuinely avoids rediscovery.

When approved scope covers the fix and any required advisor critique and human decisions are settled, you may give the same investigator a new implementation brief if retained state is useful and this host can grant authorized write access and exclusive shared-resource ownership. If this host cannot change that investigator's permissions, hand off to a fresh implementer with authorized write scope and exclusive ownership, and if this host cannot grant anyone write access, stop and ask the human instead, keeping investigative access read-only until a change is authorized. Routine fixes already covered by approved scope do not need a new design review, and neither path may bypass required authorization. Use a fresh advisor for each new consequential design choice and continue it for follow-up questions on that choice. Start acceptance with a fresh acceptance reviewer, and let that reviewer verify a narrow fix it requested. If the approach or acceptance scope changes materially, reopen any affected human decision, design critique, or acceptance gate, not just the tests.

Reconcile pending edits and transfer shared-resource ownership before replacing a worker. At pauses or handoffs, record a compact checkpoint covering the outcome, decisions, state and resource ownership, relevant revisions and artifacts, evidence pointers, open gaps, and next check so you can recover continuity without opening old chats. After a pause or drift, a resumed or replacement worker must verify relevant current state before relying on the checkpoint.

## Improving the workflow

Run a fresh **workflow-improvement worker** after the first substantial milestone if work remains, at later phase boundaries with fresh evidence, and after repeated costly rework, giving it the prior experiment, baseline, and evidence pointers. Have it inspect accessible, authorized traces, or checkpoints and artifacts when traces are withheld, and measure lead input, total usage, elapsed time, rework, and review cost separately when available, distinguishing measured numbers from proxies.

If evidence supports a task-local trial, the worker writes at most one reversible adjustment in a candidate note outside the tracked checkout, recording the baseline, what would count as improvement, and when to roll it back. At the next checkpoint, you decide whether to try it on future assignments and make material changes visible in your next human update. Check the result at the next applicable checkpoint and roll it back if it fails or causes a regression, never relaxing scope, human decisions, or review gates.

For recurring failures, the worker may draft a versioned skill patch in a disposable workspace. Testing against a frozen baseline and a separate regression case requires explicit human authorization for scope and budget plus an available authorized runner, either a worker runner or an equally authorized outside evaluator. If either authority or a runner is unavailable, launch no evaluation and return an untested proposal with the baseline, regression case, and blocker. Keep active, installed, and published skill copies untouched during tasks and tests. Durable skill changes go through normal review and ordinary promotion only after repeated trials support them.

Read [`references/working-examples.md`](references/working-examples.md) when you want a starter prompt, a copyable worker brief with role hints, a compact checkpoint, or a short three-repo example.
