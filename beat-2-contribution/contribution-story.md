# Contribution: perf(website): OmniSearch inlines a 1.55 MB search index into every page

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Shane Kelly Reyes
**GitHub Username:** shanekellyreyes
**Issue:** https://github.com/wildcard/caro/issues/1564
**Pull Request:** https://github.com/wildcard/caro/pull/1759

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

**Unit 8: Used the PR monitor and corrected its review-response draft**

My playbook covers reviewer replies, staleness thresholds, nudge etiquette, and walk-away criteria; its five-day nudge threshold remains provisional because my merged-PR search found only one eligible human-authored example, #1065, rather than the requested five. On 2026-11-10, the full monitor run detected Cubic's third review round and drafted a response to three fallback-description comments. The draft incorrectly described the existing metadata test as only checking routes and assumed the docs pages exported `searchMeta`, so I checked the actual tests, page descriptions, and README before using it. I pushed the three description fixes in `81854cee`, replaced the monitor's draft with a corrected reply, and [posted it through Claude Code](https://github.com/wildcard/caro/pull/1759#issuecomment-6766175310).

---

## Why I Chose This Issue

I found Caro #1564 by using an AI agent to scan the issues page and look for issues suitable for a first contribution. I chose it because it looked recent, appeared to need triage, and described a specific performance problem I could investigate. The oversized search index gave me a concrete starting point: trace how the index reached the browser, build the website, and measure the resulting HTML. My learning goal was to understand generated assets, lazy loading, and fallback behavior through a real contribution.

My recollection is that the scout accepted this issue, but I have not verified the saved verdict, so I am recording that as a recollection rather than a confirmed result. The earlier broad scout scan accepted all five issues it graded and skewed toward same-day issues opened by owners or collaborators, which showed me that its results still needed manual review. I added a creation-date cutoff and a 90-day update filter, but I have not rerun that scan with those changes. The agent helped me find candidates; checking the issue and reproducing the problem gave me stronger evidence for proceeding.

