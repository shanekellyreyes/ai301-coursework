# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/202

**Branch**

fix/15-orchestrator-session-cache

## Eval iterations

**Run history**

- Partial smoke run: `agreement: 2/3 scored items`
- Partial smoke run after rubric revision: `agreement: 3/3 scored items`
- First full run: `agreement: 15/20 scored items (bar: 18/20: below the bar)`
- Final full run: `agreement: 18/20 scored items (bar: 18/20: PASS)`

**Package analysis**

I analyzed `pkg-02`. The gold label was `accept`, and the final rubric also returned `accept`. The package included a clear before-and-after reproduction showing the issue behavior changed, along with relevant test evidence. The revised Tested behavior check correctly allowed a complete labeled before-and-after transcript while continuing to reject packages that only reported a generic test-suite success or tested only an unchanged control path.

**Check rationale**

The final rubric's Tested behavior check says:

“Pass if the package contains a command/result transcript or test-run output that makes the issue behavior observable before and after the change. Labeled `before` and `after` results count when they form a clear, rerunnable transcript. A relevant targeted test command may support the result when its name or output clearly covers the changed behavior. Generic suite success alone, prose saying tests passed, or a test of only an unchanged control path is insufficient. An honest, evidenced inability to reproduce may pass when clearly disclosed.”

I revised this check after the first smoke run incorrectly rejected `pkg-02`, a clear-accept package with valid before-and-after output. The revised wording recognizes observable transcripts as evidence without accepting unsupported claims or generic test-suite output.

**Trade-offs**

The revised evidence rule improved agreement on clear-accept packages such as `pkg-02`, `pkg-05`, `pkg-08`, and `pkg-16`, while preserving rejection of not-tested packages such as `pkg-04`, `pkg-07`, `pkg-10`, and `pkg-14`. The final run reached the required `18/20` agreement and passed the category floor. Two clear-accept packages, `pkg-11` and `pkg-13`, remained false negatives because the tool favored conservative evidence grading; I accepted that trade-off rather than weakening the rule enough to admit unsupported test claims.

Related paths: `eval-run.txt` in this directory; the skill files are in `tools/pr-precheck/`.