# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

AdrianOlm1

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-6030395169

> Plan for #72, built from my own reproduction above (https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5864815942, commit `2f4e82f`; I also re-ran it on Windows 11 with Python 3.11.9 and got the same results).
>
> **Cause.** `verify_password` in `core/security.py` calls `pwd_context.verify(...)` with no handling at line 37. For a stored value that isn't a usable bcrypt hash, passlib raises and it escapes. My repro and a follow-up run show two shapes: `UnknownHashError` for `"not_a_valid_bcrypt_hash"` and `""`, and a plain `ValueError` for bcrypt-shaped values (`"$2b$12$abc"` and `"$2b$12$" + "!" * 53`). `UnknownHashError` subclasses `ValueError`. The valid-hash control still returns 401 on a wrong password, so the fault is the stored value, and `/auth/login` only returns 500 because its generic `except Exception` catches the escape.
>
> **Change.** In `verify_password`, wrap the `pwd_context.verify` call in `try/except ValueError` and return `False`, and update that function's docstring to say so. In `tests/unit/test_security.py`, remove the strict `xfail` marker from `test_verify_with_wrong_hash_format` and add one parametrized test for `""`, `"$2b$12$abc"` and `"$2b$12$" + "!" * 53`, since the existing test only covers the `UnknownHashError` shape.
>
> **Not touching:** `api/routes/auth.py` (it answers 401 by itself once `verify_password` returns `False`), `hash_password`, the passlib/bcrypt pins, or non-string stored values.
>
> **How I'll check it, and what I've run.** I tried this on a local branch before posting, and I'll push it once this plan is up. The H-05 test fails with `UnknownHashError` under `--runxfail` on `main` and passes with the marker removed; all four tests (H-05 plus the three new cases) fail on `main` and pass with the change. `login_probe.py` from my repro moves the malformed-hash case from `500 Login failed` to `401 Invalid email or password`, with the valid-hash case unchanged. `ruff`, `black --check`, `mypy` and `pytest tests/unit -m unit` are clean locally (379 passed, 52 xfailed). I haven't run `test-integration` or the frontend job.
>
> **Unknowns.** Catching `ValueError` also catches passlib's `PasswordSizeError`, which is a `ValueError` raised for an over-long password. I checked: on `main`, `verify_password` raises it for a 5000-character password, and with the change it returns `False`. I think that is right, since a password that long cannot match a stored hash, but it is a behavior change beyond malformed hashes, so I'm flagging it. I haven't found any other `ValueError` source with the pinned `bcrypt<5`. I only ran the login route, though `verify_password` has no other caller.
>
> PRs #78, #91 and #92 are already open for this issue. #78 and #92 also catch `ValueError`; I got to the same place from my own reproduction, which shows bcrypt-shaped values raising a plain `ValueError`. #91 catches only `UnknownHashError`, which would leave those cases raising.
>
> I'm using Claude Code to help with this. It helped draft this plan, which I checked against my repro and edited, and I'll read every diff before I keep it.

---

## Your branch

**Branch**

fix/72-malformed-password-hash

**Evidence**

Issue #72, commit `2f4e82f` (upstream `main`) plus the change on `fix/72-malformed-password-hash`. Windows 11, Python 3.11.9, passlib 1.7.4, bcrypt 4.3.0, pytest 9.1.1. Run from the repo root with the venv active (`PYTHONPATH=.`). The `login_probe.py` script is the one from my Unit 2 repro comment.

**Before** (original `core/security.py`)

```
$ pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail --tb=short --disable-warnings
tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format FAILED [100%]
E   passlib.exc.UnknownHashError: hash could not be identified
======================== 1 failed, 1 warning in 0.66s =========================

$ pytest tests/unit/test_security.py -k "wrong_hash_format or malformed_bcrypt" -v --tb=line --disable-warnings   (new tests, original code)
test_verify_with_wrong_hash_format FAILED
test_verify_with_malformed_bcrypt_hash_returns_false[] FAILED
test_verify_with_malformed_bcrypt_hash_returns_false[$2b$12$abc] FAILED
test_verify_with_malformed_bcrypt_hash_returns_false[$2b$12$!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!] FAILED
E   passlib.exc.UnknownHashError: hash could not be identified
E   passlib.exc.UnknownHashError: hash could not be identified
E   ValueError: salt too small (bcrypt requires exactly 22 chars)
E   ValueError: invalid characters in bcrypt checksum
================= 4 failed, 24 deselected, 1 warning in 0.57s =================

$ python login_probe.py
valid bcrypt hash: 401 {'detail': 'Invalid email or password'}
malformed hash: 500 {'detail': 'Login failed'}

$ python shapes_probe.py
valid hash, right password: returned True
valid hash, wrong password: returned False
not_a_valid_bcrypt_hash: RAISED passlib.exc.UnknownHashError: hash could not be identified
empty string: RAISED passlib.exc.UnknownHashError: hash could not be identified
truncated bcrypt $2b$12$abc: RAISED builtins.ValueError: salt too small (bcrypt requires exactly 22 chars)
bcrypt-shaped, bad body: RAISED builtins.ValueError: invalid characters in bcrypt checksum
```

