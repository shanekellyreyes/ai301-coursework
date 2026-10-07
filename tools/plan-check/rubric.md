# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis-grounded | `plan_markdown`'s stated `Cause:`, read against what `repro_evidence_markdown`'s Actual/Expected actually shows | Pass if the stated cause directly explains the specific behavior the repro evidence pins down — not an adjacent or plausible-sounding mechanism. Fail if the cause contradicts the repro evidence, or targets a different symptom than what was reproduced. Unclear if the repro evidence names no specific behavior to check against. | required |
| bounded-scope | `plan_markdown`'s `Change:` section, specifically its `In:` and `Out:` lines | Pass if `Out:` explicitly excludes at least one adjacent area a drive-by fix might be tempted to touch, and `In:` names a single, specific change. Fail if there is no `Out:` line, or `In:` describes multiple unrelated changes bundled together. | required |
| executable-by-a-stranger | `plan_markdown`'s `Change:` section's named files/functions and described approach | Pass if specific file paths (or function/method names) are named, OR a specific module/subsystem is named along with concrete evidence that the author has already traced the problem there (e.g., referencing debug output or logs that pinpoint it), with exact function names explicitly deferred to be confirmed while writing the fix rather than left unknown. Fail if files/areas are named only generically ("the relevant file") with no evidence of actual tracing, or the approach only restates the diagnosis without describing an action. | required |
| test-decisive | `plan_markdown`'s `Test:` section, read against `repro_evidence_markdown`'s steps | Pass if the test plan reuses or directly references the repro's steps and states a concrete, checkable expected result (a specific value, a specific observed state) after the fix. Fail if the test plan says only to "verify" or "check" without stating what passing looks like, or doesn't connect to the repro steps at all. | required |
| comment-faithful | `plan_comment_markdown`, read against `plan_markdown` and against `repo_facts.raw_markdown`'s contribution policy plus `thread_highlights` | Fail if the comment promises anything beyond what the plan actually contains (a timeline, a merge guarantee, a scope the plan's `In:` doesn't cover). Fail if the comment ignores a maintainer signal in `thread_highlights` that directly bears on the approach. Fail if the contribution policy contains explicit AI-disclosure language and the comment doesn't disclose. Pass otherwise. | required |

## Verdict rule

Accept if every required check passes. Reject if any required check fails or is unclear (unclear counts as fail). Preferred checks never change the verdict.
