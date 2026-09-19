# RECON.md — Live DOM Recon for app.videoexpress.ai

This is the root source-of-truth recon file that `SKILL.md` tells the agent to read
"always, before Phase 0," and that `references/browser-automation.md` cites throughout
as `../RECON.md`. It did not exist in the repo — this file fills that gap by
consolidating every selector, label, and flow detail that is actually verified
elsewhere in this package (`SYSTEM_PROMPT.md`'s traps, `references/browser-automation.md`,
`references/character-prompting.md`) into one place, plus one open item that any future
edit to either workflow doc should resolve.

Everything marked **VERIFIED** was captured from the live DOM in a real run. Everything
marked **UNVERIFIED** is a defensive pattern only — treat it as "screenshot, look, adapt,"
never as a promise.

---

## 0. Reconciled — where speech goes (was two workflow generations)

This package used to contain **two different, non-identical** descriptions of how speech
reaches the video. `SKILL.md` and `references/character-prompting.md` have since been
updated to match `SYSTEM_PROMPT.md`; this section is kept as a record of the divergence
and the reasoning for resolving it the way it was resolved.

| | `SYSTEM_PROMPT.md` (root, "canonical source") | `SKILL.md` + `references/character-prompting.md` (pre-fix) |
|---|---|---|
| Lipsync | `Lipsync HD Video` ticked **always** | Ticked for talking scenes |
| Where speech goes | The separate **"Create Lipsync Audio"** dialog that opens after `Create Video`, in **Actor 1 Script** (`#opt_prompt_actor1`) | Directly in the **Video and Audio Prompt** textarea, in quotes |
| Line length limit | Under **100 characters** (dialog enforces this and rejects longer scripts) | 20–25 words |
| Scene count | 7 (adjust 6–8) | 6–8 |
| Emotional structure | Mandatory "emotional wave" (no repeated consecutive emotion, ≥3 polarity flips, stakes in the hook) | Not specified |

