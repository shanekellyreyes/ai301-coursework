# Loading Caro's Search Data Only When It Is Needed

## Overview

For my CodePath AI301 contribution, I worked on Caro's website search. [Issue #1564](https://github.com/wildcard/caro/issues/1564) reported that every page included the full search index before visitors used search. I opened [PR #1759](https://github.com/wildcard/caro/pull/1759) to move that data into a separate file and load it when search opens. I used Claude Code for implementation and debugging assistance, and I ran builds, checked measurements, reviewed changes, and verified feedback against the source. In my local builds, the homepage HTML became approximately 92% smaller. The pull request is still open and has not been merged.

## What happened

### Finding and reproducing the problem

I used an AI agent to scan issues and find possible first contributions. This issue stood out because it was recent, looked manageable, and had a result I could measure. I wanted to understand the problem myself before treating the suggested fix as correct.

I traced the search-index generator and OmniSearch component. The component imported the full index, serialized it into a `data-pages` HTML attribute, and parsed it during initialization. Visitors received the search data as part of the initial page, even if they never opened search.

I built baseline commit `aa1b0e61` and measured the generated homepage:

```text
HTML bytes: 1724865
data-pages bytes: 1589980
```

The search attribute accounted for about 92% of the homepage HTML. This reproduced the reported payload problem on the homepage. I did not measure every page or locale.

### Changing how search loads

I changed the generator to write the full index to `website/public/search-index.json`. OmniSearch now requests that file when the search overlay opens.

The loader in `website/src/lib/search-index-loader.ts` prepares the data, shares an in-flight request between callers, and reuses a successful result. If loading fails, search uses fallback results and can try again later.

The fallback needed its own import boundary. `pages.ts` eagerly imports Astro page modules, so the browser uses the plain fallback list in `pages-static.ts` instead. This keeps fallback results available without importing that page-module graph. I also added generation hooks for development commands so a fresh checkout can create the ignored public index.

### Revising the fix after review

Cubic's automated review identified a problem with the fallback:

> STATIC_PAGES_INDEX omits many current public routes

That meant a failed full-index request could remove documentation, product, and other pages from search results. I added 21 entries, bringing the fallback to all 51 current static routes. The route comparison returned:

```text
Page routes: 51; fallback entries: 51
Missing: []
Extra: []
```

I added assertions for the mobile loading event and a consistency test comparing fallback metadata with the seven pages exporting `searchMeta`.

My submission check also caught a workflow mistake. The updated description claimed changes that were still uncommitted. Local tests passing did not mean those changes were part of the submitted branch. I committed the files and reran the gate successfully.

A later review found three description problems: the security summary was cut off, the Kavana summary was a section label, and the documentation summary omitted supported Windows shells. I checked the page descriptions and README, then corrected the entries in commit `81854cee`.

### Checking the result

The changed homepage measurement was:

```text
HTML bytes: 138389
data-pages bytes: attribute not found
```

Compared with the baseline, that removed 1,586,476 bytes from the generated homepage HTML, approximately 92%. The full index still exists as a separate asset and is requested when search opens.

The full website suite passed:

```text
Test Files  17 passed (17)
     Tests  343 passed (343)
```

Those are the totals for the website suite, not 343 tests I wrote. The focused tests cover deferred loading, reuse, concurrent calls, fallback, retry behavior, data preparation, and source wiring.

In Chrome DevTools, I checked that there was no index request before opening search, one request on first open, and no additional request after closing and reopening. Blocking the request still showed fallback results. Removing the block allowed a successful retry, and the mobile opening path also loaded the index.

After the final corrections, all four configured ship-gate checks passed: dependency installation, website tests, the SEO type-check, and the local-database website build. PR precheck passed too. I pushed the changes and posted a reply explaining the revisions.

The pull request is still open and has not been merged. The smaller local HTML, passing checks, and pushed corrections are the results I can demonstrate. They do not establish that a production deployment occurred.

## Limitations and what's next

I measured generated homepage HTML, not compressed network traffic, loading time, memory usage, or every page. A 92% reduction in this measurement does not mean the website became 92% faster. Visitors who open search still download the full index.

Some wiring tests inspect source text instead of running a browser. My next improvement would be an automated built-page or browser test that checks for the missing inline payload and verifies the first-open request, reuse, and fallback behavior.

The static fallback also requires maintenance. A shared metadata pipeline could reduce duplicate descriptions and prevent the drift caught during review. This work taught me to check the failure path carefully and make sure the committed branch, test evidence, and PR description agree.
