# Working on Squint

## Scope and authority

Read README.md, PRD.md, decisions.md and the current phase in plan.md before changes. The copied references/plan-v3-codex-brief.md is provenance; preserve it. Current documents translate that user-selected brief and the Squint name. Newly proposed defaults remain suggestions until explicitly adopted and recorded. Surface conflicts rather than silently changing product scope.

Current authorization is documentation only. Do not start application work from this file. Once the user authorizes implementation, complete only the authorized phase, present its evidence and stop for review before the next phase. Do not build every tier in one pass.

Use one main Codex chat. Do not create other chats or delegate implementation; preserve the primary build session for submission. Obtain its actual session ID through the supported session-status mechanism when preparing submission; do not invent it or reuse an unrelated ID.

## Product invariants

- Tier A: tagged flags, Circle Poll, Twitch-verified channel ownership/context. Optional AI is cut by default.
- Flags send normalized geometry/metadata, never an image. Capture is local and user-triggered.
- No semantic snapping, boundary handles, native-chat modifications, OBS control, or viewer free text in broadcast surfaces.
- Live flags are approximate positions; shared-frame polls use the exact immutable reference frame. Keep these coordinate spaces distinct.
- Stream-relative timestamps are not verified VOD links. Unavailable timestamps remain unavailable.
- Counts mean extension installations. Do not describe anonymous votes as verified people or chat consensus.
- Find-it answer boxes stay server-private until closure. Use explicit public serializers; never spread a private record into events.
- Owner dashboard operations check OAuth-derived channel identity. CORS and a submitted channel name are not authorization.
- AI/server credentials never ship in the extension. Optional overlays use separate read-only revocable tokens.

## Implementation conventions once authorized

Architecture choices are proposed in architecture.md. Adopt the smallest suitable stack during its phase; record departures and reasoning. Preserve the feature IDs and contract invariants across changes. Avoid adding dependencies for features already excluded.

Validate geometry and clock rules in shared pure logic. The server owns vote deadlines, replacements, rate limits, session state and counts. Treat installation IDs, client timestamps, images and tags as untrusted inputs. Reconnect through a fresh server snapshot; do not rely on extension service-worker memory for durable state.

Use narrowly scoped meaningful tests for geometry, voting/deadline races, authorization, private/public payload boundaries, and retention. Capture/network evidence and real independent browser profiles are required where named by a phase gate. Synthetic traffic and screenshots of mocks are supplementary, not proof of live Twitch integration.

Do not use real third-party stream footage without permission for public demo material. Use the creator's channel and owned code/diagrams/objects. Do not message organizers, publish, deploy, register paid services, or upload a submission without explicit authorization for that action.

## Evidence and phase review

Use .agents/skills/squint-phase-review/SKILL.md when evaluating a phase for completion. This is a reusable review procedure, not authorization to perform future phases. Read it directly if repository skill discovery is unavailable.

Keep evidence labels: [Verified], [Inference], [Unverified], [Suggestion]. Record conditions and reproducible artifact locations in evidence.md after actual checks. Never mark implementation complete because a document describes it or tests are planned.

Every handoff states: changes, checks actually run, observed results, limitations, unresolved blockers and the next proposed phase. A failed mandatory gate stops advancement; apply cut rules to optional work and seek an explicit decision for a Tier A pivot.

## Documentation ownership

PRD.md owns scope/acceptance; architecture.md owns technical contracts; design.md owns interaction; plan.md owns gates/schedule; decisions.md owns unresolved defaults; evidence.md owns source/validation status. Update dependent documents when a decision changes. Keep provider-specific operational details out until a provider is selected.

No build/test/install commands exist yet. Add verified commands when the first application phase introduces them; do not invent working commands in reports.
