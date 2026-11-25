# EPHENYX — SEO and indexing notes

## Current public site

The official site is hosted on GitHub Pages:

- Homepage: https://ephraimofc.github.io/EPHENYX/
- EPHENYX OFC: https://ephraimofc.github.io/EPHENYX/ofc.html
- EPHENYX PRO: https://ephraimofc.github.io/EPHENYX/pro.html
- EPHENYX AI: https://ephraimofc.github.io/EPHENYX/ai.html
- EPHENYX CLOUD: https://ephraimofc.github.io/EPHENYX/cloud.html
- EPHENYX API: https://ephraimofc.github.io/EPHENYX/api.html

Each project profile has a unique title and description, canonical URL, Open Graph metadata and JSON-LD WebPage data. Homepage project cards and README navigation link to the project profiles. The sitemap lists the homepage and all five profiles.

## Source audit

The homepage and project pages include:

- Descriptive titles and meta descriptions.
- Canonical URLs and `robots=index,follow`.
- Open Graph title, description and URL; Twitter summary cards.
- Person + WebSite JSON-LD on the homepage and WebPage JSON-LD on each project profile.
- A permissive `robots.txt` that references `sitemap.xml`.
- A sitemap containing the six canonical public pages, with `lastmod` based on the latest content commit for each page (2026-11-24).
- Static HTML project-card content, with editable descriptions and destinations in `data.json`.
- A favicon and preconnect hints for Google Fonts.

Social previews currently lack a dedicated `og:image` / `twitter:image`. Add a stable, appropriately sized preview asset in a future visual pass; no design asset is being changed in this SEO-only update.

The Google verification HTML file is present in the repository. Its presence is not proof that Google has verified the property.

## Indexing status — 2026-11-25

- The owner previously reported that Google Search Console property verification, sitemap submission and an indexing request were completed. This status has not been independently checked in Search Console during this audit.
- A public site-specific search did not surface results in this check. That is not definitive evidence that Google has not indexed the site.
- Sitemap `lastmod` dates are set to the latest source commit date for the pages, not the date of this audit.

## Accuracy and trust

The project pages distinguish active development from deployed services. EPHENYX AI, EPHENYX CLOUD runtime hosting and EPHENYX API are not described as live production services until deployment and production checks are complete. The pairing portal is not advertised as available while the current prototype's credential-handling risks remain unresolved.

## Indexing actions requiring account access

1. In Google Search Console, review the sitemap status and Pages/Indexing report.
2. Inspect the homepage and each project profile; request indexing if the URL is eligible and not already indexed.
3. Review crawl issues and search performance as Google gathers data.
4. Set up Bing Webmaster Tools and submit the same sitemap.

Official resources:

- https://search.google.com/search-console/
- https://developers.google.com/search/docs
- https://www.bing.com/webmasters/

## Limits

A crawlable site, sitemap and structured metadata make the content easier for search engines to understand; they do not guarantee indexing, rankings, knowledge-panel inclusion or recommendations by Google, Meta AI or other assistants. Accurate public information, consistent official profiles, useful original documentation and a stable custom domain can strengthen discoverability over time.
