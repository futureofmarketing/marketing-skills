# Changelog

## 1.1.0

Lessons from producing real talk videos (a 16:9 stage film, its vertical Reel and an English version of the same film) folded back into the skills.

### talking-head-editor
- Up front: asks where the video will run (stage, LinkedIn, YouTube, website, Reels) and how bold it should be; the ending and the captions follow from that.
- Setup: checks for a speech-to-text engine (whisper.cpp or Parakeet) and pins one HyperFrames version per project.
- Source check: looks at the clip before transcribing (camera take vs. screen recording, private windows, variable frame rate); compares audio checksums when a corrected file arrives.
- Transcription: fixed the model flag (`large-v3` for every language other than English; `small` doesn't exist); chunked transcription against Whisper's repetition loops; guards against invented words on breaths and wrong word times.
- Cutting: cut points come from an RMS envelope of the audio, not from word times; whole-frame cuts; short pauses left alone; every cut re-checked 1.5 s either side.
- Building: a 10-second sample before the full build; face-crop maths that guarantees the footage covers every face frame; the hero headline stays outside the footage fade; engineering rules for typed text, `immediateRender`, static audio tags, scoped SVG CSS and overlap-free scene changes; no panel enters empty.
- Captions: never split a name; no captions while a kinetic headline shows the words.
- Sound: voice never faded, music fades only after the last word, quiet event-bound sound effects.
- Delivery: endings by destination (stage call-to-action click, YouTube end-screen space), full delivery encode spec.
- New Step 11: re-build a finished video in another language with an anchor table that re-times every scene to the new take.

### references
- motion-library: brand-driven motion values, no-empty-panel and scene-change rules, four new patterns (negation, one big number, chapter question, meta moment), a sharper pacing check.
- final-check: 20 points, grouped by words, content, picture, sound and delivery.
- fixes: ten new fixes (Whisper loops, phantom words, doubled words, hero contrast, caret position, frame-0 artefacts, empty panels, face-card gaps, npx version errors, corrected source files).
- studio-rules and recording-guide: updated with the same lessons (record the camera, not the screen; say your structure out loud; how to record a second language).

### reel-format
- Concrete vertical layout (chrome, visual, caption and face bands), the hook with big type over a top-faded face, calmer in-panel pace, refined safe zone.

### brand-setup
- One look per video, translation of the person's own assets into the palette, local fonts for rendering, explicit motion values, an "Approved exceptions" section, optional portrait in the name pill, example values for the Deep Blue Glass preset.

## 1.0.0

First release: talking-head-editor, brand-setup, reel-format.
