# Contribution: `verify_password` raises `UnknownHashError` on malformed stored hashes instead of returning `False`

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Adrian Olmos
**GitHub Username:** AdrianOlm1
**Issue:** https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72
**Pull Request:** https://github.com/codepath/pathreview-ai301-fa26-s3/pull/223

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: the scout with my own queries, and a 5-star floor added to my rubric**

I installed the scout over my unit 1 issue-select skill, wrote four `gh search issues` queries from my fit profile (Rust, JavaScript, Python + SQL, Rust + SQL) and five ranking rules, and ran one scan that graded five issues: four accepted and one rejected. It fell short in that two of the four accepts, hoops#8 and crowsnest#4, were repos with 0 stars created within the past week, which my `repo-in-use` check passed because it only looked at recent pushes, and my "most recently updated first" tie-break is what put them in the top five. I added a 5-star minimum to `repo-in-use` (20 would have rejected the scan's best candidate, sysknife, which has 16 stars) and a tie-break that prefers maintainer-filed issues, then re-graded the two cases directly: hoops#8 is now rejected and sysknife#633 is still accepted, and my two lowest-star unit 1 accepts still agree with gold. I did not re-run the full scan, so the new tie-break and my `pathreview` filter are untested.

**Unit 6: [one line on the tool work]**

