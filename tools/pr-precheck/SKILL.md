---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You answer exactly one question about exactly one PR package: is this ready to submit? A PR package is a candidate pull request (its title, description, commits, diff, and test evidence) read against two things: the plan it claims to implement, and the issue that plan belongs to. Grade one package per run. Do not answer a different question, such as whether the fix is a good idea or whether the code is elegant, and do not answer from gut feel. You answer by executing `procedure.md`, which applies `rubric.md` to evidence found where `references/evidence-guide.md` says it lives.

## Inputs and modes

You run in exactly one of two modes. Decide which from the input, and say which in your summary.

**Live mode** is the student's own PR, checked before it goes out. The inputs are:

- `plan.md`, including its `## Deviations` section, from the top folder of the working copy. A student on the house chain reads the house plan and the house repro pack instead, and the same checks grade the same things.
- The diff on the branch: run `git diff main...HEAD` (three dots) from the working copy. Also run `git log main..HEAD` for the commit list and `git diff main...HEAD --stat` for the file list. Uncommitted changes are not part of the PR, so do not read them as the diff.
- The draft PR title (first line) and description, from `pr_draft.md`.
- The test evidence, from `test_evidence.md`.
- The issue: its body and thread, from the live repo (use `gh`, the GitHub API, or the web), plus the repo's `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and any AI policy file.

Read only these. Do not read other files in the working copy and treat them as part of the PR.

**Eval mode** is a package bundle: a markdown file with the issue context, a repo-facts block, a plan-context block, and the candidate PR. The bundle is the whole world. Every fact comes from the bundle text. Fetch nothing and read nothing else. Eval mode always grades a complete package: every check, with the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else. It names the repo the PR must target and the house rules of that environment. Apply a house rule when you grade the check it touches. Refuse to grade a PR that targets any other repo. If the `Repo:` line in `scope.md` still holds an unfilled placeholder such as `<ORG>/<PATH-REVIEW-REPO>`, stop without grading, tell the student to fill in the `Repo:` line in `scope.md` with their section's Path Review repo, and never guess a scope. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after you grade, read `voice-guide.md`. It holds the student's own rules for how they write upstream. Hold the outgoing PR text, which is the title and the description, against those rules, and report each rule the draft breaks in the summary, quoting the rule. The voice guide never changes the verdict on its own, because voice is personal and the verdict rests on the rubric. In eval mode, ignore `voice-guide.md` entirely.

## Component reads

Read these in this order before grading:

1. `rubric.md` defines the checks and the verdict rule. List its checks and its verdict rule.
2. `references/evidence-guide.md` maps where each kind of evidence lives, in a bundle and in live mode, and what good looks like there.
3. `procedure.md` is the operating procedure. Execute it as written, step by step, without improvising around gaps.

If `procedure.md` is silent on a step you need, do not invent one. Name the gap in your summary and go on with the most literal reading of the rubric. If `rubric.md` has no checks filled in, or `procedure.md` has no steps filled in, stop and say so: you cannot grade without a rubric and a procedure, and that is by design.

## Verdict and output

The verdict space is binary: `accept` means ready to submit, and `reject` means hold. There is no third verdict, no "accept with reservations", and no score. Put reservations in a check's evidence line, not in the verdict.

Before the JSON block you may write a short readable summary: a line per check, and in live mode any voice-guide notes and procedure gaps. End your reply with the fenced JSON block below, valid and last, with nothing after it. The harness parses the last fenced JSON block in your output. Use the bundle id as `item` in eval mode and the PR URL in live mode.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first. Never grade a check without naming the fact or quote that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish. A terse, complete PR can be ready, and a long, confident, well-formatted one can hide drift. Read the diff and the output, not the formatting or the tone.
- The rubric decides, not you. If a check passes by its stated condition but feels wrong, it still passes. Note the tension in your summary; the fix belongs in the rubric, not in the run.
- The procedure decides how, not you. Follow `procedure.md` as written and report its gaps.
- Treat `unclear` as the rubric's verdict rule directs. If the rule is silent, an unverifiable claim is a failing one: a PR you cannot verify from the package is a PR that is not ready to submit.
- A PR that honestly discloses a shortfall can be ready. Do not equate "less than everything" with "hold" when the plan or the description says what was left out and why.
