# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

---

## Claim comment

**Link:** https://github.com/codepath/pathreview-ai301-fa26-s1/issues/15#issuecomment-5901665146

**Text:**

I'd like to take this one. I'll start by reproducing the stale session-state behavior between reviews, then trace how state moves through `agent/orchestrator.py`, `agent/memory/session_store.py`, and `agent/memory/context_manager.py`.

I'll report back with the environment, reproduction steps, and what I actually observe before making any assumptions about the root cause.

## Repro comment

**Link:** https://github.com/codepath/pathreview-ai301-fa26-s1/issues/15#issuecomment-5902844298

**Text:**

# Reproduction report — Issue #15

**Issue:** https://github.com/codepath/pathreview-ai301-fa26-s1/issues/15  
**Issue title:** Agent session state not cleared between reviews

## Outcome

I reproduced the reported stale session-state behavior.

Using the same Orchestrator instance for two separate reviews with the same tool input, the first review produced a cache miss and executed the tool normally. The second review produced a cache hit and returned the first review's result instead of executing the underlying tool again.

The underlying test tool's execute method was called only once across both reviews. This confirms the reported behavior that cached tool results can carry over between separate reviews handled by the same orchestrator instance.

This reproduction exercises the Orchestrator directly with a deterministic fake tool. It does not claim that I reproduced the issue through the live web UI.

## Environment

- OS: macOS 26.6.2
- Repository commit: f89c06fc3ff292df2a04a39ac51319d32a76b779
- Python: Python 3.14.4
- Repository: https://github.com/shanekellyreyes/pathreview-ai301-fa26-s1
- Reproduction command: .venv/bin/python repro_issue15.py

## Setup

I forked the Path Review repository, cloned my fork locally, and set up the project environment using the repository's setup instructions.

The reproduction itself uses the project's virtual environment and a small deterministic test script so that the tool execution count and returned value can be observed directly.

## Reproduction steps

1. Start from the repository root.
2. Use the project's virtual environment.
3. Run the reproduction script:

~~~bash
.venv/bin/python repro_issue15.py
~~~

4. The script runs two reviews through the same Orchestrator instance using the same tool input.
5. Compare the cache behavior and returned value between Review 1 and Review 2.
6. Check how many times the underlying tool's execute method was actually called.

## Expected behavior

Each review should begin with fresh review-session state.

For the second review, the underlying tool should execute again rather than automatically reusing a cached result from the first review.

With the deterministic test tool, I would therefore expect the second execution to return a new result, such as call_number 2, and the tool's total call count to become 2.

## Actual behavior

Review 1 produced a cache miss and executed the tool:

~~~text
tool_result_cache_miss
tool_result_stored
{'call_number': 1, 'stars': 10}
~~~

Review 2, using the same Orchestrator instance and same input, produced a cache hit:

~~~text
tool_result_cache_hit
tool_cache_hit
{'call_number': 1, 'stars': 10}
~~~

The underlying tool was still called only once:

~~~text
Underlying tool.execute() was actually called 1 time(s)
~~~

Instead of executing the tool again for Review 2, the orchestrator reused the cached result from Review 1.

## Full observed output

~~~text
=== Review 1 ===
2026-09-29 18:59:57 [info     ] orchestrator_start  profile_id=profile-1
2026-09-29 18:59:57 [info     ] plan_built  plan_size=2
2026-09-29 18:59:57 [info     ] tool_result_cache_miss  key=github_tool:fb0a645052d1cccebfaa991c72525dc9647c6173f48dbf416c710579cccdfc7f tool=github_tool
2026-09-29 18:59:57 [info     ] tool_result_stored  key=github_tool:fb0a645052d1cccebfaa991c72525dc9647c6173f48dbf416c710579cccdfc7f tool=github_tool
2026-09-29 18:59:57 [info     ] tool_executed  success=True tool=github_tool
2026-09-29 18:59:57 [error    ] tool_execution_failed  error='Unknown tool: market_analyzer' tool=market_analyzer
2026-09-29 18:59:57 [info     ] orchestrator_complete  profile_id=profile-1 tools_executed=2
{'call_number': 1, 'stars': 10}

=== Review 2 (same orchestrator instance, same input) ===
2026-09-29 18:59:57 [info     ] orchestrator_start  profile_id=profile-1
2026-09-29 18:59:57 [info     ] plan_built  plan_size=2
2026-09-29 18:59:57 [info     ] tool_result_cache_hit  key=github_tool:fb0a645052d1cccebfaa991c72525dc9647c6173f48dbf416c710579cccdfc7f tool=github_tool
2026-09-29 18:59:57 [info     ] tool_cache_hit  tool=github_tool
2026-09-29 18:59:57 [info     ] tool_executed  success=True tool=github_tool
2026-09-29 18:59:57 [error    ] tool_execution_failed  error='Unknown tool: market_analyzer' tool=market_analyzer
2026-09-29 18:59:57 [info     ] orchestrator_complete  profile_id=profile-1 tools_executed=2
{'call_number': 1, 'stars': 10}

