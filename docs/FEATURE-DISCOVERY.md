# EPHENYX Feature Discovery

A curated capability inventory from the public EliteProTech V1/V2 source trees. This is a **selection document**, not a promise that these features are implemented in EPHENYX. We will re-engineer approved ideas with EPHENYX architecture, UX and security standards.

## Candidate features

### Group operations
- **Join approval controls** — enable/disable group member approval; inspect pending requests and approve/reject selected members or all.
- **Group settings panel** — central per-group settings and toggles.
- **Tag members** — targeted mentions, all-member mention, mention-to-message support.
- **Group descriptions and profile images** — update group description/photo with validation.
- **Pin/unpin messages** — pin replied-to messages with bounded durations.
- **Disappearing messages** — explicit off/24h/7d/90d controls.
- **Group invite utilities** — invite links and participant actions, with permissions and confirmation for bulk actions.
- **Anti-bot / anti-link / anti-badword / anti-call / anti-group** protection.
- **Auto-delete selected members** — user-configurable targets, clear/list controls; must be transparent and admin-gated.
- **Welcome/goodbye** — configurable messages and event handling.
- **Status & channel utilities** — status events and channel reactions; opt-in and privacy-aware only.

### Media and download utilities
- **TikTok downloader** — URL validation, provider timeouts, media-type checks.
- **YouTube search and audio download** — search selection and validated audio response; use approved API only.
- **APK lookup/download** — only trusted source, malware scanning and explicit user consent.
- **Developer/resource search** — indexed discovery with source attribution.
- **Zip/resource search** — safe archive handling and size limits.

### Everyday tools
- **Polls** — question and multi-option poll creation.
- **Hashing** — SHA-256 by default, selectable supported algorithms, optional reply-to-file with strict size limit.
- **Image transformations** — flip/rotate, resize/HD, watermark, sticker and format conversions.
- **Audio conversions** — to audio, speed control and voice-note conversion; FFmpeg dependency health checks.
- **Video-note conversion** — square crop, short-duration limit and bounded processing.
- **Profile lookup** — group-visible profile info with privacy protections.
- **Link information** — safe URL metadata; block private/local network destinations to prevent SSRF.
- **VCF contact export** — only user-provided or consented contact details.
- **Uptime and ping** — health, latency and uptime reports.
- **Repository information** — repo metadata and update status.
- **URL tools** — validate, inspect and normalize URLs; never fetch arbitrary private network targets.
- **View-once/media utilities** — excluded from default shortlist due privacy and platform consent concerns; revisit only with explicit lawful use, consent and safeguards.

### AI
- **Chatbot modes** — DM, mention/reply-only groups, opted-in group mode, off; default should be private/mention-only, never global group auto-response.
- **Provider-backed AI** — clear timeouts, bounded fallback, quotas, graceful error handling.
- **Memory controls** — tenant/user/chat isolation, clear-memory command, retention limits and transparent opt-in.

## Suggested first selection

1. Join approval + pending request management
2. Poll
3. Hash
4. Disappearing messages
5. Pin/unpin
6. Tagall with mention/reply message
7. Group settings dashboard
8. Welcome/goodbye
9. Anti-link and anti-badword configurable protections
10. Image/audio conversion utilities
11. YouTube search/download via EPHENYX APIs
12. TikTok download via EPHENYX APIs
13. Ping/uptime/health
14. AI mention/reply mode
15. AI memory controls

## Engineering rules

- Preserve existing command names, response layouts and command behavior unless a change is explicitly approved.
- Use EPHENYX-native premium design; no copied layouts/branding.
- Group admin and bot-admin checks must be explicit.
- Bulk actions require limits and clear confirmation.
- API calls require allowlisted destinations, timeout and response-size limits.
- Validate file signatures, content type and size; scan untrusted uploads before serving or executing.
- Never execute uploaded code on the API host. Cloud runtime must be isolated with CPU/memory/network controls.
- No credentials in source; tenant context must be cryptographically authenticated.
- Test new features and report CI accurately.

## Source trees

- EliteProTech V1: https://github.com/EliteProTech/ELITE-PRO-V1
- EliteProTech V2: https://github.com/EliteProTech/ELITE-PRO-V2

V2 tree review confirmed the plugin families listed above. The feature names are derived from repository paths and inspected source files; each feature needs compatibility review before implementation.


## V1 command inventory (path-verified)

EliteProTech V1's single `ElitePro.js` handler contains a broad command set. This inventory is categorized for selection; it is not a direct implementation plan.


### Search, downloads and media
- TikTok: `tiktok`, `tiktoksearch`, `tiktokstalk`
- YouTube/media: `play`, `ytmp3`, `video`, `ytmp4`, `yts`, `ytsearch`, `lyrics`, `song`
- Spotify: `spotifysearch`, `sps`, `spotify`, `splay`
- Other media: `mediafire`, `pinterest`, `wallpaper`, `hdwallpaper`, `anime`, `fetchimg`, `fetchvid`, `fetchvideo`, `fetchaud`, `fetchaudio`, `shazam`

