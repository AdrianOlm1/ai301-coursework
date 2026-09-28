# Evidence guide: where proof lives in a reproduction package

<!--
The rubric's map. For each proof family: where to look in an eval
bundle, where to look live, and what good looks like as an observable
condition.
-->

## Environment

- Where it lives (eval bundle): the repro report's "Environment" line
  or block; compare it with the versions/platform in the Issue section
  (reporter's version, OS, build type, branch) and with the repo-facts
  "bug reports" line (what the template asks for). The thread
  highlights may add factors that matter (e.g. "works in Debug, crashes
  in Release", "confirmed on main").
- Where it lives (live): the student's draft repro comment; the issue
  body and thread on GitHub for the target; the repo's own setup docs
  (`docs/SETUP.md`, `pyproject.toml`, CI config) for the supported
  versions.
- What good looks like: OS/platform plus the version of the software
  under test (and of the dependency the bug lives in, when there is
  one), plus the code state (release number or commit). The version
  matches what the issue targets, or the report says in words how it
  differs ("filed against 13.0.0; unchanged on 15.2.0"). Any factor the
  issue says changes the failure (driver, shell, build profile) is
  named. No environment record at all is a fail even when the output
  looks right.

## Steps

- Where it lives (eval bundle): the repro report's "Steps" section and
  its command blocks; for "ran the issue's script", the script quoted
  in the Issue section.
- Where it lives (live): the draft repro comment's commands, from clone
  or install through the trigger.
- What good looks like: someone with only public resources could type
  the commands in order and reach the trigger: the exact commands or
  input files are shown or quoted from the issue, and anything the
  issue calls necessary (flags, driver, settings) is present. Steps
  that say "our private monorepo", "our internal config", or "set up
  the project" with nothing else cannot be re-run.

## Behavior shown

- Where it lives (eval bundle): the fenced output blocks, logs,
  tracebacks, exit codes, and described screenshots in the repro
  report, plus its Expected/Actual lines; read them against the exact
  symptom in the Issue section (error message, exit code, crash vs
  graceful error, wrong output shape).
- Where it lives (live): the output pasted in the draft repro comment,
  compared with the issue's stated symptom.
- What good looks like: the artifact itself shows the issue's symptom,
  not the author's sentence about it. Match the class of failure: a
  panic/abort (exit 101) is not a graceful validation error (exit 1); a
  runtime "Invalid path expression" is not a compile error about an
  unbound variable; garbled output with the terminal still open is not
  a crash; a version banner or "the session starts" shows setup, not
  the bug. First confirm the input actually used is the issue's input
  (same syntax, operator, expression), because a modified input makes
  every artifact after it adjacent. A control run (the same command
  with the trigger removed, showing correct behavior) is strong
  supporting evidence.

## Honesty

- Where it lives (eval bundle): the summary sentences, "Result:" lines,
  "Actual:"/"Conclusion" text, and certainty words in both the claim
  comment and the repro report, set next to the artifacts they point
  at.
- Where it lives (live): the same sentences in the student's drafts.
- What good looks like: every claim is as big as its artifact and no
  bigger. "Verified the root cause" needs a shown artifact that
  isolates the cause; "also broken on the Store release" needs an
  artifact from that release that shows the issue's symptom; repeat
  counts and "two machines" are not substitutes for the right artifact.
  An honest cannot-reproduce states the result up front, shows what
  ran, and names what differed from the reporter's environment; that
  is a pass, not a failure.

## Comms

- Where it lives (eval bundle): the candidate claim comment against the
  Issue section (does it name this issue's component/symptom and a
  next step?); both comments against the repo-facts "contribution
  policy" line (AI policy: does it require disclosure, and for what:
  PRs only, comments, or "all AI usage in any form"?).
- Where it lives (live): the draft claim comment against the issue
  body; the repo's CONTRIBUTING.md, AI_POLICY.md/AGENTS.md, and issue
  templates on GitHub; `scope.md` for the Path Review house rules
  (a classmate's claim never blocks, no piggybacking).
- What good looks like: the claim could only have been written for
  this issue (names the function, symptom, or thread pointer), says
  what the author will do next, and promises investigation or a report,
  never a fix, a date, or a guarantee. When the stated policy requires
  AI disclosure for comments or for any AI use, a disclosure naming the
  tool and the extent appears in the comments; eval packages are
  treated as AI-assisted, so a missing disclosure under such a policy
  fails however good the repro is. When the policy is silent, only
  covers PRs, or is permissive, no disclosure is required.