`SYSTEM_PROMPT.md`'s TRAP 4 is explicit that writing dialogue into the Video and Audio
Prompt is a **known failure mode** ("closed-mouth narration") on this app, which makes it
the more recent, more specific — and load-bearing — account. `SKILL.md` and
`references/character-prompting.md` now follow `SYSTEM_PROMPT.md`'s Lipsync-dialog flow,
100-character Actor 1 Script limit, 7-scene default, and emotional-wave requirement
throughout. `references/examples/rama-ravana-final-battle.md` is a full worked example in
this current layout; the 6-scene example retained in `character-prompting.md` §6 has been
updated to the same field layout (it predates the emotional-wave rule, which is why it
isn't tagged with emotions — see the Rama–Ravana example for that).

One further, separate inconsistency was noticed but **not** resolved here and is left for
a future pass: `SYSTEM_PROMPT.md` itself gives two different accounts of how parallel the
lipsync generation step is. Its `LOCKED DEFAULTS` and `STEP 4 BATCH LOOP` describe queuing
up to 5 scenes in parallel, matching the platform-wide 5-generation limit; but its own
`THE CORRECT SEQUENCE` §B step 6 describes the Create Video button going disabled while a
lipsync job runs, "effectively ONE AT A TIME." `SKILL.md` Phase 6 now tells the agent to
verify empirically (screenshot after each `Create Video` click) rather than assume either
account, since resolving which is actually true requires a live run this package can't
perform.

---

## 1. Verified selectors (union of both workflow docs)

### Create Video From Prompt — main generator modal

| Element | Selector / target | Status |
|---|---|---|
| Image Prompt textarea | `#opt_prompt` (placeholder `A man drinking coffee in a rainy cafe`) | VERIFIED |
| Video and Audio Prompt textarea | `#opt_video_prompt` (placeholder starting `He takes a sip of coffee.`) | VERIFIED |
| Aspect toggle | buttons with text `Landscape 16:9` / `Vertical 9:16` (**with a space** — do not confuse with the preview-canvas control, which reads `Landscape16:9`/`Vertical9:16` with no space) | VERIFIED |
| Image Type | `select`, values `human` / `2d` / `3d` / `photorealistic` / `other`, default `human` | VERIFIED |
| Use Consistent Character | `input[name="use_consistent_character"]` | VERIFIED |
| Lipsync HD Video | `input[name="talking_video"]` — ticking it **hides** Advanced Mode / Manual Video Length, so set duration first, lipsync last | VERIFIED |
| Advanced Mode | `input[name="advanced_mode"]` — must be ticked before "Manual Video Length, sec" renders | VERIFIED |
| Share this in the public gallery | `input[name="shared"]` — **CHECKED BY DEFAULT**, resets to checked on modal reopen; untick before every `Create Image` and `Create Video` | VERIFIED |
| Reference Photo (primary) | `document.querySelectorAll('button.button-reference-photo2')[0]` — **index 0**; index 1 is "Reference Photo 2" and shares the same class | VERIFIED |
| Reference Photo 2 (second character) | `document.querySelectorAll('button.button-reference-photo2')[1]` | VERIFIED |
| Create Image | `button.button-generate-image-submit` | VERIFIED |
| Save Image (result carousel) | `button.button-save-image` — icon-only, `title="Save Image"`, no text; do not match on visible text | VERIFIED |
| Result carousel slide | `.swiper-slide-pair-item` — slides accumulate across scenes, always take `slides[slides.length - 1]` (newest), never `slides[0]` | VERIFIED |
| Create Video (non-lipsync path) | `button.button-generate-video-submit` — carries a `hidden` class once Lipsync HD is ticked | VERIFIED |
| Create Video (lipsync path) | `button.button-generate-talking-video` — the active submit control once `talking_video` is checked | VERIFIED |

### Create Lipsync Audio dialog (opens after Create Video, when Lipsync HD is on)

| Element | Selector / target | Status |
|---|---|---|
| Video Prompt (dialog's own) | `textarea[name="prompt"]` **inside this dialog** — one line naming the actor and directing delivery/emotion, no dialogue text | VERIFIED |
| Actor 1 Script | `#opt_prompt_actor1` — spoken words, under 100 characters (counter shown below the field; over-length is rejected) | VERIFIED |
| Add Actor 2 / Actor 2 Script | button reveal, two-person scenes only | VERIFIED |
| Create (submits the generation) | button inside the dialog | VERIFIED |

**ID collision (VERIFIED):** this dialog's own `textarea[name="prompt"]` means a bare
`document.querySelector('#opt_prompt')` at this point still matches the *generator's*
field, not the dialog's. Scope every query inside the dialog from `#opt_prompt_actor1`
with `.closest(...)`, or take the last matching element, or click by screenshot
coordinates.

### Select Image modal (reference-photo / media picker)

| Element | Selector | Status |
|---|---|---|
| Folder tile | `div.library-item.library-folder.button-folder` | VERIFIED |
| Folder title text | `div.library-folder-title` | VERIFIED |
| Folder-list container | `div.modal-library-categories.scroll-y` | VERIFIED |
| Selected folder state | `.active` class on the tile | VERIFIED |
| Items container (inside a folder) | `div.modal-library-items.scroll-y` | VERIFIED |
| Draggable media item marker | `.ui-draggable` — present only after a folder is opened | VERIFIED |
| Sort dropdown | default `Newest` → newest item is index 0 | VERIFIED |
| Multi-Select / Back / Choose / Close | buttons by text | VERIFIED |
| Empty folder | literal text `Empty.` | VERIFIED |
| Timeline drag source (Media Library panel) | `.library-item.show-on-hover-parent.timeline-source-item.library-item-draggable.ui-draggable` | VERIFIED |

This modal is **not exposed to the accessibility tree** — `read_page` returns only
`Choose` and `Close`. Click tiles by screenshot coordinates or by resolving the selectors
above with JavaScript and clicking the returned point with the real mouse.

Standard platform folders: **`My AI Images`** (Save Image lands here) and
**`My AI Videos`** (finished generations land here, caption = the prompt text used).

### Timeline

| Element | Selector | Status |
|---|---|---|
| Timeline root | `div.timeline.ui-resizable` | VERIFIED |
| Drop target | `div.track-row.ui-droppable` (two exist on a fresh project) | VERIFIED |
| Timeline clip (for context-menu actions) | `div.brick.video` | VERIFIED |
| Zoom out / in | `button.timeline-button-zoomout` / `button.timeline-button-zoomin` | VERIFIED |
| Auto Align Clips | toolbar link by text | VERIFIED |

Drag-and-drop is **jQuery UI 1.12.1**, not HTML5. `dragstart`/`dragover`/`drop` events do
nothing. Use a real mouse sequence: mousedown on the source → several mousemove steps →
mouseup over the target. See `references/browser-automation.md` §2 and §4 (Snippet 4) for
the full fallback script.

### Top bar

`New`, `Open`, `Save`, `Save Project As`, `Export Project`, `Export Video`, `My Videos`,
`My Purchases` — all by visible text. VERIFIED.

---

## 2. Unverified / defensive-only items

- **Consistent Character agreement dialog**: wording and selector unverified; if any
  dialog containing "agree" appears after ticking Use Consistent Character, accept it and
  re-screenshot.
- **Manual Video Length slider cap**: whether it caps at 10s is unverified — read the
  displayed value back before trusting it.
- **Upload entry point for a customer's own reference photo**: not covered by verified
  recon. Try the "Reference Photo" button's Select Image modal first; screenshot and adapt.
- **Export dialog options**: unverified. Screenshot, accept defaults, take the highest
  resolution/quality and MP4 if offered, never tick anything that publishes or shares.
- **Per-item tile class inside a media folder**: not independently captured beyond the
  timeline drag-source selector above; if it doesn't match at runtime, read the class off
  the actual element and reuse it for the rest of the session.

---

## 3. See also

- `SYSTEM_PROMPT.md` — the canonical, self-contained agent instructions (traps, sequence,
  script-writing rules).
- `references/browser-automation.md` — deeper detail on the drag-and-drop mechanics,
  polling patterns, and ready-to-paste JavaScript snippets.
- `references/character-prompting.md` — the character-bible method and a worked
  6-scene example.
- `references/examples/rama-ravana-final-battle.md` — a full 7-scene worked example
  including the emotional wave.
- `references/troubleshooting.md` — symptom → cause → fix runbook.
