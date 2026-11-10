# Contribution: [Issue Title]

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** [Student name]
**GitHub Username:** [your GitHub username, exactly as it appears on your profile - no @, no
profile URL. Your comments upstream are identified by this name.]
**Issue:** [GitHub issue link]
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Built and tested the issue-scout**

I installed the Unit 5 scout over my Unit 1 skill, carried forward my rubric and evidence guide, and wrote search queries and ranking filters based on my fit profile. The first scan returned 300 results, 298 after deduplication; 248 passed the filters, and the scout accepted all five it graded. The results skewed toward same-day issues created by repository owners or collaborators, so I added a creation-date cutoff and a 90-day update filter; I have not rerun the scan with those changes yet. I also directly graded five issues from the selection site: OpenImageIO #4720 was accepted, AIBrix #3100 was accepted with a caveat because it asks for testing feedback rather than a code change, and three others were rejected because of existing claims or PRs and/or weak maintainer responsiveness.

**Unit 6: Installed the voice guard and wrote Caro repo memory**

The voice guard first could not read my relative draft path, then its Claude check failed because my OAuth session had expired; after logging in again, it passed and allowed my claim and reproduction comments to post. I also posted the reproduction report even though the Unit 6 instructions say to keep it for the PR description; I’ll record that mistake honestly and keep future reproduction evidence in the PR. After reading Caro’s `CLAUDE.md` and `CONTRIBUTING.md` and doing a focused search-index orientation, I created a 30-line `CLAUDE.local.md` and excluded it from Git.
**Unit 7: Installed ship-gate and configured the Caro manifest**

I installed ship-gate and configured a local manifest for dependency installation, website tests, the SEO type-check, and the local-database website build. On the first run, all four checks passed, but the gate stopped because the branch had no committed changes against `main`; unrelated project hooks also printed non-blocking missing-script errors. I added generation to `predev` and `prestart` because the generated index is ignored by Git; `prebuild` already generated it. After committing and pushing the fix, updating the plan and evidence, and correcting the PR draft and commit title, I reran the gate: all four checks and pr-precheck passed, and I opened PR #1759.
**Unit 8: Installed the PR monitor and wrote its playbook**

I installed the PR monitor and wrote rules for reviewer replies, nudges, and when to close a PR. The full merged-PR history search found only one eligible human-authored example, #1065, so my five-day nudge threshold is provisional rather than based on the requested five-PR sample. Running `monitor.py --state` on my feature branch resolved `wildcard/caro`, reported `NO PR YET`, and confirmed all four playbook sections are filled. It made no Claude call and drafted nothing; I need an open PR before the monitor can draft a move.
---

## Why I Chose This Issue

[1-2 paragraphs in your own words: how you searched, what your scan
surfaced, what your scout's verdict said, why this issue fits your
profile, and what you hope to learn. If your scout's verdict on it
was reject and you chose it anyway, say so here and say why; choosing
against your own tool silently is what loses points. Close with an
honest read of the tool itself: where the scout fell short on a real
wild search and what you changed or would change, or what convinced
you it held up. **Record the link to your claim comment in this section too.**
Not claimed yet? Write `claim pending` and today's date where the link goes,
and add the link the moment your claim lands.]

**Toolkit**

- Tool: issue-scout (your scout: your search strategy, your rubric),
  and how you used it
- What it produced: [the scan and its verdict on this issue, described]
- Difficulties: [where it fell short, or "none met" if none did]
- What changed: [what you patched or would update, or "nothing"]

---

## Understanding the Issue

**Problem Description**

Every page using OmniSearch includes the full search index in a `data-pages` attribute, even though the index is only needed for search.

**Expected Behavior**

Pages should avoid downloading and parsing the search index until someone opens search. On the first open, OmniSearch should fetch the static JSON index and reuse it for the session.

**Current Behavior**

The generator writes the index to `website/src/config/search-index.json`. `OmniSearch.astro` imports it, serializes it into `data-pages`, and parses it when the component initializes. My built homepage had a 1,589,980-byte `data-pages` attribute. The `open()` handler only opens the overlay and renders results.

**Affected Components**

`website/scripts/generate-search-index.mjs` generates the index; `website/src/components/OmniSearch.astro` embeds and reads it; `website/src/layouts/Layout.astro` and `website/src/layouts/LandingPage.astro` render OmniSearch. I found no `search-index.json` in `website/public`. `OmniMenu.astro` also contains an index attribute, but the active layouts do not import it.

