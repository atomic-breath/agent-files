---
name: coordinate-initiative
description: Lead substantial work across repositories, workers, or sessions. Use when explicitly asked to coordinate an initiative through decisions, implementation, and verified acceptance.
disable-model-invocation: true
---

# Coordinate an initiative

Delegate to protect the lead's working context. Keep the agreed outcome, open decisions, dependencies, ownership, and acceptance evidence in view. Let workers absorb the detail. Keep the human able to pick up the thread and steer.

## Set up the work

For a small, clear task without a required independent review, do it directly and verify the result yourself. Skip the coordination setup.

For larger work, reuse the initiative's issue, plan, or chat. The lead owns integration and final acceptance. Agree on what success looks like, who owns each repo or shared resource, and which decisions need approval. Check the riskiest assumption cheaply before assigning work that depends on it.

## Delegate and keep the lead's reads focused

- Give a read-only investigator a question to answer and corroborate. Give an implementer an outcome, named write scope, and exclusive ownership of any shared resource it needs. Include relevant repo rules and local skills, exact inputs, success and stop conditions, and when to escalate. Give workers the available tools to search code and history, read docs, research the public web, and run authorized diagnostics. Keep write scope, shared resources, and external data destinations explicit. Use public concepts in web searches. Keep private code, identifiers, and logs within approved destinations.
- Ask for a compact return with the outcome, checks, evidence pointers, uncertainty, and next step or decision. Keep broad reads and searches with workers. Before pulling files, diffs, logs, search results, or worker chats into the lead's context, name the decision or check the read could change. Start with a cited excerpt, a follow-up, or a fresh verifier. Bound output before running a command or search. For example, filter a saved log around the failure or have a worker monitor a noisy run. Expand only when the smaller read leaves the question open. Avoid repeating an investigation just to regain context. Keep implementation with a worker if taking it over would require reconstructing the investigation.
- Workers surface completion, blockers, and changes that affect other assignments. For live status, ask for the last verified fact, next step, and what they need when it could change your next move. On hosts without messaging, use the latest checkpoint or agree on the next return point. Interrupt to redirect or stop.

## Use the right worker for the job

- Keep role briefs, local skills, and tools stable unless the work, governing rules, or a reversible workflow trial changes them. Start fresh on a new bounded assignment. Resume the same investigator or implementer when continuing a reproduction, a hard-won diagnosis, or a fix-and-retest loop saves real rediscovery. Decide at natural checkpoints. An investigator can carry a diagnosis into a fix after applicable approvals, with a new brief and explicit write and resource ownership if the host supports that change. Otherwise give a fresh implementer the checkpoint. Reconcile outstanding edits and release or transfer shared resources before any replacement starts.
- Before recommending a consequential design, ask a fresh read-only advisor: “Given this goal and approach, what could we reuse or remove? Is there a cheaper, simpler, smaller, or better-abstracted way? What am I missing?” Ask for the strongest credible alternative, its evidence, and its tradeoff. Use the same advisor for follow-ups on that choice. Bring in a separate planner when unresolved ambiguity warrants it.
- For substantial or consequential work or a required review, give a fresh read-only correctness reviewer the approved outcome, exact integrated artifact, and test evidence. Choose someone who neither implemented the change nor shaped the selected design. They can recheck a narrow correction they requested. Reopen affected gates for material changes. Read-only roles can verify in approved disposable areas. If subagents are unavailable, use staged reads and a fresh session or teammate when possible. Keep any required independent review open until it happens.

## Keep the human oriented and verify acceptance

- At kickoff, meaningful changes, and the next available checkpoint during a long quiet stretch, link the update to the agreed goal. Say what's done, what's active, and what's next. Make it clear whether work is continuing, blocked, or waiting for a decision. Assume the human missed the worker activity.
- Look up facts and handle routine mechanics yourself. Pause affected work for the human's decision before committing to an unresolved choice about behavior, shared contracts, persistent data, scope, architecture, or rollout risk. This includes the initial design. Ask if unsure whether the choice matters. Explain why it surfaced, the practical consequences of the viable choices, your recommendation, and what happens next. Keep separate, authorized work moving.
- Tie acceptance to the repo revisions and local changes tested together, the exact build, configuration and fixture, and what happened. A green per-repo check does not establish integrated acceptance. On pause or replacement, leave a compact checkpoint with decisions, state, ownership, evidence pointers, and the next check. Verify the current state on resume. After repeated failures, distinguish product defects, test defects, and missing evidence before rerunning. Reuse checks whose inputs still match.

## Improve how the work runs

At the first substantial milestone if work remains, later phase boundaries with evidence to assess, and after costly rework repeats, use a fresh performance worker. Give it the prior experiment, baseline, and evidence pointers. Let it inspect authorized task traces or use the checkpoint and artifacts when traces are unavailable. Track lead input, total model use, time, and rework separately. Label estimates and count the review's own cost.

When the evidence supports a change, have the worker draft one reversible task-local adjustment outside the checkout, with a baseline, success criterion, and rollback trigger. The lead decides whether to try it on future assignments, tells the human about material changes, and checks it at the next applicable checkpoint. Revert a failed or regressive trial. Keep scope and approval gates intact.

For recurring problems, the worker can draft a versioned skill patch in isolation. Run a frozen baseline and a separate regression case only when the human has authorized evaluation scope and budget and an authorized runner is available. If authorized but the worker can't run it, hand it to an available outer evaluator under the same authority. Otherwise return an untested proposal, test cases, and the missing prerequisite. Leave installed and published files untouched. Promote durable changes through normal review after repeated evidence supports them.

Use [briefs, checkpoints, and a cross-repo example](references/working-examples.md) when setting up a handoff or coordinating a change across repos.
