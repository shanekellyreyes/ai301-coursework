# Procedure: how this skill grades a plan package

## Read order

1. Read `issue` (title, body_markdown, labels) first — this is what the plan claims to solve.
2. Read `repro_evidence_markdown` next — this is the ground truth the diagnosis must match. Note the specific Actual/Expected behavior stated.
3. Read `thread_highlights` — note any maintainer comment that constrains approach, scope, or process.
4. Read `repo_facts.raw_markdown` — note the contribution policy's stance on AI disclosure, and the bug-report template's asks.
5. Read `plan_markdown` — note its stated `Cause:`, `In:`/`Out:`, named files/approach, and `Test:` section separately.
6. Read `plan_comment_markdown` last, now that the plan's actual scope is known, so overpromising is checkable.

## Evidence gathering

- For diagnosis-grounded: record the repro evidence's Actual/Expected line verbatim, and the plan's `Cause:` line verbatim, side by side.
- For bounded-scope: record the plan's `In:` and `Out:` lines verbatim; note whether `Out:` is present at all.
- For executable-by-a-stranger: record every file/function path named in `Change:`.
- For test-decisive: record the plan's `Test:` section verbatim, and the repro's numbered steps, to compare directly.
- For comment-faithful: record every claim in `plan_comment_markdown` as a list, then check each against the plan's `In:` line, `thread_highlights`, and the contribution policy line.

## Check execution

Execute checks in this order: diagnosis-grounded, bounded-scope, executable-by-a-stranger, test-decisive, comment-faithful. Each check is graded from the evidence recorded in the gathering step, without re-reading the full package. If evidence for a check is genuinely absent from the package (e.g., no `Test:` section exists at all), grade that check `unclear`, not `fail` — the verdict rule converts `unclear` to a failing result at the verdict stage, not at this stage.

## Verdict assembly

Accept if all five checks pass. Reject if any check is `fail` or `unclear`. The output's deciding check is the first required check, in execution order, that did not pass — quote that check's recorded evidence (the verbatim lines from gathering) in the output, not a paraphrase.
