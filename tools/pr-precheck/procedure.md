# Procedure: how this tool grades a PR package

## Read order

1. Read the plan first: the plan-context block in an eval bundle, or `plan.md` including `## Deviations` in live mode. Write down three lists: the files the plan names, the things it says it will NOT change, and the test plan's repro steps (every failure mode it names). The diff and the description are compared against these lists, so they must exist before either is opened.
2. Read the repro evidence that the plan built on, if present. Note the command or input that triggers the bug. The test evidence is graded against it.
3. Read the repo-facts block (live mode: `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and any AI policy file). Write down each ask the repo states for a PR, and whether it requires AI-use disclosure.
4. Read the diff and commit list. List every changed file, and for each hunk note what it does in one line.
5. Read the description and title last. List every factual claim it makes about the change.
6. Read the test evidence last.

## Evidence gathering

1. diff-matches-plan: put the changed-file list beside the plan's named files. Mark each changed file as named, covered by a deviation note, or unplanned. Then go through the hunks inside named files and mark any hunk that does something the plan did not ask for (a rename, a new option, a rewrite). Then check each thing the plan promised for presence in the diff.
2. description-matches-diff: for each claim listed in Read order step 5, find the diff line or file that makes it true. Record "no proof in diff" for any claim with none. A claim that something was not added is true when nothing in the diff adds it.
3. test-evidence-decisive: put the test plan's repro steps beside the commands in the test evidence. Record, for each failure mode the plan names, whether a command and its output, or a before and after outcome, is shown on the changed path. Record whether the added test would fail without the fix.
4. diff-reviewable: scan only the added lines (`+`) of the diff and list each debug print, commented-out code line, added function or import that nothing uses, re-indent or re-print hunk, and edit unrelated to the fix. Read the commit messages as supporting evidence only.
5. template-asks-met: for each ask written down in Read order step 3, find the description section or diff line that meets it. Record "none found" if there is none.
6. ai-disclosure: find the sentence in the description that says AI was used and how. Record "none found" if there is none. Record the policy's wording on disclosure.
7. repo-checks-named: list the check commands the template names, and find each in the test evidence with its result.

## Check execution

1. Run the checks in the order they appear in the rubric table.
2. Grade each check using only the lists and records above, and quote the fact that decided it.
3. A grade is `pass` when the pass condition is met, `fail` when it is not, and `unclear` only when the evidence the check needs is genuinely absent. "The repo states no ask" and "the policy states no disclosure requirement" are `pass`, not `unclear`.
4. A sentence saying something passed is not evidence. For test-evidence-decisive, a note after `#` saying what a command printed is not output.
5. Do not let a polished description, a ticked checklist, or a long commit history change a grade. Grade the diff and the output.

## Verdict assembly

1. List the grades of the `required` checks in rubric order. If every one is `pass`, the verdict is `accept`. If any is `fail` or `unclear`, the verdict is `reject`, except where the rubric's verdict rule says `unclear` is a `pass`.
2. `preferred` grades are reported in the output and never change the verdict.
3. In the output, give each check one line quoting the deciding fact. When more than one required check failed, state the first failing required check in rubric order first.
4. Note any step above that did not tell you what to do, instead of inventing one.
