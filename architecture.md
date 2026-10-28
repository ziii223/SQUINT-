# Squint — Architecture

Status: proposed design, not implemented. Requirements come from [PRD.md](PRD.md); unresolved choices are in [decisions.md](decisions.md). All deployment, capture and performance claims remain [Unverified].

## Suggested implementation shape

[Suggestion] A TypeScript workspace with a Manifest V3 Chrome extension, a React dashboard, a small Node HTTP service, shared validation/geometry modules, and SQLite on a single persistent backend instance. Serve dashboard and owner API on one origin. Use HTTP commands plus server-sent updates and snapshot recovery; ordinary refresh/polling is an acceptable reliability fallback. No Redis, distributed workers, model service or separate microservices for Tier A.

Hosting, package choices and supported versions are not selected. Verify them when authorizing the relevant phase. A host without persistent storage or long-lived response support needs a different storage/update adapter before deployment.

~~~text
Twitch player → extension local capture → freehand/rectangle → normalized box
                                                    │
                                metadata-only commands over HTTPS
                                                    ↓
Twitch OAuth / API ← backend → session store / aggregation → owner dashboard
                               │
                               └→ public poll reference + state → extension

Owner reference upload → validated image store → immutable poll frame
Owner private Find-it answer → private record → scoring on close
Optional safe public events → read-only OBS source
~~~

Proposed later source layout: apps/extension, apps/dashboard, apps/server, packages/contracts, packages/geometry, tests/fixtures. This documentation task does not create those applications.

## Components and boundaries

**Extension content script:** player discovery, local image/selection overlay, normalized geometry, tags and poll reference rendering. Isolate styles with shadow DOM. Leave native chat untouched. Release local bitmaps on submit/cancel/navigation; keep only transient selection state during a request failure.

**Extension service worker:** toolbar/shortcut activation, privileged screenshot fallback, API transport and installation ID storage. It can restart; persist only needed IDs/configuration, not viewer screenshots. Reject requests that no longer match the active capture/channel/session. Validate message sender and requested host.

**Backend:** authenticated ownership, channel/session resolution, input validation, deduplication, rate limiting, authoritative clock, aggregation, poll lifecycle and retention. Store Twitch secrets only here. Do not log request bodies containing images, OAuth codes/tokens, installation IDs or optional AI input.

**Dashboard:** owner-only live signals, poll/game control and optional report. Explicitly obtains a reference image from its owner; no implicit access to a Twitch player in another tab. OBS custom-dock OAuth behavior is [Unverified]; complete sign-in in a normal browser if needed, then test a supported secure dock session flow. Never put owner credentials in a dock URL.

**Optional overlay:** separate revocable read-only credential, scoped to an allowlisted projection of one channel/session's reactions or results. Rotating/revoking it invalidates existing connections; never grant poll control or private flags.

## Capture and coordinate contract

1. Resolve the current player, channel and open server session before capturing. Recheck them at submission.
2. [Unverified] Try drawing the visible video frame to canvas. Cross-origin/protected/tainted/black output is a failure, not a usable frame.
3. Fallback to visible-tab capture from an actual permitted extension toolbar/shortcut activation. Call privileged APIs from the service worker, not the content script. An injected button alone does not confer activeTab permission. [Verified: API existence, not Twitch success; evidence.md]
4. Capture before adding Squint UI, then crop locally to visible video content. Reject unsupported occlusion/cropping instead of capturing unrelated page areas. Screenshot fallback only represents visible pixels, not higher-resolution source recovery.
5. Resolve intrinsic frame aspect ratio and the displayed image bounds, accounting for player letterboxing. For the first spike support contain-style, fully visible normal/theater players. Recalculate or cancel on resize, zoom, player replacement or channel change; never silently remap a draft using new bounds.
6. Freeze that local image. Playback underneath continues. The exact freeze point is capture completion, not a guaranteed zero-latency click instant.

Represent a selected box as x, y, w, h in [0,1], relative to the full active image content, excluding DOM controls and external letterboxing. Require finite numbers, positive width/height, x+w≤1 and y+h≤1. Reject invalid server inputs; local gestures can be clamped to image bounds. Minimum area for usability is a configurable client hint, not authorization.

For a freehand stroke, take min/max of valid points, clip to image bounds and require nonzero area. Closure/perfect circles are unnecessary. Rectangles use the same final representation. No semantic selection, handles or object tracking. A normalized box can refer to multiple objects; it does not imply understanding.

