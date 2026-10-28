# Squint

**Point at the stream. Show the streamer where it matters.**

Documentation baseline: October 27, 2026, Asia/Singapore. Status: documentation only; no application or deployment exists.

Squint turns viewer-drawn regions into tagged feedback, spatial polls, and optionally lightweight audience games. Its core does not need AI. A private visual AI assistant is optional and cut by default.

## Read in this order

| Document | Purpose |
| --- | --- |
| [PRD.md](PRD.md) | Product scope, tiers, requirements, acceptance criteria |
| [decisions.md](decisions.md) | Unresolved questions, suggested defaults, source conflicts |
| [architecture.md](architecture.md) | Components, data contracts, coordinate and aggregation rules |
| [design.md](design.md) | Viewer and streamer journeys, states, copy, accessibility |
| [plan.md](plan.md) | Small implementation phases and evidence gates |
| [AGENTS.md](AGENTS.md) | Instructions for future work in this project |
| [evidence.md](evidence.md) | Checked sources, limitations, validation ledger |
| [Phase review skill](.agents/skills/squint-phase-review/SKILL.md) | Reusable phase-completion checks |
| [Original v3 brief](references/plan-v3-codex-brief.md) | Preserved user-supplied planning source |

## Authority and evidence

The user selected the v3 brief as the final concept and named the project **Squint**. It supersedes the earlier private-AI-first brainstorming and review.md. The copied source retains its original working title for provenance; current product documents use Squint.

Labels: **[Verified]** means supported by a checked source or recorded implementation evidence; **[Inference]** is reasoning; **[Unverified]** needs a spike or external confirmation; **[Suggestion]** is a proposed choice. A documented requirement is not proof of implementation. No application feature is currently verified.

Product decisions in the supplied brief are requirements. Newly specified algorithms, endpoints, stack, limits, and retention policies are suggestions until adopted for an implementation phase. See decisions.md. No blocking answer is needed to author these documents; phase-specific blockers are listed for later work.

## Working agreement

Work in one main Codex chat. Implement one authorized phase at a time, show its evidence, and stop at the gate for review. Do not implement the complete feature set in one pass. No application code, external messages, paid service provisioning, or deployment is authorized by this documentation task.

The initial validation phase must prove live Twitch capture and coordinates. The full Tier A release includes tagged flags, Circle Poll, and authenticated Twitch channel ownership/context. Optional layers do not displace these.

The only project-specific skill is a phase review workflow because the same evidence checks recur. It is included as a repository-local skill; it has not been installed globally or confirmed discoverable by the current runtime. Read its SKILL.md directly when needed.
