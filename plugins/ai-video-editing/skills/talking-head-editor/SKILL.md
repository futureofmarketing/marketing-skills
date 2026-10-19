---
name: talking-head-editor
description: Turn one raw talking-head take into a polished 16:9 video and a 9:16 Reel with motion graphics, word-synced captions, brand styling and music, using ffmpeg, a local Whisper model and HyperFrames. Use when someone wants to edit, cut, caption, brand or add motion graphics to a recording of themselves speaking to camera, or asks to make a Reel, Short or TikTok from such a recording.
---

# Talking-Head Editor

You are the person's video editor and motion designer. They record one take of themselves talking to camera. You turn it into a finished video with real motion graphics (kinetic type, animated interface mockups, building diagrams), captions and music, plus a vertical Reel. The target look and every pattern are in `references/motion-library.md`: read it before Step 5. The result should feel produced by a motion designer, never like slides placed next to a face. They direct in plain language and approve at fixed gates. They have no video skills, so never assume editing knowledge.

## The one rule

**Every step ends with proof.** Never move on until you have shown the evidence named for that step: a table, a transcript, a report, a still frame, a measurement. If you can't produce the proof, say what's missing instead of guessing.

There are three human gates. At each one, stop and wait for an explicit "approved":

1. After the transcript and glossary (Step 2)
2. After the rough cut (Step 3)
3. After the storyboard (Step 5)

Also stop for approval of the brand board (Step 4) and the contact sheet (Step 6).

## Before you start

- Make sure the HyperFrames agent skills are installed in the project. If not, run `npx skills add heygen-com/hyperframes -y` and ask the person to start a new session so they load.
- Read `references/studio-rules.md` and `references/motion-library.md`. If the project has no `CLAUDE.md` (or `AGENTS.md` for Codex) yet, offer to create one from it so the rules apply in every session.
- Ask once, up front: the spoken language of the clip, where the brand files are, and whether there is a licensed music track. Do not ask anything else until it matters.
- If the person is about to record and hasn't yet, share `references/recording-guide.md` with them first.

## Folder layout

```
edit/
  source/   original clip, never modified
  work/     transcripts, glossary, cut map, storyboard, face track
  brand/    brand.md, logos, fonts, screenshots, brand board
  music/    licensed track(s)
  video/    HyperFrames project (16:9), plus the 9:16 composition
  render/   rough cut, final files, captions.srt
  qa/       clip report, cut check, audio report, contact sheet, final check
```

## Workflow

### Step 0: Setup check

Check ffmpeg, ffprobe, Node.js 22 or newer, npx, Python 3 and pip, and run `npx hyperframes doctor`. Its ✗ marks on optional extras (text-to-speech, music generation, Docker) don't matter for this workflow; say so, so the person isn't alarmed. Also check that Python can import faster-whisper and which OpenCV version is installed. Show a table (tool, status, version, missing). Propose install commands for the person's operating system, including `npx hyperframes browser ensure` if HyperFrames' Chrome is missing. Install nothing without an OK.

Proof: a table with every tool installed.

### Step 1: The take

The person records one horizontal take (1080p or higher, 45 to 90 seconds). Nothing to do here except point them to `references/recording-guide.md` if they haven't recorded yet.

### Step 2: Transcript and glossary

1. Create the folder layout. Copy the clip into `edit/source/` and never modify that copy.
2. Run ffprobe and write `edit/qa/clip-report.md` (duration, resolution, frame rate, codecs, audio sample rate) plus loudness from ffmpeg's `ebur128` filter.
3. Extract the audio as 16 kHz mono WAV. Transcribe with word-level timestamps using `npx hyperframes transcribe <wav> --model small --language <code>`. Set the language explicitly and, for any language other than English, never use a model ending in `.en`. If the command fails (it may only print "Command failed"), use faster-whisper with `word_timestamps=True` and the same language; if it can't open the file, load the WAV into a numpy array and pass that. If the take mixes languages (for example English phrases in a German talk), re-transcribe those spans in the other language before the gate.
4. Write `edit/work/transcript.json` (every word with start and end) and `edit/work/transcript.txt` (one sentence per line).
5. Write `edit/work/glossary.md` with every name, brand and technical term, and flag words that look misheard (for example "Cloud Code" for "Claude Code").

Never change what the person actually said. A script, if they gave one, is only a reference.

Proof and gate: show the transcript and flagged words. Wait for corrections and "approved".

### Step 3: Clean cut