**Toolkit**

- Tool: GitHub issue and terminal source inspection with `grep`, `git grep`, and `sed`.
- What it produced: A trace from the generator to the rendered attribute and the eager client-side parse.
- Difficulties: `git grep` also found `OmniMenu.astro`; checking the layouts showed it wasn’t in the active render path.
- What changed: I scoped the investigation to `OmniSearch.astro`; no code has changed yet.

---

## Reproduction Process

**Environment Setup**

macOS 26.6.2; Node v22.23.1; npm 10.9.8; Caro 1.5.0 at baseline commit `aa1b0e61` in my fork. Installed dependencies with `npm ci --no-audit --no-fund`; it completed with deprecation warnings. The website build completed with warnings.

**Steps to Reproduce**

1. From the repository root, run `npm ci --no-audit --no-fund`.
2. From the repository root, run `cd website && node scripts/extract-version.mjs && node scripts/generate-search-index.mjs && ASTRO_DATABASE_FILE="$(mktemp -t caro-build-db)" ASTRO_TELEMETRY_DISABLED=1 npx astro build`.
3. From the repository root, measure `website/dist/client/index.html` and its `data-pages` attribute with the Python command below.

   `python3 -c 'import pathlib,re; p=pathlib.Path("website/dist/client/index.html"); b=p.read_bytes(); m=re.search(rb"data-pages=\"([^\"]*)\"",b); print("HTML bytes:",len(b)); print("data-pages bytes:",len(m.group(1)) if m else "attribute not found")'`

   **Observed result:** HTML bytes: 1724865; data-pages bytes: 1589980.

**Reproduction Evidence**

