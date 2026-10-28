# Squint — Interaction and visual design

Status: design specification; no screens or interactions have been built. Scope follows [PRD.md](PRD.md); coordinates and authority follow [architecture.md](architecture.md).

## Design intent

Squint helps people point to something rather than invent a text description of its location. Keep the viewer action brief and the streamer response easy to interpret. Do not make the core look like a generic AI chatbot. Fixed tags and visible positions carry the signal.

[Suggestion] Dark neutral surfaces compatible with Twitch, one warm yellow highlight for Squint, cyan for the second signal category, crisp typography and restrained motion. Names and icons accompany colors. Never mimic Twitch authorization UI or imply official endorsement. Product name: Squint; primary viewer action: **Mark**.

## Viewer — tagged flag

~~~text
Watching → activation → capturing → frozen selection → choose tag → submitting
                                                               ↓
                                                    accepted → watching
~~~

- Activate from toolbar/shortcut. An optional page button can be considered only after the capture permission spike supports it.
- Capturing state is short and explicit. On failure: **Couldn't capture this frame. Try again.** Show an actionable permission error where appropriate.
- Frozen overlay shows only the player's captured image plus tools; **Frozen frame · live continues** remains visible. Audio is untouched. No audio controls are added.
- Tool choices: **Freehand** and **Rectangle**. Stroke previews are local. On pointer release, replace the stroke with its geometric enclosing box. No semantic snap, resize handles or implied AI.
- **Redraw** removes the current selection. **Cancel** or Escape exits. A click with zero area remains unselected; do not send a point as a flag.
- Fixed tag buttons: **Can't read this** and **What is this?**. Selecting a tag selects it; **Send flag** is the final deliberate submission action. This extra explicit action is a proposed clarification of the brief's tag-and-send step.
- While sending, disable repeat submission. Accepted: close overlay and show **Flag sent to the streamer** briefly. Do not say that the streamer saw it or must act.
- Failure: preserve local box/tag, offer **Retry** or **Cancel**; no background retries. If the session ended or channel changed, cancel the stale draft and explain why it cannot be sent.
- Cooldown: **You can send another flag in 12s**; countdown uses server retry information. The streamer dashboard need not expose who sent the signal.

The viewer screenshot never appears in the streamer dashboard. Clear this up in onboarding: **Your selected area and tag are sent. Your screenshot stays on your device.** Use that wording only after the network test passes.

## Viewer — Circle Poll

Nonintrusive **Poll open** notice names the question and remaining time. Participation is opt-in; normal playback and native chat stay accessible.

**Choose an area** opens the shared reference still over the player with question, timer, Freehand/Rectangle, Cancel and **Submit vote**. Display **Reference frame · live continues**; this is not a current frame capture. Preserve image aspect ratio; unused margins are not selectable.

After acknowledgment: **Vote saved. You can change it until the poll closes.** Reopening permits a replacement. A previous box may be shown as a visual reference, but re-submitting is deliberate. The total means installations with an accepted vote, not strokes or requests.

If poll closes mid-drawing: disable submission, show **Poll closed**, and provide **Back to live**. The server settles deadline races. No votes: **No votes received**. Tied winning regions: **Tied regions**. A result should not announce a named object unless an optional editable AI label is actually available.

## Streamer dashboard — Tier A

Primary sections:

1. Channel header: Squint, Twitch-derived channel name/title/category, session state, connection status, Start/End session, Sign out.
2. **Live flags**: schematic 16:9 stage with grid heat, legend and 60-second-window explanation. Tag filters, distinct-installation total and tag breakdown. Without an owner reference background, do not render an invented screenshot.
3. **Circle Poll**: reference upload/paste, image preview, plain-text question, duration, Start poll. Start is disabled until valid input and session. Show the current immutable frame while a poll is open; changing it creates a new poll after closure.
4. Active poll: timer, distinct participant count, heat map and Close poll. Result: winning region(s), count, closed status, Start another poll.

Suggested copy: **2 extension installations flagged this area · Can't read this**. Compact labels can say **2 responses** where a nearby explanation defines the identity limit. Avoid **Chat voted**, **All viewers**, and **2 verified people**.

Threshold toast: concise tag/count/location cue, rate-limited by threshold crossing rather than every event. No continuous flashing or sound dependency. Do not promise that live flags identify the same moving object across time. Label the map **Approximate locations from recent frames**.

Dashboard controls should be usable in a narrow OBS dock. A normal browser tab remains the fallback. Use visible disconnected/stale badges rather than showing old counts as current. Reconnection requests a fresh state snapshot.

End session confirms the concrete effects: closes current voting and stops accepting signals. It is separate from deleting retained reports. See the retention decision; do not claim that End deletes data unless implemented that way.

## Optional surfaces

- Report: grouped tags, distinct counts and approximate stream/session-relative time basis; sorted by count with time sort, export and Delete. No fabricated screenshot thumbnail or VOD link. Show the applicable expiry.
- Find-it: owner selects answer using the same rectangle/freehand tools; label **Secret answer — revealed when the round closes**. Owner preview is never the viewer reference asset. Leaderboard uses generated names, overlap score and final-vote tie timing.
- Reactions: fixed emoji choices and a tap/region input. Overlay mapping is approximate. A prominent **Stop reactions** control stops publication and clears active displayed reactions; the overlay shows nothing after disconnection or token revocation.
- AI winner label: **AI suggestion**, edit control and Unclear fallback. An incorrect label never changes vote geometry or count.
- Annotated frame: a dismissible still clearly labeled as a reference, never advertised as synchronized drawing on live footage.
- Private Explain: separate docked shadow-DOM panel only if explicitly enabled in a later phase. Preserve the unsent exact crop and explicit Send. Do not replace/tab native chat as in earlier brainstorming.

## Accessibility and recovery

Buttons have names, focus indicators and keyboard access. Provide rectangle input through keyboard-adjustable coordinates or equivalent accessible fields; this is an accessible selection alternative, not draggable boundary handles. Do not claim full accessibility before verifying the actual implementation.

Keep text readable at normal browser zoom. Maintain adequate contrast; counts and tags accompany heat colors. Respect reduced motion; avoid flashing. Trap focus only while the modal inspection interface is open, restore it to the invoking control, and keep Escape reliable.

Define and inspect these states: inactive session, no player, capture failure, pending permission, no selection, submitting, cooldown, rejected session, disconnected, reconnecting, empty map, no votes, tied result, expired asset, unauthorized dashboard, ended session. Never manufacture sample data in a live dashboard; fixture/demo mode must be visibly labeled.

Initial layout support: normal and theater. Fullscreen behavior is out of scope; exit or explain unsupported mode safely rather than leaving an invisible blocking overlay. Changing browser zoom/layout mid-selection cancels or explicitly recalculates before continuing; coordinate behavior must match the tested architecture.
