# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/223

**Branch**

fix/72-malformed-password-hash

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke test, `--limit 3`: `agreement: 3/3 scored items`
2. Full run, saved with `--save-run`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`, with `categories: clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. This is the run in `eval-run.txt`, and my rubric did not change after it.

After that I re-graded `--only pkg-13,pkg-20` (`agreement: 2/2 scored items`) only to read the per-check notes for this write-up. It changed no file and is not the run in `eval-run.txt`.

**Package analysis**

`pkg-20` (ghostty, mode-2031 color-scheme fix). My rubric: `reject`. Gold label: `reject`, noted as "excellent bounded fix implementing the maintainer-authored diagnosis with decisive mode-2031 evidence, but the description contains no AI-use disclosure and ghostty's stated policy requires disclosing all AI usage; every package here is treated as AI-assisted work".

The deciding check was `ai-disclosure`. The package's repo-facts line says: "All AI usage in any form must be disclosed, stating the tool used and the extent of the assistance", and the description has no sentence about AI use, so the check failed and a required failure holds the package. Everything about the code was fine: `diff-matches-plan` and `diff-reviewable` both passed (only `src/config/Config.zig` changed, and the added lines are the fix, an explanatory comment and two tests).

My rubric also failed two other checks on this package that gold does not name, so I checked them against the package text. `description-matches-diff` failed because the description says "a second case covers the single-theme scheme flip", but the second added test, `changeConditionalState still ignores unrelated keys`, passes `.fullscreen` and expects `null`; the flip is covered by the updated first test. That one is a real mismatch. `test-evidence-decisive` failed because the before and after appear only as `#` comments on the identical command (`# before: ESC[?997;2n (light)`), with no printed output, and my procedure says "A note after `#` saying what a command printed is not output". Gold calls that same evidence "decisive", so on that check my rubric is stricter than the staff label. It did not change the verdict here, since `ai-disclosure` alone rejects the package.

**Check rationale**

`ai-disclosure`, exactly as it reads in `tools/pr-precheck/rubric.md`:

> **Evidence:** The repo-facts block's contribution policy line, read against the description (live mode: the course's own ask, in the template's Notes for Reviewers)
>
> **Pass condition:** If the policy requires AI-use disclosure, or this is live mode where the course asks for it, the description states in plain words that AI was used and how. Fail if a required disclosure is absent. Pass if the policy states no disclosure requirement and the package is an eval bundle.
>
> **Weight:** required

It reads this way because of what the clear accepts look like. Before writing it I checked the seven scored `accept` packages. Six of them (`pkg-02`, `pkg-05`, `pkg-08`, `pkg-13`, `pkg-16` and `pkg-19`) come from repos whose policy line says "no stated AI policy" and carry no AI disclosure at all; only `pkg-11`, whose repo states an AI policy, discloses. A check that always demanded disclosure would have turned those accepts into rejects. So I made the requirement conditional on what the repo states, which is what `pkg-01`'s "no stated AI policy" and `pkg-20`'s "strict AI rules" policy lines separate. The live-mode clause exists because the course asks for the disclosure in Notes for Reviewers even though Path Review's PR template has no section for it. I rejected making the check preferred, because then `pkg-20` would have been accepted.

**Trade-offs**

The conditional wording gives up one case on purpose: in an eval bundle whose policy line states no disclosure requirement, a description with no AI statement passes this check. That is a PR I would want a human to flag in real life, but it matches gold: the clear accepts `pkg-02`, `pkg-05`, `pkg-08`, `pkg-13`, `pkg-16` and `pkg-19` all have exactly that shape and all are `accept`. The check also trusts the repo-facts line, so a policy hidden in a linked document the bundle does not quote would be missed.

Nothing changed elsewhere, and here is how I know: I did not revise the rubric after the first full run, which already agreed on all 20 with every category matched (`standards-wall 2/2`), so there was no loosened check to canary with `--only`. The saved `eval-run.txt` fingerprints the files it graded, and the uploaded files are the same ones.

One tension I found and did not fix, because changing a file after the saved run would break that match: the `test-evidence-decisive` pass condition accepts "a stated before and after outcome", but my procedure says a note after `#` is not printed output. That is why `pkg-20`'s evidence failed that check while gold calls it decisive. It cost no verdict in the 20 packages, but a package whose only fault was `#`-commented evidence would be rejected unfairly.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
