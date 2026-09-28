# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

AdrianOlm1

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5861106191

"I'd like to work on #72 as my first Path Review contribution. A few classmates have claimed it too; per the course house rules I'm doing my own setup and will post my own report.

What the issue describes: `verify_password()` in `core/security.py` passes the stored hash straight to `pwd_context.verify()`, so when the stored value isn't a recognizable hash, passlib's `UnknownHashError` escapes instead of the function returning `False`. The covering test is `tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format`, marked strict `xfail` for H-05.

What I'll do next:

1. Set the project up from `docs/SETUP.md` in a fork and record my OS, Python, passlib and bcrypt versions and the commit I test. The earlier reports here are from arm64 macOS, Windows and WSL; mine will be Intel (x86_64) macOS.
2. Run the H-05 test as shipped and with `--runxfail`, with a valid bcrypt hash as a control.
3. Check what the caller sees: `api/routes/auth.py` calls `verify_password()` inside the login handler, so I want to see what `POST /auth/login` returns when a user's stored hash is malformed, compared with a normal wrong password.

I'll post the commands and output here whether or not it reproduces."


**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72#issuecomment-5864815942

"Reproduction report for #72. **Result: reproduced** at commit `2f4e82f` (current `main`). `verify_password()` raises `passlib.exc.UnknownHashError` for the test's malformed stored hash instead of returning `False`. As a result, `POST /auth/login` returns **500 "Login failed"** for a user whose stored hash is malformed, where a normal wrong password gets **401**.

**Environment**

- macOS 26.5.2 (build 25F84), Intel x86_64. Earlier reports on this issue are arm64 macOS, Windows and WSL.
- Python 3.12.13 in a fresh `.venv`. CI pins 3.11, which I don't have installed; 3.12 is within `requires-python = ">=3.11"`.
- passlib 1.7.4, bcrypt 4.3.0, pytest 9.1.1
- Code: commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088`, no local changes
- Setup deviation: `pip install -e ".[dev]"` fails on Intel macOS without a Rust toolchain, because `mutmut` pulls `libcst>=1.9.0`, which has no x86_64 macOS wheel. I installed the project plus the dev test dependencies without `mutmut` (command below). `mutmut` isn't involved in this test. No Docker, Postgres or Redis was needed.

**Steps**

```bash
git clone https://github.com/codepath/pathreview-ai301-fa26-s3.git
cd pathreview-ai301-fa26-s3 && git checkout 2f4e82f
cp .env.example .env
python3.12 -m venv .venv && source .venv/bin/activate
pip install -e . "pytest>=7.4.0" "pytest-cov>=4.1.0" "pytest-asyncio>=0.23.0" \
  "pytest-benchmark>=4.0.0" "pytest-httpserver>=1.0.8" "hypothesis>=6.92.0"

# 1. As shipped: the strict xfail hides the error
pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --disable-warnings
# -> XFAIL, "1 xfailed, 2 warnings"

# 2. Same test with the xfail marker ignored
pytest tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format -v --runxfail --tb=short --disable-warnings
```

Output of step 2 (from the traceback on, with pytest's `^^^` marker lines removed):

```
tests/unit/test_security.py:227: in test_verify_with_wrong_hash_format
    result = verify_password("password", wrong_hash)
core/security.py:37: in verify_password
    return bool(pwd_context.verify(plain_password, hashed_password))
.venv/lib/python3.12/site-packages/passlib/context.py:2343: in verify
    record = self._get_or_identify_record(hash, scheme, category)
.venv/lib/python3.12/site-packages/passlib/context.py:2031: in _get_or_identify_record
    return self._identify_record(hash, category)
.venv/lib/python3.12/site-packages/passlib/context.py:1132: in identify_record
    raise exc.UnknownHashError("hash could not be identified")
E   passlib.exc.UnknownHashError: hash could not be identified
FAILED tests/unit/test_security.py::TestSecurity::test_verify_with_wrong_hash_format
======================== 1 failed, 2 warnings in 0.56s =========================
```

**3. What the login route returns** (a stub DB session stands in for Postgres, so only the stored hash changes between the two runs):

```python
# login_probe.py, run from the repo root with the venv active: python login_probe.py
from types import SimpleNamespace
from fastapi.testclient import TestClient
from api.main import app
from core.database import get_db
from core.security import hash_password

def session_returning(stored_hash):
    user = SimpleNamespace(id=1, email="user1@example.com", is_active=True, hashed_password=stored_hash)
    class FakeSession:
        async def execute(self, _query):
            return SimpleNamespace(scalar_one_or_none=lambda: user)
    async def override():
        yield FakeSession()
    return override

client = TestClient(app)
form = {"username": "user1@example.com", "password": "wrong-password"}
for label, stored in [("valid bcrypt hash", hash_password("password1")),
                      ("malformed hash", "not_a_valid_bcrypt_hash")]:
    app.dependency_overrides[get_db] = session_returning(stored)
    r = client.post("/auth/login", data=form)
    print(f"{label}: {r.status_code} {r.json()}")
