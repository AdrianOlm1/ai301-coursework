# Procedure: how this skill grades a plan package

## Read order

1. Read the issue context first: the title and body, then the "Thread highlights". Note in one line each: what the issue asks for, and every explicit maintainer or owner direction in the thread (a settled approach, a located culprit, a rejected approach, an open PR to engage, a request to test).
2. Read the repo-facts block next. Note the contribution policy, especially any AI-use disclosure requirement, and any template or comment asks.
3. Read the repro-evidence block BEFORE the plan. List each numbered step, the artifact or output, and every control run, and write down what each one rules in or out. This is the ground truth for the cause. The thread's guesses are not.
4. Only then read the candidate plan (diagnosis, scope, approach, test plan, risks), then the candidate plan comment.

## Evidence gathering

1. diagnosis-matches-repro: copy the plan's one-sentence cause. Next to it, list each repro step and each control run from step 3 above, and mark each as explained or contradicted by that cause.
2. scope-bounded: copy the plan's in-scope list, its not-in-scope line, and the files or areas it names. List anything in the approach that the issue body does not ask for.
3. executable: copy the files or areas and the approach steps. Note any step that defers a decision, or names no location.
4. test-observable: copy the test plan. Match it to the repro steps and note the expected result it names.
5. thread-aware: for each maintainer direction noted in Read order step 1, find the plan comment sentence that follows or answers it. Record "none found" if there is no such sentence.
6. policy-compliance: copy the policy line's disclosure or comment requirements. Find the plan comment sentence that satisfies each. Record "none found" if there is none.
7. honest-unknowns: copy the plan's stated risks and unknowns.

## Check execution

1. Run the checks in the order they appear in the rubric table.
2. Grade each check from the evidence recorded above, using only the package text. Quote the fact that decided it.
3. A grade is `pass` when the pass condition is met, `fail` when it is not met, and `unclear` only when the evidence the check needs is genuinely absent from the package. "No maintainer direction in the thread" and "policy states no requirement" are `pass`, not `unclear`.
4. For diagnosis-matches-repro, grade `fail` if even one control run contradicts the stated cause. A confident or long diagnosis does not change this.
5. Do not re-read the whole package for a check. Use the recorded evidence, and go back only to the passage named for that check.

## Verdict assembly

1. List the `required` grades. If every one is `pass`, the verdict is `accept`. If any is `fail` or `unclear`, the verdict is `reject`.
2. `preferred` grades are reported but never change the verdict.
3. In the output, give each check one line quoting the deciding fact. For a `reject`, the `evidence` of the first failing required check names the quoted plan text and the quoted repro or thread text it conflicts with.
4. Note any step above that did not tell you what to do, instead of inventing one.