### AI and generation
- Chat: `gpt`, `ai`, `chatgpt`, `chatbot`, `gemini`
- Image tools: `flux`, `image`, `img`, `removebg`, `ocr`, `img2txt`, `hd`, `remini`, `enhance`, `upscale`, `toanime`, `animeavatar`
- Audio generation: `musicgen`, `aisong`, `elevenlab`
- All-in-one: `aio`

### Group and automation
- Group admin: `tagadmin`, `demoteall`, `promoteall`, `kickall`, `addall`, `groupsettings`, `gcalert`
- Automation: `welcome`, `here`, `autoreact`, `auto_react`, `antilink`, `antidelete`, `anticall`, `autolikestatus`, `autoviewlike`, `setlikestatus`
- Status: `statusd`, `story`, `fakeigstory`, `igstory`

### Utilities, contacts and information
- `ping`, `alive`, `test`, `readmore`, `define`, `google`, `gitclone`, `npm`, `npmdownloader`, `npmdownload`, `npmdl`, `fetch`, `update`, `ssweb`, `screenshot`, `shorturl`, `tiny`, `tourl`, `tempurl`, `catbox`, `url`, `imgbb`, `get`, `jid`, `lid`
- Contacts: `savecontact`, `svcontact`, `vcf`, `sendcontact`, `sencontact`, `contacttag`, `contag`
- Notes: `note`, `listnote`, `deletenote`
- Translation/voice: `translate`, `trt`, `tts`

### Entertainment and community
- `quotes`, `fact`, `truth`, `8ballpool`, `stickkill`, `animeblush`, `animewave`, `animesmile`, `animepoke`, `animewink`, `animebonk`, `animebully`, `waifu`, `neko`, `loli`, `flirt`, `insult`, `pickupline`, `wasted`
- Religious resources: `bible`, `quran`

### Owner / maintenance — restricted
- `disable`, `save`, `clearchat`, `eval`, `ev`, `create`, `firelogo`, `cloud`
- Restricted actions are not candidates for import without a threat model, permissions review, audit logging and explicit owner controls. Arbitrary code execution and unsafe remote fetches are excluded from EPHENYX implementation.

## Review note

The V1 command names were extracted from the source handler. V2 source inspection confirmed examples of join approval, disappearing messages, pin/unpin, polls, hashing, TikTok and YouTube download, chatbot modes, Tic-Tac-Toe and group member tagging. Commands may be provider-dependent, outdated, or unsafe; source existence is not proof of current functionality. We will validate each selected feature before implementation.


## Implementation selection board (reviewed source examples)

The first source-code review confirmed these practical design details:

| Feature | Source behavior verified | EPHENYX recommendation |
|---|---|---|
| `tagall` | Resolves group participant IDs, deduplicates mentions, supports quoted text and admin/bot-admin gates | Keep mention cap and rate-limit; allow quoted-message tagging and optional custom header |
| `poll` | Splits a question and options; single-choice poll payload | Add option-count/length validation and selectable-count support; use WhatsApp native poll |
| Join approval | Lists pending requests, resolves target aliases, supports approve/reject, and requires a second confirmation for bulk-all | Adopt bounded batches, safety delay, confirmation phrase, clear result summary |
| Group settings | Central setting command is present in V1 command inventory | Build an EPHENYX settings view; each toggle must explain state and persist independently |
| Pin/unpin | V2 source path confirmed | Require bot-admin and group-admin; pin replied-to message and display duration |
| Disappearing messages | V2 source path confirmed | Explicit values only (off/24h/7d/90d); no silent enablement |
| Hash | V2 source path confirmed | SHA-256 default; only accept bounded file sizes and explain output |
| Video note | V2 source path confirmed | Cap input duration and size; check FFmpeg availability before processing |

### Suggested command rollout (not yet implemented)

**Tier I — low-risk / everyday utility:** `poll`, `hash`, `ping`, `uptime`, `repo`, `translate`, `note`, `listnote`, `deletenote`, `vcf` (user-supplied contacts only).

**Tier II — admin conveniences:** `tagall`, `pending`, `approve`, `reject`, `joinapproval`, `pin`, `unpin`, `ephemeral`, `groupsettings`, `welcome`, `goodbye`, `antilink`, `antibadword`. All admin features need permission checks, anti-abuse limits and tests.

**Tier III — provider-dependent media:** `tiktok`, `play`, `ytsearch`, `ytmp3`, `ytmp4`, `convert`, `videonote`, `sticker`, `ocr`, `removebg`, `upscale`. Implement only against maintained EPHENYX endpoints, not scraped or arbitrary APIs; display provider errors cleanly and set timeouts/size limits.

**Excluded from automatic porting:** `eval`, `shell`, arbitrary `fetch`, mass kick/add/promote/demote, unconsented contact harvesting, view-once bypass, and bulk status/channel actions. These require separate owner/security review and are not part of the default feature shortlist.

No command is being added to a bot by this document. The shortlist remains a source-informed proposal for user selection; designs and command UX will be EPHENYX-native.


## Latest source review

The latest source-backed findings, risks, shortlist tiers and selection status are recorded in [Feature review — 23 November 2026](./FEATURE-REVIEW-2026-11-23.md). No commands have been ported from that review.
