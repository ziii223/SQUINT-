# Squint — Sources and validation ledger

Baseline and source check: October 27, 2026, Asia/Singapore. A source-supported capability is not an implemented Squint capability. No live app, capture spike, user test, OAuth flow or deployment has been completed.

## Evidence vocabulary

- **[Verified]**: supported by a named checked source, or a reproducible observed result with conditions and artifact. State which kind.
- **[Inference]**: reasoning from evidence; not independently demonstrated.
- **[Unverified]**: unknown, untested or externally unresolved.
- **[Suggestion]**: proposed design/default requiring adoption and validation.

Requirements may be definite without being implemented. Proposed performance targets are not observed benchmarks. The copied source's historical labels are preserved, but this ledger governs current claims.

## Hackathon — checked official sources

[Overview](https://twitchcon-codex.devpost.com/) and [official rules](https://twitchcon-codex.devpost.com/rules) were reopened during document authoring.

**[Verified: source]** Submission window: October 23, 2026, 10:00 a.m. PT to November 2, 2026, 2:00 p.m. PT. Judging ends November 15, 2026, 5:00 p.m. PT. Entrants build a new interactive Twitch-connected experience with Codex. Examples include API, chat, overlays, EventSub or Extensions. Requirements include source/run materials, project description, main Codex session ID, and a public YouTube video under three minutes showing Twitch functionality and Codex use. Judge access must be free and unrestricted through judging. Use owned or authorized demo material. Viability/theme is checked before four equally weighted criteria: idea, impact, implementation and design. Individuals/teams can enter; organizations are excluded. The overview lists $25,000 and five finalists.

**[Inference: timezone conversion]** November 2, 2 p.m. Pacific Standard Time (UTC−8) corresponds to November 3, 6 a.m. Singapore (UTC+8). Internal target remains evening November 2 SGT. Confirm the submission form's displayed deadline before submitting; no deadline is extended by this interpretation.

**[Unverified]** Organizers have not confirmed Squint's specific browser-extension + OAuth/API/dashboard implementation. No message has been sent from this chat. Participant totals fluctuate and are omitted as irrelevant to scope.

## Technical sources

| Source | Source-supported fact | Squint limitation |
| --- | --- | --- |
| [Chrome captureVisibleTab](https://developer.chrome.com/docs/extensions/reference/api/tabs#method-captureVisibleTab) | Visible-tab capture exists; permissions and privileged context matter | Live Twitch success, capture quality, zoom mapping and overlay handling untested |
| [Chrome activeTab](https://developer.chrome.com/docs/extensions/develop/concepts/activeTab) | Access follows qualifying extension invocation and is temporary | Injected Mark button is not assumed to grant it |
| [Twitch OAuth](https://dev.twitch.tv/docs/authentication/getting-tokens-oauth/) | Server-based authorization-code flow and token-specific API access are documented | Registration, callbacks, scopes and secret storage not configured |
| [Twitch validation](https://dev.twitch.tv/docs/authentication/validate-tokens/) | OAuth sessions require validation at startup and hourly; revocation invalidates affected sessions | Behavior is a requirement, not tested code |
| [Twitch API reference](https://dev.twitch.tv/docs/api/reference/) | Channel/stream data endpoints provide integration options | Actual token needs and stream-relative mapping require implementation checks |
| [Twitch Extensions](https://dev.twitch.tv/docs/extensions/) | Native Twitch Extensions use a sandboxed integration model | Squint's selected Chrome route is a different architecture |

Chrome capture and Twitch API pages were reopened during this authoring turn; activeTab and native-Extension details were checked in the preceding review. Revisit endpoint-specific documentation before implementation; do not infer a screenshot API from channel metadata.

## Differentiation evidence

- **[Verified: documentation, earlier review]** [Google Lens in Chrome](https://support.google.com/chrome/answer/15086890) supports page-region search and question/follow-up workflows in a side panel. This weakens a private-AI-first pitch; actual live Twitch usability has not been benchmarked.
- **[Verified: published precedent, earlier review]** [Snapstream research](https://kixlab.github.io/website-files/2020/chi2020-Snapstream-paper.pdf) investigates snapshot interaction in visual-art livestreams. [Twitch's time-travel post](https://blog.twitch.tv/en/2019/08/21/just-add-time-travel-building-your-games-for-both-streamers-and-viewers-cd0c54f78b64/) discusses spatial clicks and latency. Neither supports a claim that spatial viewer interaction is new.
- **[Unverified]** Current gallery competition: [project gallery](https://twitchcon-codex.devpost.com/project-gallery) has not been audited in this authoring task. Suggested search terms: screenshot, circle, legible, poll, spatial, viewer feedback; recheck November 1.
- **[Unverified]** Source-brief claims about no matching tool found, screenshot-only products, extension categories and an earlier hackathon winner were not independently rechecked here. Preserve them as research leads, not proof of uniqueness.
- **[Inference]** Streamer-facing tagged geometry and collective reference-frame voting could differentiate Squint; viewer/streamer tests are required. Avoid categorical claims about what a competing product can never do.

## Current validation status

| Area | Status | Evidence needed |
| --- | --- | --- |
| Documentation links/skill format | [Verified: local check] | 10 Markdown files present; 20 local links resolve; required deliverables present; skill validator passes; source SHA-256 matches |
| Live capture and activation | [Unverified] / not started | Live Twitch normal/theater matrix and screenshots |
| Metadata-only flags | [Unverified] / not started | Success/error/retry network capture |
| Independent participant counts | [Unverified] / not started | Two real browser profiles plus shared-ID control |
| Auth/channel isolation | [Unverified] / not started | Real login, wrong-owner/anonymous/state/revocation cases |
| Reference alignment / votes | [Unverified] / not started | Aspect-ratio cases, replacement, closure/race fixtures |
| Private Find-it answer | [Unverified] / optional | Static asset/network/error/SSE inspection before close |
| Retention | [Suggestion] D04 unresolved | Selected policy and expiry/delete/logging checks |
| Hosting/judge access | [Unverified] / not selected | Real deployed smoke test and installation path |
| Organizer eligibility | [Unverified] / unanswered | Written confirmation or explicitly documented residual risk |
| AI provider/privacy/budget | [Unverified] / cut by default | Provider decision, actual settings and cap tests |

## Append implementation evidence here

Documentation check, October 27, 2026 SGT: a local Python filesystem check enumerated the 10 Markdown files, resolved all 20 relative Markdown links, checked the required file list, and compared source/copy SHA-256 digests. All passed. The skill-creator quick_validate.py was run against .agents/skills/squint-phase-review and returned **Skill is valid!**. These checks validate package structure only; they do not validate application behavior or prove that every proposed design choice is correct.

No application-phase evidence exists yet. A later entry should state phase/feature, timestamp/timezone, build revision, test conditions, procedure or actual command, observed result, artifact location, limitation and pass/fail. Redact secrets and private identifiers before sharing artifacts. Keep failed checks visible until a linked recheck resolves them.
