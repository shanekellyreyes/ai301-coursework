# Rubric: is this pull request ready to submit?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Plan fidelity / silent drift | The plan's stated scope, named files, intended behavior, deviation notes, and the branch diff read against `main` | Pass if every changed file and behavior is inside the plan's stated boundary or an explicitly documented deviation. Fail if the diff silently adds, removes, or changes behavior beyond the plan. An honestly disclosed deviation may pass when the PR clearly explains it. | required |
| Tested behavior / not tested | The plan's test plan, the PR's test-evidence section, and every command/result transcript | Pass if the package contains a command/result transcript or test-run output that makes the issue behavior observable before and after the change. Labeled `before` and `after` results count when they form a clear, rerunnable transcript. A relevant targeted test command may support the result when its name or output clearly covers the changed behavior. Generic suite success alone, prose saying tests passed, or a test of only an unchanged control path is insufficient. An honest, evidenced inability to reproduce may pass when clearly disclosed. | required |
| Reviewable diff / unreviewable | The complete diff, changed-file list, and commit contents | Pass if the diff is focused, understandable, and directly related to the issue and plan. Fail for debug output, dead code, unexplained generated files, unrelated formatting churn, or unrelated hunks that make the intended change difficult to review. | required |
| Repository standards / standards wall | Repo facts, issue or thread requirements, pull-request template asks, contribution policy, title, and description | Pass if every applicable repository and template requirement is addressed, including required issue references and AI-use disclosures. A clearly disclosed limitation is acceptable when the repository rules permit it. | required |
| Description and evidence claims | The PR description compared as a whole with the plan, diff, test evidence, and repository facts | Pass if the description is consistent with the package as a whole and its test or result claims have corresponding evidence. The description does not need to repeat every output line. Fail when it claims unrun tests, claims behavior contradicted by the diff or evidence, silently omits a material deviation, or presents unsupported success. | required |

## Verdict rule

Return `accept` only when every required check passes.

Return `reject` when any required check fails or is `unclear`. An unverifiable claim is not ready to submit. Preferred checks, if any are added later, may provide feedback but never change the binary verdict.

The output must contain one evidence line for every check. When multiple required checks fail, identify the first failing required check in rubric order as the primary reason for the verdict.