Poll selections are normalized to the reference image, not the live video's dimensions. Render the reference with preserved aspect ratio in the player viewport; map only its actual image bounds. Discard click/gesture input in the surrounding bars. The dashboard uses identical reference coordinates. A 16:9 flags map is a schematic container; render the appropriate source-aspect viewport inside it where known, without pretending to have a current screenshot.

## Channel, session and time

Canonical ownership key: Twitch broadcaster user ID from the validated OAuth token, with current login as display/context. Do not key access control on a client-supplied login. A backend Squint session has its own unpredictable ID, owner ID, state, start time and optional Twitch stream ID/start time.

[Suggestion] Fetch active stream context through Get Streams; channel title/category through Get Channel Information. Validate required token types/scopes against current Twitch docs during implementation. If a stream start time is available, compute an approximate stream-relative capture offset. Transport/playback latency and client clocks make this approximate. Keep server receivedAt as the authoritative aggregation clock. If Twitch stream start is unavailable, label a time as Squint-session-relative or unavailable, never silently as stream-relative. No VOD link is derived from it.

Manual start/end is mandatory. On session end: reject new signals, close the poll atomically, disconnect public session feeds or send closed state, and retain only data permitted by the selected TTL. On a new session: issue a new session ID. Reject old-session queued commands. Optional EventSub automation calls the same state transitions; verify signatures/event IDs before using it.

## Proposed records

| Record | Essential fields / exposure |
| --- | --- |
| ChannelOwner | broadcaster ID, current login, encrypted/server-protected token reference; private |
| AppSession | owner ID, session credential hash, expiry/revocation; private |
| StreamSession | ID, broadcaster ID, state, started/ended timestamps, optional Twitch stream context |
| Flag | event ID, session ID, installation key, tag enum, box, capture offset/basis, server receivedAt, expiresAt; private |
| Poll | ID, session ID, immutable image ID/hash/dimensions, plain-text question, mode, state, opensAt/closesAt, revision |
| PollAnswer | poll ID, secret box; private Find-it-only table, never shared by default |
| Vote | poll ID + installation key unique, event ID, box, acceptedAt; deleted on closure after results computed |
| PollResult | aggregate heat grid, count, winning/tied components, optional pseudonymous game scores; no raw installation IDs |
| ReferenceFrame | owner ID, validated raster asset, image dimensions, expiry; accessible through a bounded poll asset route |
| Reaction | allowlisted emoji, normalized point, session ID, expiry; transient |

Installation key is derived server-side from the random installation ID using a channel/session-scoped keyed digest [Suggestion]. This limits exposed linkage, but IDs remain pseudonymous and spoofable. Keep raw IDs out of dashboards/public events. Report distinct counts can use retained scoped keys until TTL expiry. Independent tabs sharing extension storage share one identity.

## Commands and read projections

Proposed API shapes, not implemented routes:

| Surface | Commands / reads | Access |
| --- | --- | --- |
| Auth | /auth/twitch, /auth/twitch/callback, /auth/logout | server OAuth flow and owner cookie |
| Owner | /api/owner/session, /api/owner/signals, /api/owner/events | verified channel owner only |
| Owner polls | /api/owner/polls, /api/owner/polls/:id/close | verified owner + state-changing request protection |
| Viewer | /api/viewer/channels/:login/context | public safe session/poll context; no private flags |
| Viewer flags | /api/viewer/flags | installation-scoped transport + strict anonymous limits |
| Viewer poll | /api/viewer/polls/:id, /api/viewer/polls/:id/vote | safe frame/question/deadline; no answer/raw votes |
| Public events | /api/viewer/channels/:login/events | safe revision/state notices only |
| Optional | owner report/delete, game setup, reaction and overlay endpoints | feature-gated, narrowly scoped |

Flag command fields: eventId, channelLogin, sessionId, installationId, tag, box, capturedAt, captureOffsetMs (nullable), timeBasis. Reject unknown fields and any image/data URL/binary payload. Server resolves login/session and ignores client claims of owner identity. Viewer write quotas are not an identity guarantee; optional short-lived participation tokens are abuse controls, not proof of being a genuine viewer.

Vote fields: eventId, sessionId, pollId, installationId, box. Poll mode and result eligibility come from the server. Upload a reference only through an authenticated owner endpoint; validate decoded raster type, pixel dimensions, bytes and ownership, re-encode to a safe raster, strip metadata. Suggested initial cap: 5 MB, 4096 pixels per dimension, 8 megapixels after decoding; adopt/test before shipping. Do not accept arbitrary asset-fetch URLs or SVG/HTML uploads.

