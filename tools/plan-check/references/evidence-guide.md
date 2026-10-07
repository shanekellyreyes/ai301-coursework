# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives: `plan_markdown`'s `Cause:` line, checked against `repro_evidence_markdown`'s stated Actual/Expected behavior.
What good looks like: the cause is stated specifically enough that it predicts the exact symptom the repro evidence shows — not a generic "something is stale" but naming the actual mechanism (e.g., "view X isn't refreshed after event Y").

## Scope

Where it lives: `plan_markdown`'s `Change:` section, its `In:` and `Out:` lines.
What good looks like: `In:` names one specific change; `Out:` names something real that could plausibly be touched while making that change, and explicitly excludes it.

## Executability

Where it lives: `plan_markdown`'s `Change:` section — the named files/functions and the described approach.
What good looks like: real file paths or function names, plus a description of the actual edit, specific enough that a stranger doesn't need to ask "where" or "how."

## Test plan

Where it lives: `plan_markdown`'s `Test:` section, read against `repro_evidence_markdown`'s numbered steps.
What good looks like: the test plan references the same steps used to reproduce the bug, and states a concrete expected outcome (not just "confirm it works") after the fix is applied.

## Honesty

Where it lives: `plan_markdown`'s `Change:`/`Test:` sections for any stated risks or unknowns, and the language used there.
What good looks like: a genuine unknown is flagged as such ("may also affect X, untested"), rather than presented with unwarranted confidence.

## Comms

Where it lives: `plan_comment_markdown`, read against `plan_markdown`'s actual scope, `thread_highlights` (maintainer comments), and `repo_facts.raw_markdown`'s contribution policy line.
What good looks like: the comment promises only what the plan's `In:` line covers, explicitly acknowledges any maintainer constraint already stated in the thread, and discloses AI assistance only where the policy actually requires it.
