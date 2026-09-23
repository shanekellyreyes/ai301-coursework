# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/15

**Verdict output**

```text
❯ issue-select: grade these candidate first issues: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/73 https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53 https://github.com/codepath/pathreview-ai301-fa26-s1/issues/15

⏺ Skill(issue-select)

Successfully loaded skill

Read 3 files, ran 4 shell commands

Curl works with the GitHub API. Let me pull the repo-level facts and each issue's full data in parallel.

Ran 9 shell commands

Summary

All three candidates are from the scoped repo (codepath/pathreview-ai301-fa26-s1). Repo-level facts checked once and applied to all: not archived, last push 2026-09-16 (6 days before today, well within 90) → repo-in-use passes; no CONTRIBUTING.md/AI_POLICY.md/AGENTS.md says anything about AI-assisted contributions → policy check passes by silence; maintainer responsiveness sampled across issues with comment activity — most threads are student claim-comments with no reply, but issues #52 and #43 both got substantive closing comments from collaborator Aburke225 within ~6 days of opening, so the maintainer is demonstrably alive → passes.

Ranked (all accepted):

1. #15 — Agent session state not cleared between reviews — best fit: it's squarely in agent/orchestrator.py, the area you said you want to grow in (agentic workflows). Bounded single-root-cause fix (empty cache + clear session state), no assignee, no linked PR, no claims, 3–4h estimate gives more room to learn than the others.
2. #53 — PII scrubber misses parenthesized phone numbers — good fit, touches the AI-system safety layer without being deep security research; very clear repro steps and named failing tests. One loose end: a commit in an unrelated fork (Evin009/ai301-coursework) references this issue number, but it's not a linked PR against this repo and there's no claim in the thread, so it doesn't fail the "nobody's on it" check — just note it before you start.
3. #73 — README/.env.example disagree on LLM API key — accepted but lowest fit: pure docs/config bug, 1–2h, least aligned with your agentic/RAG interests, though it's the safest/quickest of the three.

[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/15",
    "checks": [
      {"name": "Maintainer is responsive", "grade": "pass", "evidence": "Collaborator Aburke225 posted substantive closing comments on #52 and #43 within ~6 days of those issues opening"},
      {"name": "Repo is in use", "grade": "pass", "evidence": "not archived; last push 2026-09-16, 6 days before capture (2026-09-22)"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "labels ['good first issue','agent','tier-1'], no design/epic/tracking labels; body describes one root cause (stale ContextManager) with one fix (empty cache + clear session state)"},
      {"name": "Nobody's already on it", "grade": "pass", "evidence": "assignees: [], no linked PRs, 0 comments"},
      {"name": "Contribution policy allows this workflow", "grade": "pass", "evidence": "no CONTRIBUTING.md/AI_POLICY.md/AGENTS.md mentions AI-assisted contributions"},
      {"name": "Issue has a clear description", "grade": "pass", "evidence": "names specific files: agent/orchestrator.py, agent/memory/session_store.py, agent/memory/context_manager.py"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53",
    "checks": [
      {"name": "Maintainer is responsive", "grade": "pass", "evidence": "same repo-wide sample as #15: Aburke225 replied substantively on #52/#43 within ~6 days"},
      {"name": "Repo is in use", "grade": "pass", "evidence": "not archived; last push 2026-09-16, 6 days before capture"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "labels ['bug','good first issue','safety','tier-1']; body is one bounded regex-pattern bug in pii_scrubber.py"},
      {"name": "Nobody's already on it", "grade": "pass", "evidence": "assignees: []; the only timeline event is a 'referenced' commit from an unrelated fork (Evin009/ai301-coursework), not a linked PR in this repo and no claim comment in the thread (0 comments)"},
      {"name": "Contribution policy allows this workflow", "grade": "pass", "evidence": "no CONTRIBUTING.md/AI_POLICY.md/AGENTS.md mentions AI-assisted contributions"},
      {"name": "Issue has a clear description", "grade": "pass", "evidence": "includes exact reproduction code and names the failing tests (test_us_phone_number_redaction, etc.)"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/73",
    "checks": [
      {"name": "Maintainer is responsive", "grade": "pass", "evidence": "same repo-wide sample: Aburke225 replied substantively on #52/#43 within ~6 days"},
      {"name": "Repo is in use", "grade": "pass", "evidence": "not archived; last push 2026-09-16, 6 days before capture"},
      {"name": "Scope fits a newcomer", "grade": "pass", "evidence": "labels ['bug','good first issue','docs','tier-1']; body asks to reconcile two files, a single bounded task"},
      {"name": "Nobody's already on it", "grade": "pass", "evidence": "assignees: [], no linked PRs, 0 comments"},
      {"name": "Contribution policy allows this workflow", "grade": "pass", "evidence": "no CONTRIBUTING.md/AI_POLICY.md/AGENTS.md mentions AI-assisted contributions"},
      {"name": "Issue has a clear description", "grade": "pass", "evidence": "names specific files: README.md and .env.example"}
    ],
    "verdict": "accept"
  }
]
```

