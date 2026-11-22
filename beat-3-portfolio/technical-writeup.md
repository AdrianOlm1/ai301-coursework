# Making a login check fail safely: my first pull request, and the two things my own review tools caught

*AI use: I drafted this write-up with Claude (Anthropic's AI assistant) from my own project record, and I reviewed it before submitting. Claude also helped me run the commands, write the fix and tests, and draft the pull request description, as that description says. The tests, outputs and tool results quoted below are real and come from this term's work.*

## Overview

Path Review is the Python web app my course uses for practice contributions. In it, a function called `verify_password` crashed instead of returning "no match" whenever a stored password record was damaged, so a user with a bad record got a server error (a 500) at login instead of the normal "wrong password" answer (a 401). I claimed the issue (#72), reproduced the bug, posted a plan, wrote a fix and its tests, and opened pull request #223: https://github.com/codepath/pathreview-ai301-fa26-s3/pull/223. I also used three review tools I built earlier in the term (their checks are rubrics I wrote) to grade my plan and my pull request before I posted them. As of November 22, the pull request is open, GitHub says it can be merged, and all five of its automated checks pass. No maintainer has reviewed it yet.

## What happened

Dates below are GitHub's UTC timestamps.

**September 28: claim and reproduction.** I claimed the issue and reproduced the bug on unmodified code. The shipped test for it was hidden by a marker that expects it to fail, so I ran it with that marker ignored, and I used a small script to see what the login route returns. A valid password record with a wrong password gave the normal 401. A damaged record gave the crash:

```
valid bcrypt hash: 401 {'detail': 'Invalid email or password'}
malformed hash: 500 {'detail': 'Login failed'}
```

That control run mattered: it showed the problem was the damaged record, not password checking in general.

**October 7: my plan, and the first change I made.** My plan was to catch the library's error inside `verify_password` and return `False`. Before I posted it, my plan-checking tool flagged something I had only listed as an unknown:

> Your unknown has a concrete answer in the thread. cmkhoa's plan (comment 6029915163) catches `ValueError` but re-raises `PasswordSizeError`. In passlib that's a `ValueError` subclass raised for an over-long *password*, not a bad hash.

I tested it instead of guessing. With my fix, a 5,000-character password now returns `False` where the original code raised an error. I moved that from "unknown" into the plan as a stated side effect, and I said so in my plan comment, because it is a behavior change beyond damaged records.

**The fix and its tests.** The core of the change is four lines in `core/security.py` (plus a comment and a docstring update), and some tests:

```diff
-    return bool(pwd_context.verify(plain_password, hashed_password))
+    try:
+        return bool(pwd_context.verify(plain_password, hashed_password))
+    except ValueError:
+        return False
```

I removed the marker from the existing test and added a new test covering three more kinds of damaged record. Run against the original code, the four relevant tests failed. With the fix they pass:

```
before (original code): 4 failed, 24 deselected, 1 warning
after  (my change):     4 passed, 24 deselected, 1 warning
login, malformed hash:  500 'Login failed'  ->  401 'Invalid email or password'
whole unit suite:       379 passed, 52 xfailed
```

I made one mistake here: my first "before" run came out passing, because the fix was already committed and the step I used to restore the old code had nothing to restore. I noticed because a "before" run should fail, and redid it by checking out the original file.

**October 14: the second change I made.** Before I opened the pull request, my pull-request checking tool rejected my draft on a single point:

> CONTRIBUTING.md: 'Every commit message must follow this format: <type>(<scope>): <description>'; sole commit is 'Unit 3: plan-check skill, eval run, plan, and plan-and-implement'.

My commit message was labeled for my coursework, not for the fix. I amended it to `fix(api): return False from verify_password for malformed stored hashes`, ran the tool again, and it said the pull request was ready. I made a second slip when I opened it: I pasted the title but not the description, so the pull request briefly showed the blank template. I checked what GitHub had recorded, saw it, and replaced it with the real description.

**What landed.** Once the pull request was open, GitHub ran the project's five automated checks (lint, typecheck, unit tests, integration tests, frontend). All five passed, and GitHub reports the pull request as mergeable. That is the result so far. It is not a merge, and no one has commented or reviewed.

## Limitations and what's next

The pull request is unreviewed, and it is in a crowded spot: by my own tool's count on November 22, about 29 open pull requests target this same issue, and when I re-graded the issue with my issue-picking tool it rejected it as already claimed. I kept it because I had already done the claim, reproduction, plan and fix, but I would weigh that signal sooner next time.

My testing also has limits. I only exercised the login route, and my fix catches every `ValueError` in that call, so a `ValueError` from some cause I have not seen would also become `False`. I said so in my plan comment, but I have not ruled it out.

What I would do next follows from the commit-message miss. My review tool caught it only through a broader check that covers the project's stated rules, so it was easy to miss. I would add the project's commit rule as its own explicit check, and I would check what GitHub recorded right after opening any pull request. For this one, the next step is simple: watch for a review and answer it.