[2-4 sentences: the voice-guard, the repo-memory file. What broke or
fell short, and what changed because of it. "Nothing broke" counts when
it is true - say how you know.]

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I did not find this issue on the Issue Selection site or through a wild scan. It comes from the course's own Path Review repo. In unit 1 I ran my issue-select skill on three open Path Review issues (#72, #5 and #20); it accepted all three and ranked #72 first, and I chose it because it was the only one labelled `good first issue`, the smallest piece of work (one function, `verify_password` in `core/security.py`, plus an existing test whose `xfail` marker had to come off, about 1 to 2 hours), and it is in Python, my strongest language. Because this is a sandbox issue and not one from the site, I asked my instructor whether I could build my story on it. In unit 5 my scout's wild scan, with queries built from my fit profile, surfaced other candidates (sysknife#633, holographic-memory#161, hoops#8 and crowsnest#4 accepted, Discord-Dashboard/Core#112 rejected), but I never claimed any of them.

My scout now disagrees with my choice. When I graded #72 on 2026-11-22 with my patched rubric it returned `reject`: `not-claimed` failed because about 29 open PRs now fix #72 (mine, #223, is one of them), and the thread has 108 comments, almost all claim comments. I kept the issue anyway, for two reasons: Path Review's house rules say a classmate's PR does not block mine, and by then I already had a claim comment, a reproduction, a posted plan and an open PR with a green CI run on it. A scout aimed at the real world is right that this issue is crowded. What I hope to learn from it is the whole path of a small security-relevant bug, from claim to a reviewed PR, including what a maintainer says about it. My honest read of the scout: it caught real problems the first time I ran it on the wild (two of its four accepts were repos with zero stars, which I fixed with a star floor), but its ranking was only partly verified and its recency tie-break favoured brand-new repos.

**Claim comment:** https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5861106191

**Toolkit**

- Tool: issue-select in unit 1 (three Path Review candidates), then issue-scout in unit 5 (my four-query strategy and my rubric, with the unit 6 scope patch), then a direct `issue-scout: grade` of #72 on 2026-11-22.
- What it produced: in unit 1, three accepts with #72 first; in unit 5, a scan of 4 accepts and 1 reject; on 2026-11-22, a `reject` for #72 on `not-claimed` ("about 29 open PRs fix #72"). The patched rubric only writes a difficulty note for accepted issues, so #72 got none; my own reading is straightforward, since the issue names the function and the test.
- Difficulties: the scan accepted hoops#8 and crowsnest#4, repos with 0 stars created within the past week, because my `repo-in-use` check only looked at recent pushes. The `maintainer-responsive` preferred check also failed sysknife on a technicality (its maintainer files nearly all issues, so there are almost no outside issues to sample).
- What changed: I added a 5-star minimum to `repo-in-use` (20 would have rejected sysknife, the scan's best candidate), a ranking tie-break that prefers maintainer-filed issues, and I replaced my `scope-bounded` check with `scope-feasible` plus a difficulty note. The new tie-break was not re-tested, and I expect the scope patch to flip two unit 1 scope rejects to accept, which I have not measured. All of this is logged in `scan-notes.md`.

---

## Understanding the Issue

**Problem Description**

`verify_password` in `core/security.py` hands the stored password hash straight to passlib's `pwd_context.verify`. If the stored value is not a usable bcrypt hash, passlib raises instead of the function returning `False`, and the exception escapes to the caller. The caller, the login handler in `api/routes/auth.py`, only has a generic `except Exception`, so a user whose stored hash is malformed gets a 500 instead of the 401 a wrong password gets.

**Expected Behavior**

`verify_password` should fail closed: for a stored value that is not a valid hash it returns `False`, so the login route answers `401 Invalid email or password` exactly as it does for a wrong password.

**Current Behavior**

For `"not_a_valid_bcrypt_hash"` and `""` passlib raises `passlib.exc.UnknownHashError`. For bcrypt-shaped but invalid values (`"$2b$12$abc"`, `"$2b$12$" + "!" * 53`) it raises a plain `ValueError`. Through `POST /auth/login` the result is `500 {'detail': 'Login failed'}`, where a valid hash with a wrong password returns `401`. The covering test, `test_verify_with_wrong_hash_format`, is marked `strict` `xfail` (manifest H-05), which hides the failure.

**Affected Components**

`core/security.py` (`verify_password`, the function to fix), `tests/unit/test_security.py` (the H-05 test and its `xfail` marker), and `api/routes/auth.py` (the login handler, which only shows the symptom and is the function's only caller).

**Toolkit**

- Tool: Claude Code, used to read `core/security.py` and find the one caller of `verify_password`; no skill graded this phase.
- What it produced: the call chain from the login route to `pwd_context.verify`, which is why the 500 shows up at the login route and not in the function.
- Difficulties: none met.
- What changed: nothing.

---

## Reproduction Process

**Environment Setup**

I first reproduced on Intel (x86_64) macOS 26.5.2 with Python 3.12.13 in a fresh `.venv`, passlib 1.7.4, bcrypt 4.3.0 and pytest 9.1.1, on commit `2f4e82f` of `main` with no local changes (the earlier reports on the thread were arm64 macOS, Windows and WSL). The one setup problem: `pip install -e ".[dev]"` fails on Intel macOS without a Rust toolchain, because `mutmut` pulls `libcst>=1.9.0`, which has no x86_64 macOS wheel. I installed the project plus the dev test dependencies without `mutmut`, which this test does not use. No Docker, Postgres or Redis was needed. I later re-ran everything on Windows 11 with Python 3.11.9, the version CI uses, where `pip install -e ".[dev]"` worked and gave the same results.

**Steps to Reproduce**

1. `git clone https://github.com/codepath/pathreview-ai301-fa26-s3.git`, `git checkout 2f4e82f`, `cp .env.example .env`, create a venv, and install the project and the test dependencies.
2. Run the H-05 test as shipped: `pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v`. It reports `XFAIL`, because the strict marker hides the error.
3. Run it with the marker ignored: `... --runxfail --tb=short`. It fails with `core/security.py:37: in verify_password` and `passlib.exc.UnknownHashError: hash could not be identified`.
4. Run a small probe (`login_probe.py`, in my reproduction comment) that sends `POST /auth/login` through FastAPI's test client with a stub database session. A valid bcrypt hash with a wrong password returns `401`, and the malformed hash `"not_a_valid_bcrypt_hash"` returns `500 {'detail': 'Login failed'}`.

**Reproduction Evidence**

- **Commit showing reproduction:** none. I reproduced it on unmodified `main` without changing any code; the commit with the fix is `913d762` on my fork, branch `fix/72-malformed-password-hash`.
- **Screenshots/logs:** my reproduction comment on the issue, with the commands and the full output: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5864815942
- **My findings:** reproduced. The failure comes from the unrecognizable stored value, not from verification in general, because the valid-hash control behaves correctly (`401`). Two shapes escape, not one: `UnknownHashError` for unrecognizable values, and a plain `ValueError` for bcrypt-shaped but invalid ones (an earlier report on the thread had shown the second; I confirmed it with `"$2b$12$" + "!" * 53`). I only exercised the login route.

**Toolkit**

- Tool: repro-check (unit 2), which graded my claim comment and my reproduction report before I posted them.
- What it produced: a per-check read-out ending in a JSON verdict; my skill's one graded eval run agreed with the staff labels on all 20 packages.
- Difficulties: my first full-run attempt errored on every package because the Claude Code CLI was not signed in, so no package was graded and no run file was written; I list it so the history is complete.
- What changed: nothing in the skill after the graded run; I signed in and re-ran.

---

## Solution Approach

**Analysis**

`verify_password` returns `bool(pwd_context.verify(plain_password, hashed_password))` with nothing around it (`core/security.py` line 37). passlib raises for a stored value it cannot verify, and there are two exception shapes: `UnknownHashError` for a value it cannot identify as a hash, and a plain `ValueError` for a bcrypt-shaped value with a bad salt or body. `UnknownHashError` is a subclass of `ValueError` (its MRO is `UnknownHashError, ValueError, Exception`), so one `except ValueError` covers both. The login route is not the cause; it is where the escaped exception shows up, through its generic `except Exception`.

**Proposed Solution**

Wrap the `pwd_context.verify(...)` call in `try/except ValueError` and return `False`, and update the function's docstring to say so. Remove the strict `xfail` marker from `test_verify_with_wrong_hash_format`, and add one parametrized test for `""`, `"$2b$12$abc"` and `"$2b$12$" + "!" * 53`, which covers the plain `ValueError` shape the existing test does not. Deliberately not changing: `api/routes/auth.py` (it answers `401` on its own once `verify_password` returns `False`), `hash_password`, the passlib and bcrypt pins, non-string stored values, and logging inside `core/security.py`.

**Implementation Plan**

1. In `core/security.py`, wrap the `pwd_context.verify` call in `verify_password` in `try/except ValueError` and return `False`.
2. In `tests/unit/test_security.py`, delete the `@pytest.mark.xfail(strict=True, ...)` decorator on `test_verify_with_wrong_hash_format`.
3. Add `test_verify_with_malformed_bcrypt_hash_returns_false`, parametrized over the three values above.

Deviations: the only thing the build added beyond the plan comment was rewording the `Returns:` line of the docstring, because the old line no longer described the behavior. One side effect I tested and disclosed in the plan comment: `PasswordSizeError`, which passlib raises for an over-long password, is also a `ValueError`, so a 5000-character password now returns `False` (a `401` at login) instead of raising.

**Toolkit**

- Tool: plan-check (unit 3), which graded my `plan.md` and plan comment against the issue and my reproduction.
- What it produced: `accept` on all three runs; the re-runs flagged two real things, that `PasswordSizeError` is also a `ValueError` (an unknown I had listed but not tested), and that my comment's tense understated what I had already run.
- Difficulties: its procedure only knew eval-bundle section names, so in live mode it had to map them to the thread and `docs/CONTRIBUTING.md` itself and reported that as a gap.
- What changed: I tested the `PasswordSizeError` case, moved it from "unknown" into the plan's risks and the posted comment, and corrected the comment's wording. I did not change `procedure.md` after the graded eval run.

---

## Testing Strategy

I re-ran my reproduction before and after the fix, and the repo's own checks. Before, on `main`: the H-05 test run with `--runxfail` fails with `UnknownHashError` at `core/security.py:37`; the login probe returns `500 {'detail': 'Login failed'}` for the malformed hash; and the four relevant tests (H-05 plus the three new cases) all fail against the original `security.py` (`4 failed`, with `UnknownHashError` twice and `ValueError` twice). After, on the branch: those four tests pass (`4 passed`), the login probe returns `401 {'detail': 'Invalid email or password'}` for the malformed hash with the valid-hash case unchanged, and a direct probe shows a valid hash still returns `True` for the right password and `False` for a wrong one. The repo's checks all pass: `pytest tests/unit -m unit` gives 379 passed and 52 xfailed (other seeded bugs, none flipped to `XPASS`), `ruff check .` and `black --check .` are clean, `mypy` reports no issues in 76 files, and `tests/integration` has no tests (`no tests ran`). After I opened the PR, all five CI jobs (lint, typecheck, test-unit, test-integration, frontend) ran green on GitHub.

**Toolkit**

- Tool: pytest, ruff, black and mypy run through Claude Code, and pr-precheck (unit 4) on the evidence.
- What it produced: `test_evidence.md` with every before and after output, and a pr-precheck `accept` for test-evidence-decisive.
- Difficulties: `make` is not installed on my Windows machine, so I ran the command each Makefile target expands to and said so. I also made one real mistake: my first "before" run of the new tests used `git stash` to put back the original `security.py`, but the fix was already committed so the stash held nothing and the tests passed against the fixed code. I caught it because the exit code was wrong for a "before", and redid it by checking out `main`'s `security.py`, which gave the true `4 failed`.
- What changed: I recorded the `make` caveat in the PR and re-captured the before run properly.

---

## Implementation Notes

Dates are GitHub's UTC timestamps.

**2026-09-28**

Claimed #72 and posted my reproduction. I wrote a claim comment that named a concrete first step and what I would report back, then reproduced on Intel macOS and posted the commands and output, including the login route's `500`. The one wrong turn was the `mutmut`/`libcst` install failure on Intel macOS, which I worked around by installing without `mutmut`.

**2026-10-07**

Wrote the plan and posted it as a comment on the issue. Cloned the repo on my Windows machine, re-ran the reproduction there (same results), and built the fix on `fix/72-malformed-password-hash` before posting the plan, which the posted comment says plainly. plan-check accepted the plan, and its re-run caught the `PasswordSizeError` case, which I tested and added.

**2026-10-14**

Opened PR #223. pr-precheck rejected my first draft because my only commit had the wrong message (`Unit 3: ...` instead of the `fix(api): ...` form `docs/CONTRIBUTING.md` requires); I amended it and force-pushed to my fork with `--force-with-lease` before opening the PR. When I opened the PR I pasted the title but not the description, so it showed the blank template for about a minute; I replaced it with the filled-in description straight away.

**Toolkit**

- Tool: Claude Code for the build (it drafted the code, tests and description; I read the diff), with plan-check and pr-precheck gating the plan and the PR.
- What it produced: a two-file diff (`core/security.py`, `tests/unit/test_security.py`, 21 lines added and 6 removed) and a PR description whose claims match it.
- Difficulties: the wrong commit message and the blank PR description above, both mine.
- What changed: pr-precheck now catches the commit-message rule through `template-asks-met`; I now check what GitHub actually recorded after opening a PR instead of trusting my paste.

---

## Pull Request

**PR Link:** https://github.com/codepath/pathreview-ai301-fa26-s3/pull/223

**PR Description:** Title `fix(api): return False from verify_password for malformed stored hashes`, with `Closes #72`. The summary says that `verify_password` let passlib's exception escape, that this made `POST /auth/login` return `500` for a malformed stored hash, and that the change wraps the call in `try/except ValueError`. It lists the three changes, pastes the before and after outputs and the repo checks, and has Notes for Reviewers with my AI-use disclosure (Claude Code drafted the fix, tests and description and ran the checks; I read the diff), the docstring deviation, the `PasswordSizeError` side effect, what I left out on purpose, and the other open PRs on the issue.

**Maintainer Feedback:**

- 2026-11-22: none yet. The PR has no comments and no reviews. All five CI jobs ran and passed.

**Status:** Awaiting review.

**Toolkit**

- Tool: pr-precheck (unit 4), run on my branch's diff, my `plan.md`, my draft description and my test evidence.
- What it produced: `reject` on my first draft (one required check: the commit message ignored the contributing guide's Conventional Commits rule), then `accept` after I amended the commit.
- Difficulties: it could not read the issue thread because `gh` was not set up then, so it called the plan-comment link and my claims about the other PRs "unverified"; I checked those myself.
- What changed: nothing in the skill after the graded eval run (it agreed with gold on all 20); the fix was to my commit.

---

## Learnings and Reflections

**Technical Skills Gained**

How passlib reports bad hashes: `UnknownHashError` and `PasswordSizeError` are both `ValueError` subclasses, which is why one narrow `except ValueError` was enough and a bare `except Exception` was not needed. What "fail closed" means for a password check, and how a strict `xfail` marker works (it makes CI fail with `XPASS(strict)` once the bug is fixed, so removing it is part of the fix). I also learned Conventional Commits, and how to open a pull request across forks.

**Challenges Overcome**

The `mutmut` install failure on Intel macOS, the `make`-less Windows machine, the false "before" run caused by stashing a committed file, and the wrong commit message. Each one is in the Toolkit blocks above with what I did about it.

**What I'd Do Differently Next Time**

Read `docs/CONTRIBUTING.md` for the commit and branch rules before I commit, not after a tool tells me. Check `git status` and `git log` against GitHub before I say something is pushed, and read back what GitHub recorded after I open a PR. And treat a crowded issue as a signal: my own scout rejected #72 for being claimed by dozens of people, and I would weigh that earlier.

---

## Resources Used

- `docs/CONTRIBUTING.md` in the Path Review repo: the branch naming rule, Conventional Commits, and the rule that removing the `xfail` marker is part of a seeded-bug fix.
- The #72 thread: classmates' earlier reports showed the plain `ValueError` shape, which I then confirmed myself, and the open PRs #78, #91 and #92, whose diffs I read to see what they catch.
- passlib's exception classes, read in the installed package to check that `UnknownHashError` and `PasswordSizeError` subclass `ValueError`.