**After** (with the change)

```
$ pytest tests/unit/test_security.py -k "wrong_hash_format or malformed_bcrypt" -v --disable-warnings
test_verify_with_wrong_hash_format PASSED [ 25%]
test_verify_with_malformed_bcrypt_hash_returns_false[] PASSED [ 50%]
test_verify_with_malformed_bcrypt_hash_returns_false[$2b$12$abc] PASSED [ 75%]
test_verify_with_malformed_bcrypt_hash_returns_false[$2b$12$!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!] PASSED [100%]
================= 4 passed, 24 deselected, 1 warning in 0.57s =================

$ pytest tests/unit/test_security.py -v --disable-warnings
======================== 28 passed, 1 warning in 4.54s ========================

$ python login_probe.py
valid bcrypt hash: 401 {'detail': 'Invalid email or password'}
malformed hash: 401 {'detail': 'Invalid email or password'}

$ python shapes_probe.py
valid hash, right password: returned True
valid hash, wrong password: returned False
not_a_valid_bcrypt_hash: returned False
empty string: returned False
truncated bcrypt $2b$12$abc: returned False
bcrypt-shaped, bad body: returned False

$ ruff check .
All checks passed!
$ black --check core/security.py tests/unit/test_security.py
2 files would be left unchanged.
$ mypy api/ core/ ingestion/ rag/ agent/ safety/
Success: no issues found in 76 source files
$ pytest tests/unit -m unit -q
379 passed, 52 xfailed, 2 warnings in 9.04s
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke test, `--limit 3`: `agreement: 3/3 scored items`
2. First full run: `agreement: 19/20 scored items  (bar: 18/20: PASS)`. The one miss was `pkg-14` (`failed: executable, honest-unknowns`).
3. Re-check after rewording `executable`, `--only pkg-14,pkg-10,pkg-17,pkg-18`: `agreement: 4/4 scored items`
4. Confirming full run, saved with `--save-run`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`, categories `clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`. This is the run in `eval-run.txt`.

**Package analysis**

`pkg-14` (zellij, color-query responses echoed into the pane on reattach). Gold label: `accept`, noted as "honestly scoped-down: reattach handshake fix with a regression-window repro; defers the untestable Windows variant and says so; arguable on the deferral, ready as scoped". My final rubric: `accept`, agreeing with gold.

My first rubric said `reject`, failing `executable`. The plan's Files line says: "exact functions to be pinned in the PR after tracing the query issuance with debug logs, which I have working". My first `executable` wording failed any plan where "a real decision is deferred to build time", and the grader read that sentence as a deferred decision. But the plan had already made both real decisions. It names the area ("the client attach/reattach path in `zellij-server`") and commits to what the change does ("consuming or draining pending OSC query responses ... before pane input is wired"). What it leaves open is only which function inside that area to edit, which is found by tracing, not decided. The packages that really are unbuildable defer the choice itself, such as `pkg-17`'s "gocui? tcell? not sure". So I changed `executable` to separate a deferred decision from a location still to be found.

**Check rationale**

`executable`, exactly as it reads in `tools/plan-check/rubric.md`:

> A stranger could start work without asking the author anything: the plan names where the change goes (a file, module, or component) and commits to one approach, meaning it says what the change does. Fail if the approach is "investigate", "profile", or "poke around", if a real DECISION about where or how is deferred to build time (a choice between layers, approaches, or locations: "whichever is easier", "somewhere", "gocui? tcell? not sure", "maybe also check"), or if no file, module, or area is named. Naming the module and the change but leaving the exact function or line to be found by tracing is NOT a deferred decision and passes. A short plan that names the file and the change passes.

It reads this way because of the `pkg-14` miss above. The first draft said "(file, function, or component)", which let a grader fail a plan for not naming the function. I kept the failure cases that match the unbuildable packages (`pkg-10` "profile-and-optimize with no files", `pkg-17` "no chosen layer", `pkg-18` "whichever is easier") and added the sentence that tells the grader the difference between a decision left open and a location still to be found. I rejected dropping the check, since the unbuildable category needs something that catches those three.

**Trade-offs**

Loosening `executable` could have let a vague plan through, so I re-ran `--only pkg-14,pkg-10,pkg-17,pkg-18`: the three unbuildable packages stayed `reject` and `pkg-14` flipped to `accept` (`agreement: 4/4`). The confirming full run then showed no other package moving (20/20).

What the check gives up: it accepts a plan that names a module and a change without naming the function. If the named area is so wide that it is not really a location (for example "the networking layer"), the check as written would still pass it, because it only tests whether the plan commits to an approach and names an area. None of the 20 eval packages tests that case, so this is a known blind spot, not a confirmed one.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
