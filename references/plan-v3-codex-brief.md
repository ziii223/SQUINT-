# "Point at the stream" — Plan v3 (brief for Codex)

Prepared Oct 27, 2026 (SGT). This supersedes `base-layer-plan.md` (v2). It is a **concept plan**, not a PRD, and it is **not authorization to start building**.

---

## 0. Instructions for the reader (Codex)

1. **Do not write application code yet.** First read everything, then list any blocking questions (keep it to a few).
2. After the user agrees, produce these documents, concise and consistent with each other: `PRD.md`, `AGENTS.md`, `architecture.md`, `design.md`, a phase-based `plan.md` with explicit exit criteria per phase, and skills instructions only where a repeated task justifies one. Exact file layout can be decided with the user.
3. Implementation later proceeds in **small phases** with reviewable evidence. Never implement everything in one pass.
4. **Keep the evidence labels.** Do not promote an [Unverified] item to a fact, and do not claim capabilities (privacy, accuracy, Twitch eligibility) that the final build does not actually have.
5. Re-verify the hackathon rules at the official pages before relying on them: https://twitchcon-codex.devpost.com/ and https://twitchcon-codex.devpost.com/rules.
6. **Build everything in one main Codex thread.** The submission needs the session ID of the thread where most of the project was built (`/status` in Codex).

Evidence labels: **[Verified]** = seen in a source during planning, **[Inference]** = reasoning, **[Unverified]** = needs a spike or check, **[Suggestion]** = recommendation.

---

## 1. Hackathon context [Verified from the pages; re-check]

- Codex x TwitchCon Hackathon. Submission period **Oct 23, 2026 10:00 a.m. PT to Nov 2, 2026 2:00 p.m. PT**. With the Nov 1 clock change this is about **6:00 a.m. Nov 3 in SGT** [Inference]. Target submitting by the evening of Nov 2 SGT.
- Build for streamers and their community on Twitch **with OpenAI Codex**. The project must be an interactive experience that **connects to Twitch** (examples: chat, a stream overlay, the Twitch API, EventSub, an Extension).
- Must be **newly created during the submission period** and work as described in the video and text.
- Submit: repository URL with all source, assets and run instructions (private repos must be on GitHub or GitLab and shared with the address on the rules page); a text description including how Codex was used; the **Codex session ID**; a public YouTube demo **under 3 minutes** showing the project working on Twitch and how it was built with Codex; no third-party trademarks or copyrighted material without permission.
- Judging: pass/fail check on theme and viability, then four equally weighted criteria: **Innovation/Idea** (including difference from existing concepts), **Potential Impact** (useful or entertaining for streamers and communities), **Technological implementation** (including how well Codex is used), **Design** (easy to use, fun to watch).
- Must be free and unrestricted for judges to test until judging ends; individuals or teams only (no organizations). About 990 participants were registered when checked.
- **[Open question]** Does a Chrome extension on twitch.tv count as "connects to Twitch"? Planned answer: Twitch OAuth, Twitch API channel data and a streamer dashboard. Ask the organizers (email on the rules page) and keep building.

---

## 2. Crowding check

**Niche in its specific form, adjacent to a familiar category. Not fully confirmed.**