**Claim comment:** [My claim on Caro #1564](https://github.com/wildcard/caro/issues/1564#issuecomment-6717799627)

**Toolkit**

- Tool: An AI agent to scan the issues page and check first-contribution fit, alongside my Unit 5 issue-scout for evaluating candidates.
- What it produced: Caro #1564 as a contribution candidate; I then checked the issue and posted a claim under `shanekellyreyes`.
- Difficulties: The broad scout results favored very recent owner- or collaborator-created issues, and I have not verified the saved verdict for this specific issue.
- What changed: I added a creation-date cutoff and a 90-day update filter, and kept the distinction between my remembered scout verdict and the evidence I verified directly.

---

## Understanding the Issue

**Problem Description**

Every page using OmniSearch includes the full search index in a `data-pages` attribute, even though the index is only needed for search.

**Expected Behavior**

Pages should avoid downloading and parsing the search index until someone opens search. On the first open, OmniSearch should fetch the static JSON index and reuse it for the session.

**Current Behavior**

At baseline commit `aa1b0e61`, the generator wrote the index to `website/src/config/search-index.json`. `OmniSearch.astro` imported it, serialized it into `data-pages`, and parsed it when the component initialized. My built homepage had a 1,589,980-byte `data-pages` attribute. At baseline, the `open()` handler only opened the overlay and rendered results.

**Affected Components**

`website/scripts/generate-search-index.mjs` generates the index; `website/src/components/OmniSearch.astro` embeds and reads it; `website/src/layouts/Layout.astro` and `website/src/layouts/LandingPage.astro` render OmniSearch. I found no `search-index.json` in `website/public`. `OmniMenu.astro` also contains an index attribute, but the active layouts do not import it.

**Toolkit**

- Tool: GitHub issue and terminal source inspection with `grep`, `git grep`, and `sed`.
- What it produced: A trace from the generator to the rendered attribute and the eager client-side parse.
- Difficulties: `git grep` also found `OmniMenu.astro`; checking the layouts showed it wasn’t in the active render path.
- What changed: I scoped the investigation to `OmniSearch.astro`; no implementation changes had been made at that investigation stage.

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
- What changed: During baseline reproduction, I restored the generated file and recorded why the build check was excluded at that stage. The final manifest includes the local-database build after the generated output moved out of tracked source.

---

## Solution Approach

**Analysis**

At baseline commit `aa1b0e61`, `generateSearchIndex()` in `website/scripts/generate-search-index.mjs` wrote the full index into the source configuration directory. `website/src/components/OmniSearch.astro` imported it, serialized it into `data-pages`, and parsed it during initialization. The measured attribute occupied 1,589,980 bytes of the homepage's 1,724,865-byte HTML file.

**Proposed Solution**

Generate a public JSON asset and fetch it when search opens. Reuse a successful result and retain a compact, client-safe fallback when loading fails. Preserve the full index's content and schema, search ranking, fuzzy matching, and `OmniMenu.astro`.

**Implementation Plan**

1. Change `generateSearchIndex()` in `website/scripts/generate-search-index.mjs` to write `website/public/search-index.json`.
2. In `website/src/components/OmniSearch.astro`, remove the full-index import and `data-pages` attribute. Use `loadIndex()` from `open()` and the mobile trigger's `omni-search:load` event.
3. Put preparation, shared in-flight loading, successful-result caching, retry, and fallback behavior in `createSearchIndexLoader()` and `prepareSearchPages()` in `website/src/lib/search-index-loader.ts`.
4. Export the client-safe `STATIC_PAGES_INDEX` from `website/src/config/pages-static.ts`. Keep the eager page metadata glob and the `buildPageEntries()`/`getEntry()` merge in `website/src/config/pages.ts`.
5. Ignore the public generated JSON in `website/.gitignore`, remove tracked `website/src/config/search-index.json`, and generate the public asset through the website package's development and build hooks.
6. Cover loader behavior, client wiring, and metadata consistency in `website/src/lib/search-index-loader.test.ts`; compare fallback paths with the current static page routes.
7. Run the manifest checks, inspect the build output, and check first-open loading, reuse, and fallback in the browser.

**Deviations**

The implementation needed a separate loader module and client-safe fallback module so browser code would not import the eager Astro page glob. I added generation hooks for `predev`, `prestart`, and `preastro`; `prebuild` already generated the index. I also added a path-specific `.gitattributes` `-diff` rule so the large generated JSON deletion would not obscure the source changes.

Review feedback led me to expand the fallback to all 51 current static routes, synchronize entries with seven page `searchMeta` exports, and assert both ends of the mobile loading event. A later review caught poor descriptions copied from generated snippets. I replaced the security and Kavana descriptions with their page metadata and checked the supported shell wording against the README.

**Toolkit**

- Tool: Terminal source inspection and Git, with Claude Code and ChatGPT assisting implementation, debugging, and test changes.
- What it produced: A traced cause, a public generated asset, a reusable lazy loader, a client-safe fallback, and focused tests.
- Difficulties: The eager Astro page glob was unsuitable for the browser fallback, and generated text snippets were not always useful descriptions.
- What changed: I separated browser-safe data from the page glob, expanded route coverage, and checked questionable descriptions against their actual page sources.

---

## Testing Strategy

The first observable check was the built homepage payload. In the recorded before-and-after run, baseline commit `aa1b0e61` produced 1,724,865 bytes of HTML with a 1,589,980-byte `data-pages` attribute. After the lazy-loading change, the measured homepage was 138,389 bytes and the attribute was absent. The generated `website/dist/client/search-index.json` contained 51 pages and measured 1,174,299 bytes. These measurements cover the homepage, not every page or locale.

The second observable check was browser request behavior. In Chrome DevTools, no index request appeared before search opened; the first open requested the index, and closing and reopening did not add another request. Blocking the request still produced fallback results, and removing the block allowed a retry. Opening search through mobile navigation also loaded the index.

The latest full website suite passed 17 files and 343 tests. The focused loader file passed 10 tests, covering deferred loading, reuse, concurrency, fallback, retry after an empty response, preparation, and source wiring. The mobile assertions check the event dispatch and listener in source; they do not simulate a browser click. The metadata test compares fallback titles, descriptions, and keywords with seven page `searchMeta` exports. A separate route comparison found 51 static page routes and 51 fallback entries, with no missing or extra routes.

The SEO TypeScript check returned without diagnostics. Ship-gate passed all four manifest checks and PR precheck on the final description-fix commit, `81854cee`, including dependency installation, the website tests, the SEO type-check, and the local-database website build. Earlier gate attempts exposed uncommitted branch changes and incomplete draft evidence; I corrected those before the successful runs.

I used a local Astro database because the standard remote build requires Astro Studio access. Captured build output included a Vite dynamic-import warning and localized route-priority warnings. Without a baseline warning log, I cannot say whether those warnings were new.

**Toolkit**

- Tool: npm, Astro, Python measurements, Vitest, TypeScript, Chrome DevTools, and ship-gate.
- What it produced: Before-and-after payload measurements, observed request behavior, 343 passing tests, matching route sets, a clean SEO type-check, and a passing gate.
- Difficulties: The remote build required service access, and the precheck evaluated committed branch contents while my updated files were initially uncommitted.
- What changed: I used the local database build, committed the reviewed changes before rerunning precheck, and kept measured results separate from unverified claims.

---

## Implementation Notes

**2026-11-09**

I implemented the fix on `feature/1564-lazy-load-search-index`. The generator now writes the index to `website/public/search-index.json`; OmniSearch fetches it on first open and reuses it. The loader prepares the compact `PAGES_INDEX` for the initial display and fallback. I added tests for deferred loading, reuse, and fallback. The generated public JSON is ignored by Git, and the website's `predev` and `prestart` scripts generate it on a fresh checkout.

**2026-11-09**

A direct Node import of `website/src/config/pages.ts` failed because it uses Astro's `import.meta.glob`, which Node does not provide. I used a temporary adaptation to estimate the compact fallback, then verified fallback results in the browser by blocking the index request. During baseline reproduction, index generation also changed the tracked `website/src/config/search-index.json`; I restored it, then changed the generator to write the ignored public file.

**2026-11-10 — Review-feedback changes**

I pushed `5953f4eb` to separate the client-safe fallback from the eager page glob and address the first review-feedback changes. In `99a75a54`, I expanded the fallback to all 51 current static routes, aligned the affected `searchMeta` values, and added mobile event assertions and a metadata consistency test. A route comparison found no missing or extra paths.

**2026-11-10 — Description fixes and reviewer reply**

Cubic then identified a truncated security description, a section label used as Kavana's summary, and incomplete shell-support wording. I checked the actual page descriptions and README, corrected the three strings, and pushed `81854cee`. Ship-gate passed all four manifest checks and PR precheck on that commit.

The PR monitor drafted a response, but it incorrectly described the existing test and assumed metadata exports that were not present. I replaced those claims with the verified changes and checks, then [posted the corrected reply through Claude Code](https://github.com/wildcard/caro/pull/1759#issuecomment-6766175310).

**Toolkit**

- Tool: Claude Code and ChatGPT for implementation assistance and draft review; Terminal for source inspection, tests, builds, measurements, and Git; Chrome DevTools for browser checks.
- What it produced: The lazy-loading fix, review-feedback commits, 343 passing tests, a passing gate, and a posted reviewer response.
- Difficulties: Astro-specific imports complicated the fallback, generated snippets supplied poor descriptions, and the monitor proposed claims that did not match the code.
- What changed: I separated client-safe data, verified descriptions against their source pages, and replaced the monitor's unsupported claims before posting.

---

## Pull Request

**PR Link:** [PR #1759](https://github.com/wildcard/caro/pull/1759)

**PR Description:** The description names issue #1564, explains the lazy-loading change and compact fallback, and records reproduction measurements, browser checks, test results, and limitations. I updated it after the review-feedback changes; it now records 343 passing tests.

**Maintainer Feedback:**

- **2026-11-10 — Feedback received:** Cubic, an automated reviewer, flagged missing mobile-trigger assertions, differences between fallback entries and page `searchMeta`, and missing fallback routes.
- **2026-11-10 — My response:** I pushed `99a75a54`, adding the mobile event assertions, synchronizing the affected metadata, and expanding the fallback to all 51 current static routes. The route comparison found no missing or extra routes, the full suite passed 343 tests, and ship-gate passed.
- **2026-11-10 — Further feedback received:** Cubic flagged a truncated `/docs/security` description, a section label used as the `/docs/kavana` summary, and `/docs` wording that omitted supported Windows shells.
- **2026-11-10 — My response:** I checked the page descriptions and README, pushed the three corrections in `81854cee`, and reran ship-gate successfully. I then [posted a reply explaining the fixes and checks](https://github.com/wildcard/caro/pull/1759#issuecomment-6766175310).
- **2026-11-10 — Other status:** No human maintainer review was recorded in the checks I inspected. The CLA passed; the six failing Vercel checks reported team authorization requirements.

**Status:** Awaiting review


**Toolkit**

- Tool: Ship-gate and PR monitor.
- What it produced: The ship-gate passed all four manifest checks and pr-precheck. PR #1759 opened with the draft body unchanged. The CLA check passed after signing; six Vercel deploy checks await team authorization.
- Difficulties: The generated JSON deletion initially obscured the review diff, so I added a path-specific `-diff` rule. The PR checks still need Vercel team authorization; GitHub did not list the website test workflow, though the local ship-gate ran it successfully.
- What changed: I updated the plan and evidence, corrected the PR draft and commit title, reran the gate to PASS, opened PR #1759, and signed the CLA.

---

## Learnings and Reflections

**Technical Skills Gained**

I learned to trace a generated asset from its build script into rendered HTML and browser initialization. Moving the search index to a public file changed when the browser downloaded it, while the loader handled reuse, concurrent requests, retries, and fallback results. I also learned why a browser-safe fallback needs to stay separate from an eager Astro page glob.

**Challenges Overcome**

The standard remote build required Astro Studio access, so I used a local database build. Generated output also changed a tracked file during reproduction, which required care to keep the baseline and implementation changes separate. Later, review exposed missing fallback routes and poor generated descriptions; I checked route coverage and page metadata instead of assuming the generated snippets were suitable.

The gate and monitor needed review too. The gate caught a mismatch between the PR draft and uncommitted branch contents. The monitor drafted statements about tests and metadata exports that were inaccurate, so I checked the source before posting a corrected reply.

**What I'd Do Differently Next Time**

I would save the baseline build warnings and the scout's exact verdict for the selected issue, so I could compare results without relying on memory. I would check route coverage and fallback metadata earlier, before asking for review. I would also validate each concrete claim in an AI-generated reply against the code or terminal output and document a limited maintainer-history sample before treating a nudge threshold as established.

---

## Resources Used

- [Caro issue #1564](https://github.com/wildcard/caro/issues/1564): supplied the reported inline-index problem and the suggested direction of serving a static JSON asset.
- [Caro's CLAUDE.md](https://github.com/wildcard/caro/blob/main/CLAUDE.md) and [CONTRIBUTING.md](https://github.com/wildcard/caro/blob/main/CONTRIBUTING.md): informed my repository orientation, local repo memory, and contribution workflow.
- [Caro's README at the description-fix commit](https://github.com/wildcard/caro/blob/81854cee/README.md): confirmed the implemented target shells, including PowerShell and cmd.
- [Security page source](https://github.com/wildcard/caro/blob/81854cee/website/src/pages/docs/security.astro) and [Kavana page source](https://github.com/wildcard/caro/blob/81854cee/website/src/pages/docs/kavana.astro): supplied the actual descriptions used to correct the fallback entries.
- [CodePath Unit 8 materials](https://github.com/codepath/ai301-unit8-starter) and the assignment instructions: explained installing the PR monitor, writing its playbook, reviewing its draft, and posting through Claude Code.
- [PR #1759's review thread](https://github.com/wildcard/caro/pull/1759): identified concrete fallback, metadata, and mobile-wiring problems that I checked and addressed.
