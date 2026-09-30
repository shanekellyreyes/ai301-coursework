# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: `repro_report_markdown`'s stated Environment line, checked against any version details named inline in `issue.body_markdown` (there is no separate structured environment field — it's inline prose in both places).
What good looks like: concrete version numbers actually used (not "latest" or "standard setup"), checked against what the issue names — a stated, explained mismatch is fine; an unstated one isn't.

## Steps

Where it lives: `repro_report_markdown`'s steps/transcript section, read independently of the issue's own stated steps.
What good looks like: a named starting point (fresh clone, specific commands to set up state) and each subsequent command a stranger could run verbatim — an actual transcript, not a description of one.

## Behavior shown

Where it lives: the pasted output/transcript inside `repro_report_markdown`.
What good looks like: the transcript contains the issue's specific symptom verbatim or near-verbatim (same silent failure, same wrong value, same missing output) — not a paraphrase of it, not a nearby-but-different failure.

## Honesty

Where it lives: `repro_report_markdown`'s own stated Expected vs. Actual (or explicit conclusion), compared against its own transcript in the same document.
What good looks like: the claim matches the evidence exactly — including a specific, evidenced "I could not reproduce this, here's what I tried and what happened instead," which is a full pass, not a partial one.

## Comms

Where it lives: `claim_comment_markdown` and `repro_report_markdown`, checked against `repo_facts.raw_markdown`'s "bug reports: template asks for" line and its "contribution policy" line.
What good looks like: a required disclosure (per the contribution policy) is actually present in the comment text; a field the bug-report template explicitly asks for is actually filled in. Tone and length don't matter here — only unmet stated requirements do.
