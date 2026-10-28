# Squint — Decisions and open questions

Status: October 27, 2026. Source-selected product choices are requirements. Added implementation defaults below are **[Suggestion]**, not user approvals or verified behavior. Resolve each by its named phase; no answer is needed to finish this documentation package.

## Locked product direction

- Name: **Squint**.
- Viewer-to-streamer spatial feedback is the core; tagged flags, Circle Poll and Twitch channel ownership/context form Tier A.
- Layers 3–5 are conditional; Layers 6–8 are optional and cut by default.
- Desktop Chrome, normal/theater layouts; viewer extension plus owner dashboard.
- Geometric boxes only. Semantic/AI snapping, boundary handles, OBS control and native-chat replacement are excluded.
- One main Codex build chat; small phases reviewed before advancement.

## Implementation decision register

| ID | Topic | Proposed default / unresolved issue | Resolve by |
| --- | --- | --- | --- |
| D01 | Capture | Test video-to-canvas, then toolbar/shortcut visible-tab fallback; preserve normal/theater coordinate accuracy | Phase 1 |
| D02 | Stack | TypeScript, MV3 extension, React dashboard, Node service, SQLite on one durable instance, HTTP + SSE | Phase 1–2 |
| D03 | Geometry | Full active image normalized coordinates; aspect-preserving reference placement; cancel draft on layout changes | Phase 1 |
| D04 | Retention | See bounded 24-hour proposal below; original brief is inconsistent | Before persistence/Phase 2 |
| D05 | Grid and winner | 12×8, 60-second live window; flag distinct-per-cell; poll mass-normalized coverage, 80%-peak components and explicit ties | Phase 2–3 fixture review |
| D06 | Ownership | Backend OAuth authorization-code flow; validate token identity; minimal scopes; owner-only private reads | Phase 2 before exposed data |
| D07 | Stream time | Twitch stream start when available, otherwise explicitly session-relative; receivedAt governs aggregation | Phase 2–4 |
| D08 | Hosting/judging | Provider, URL, budget and long-lived connection/persistent-disk support undecided | Before deployment/Phase 4 |
| D09 | Optional AI | Provider, terms, consent, hard atomic spend limits and judge access undecided; no calls or keys needed for Tier A | Before Layer 6 or 8 |
| D10 | Explicit submission | Tag selection followed by Send flag; poll Submit vote; Escape cancels; no automatic submission on drawing | Phase 1 design review |
| D11 | Anonymous participation | One installation identity per browser profile; reset/spoofing possible; counts labeled accordingly | Phase 2 |
| D12 | Reference uploads | Raster-only safe re-encoding; suggested 5 MB / 4096 per axis / 8 MP decoded caps | Phase 3 |

## D04: Retention proposal and source conflict

The brief's Layer 3 suggests retention for the report and a possible 24-hour bound; its privacy table also suggests deletion when the stream ends. Those cannot both produce a persistent post-stream report. Choose one policy before storage implementation; do not ship contradictory copy.

**Proposed baseline:** flags and their grouped reports expire 24 hours after receipt, including while a session remains open. Ending a session stops writes and leaves a read-only report only until each record's expiry. Owner deletion is immediate at the application layer. This permits an end-of-stream report without indefinite retention and means long streams may have a rolling, incomplete report; disclose the coverage interval. Expired data is filtered from reads immediately, with background physical cleanup and logging/backups reviewed separately.

| Data | Proposed lifetime / exposure |
| --- | --- |
| Viewer flag screenshot | Memory-only locally during selection/submission; discarded on success, cancel or navigation; never uploaded |
| Flag event and installation linkage | 24 hours from receipt or earlier owner deletion |
| Report | Derived from unexpired flags; no independent indefinitely retained identity copy |
| Open poll/game raw votes | Until server close, then deleted after atomic result computation; manual/session-end/deadline closure required |
| Poll/reference/result | 24 hours from close or earlier owner deletion; answer geometry private until game close; raw identity mapping removed with votes |
| Leaderboard | Generated pseudonym and score only, until session end or 24 hours from close, whichever comes first |
| Reactions | Display TTL only; no event history; short-lived abuse counters are separate and disclosed |
| Unused owner upload | Suggested 1-hour expiry unless attached to a live poll/frame |
| Optional AI uploads | Provider policy and Squint retention not selected; feature remains disabled |

After selecting a policy, update PRD, architecture, design copy and evidence together. Avoid broad anonymous/no-retention claims; pseudonymous installation IDs and infrastructure logs are data too.

## Scope and sequencing conflicts

**Mandatory Tier A versus flags-only fallback:** the source protects Tier A but also proposes skipping Circle Poll if alignment fails. Treat that as an explicit reduced-scope pivot requiring the user's decision. Never report the original Tier A release complete in that scenario. Do not silently ship a half-working poll.

**OAuth scheduled late:** minimal owner authentication moves into Phase 2 before any shared/private dashboard exposure. Phase 4 completes Twitch context and production integration. This is a suggested dependency adjustment, not removal of Layer 2.

**Old private AI brainstorming:** source v3 supersedes it. No chat tabs, boundary snapping or upload-before-send promise applies to the core. Layer 8 has its own explicit image consent if later enabled.

## External and phase-specific blockers

1. **Eligibility [Unverified]:** organizer acceptance of browser extension + OAuth/API/dashboard. Draft a question for user review if useful; do not send it without authorization. Continue only feasible local work while awaiting the answer; do not claim acceptance.
2. **Crowding [Unverified]:** inspect current gallery and compare spatial-feedback tools. Prior absence in search results is not proof of uniqueness. Recheck November 1 as the source requests.
3. **Live capture [Unverified]:** both proposed routes need an actual Twitch spike. Failure of both stops Phase 1.
4. **Credentials [Unverified]:** owner-controlled Twitch app registration, redirect URL and a test channel are needed for real OAuth. Do not request secrets in chat or invent credentials; provide environment-variable setup when needed.
5. **Hosting [Unverified]:** no provider or paid budget authorized. Select before deployment; retain working service through judging independently of short data TTLs.
6. **Optional models [Unverified]:** no provider/budget decision blocks Tier A; it blocks only optional AI.

## Decisions after this baseline

No implementation decisions have yet been adopted or tested. For each later change record the ID, date, chosen option, reason, affected documents, evidence and any user decision on scope. Existing [Suggestion] entries must not become [Verified] merely by being implemented without testing.