---

## Eval iterations

**Run history**

- Smoke test (3 issues): 2/3 agreement
- Targeted re-check (`--only issue-01`, after first scope fix): 0/1 agreement
- Full run (20 issues), rubric v1: 12/20, category floor unmet (clear-accept 0/8)
- Full run, rubric v2 (maintainer + scope checks loosened): 17/20, category floor unmet (policy 0/1)
- Full run, rubric v3 (added contribution-policy check, tightened scope + claim checks): 19/20, bar passed
- Final confirmed run (`--save-run eval-run.txt`): 19/20, matches `eval-run.txt`

**Issue analysis**

For `issue-20`, my rubric returned `accept` while gold said `reject`. Gold's reasoning was that a small-looking feature request still hid an unspecified product decision. My rubric missed this because the required `Scope fits a newcomer` check looks for concrete scope problems — multiple deliverables, design/architecture labels, unresolved debate, or repeated abandoned PRs — and none of those were present. My `Issue has a clear description` check couldn't catch it either, since it's only a preferred check and can't change the verdict. This showed me an issue can look small and bounded while still being under-specified enough that a contributor would have to make a product or UX decision on their own.

**Check rationale**

> "Fail only if every sampled issue shows 'no maintainer comment in thread' AND at least one was opened more than 7 days before the bundle's capture date (enough time to have gotten a response). If every sampled issue was opened within 7 days of capture, there hasn't been enough time to judge fairly — pass by default. Unclear if `raw_markdown` has no 'maintainer first-response sample' line at all."

I wrote the check this way because my original 60%-response threshold was too strict. It produced false rejects on repos like conda, where some sampled issues were filed by maintainers themselves (no external response expected) or were too recent to reasonably expect a reply yet. I changed the check to fail only when the evidence consistently shows no maintainer response, and at least one sampled issue is old enough that a response would've been reasonable to expect — shifting it from an arbitrary percentage to actual evidence of an inactive maintainer.

**Trade-offs**

This check is intentionally forgiving: a repo can still pass with weak maintainer participation, as long as one sampled issue got any response at all. I accepted that risk because the stricter percentage rule was rejecting active repos over issues that were simply new or maintainer-authored. If I kept tuning, I'd re-run the conda cases as canaries to make sure I don't reintroduce those false rejects.

---

## Selection rationale

**Selection rationale**

I chose issue #15 because it matches what I said I want to grow in — agentic workflows. It touches `agent/orchestrator.py`, `agent/memory/session_store.py`, and `agent/memory/context_manager.py` directly. It's the biggest estimate of the three (3–4h vs. #53/#73), but the alignment with what I actually want to learn is worth the extra time.

The verdict confirmed it's one bounded root-cause fix (stale session/context state), not several unrelated changes, with named files and no assignees, open linked PRs, or active claims blocking it. What the rubric can't measure is how comfortable I actually am in that part of the codebase, or how long it'll really take me to understand how the orchestrator, session store, and context manager interact.

I expect the hardest part will be understanding the existing agent lifecycle before touching anything — tracing when a session starts, what gets cached, and where state is supposed to reset — so I don't fix the reported bug while breaking another part of the review flow.
