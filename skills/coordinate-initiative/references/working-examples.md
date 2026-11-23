# Working examples

These shapes are starting points. Include only what changes the next decision or check.

## Give a worker a bounded assignment

> Find why the client can still retry after the API reports success. Read client retry handling, the API response contract, and the queue consumer at the listed revisions. Use the repo runbooks and public docs if useful. This is read-only. Return the likely cause, how you corroborated it, the decisive code or test locations, and one check that could disprove it. Flag an interface choice before anyone implements it.

For implementation, add owned paths and shared test-resource ownership. For an advisor, supply the proposed approach and ask what could be reused or removed. For acceptance, give an independent reviewer the approved criteria, exact integrated build, observed results, and open issues.

## Hand off what matters

> Retry ownership remains undecided. The API and client checks passed independently. The failure reproduced on client `a17`, API `b42`, and background job `c08`, with the retry fixture enabled. See the integration run's second delivery and the consumer's duplicate-handling check. The test environment is released. Next, verify the proposed ownership choice on the same fixture before assigning dependent work.

If the worker already has a live reproduction, continue it when the scope and authority allow. If it must hand off, supply this checkpoint and verify the current revisions and resource ownership with the replacement.

## Coordinate across three repos

A client displays an export request, an API accepts it, and a background job runs it. The goal is to retry a failed request safely. An investigator traces the three boundaries and finds that two services can retry the same request. The lead asks an advisor whether the existing job identifier can prevent duplicates. Choosing which service owns that record changes a shared contract, so the lead explains both choices to the human before dependent implementation.

Once approved, give the owning workers their interface versions and give one worker exclusive use of the shared fixture for the check. A green run that used yesterday's API cannot establish the result. Review the exact versions together and observe a retry reach the same export without launching a duplicate. If an independent reviewer is unavailable, record the evidence and leave acceptance open.

## Start from any harness

> Use the Coordinate Initiative skill for this work. Our goal is [outcome] across [repos]. Start from [existing issue or plan]. The acceptance check is [observable result]. I own [product or contract decisions]. Use available investigators and reviewers. Keep me oriented at meaningful checkpoints and bring me consequential choices before affected work depends on them.

Invoke the named skill if the harness supports it. Otherwise give the agent the skill's Markdown, or explicitly point it at the readable file.
