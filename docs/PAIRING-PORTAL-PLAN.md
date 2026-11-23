# EPHENYX Pairing Portal — Product and Security Plan

## Purpose

Build an official, public EPHENYX web presence where visitors can learn about the ecosystem and securely pair an EPHENYX OFC or EPHENYX PRO WhatsApp bot. The experience should feel like a polished product site, not a generic bot script page.

This document is a delivery specification. It does not claim that pairing is live.

## Public experience

- Official EPHENYX landing page with clear product identity and links to OFC, PRO, AI, CLOUD, API and support.
- Dedicated, crawlable product pages for EPHENYX OFC and EPHENYX PRO with genuinely distinct descriptions, features, setup guides, release notes and FAQs.
- Pairing hub with separate OFC and PRO choices.
- Each bot pairing flow offers WhatsApp phone-number pairing code and QR pairing where supported.
- Clear steps: choose bot, enter international phone number, request code, open WhatsApp > Linked Devices > Link with phone number, enter the one-time code.
- Show progress, expiry, retry guidance and useful errors without exposing internal logs.
- Mobile-first, accessible, fast, responsive interface with EPHENYX-native visual design.
- Public docs for commands, setup, privacy, security, support and status.

## Pairing service architecture

The browser must never hold or receive a long-lived WhatsApp session credential.

1. The browser requests a short-lived pairing job from an authenticated, rate-limited pairing API.
2. The service validates and normalizes the phone number, creates an unpredictable job ID and starts an isolated Baileys pairing worker.
3. The browser polls or subscribes to the job status using a short-lived, scoped token.
4. Only the one-time pairing code or temporary QR is returned to the requesting browser session.
5. The worker stores credentials in encrypted, private session storage accessible only to the intended bot runtime. Credentials are never sent as a creds.json attachment, returned by an API, written to public logs or placed in a public repository.
6. Pairing jobs expire and clean up incomplete state. Completed sessions are handed to the correct OFC/PRO runtime through a private, authenticated channel.
7. The service enforces per-IP, per-number and global concurrency limits, CSRF/origin protections where applicable, request size limits, bot-specific authorization and abuse monitoring.

Pairing codes and QR payloads are sensitive. Do not persist them longer than required; never include them in analytics, application logs, URLs, screenshots or error reports.

## Security findings from the existing EPHENYX-OFC-PAIR prototype

The current prototype is useful as a reference for the user flow only. It must not be exposed publicly as-is.

- The pair handler constructs a filesystem path from the supplied phone number and removes that path before validating/normalizing the number. This creates a path-manipulation risk.
- It sends the full `creds.json` session credential to the WhatsApp account as a document. Session credentials grant account access and must never be delivered this way.
- It logs the pairing number and pairing code.
- It starts socket/session work per request without robust global concurrency, per-number abuse controls or durable job ownership.
- The QR handler also sends the session credential file after pairing.
- The current HTML inserts API error text into `innerHTML`; untrusted error strings must be rendered as text, not HTML.
- The current prototype has no demonstrated encrypted session handoff into the correct bot runtime.

Rebuild these flows around isolated jobs and private credential handoff. Do not simply reskin or redeploy the existing prototype.

## GitHub and Cloud deployment

- Pairing portal source must be deployable from a connected GitHub repository and branch through EPHENYX CLOUD.
- Build from a pinned commit; record repository, branch, commit SHA, build ID and deployment history.
- Store provider tokens and application secrets only in Cloud's encrypted, write-only environment settings.
- Build and runtime workloads must be isolated from the Cloud control plane.
- Provide deploy, redeploy, stop, restart, logs, health checks and rollback only after the runtime safety gates pass.
- The pairing API and the long-running WhatsApp bot runtime are separate services with separate credentials and resource limits.

## Domain and routing

Use one official domain when acquired/configured, with a clean structure such as:

- `www.<official-domain>` — brand homepage.
- `pair.<official-domain>` — pairing hub.
- `docs.<official-domain>` — guides and command reference, if a separate docs host is useful.
- `status.<official-domain>` — operational status when monitoring is real.

These are proposed hostnames, not claims that a domain is owned or configured. A custom domain is optional for initial development; the current GitHub Pages site can remain the public home until DNS and TLS are configured.

## Search and public recognition

- Give every important product and documentation page a unique, accurate title, description, canonical URL and useful body content.
- Publish crawlable HTML rather than relying on client-only rendering for all important text.
- Maintain a valid sitemap, robots.txt, canonical URLs, Open Graph and social preview metadata.
- Use accurate Schema.org structured data for the organization/brand, website, software products and person only where the page content supports it.
- Add Search Console and Bing Webmaster Tools verification after the final domain is selected; submit the sitemap and monitor indexing and errors.
- Publish useful original material: release notes, feature guides, API documentation, privacy/security explanations and troubleshooting.
- Link official GitHub repositories and official social profiles consistently. Use matching brand names and descriptions across public profiles.
- Provide shareable product pages and preview images for WhatsApp, Facebook and other platforms.
- Do not buy backlinks, keyword-stuff pages, generate fake reviews, impersonate other brands or promise search ranking.
- Search engines and Meta AI discovery are not guaranteed. The goal is to make accurate, useful, public information easy for crawlers and people to find.

## Delivery sequence

1. Review and harden the existing pairing prototype; keep it private until the security findings are fixed.
2. Design the public pairing hub and separate OFC/PRO product pages.
3. Implement pairing job API, scoped job tokens, expiry, throttling and encrypted private session handoff.
4. Connect the pairing worker to the matching bot runtime and test session lifecycle, restart and logout.
5. Add privacy notice, acceptable-use guidance, support and incident handling.
6. Deploy from GitHub through EPHENYX CLOUD once isolated runtime provisioning is ready.
7. Configure the official domain, TLS, sitemap, Search Console and Bing Webmaster Tools.
8. Monitor uptime, pairing success/failure, abuse and indexing using privacy-preserving telemetry.

## Acceptance criteria

- No session credential is returned to the browser or sent as a file to WhatsApp.
- No user-controlled value is used as a filesystem path.
- Pairing codes and QR payloads are short-lived and scoped to the requesting job.
- Abuse controls prevent unbounded socket creation and pairing floods.
- OFC and PRO sessions cannot cross tenant or runtime boundaries.
- The site works on mobile, is accessible, and has crawlable product content.
- Deployments are traceable to a GitHub commit and secrets never appear in logs or source.
- Search visibility is measured through webmaster tools; no indexing or ranking guarantee is claimed.