```

Output (log lines trimmed to the relevant ones):

```
[warning  ] login_failed_invalid_credentials email=user1@example.com
valid bcrypt hash: 401 {'detail': 'Invalid email or password'}
[error    ] login_error                    error='hash could not be identified'
malformed hash: 500 {'detail': 'Login failed'}
```

**Expected:** `verify_password()` returns `False` for a stored value that isn't a usable hash, so the login route answers 401 the same way it does for a wrong password.

**Actual:** `UnknownHashError` escapes from `core/security.py:37`, the test fails under `--runxfail`, and the login handler's generic `except Exception` turns the error into a 500. The valid-hash control behaves correctly (401), so the failure comes from the unrecognizable stored hash, not from verification in general. I only tested the login route.

Two notes, not part of the bug itself: passlib 1.7.4 prints a trapped `AttributeError: module 'bcrypt' has no attribute '__about__'` traceback the first time it loads bcrypt 4.3.0, which doesn't change the result. Also, earlier reports here showed a truncated `"$2b$12$abc"` raising a plain `ValueError` ("salt too small") rather than `UnknownHashError`. I tried a different bcrypt-shaped value with a full-length but invalid body, `"$2b$12$" + "!" * 53`, and it also raised a plain `ValueError`, this time with `invalid characters in bcrypt checksum`.

Next I'll try a change that returns `False` for these inputs and remove the strict `xfail`.

I used Claude Code to help run these steps and draft this comment. I ran the commands on my machine, and the output above is copied from those runs."


## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

0. First full-run attempt errored before grading anything: every package reported `claude exited 1` because the Claude Code CLI wasn't signed in. The harness printed `agreement: 0/0 scored items` and did not write `eval-run.txt`. No package was graded, so this isn't a rubric score. I list it so the history is complete.
1. First graded full run with `--save-run eval-run.txt`: **20/20 scored items**, `categories: clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`, `bar: 18/20: PASS`. This is the run committed in `eval-run.txt`. There were no disagreements, so I made no `--only` re-runs and no revisions.

**Package analysis**

`pkg-20` (ghostty-org/ghostty#13604), category `disclosure`. Gold label: `reject`. My rubric: `reject`, agreeing with gold.

On every proof check this is the best package in the set. The run passed `env-recorded` ("ghostty 1.3.1 (release build, Fedora 42 RPM), GTK backend, GNOME 48 (Wayland)"), `trigger-matches` (the issue's exact `--config-default-files=false --window-theme=dark` launch and `CSI ? 996 n` query) and `behavior-shown`. For `behavior-shown` the evidence was "output `^[[?997;2n` (wrong/light) vs control `^[[?997;1n` (correct/dark) matches issue's described symptom exactly". It failed on one check, `ai-disclosure`: "repo requires disclosing 'All AI usage in any form... stating the tool used and the extent'; neither comment discloses any AI use."

My rubric read it that way because `ai-disclosure` is a required check that reads the repo-facts contribution policy, not the report's quality, and it treats every eval package as AI-assisted work. Ghostty's policy asks for disclosure of all AI usage in any form, and neither comment has a disclosure sentence, so the check fails however good the repro is. Under the verdict rule, any failed required check means reject. A rubric that only graded proof would have accepted this package and missed the whole disclosure category. The same check has to pass `pkg-07`, where p5.js requires disclosure and the claim includes one, and `pkg-09`, where fd's disclosure ask covers PRs only. The run passed both: for `pkg-09` it quoted "the policy states no disclosure ask for issue comments".

**Check rationale**

`ai-disclosure`, quoted from `rubric.md`:

> | ai-disclosure | The repo-facts contribution policy (AI policy) read against the claim comment and repro report text. Treat every candidate package as AI-assisted work. | Pass if the repo states no AI policy, a permissive policy, or a policy whose disclosure ask covers only pull requests (not issue comments); or if the policy requires disclosure for comments/issues/all AI usage AND the comments disclose it (naming the tool and the extent). A policy that asks comments to be in the contributor's own words passes unless the comment is plainly generic boilerplate. Fail only if the stated policy requires disclosing AI use in comments or in any form and neither comment discloses, however strong the reproduction is. | required |

It reads this way instead of the simpler rule, "fail if the repo has an AI policy and the comments don't disclose AI use." I rejected that rule after reading the policies in the set, because it would have wrongly failed several clear accepts. `pkg-05` (conda) is permissive with no disclosure ask. `pkg-09` (fd) asks for disclosure only in pull requests and says explicitly that it has no ask for issue comments. `pkg-03` (ripgrep) and `pkg-12` (prettier) have AI policies about quality and own-words comments, not disclosure. So the check has to read *what* the policy asks for and *where*, and fail only when the ask covers comments or "all AI usage in any form."

The "treat every candidate package as AI-assisted" clause is there because a grader can't detect AI use from the text. Without that premise, the grader could decide the ghostty comments were human-written and pass `pkg-20`. The own-words clause keeps `pkg-03` passing: ripgrep's rule is about voice, not disclosure, and that comment is specific and technical.

**Trade-offs**

The check gives up flexibility for predictability. Because it assumes every package is AI-assisted, it would also reject a genuinely hand-written comment on a disclosure-required repo that doesn't state "no AI was used". I accept that miss, because a grader can't tell the two apart from the text, and failing closed matches what the policy asks for.

The own-words clause is a second known blind spot. On a repo like ripgrep that forbids AI-written comments, it passes any comment that is specific and technical, even if an AI wrote it. Only obvious boilerplate would fail. No package in the set tests an AI-written but specific comment on an own-words repo, so this is a known gap rather than a confirmed miss.

Nothing else changed, and here is how I know: I made no revision after the graded run, so there was nothing to canary. The one run shows every single-package-sensitive category matched (`disclosure 1/1`), and the conditional-policy packages the check could over-trigger on (`pkg-03`, `pkg-05`, `pkg-07`, `pkg-09`, `pkg-12`) all came back `accept`, matching gold.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
