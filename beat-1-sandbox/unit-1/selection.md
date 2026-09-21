# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72

**Verdict output**

```
All three issues are accepted. Rank is #72, then #5, then #20. All three are in the scoped repo,
codepath/pathreview-ai301-fa26-s3. I gathered the evidence through the public GitHub API, since gh isn't installed
here.

Repo-level checks (same for all three)
- maintainer-alive: pass. The last 5 commits are human-authored by Aburke225, the newest on 2026-09-16.
- repo-in-use: pass. The repo isn't archived and was last pushed 2026-09-16. It has no release, but the push is within
  90 days.
- ai-policy: pass. No CONTRIBUTING.md, AI_POLICY.md or AGENTS.md exists, and there's no PR template, so the policy is
  silent.
- maintainer-responsive (preferred): unclear. The recent issues have 0 or 1 comments, and I didn't check who wrote
  them.

Ranking
1. #72, verify_password raises UnknownHashError.
   - Scope: one bug in core/security.py, with the xfail test to un-mark, about 1–2 hours. It passes scope-bounded.
   - Claims: no assignee, no comments, no linked PR, so not-claimed passes.
   - Label: it carries good first issue, the only one of the three that does.
   - Fit: Python, small and well scoped, which is a good first reps pick, though it has no SQL angle.
2. #5, end-to-end ingestion test with a sample resume fixture.
   - Scope: one integration test, about 4–6 hours. It passes scope-bounded.
   - Claims: no assignee or comments. Its only cross-reference is #43, a closed issue rather than a PR, so not-claimed
     passes.
   - Label: none of good first issue, help wanted or easy.
   - Fit: Python with an embedding-storage path, which is the closest of the three to your database preference.
3. #20, DependencyAuditTool.
   - Scope: a new tool plus a hook in agent/orchestrator.py, about 5–8 hours. It passes scope-bounded, since there's
     no debate, no maintainer warning and no bot origin. It is the largest and least defined of the three.
   - Claims: no assignee or comments, so not-claimed passes.
   - Label: none of the friendly labels.
   - Fit: Python agent-tool work overlaps your Claude SDK experience but adds little new growth.

No issue is blocked by a claim. The Path Review house rule means claim comments wouldn't have counted anyway, and
there are none.

[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/72",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "Last 5 commits by human Aburke225, newest
2026-09-16"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "archived: false; pushed 2026-09-16 (no release, push
within 90 days)"},
      {"name": "not-claimed", "grade": "pass", "evidence": "assignees []; 0 comments; no linked/open PR"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "One bug in core/security.py; remove xfail marker in
test; est. 1-2 hours"},
      {"name": "ai-policy", "grade": "pass", "evidence": "No CONTRIBUTING/AI_POLICY/AGENTS/PR template found; policy
silent"},
      {"name": "maintainer-responsive", "grade": "unclear", "evidence": "Recent issues have 0-1 comments; maintainer
replies not verified (preferred, no effect on verdict)"},
      {"name": "labelled-friendly", "grade": "pass", "evidence": "Labels: bug, good first issue, api, tier-1"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/5",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "Last 5 commits by human Aburke225, newest
2026-09-16"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "archived: false; pushed 2026-09-16 (no release, push
within 90 days)"},
      {"name": "not-claimed", "grade": "pass", "evidence": "assignees []; 0 comments; only cross-reference is closed
issue #43, not an open PR"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "One integration test for
IngestionPipeline.ingest_resume; est. 4-6 hours"},
      {"name": "ai-policy", "grade": "pass", "evidence": "No CONTRIBUTING/AI_POLICY/AGENTS/PR template found; policy
silent"},
      {"name": "maintainer-responsive", "grade": "unclear", "evidence": "Recent issues have 0-1 comments; maintainer
replies not verified (preferred, no effect on verdict)"},
      {"name": "labelled-friendly", "grade": "fail", "evidence": "Labels: enhancement, ingestion, tests, tier-2 (no
good first issue/help wanted/easy)"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/20",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "Last 5 commits by human Aburke225, newest
2026-09-16"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "archived: false; pushed 2026-09-16 (no release, push
within 90 days)"},
      {"name": "not-claimed", "grade": "pass", "evidence": "assignees []; 0 comments; no linked/open PR"},
      {"name": "scope-bounded", "grade": "pass", "evidence": "One new tool (dependency_audit_tool.py) plus
orchestrator hook; no debate or bot origin; est. 5-8 hours"},
      {"name": "ai-policy", "grade": "pass", "evidence": "No CONTRIBUTING/AI_POLICY/AGENTS/PR template found; policy
silent"},
      {"name": "maintainer-responsive", "grade": "unclear", "evidence": "Recent issues have 0-1 comments; maintainer
replies not verified (preferred, no effect on verdict)"},
      {"name": "labelled-friendly", "grade": "fail", "evidence": "Labels: enhancement, agent, devops, tier-2 (no good
first issue/help wanted/easy)"}
    ],
    "verdict": "accept"
  }
]
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

1. Smoke test, `--limit 2`: 2/2 agreement.
2. Full run: 19/20 agreement. `issue-19` was the one miss — gold said `accept`, my rubric said `reject` on `scope-bounded`.
3. Targeted re-check after rewording `scope-bounded`, `--only issue-19,issue-05,issue-10,issue-15,issue-20`: 5/5 agreement (confirmed the fix caught issue-19 without loosening the check on the genuine scope rejects).
4. Final full run, `--save-run eval-run.txt`: **20/20 agreement**, category floor met in every category (claimed 4/4, clear-accept 8/8, dead-repo 3/3, policy 1/1, scope 4/4). This is the run committed in `eval-run.txt`.

**Issue analysis**

`issue-19` (zxcalc/zxlive#517). Gold label: `accept`, noted as "maintainer-diagnosed performance bug with named causes, unclaimed." My final rubric also lands on `accept`, agreeing with gold.

It didn't start that way. My first `scope-bounded` wording failed this issue as an umbrella, because the issue body lists two "potential causes" of a UI freeze and then three "additional suggestions" as a numbered list — which reads, on a skim, like a checklist of separate deliverables. But the issue is one bug (selecting large subgraphs freezes the UI) with a maintainer naming multiple possible causes of that single bug and multiple possible implementation approaches to fixing it, not multiple independent features meant to be split into separate PRs. That's the distinction the gold note is pointing at. I rewrote `scope-bounded` to say a numbered list of possible causes or approaches for one underlying bug doesn't fail the check, only a list of genuinely separate deliverables does, and re-running confirmed the fix without breaking the real umbrella rejects (`issue-05`, `issue-10`).

**Check rationale**

`scope-bounded`'s current wording, quoted from `rubric.md`:

> Fail if ANY of: it lists several independent deliverables meant to be split into separate PRs or tracked as separate sub-issues (an umbrella/tracking/megaissue); it is a support or usage question; a maintainer says it needs core-internals or architecture work; the thread shows the design is still being debated with no maintainer decision; the issue is auto-generated by a bot with no maintainer triage. A numbered list of possible CAUSES or implementation approaches for ONE underlying bug is not a checklist of separate deliverables and does NOT fail this check, even when phrased as "additional suggestions."

I wrote it this way because the eval set showed the check needs to tell apart two things that look identical in formatting — a numbered list — but mean opposite things for scope: separate deliverables (real umbrella, should reject) versus separate theories about one problem (still one bounded fix, should accept). Grading on formatting alone (the presence of a numbered list) would have caused exactly the miss on `issue-19` that my first draft made.

**Trade-offs**

The rewrite trades a formatting-based rule (numbered list → fail) for a judgment-based one (are the list items independent deliverables, or facets of one bug?), which is harder to apply consistently than a hard count. I canary-checked this with `--only issue-19,issue-05,issue-10,issue-15,issue-20`: the two genuine umbrellas (`issue-05`'s codebase-wide type-annotation sweep, `issue-10`'s self-described megaissue) still correctly reject, so the looser wording didn't leak on the cases I could check. What I accept it will still miss: an issue that frames several actually-independent features as "possible causes of one problem" to dress up as bounded — the check relies on the grader reading the issue's substance, and a well-disguised umbrella could still slip past it. None of the 20 eval issues tests that specific disguise, so this is a known blind spot rather than a confirmed one.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. **Fit and time.** #72 is a small, well-scoped Python bug (1–2 hours) in `core/security.py`. It isn't a Rust, JS, or SQL issue — none of the three accepted candidates were — but as a first PR into a codebase I've never touched, I'd rather start with something small and clearly Python (my strongest language) than stretch on unfamiliar tooling and an unfamiliar repo at the same time. The Rust/JS/SQL growth can come with later, better-scoped picks once I know the codebase.
2. **What the verdict got right vs. what I weighed beyond it.** The verdict correctly flagged it as unclaimed, bounded, and the only one of the three carrying `good first issue`. What it doesn't grade, but I weighed anyway: the issue already has a failing (`xfail`-marked) test for the exact bug, which means "done" is unambiguous — I can verify my fix myself by removing the marker and watching the test pass, without needing a maintainer's judgment call on a fuzzier acceptance criterion the way #5 or #20 would require.
3. **Anticipated difficulty in claiming.** Low. There's no assignee and no comments, and Path Review's house rule means even a classmate's claim comment wouldn't block me. The likely friction is entirely on the code side: tracing why `verify_password` raises `UnknownHashError` on a malformed hash instead of handling it, which is a small, self-contained fix to read and test, not a negotiation with a maintainer I haven't worked with before.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
