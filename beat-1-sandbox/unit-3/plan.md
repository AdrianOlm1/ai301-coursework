# Plan: #72 `verify_password` raises on malformed stored hashes

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72
My reproduction: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5864815942 (commit `2f4e82f`, passlib 1.7.4, bcrypt 4.3.0). I re-ran it on Windows 11, Python 3.11.9, same commit and versions, with the same results.

## Diagnosis

`verify_password` in `core/security.py` (line 37) returns `bool(pwd_context.verify(plain_password, hashed_password))` with nothing around it. When the stored value is not a usable bcrypt hash, passlib raises instead of returning, and the exception escapes. There are two escape shapes, and both are `ValueError`s:

- a value passlib cannot identify as a hash at all raises `passlib.exc.UnknownHashError` (`"not_a_valid_bcrypt_hash"` and `""`), and `UnknownHashError` is a subclass of `ValueError` (its MRO here is `UnknownHashError, ValueError, Exception`);
- a bcrypt-shaped value with a bad salt or body raises a plain `builtins.ValueError` (`"$2b$12$abc"` gives "salt too small", `"$2b$12$" + "!" * 53` gives "invalid characters in bcrypt checksum").

Evidence this explains the whole repro:

- Repro, step 2 (`--runxfail`): `core/security.py:37: in verify_password` then `passlib.exc.UnknownHashError: hash could not be identified`, so the failure is in the unguarded call.
- Control: a valid bcrypt hash behaves correctly. `valid bcrypt hash: 401 {'detail': 'Invalid email or password'}`, and `verify_password` returns `True` for the right password and `False` for a wrong one. So the fault is the unrecognizable stored value, not verification in general.
- Caller effect: `malformed hash: 500 {'detail': 'Login failed'}`. `api/routes/auth.py` only has a generic `except Exception` around `verify_password`, which turns the escaped error into a 500. The login route is not the cause; it is where the escape shows up.

## Scope

In scope: make `verify_password` return `False` for a stored hash that passlib cannot verify (the two shapes above), and take the `xfail` marker off the H-05 test, adding a test for the second shape.

Not in scope:

- `api/routes/auth.py` and its generic `except Exception` (once `verify_password` returns `False`, the login route already answers 401 on its own).
- `hash_password`, the `CryptContext` configuration, or the passlib and bcrypt pins.
- Non-string stored values (`verify_password(..., 123)` raises `TypeError`); the signature says `str`, and `None` already returns `False`.
- Logging inside `core/security.py`; the login route already logs `login_failed_invalid_credentials`.

## Files I will touch

- `core/security.py`: `verify_password` only.
- `tests/unit/test_security.py`: remove the `@pytest.mark.xfail(...)` decorator on `test_verify_with_wrong_hash_format`, and add one parametrized test next to it.

`pyproject.toml` has no suppression for #72 (I searched it), so nothing there changes.

## Approach

1. In `verify_password`, wrap the `pwd_context.verify(...)` call in `try/except ValueError` and return `False` there. Catching `ValueError` covers `UnknownHashError` and the plain `ValueError`s, so I do not need to import `passlib.exc`. A comment on the `except` says why it fails closed.
2. In `tests/unit/test_security.py`, delete the `@pytest.mark.xfail(strict=True, ...)` decorator on `test_verify_with_wrong_hash_format`. The marker is `strict=True`, so leaving it makes CI fail with `XPASS(strict)` once the fix lands.
3. Add `test_verify_with_malformed_bcrypt_hash_returns_false`, parametrized over `""`, `"$2b$12$abc"`, and `"$2b$12$" + "!" * 53`, each asserting `verify_password("password", stored) is False`. The existing H-05 test only covers the `UnknownHashError` shape, so this covers the plain `ValueError` shape.

## Test plan

Re-run my reproduction steps against the change. Expected after the fix:

1. `pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --disable-warnings` shows `PASSED` with the marker removed. Before the change the same test, run with `--runxfail`, shows `FAILED` with `passlib.exc.UnknownHashError: hash could not be identified`.
2. The new parametrized test passes for all three values after the change. Before the change, run against the old `verify_password`, `""` fails with `UnknownHashError` and the other two fail with `ValueError`.
3. `python login_probe.py` (the script from my repro comment): `valid bcrypt hash: 401 {'detail': 'Invalid email or password'}` is unchanged, and `malformed hash:` goes from `500 {'detail': 'Login failed'}` to `401 {'detail': 'Invalid email or password'}`.
4. Regression guard: the existing `test_verify_password_correct`, `test_verify_password_incorrect`, `test_verify_password_case_sensitive` and the long-password test still pass, and a valid hash still returns `True` for the right password and `False` for a wrong one.
5. `make lint`, `make typecheck`, and `make test-unit` pass.

## Risks and unknowns

- A `ValueError` raised for a reason other than a bad stored hash would also be turned into `False`. One such case exists and I checked it: passlib's `PasswordSizeError` is a `ValueError` subclass (MRO `PasswordSizeError, PasswordValueError, ValueError`) raised for an over-long password. On `main`, `verify_password("a" * 5000, <valid hash>)` raises `PasswordSizeError`; with the change it returns `False`. I think that is correct, because a password that long cannot match a stored hash, and it turns a login 500 into a 401, but it is a behavior change beyond malformed hashes, so the plan comment flags it. I have not found any other source with the pinned `bcrypt>=4.0.1,<5.0.0`, and the existing long-password test passes. The `False` is the safe direction for a login check.
- I have only exercised the login route (`POST /auth/login`). `verify_password` has no other caller, which I checked with a repo search, but I did not run any other route.
- Only the unit tests and the login probe run on my machine. I cannot confirm the `test-integration` and `frontend` CI jobs until the PR runs; they should be unaffected, since only `core/security.py` and one test file change.

## Deviations

Nearly nothing changed; the plan held. The code change is the `try/except ValueError` in `verify_password` that I planned, the `xfail` marker is gone from `test_verify_with_wrong_hash_format`, and the new parametrized test covers `""`, `"$2b$12$abc"` and `"$2b$12$" + "!" * 53`. Both files and the approach are exactly what the plan comment names, so no follow-up comment was needed.

One thing about order: I built the change on a local branch before I posted the plan comment, not after, and the posted comment says so. Re-running plan-check on the finished drafts turned up the `PasswordSizeError` case (an over-long password also returns `False` now), which I had listed as an unknown, so I tested it, moved it into Risks above, and put it in the comment before posting.

One small addition the plan did not list: I also reworded the `Returns:` line of the `verify_password` docstring to say an unverifiable stored hash returns `False`, because the repo's contributing guide asks for docstrings on public functions and the old line ("True if password matches, False otherwise") no longer described the behavior. It is a docstring edit in the same function, not a new file or a change in scope.

What I checked: the new tests failed 4/4 on the original code and pass 4/4 now, `login_probe.py` moved the malformed hash from `500 Login failed` to `401 Invalid email or password`, and `ruff`, `black --check`, `mypy` and `pytest tests/unit -m unit` (379 passed, 52 xfailed, no XPASS) are clean. I did not run `test-integration` or the frontend job locally.
