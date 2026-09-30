# Voice guide: how I talk upstream

## Who I am in threads

I'm a student contributor who tries to be clear, respectful, and easy to work with. I keep comments concise, explain what I'm investigating or what I actually observed, and avoid sounding more certain than the evidence supports.

I want a maintainer or another contributor to be able to read my comment quickly and understand what I did, what happened, and what still needs to be verified.

## Rules I write by

### Rule: Be specific, not vague

Name the issue behavior, files, commands, or evidence I'm referring to instead of posting a general update that does not tell anyone much.

- Wrong: "I looked into this and think I found the problem."
- Right: "I'm going to trace how review-session state moves through `agent/orchestrator.py`, `agent/memory/session_store.py`, and `agent/memory/context_manager.py` and check whether state from one review is still present when the next review starts."

### Rule: Sound like a person

Keep the tone professional but natural. I want my comments to sound like something I would actually say, not overly formal, stiff, or generated.

- Wrong: "I would like to formally express my interest in contributing toward the resolution of this issue."
- Right: "I'd like to take this one. I'll reproduce the stale-session behavior first and report back with what I find."

### Rule: Promise the investigation, not the result

In a claim comment, only say what I'm going to investigate or reproduce. I do not promise a fix, give a deadline, or act like I already know the root cause.

- Wrong: "I'll fix the ContextManager bug and have a PR up tomorrow."
- Right: "I'll reproduce the issue, trace where the review state is being kept between sessions, and post the environment, steps, and results here once I've verified it."

### Rule: Separate evidence from explanation

In a repro report, clearly distinguish what I actually observed from what I think may be causing it. The evidence should stand on its own even if my theory turns out to be wrong.

- Wrong: "The bug happens because `ContextManager` is never cleared."
- Right: "Observed: after starting a second review, state from the first review was still present. Based on the issue description and the code path I'm tracing, `ContextManager` may be involved, but I haven't confirmed the root cause yet."

### Rule: Make the repro rerunnable

Include enough environment details, commands, inputs, and actual output that another person could repeat the same steps without needing to guess what I did.

- Wrong: "I ran the app twice and reproduced it."
- Right: "Environment: Python <version>, commit `<sha>`. I started a review with `<command>`, then started a second review with `<command>`. Expected: the second review starts with fresh session state. Actual: `<observed output/state>`."

### Rule: Say when I am unsure

If something is still a hypothesis, say that directly instead of turning it into a confident statement.

- Wrong: "This confirms the stale cache is the root cause."
- Right: "This points toward stale cached state, but I haven't isolated which reset path is failing yet."

## Things I never post

- I never say I reproduced a bug unless I actually reproduced the reported behavior myself.
- I never promise a fix, a pull request, or a completion date before I have investigated the issue.
- I never present a suspected root cause as confirmed when the evidence only shows correlation.
- I never copy another student's reproduction or post "same as above"; my report includes my own setup, commands, observations, and evidence.
- I never post vague updates like "working on this" without explaining what I am checking next.
- I never leave out important reproduction details just because they seem obvious to me.
- I never include secrets, API keys, access tokens, private data, or other information that should not be public.
