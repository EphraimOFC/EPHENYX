# EPHENYX — verified SEO/indexing notes

## Source audit

The current homepage source contains:

- A descriptive title and meta description.
- Canonical URL: `https://ephraimofc.github.io/EPHENYX/`.
- `robots=index,follow`, Open Graph title/description/url/type and a Person + WebSite JSON-LD graph.
- A permissive `robots.txt` that references `sitemap.xml`.
- A sitemap with the single homepage URL. This is accurate while the portfolio remains a one-page site.
- Project cards that are rendered from `data.json`; the audit branch adds static HTML fallback cards for Cloud and API, so core project information exists before JavaScript runs.
- Google verification HTML file in the repository. Presence of the file is not proof that Google has verified the property.

## Improvements in the current review branch

- Add a favicon and a Twitter summary card.
- Preconnect to the Google Fonts origins and use a stylesheet link rather than CSS `@import`.
- Keep project descriptions/links present in static HTML and dynamic data.
- Point the EPHENYX API card to its actual public repository.

## Indexing observation

A web search for the official site/EPHENYX query did not return the site in the results shown. This is an observation from the available search engine, not a definitive Google indexing report. Google Search Console is authoritative.

## Still to verify in Search Console

1. Confirm the property is verified and the sitemap is submitted.
2. Inspect the canonical homepage and request indexing if needed.
3. Check Pages/Indexing, crawl issues, mobile usability and impressions after data accumulates.
4. Consider dedicated, crawlable project pages only when there is substantial unique content to publish.

Official tools: https://search.google.com/search-console/ and https://developers.google.com/search/docs