1. Find cut points in the gaps between words in the transcript, then confirm each one in the audio. If you use `silencedetect`, start about 25 dB below the integrated loudness. If it finds no pauses (music or room tone under the voice), compute a 20 ms RMS envelope of the audio and cut at its minima inside the word gaps. Never cut inside a word.
2. Shorten pauses longer than 0.4 s and keep about 0.2 s of breath between sentences. Remove false starts and repeated sentences, keeping the last complete version. Never reorder sentences or drop a complete sentence without asking.
3. Give every audio segment a 20 to 30 ms fade in and out and join with `concat`. Never use `acrossfade`: it shortens the audio at every cut and drifts out of sync.
4. Write `edit/work/cut-map.json` (kept segments with source in and out, removed segments with reasons) and `edit/render/rough-cut.mp4` (no graphics, no music), encoded as H.264 with a keyframe every second (`-g 30 -keyint_min 30`) and 48 kHz AAC, so it seeks cleanly in HyperFrames. Confirm audio and video have exactly the same length.
5. Transcribe the rough cut again, compare it word by word with the kept sentences, and write `edit/qa/cut-check.md`. Differences that appear only in the re-transcription (recognition noise, not a cut error) are checked by listening to that spot or with a second pass. Save the new word-level transcript as `edit/work/transcript-cut.json`. Every later step is timed against this file.

Fix problems and re-check until clean.

Proof and gate: a clean cut check. Ask the person to watch the rough cut once with headphones. After "approved", create `edit/work/CUT.lock`. From then on, never change the cut's timing except to fix a clipped or doubled word.

### Step 4: Brand file

If the `brand-setup` skill is installed and `edit/brand/brand.md` doesn't exist yet, run it now; it covers this whole step. Otherwise:

Interview the person one question at a time, then write `edit/brand/brand.md`: colours with hex codes and roles (background, primary text, secondary text, accent, emphasis), a headline font and a label font (free Google Fonts if needed, stored locally), which logos, screenshots and product images to use where, a persistent header (for example name and URL), chapter label style, motion character in three adjectives, banned effects, and one or two references with what they like about them.

Never redraw or recreate a logo. Use only files the person supplied.

Build a one-page brand board as a small HTML page, capture it with `npx hyperframes snapshot` or a headless browser, and save `edit/brand/brand-board.png`.

Proof: the brand board, approved.

### Step 5: Storyboard

Write `edit/work/storyboard.md` as a table with one row per sentence: number and chapter label, the sentence as spoken, start and end in the cut, the motion pattern from `references/motion-library.md` (kinetic headline, building statement card, check-off grid, chat input, terminal or app window, timeline, system map, process pipeline, logo card, call-to-action close), every animated beat and the spoken word that triggers it, and the face layout (hero, split or circle).

Rules: only words actually said, approved facts and files in `edit/brand/`. One idea per visual; continue a visual when the thought continues. Every concrete thing the person names (each list item, tool, channel, format, step or number) gets its own visual element (tile, mini-graphic, chip, mockup or node) that appears or lights up on its spoken word, within 3 frames. For named items, use a purpose-drawn mini-graphic from the vocabulary in the motion library, never a generic icon or emoji; mark these words in the storyboard. At least two animated beats per scene; nothing stands still for more than about 2 seconds while the person speaks. Open and close in hero layout with a kinetic headline. Face-first at least every third or fourth sentence. Vary the patterns: don't use the same one more than twice in a row.

Build nothing yet.

Proof and gate: the storyboard, approved line by line.

### Step 6: Build scenes and place the face

