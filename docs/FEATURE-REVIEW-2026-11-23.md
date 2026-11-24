# EliteProTech feature source review — 23 November 2026

This is a source-backed review of public EliteProTech V1 and V2 trees. It is a feature-selection reference, not an implementation plan. No external commands or designs were copied into EPHENYX bots.

## Sources reviewed

- [V1 repository](https://github.com/EliteProTech/ELITE-PRO-V1), main: single `ElitePro.js` handler (10,300 lines) and package dependencies.
- [V2 repository](https://github.com/EliteProTech/ELITE-PRO-V2), main: individual plugin/event files listed below.

V1's handler has a wide mix of media, AI, group moderation, status, contact, information, entertainment and owner-maintenance commands. Source inspection counted numerous network-fetch call sites and owner execution paths. The large single-file handler is a reason to mine features by behavior, not port its implementation.

## V2 feature details

| Candidate | Source behavior observed | EPHENYX adaptation |
|---|---|---|
| Join approvals | `joinapproval on/off`, `pending`, targeted `approve/reject`; pending display capped at 50; bulk-all requires confirmation; processes one target at a time with 1.5s delay | Strong candidate; add explicit batch cap, permission checks, timeout handling, result summary |
| Poll | Native single-choice poll; only requires a question and 2+ options | Strong candidate; validate question/options lengths, cap options, allow multi-select only if supported |
| Hash | Text or replied file; SHA-256 default; 25 MiB file ceiling | Strong candidate; bounded file size and explicit algorithm |
| Pin/unpin | Reply-to-message pin/unpin; pin durations 24h/7d/30d | Candidate; source plugin lacks explicit group/admin flags—add group/admin/bot-admin gates |
| Disappearing messages | Explicit off/24h/7d/90d; group admin and bot admin required | Candidate; show current setting and require explicit duration |
| Tag all | Deduplicates resolved participant IDs; group admin, bot-admin and owner bypass | Candidate; add mention cap, cooldown, preview and custom quoted text |
| Bulk participants | Promote/demote/kick all; confirmation; admin and bot-admin checks; protects bot and admin participants | High-risk; not in initial shortlist unless user explicitly chooses, then bounded batches and audit |
| Group settings | Persistent per-group JSON settings for anti-link, bad-word, anti-bot, welcome/goodbye and related toggles | Candidate; make each toggle independent, validate values and persist per group |
| Anti-link / bad-word | Configurable delete/warn/kick-style rules; admin/owner bypass; warning thresholds | Candidate; normalize URL parsing, whitelist safe links, cooldowns, reset/list warnings, transparent notices |
| Welcome/goodbye | Group join/leave templates with member/group/count variables and optional profile images | Candidate; template validation and privacy-safe profile-image fallback |
| Video note | FFmpeg conversion with 60-second source ceiling | Candidate; add file-size ceiling, cleanup and dependency health checks |
| WhatsApp link info | Parses WhatsApp group/channel invite links only; resolves invite metadata | Low-risk utility; no arbitrary URL fetching |
| VCF | Group-admin export of resolvable group-member numbers | Not default; require clear consent and avoid bulk contact harvesting |
| Chatbot mode | Owner-controlled global `dm/group/all/mention/off` modes; `group/all` can reply to every message | Do not port; EPHENYX AI should remain private/mention/reply or explicit per-group opt-in |
| Media download | TikTok, YouTube audio/video through external providers, with long request timeouts and partial file checks | Use maintained EPHENYX API endpoints only; validate provider URLs, content signatures, size and timeouts |
| Owner fetch | V2 validates URLs, resolves DNS, blocks some private addresses, caps redirects at 3 and response at 25 MiB | Do not port as-is; DNS is not pinned to the validated address and IP-range coverage needs review |
| Owner eval/shell | Dynamic code evaluation and shell execution, owner-only | Excluded; never port to the Cloud or bot command surface |

## V1-specific safety observations

- `approve` and `reject` require group-admin/bot-admin, but a no-target path can act on all pending requests without the V2 confirmation/delay flow.
- `poll` accepts 2+ options without source-level bounds.
- `fetch` accepts arbitrary HTTP(S) URLs, has no private-address/DNS validation in the inspected handler, and does not set a request timeout.
- Owner `eval` and command-execution surfaces exist; they are excluded.
- The public handler contains credential-like literals, including a bot-token-shaped value and provider URLs with embedded credentials. Values were not copied or disclosed. Do not reuse those values; upstream should review/rotate its exposed credentials.
- V1 `song` is a separate existing UX. EPHENYX's `song.js` design must remain untouched unless the user explicitly requests a change.

## Shortlist for user selection

**Tier I — low-risk utilities:** `poll`, `hash`, `ping`, `uptime`, `repo`, notes, translation, safe URL utilities.

**Tier II — group-admin conveniences:** join approval + pending list, pin/unpin, disappearing messages, tagall, group settings, welcome/goodbye, anti-link and anti-badword. All require explicit permission checks, caps, rate limits and clear state/confirmation.

**Tier III — media:** TikTok, YouTube search/audio/video, image/audio/video conversion, sticker, OCR and other provider-dependent commands. Only implement against EPHENYX-owned/maintained API endpoints with URL allowlists, signature checks, response-size limits, timeouts and provider error handling.

**AI:** natural chat after command dispatch, private default, group mention/reply or explicit group opt-in, per-user/per-chat/tenant memory boundaries, retention and clear-memory controls.

**Excluded by default:** eval, shell, arbitrary fetch, mass kick/add/promote/demote, view-once bypass, unconsented contact exports/harvesting, fake-status tools, and unreviewed owner-only operations.

No commands have been added to PRO or OFC from this review. The shortlist is waiting for selection; existing bot command UX remains preserved.