- **Commit showing reproduction:** [Baseline commit `aa1b0e61` in my fork](https://github.com/shanekellyreyes/caro/commit/aa1b0e61)
- **Screenshots/logs:** Terminal output showed the Astro build completed and reported the byte counts above.
- **My findings:** The baseline homepage contains a 1,589,980-byte `data-pages` attribute in a 1,724,865-byte HTML file, about 92% of the file. This reproduces the inline-index behavior. The local attribute measurement is 34,490 bytes higher than the issue’s reported 1,555,490 bytes; I have recorded my run’s actual values.

**Toolkit**

- Tool: Terminal commands using npm, Astro, and Python for the reproduction and measurement; `repro-check` for grading the claim draft.
- What it produced: A successful baseline build and the measured HTML and `data-pages` sizes.
- Difficulties: The index-generation step changed tracked `website/src/config/search-index.json` before code changes.
- What changed: I restored that generated file and moved the build check to the manifest’s excluded table with the reason recorded.

---

## Solution Approach

**Analysis**

`website/scripts/generate-search-index.mjs` generated the full index at `website/src/config/search-index.json`. `OmniSearch.astro` imported that file, serialized the index into the `data-pages` attribute on every rendered page, and parsed it during client-side setup. The baseline homepage measurement showed that this attribute was 1,589,980 bytes.

**Proposed Solution**

Generate the index as `website/public/search-index.json` and fetch it when OmniSearch first opens. Reuse the fetched index for later opens, and keep the compact `PAGES_INDEX` available when the request fails. Keep the index content and schema, search ranking and fuzzy matching, and `OmniMenu.astro` unchanged.

**Implementation Plan**

1. Change `generate-search-index.mjs` to write the generated index to `website/public/search-index.json`.
2. Remove the full index import and `data-pages` attribute from `OmniSearch.astro`; load and reuse the index when search opens, with `PAGES_INDEX` as the fallback.
3. Add focused loader tests for deferred loading, reuse, and fallback behavior; verify the build output and search behavior.

**Deviations**

On 2026-11-09, I kept the generated public JSON out of Git by adding it to `website/.gitignore`. Because a fresh checkout will not contain this ignored generated file, I updated the website's `predev` and `prestart` scripts to generate it. `prebuild` already generated the index.

**Toolkit**

- Tool: Terminal source inspection and Git commands; Claude Code for implementation and test writing.
- What it produced: A trace from index generation through HTML embedding, plus the lazy loader, integration changes, and focused tests.
- Difficulties: Astro's `import.meta.glob` meant Node could not import `pages.ts` directly for a quick fallback-size measurement. I used a temporary adaptation for that measurement; the browser check later confirmed fallback results still appeared when the request was blocked.
- What changed: I moved the generated output to the public directory, ignored it in Git, added dev and start generation hooks, and covered the loader behavior with tests.

---

## Testing Strategy

I compared the built homepage before and after the change. On baseline commit `aa1b0e61`, `website/dist/client/index.html` was 1,724,865 bytes and its `data-pages` attribute was 1,589,980 bytes. After the change, I rebuilt the site and measured `website/dist/client/index.html` at 138,389 bytes; the `data-pages` attribute was absent. The build also produced `website/dist/client/search-index.json` at 1,174,299 bytes with 51 indexed pages. This measurement covers the homepage, not every page or locale.

The post-change build completed using a local Astro database. The website test suite passed: 17 files and 342 tests. `npx tsc -p tsconfig.seo.json`, run from `website/`, returned without errors or diagnostic output.

In Chrome DevTools, I confirmed there was no search-index request before opening OmniSearch, one successful request on first open, and no additional request after closing and reopening. When I blocked the request, fallback results still appeared. After removing the block, the request succeeded. Opening search from the mobile navigation also loaded the index. I also ran `npm run dev -- --help` and `npm start -- --help` from `website/`; both setup hooks generated the index and Astro exited after printing help. The local ship-gate reran all four manifest checks successfully, then stopped before precheck because the branch had no committed changes against `main`.

**Toolkit**

- Tool: Terminal build and measurement commands, the website's Vitest suite, TypeScript, and Chrome DevTools.
- What it produced: Before-and-after homepage measurements, 342 passing tests, a clean SEO type-check, a successful local-database build, and observed browser behavior.
- Difficulties: The standard website build uses `--remote`, which needs Astro Studio access. I ran the build with a local database, following the repository workflow's local-build approach.
- What changed: I used the local database build and verified the generated static index and lazy-loading behavior in the browser.

---

## Implementation Notes

**2026-11-09**

I implemented the fix on `feature/1564-lazy-load-search-index`. The generator now writes the index to `website/public/search-index.json`; OmniSearch fetches it on first open and reuses it. The loader prepares the compact `PAGES_INDEX` for the initial display and fallback. I added tests for deferred loading, reuse, and fallback. The generated public JSON is ignored by Git, and the website's `predev` and `prestart` scripts generate it on a fresh checkout.

**2026-11-09**

A direct Node import of `website/src/config/pages.ts` failed because it uses Astro's `import.meta.glob`, which Node does not provide. I used a temporary adaptation to estimate the compact fallback, then verified fallback results in the browser by blocking the index request. During baseline reproduction, index generation also changed the tracked `website/src/config/search-index.json`; I restored it, then changed the generator to write the ignored public file.

**Toolkit**

- Tool: Claude Code for implementation and test writing; Terminal for builds, tests, measurements, and Git; Chrome DevTools for browser checks.
- What it produced: A lazy-loading search index, focused tests, an ignored generated asset, and measured build and browser evidence.
- Difficulties: Astro-specific `import.meta.glob` prevented importing `pages.ts` directly in Node; generated index output also needed to stay out of the tracked source tree.
- What changed: I measured the fallback through a temporary adaptation, moved generated output to `website/public/`, added lifecycle generation for dev and start, and restored the tracked generated file before removing it from the implementation.

---

## Pull Request

**PR Link:** [PR #1759](https://github.com/wildcard/caro/pull/1759)

**PR Description:** The content of `~/caro/pr_draft.md` was posted unchanged.

**Maintainer Feedback:**

- 2026-11-10: CLA check passed after I signed. Vercel deploy checks await KADOSH DEV Team authorization; no human maintainer review yet.

**Status:** Open. CLA passed; GitHub reports 9 successful checks, 6 Vercel authorization failures, and 2 skipped checks.

**Toolkit**

- Tool: Ship-gate and PR monitor.
- What it produced: The ship-gate passed all four manifest checks and pr-precheck. PR #1759 opened with the draft body unchanged. The CLA check passed after signing; six Vercel deploy checks await team authorization.
- Difficulties: The generated JSON deletion initially obscured the review diff, so I added a path-specific `-diff` rule. The PR checks still need Vercel team authorization; GitHub did not list the website test workflow, though the local ship-gate ran it successfully.
- What changed: I updated the plan and evidence, corrected the PR draft and commit title, reran the gate to PASS, opened PR #1759, and signed the CLA.

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
