# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives: in an eval package, the plan's stated cause is the "Diagnosis" or "Cause" line of the "Candidate plan" section. The behavior that cause must explain is in the "Repro evidence" section: the numbered steps, the artifact or output, and the "Control runs" or "Control" lines. In live mode, the plan is the student's `plan.md`, and the repro evidence is their posted repro comment on the issue (or the house repro pack).

What good looks like: the stated cause accounts for every step and every control run. A control that behaves correctly with the blamed component still in the loop rules that cause out. The thread's guesses count only as far as the repro evidence supports them.

## Scope

Where it lives: the "Scope" lines of the candidate plan: the in-scope statement, the "not in scope" line, and the files or areas named in the approach. Compare them with the issue title and body.

What good looks like: one change the issue asks for, with named files and an explicit "not touching" line. A drive-by rewrite adds a refactor, migration, new option, or redesign the issue never asked for. Deferring a related symptom with a reason is still bounded.

## Executability

Where it lives: the "Approach" steps of the candidate plan: the files or areas, the chosen approach, the order of work.

What good looks like: a stranger could open the named file and start without asking the author anything. "Investigate", "somewhere", and "whichever is easier" mean a decision is still unmade.

## Test plan

Where it lives: the "Test plan" lines of the candidate plan, read against the numbered steps in the repro evidence.

What good looks like: it names a specific observable result, such as the repro steps re-run with the expected output, or a named test that fails before and passes after. "Run the full suite" and "should feel fast" name no outcome the fix must produce.

## Honesty

Where it lives: the "Risk" or "Unknowns" lines of the candidate plan, and any hedging in the plan comment. In live mode, also the `## Deviations` section of `plan.md`, where an honest mid-build change is recorded.

What good looks like: what the repro evidence confirms is stated as confirmed, and what is not known is stated as unknown, with what would settle it. Confident language over a gap is false confidence.

## Comms

Where it lives: the "Candidate plan comment" section, read against the "Thread highlights" section of the issue context and the repo-facts block's "contribution policy" and "bug reports" lines. In live mode, the draft `comment.md`, the live issue thread, and the repo's CONTRIBUTING.md, AI policy files, and templates.

What good looks like: the comment answers each explicit maintainer direction in the thread (follows it, or says why not), engages any open PR or prior art, and includes any AI-use disclosure the policy requires, naming the tool and the extent. Boilerplate that could be pasted on any issue is not thread-aware.
