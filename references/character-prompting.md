# Character Prompting — Keeping One Person Looking Like One Person

This file is for the **agent**, not the customer. The customer answers two questions and never sees any
of this. Everything here is about what you type into the generator modal's two textareas, and into the
Create Lipsync Audio dialog that opens after Create Video, so that seven separately-generated clips look
and sound like seven shots of the same human being in the same room on the same afternoon.

> **Field layout note:** this file follows `SYSTEM_PROMPT.md`'s and `RECON.md`'s current convention:
> spoken dialogue goes in the Create Lipsync Audio dialog's **Actor 1 Script** field, never in the
> generator modal's Video and Audio Prompt. See `RECON.md` §0 for why, and
> `references/examples/rama-ravana-final-battle.md` for a fully worked example in this layout.

The backend is a diffusion-transformer latent video engine that renders picture and audio together in one pass. Never name or speculate about the specific model to a customer. It has no memory between generations. Every clip is generated from
scratch. The *only* things carrying identity forward are:

1. The **Reference Photo** (one saved image, reused for every scene) — locks the face.
2. The **character bible text** (one block of words, reused verbatim for every scene) — locks
   everything the reference photo can't hold on its own: wardrobe, room, lighting, framing.

Drop either one and the character mutates between scenes. Customers notice instantly and it is the
single loudest signal of "AI slop."

---

## 1. The character bible concept

Write the physical description **once**. Store it in your working notes as a fixed string. Paste that
exact string into the **Image Prompt** field for every single scene, byte for byte. Do not retype it.
Do not paraphrase it. Do not "improve" it on scene 4 because you thought of a better word.

The bible is a **frozen block**. Only one line changes per scene — the action line, appended after the
block.

```
IMAGE PROMPT = [ CHARACTER BIBLE — identical every scene ] + [ ACTION LINE — the only variable ]
```

Two hard rules:

- **The bible never shrinks.** A reader might find it repetitive. The model does not. Re-stating
  "charcoal-grey crew-neck sweater" in scene 6 is what prevents scene 6 from inventing a blazer.
- **The bible describes a close-up.** Verified in the app: ticking **Use Consistent Character**
  surfaces the hint *"For best results, please only use close up shots."* Build the bible around
  shoulders-up framing and it will hold. Build it around a wide shot and it will not.

### Where the bible comes from

You get exactly one input from the customer about the character, in Q1. Two paths:

| Q1 answer | How you build the bible |
|---|---|
| **"I'll upload my own photo"** | After the photo is in the library, screenshot it in the browser, look at it, and write the bible *describing what you actually see* — age range, build, skin tone, hair, facial hair, eye colour, distinguishing marks, exact garment and colour. Then invent and freeze a location, lighting and framing (the photo may not show a usable one). |
| **"Generate a character"** + short description | Expand their short description into a full bible. Fill **every** slot below with a specific concrete choice. You are allowed to invent everything they didn't specify — that is expected. Never leave a slot vague or open. |

Either way you write the bible yourself and you never go back to the customer to ask about it.

---

## 2. Character bible template (fill in every blank)

Copy this, fill it, then never touch it again for the rest of the video.

```
Close-up portrait of a [AGE]-year-old [GENDER], [BUILD] build, [SKIN TONE] skin,
[HAIR LENGTH + STYLE + COLOUR], [FACIAL HAIR or "clean-shaven"], [EYEBROW DESCRIPTION],
[EYE COLOUR] eyes, [ONE DISTINGUISHING MARK], wearing [EXACT GARMENT + COLOUR + NECKLINE]
[SECOND LAYER or "no second layer"], [ACCESSORIES or "no jewellery, no glasses"].
[PRONOUN] is [SEATED / STANDING] in [LOCATION] with [WHAT IS DIRECTLY BEHIND THE HEAD]
and [ONE BLURRED BACKGROUND OBJECT + WHERE IT SITS IN FRAME].
[LIGHT DIRECTION + QUALITY + COLOUR TEMPERATURE], [WHERE THE SHADOW FALLS].
Shallow depth of field, shoulders-up framing, camera at eye level, static camera.
```

Slot-filling rules:

| Slot | Rule |
|---|---|
| Age | Give a number, not a range. "34-year-old", not "in his thirties". |
| Build | One word: slim / medium / athletic / broad / heavy-set. |
| Hair | Length **and** style **and** colour. "Short black hair with a neat fade" beats "dark hair". |
| Garment | Name the item, the colour, and the neckline. Colour must be a plain word — "charcoal-grey", not "slate ombré". |
| Distinguishing mark | Exactly one, small, and on a specific side. A mole, a scar, freckles across the nose. This is a cheap, high-value anchor. |
| Location | One room. Name what is *directly behind the head* — that is the pixel region the model most often reinvents. |
| Background object | One, blurred, with a stated frame position. Two or more objects give the model more to get wrong. |
| Lighting | Direction + quality + temperature + where the shadow lands. "Soft warm key light from the front-left, gentle shadow on the right side of his face." |
| Framing | Always end with the close-up/static-camera clause. Verbatim, every scene. |

Things that must **not** appear in the bible: brand names, camera model names, lens focal lengths,
anything that changes over time ("halfway through the day"), and any plural or unspecified noun
("some plants", "books").

---

## 3. The prompt fields — and where speech actually goes

The generator modal has two textareas, and — once Lipsync HD Video is ticked and you click Create
Video — a separate **Create Lipsync Audio** dialog opens with two fields of its own. All four do
different jobs:

| Field | Where | What you put in it |
|---|---|---|
| **Image Prompt** | Generator modal, placeholder "A man drinking coffee in a rainy cafe" | The frozen character bible + the one-line action/expression for this scene. **No speech. No audio. No dialogue.** |
| **Video and Audio Prompt** | Generator modal, placeholder starting "He takes a sip of coffee..." | The motion/mood/camera description for this scene, plus the frozen accent/tone/pace voice line, plus a room-sound clause. **No dialogue** — see the warning below. |
| **Video Prompt** (dialog's own `textarea[name="prompt"]`) | Create Lipsync Audio dialog | One line naming the actor and directing *how* they deliver the line — the emotion, the performance. No dialogue text. |
| **Actor 1 Script** (`#opt_prompt_actor1`) | Create Lipsync Audio dialog | The spoken words for this scene, under 100 characters — the field enforces this and rejects longer scripts. |

**Do not write dialogue into the Video and Audio Prompt.** On this app that has produced
"closed-mouth narration" — a clip where the audio plays as a voiceover but the character's lips never
move. It looks finished and slips through unnoticed unless you check for it. Speech belongs only in
Actor 1 Script.

### Image Prompt template

```
[CHARACTER BIBLE — pasted verbatim]
[ACTION LINE: one sentence. Expression and micro-movement only.]
```

Good action lines — note they change nothing structural:

- `He looks straight into the camera with a slight frown, head tilted a few degrees to the left.`
- `He raises his eyebrows and gives a small open-mouthed laugh.`
- `He leans a few inches toward the camera, expression serious, jaw set.`
- `He nods once, mouth closed, a small confident smile forming.`

Bad action lines — every one of these breaks the shot:

- `He stands up and walks to the window.` (leaves the framing, reveals the room)
- `He holds up his phone to show the screen.` (introduces an unspecified object)
- `Cut to him later that evening.` (changes lighting)
- `He gestures widely with both hands.` (pulls hands into a shoulders-up frame; hands are where
  generative video fails most visibly)

### Video and Audio Prompt template

```
[MOTION: one short sentence describing how he moves while listening/speaking.]
[VOICE: accent + tone + pace — frozen, identical every scene.]
[ROOM SOUND: one clause.]
```

Filled example:

```
He speaks directly to the camera, small natural head movements, hands stay out of frame.
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```

### Create Lipsync Audio dialog — Video Prompt and Actor 1 Script

Opens after you click **Create Video** with Lipsync HD Video ticked. This dialog has its **own**
`textarea[name="prompt"]` — a bare `#opt_prompt` selector at this point still matches the *generator
modal's* field, not the dialog's, so scope every query from `#opt_prompt_actor1` with `.closest(...)`,
or click by screenshot coordinates.

Filled example, continuing the scene above:

```
Video Prompt (dialog): Actor 1 is the man in the charcoal-grey sweater. He speaks with warm,
confident conviction, direct to camera.

Actor 1 Script: Your morning routine isn't broken. You just built it for someone else's life.
```

Do not repeat the character bible in either dialog field. The image carries appearance; the Video and
Audio Prompt carries scene mood and the frozen voice; the dialog carries performance and the words
themselves.

---

## 4. Specifying voice and accent in text

There is no voice-picker dropdown anywhere in this flow. Voice *character* (accent, tone, pace) is
specified in words, inside the generator modal's **Video and Audio Prompt**, and stays frozen across
every scene. The *delivery* of a given line (its emotion, its performance) is specified separately,
per scene, in the Create Lipsync Audio dialog's own Video Prompt field.

| Want | Write in Video and Audio Prompt (frozen every scene) |
|---|---|
| Default / broadest reach | `Neutral American English accent, warm confident tone, natural conversational pace.` |
| Authority, expert framing | `Neutral American English accent, calm measured tone, deliberate unhurried pace.` |
| Energetic hook or ad read | `Neutral American English accent, bright energetic tone, brisk pace.` |
| British | `Standard British English accent (Southern England), warm confident tone, natural conversational pace.` |
| Australian | `Australian English accent, friendly relaxed tone, natural conversational pace.` |
| Softer, intimate | `Neutral American English accent, low warm tone, slow measured pace, speaking quietly.` |

Rules:

- Pick one line from the table (or write one in the same shape) and reuse it **verbatim** in the
  Video and Audio Prompt for all seven scenes. It is part of the frozen set, like the bible.
- Never write a gendered voice descriptor that contradicts the character in the image.
- Never name a real person's voice, a celebrity, or a public figure. Describe the sound, not a human.
- Keep every Actor 1 Script line **under 100 characters** — the dialog enforces this and rejects
  longer scripts. That's roughly 12–15 spoken words, about 5–7 seconds at conversational pace.
  Over-writing the line is the most common cause of a rejected submission.
- End the Video and Audio Prompt with a room-sound clause (`Quiet indoor room tone, no music.`).
  Silence is not a default; if you don't specify the acoustic, scenes can arrive with mismatched
  ambience.
- In the Lipsync dialog's Video Prompt, match the scene's emotion tag: name the actor, then direct the
  performance in plain language ("she excitedly says it", "he whispers, furious", "voice breaking with
  sadness"). Any emotion, any phrasing, including physical performance, is fine there — it's the one
  field in this flow meant to vary scene to scene.

For talking scenes, tick **Lipsync HD Video** — in this workflow that means every scene. Its verified
in-app hint: *"works best for Human, Photorealistic (Cinematic), or 3D images that include a person"*
— which is exactly the configuration this workflow uses (Image Type: `human`). Do not tick
**Video Only (No Sound)** for any scene where the character speaks.

---

## 5. Why close-ups win

The platform tells you directly. Ticking **Use Consistent Character** displays:

> "For best results, please only use close up shots"

Take that literally. Beyond it being the vendor's own guidance, the mechanics are simple: a
shoulders-up frame gives the model a small, tightly-constrained set of pixels to keep stable — face,
hair, collar, a soft wall. A medium or wide shot adds hands, torso proportions, full garment cut,
furniture, floor, and room geometry, and every one of those is a fresh chance to differ from the last
clip. Vertical 9:16 compounds the benefit: the frame is already narrow, so a close-up fills it and
there is almost no background left to drift.

Practical consequences:

- Every scene ends with `shoulders-up framing, camera at eye level, static camera`.
- Hands stay out of frame. Say so in the video prompt when there's any risk.
- No camera moves. No pans, dollies, zooms, orbits, or handheld. A camera move is a request to render
  background that was never described — see the drift table below.
- If you genuinely need visual variety, get it from **expression and head angle**, not from framing.
  A slight frown, a raised brow, a lean-in, a single nod, a small laugh. Seven scenes of the same shot
  with seven different expressions reads as intentional; seven different framings reads as broken.

---

## 6. Worked example — 6 scenes, ~60 seconds

Topic: *why your morning routine isn't working*. Vertical 9:16, Image Type `human`, Use Consistent
Character ON, Reference Photo = the saved hero image, Lipsync HD Video ON, Manual Video Length 10s,
public gallery OFF. (This example predates the emotional-wave requirement in `SYSTEM_PROMPT.md`; for a
7-scene example that also demonstrates the emotional wave, see
`references/examples/rama-ravana-final-battle.md`.)

### The frozen character bible

Written once. Pasted verbatim as the opening of all six Image Prompts.

```
Close-up portrait of a 34-year-old man, medium build, warm olive skin, short black hair with a neat
fade, trimmed dark beard, thick dark eyebrows, brown eyes, a small mole on his left cheek, wearing a
charcoal-grey crew-neck sweater over a white t-shirt collar, no jewellery and no glasses. He is
seated in a small home office with a plain warm-grey wall directly behind his head and a blurred
brass floor lamp glowing in the top-left of the frame. Soft warm key light from the front-left,
gentle shadow on the right side of his face. Shallow depth of field, shoulders-up framing, camera at
eye level, static camera.
```

### The frozen voice line (Video and Audio Prompt, every scene)

```
Neutral American English accent, warm confident tone, natural conversational pace.
```

### Scene 1 — hook

**Image Prompt**
```
[BIBLE VERBATIM]
He looks straight into the camera, eyebrows slightly raised, expression direct and open.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, small natural head movements, hands stay out of frame.
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```
**Create Lipsync Audio dialog → Video Prompt**
```
Actor 1 is the man in the charcoal-grey sweater. He speaks with direct, open confidence.
```
**Actor 1 Script** (72 chars)
```
Your morning routine isn't broken. You just built it for someone else's life.
```

### Scene 2 — the confession

**Image Prompt**
```
[BIBLE VERBATIM]
He gives a small tired half-smile, head tilted a few degrees to the right.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, a small shake of the head, hands stay out of frame.
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```
**Create Lipsync Audio dialog → Video Prompt**
```
Actor 1 speaks with a small tired half-smile, voice honest and a little rueful.
```
**Actor 1 Script** (82 chars)
```
I woke at five for a year. Cold plunge, journal, gratitude list. Exhausted by ten.
```

### Scene 3 — the real problem

**Image Prompt**
```
[BIBLE VERBATIM]
He leans a few inches toward the camera, expression serious, jaw set.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, leaning in slightly, hands stay out of frame.
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```
**Create Lipsync Audio dialog → Video Prompt**
```
Actor 1's voice turns serious and firm, leaning in with conviction.
```
**Actor 1 Script** (87 chars)
```
The problem wasn't the wake-up time. I spent my sharpest hours on other people's email.
```

### Scene 4 — the change

**Image Prompt**
```
[BIBLE VERBATIM]
He nods once, mouth closed, a small confident smile forming.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, one firm nod, hands stay out of frame.
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```
**Create Lipsync Audio dialog → Video Prompt**
```
Actor 1 speaks with a firm, decisive nod in his voice, confident and resolved.
```
**Actor 1 Script** (69 chars)
```
So I flipped it. First thing I touch is the task that scares me most.
```

### Scene 5 — the proof

**Image Prompt**
```
[BIBLE VERBATIM]
He raises his eyebrows and gives a small open-mouthed laugh, eyes creasing.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, brief light laugh, hands stay out of frame.
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```
**Create Lipsync Audio dialog → Video Prompt**
```
Actor 1's voice lightens with a small proud laugh, pleased and energized.
```
**Actor 1 Script** (80 chars)
```
It took nine days to feel normal. By week three I finished the hard thing first.
```

### Scene 6 — the call to action

**Image Prompt**
```
[BIBLE VERBATIM]
He looks straight into the camera, expression calm and steady, chin slightly lowered.
```
**Video and Audio Prompt**
```
He speaks directly to the camera, still and steady, hands stay out of frame.
Neutral American English accent, warm confident tone, natural conversational pace.
Quiet indoor room tone, no music.
```
**Create Lipsync Audio dialog → Video Prompt**
```
Actor 1 speaks calm and steady, warm and inviting, direct to camera.
```
**Actor 1 Script** (85 chars)
```
Pick one task tonight. Do it first tomorrow. That's the whole routine. Try nine days.
```

### What changed across the six scenes

| Frozen (identical in all 6) | Variable (changes each scene) |
|---|---|
| Entire character bible | The single action/expression line at the end of the Image Prompt |
| Location, wall, lamp, lighting, shadow | The motion sentence in the Video and Audio Prompt |
| Framing and camera clause | The delivery direction in the Lipsync dialog's Video Prompt |
| Accent / tone / pace line (Video and Audio Prompt) | The Actor 1 Script line and its emotion |
| Room-sound clause | — |
| Reference Photo | — |

Six scenes at ~10s each lands at roughly 60 seconds. If the target runtime shifts, change the number
of scenes, not the per-clip length — the 10s ceiling is a model limit, not a preference.

---

## 7. Drift triggers — the things that break the character

Every row below is a real failure mode. Scan your prompts for all of them before you queue the
first batch.

| Trigger | What actually happens | Fix |
|---|---|---|
| **Changing wardrobe words** — "sweater" in scenes 1-3, "jumper" or "top" or "pullover" later | The garment re-renders as a different item, often a different colour | One noun, one colour, chosen once. Copy-paste, never retype |
| **Dropping a wardrobe detail** — omitting "over a white t-shirt collar" in one scene | The neckline changes; the collar vanishes or becomes a v-neck | The bible never shrinks. Every scene gets the full block |
| **Changing location words** — "home office" becomes "study" or "room" | New furniture, new wall colour, new depth | One location noun for the whole video |
| **Vague pronouns and unnamed nouns** — "the light", "some plants", "his desk" | The model invents a fresh instance each time | Every noun gets a describing adjective and, for background objects, a frame position |
| **Camera moves that reveal background** — pans, dollies, orbits, zoom-outs, "handheld" | The model must render room it was never given, and improvises differently each clip | `static camera` in every Image Prompt. No motion verbs applied to the camera |
| **Standing up, walking, turning away** | Leaves the close-up; body proportions and full garment appear and diverge | Action lines describe expression and head movement only |
| **Hands entering frame** — gesturing, pointing, holding an object | Hands are the highest-variance region; count and shape wobble between clips | `hands stay out of frame` in the video prompt |
| **Time-of-day language** — "later", "that evening", "the next morning" | Lighting and shadow direction flip | Keep the same lighting clause. Never reference elapsed time in the prompt |
| **Adding props mid-sequence** — a mug, headphones, a phone | The prop persists into later clips inconsistently, or displaces the framing | No props. If a prop is essential it goes in the bible from scene 1 |
| **Rewriting the bible for "variety"** | Paraphrase is a new description. The model reads it as a new person | The bible is copy-paste only. Variety comes from the action line |
| **Changing the accent/tone line between scenes** | Audible voice shift mid-video — as damaging as a face shift | Freeze the voice line in the Video and Audio Prompt alongside the bible |
| **Writing dialogue into the Video and Audio Prompt** | Risks closed-mouth narration — audio plays, lips don't move | Speech only in Actor 1 Script, in the Create Lipsync Audio dialog |
| **Letting the Actor 1 Script line run long** | The dialog rejects it outright once it exceeds 100 characters | Under 100 characters, counted, for every scene |
| **Leaving prompt auto-enhancement on for scene prompts** | The enhancer rewrites your text before generation, which can substitute its own wardrobe and background wording | See the note below |

### Note on the auto-enhance checkboxes (partly unverified)

Two verified controls exist: **Automatically enhance my image prompt** (verified ON by default) and
**Automatically enhance my video prompt**. Auto-enhance is useful for the *first* hero image, where
you want the model to embellish freely. It works against you once the character is locked, because an
enhancer that rewrites your frozen block is by definition producing a different block each time.

**Unverified:** the exact rewriting behaviour of these enhancers, and whether turning them off
measurably reduces drift, were not observed in recon. Handle defensively at runtime:

1. Leave enhancement **on** for the hero-image generation.
2. Before generating scene videos, screenshot the modal, locate the two enhance checkboxes, and
   untick them.
3. If the checkboxes are not present or not clickable in the current build, proceed anyway and
   compare scene 1 and scene 2 outputs; if the character visibly differs, note it and continue —
   do not stop to ask the customer.

### Runtime consistency check (defensive, unverified signals)

After the first batch completes, look at the results. If a clip shows a different garment,
different wall, a visibly different face, or a closed mouth while audio plays, regenerate that one
clip with the identical prompts before continuing — the fault is usually sampling variance or a
field mix-up, not the prompt itself. Do not edit the bible in response to one bad clip. There is no
verified in-app "consistency score" or similar signal; judge by eye from a screenshot.

---

## 8. Two-person scenes

**Reference Photo 2** exists in the modal and is marked Optional — it is the second character slot.
This workflow does not use it by default. If a video genuinely needs two people, both characters need
their own full bible, both bibles go in the same Image Prompt, the framing clause changes to a
two-shot (which contradicts the close-up guidance and will drift more), and the Create Lipsync Audio
dialog's "Add Actor 2" reveals an Actor 2 Script field. Default to one character.

---

## 9. Pre-flight checklist

Before queuing any batch, confirm all of these against your scene prompts:

- [ ] The character bible string is **byte-identical** in every Image Prompt
- [ ] Only the final action line differs between Image Prompts
- [ ] No Image Prompt contains speech, dialogue, or audio words
- [ ] No Video and Audio Prompt contains a quoted spoken line — dialogue lives only in Actor 1 Script
- [ ] Every Video and Audio Prompt has: motion sentence, voice line, room-sound clause
- [ ] The voice line (Video and Audio Prompt) is byte-identical in every scene
- [ ] Every Actor 1 Script line is **under 100 characters**
- [ ] Every scene's Lipsync dialog Video Prompt directs delivery/emotion, with no dialogue text in it
- [ ] No camera-move verbs anywhere
- [ ] No hands, no props, no standing, no walking
- [ ] Aspect is **Vertical 9:16**, Image Type is **human**
- [ ] **Use Consistent Character** is ticked and the Reference Photo is set to the saved hero image
- [ ] **Lipsync HD Video** is ticked for every scene
- [ ] **Share this in the public gallery** is **unticked**