1. Detect the face in the rough cut (at least 5 samples per second) with OpenCV's Haar face detector and write `edit/work/face-track.json`. OpenCV 5 no longer ships the Haar models: if the installed version is 5 or newer, install `opencv-python-headless<5` into a separate environment first.
2. Create the HyperFrames project: run `npx hyperframes init video --non-interactive --example blank --resolution landscape` inside `edit/` (let it create `edit/video/` itself; don't create that folder beforehand), 1920x1080 at 30 fps. Use the rough cut as the base `<video>` with its own audio (`data-has-audio="true"`, not muted). Follow hyperframes-core for the composition contract. Use talking-head-recut only for ideas about cards: this workflow re-frames the face, which talking-head-recut doesn't do.
3. Drive the face crop from `face-track.json`: for each layout, a clipping box holds an inner wrapper with a fixed translate and scale, and the timed `<video>` sits inside that wrapper. Never put timing attributes on the wrapper.
4. Build one scene per storyboard row as its own sub-composition, following `brand.md` and the pattern specs, persistent chrome and motion language in `references/motion-library.md`. Search the HyperFrames registry before hand-building a named visual. Apply the face layouts:
   - Hero: the face fills the right 55 to 65 percent of the frame at full height and fades softly into the background on its left edge; headline on the left. If that needs more than 1.2x zoom, use the largest crop the source supports.
   - Split: face in a rounded portrait card in the left third, a glass panel with the visual on the right.
   - Circle: face in a small circle bottom left, the visual takes the stage.
   Morph the face frame between layouts instead of cutting, and use blur dissolves between scenes.
5. Keep text and logos inside the title-safe area (10 percent in from every edge). Never cover the face or crop through chin or forehead. Change layouts only at sentence boundaries, with 0.4 to 0.6 s transitions. Keep the crop stable. Never zoom past what the source resolution supports.
6. Add the persistent chrome now (header pill, typed chapter chip, drifting background glow), so the contact sheet shows the real look. Run the pacing check from the motion library and `npx hyperframes check`, and fix everything. Capture one still per storyboard row with `npx hyperframes snapshot` at the middle of each sentence and combine them into `edit/qa/contact-sheet.png`.

Proof: the contact sheet, approved, plus a 10-second draft render (`npx hyperframes render --quality draft`) of the busiest scene so the person sees the motion, not just stills.

### Step 7: Captions

Add word-synced captions timed from `transcript-cut.json`, with glossary spellings, built as one sub-composition marked `data-track-kind="captions"` (captions placed directly in index.html fail `check --strict`). Phrase-level: two to four words at a time, one line, white and bold, with a short fade in and out (no bounce). Inside the title-safe area with the bottom edge at 90 percent of the height; in split and picture-in-picture layouts centre them under the visual, never over the face. Over footage, give them the backing pill from brand.md. Tool names and the key term of a sentence switch to the emphasis colour; at most one or two words per sentence. Word for word, never paraphrased. Also write `edit/render/captions.srt`.

Proof: zero mismatches between captions and transcript, `npx hyperframes check` passing, three stills with captions.

### Step 8: Sound

Add the music from `edit/music/` as an `<audio>` element with an `id` (without one, HyperFrames renders it silent). Pick the section that fits the energy of the take and say why. Keep music 18 to 24 dB below the voice, duck it further under speech, fade in over 1 s and out over 2 s. Let it come up slightly in deliberate pauses where the carve allows; if it can't, say so rather than forcing it. Use the hyperframes-audio skill for fades and ducking: install its helper with `npm i -D @hyperframes/core`, then run its carve script naming both tracks explicitly (`--bed <music id> --voice <video id>`). Write `data-automation` attributes double-quoted, with `&quot;` inside.

Render a draft (`npx hyperframes render --quality draft`), measure with `ebur128`, and write `edit/qa/audio-report.md`.

Proof: the audio report, with any spot where the voice is hard to understand.

### Step 9: Render the video and the Reel

1. Confirm the brand header and chapter labels are on every scene.
2. Run `npx hyperframes check` until it passes.
3. Render `edit/render/final-16x9.mp4` (1920x1080, 30 fps, delivery quality). Tell the person it can take several minutes.
4. Normalise loudness with ffmpeg `loudnorm` in two passes to -14 LUFS integrated, true peak -1 dBTP. For the 16:9 file, copy the video stream and add `aresample=48000`, since loudnorm resamples internally. The Reel is re-encoded instead, as `reel-format` specifies.
5. Build the vertical Reel (follow the `reel-format` skill if it's installed) as its own project in `edit/video/reel/` (its own index.html and compositions, assets linked from `../assets`): a composition at 1080x1920 that re-lays out every scene (face on top, visual below, captions inside the safe zone that social apps leave free of buttons). Never just crop. Render `edit/render/final-9x16.mp4` and normalise it the same way.
6. If a file is too large for the target platform, add a compressed copy and report its size.

Proof: both files play from start to finish and match the preview.

### Step 10: Final gate

Run `npx hyperframes check --strict` on both compositions, then the 15-point check in `references/final-check.md`. Write `edit/qa/final-check.md` with pass or fail and evidence for each point. Fix every fail, re-render, re-check. Show the person the report only when all 15 points pass.

## When something goes wrong

Use `references/fixes.md`. After every fix, offer to add the lesson to the project's Studio Rules so the next video avoids it.

## Notes

- Everything here is free and open source apart from the person's AI plan and any music licence. Don't suggest paid video tools.
- HyperFrames renders through headless Chrome; a 75-second video can take several minutes on a laptop.
- The more precise the person's input (supplied logos, screenshots, references), the closer the result gets to what they have in mind. Ask for files rather than inventing look-alikes.

---
Credit: Prof. Dr. Andreas Fuchs, [futureofmarketing.de](https://www.futureofmarketing.de)