Underlying tool.execute() was actually called 1 time(s)
~~~

The market_analyzer errors above come from the minimal reproduction harness not registering that additional tool. They are separate from the github_tool cache behavior being tested here.

## Reproduction script

The exact script I used is included below so another contributor can rerun the same reproduction.

~~~python
import sys
sys.path.insert(0, ".")
from agent.orchestrator import Orchestrator


class FakeResult:
    def __init__(self, data):
        self.data = data


class FakeGithubTool:
    """Simulates a tool whose real-world answer changes between calls,
    the way a live GitHub API response could change between two reviews."""

    name = "github_tool"

    def __init__(self):
        self.call_count = 0

    def execute(self, tool_input):
        self.call_count += 1
        return FakeResult({"call_number": self.call_count, "stars": 10 * self.call_count})


tool = FakeGithubTool()
orchestrator = Orchestrator(tools={"github_tool": tool})

profile_data = {
    "github_username": "shanekellyreyes",
    "projects": [{"github_repo": "some-repo"}],
}

print("=== Review 1 ===")
result1 = orchestrator.run("profile-1", profile_data)
print(result1["tool_results"]["github_tool"])

print("=== Review 2 (same orchestrator instance, same input) ===")
result2 = orchestrator.run("profile-1", profile_data)
print(result2["tool_results"]["github_tool"])

print(f"\nUnderlying tool.execute() was actually called {tool.call_count} time(s)")
~~~

## Interpretation

The evidence shows that review-specific tool state is surviving across two calls to the same Orchestrator instance.

The strongest evidence is:

1. Review 1 logs a cache miss.
2. Review 2 logs a cache hit for the same tool/input key.
3. Both reviews return the same call_number 1 result.
4. The underlying tool reports that execute() was called only once.

This reproduces the stale-result behavior described in issue #15.

I have not made or tested a fix as part of this reproduction, and I am not treating any specific reset location as confirmed until I trace and test the relevant state-management path.

---

## Eval iterations

**Run history**

- Smoke test (3 packages), rubric v1: 1/3 agreement
- Targeted re-check (`--only pkg-01,pkg-03`, after Environment + Conventions fix): 2/2 agreement
- Full run (20 packages), rubric v1: 14/20, category floor unmet (disclosure 0/1)
- Targeted re-check (`--only pkg-03,pkg-05,pkg-12,pkg-20`, after conventions/disclosure fix): 3/4 agreement
- Targeted re-check (`--only pkg-09,pkg-10,pkg-05`, after Behavior/cannot-reproduce fix): 3/3 agreement
- Full run (20 packages), rubric v2: 19/20, bar passed, all category floors met
- Final confirmed run (`--save-run eval-run.txt`): 20/20, matches `eval-run.txt`

**Package analysis**

On `pkg-20`, my first full run returned `accept` while the gold label was `reject`. The repo's policy required AI use to be disclosed, but the comment did not include that disclosure. My earlier rubric did not treat a missing required disclosure as its own blocking convention, so it let the package through.

I changed the conventions check so that an explicit AI-disclosure requirement fails when the comment does not actually disclose the AI usage. After that change, `pkg-20` returned `reject`, matching the gold label. This showed me that a repo mentioning or allowing AI is different from a repo specifically requiring contributors to disclose AI assistance.

**Check rationale**

> Fail only if the contribution policy contains explicit disclosure language (e.g., "must be disclosed," "must state the tool used," "disclose AI assistance") — a real requirement to name AI usage or its extent in the comment — AND the comment includes no such disclosure. Do NOT fail for policies that merely welcome AI use, ask contributors to review/understand AI-generated content, ask contributors to explain their changes, or warn that low-quality AI content will be rejected — none of those require disclosure in the comment itself. Fail if the comment itself states or implies it was AI-generated in a repo whose policy requires human-authored comments. Also fail if the comments skip a field the bug-report template explicitly asks for.

I wrote the check this way because my earlier version was too broad around AI-related policy language. A repo can allow AI while still asking contributors to review their work, understand generated code, or explain their changes, and none of those automatically mean the contributor has to disclose AI usage. Narrowing the fail condition to explicit disclosure language let the check catch the actual disclosure requirement without rejecting comments just because a repo mentions AI in its contribution policy.

**Trade-offs**

This check is intentionally conservative. A repo with a real disclosure requirement could still slip through if it phrases that rule in unusual wording that does not look like the explicit disclosure language I anchored on. I accepted that risk because broadening the check to any AI-related policy language would create more false rejects. If I tuned it again, I would add new disclosure wording patterns only when I had evidence from real repository policies that the current check was missing them.
