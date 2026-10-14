# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Where it lives: in an eval bundle, the plan is the "Plan context" block: its scope sentence, its "Files:" line, its "Not in scope" line, and its test plan. The change is the "Candidate PR" section's "Diff" (changed files and hunks) and "Commits". Any deviation note is in the plan context or restated in the "Description". In live mode, the plan is `plan.md` with its `## Deviations` section, the change is `git diff main...HEAD` run from the working copy (three dots), and the claims are in `pr_draft.md`.

What good looks like: every changed file falls inside the plan's named files, or a deviation note covers it, and everything the plan promised is in the diff or disclosed as deferred. Silent drift runs two ways: more than the plan (an unplanned file, option, flag, or rewrite) or less than the plan with a description that claims otherwise. A description that says "no changes beyond the plan" over a diff with an unplanned file is drift, not a wording problem.

## Test evidence (harness category: not-tested)

Where it lives: in an eval bundle, the "Test evidence" part of the "Candidate PR", read against the test plan in the "Plan context" and the repro evidence's steps. In live mode, `test_evidence.md` next to `plan.md`, and the "Testing" section of `pr_draft.md`, read against your unit 2 repro steps and the plan's test plan.

What good looks like: the plan's repro is re-run on the changed code with a command and its printed output, or a stated before and after outcome, on the path the repro names. If the plan names two failure modes, both are shown or the omission is stated. "Tests pass", "works locally", or output from a control path is not evidence of the fix. A failing check that is reported honestly with the reason still counts as evidence.

## Diff quality (harness category: unreviewable)

Where it lives: the added lines in the "Diff" and the "Commits" list. In live mode, the added lines of `git diff main...HEAD` and `git log main..HEAD`.

What good looks like: the fix and its test, nothing else. Debris is a debug print or log left behind, a commented-out code line, a function or import added that nothing uses, a re-indent or re-print hunk, or an unrelated edit. An explanatory comment about why code does something is not debris. Commit messages like "wip" or "misc cleanups while debugging" are a supporting tell, but the debris in the added lines is what decides it.

## Standards and comms (harness category: standards-wall)

Where it lives: in an eval bundle, the repo-facts block's "pull requests:" line (the template's asks) and its "contribution policy" line (including any AI-use policy), read against the "Description" and the "Diff". In live mode, `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, any AI policy file, and the issue thread, read against `pr_draft.md` and the branch diff.

What good looks like: every section the template asks for has real content, the issue is linked as the template asks, ticked items are true of the diff, a required artifact (changelog or whatsnew entry, tests) is in the diff, and a required AI-use disclosure is in the description in plain words saying that AI was used and how. A repo that states no ask and no disclosure requirement has nothing to meet. (Whether the description's claims match the diff is plan fidelity, above.)
