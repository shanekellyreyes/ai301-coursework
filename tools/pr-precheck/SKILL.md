---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer exactly one question for exactly one PR package:

> Is this pull request ready to submit?

A PR package contains the issue context, repository requirements, plan, candidate PR title and description, commits, diff, and test evidence. Read the candidate PR against the plan it claims to implement, the issue that plan belongs to, and the repository's stated standards.

Do not grade a different question, grade multiple packages at once, or decide from general impressions. Execute the rubric and procedure in this directory.

## Inputs and modes

Run in exactly one of these modes.

### Live mode

For a student's own submission, read:

- `plan.md`, including its scope, test plan, and deviation notes.
- The branch diff against the repository default branch. From the working copy, the relevant comparison is `git diff main...HEAD`.
- The draft PR title and description in `pr_draft.md`.
- The test and reproduction evidence in `test_evidence.md`.
- The issue thread, repository PR template, contribution rules, and stated policies from the scoped repository.

A house-chain student reads the house plan and house reproduction pack instead of their personal plan and reproduction files. Apply the same checks to the same categories of evidence.

### Eval mode

The supplied package bundle is the entire world. Read every required fact from the bundle text. Do not fetch GitHub, inspect another repository, use the local filesystem, or infer facts from outside the bundle. Grade the complete package and apply the full rubric and verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before all other package material.

Confirm that the PR targets the repository named by the filled `Repo:` line and follow the house rules stated in that file. If the repository line contains an unfilled placeholder, refuse to grade and tell the student to fill it with the correct Path Review repository.

Refuse to grade a PR outside the scoped repository. Do not guess the repository or silently substitute another one.

In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` after `scope.md`. Check the draft PR title and description against the writing rules in that guide.

Report any broken voice rule in the readable summary. The voice guide does not change the verdict by itself; it affects the verdict only when a rubric check explicitly evaluates that requirement.

In eval mode, ignore `voice-guide.md` entirely. Universal communication requirements are evaluated through the rubric and the repository facts in the package.

## Component reads

Read and execute these components:

- `rubric.md` defines the checks, evidence sources, pass conditions, weights, and verdict rule.
- `procedure.md` defines the order and method of grading.
- `references/evidence-guide.md` maps each evidence family to its source.
- `scope.md` and `voice-guide.md` apply only in live mode as described above.

If `rubric.md` has no student-written rubric content or `procedure.md` has no student-written procedure content, refuse to grade and explain which component is missing.

If the procedure is silent about a needed step, report that gap instead of inventing a new step. If evidence is absent or cannot be verified, grade the affected check `unclear`; the rubric's verdict rule determines how that grade affects the final verdict.

## Verdict and output

The verdict is binary:

- `accept` means the PR is ready to submit.
- `reject` means the student should hold the PR and fix or disclose the problem.

Provide a readable per-check summary before the final machine-readable block. Every check must include a decisive evidence line.

End the response with this valid fenced JSON block and nothing after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}