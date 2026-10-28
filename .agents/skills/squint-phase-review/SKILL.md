---
name: squint-phase-review
description: Review evidence before completing a Squint implementation phase, especially geometry, image-free flags, owner isolation, vote closure and private game answers. Use for phase handoffs or release readiness, not ordinary unrelated edits.
---

# Squint phase review

Use this repeated gate-review workflow to decide whether the currently authorized Squint phase is complete. Reading this skill does not authorize implementation, deployment, messages, or advancement to another phase.

Read the current phase in [plan.md](../../../plan.md), its acceptance criteria in [PRD.md](../../../PRD.md), relevant contracts in [architecture.md](../../../architecture.md), and unresolved [decisions.md](../../../decisions.md). Do not load unrelated optional-layer requirements as current work.

## Review observable invariants

- For capture/geometry: verify actual Twitch normal/theater frames and tested zooms, active-image coordinates excluding bars, cancellation and live return. A fixture-only mock cannot prove capture.
- For flags: inspect network traffic on success, failure and retry; no screenshot or encoded image may leave. Two independent profiles count separately; two tabs sharing one installation do not. Check cooldown, idempotency and window expiry.
- For private data: test anonymous and wrong-owner reads/writes. Public projections must omit private signals, tokens and installation IDs. CORS is not an ownership check.
- For polls: reference aspect ratio, replacement without extra participants, no-vote/tie results, server deadline, restart reconciliation and close/vote races need evidence.
- For Find-it, if in scope: inspect public API, HTML, JS assets, errors and event streams before close; secret geometry must be absent. Check identical/disjoint boxes and final-vote tie timing.
- For retention or optional models: verify the adopted expiry policy, application reads/deletes and separately disclosed logs; confirm hard budgets and explicit image submission. Never assert provider behavior without checked settings/terms.

Run only checks relevant to changed behavior and the current gate. Record actual results in [evidence.md](../../../evidence.md), including conditions, revision/artifacts and failures. Distinguish [Verified] source facts from measured Squint behavior and keep [Unverified] gaps visible.

## Handoff

Report scope changed, checks run, observed results, remaining limitations and gate status. Mark the phase passed only when all mandatory criteria have evidence. After a passed gate, stop for the user's review before the next phase, as required by the project's staged workflow. A failed Tier A gate blocks advancement; optional failures trigger the documented cut order. A reduced-scope Tier A pivot requires the user's decision and must not be labeled complete against the original scope.
