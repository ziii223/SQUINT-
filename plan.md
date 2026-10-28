# Squint — Phased implementation plan

Baseline: October 27, 2026, Asia/Singapore. Only Phase 0 documentation is being delivered now. All application phases are **not started**. Dates below are target windows from the source brief, not estimates of completed work; rebaseline against actual progress.

## Phase rules

Authorize one phase, implement it, collect evidence, review the gate, then stop before the next phase. Gate evidence includes actual commands/checks, observed results, conditions, limitations and artifacts. Use the [phase review skill](.agents/skills/squint-phase-review/SKILL.md). No automatic advancement based solely on elapsed time or passing one unit test.

The whole project stays in one main Codex chat. Do not create separate implementation chats. Keep the actual main build session ID for submission. No deployment or paid provisioning follows implicitly from a local implementation phase.

## Phase 0 — Documentation and alignment

Target: October 27. Status: documentation authored; implementation not authorized by this package.

Deliver PRD, agent instructions, architecture, design, decision register, evidence ledger, phased plan and one reusable phase-review skill. Preserve the source brief. Validate links, consistency and skill format.

Exit: user can review the complete document set; all unverified capabilities and proposal conflicts remain visible; documentation checks pass. User decides whether to begin Phase 1 and which defaults to adopt. Source/hosting/retention questions are not falsely marked resolved.

## Phase 1 — Capture and selection feasibility

Target: October 27; update if the phase starts later. Dependencies: real desktop Chrome, test Twitch page with owned/authorized material, source-frame fixtures.

Scope: the smallest local extension experiment proving activation, capture, normal/theater placement, freehand/rectangle to normalized box, tag preview, Escape/cancel and failure cleanup. No backend, OAuth dashboard, poll, AI, semantic snapping or public deployment.

Compare video-to-canvas with visible-tab fallback. Record which route actually works and its permission/activation path. Try Lens on the same task as a reference; this can deprioritize optional private AI without invalidating the streamer feedback concept.

Exit evidence:

- Live capture is clean and not black; screenshot made before overlay.
- Coordinate matrix passes at 100% plus another zoom, in normal/theater layouts; includes a non-16:9 reference/letterbox case.
- Playback continues under a frozen snapshot; closing reveals live content.
- Escape, no player, denied permissions, channel change and resize do not strand UI.
- No network image transmission; no application claims beyond the spike.
- Actual capture route/defaults recorded in decisions.md; evidence saved.

Stop: if both capture routes fail, stop and reconsider the delivery mechanism. Do not proceed to backend work to hide the failure.

## Phase 2 — Tagged flags end to end and minimal owner authentication

Target: October 28–29. Dependencies: Phase 1 passes; D04 retention selected; Twitch test app/credentials handled outside chat.

Scope: backend/session contracts, real minimal OAuth channel ownership, flag ingestion, strict image-free payloads, cooldown/idempotency, grid aggregation, live dashboard and connection recovery. Read-only public viewer context is separate from owner flags. All private endpoints require owner authorization even in the first shared deployment. No optional reports yet; retention hooks may be required for correctness.

Exit evidence:

- Two real independent browser profiles/installations mark the same area; the owner's dashboard displays 2 installations within the proposed 3-second target under stated local conditions. Two tabs sharing one ID do not count as two people.
- Repeated submissions and reconnects do not inflate counts; 60-second live-window expiry and threshold re-arming behave as specified.
- Network inspection for success/failure/retry paths finds no screenshot payloads.
- Wrong-owner and anonymous private reads fail. OAuth denial/state errors fail safely. No credentials in extension bundles or logs.
- Session restart rejects stale commands. Chosen TTL is enforced in reads and cleanup fixtures.
- Streamer tries a real response to a flag and reports whether the map is understandable.

Stop: if the dashboard conveys little practical value, reassess with the user before adding modes. Broken owner isolation blocks further shared testing/deployment.

## Phase 3 — Circle Poll

Target: October 30. Dependencies: Phase 2 and D05/D12 decisions.

Scope: owner reference upload/paste, validated immutable image, question/timer, one active poll, shared-frame viewer selection, one replaceable vote per installation, heat map, server closure and result display. No Find-it, reactions or model calls.

Exit evidence:

- Two profiles vote on the same reference and result geometry aligns in both supported layouts.
- Re-vote replaces box and heat contribution without increasing total; late/stale votes fail.
- Fixture cases verify normalization, big/small box contributions, single cluster, equal distant clusters, empty result and deterministic tie display.
- Manual and timed close are idempotent; backend restart reconciles deadlines; close/vote race cannot accept late votes.
- Raw votes disappear at closure after aggregate computation; retained results follow the selected TTL.
- Uploaded malformed/oversized/non-raster files fail cleanly; no viewer asset route leaks private owner fields.

