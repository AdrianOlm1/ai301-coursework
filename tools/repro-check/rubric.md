# Rubric: is this reproduction package ready to post?

<!--
Filled for Unit 2. Seven required checks, one preferred. Each check
reads the thing itself (the artifact against the issue, the claim
against the issue, the comments against the repo's stated policy),
never the write-up's shape. Worksheet lessons folded in from the
activity: a long, confident, well-formatted report can still be the
wrong target (calib-03), and an exact-command repro with no
environment record still fails when the issue says the environment
changes the failure (calib-04).
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | The repro report's environment record, read against the versions/platform the issue targets and the factors the issue or thread says matter (repo-facts bug-report template asks, e.g. driver, shell, build profile, branch). | Pass if the report names the platform/OS AND the version of the software under test, AND for every factor the issue says changes the behavior, either matches the issue's target or explicitly states the difference. Fail if there is no environment record, or if the tested version/platform differs from what the issue targets (e.g. an old major version against an issue confirmed on latest/main) and the report never says so. A stated difference passes; a silent one fails. | required |
| steps-rerunnable | The repro report's steps, read from starting state to the trigger. | Pass if a stranger with only public resources could run the same trigger: the commands/inputs are shown, or the report points at a runnable script/snippet that is quoted in the issue ("ran the issue's script verbatim" is fine). Fail if there are no steps, if the steps depend on private code, private config, or anything the reader cannot obtain, or if a parameter the issue names as necessary (driver, flag, platform setting) is omitted. | required |
| trigger-matches | The repro report's commands/inputs, read side by side with the trigger the issue (and any maintainer note in the thread) describes. | Pass if the report runs the issue's own trigger: the same syntax, flags, input shape and code path (a faithful re-creation of the same input is fine; a control run that deliberately varies one thing is fine as long as the main run uses the real trigger). Fail if the main run substitutes a different syntax, operator, expression or input (e.g. a prefix range for an offset-from-end range, an unbound variable, a colon for an `=`), because it then exercises a different code path. | required |
| behavior-shown | The report's artifacts (output excerpts, logs, tracebacks, exit codes, measurements, described screenshots) read against the specific symptom the issue describes. | Pass if an artifact is shown AND it displays the issue's specific symptom (same error class/message family, same crash-vs-graceful distinction, same wrong output), OR, for a cannot-reproduce, an artifact shows what actually happened when the real trigger ran. Fail if there are no artifacts, if the artifacts only show setup (version banner, session list, "it launches"), or if they show an adjacent behavior (a graceful validation error presented as a panic, garbled output with the process still alive presented as a crash, a compile error presented as a runtime error). | required |
| outcome-honest | The report's and claim's stated result and any certainty words ("confirmed", "verified", "100%", "root cause", "also on release X"), read against what the artifacts actually show. | Pass if every claim of result, cause, or scope is backed by a shown artifact, and a cannot-reproduce says what was tried and what differed from the report. Fail if the package asserts more than it shows: a root cause "verified" with no artifact, "guaranteed reproducible" with none, repetition count or "two machines" offered in place of the right artifact, or a generalization (to another release/platform) that its own artifact contradicts. An honest, evidenced cannot-reproduce passes. | required |
| claim-specific | The candidate claim comment, read against the issue title/body. | Pass if the claim names something specific to THIS issue (the component, symptom, trigger, or a pointer from the thread) AND states a concrete next step the author will take, AND promises only investigation or reporting. Fail if it is interchangeable boilerplate that could sit on any issue, a bare "+1"/me-too, a request to be assigned or to reserve the issue with no stated intent, or promises a fix, a date, or a guarantee ("fixed within 2 days guaranteed"). | required |
| ai-disclosure | The repo-facts contribution policy (AI policy) read against the claim comment and repro report text. Treat every candidate package as AI-assisted work. | Pass if the repo states no AI policy, a permissive policy, or a policy whose disclosure ask covers only pull requests (not issue comments); or if the policy requires disclosure for comments/issues/all AI usage AND the comments disclose it (naming the tool and the extent). A policy that asks comments to be in the contributor's own words passes unless the comment is plainly generic boilerplate. Fail only if the stated policy requires disclosing AI use in comments or in any form and neither comment discloses, however strong the reproduction is. | required |
| template-asks | The repo-facts bug-report template asks, read against the repro report. | Pass if the report supplies the information the template asks for that is relevant to this bug (version, OS, steps, expected vs actual). | preferred |

## Verdict rule

Accept if every `required` check passes; otherwise reject. The
`preferred` check (template-asks) is reported but never changes the
verdict. `unclear` on a required check counts as `fail`, with one
exception: in live mode on a claim-only draft, checks that need the
repro report (env-recorded, steps-rerunnable, trigger-matches,
behavior-shown, template-asks) are graded `unclear` with evidence
`not yet applicable: claim-only draft` and are left out of the
verdict. On a claim-only draft, outcome-honest grades the claim
comment alone: a claim that asserts a result it has not yet produced
fails; a claim that promises investigation passes.
