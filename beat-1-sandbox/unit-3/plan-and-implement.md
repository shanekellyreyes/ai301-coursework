# # Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced

`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the

repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong

label is not graded.

---

## Posted upstream

**GitHub username**

shanekellyreyes

**Plan comment**

[https://github.com/codepath/pathreview-ai301-fa26-s1/issues/15#issuecomment-6030239846](https://github.com/codepath/pathreview-ai301-fa26-s1/issues/15#issuecomment-6030239846)

Traced the stale-state bug to two separate mechanisms, both on the same long-lived `Orchestrator` instance. First: `_execute_tool()` checks `ContextManager`'s in-memory cache before executing a tool, so a second review with unchanged input gets the first review's cached result instead of a fresh execution — `repro_issue15.py` confirms this directly, with `tool.call_count` staying at 1 across two `run()` calls. Second: `Orchestrator.run()` loads `session_state` from Redis via `session_store.get(profile_id)`, merges new results into it, and writes the merge back — so any key from a prior review that the current one doesn't overwrite persists in Redis indefinitely. That matches the issue's own wording: "empty that cache and delete the profile's saved session state."

Plan: add a `reset()` method to `ContextManager` and call it, plus `session_store.delete(profile_id)`, at the top of `Orchestrator.run()` — clearing both at the same point, using `SessionStore`'s existing `delete()` method rather than new logic. Scoped to `agent/orchestrator.py` and `agent/memory/context_manager.py` only; not touching `SessionStore`'s connection handling, TTL, or `getset` methods themselves.

Test plan: `repro_issue15.py` for the `ContextManager` half (before: identical cached result both reviews; after: a fresh one). Adding a new test in `tests/unit/test_orchestrator.py` for the `SessionStore` half, confirming a second review's stored state doesn't retain keys from a first review it didn't itself produce — no test file currently exists for `Orchestrator`.

One open risk I'm still verifying: whether `session_store.delete()` is safe to call unconditionally when no prior session exists for a given `profile_id` — expected to be a no-op on Redis's end, but I want to confirm against the actual implementation before relying on it. Will report back once I've tested both halves.

---

## Your branch

**Branch**

fix/15-orchestrator-session-cache

**Evidence**

Before the fix, I ran:

```bash

.venv/bin/python repro_[issue15.py](http://issue15.py)

```

Output:

```text

=== Review 1 ===

{'call_number': 1, 'stars': 10}

=== Review 2 (same orchestrator instance, same input) ===

{'call_number': 1, 'stars': 10}

Underlying tool.execute() was actually called 1 time(s)

```

After implementing the fix, I ran the same reproduction again:

```bash

.venv/bin/python repro_[issue15.py](http://issue15.py)

```

Output:

```text

=== Review 1 ===

{'call_number': 1, 'stars': 10}

=== Review 2 (same orchestrator instance, same input) ===

{'call_number': 2, 'stars': 20}

Underlying tool.execute() was actually called 2 time(s)

```

I also ran the new targeted unit tests:

```bash

.venv/bin/python -m pytest tests/unit/test_[orchestrator.py](http://orchestrator.py) -v

```

Output:

```text

tests/unit/test_[orchestrator.py](http://orchestrator.py)::TestContextManagerReset::test_reset_clears_cached_results PASSED

tests/unit/test_[orchestrator.py](http://orchestrator.py)::TestContextManagerReset::test_orchestrator_run_does_not_reuse_cache_across_runs PASSED

tests/unit/test_[orchestrator.py](http://orchestrator.py)::TestSessionStoreNotRetainedAcrossRuns::test_stale_keys_from_a_prior_review_are_not_retained PASSED

3 passed in 0.11s

```

Finally, I ran the full test suite:

```bash

.venv/bin/python -m pytest tests/ -q

```

Output:

```text

378 passed, 53 xfailed, 1 warning in 4.71s

```

The 53 xfailed tests were pre-existing expected failures and were not introduced by this change.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these

fields.

**Run history**

- Smoke test (3 packages), rubric v1: 3/3 agreement

- Full run (20 packages), rubric v1: 19/20, every category matched (clear-accept 6/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4)

- Targeted re-check `--only pkg-14,pkg-10`, after revising `executable-by-a-stranger`): 2/2 agreement

- Final confirmed run `--save-run eval-run.txt`): 20/20, bar passed, every category matched

**Package analysis**

On `pkg-14`, my first full run returned `reject` while the gold label was `accept`. The deciding check was `executable-by-a-stranger`. My original version of that check required the plan to name exact file paths or function names, so it treated the plan as not specific enough to build from.

After reviewing the package, I realized that requirement was too strict. The plan had already traced the problem to a specific part of `zellij-server`'s client connection handling and referenced concrete tracing evidence, even though it had not pinned the issue to an exact function yet. That was still enough information for someone to begin the implementation without guessing blindly.

I revised `executable-by-a-stranger` so that a plan can also pass when it names a specific module or subsystem, provides concrete evidence that the problem has already been traced there, and explicitly leaves the exact function name to be confirmed during implementation. After that change, I re-ran `pkg-14` along with `pkg-10` as a canary, and both matched their gold labels.

**Check rationale**

> Pass if specific file paths (or function/method names) are named, OR a specific module/subsystem is named along with concrete evidence that the author has already traced the problem there (e.g., referencing debug output or logs that pinpoint it), with exact function names explicitly deferred to be confirmed while writing the fix rather than left unknown. Fail if files/areas are named only generically("the relevant file") with no evidence of actual tracing, or the approach only restates the diagnosis without describing an action.

I wrote the check this way because my original version treated exact file or function names as the only way for a plan to be executable. `pkg-14` showed me that this can reject a plan that is actually specific enough to act on. If someone has already traced the problem to a concrete subsystem and has evidence showing why that area is responsible, not knowing the final function name yet does not necessarily make the plan unbuildable.

At the same time, I did not want to loosen the check enough that vague plans could pass. That is why the revised version still fails plans that only say things like "the relevant file" or name a broad area without showing any evidence that the contributor actually traced the problem there.

**Trade-offs**

The trade-off is that the rubric can judge what tracing evidence the plan describes, but it cannot independently prove that the contributor actually performed that tracing. A plan could claim that debug output or logs narrowed the problem to a subsystem even if that work was not really done.

I accepted that risk because requiring exact file and function names in every case produced a false reject on `pkg-14`. I also re-ran `pkg-10` as a canary after loosening the check, and it still produced the expected result. That gave me evidence that the change was narrow rather than making the executability check generally easier to pass.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in

`tools/plan-check/`.