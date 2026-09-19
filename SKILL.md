---
name: videoexpress-consistent-character
description: Use when the user wants a consistent-character talking video on VideoExpress.ai (app.videoexpress.ai) — spokesperson videos, AI avatar or presenter videos, talking-head UGC clips, or any multi-scene video where the same person must look identical in every shot.
---

# VideoExpress.ai — Consistent Character Video

Builds a ~60-second vertical talking-head video on app.videoexpress.ai: one character, 7 scenes
(adjust 6–8 to land near 60s), the same face and outfit in every shot, assembled on the timeline and
exported.

The person running this is a **non-technical customer**. They answer two questions and nothing else.
Everything after that is your job. Never ask them to inspect the page, read code, or fix a selector.

## Source of truth

`RECON.md` (this package) is live DOM recon from the real app. It is the **only** authority for
selectors, button labels, folder names, and flow. Read it before you touch the browser — including
its §0, which resolves a past inconsistency between this file and `SYSTEM_PROMPT.md` over where
spoken dialogue goes. This file now follows `SYSTEM_PROMPT.md`'s Lipsync Audio dialog flow throughout.

**Never invent a selector, button label, or UI control.** If you need something RECON.md does not
cover, handle it as a runtime check — screenshot, look at what is actually on screen, adapt — and
tell the user plainly that you are improvising that step.

Detail lives in `references/`. Read the file when you reach the phase that needs it; do not preload
everything. If a filename below is missing, list `references/` and read what is there.

| File | Read it when |
|---|---|
| `RECON.md` | Always, before Phase 0 |
| `references/browser-automation.md` | Any time you need a selector or exact label |
| `references/character-prompting.md` | Phase 2 and every scene in Phase 6 |
| `references/examples/rama-ravana-final-battle.md` | A fully worked 7-scene example in the current field layout |
| `references/troubleshooting.md` | Anything fails, stalls, or looks wrong |

## Fixed defaults — do not ask about these

| Setting | Value |
|---|---|
| Aspect ratio | Vertical 9:16 (modal toggle, not the preview-canvas one) |
| Image Type | Human |
| Scenes | 7 (adjust 6–8 so total lands near 60s) |
| Clip length | ≤ 10 seconds each (an architectural ceiling of the video engine, not a setting) |
| Target runtime | ~60 seconds total |
| Parallel generations | 5 at a time, maximum |
| Use Consistent Character | ON |
| Lipsync HD Video | ON, always. Speech goes in the Create Lipsync Audio dialog's **Actor 1 Script** field, opened after clicking Create Video — never in Image Prompt or Video and Audio Prompt (see Phase 6) |
| Share this in the public gallery | **CHECKED BY DEFAULT — you must actively untick it every time** |

---

## Phase 0 — Open the app

1. Load the browser tools you need in a single `ToolSearch` call (tabs context, navigate, computer,
   read_page, find, form_input, javascript_tool, file_upload).
2. Open `https://app.videoexpress.ai` and screenshot.
3. If the user is not logged in, stop and ask them to log in, then continue. Never enter credentials
   yourself.

## Phase 1 — Ask exactly two questions, in one message

Send **one** message containing both questions, then never ask again:

> **1.** Do you want to upload your own reference photo of the person, or should I generate an AI
> character for you? (If you want me to generate one, give me a short description — age, gender,
> look, and what they're wearing.)
>
> **2.** What's the topic of the video?

Rules:
- No third question. Not about length, style, voice, music, call-to-action, or anything else.
- Missing detail is your problem to solve. If they say "generate one" with no description, invent a
  credible one that fits the topic and proceed.
- If they answer "upload", ask for nothing further here — you collect the file path in Phase 4.
- After this message you run fully autonomously to a finished export.

## Phase 2 — Lock the Character Bible and write the script

Do this before opening any modal. It is the difference between a professional video and eight
strangers wearing the same shirt.

1. Write a **Character Bible**: one paragraph, fixed wording, covering age, build, face, hair,
   exact clothing (garment, colour, fit), plus the exact location, background, and lighting.
   This paragraph gets pasted verbatim into every single scene. See
   `references/character-prompting.md`.
2. Write **7 scenes** (adjust 6–8) on the user's topic, structured as an **emotional wave**:
   - Tag every scene with one dominant emotion (Sad, Happy, Surprised, Angry, Excited, Shocked, with
     Curious/Relieved allowed as connectors). No two consecutive scenes share an emotion, and the
     wave must flip polarity (negative ↔ positive) at least 3 times across the video.
   - Scene 1 names the stakes (why the viewer should care) as the hook. Keep one open loop running
     that only the final scene resolves; end on the highest-energy positive beat flowing into the CTA.
   - Each spoken line must be **under 100 characters** (the platform's Actor 1 Script field enforces
     this and rejects longer scripts) — roughly 12–15 spoken words.
   See `references/character-prompting.md` and `references/examples/rama-ravana-final-battle.md`
   for the full method and a worked example.
3. For each scene produce three pieces of text, used in two different places:
   - **Image Prompt** (in the generator modal) = Character Bible verbatim + one short clause showing
     this scene's emotion on the character's face/body. No speech, no dialogue.
   - **Video and Audio Prompt** (in the generator modal) = scene mood/camera description only, plus
     the frozen accent/tone/pace voice line, identical every scene. No dialogue here either.
   - **Actor 1 Script + delivery direction** (entered later, in the Create Lipsync Audio dialog that
     opens after Create Video — see Phase 6): the spoken line itself (under 100 characters), and a
     one-line delivery direction ("she excitedly says...", "he whispers, furious...") matching the
     scene's emotion tag.
4. Show the user the script as a numbered list before generating. This is a status update, not a
   question — do not wait for approval, just proceed.

## Phase 3 — Open the generation modal and set it up

1. Left sidebar → **Create with AI** → the card **Create Video From Prompt**
   (its badges read: Image to Video, Lipsync HD, Narrate, Consistent Character). That card, not
   "Text To Video" and not "Image To Video (Old Algorithm)".
2. In the modal, click **Vertical 9:16**. Confirm by screenshot. Do not use the preview-canvas
   aspect control elsewhere on the page — the modal one governs generation.
3. Set **Image Type** to `human`.
4. **UNTICK** **Share this in the public gallery** — `input[name="shared"]` is **CHECKED BY DEFAULT**. Untick it, then verify by screenshot. Repeat every time the modal reopens; inaction publishes the video.
5. Leave "Automatically enhance my image prompt" as you find it.

## Phase 4 — Get the reference photo

**Branch A — user said "generate":**
1. Paste the Character Bible alone into **Image Prompt** (no scene action yet).
2. Click **Create Image**. Wait, screenshot, and review the carousel (`Previous slide` /
   `Next slide`) for the frame with the clearest, most natural face.
3. Click **Save Image**. It lands in the `My AI Images` folder. Whether it appears immediately is
   unverified — poll the folder rather than assuming.
4. If the face is unusable, adjust the Bible's wording and regenerate. Two retries max, then take
   the best one and move on. Never ask the user to choose.

**Branch B — user said "upload":**
1. Ask for the file path in one short message. This is a handoff, not a new question in the
   two-question budget — keep it to a single line.
2. Click **Reference Photo** to open the "Select Image" modal, screenshot it, and look for an
   upload affordance. **RECON.md does not verify an upload control** — this step is improvised.
   If a file input exists, use the browser file-upload tool against it. If no upload path is
   visible, say so plainly and fall back to Branch A rather than guessing.
3. Once uploaded, treat it exactly like a saved AI image for the rest of the flow, and derive the
   Character Bible by *describing the uploaded person* — clothing, hair, setting — so text prompts
   in later scenes reinforce the same look.

## Phase 5 — Attach the character

1. Tick **Use Consistent Character**.
2. Screenshot. A first-use agreement dialog may appear — its exact wording and selector are
   **unverified**. If any dialog containing "agree" is on screen, accept it, then screenshot again
   to confirm you are back in the modal.
3. Click **Reference Photo** — `document.querySelectorAll('button.button-reference-photo2')[0]`
   (index 0; index 1 is "Reference Photo 2" and shares the same class) → the "Select Image" modal
   opens.
4. Folder tiles and image tiles are **not in the accessibility tree**. `read_page` shows only
   `Choose` and `Close`. Click tiles by screenshot coordinates, or via the verified selectors in
   `RECON.md` / `references/browser-automation.md`. Do not expect `find` to locate them.
5. Open the **My AI Images** folder. Sort is `Newest` by default, so your saved image is first.
   Select it → **Choose**.
6. Screenshot to confirm the reference photo is in slot 1 and slot 2 is empty before generating
   anything.
7. Leave **Reference Photo 2** empty — it is for a second person in two-character scenes.

## Phase 6 — Generate the clips

For each scene, in order:

1. Clear **Image Prompt** and paste that scene's full text — Character Bible verbatim, then the
   emotion-showing action clause. Never write "same character as before" or "as previously
   described"; the model does not carry that over between generations.
2. Clear **Video and Audio Prompt** and paste this scene's mood/camera description plus the frozen
   voice line. **No dialogue in this field** — writing spoken words here risks a "closed-mouth
   narration" clip where audio plays but the lips don't move.
3. Confirm **Lipsync HD Video** (`input[name="talking_video"]`) is ticked. Ticking it hides the
   Advanced Mode / Manual Video Length controls, so set any manual duration first, lipsync last.
4. Re-verify Vertical 9:16, Image Type human, Use Consistent Character on, public gallery off.
5. Click **Create Video**. With Lipsync HD ticked, the active submit control is
   `button.button-generate-talking-video` (the plain `button.button-generate-video-submit` carries a
   `hidden` class and does nothing if clicked).
6. The **Create Lipsync Audio** dialog opens. This dialog has its **own** `textarea[name="prompt"]`,
   so a bare `#opt_prompt` selector at this point still matches the generator's field, not the
   dialog's — scope every query from `#opt_prompt_actor1` with `.closest(...)`, or click by
   screenshot coordinates.
   - Dialog's **Video Prompt** = one line naming the actor and directing delivery/emotion for this
     scene (e.g. "Actor 1 is the man in the green sweater. He excitedly says his line."). No dialogue
     text here.
   - **Actor 1 Script** (`#opt_prompt_actor1`) = this scene's spoken line, under 100 characters. If
     the app rejects it as too long, shorten it and submit again.
   - Click **Create** inside the dialog. That submits the actual generation.

Batching: the platform's stated parallel-generation limit is **5**. Queue up to 5 scenes this way,
then wait. In practice the Create Video / Create Lipsync Audio flow has also been observed to disable
its own **Create Video** button until the current job finishes, i.e. effectively one-at-a-time —
treat that as the safer assumption for lipsync scenes: screenshot after each `Create Video` click and
confirm the button either re-enables or a new slide appears before starting the next scene, rather
than firing all 5 blind. Poll the `My AI Videos` folder in the Media Library (and any visible job UI,
including in-progress percentage badges) until the batch is finished — the exact completion signal is
unverified, so verify by counting finished items, not by assuming a timer. Give the user a one-line
progress note per batch. Never ask them anything.

If a clip comes back with a different-looking person, a closed mouth while audio plays, or the wrong
face/clothes, regenerate that one scene with the identical prompts (image, video, and lipsync dialog
text unchanged). If it fails twice, keep the closest one and note it in the final summary.

## Phase 7 — Assemble the timeline

1. Left sidebar → **Media Library** → open the **My AI Videos** folder. Media items only become
   draggable **after a folder is opened** — at the folder-list level nothing is draggable. Every
   tile's caption is the prompt text used, so read captions to match tiles to scenes — completion
   order is not story order.
2. Drag each clip in scene order onto the timeline drop target (`div.track-row`).
   **These are jQuery UI droppables.** Synthetic HTML5 `dragstart`/`dragover`/`drop` events **do not
   work**. Use a real mouse sequence: mousedown → several mousemove → mouseup (`left_click_drag`, or
   a scripted mouse-event sequence).
3. Screenshot after each drop to confirm the clip actually landed. Do not batch-drag blind.
4. Click **Auto Align Clips** to snap everything end-to-end with no gaps.
5. Screenshot the timeline and verify the clip count and order match the script.

If drags fail twice in a row, stop dragging, tell the user the clips are safe in `My AI Videos`, and
report exactly what happened. Do not thrash the timeline.

## Phase 8 — Export and hand off

1. Top bar → **Save Project As** (name it after the topic), then **Export Video**.
2. Follow the export UI as it appears on screen; screenshot at each step rather than assuming.
3. Report back in plain language: topic, character, scene count, runtime, where the file is.
4. Tell the user to **download their files** — VideoExpress hosts project files for about 60 days.

---

## Non-negotiables

- Two questions to the user, total. Everything else is a default or your judgement call.
- Character Bible verbatim in every Image Prompt.
- Speech only in **Actor 1 Script** inside the Create Lipsync Audio dialog — never in Image Prompt,
  never in Video and Audio Prompt (dialogue in the wrong field is a known cause of closed-mouth
  narration).
- Public gallery checkbox stays off.
- No invented selectors or labels. Screenshot, look, adapt — and say when you are improvising.
- Never enter the user's password or payment details.
