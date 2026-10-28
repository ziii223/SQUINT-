# Squint — Product requirements

Status: documentation baseline, October 27, 2026. Based on the user-selected v3 brief. All implementation remains [Unverified]. Sources and hackathon requirements are centralized in [evidence.md](evidence.md).

## Product promise

Viewers point at a Twitch stream by circling a region. Streamers see **where** viewers need help or want to participate, plus a fixed tag explaining **why**. Spatial votes use a shared still so viewers vote on the same content despite different playback delays.

Primary roles: anonymous extension viewer; Twitch-authenticated channel owner. No viewer account or Twitch login in v1. The channel owner has a dashboard account/session derived from Twitch OAuth; the exclusion of general accounts does not remove required streamer authentication.

Target: desktop Chrome on twitch.tv, normal and theater layouts. The streamer uses a browser tab or OBS custom browser dock. Optional reactions use an OBS browser source. No OBS control is requested.

## Problem and differentiation

[Inference] Text chat is poorly suited to unnamed visual choices and locating small or confusing details. A viewer can point more directly than describing a position in words.

[Verified: planning precedent] Visual search, screenshot tools, and prior spatial-interaction research already exist; see evidence.md. Do not claim first-ever circle interaction, guaranteed novelty, or that Lens cannot evolve. Squint's proposed distinction is the feedback loop to a real streamer and collective spatial participation. Its value is [Unverified] until tested.

Success means a streamer can recognize a flagged region, two independent extension installations can contribute visible signals, and a spatial poll produces a comprehensible result. Counts represent extension installations, not verified people, all viewers, or chat consensus.

## Tier A — required release

### A0 / Layer 0: tagged flags

Viewer activates Mark from the extension toolbar or a keyboard shortcut. Capture creates a local frozen still; underlying playback continues. Viewer draws a freehand loop or rectangle, selects **Can't read this** or **What is this?**, then submits. Escape cancels.

Selection becomes a normalized box. No semantic snapping, resize handles, or image upload. Only the channel, server-recognized session, tag, box, approximate stream-relative capture time, and pseudonymous installation identity are submitted. Images stay local in this mode. A short confirmation appears after server acceptance; failure offers a deliberate retry without silent queuing into another session.

Backend applies deduplication/rate limits, retains a rolling live aggregation, and pushes aggregate updates to the owner's dashboard. Proposed values: 20-second flag cooldown per installation/channel, 60-second live window, 12×8 grid, threshold 2 for demonstrations. These are tunable [Suggestion], not measured optimal values.

Dashboard displays a 16:9 spatial map with counts and tag breakdown; threshold crossings produce a toast. The base map has no viewer screenshot. Explain that coordinates from moving scenes are approximate; never imply object tracking. Historical live flags are not laid over unrelated current footage.

Acceptance:

- Clean, non-black capture on a live test stream without Squint overlay contamination.
- Correct coordinates at 100% and another zoom level in normal and theater layouts, including letterboxing.
- Two distinct browser profiles/installations flag one region; dashboard reports 2 installations within the proposed 3-second target under the recorded test conditions.
- Captured network evidence shows zero image transmission in this mode, including errors and retries.
- Repeated requests do not inflate counts; cooldown is enforced server-side.
- Escape, permission denial, player loss, and channel navigation clear local selection safely.
- Streamer private data is inaccessible to another channel owner and unauthenticated callers.

### A1 / Layer 1: Circle Poll

Owner enters a question, uploads/pastes a reference still they are authorized to use, chooses duration, and starts a poll. The dashboard cannot independently capture Twitch video. Only one open poll/game per channel.

Extension announces **Poll open**; viewer opts in to see the same reference image over the player, draws a region, and submits. One active vote per installation; a new vote replaces it. Dashboard shows participation and a heat map on the reference still. Server closes by deadline or owner action and highlights the most-circled region. It does not claim to recognize a named object or infer what the voter intended.

Acceptance:

