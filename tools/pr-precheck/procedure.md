# Procedure: how this tool grades a PR package

## Read order

1. Determine the mode before grading.

2. In live mode, read `scope.md` first. Confirm that the target repository matches the filled `Repo:` line and that the scope rules apply. If the repository line still contains a placeholder, refuse to grade. Then read `voice-guide.md`.

3. In eval mode, treat the supplied package bundle as the complete world. Read only the bundle and the four inlined tool files. Do not fetch GitHub pages, inspect the local filesystem, or use facts outside the bundle.

4. Read the issue context and repository facts. Record the issue behavior, repository template requirements, contribution rules, and any AI-use policy.

5. Read the plan context before reading the diff. Record the plan's stated scope, named files or folders, intended behavior, test plan, and deviation notes.

6. Read the candidate PR title and description. Separate factual claims, issue references, stated changes, test claims, limitations, and disclosures.

7. Read the complete diff and commit list. In live mode, the branch diff is the working-copy result of `git diff main...HEAD`. In eval mode, use the frozen diff in the package.

8. Read the complete test-evidence block last. Record each command and the actual output it printed. Do not treat prose summaries or comments after a command as output.

## Evidence gathering

1. For plan fidelity, make a list of every changed file and meaningful behavior in the diff. Compare each item with the plan's named scope and deviation notes. Record any extra, missing, or changed work.

2. For test evidence, make a list of the plan's required tests and issue-reproduction checks. Match each one to a command and observable output in the test-evidence block. Record whether the output demonstrates the intended behavior, only a control path, or no meaningful result.

3. For diff quality, inspect every changed file and hunk. Record debug statements, dead code, generated artifacts, unrelated formatting, unexplained files, and hunks that are difficult to review.

4. For repository standards, compare the title and description with the repository facts, issue requirements, pull-request template, contribution policy, and AI-use policy. Record each satisfied ask and each missing or undisclosed ask.

5. For description claims, map each factual claim to supporting evidence in the diff, plan, or actual test output. Record claims that are unsupported, overstated, contradicted, or honestly limited.

6. Keep evidence for each check to the smallest decisive fact or quote. Never write “looks fine” without naming what was inspected.

## Check execution

1. Execute the checks in the exact order listed in `rubric.md`.

2. Grade the plan-fidelity check first. Assign `pass` only when the diff is within the plan or its clearly recorded deviations. Assign `fail` when the diff silently drifts beyond that boundary. Assign `unclear` when the package does not provide enough information to compare the diff with the plan.

3. Grade the tested-behavior check next. Assign `pass` only when commands and actual printed output demonstrate the required behavior and checks. Assign `fail` when tests are absent, only prose is supplied, output tests an unrelated control path, or the evidence does not test the issue behavior. Assign `unclear` when the evidence cannot be interpreted.

4. Grade the reviewable-diff check. Assign `pass` when the complete diff is focused and reviewable. Assign `fail` when unrelated or unexplained material hides the intended change. Assign `unclear` when the package omits enough of the diff to prevent review.

5. Grade the repository-standards check. Assign `pass` when every applicable repository ask is satisfied or an allowed limitation is clearly disclosed. Assign `fail` when a required template section, issue reference, policy requirement, or AI disclosure is missing. Assign `unclear` when the repository requirements are not available.

6. Grade the description-and-evidence check. Assign `pass` when the PR description is supported by the plan, diff, and test evidence. Assign `fail` when it overclaims or contradicts the package. Assign `unclear` when a claim cannot be verified.

7. Do not repair missing evidence by inference. If a procedure step is silent or a required source is absent, report the gap and grade the affected check `unclear`.

## Verdict assembly

1. Apply the verdict rule in `rubric.md` after all checks are graded.

2. Return `accept` only when every required check is `pass`.

3. Return `reject` if any required check is `fail` or `unclear`. Preferred checks never change the binary verdict.

4. Before the final JSON block, provide a concise per-check summary with the grade and decisive evidence.

5. If multiple required checks fail, identify the first failing required check in rubric order as the primary reason in the summary.

6. End the response with the required fenced JSON block. Use the PR URL in live mode or the bundle id in eval mode. Include one evidence line for every check. Do not write anything after the JSON block.