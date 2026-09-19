# Example — "Rama and Ravana's Final Battle"

A worked example of the script-writing stage (`SYSTEM_PROMPT.md`, STEP 1) applied to a
mythological-retelling topic. This is a **spokesperson-format** video: one consistent
narrator character tells the story of the Ramayana's climactic battle directly to camera,
across 7 scenes riding the mandatory emotional wave. It does not attempt to depict Rama
and Ravana as two separate on-screen characters fighting — this workflow locks one face
per video, and two-person scenes are explicitly out of scope by default (see
`references/character-prompting.md` §8).

Follow this example's shape for any similar "retell a dramatic story" topic: one narrator,
one frozen Character Bible, one frozen voice line, 7 scenes each pushing the story forward
and the emotion along the wave.

---

## Character Bible (frozen, pasted verbatim into every Image Prompt)

```
A muscular 30-year-old Indian warrior-narrator with bronze skin, a sharp jawline, and
long black hair tied back, a thin scar above his right eyebrow, wearing ornate
gold-trimmed maroon warrior robes and a beaded necklace, standing before a dark stone
temple wall lit by flickering torchlight, warm amber light rising from below-left, deep
dramatic shadows, cinematic close-up framing.
```

## Voice line (frozen, identical in every Video and Audio Prompt)

```
Deep resonant Indian-accented English, commanding dramatic tone, deliberate
epic-storytelling pace.
```

## Settings

Vertical 9:16 · Image Type `human` · Use Consistent Character ON · Lipsync HD Video ON ·
Share this in the public gallery **OFF** (verify before every generation) · 7 scenes.

---

## Scene 1 — Shocked (hook + stakes)

**Image Prompt** = Character Bible verbatim + `Eyes wide with urgency, leaning slightly
toward the camera, one eyebrow raised in alarm.`

**Video and Audio Prompt** = `Static close-up, temple torchlight flickering behind him,
no camera movement.` + voice line (verbatim).

**Create Lipsync Audio dialog → Video Prompt** = `Actor 1 is the warrior-narrator in
maroon robes. He says the line with wide-eyed, urgent intensity, as if the stakes are
enormous.`

**Actor 1 Script** (72 chars): `Ravana had ten heads and no fear. Only Rama's arrow could end his reign.`

---

## Scene 2 — Angry (the villain / the problem)

**Image Prompt** = Bible + `Jaw clenched, brow lowered in anger, a hard stare directly at
the camera.`

**Video and Audio Prompt** = `Static close-up, torchlight now harsher and redder, no
camera movement.` + voice line.

**Lipsync dialog Video Prompt** = `Actor 1 speaks with simmering anger, jaw tight, voice
hardening with contempt for Ravana.`

**Actor 1 Script** (75 chars): `He stole Sita and mocked the gods. His arrogance would cost him everything.`

---

## Scene 3 — Sad (the cost of doing nothing / the darkest moment)

**Image Prompt** = Bible + `Eyes downcast, shoulders slightly slumped, a weary, doubtful
expression.`

**Video and Audio Prompt** = `Static close-up, torchlight dimming, shadows deepening, no
camera movement.` + voice line.

**Lipsync dialog Video Prompt** = `Actor 1's voice drops, quiet and weary, almost
defeated, eyes lowering with doubt.`

**Actor 1 Script** (65 chars): `Arrow after arrow, his heads grew back. Even Rama began to doubt.`

---

## Scene 4 — Surprised (the twist / discovery)

**Image Prompt** = Bible + `Eyes wide with sudden realization, lips parted, head tilting
forward in surprise.`

**Video and Audio Prompt** = `Static close-up, a single shaft of torchlight brightening
across his face, no camera movement.` + voice line.

**Lipsync dialog Video Prompt** = `Actor 1's eyes widen, voice quickens with sudden
realization and hushed excitement.`

**Actor 1 Script** (65 chars): `Then Vibhishana revealed it: the nectar hidden in Ravana's navel.`

---

## Scene 5 — Excited (the solution working)

**Image Prompt** = Bible + `Eyes blazing with determination, leaning in, a fierce excited
grin forming.`

**Video and Audio Prompt** = `Static close-up, torchlight flaring brighter behind him, no
camera movement.` + voice line.

**Lipsync dialog Video Prompt** = `Actor 1 speaks with fierce, driving excitement, leaning
forward, voice rising to a triumphant edge.`

**Actor 1 Script** (66 chars): `Rama loosed the Brahmastra, aimed straight for the one weak point.`

---

## Scene 6 — Happy (the transformation / victory)

**Image Prompt** = Bible + `A wide relieved smile, eyes bright, chin lifted in triumph.`

**Video and Audio Prompt** = `Static close-up, warm golden torchlight steady and calm, no
camera movement.` + voice line.

**Lipsync dialog Video Prompt** = `Actor 1 breaks into a relieved, joyful smile, voice
warm and triumphant.`

**Actor 1 Script** (60 chars): `Ravana fell. Sita was free. Good had finally conquered evil.`

---

## Scene 7 — Excited (resolve the open loop, CTA)

**Image Prompt** = Bible + `A warm confident smile, direct steady eye contact, chin
slightly raised, energetic.`

**Video and Audio Prompt** = `Static close-up, torchlight bright and celebratory, no
camera movement.` + voice line.

**Lipsync dialog Video Prompt** = `Actor 1 speaks with bright, energetic enthusiasm,
direct to camera, inviting the viewer in.`

**Actor 1 Script** (64 chars): `This is why Dussehra celebrates good over evil. Follow for more.`

---

## What changed across the seven scenes

| Frozen (identical in all 7) | Variable (changes each scene) |
|---|---|
| Entire character bible | The action/expression clause at the end of the Image Prompt |
| Location, torch wall, framing, static-camera clause | The one-line torchlight/mood description in the Video and Audio Prompt |
| Voice line (accent/tone/pace) | The delivery direction in the Lipsync dialog's Video Prompt |
| Reference Photo | The Actor 1 Script line and its emotion tag |

Emotional wave used: Shocked → Angry → Sad → Surprised → Excited → Happy → Excited,
matching the mandatory pattern in `SYSTEM_PROMPT.md` STEP 1(B) — no two consecutive
scenes share an emotion, the story flips from despair (scene 3) to hope (scene 4), and it
ends on the highest-energy positive beat flowing into the CTA. Every Actor 1 Script line
above is under the platform's 100-character limit.