- Reference frame keeps its aspect ratio and selection aligns in both supported layouts.
- Two independent installations contribute; replacement leaves the participant total unchanged.
- Deadline and manual closure agree across clients; post-close votes are rejected server-side.
- Prepared examples cover one cluster, distant equal clusters, a broad box, no votes, and replacement.
- Result clearly distinguishes a tied outcome or no votes; no fabricated winner.
- Public payloads do not leak owner tokens, private flag data, or installation identifiers.

### A2 / Layer 2: Twitch identity and context

Streamer connects Twitch through backend OAuth, claims only the verified account's channel, and sees channel title/category. Every private read/write checks that ownership. No public deployment of a dashboard with placeholder authentication.

Manual session start/end works without EventSub. Optional online/offline automation may be cut if it consumes more than half a day. Squint session identity prevents old flags or votes entering a new session. EventSub is not a prerequisite for Tier A.

Acceptance:

- Real OAuth login/callback/logout succeeds; invalid state and wrong-channel access fail.
- Channel context is fetched through Twitch API and handles API outage without inventing values.
- Token invalidation ends associated application sessions according to Twitch requirements.
- Session closure closes polls and rejects stale writes; reconnect obtains a current snapshot.
- Organizer eligibility remains a separately documented question, not an assertion.

## Tier B — conditional additions

| Layer | Required behavior if implemented | Acceptance |
| --- | --- | --- |
| 3: report | Owner-only grouped flag log, counts/tags and approximate stream-relative timestamps; export and delete | Grouped count uses distinct installations; deletion/expiry verified; timestamps never presented as verified VOD links |
| 4: Find-it | Circle Poll with secret owner-marked answer box; server scoring and generated pseudonyms | Secret absent from all viewer responses/events/assets before close; identical/disjoint overlap cases correct; 2+ entries rank; replacement rules hold |
| 5: reaction map | Fixed-allowlist emoji at normalized locations on optional OBS browser-source overlay | Rate limits and owner kill switch work; only safe public payloads; approximate alignment disclosed; no free-text names |

## Tier C — optional, cut by default

| Layer | Boundary |
| --- | --- |
| 6: AI winner label | At most one model request per closed poll using the owner's reference crop; editable result, Unclear fallback, provider terms and hard spend cap decided first |
| 7: annotated frame | Owner annotates a still and shares a dismissible still; not a live synchronized telestrator |
| 8: private Explain panel | Separate shadow-DOM panel; exact viewer crop stays pending until Send; presets never auto-send; backend credentials and budgets; no changes to native chat |

The earlier AI-first sidebar, automatic selection snapping, and native-chat tabs are superseded. Do not reintroduce them as defaults.

## Exclusions

Auto-zoom/OBS control; semantic/AI snapping and boundary handles; rebuilding or tabbing native chat; mobile/native apps; native Twitch Extension; viewer accounts, synced history or monetization; whole-stream memory; preceding audio/video; fullscreen polish; audio controls; Edge support; free-text nicknames. No paid model dependency in Tier A.

## Data, abuse, and limitations

Images remain local for flags. Owner reference frames are uploaded for polls; this is not an image-free product overall. A stable installation ID is pseudonymous, not proof of identity or truly anonymous data. It can be reset/spoofed; uniqueness is only per installation identity. Validate payloads, size, timing and channel/session server-side; rate limits reduce abuse, not eliminate brigading.

Proposed retention and image limits are in decisions.md; implement and verify cleanup before asserting retention guarantees. Reference images are visible to poll participants. Raw flags are owner-only. Optional overlay URLs carry read-only, revocable credentials, never owner sessions.

## Scope and release gates

Cut order: Layer 7 → 8 → 6 → 5 → 4 → 3 → EventSub. Tier A is protected. The v3 fallback of flags alone is a reduced-scope pivot requiring an explicit user decision; it is not a completed Tier A release.

Feature freeze: end of November 1 SGT. Prefer submission during November 2 SGT. Phase gates and external blockers are in plan.md. Hosted judging access must remain available through judging; do not confuse backend availability with per-session data retention.
