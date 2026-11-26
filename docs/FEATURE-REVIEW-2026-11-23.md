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


## Follow-up source review — 25 November 2026

The V1 repository's latest change since the previous audit is [commit bed443e](https://github.com/EliteProTech/ELITE-PRO-V1/commit/bed443e8e73c24a36218ddca7d4de6264b390ca6), which exposes `levelup on/off`, `level/rank/xp`, and `leaderboard/lb/top`. The underlying [levels module](https://github.com/EliteProTech/ELITE-PRO-V1/blob/11e74a7ed2b6aa72a98bf354339aa82728696a70/lib/levels.js) stores per-group user XP, grants 5–15 XP with a 30-second per-user cooldown, uses quadratic thresholds, and tracks only eligible group messages. The module's JSON persistence does not explicitly set restrictive file permissions and has no group/user population cap. EPHENYX should treat this as an optional group feature and add bounded storage, retention/reset controls, schema validation and mode-0600 atomic writes before considering implementation.

V2's [15:01 UTC 25 November reconnect commit](https://github.com/EliteProTech/ELITE-PRO-V2/commit/0f7c55b41e9b3719de5ce3f2509f28f75b43e109) closes previous socket listeners, avoids duplicate reconnect timers, applies disconnect-aware exponential backoff and stops reconnecting after logout/session takeover. This is a useful reliability pattern, but it must be evaluated against EPHENYX's existing Baileys session-preservation requirements before adaptation.

V2's [play.js update](https://github.com/EliteProTech/ELITE-PRO-V2/commit/e6c0c8da7b547aaf6b0d310c594171d76ebf793c) switches to the provider's `api.eliteprotech.site/download/ytmp3` endpoint and adds API-status, MP3-format, HTTPS-link and audio-signature checks. The inspected download path has no explicit maximum response-byte limit and follows redirects without an allowlisted redirect-host check. It remains a provider-dependent example, not a reason to change EPHENYX song.js.

The V2 TikTok and APK plugins also moved to the `api.eliteprotech.site` host in commits [ca3a2e1](https://github.com/EliteProTech/ELITE-PRO-V2/commit/ca3a2e136bb2561f7b2188c471742e88da009602) and [62be8d1](https://github.com/EliteProTech/ELITE-PRO-V2/commit/62be8d120d5f2d837d5e8169fb0592cf1f0fe683). These are endpoint changes, not proof of independent extraction or provider reliability.

No source code or command UX was copied into EPHENYX. The feature shortlist remains for selection.


## Follow-up source audit — 26 November 2026

- V1's [26 November update](https://github.com/EliteProTech/ELITE-PRO-V1/commit/cb8ea6ffd6912eed159ce1d5225fa64f94a2345f) changes provider hostnames for many existing commands. Treat this as a provider migration, not a feature port. The EPHENYX API should own its provider configuration and must not assume that a new upstream hostname is stable or safe merely because the source bot uses it.
- V2's [25 November index update](https://github.com/EliteProTech/ELITE-PRO-V2/commit/36a624fc3f5d3b85b0adc9b1085640df7729a8fd) removes the conditional Android browser selection and uses Ubuntu Chrome for registered sessions as well. There is no rationale or test result in that commit; it is not adopted.
- V2's [reconnect refactor](https://github.com/EliteProTech/ELITE-PRO-V2/commit/0f7c55b41e9b3719de5ce3f2509f28f75b43e109) is worth studying for listener cleanup, duplicate-timer prevention, exponential backoff and logout/session-takeover handling. EPHENYX should only adopt the pattern with tests proving that existing auth/session persistence and anti-delete/anti-view-once behavior remain intact.

No command was copied or changed. The feature shortlist remains a selection document, and `song.js` remains untouched.