- **[Not verified]** The project gallery (https://twitchcon-codex.devpost.com/project-gallery) could not be opened with the tools used. The user should search it for "screenshot", "circle", "legible", "poll", "viewer feedback" and re-check on Nov 1.
- **[Verified]** No existing tool found in searches that lets viewers mark a region of Twitch video to signal confusion or vote spatially. Absence in search results is weak evidence.
- **[Verified]** Adjacent precedent: Twitch's extension review docs list a "Polling and Voting" category for viewer feedback to broadcasters; the 2019 TwitchCon hackathon grand prize (OK Gamers) gave streamers a visual view of what viewers want instead of guessing from a fast-moving chat. Judges know "viewer signal to streamer". The new element is *where on screen*.
- **[Verified]** Chrome's Google Lens already lets users select anything on a page and ask AI questions in a side panel (https://support.google.com/chrome/answer/15086890). A private "ask AI about my selection" feature is therefore **not** differentiated, which is why it is last and cut by default.
- **[Verified]** Screenshot tools exist (for example "Twitch Screenshot" for clean player frames) but have no streamer feedback loop.
- **[Inference]** Likely crowded areas: chat bots, Twitch-plays games, text polls, overlays, recap/summary tools.

---

## 3. Concept

Viewers **point at the stream** by drawing a loop on the video. One engine, several modes. The streamer sees *where* and *why*.

**Why this shape** [Inference]:
- Streamer-facing, which fits "for streamers and their community" and the theme check.
- The base needs no AI and almost no privacy surface.
- Several modes are visible on stream, which helps "fun to watch".
- Lens has no streamer on the other end, so it cannot copy this.

**When circle voting beats chat polls** [Inference]: when options are visual or unnamed (items on a table, parts of code, map locations). Do not claim it replaces chat polls.

**Important distinctions (carry into the PRD):**
- A frozen frame or crop is not an understanding of the whole stream.
- A chrome extension is not a native Twitch Extension; do not conflate them.
- Capture time is not a verified VOD timestamp or playable link.
- Results are from "extension viewers", not chat. Without viewer Twitch login, brigading is possible; state it.

---

## 4. Decisions made with the user

- **Excluded:** auto-zoom via OBS control (too risky for the time).
- **Included, in tiers:** everything else in section 5. The user wants the whole set; the tiering and cut order below exist to protect a working demo.
- **Reframed:** the live "streamer draws, viewers see" telestrator became the **Annotated Frame** layer (a still, not a live stream overlay) because live sync is unreliable under Twitch latency [Inference].
- **Not chosen:** a native Twitch Extension version. It would remove the viewer-install barrier but needs a different architecture, streamer enablement and Twitch review.

---

## 5. Layers

### Tier A — MUST ship

#### Layer 0: Tagged flags ("Can't read this" / "What is this?")
**Viewer (Chrome extension on twitch.tv, desktop):**
1. Toolbar button or keyboard shortcut starts **Mark**.
2. Capture the current video frame by drawing the `<video>` element to a canvas in the content script [Suggestion]. Fallback: `captureVisibleTab` triggered from the toolbar or shortcut (an injected page button alone will not grant `activeTab`). **[Unverified]** until the spike.
3. Frozen frame; viewer draws a freehand loop or rectangle. Escape cancels.
4. Reduce to a **normalized bounding box** (x, y, w, h as 0 to 1 fractions of the video). Local only. No semantic snapping.
5. Pick a tag: **Can't read this** or **What is this?** Overlay closes, live video returns, one short confirmation.
6. **Sent:** channel login, tag, normalized box, stream-relative time, random anonymous install ID. **No image.**

**Backend:** dedupes per install ID (about one flag per 20 s), rate-limits, clusters into a coarse grid (suggest 12×8) over a rolling about 60 s window, pushes live updates to the streamer page. Thresholds tunable; use a low threshold (2) for the demo.

**Streamer dashboard:** 16:9 map with highlighted cells, counts and tag breakdown ("5 asking: what is this?"), plus a toast when a threshold is crossed. OBS custom browser dock or any tab.

**Exit criteria:**
- [ ] Clean frame on a live stream, not black, no overlay contamination
- [ ] Box coordinates correct at 100% and at least one other browser zoom, in normal and theater layouts
- [ ] Two real browsers flag the same area and the dashboard shows "2" within a few seconds
- [ ] No image uploaded in this mode (verify in the network tab)
- [ ] Escape and capture failure leave no stray state

#### Layer 1: Circle Poll
1. Streamer enters a question and pastes or uploads a **reference frame** into the dashboard (the dashboard cannot capture the stream itself). Sets a timer, clicks **Start poll**. One poll per channel at a time.
2. Viewers' extension shows "Poll open"; clicking shows the reference image over the player. Viewer circles a pick and submits.
3. Dashboard shows a live heat map on the reference image with a count.
4. On timer end or **Close**, the **most-circled region** is highlighted.

Design: shared reference still because live video differs per viewer (latency, camera motion) [Inference]. Only the streamer's own image is uploaded; viewers send normalized boxes. One vote per install ID per poll; re-circling replaces. Winner [Suggestion]: weight grid cells by box coverage; winner = connected region of cells at or above about 80% of the peak.

**Exit criteria:**
- [ ] Reference image aligns on the player in normal and theater layouts
- [ ] Two browsers vote and the heat map shows both
- [ ] Close highlights a plausible winner on a prepared test image
- [ ] Repeat vote from the same install ID replaces rather than adds
- [ ] Votes after close are rejected cleanly

#### Layer 2: Twitch connection and context
- **Twitch OAuth** so the streamer claims their channel (nobody else can read that channel's signals). [Suggestion; flow details Unverified]
- Twitch API: read channel title and category; show on the dashboard.
- Optional **EventSub** `stream.online` / `stream.offline` to auto-clear sessions and polls. **[Unverified]** cost; skip if it takes over half a day.
- **Honest tension:** the streamer opens one dashboard URL, so this is not "zero streamer setup". Viewers need only the extension.

### Tier B — SHOULD ship (reuse the same data)

#### Layer 3: Legibility and questions report
An end-of-stream screen built from the flag log: "Can't read at 1:12:05, 4 viewers", "What is this? at 0:48:30, 6 viewers", sorted by count with timestamps for editing or VOD notes.
- Needs flag retention until stream end, which changes the privacy wording (see section 6). [Suggestion] Streamer can delete at any time; auto-delete after a fixed period (suggest 24 h, tunable).
- Timestamps are stream-relative, not VOD-verified.

#### Layer 4: Find-it game (mode of Circle Poll)
- Streamer shows a reference frame and secretly marks the answer region ("where's the bug?", "where's the hidden item?"). The secret region stays on the backend and is **never sent to viewers** until the round closes.
- Viewers circle where they think it is. Score = overlap between the viewer's box and the secret box (IoU or similar) [Suggestion]; ties broken by submit time.
- Mini leaderboard on the dashboard and optionally the on-stream overlay. **Auto-generated pseudonymous names** by default (for example "Viewer-7F3"); no free-text names in v1 to avoid moderation problems on a broadcast. [Suggestion]

**Exit criteria:** secret region not visible in network traffic before close; scoring correct on a prepared case; leaderboard ranks 2+ players.

#### Layer 5: Reaction map
- Viewer circles or taps a spot and picks an emoji from a **fixed allowlist** (no free text). Emoji appear at those positions through an OBS browser source overlay. [Suggestion]
- Needs rate limits per install ID and a **streamer kill switch** because the overlay is broadcast-visible.
- **[Unverified]** Position mapping: positions are normalized to the video; if the streamer's scene has webcam and panels, the overlay maps to the full canvas, so the alignment is approximate. State that limitation.

### Tier C — Optional, cut by default

#### Layer 6: AI label for the poll winner
After a poll closes, the backend crops the winning region **from the streamer's own reference frame** and asks a vision model one question ("What is in this crop?"). The dashboard shows "Most circled: …" with an "Unclear" fallback and streamer-editable text. One call per poll; no viewer images involved. Provider and spend cap not decided; set a hard cap because judges may test it.

#### Layer 7: Annotated frame (reframed telestrator)
Streamer pastes a frame, draws annotations (arrows, circles), and pushes it to viewers as a dismissible still over or beside the player. Reuses the reference-frame infrastructure. **[Unverified]** value; cut first.

#### Layer 8: Private "Explain this" panel
Docked shadow-DOM panel (do not rebuild or modify native chat); crop as an unsent attachment; prompts fill but never auto-send; image uploaded only after an explicit Send with the exact crop disclosed; credentials on the backend; caps on size, rate, history and spend; no silent retries; concise answers that say when the crop is insufficient. Overlaps Lens and costs money per viewer, so it is last.

---

## 6. Data and privacy (use this to write accurate claims)

| Feature | Leaves the viewer's device | When | Suggested retention |
| --- | --- | --- | --- |
| Tagged flags | Channel, tag, normalized box, stream-relative time, anonymous install ID | On submit | Rolling window for live view; kept for the report until stream end, then deleted (tunable) |
| Circle Poll / Find-it votes | Poll ID, normalized box, anonymous install ID | On submit | Deleted when the poll closes (leaderboard kept for the session) |
| Reaction map | Emoji from allowlist, normalized position, install ID | On tap | Not stored beyond live display |
| AI label (streamer side) | The streamer's own reference crop | On poll close | Per provider terms; **not yet verified** |
| Private Explain (if built) | The viewer's exact crop, after Send | On Send | Per provider terms; **not yet verified** |

Do not claim zero retention or that a provider never trains on data without verifying the chosen provider's actual terms and settings. Keep service credentials on a backend, never in the extension.

---

## 7. Cut list (do not build)

Auto-zoom or any OBS control · semantic or AI selection snapping and boundary handles · rebuilding or tabbing native chat · mobile or native Twitch apps · native Twitch Extension version · accounts, synced history, monetization · whole-stream memory · preceding audio/video context · fullscreen polish · audio controls · Edge support · viewer free-text nicknames.

---

## 8. Cut order if time runs short

Cut in this order, earliest first: **Layer 7, Layer 8, Layer 6, Layer 5, Layer 4, Layer 3, EventSub.** Tier A is never cut. A project with Tier A done well beats one with every layer half-working.

**Hard feature freeze: end of Sun Nov 1 SGT.** After that, only bug fixes, docs and the demo. Do not demo anything that is not working.

---

## 9. Timeline (SGT)

| Date | Work | Gate |
| --- | --- | --- |
| Tue Oct 27 | Planning docs; user checks gallery and emails organizers; Chrome Lens test on a live stream; capture spike | Capture works? Decide fallback |
| Wed Oct 28 | Layer 0 viewer extension: mark, box, tag, send | Flag reaches backend |
| Thu Oct 29 | Layer 0 backend, clustering, dashboard | Two browsers show "2" |
| Fri Oct 30 | Layer 1 Circle Poll | Poll winner shown |
| Sat Oct 31 | Layer 2 OAuth and API context; Layer 3 report | Channel claim works |
| Sun Nov 1 | Time-boxed (about 4 h each): Layer 4 Find-it, Layer 5 Reaction map; then freeze | Cut whatever is not green |
| Mon Nov 2 | README and run instructions, fresh-machine install test, record demo and Codex footage, submit by evening SGT | Submitted |

**Risk:** the schedule is tight and Nov 2 has no spare buffer. If any gate fails, apply the cut order immediately rather than extending.

---

## 10. Kill and pivot criteria

- **Lens test:** if Chrome Lens handles briefly shown items on live Twitch very well, do not pitch private AI at all. Lead with the streamer-facing layers.
- **Capture spike fails with both methods** by end of Oct 27: stop and rethink before spending more time.
- **Dashboard feels thin** after Oct 29: switch ideas rather than add features.
- **Poll alignment unreliable** by end of Oct 30: ship Layer 0 alone with a polished demo and skip Layers 1, 4 and 7.

---

## 11. Suggested 3-minute demo

1. (0:00-0:15) Problem: unreadable small text, and "what next?" decided by chat noise.
2. (0:15-0:55) Tagged flags: two viewers circle small code and tag it; dashboard shows "2 viewers: can't read"; streamer enlarges it.
3. (0:55-1:50) Circle Poll with a photo of a few objects; winner highlights on the dashboard and optionally on stream. If Find-it or the reaction map is working, show one briefly (about 20 s).
4. (1:50-2:40) Codex footage: build thread, tests, session ID.
5. (2:40-3:00) Honest limits: extension-only voters, small sample, approximate overlay alignment.

**Demo content:** the user's own channel and own material (code editor, hand-drawn diagram, objects on a table). No licensed game footage.

---

## 12. Open questions

1. What is in the gallery? (user)
2. Will organizers accept a Chrome extension plus Twitch OAuth and API as the Twitch connection?
3. Is canvas capture from the `<video>` element reliable on Twitch? (spike)
4. Does a pasted reference image align reliably over the player in all supported layouts?
5. Which AI provider and spend cap, if Layer 6 or 8 is built?
6. Grid clustering or simple region overlap for poll winners and flag hot spots? (design choice)
7. Backend hosting and free access for judges: not decided.
