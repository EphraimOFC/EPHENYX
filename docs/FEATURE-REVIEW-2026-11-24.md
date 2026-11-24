# EliteProTech V1/V2 source audit — 24 November 2026

This follow-up audits additional capabilities and security boundaries from the primary source repositories. It is a feature-selection document only: **no external code or command design is copied into EPHENYX bots**.

## Sources

- [EliteProTech V1 repository](https://github.com/EliteProTech/ELITE-PRO-V1) — current `main/ElitePro.js` (single command handler)
- [EliteProTech V2 repository](https://github.com/EliteProTech/ELITE-PRO-V2) — current `main` plugin tree

## V1 behaviors verified

| Capability | Source observation | EPHENYX adaptation |
|---|---|---|
| `approve` / `reject` | Group-admin, bot-admin checks; mention, quoted target, number, or no-target (all pending requests) | Targeted requests by default; bounded bulk batches, explicit confirmation, delays, result reporting |
| `poll` | Native single-choice poll; requires question and at least two options | Validate question/options and cap count/length before send |
| `tagall` | Mentions all participants; custom message; group and admin checks | Preview, mention cap, cooldown, optional quoted message |
| `groupsettings` | Read-only summary of welcome, anti-link, anti-status and chatbot state | Keep read-only; expose only settings this bot actually supports |
| `welcome` | Supports current group and all-group switches | Per-group admin setting; global switches owner-only, explicit confirmation |
| `chatbot` | Owner-controlled global/group/chat modes including modes that reply to all group messages | Do not port global group-auto-chat; retain private, mention/reply and per-group opt-in AI policy |
| `fetch` | Arbitrary HTTP URL; no request timeout or private-address validation; content-length check can be absent; response handling can fetch remote media | Exclude arbitrary fetch. Use allowlisted APIs, DNS/IP checks, redirect limits, response caps and timeouts |
| `eval` / `ev` | Owner-only dynamic code execution | Excluded from bot and Cloud command surfaces |

## Additional V2 capabilities

| Candidate | Source behavior | Risk / implementation notes |
|---|---|---|
| Developer search | GitHub user/repository and npm package search; username/package input validation; 30-second provider timeout | **Good utility candidate.** Use EPHENYX-owned API, encode inputs, cache and rate-limit |
| Image flip/rotate | Accepts replied image; 10 MiB input and 20 MP pixel cap; validates supported rotation angles | **Good utility candidate.** Preserve the input cap, validate MIME/signature, cleanup temp files |
| Text watermark | 10 MiB and 20 MP caps; text max 240 characters; fixed-font layout checks | **Good utility candidate.** Keep bounded dimensions/text and test font availability |
| Audio speed | Allows 0.5x–2x and FFmpeg conversion | Candidate after file-size/duration caps and cleanup are added |
| Group/channel invite information | Strict WhatsApp invite host/path parsing; resolves group or channel metadata | **Good utility candidate.** Never fetch arbitrary links; protect privacy and rate-limit lookups |
| Channel-post relay | Parses WhatsApp channel-post links, restricts media host to WhatsApp CDN, 60-second timeout, optional SHA-256 verification | Defer: media download has no explicit byte cap; add streaming cap, MIME/signature checks and consent-aware forwarding first |
| Tic-Tac-Toe | Current plugin starts a per-chat game and expires idle games after 10 minutes | Defer: source tree has no move-handler path, so the game appears incomplete in this snapshot |
| Owner chat tools | Pin/unpin chat, star/unstar a replied message; clear/delete chat uses a 60-second confirmation | Owner-only candidate, with accurate local-only semantics and no broad deletion |
| Group status | Relays content using group-status context | Excluded from initial shortlist: unusual status relay behavior and permissions need a separate review |
| View-once send helper | Sends user-selected image/video/audio as view-once, without restoring/bypassing another user's media | Sensitive; not a default command. Only consider explicit, consent-based sending with user-provided media |
| Message export/relay | Owner CRM utility exports a message payload and can replay it; 5 MiB file limit and some control-message checks | Excluded: replay/export of message payloads needs a separate threat model and stronger restrictions |

## Revised shortlist additions

These are additions to the existing selection board, not commands approved for implementation.

**Tier I — useful low-risk tools**
1. GitHub/npm developer search (provider-backed, cached, rate-limited)
2. Image flip/rotate
3. Text watermark
4. WhatsApp group/channel invite ID lookup
5. Owner pin/unpin chat and star/unstar replied message (owner-only, local semantics)

**Tier II — after safety limits**
6. Audio speed converter (bounded duration and size, temp cleanup)
7. Channel-post relay (only after streaming size cap, MIME/signature checks, and explicit forwarding intent)

**Deferred**
- Tic-Tac-Toe until a complete move handler, turn validation, game cleanup and tests are confirmed
- Group status relay
- Message export/replay
- View-once utilities
- Bulk participant actions
- Arbitrary fetch and owner eval/shell

## Selection and engineering boundaries

- Existing commands remain untouched; especially song.js layout, reactions and provider behavior.
- Any selected feature will be implemented with EPHENYX-native UX, not source design copying.
- Commands retain explicit permissions, bounded API/media work, timeouts, size/duration caps, rate limits, cleanup and tests.
- Use EPHENYX API endpoints where media/provider calls are required; avoid scraped or arbitrary providers.
- Do not execute user uploads in the control plane. Cloud runtime stays isolated and disabled until its release gates are satisfied.

