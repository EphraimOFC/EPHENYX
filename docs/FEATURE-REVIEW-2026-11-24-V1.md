# EliteProTech V1 source audit — 24 November 2026

This review is pinned to the source commits below. It records capability and security observations only; it does not copy code or UX and does not authorize bot implementation.

## Reviewed source

- V1: [EliteProTech/ELITE-PRO-V1](https://github.com/EliteProTech/ELITE-PRO-V1/tree/bed443e8e73c24a36218ddca7d4de6264b390ca6) — `ElitePro.js` command handler, README, package manifest.
- V2: [EliteProTech/ELITE-PRO-V2](https://github.com/EliteProTech/ELITE-PRO-V2/tree/f1ed04d3d3ae5651f60912a30790ede916fa2276) — plugin families and previously source-reviewed group/tools plugins.

## V1 capabilities confirmed in the handler

### Group controls and automation
- Group admin list/tagging, group settings, participant/contact utilities.
- `welcome` supports per-group and all-group toggles.
- `chatbot` settings include DM, group, current-chat and global modes.
- Anti-link, anti-call, anti-delete, anti-status, anti-bot and anti-spam related command families.
- Group actions include bulk add/kick/promote/demote variants. These are **not** recommended for EPHENYX porting.

### AI and creative tools
- Chatbot/provider commands (GPT/AI/ChatGPT/Gemini and chatbot settings).
- Image generation/editing families, OCR/text extraction, background removal, upscaling and anime avatar.
- Audio generation and voice synthesis.
- Translation and text-to-speech.

### Media and discovery
- YouTube search/audio/video, TikTok search/stalk/download, Spotify search/play, MediaFire, Shazam, lyrics and wallpaper/anime tools.
- Direct fetch/send commands for image, video, audio and other content.
- GitHub repository download helper.

### Everyday tools
- Poll, notes/list/delete, QR tools, screenshot, URL shorteners/inspectors, contacts/VCF, channel ID, ping/alive, profile/group information, translation and TTS.
- Status save/download is present but privacy-sensitive and excluded from default implementation selection.

## Security findings (do not copy)

| Source observation | Risk | EPHENYX treatment |
|---|---|---|
| Google search handler embeds an API credential in the public source (line 1209). | Credential exposure and quota/abuse risk. | Never reuse it; use EPHENYX-managed credentials, secret storage and rotation. Value intentionally not reproduced here. |
| Owner-only `eval`/code-evaluation handler (line 3639). | Arbitrary code execution in the bot process. | Exclude `eval`, shell and remote code execution commands. |
| NPM package downloader interpolates package/version into a shell command (line 3670). | Shell injection if input is not safely validated/escaped. | Use argument-based process execution, strict package/version validation and isolated temporary directories—or omit. |
| `fetch` accepts arbitrary HTTP(S) URLs (line 3460); other direct fetch helpers accept URL strings. | SSRF, redirects to private endpoints, oversized/chunked responses, content spoofing. | Use destination allowlists, redirect revalidation, block private/local IPs, response byte caps, MIME/signature checks and timeouts. |
| GitHub clone helper checks URL text for `github.com` and then extracts path by regex. | Host parsing can be bypassed by deceptive URLs. | Parse with `URL`; require exact `github.com` hostname and owner/repo syntax. |
| Global chatbot and all-group welcome switches exist. | Unintended messages across private/group chats. | Natural AI must remain opt-in per DM; groups mention/reply or explicit admin group opt-in. Welcome settings must be independently scoped and auditable. |
| Status-saving handler retrieves quoted status content. | Privacy/consent risk. | Do not port view-once/status bypass. Status features only with explicit user intent and platform-consent review. |

## Source anchors

- [Google search credential handler](https://github.com/EliteProTech/ELITE-PRO-V1/blob/bed443e8e73c24a36218ddca7d4de6264b390ca6/ElitePro.js#L1209)
- [Welcome group/all toggles](https://github.com/EliteProTech/ELITE-PRO-V1/blob/bed443e8e73c24a36218ddca7d4de6264b390ca6/ElitePro.js#L1262-L1320)
- [Status save handler](https://github.com/EliteProTech/ELITE-PRO-V1/blob/bed443e8e73c24a36218ddca7d4de6264b390ca6/ElitePro.js#L1803-L1850)
- [Chatbot DM/group/chat/global controls](https://github.com/EliteProTech/ELITE-PRO-V1/blob/bed443e8e73c24a36218ddca7d4de6264b390ca6/ElitePro.js#L2025-L2120)
- [Arbitrary code evaluation](https://github.com/EliteProTech/ELITE-PRO-V1/blob/bed443e8e73c24a36218ddca7d4de6264b390ca6/ElitePro.js#L3639-L3665)
- [Shell-interpolated NPM package download](https://github.com/EliteProTech/ELITE-PRO-V1/blob/bed443e8e73c24a36218ddca7d4de6264b390ca6/ElitePro.js#L3670-L3705)
- [Arbitrary URL fetch](https://github.com/EliteProTech/ELITE-PRO-V1/blob/bed443e8e73c24a36218ddca7d4de6264b390ca6/ElitePro.js#L3910-L3990)

## Selection shortlist — source-backed, no porting yet

### Tier 1 — low-risk, useful utilities
- Poll (V1/V2 native poll patterns).
- Hash (V2 source: SHA-256 default, text/replied file; V2 file cap observed at 25 MiB).
- Notes/list/delete; translate/TTS; ping/uptime; repo information; QR tools.
- VCF export only from user-provided or consented contacts.

### Tier 2 — admin features after permissions/tests
- Group settings, tagadmin and capped tagall.
- Welcome/goodbye, per-group opt-in.
- Join approval and bounded approve/reject.
- Disappearing-message controls, reply-to pin/unpin.
- Anti-link/anti-badword/anti-bot/call controls with clear state and audit.

### Tier 3 — API-backed media
- YouTube search/audio/video and TikTok download.
- Shazam, MediaFire, Spotify search, image/audio/video conversion, OCR/upscale.
- Use only maintained EPHENYX APIs, explicit provider timeouts, strict download-size/MIME/signature checks, and clear provider failure messages.

### Excluded from automatic porting
- `eval`, shell, arbitrary URL fetch, unsafe NPM shell command, bulk add/kick/promote/demote, status/view-once recovery, global unsolicited chatbot mode, unconsented contact collection.

## Recommendation

Keep the existing EPHENYX command UX unchanged. This shortlist is for user selection; no commands have been added to PRO or OFC. Implement approved features as EPHENYX-native code with permission tests and safe APIs rather than copying the source implementation.
