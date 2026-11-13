I opened a pull request to improve how Caro's website loads its search feature. My change downloads the full search data when someone opens search, while keeping basic results available if loading fails.

In my local builds, the homepage HTML dropped from about 1.72 MB to 138 KB, approximately 92% smaller. That measures the page's HTML size, not how much faster the website loads.

I used AI assistance to investigate and implement the change, then checked the builds, measurements, tests, and automated review feedback. Review caught missing fallback pages and unclear descriptions, which I corrected. The full website suite passed 343 tests.

The pull request is still open and has not been merged. The work taught me to verify the failure path as carefully as the main feature and to back up performance claims with measurements.

https://github.com/wildcard/caro/pull/1759