Stop: failed alignment blocks this phase. A flags-only release is an explicit reduced-scope pivot for user decision, not a green Tier A gate.

## Phase 4 — Twitch context, hardening and Tier A release candidate

Target: October 31. Dependencies: Phases 1–3; hosting choice before any deployment action.

Scope: complete title/category/current-stream integration, accurate time-basis labels, revocation/logout handling, manual session lifecycle, durable restart/reconnect, retention and installed-extension flow. Minimal OAuth already exists from Phase 2. Resolve eligibility evidence and document any unanswered organizer question. EventSub is optional and capped at half a day; manual lifecycle remains sufficient.

Exit evidence:

- Real Twitch identity owns the correct dashboard; real title/category are shown; unavailable API context is labeled unavailable.
- Stream-relative versus session-relative time is tested without implying VOD precision.
- Token invalidation, session end and channel switching do not leak data or mix sessions.
- All Tier A acceptance criteria are green; local data resets and fresh install instructions work.
- Proposed hosting supports persistence and update/recovery; public deployment only if authorized.
- Remaining eligibility/limitations are written accurately. No optional layer starts before Tier A is stable.

## Phase 5 — Conditional Tier B work, one layer per review

Window: October 31–November 1. Do not treat this as a promise that all layers fit.

**5a / Layer 3 report:** grouped count/time/tag report, time-basis/coverage labels, export, delete and TTL. Gate: known input produces correct distinct counts; deletion/expiry visible; no VOD-link claim. Adopt D04 before this work.

**5b / Layer 4 Find-it:** time-box approximately four hours after Tier A; shared reference and private answer, IoU scoring, replacement tie timing, generated pseudonyms. Gate: network and static asset inspection reveal no answer before close; identical/disjoint boxes score correctly; at least two players rank; scores/results survive close without raw identity leakage.

**5c / Layer 5 reactions:** time-box approximately four hours if time remains; fixed emojis, rate limits, optional read-only OBS source and kill switch. Gate: kill switch clears existing display and stops new delivery; revocation works; invalid/free-text payloads rejected; normalized full-canvas alignment limitation shown.

If a subphase does not meet its gate within the timebox, cut it and preserve Tier A. Do not extend the freeze to finish optional work.

## Phase 6 — Tier C only by explicit selection

Default: skipped. No date reserved. Reopen only while Tier A is green and before feature freeze.

- Layer 6 label: one safe owner-reference crop/model call per poll, editable output, Unclear/error path, provider terms and atomic hard budget enforced; no viewer images. Gate includes duplicate close/retry proving no extra calls.
- Layer 7 annotated still: reusable owner still and annotations, dismissible viewer presentation, clear reference label. Gate: delivery does not impersonate synchronized live overlay.
- Layer 8 private Explain: separate panel, unsent crop, explicit Send, model/retention caps, no silent retries, insufficient-context responses; native chat unchanged. Gate includes network verification that viewer image leaves only after Send.

No Tier C work is necessary to use Codex meaningfully; the core may remain entirely non-AI at runtime.

## Phase 7 — Freeze, demonstration and submission readiness

Hard feature freeze: end of Sunday, November 1 SGT. November 2 is for fixes, run instructions, recording and submission preparation. Target evening November 2 SGT; source official deadline and SGT conversion are in evidence.md.

Acceptance:

- Fresh-profile/machine install and run instructions are tested; correct extension artifact, environment-variable names and actual commands are documented.
- Judge path is free to test; service stays available through judging; credentials are not published. Optional AI cannot create unexpected spending or silently block required features.
- Repository includes code, authorized assets, setup and limitations. Do not claim unbuilt features in text/video.
- Demo is shorter than 3 minutes, shows real Twitch interaction and Codex use, and uses owned/authorized material. Suggested pacing: 0:00–0:15 problem; 0:15–0:55 flags and manual streamer response; 0:55–1:45 Circle Poll and at most a brief working extra; 1:45–2:35 Codex/testing; 2:35–2:50 limitations. Leave a buffer below 3 minutes.
- Record actual main build session ID; collect repository/video URLs and submission text.
- Prepare artifacts for user review; publishing/uploading/submitting requires its own authorization. Preparation is not a claim of submission.

## Cut and fallback policy

Cut first: Layer 7, then 8, 6, 5, 4, 3, EventSub. Tier A stays protected. If any mandatory gate fails, diagnose it or ask for a concrete reduced-scope pivot; do not quietly relabel incomplete work as done. Schedule pressure is a reason to remove optional features, not to fake evidence or bypass channel isolation.