## Authoritative mutation rules

- Validate and rate-limit before side effects. Suggested viewer baseline is 30 requests/minute per installation/channel plus bounded IP/global budgets, tuned for poll replacements and demo traffic; no IP retention claim until infrastructure logging is checked.
- Scope idempotency keys by operation/session/installation. Same ID plus same payload returns prior outcome; changed payload with same ID fails. Deduplication cannot rely on a client's random IDs being honest.
- Flags: enforce 20-second cooldown across tags per installation/channel; repeated flags inside the window cannot inflate the live count. Use server receivedAt for the 60-second window.
- Votes: atomic upsert by poll and installation. Replacement sets acceptedAt to the replacement time and adjusts heat, never participant count. Check server now < closesAt AND state=open within the transaction.
- Start poll and enforce one active poll/game per channel transactionally. Server timers are not sufficient: every read/write also reconciles an elapsed deadline. Backend restart and duplicate close must produce one result.
- Close atomically: compute aggregate/game result, mark closed, publish only safe result, delete raw votes. Ordinary vote duplicates after close return a terminal closed result without inserting anything. Suggested expired/closed error status: 409 with machine-readable reason; rate limits: 429 with retry time.
- UI confirmation follows acknowledgment. Network failure is not success. Reconnect fetches current state/revision; missed SSE events never reconstruct authoritative totals client-side. Resubmission requires a live matching session and explicit viewer action.

## Aggregation proposals

**Flag map:** 12×8 grid, rolling 60 seconds. For every intersected cell, count each installation at most once per tag; maintain a separate distinct-installation union for totals. Never sum cell counts as a viewer total. Per-cell intersection can illuminate a broad area; tune examples so a large loop does not imply precision. Toast only when a cell/tag transitions below→at/above threshold; re-arm after it falls below. Reports use explicit time buckets and distinct installation counts, not a sum of cell hits. Bucket duration remains a tunable suggested default of 60 seconds.

**Poll heat:** Each vote contributes total mass 1. Cell contribution = area(box ∩ cell) / area(box). This keeps large rectangles from contributing multiple full votes. Replacements remove old contributions. Peak P=0 means no winner. Otherwise candidates are cells with heat≥0.8P. Group by 4-neighbor connectivity. Most-circled region is the component(s) with highest summed heat; resolve equal values within a documented tolerance as tied components, not an invented single winner. Reference-image coordinates define the highlight. This is spatial density, not semantic object recognition or majority support. Adopt after prepared examples; do not call it validated yet.

**Find-it:** intersection-over-union between final vote box and private answer. Larger IoU ranks higher; equal score uses earliest accepted final vote, then stable server order. Replacing a vote forfeits its earlier tie time. Zero-overlap entries are misses, not winners. Generate names such as Viewer-7F3 with per-session collision handling; no user-entered nicknames. Send only aggregate participation until close, then the allowed answer/result projection. Raw answer geometry must be absent from HTML, source bundles, assets, public API, SSE and error responses before closure.

## Authentication and deployment boundaries

[Suggestion] Backend authorization-code flow, exact registered redirect, unpredictable single-use expiring state tied to the initiating browser, server-side code exchange and token validation. Owner cookie: Secure, HttpOnly, appropriate SameSite and expiry; protect mutations against CSRF and validate origins. Check token client/user identity; revalidate startup/hourly and invalidate affected app sessions on revocation/401 as Twitch documents. Ask only for scopes required by selected endpoints; no channel poll-management scope for Squint's own spatial polls.

Extension API transport should run in the service worker with explicit API host permissions; verify Chrome cross-origin behavior in the spike. Public endpoints require their own validation/limits; CORS is not authentication. Keep owner and viewer serialization types separate. Avoid deploying debug/test-auth endpoints publicly.

Suggested single-instance durable database avoids inconsistent counters and timer ownership across instances. Do not deploy this design to autoscaling ephemeral/serverless instances without changing the persistence/coordination strategy. Run startup reconciliation of expired polls and expired data, scheduled cleanup, and query-time expiry checks. Verify end-to-end HTTPS, reconnect behavior and hosting availability for judges.

Retention proposals and reference-asset lifetimes live in decisions.md. Expired data must not appear in reads while background cleanup catches up. Verify logs, backups and provider defaults before promising a complete deletion guarantee. Optional AI needs a provider, terms review and hard budget enforcement before any calls; cap atomically, cache one label per poll, and never silently retry into extra spend.
