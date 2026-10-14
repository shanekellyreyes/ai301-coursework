# Evidence guide: where the deciding facts live

This guide maps each rubric check to the evidence source that decides it. In eval mode, use only the frozen package bundle. In live mode, use the named files, branch diff, and repository sources.

## Plan fidelity

Read the plan's `Files:` or scope section, intended behavior, test plan, and `## Deviations` section.

Compare those statements with:

- The changed-file list and every meaningful hunk in the diff.
- The branch diff produced by `git diff main...HEAD` in live mode.
- The frozen `Diff` and commit sections in eval mode.
- The PR description's summary and changes claims.

Deciding evidence is the exact plan line, deviation note, changed file, or diff hunk that shows whether the work stayed within scope. A file is within scope only when the plan names that file or its folder, or when the plan's documented deviation explicitly accounts for it. A description claiming exact plan fidelity does not override a diff that shows silent extra work.

## Test evidence

Read the plan's test plan and the candidate PR's complete test-evidence block.

For every claimed test or reproduction:

- Identify the command that was run.
- Identify the actual printed output from that command.
- Determine whether the output exercises the issue behavior, a relevant before-and-after comparison, or only an unrelated control path.
- Distinguish command output from prose, comments, expected-output notes, and claims that a test passed.

In live mode, also inspect the actual test and check results supplied by the student. In eval mode, use only the frozen `Test evidence` section. A test claim without a command and observable output is not proof.

## Diff quality

Read the complete diff and changed-file list, not only the PR summary.

Look for:

- Debug prints, temporary logging, commented-out experiments, and dead code.
- Generated files, local environment files, or unrelated artifacts.
- Unrelated formatting or refactoring.
- Unexplained changes outside the plan's boundary.
- Large or tangled hunks that make the intended behavior difficult to review.
- Tests that do not exercise the changed behavior.

The deciding evidence is the specific file path and diff hunk that makes the change focused and reviewable or makes it unreviewable. Do not fail a concise, focused diff merely because it has few lines.

## Standards and communication

Read the repository facts, issue or thread requirements, pull-request template, contribution policy, AI-use policy, PR title, and PR description.

Check for:

- The required issue reference or closing statement.
- The required template sections and meaningful content.
- Required test, reproduction, or review information.
- Required AI-use disclosure.
- A title that identifies the change clearly.
- Claims and limitations stated in the contributor's own voice.
- Honest disclosure of skipped checks, deviations, or unresolved limitations.

The deciding evidence is the exact repository rule, template request, title or description line, or disclosure that satisfies or violates the requirement. A shortfall may be acceptable when it is honestly disclosed and the repository rules do not require the missing work before submission. Silence about a required standard is not disclosure.