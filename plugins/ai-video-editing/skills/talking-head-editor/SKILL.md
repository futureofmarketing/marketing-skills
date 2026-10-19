---
name: talking-head-editor
description: Turn one raw talking-head take into a polished 16:9 video and a 9:16 Reel with real motion graphics (kinetic headlines, building cards, check-off grids, typed interface mockups, system maps), word-synced captions, brand styling and music, using ffmpeg, a local Whisper model and HyperFrames. Also builds a second-language version of a finished video from a new take. Use when someone wants to edit, cut, caption, brand or add motion graphics to a recording of themselves speaking to camera, asks for a Reel, Short or TikTok from such a recording, or wants the same video again in another language.
---

# Talking-Head Editor

You are the person's video editor and motion designer. They record one take of themselves talking to camera. You turn it into a finished video with real motion graphics, captions and music, plus a vertical Reel. The target look and every pattern are in `references/motion-library.md`: read it before Step 5. The result must feel produced by a motion designer: something meaningful moves every one to two seconds, every element lands on the word that names it, and no scene is a slide placed next to a face. The person directs in plain language and approves at fixed gates. They have no video skills, so never assume editing knowledge.

## The one rule

**Every step ends with proof.** Never move on until you have shown the evidence named for that step: a table, a transcript, a report, a still frame, a measurement. If you can't produce the proof, say what's missing instead of guessing.

There are three human gates. At each one, stop and wait for an explicit "approved":

1. After the transcript and glossary (Step 2)
2. After the rough cut (Step 3)
3. After the storyboard (Step 5)

Also stop for approval of the brand board (Step 4), the 10-second sample (Step 6) and the contact sheet (Step 6).

## Before you start

- Make sure the HyperFrames agent skills are installed in the project. If not, run `npx skills add heygen-com/hyperframes -y` and ask the person to start a new session so they load.
- Read `references/studio-rules.md` and `references/motion-library.md`. If the project has no `CLAUDE.md` (or `AGENTS.md` for Codex) yet, offer to create one from the studio rules so they apply in every session.
- Ask once, up front, in one message:
  1. The spoken language of the clip (and whether it mixes in English phrases).
  2. **Where the video will run:** stage or event screen, LinkedIn, YouTube, website, or a Reel feed. This decides captions, the ending and the formats (see Step 9).
  3. Where the brand files, logos and screenshots are.
  4. Whether there is a licensed music track.
  5. "Go all out, or keep it calm?" The brand sets the look; the person sets the ambition.

  Do not ask anything else until it matters.
- If the person is about to record and hasn't yet, share `references/recording-guide.md` first.

## Folder layout

```
edit/
  source/   original clip, never modified
  work/     transcripts, glossary, cut map, storyboard, face track
  brand/    brand.md, logos, fonts, screenshots, brand board
  music/    licensed track(s)
  video/    HyperFrames project (16:9), plus the 9:16 composition
  render/   rough cut, sample, final files, captions.srt
  qa/       clip report, cut check, audio report, contact sheets, final check
```

## Workflow

### Step 0: Setup check

Check ffmpeg, ffprobe, Node.js 22 or newer, npx, Python 3 with numpy, and run `npx hyperframes doctor`. Its ✗ marks on optional extras (text-to-speech, music generation, Docker) don't matter for this workflow; say so, so the person isn't alarmed. Check that a word-level speech-to-text engine is available: `npx hyperframes transcribe` needs whisper.cpp (`brew install whisper-cpp` on macOS) or Parakeet (`npx hyperframes models install parakeet`); faster-whisper in Python is the fallback. Check which OpenCV version is installed. Show a table (tool, status, version, what it's for). Propose install commands for the person's operating system, including `npx hyperframes browser ensure` if HyperFrames' Chrome is missing. Install nothing without an OK.

**Pin the HyperFrames version.** Note the version `doctor` reports and use that exact version for the whole project (`npx hyperframes@<version> …`, or the scripts `hyperframes init` writes into `package.json`). An unpinned `npx --no-install hyperframes` can resolve a newer release that isn't cached and stop with "npx canceled due to missing packages".

Proof: a table with every tool installed.

### Step 1: The take

The person records one horizontal take (1080p or higher, 45 to 90 seconds). Nothing to do here except point them to `references/recording-guide.md` if they haven't recorded yet.

### Step 2: Source check, transcript and glossary

1. Create the folder layout. Copy the clip into `edit/source/` and never modify that copy.
2. **Look at the clip before anything else.** Export three stills (start, middle, end) and view them. Confirm it is a camera take of the person, not a screen recording with a webcam bubble, and that nothing private is visible (open apps, chat windows, file names, notifications). If the person supplies a corrected file, compare the audio streams (`ffmpeg -i in.mp4 -map 0:a -f md5 -`): identical audio means the transcript and cut can be reused as they are.
3. Run ffprobe and write `edit/qa/clip-report.md`: duration, resolution, frame rate (compare `r_frame_rate` with `avg_frame_rate`; a mismatch means variable frame rate, so convert to constant frame rate while cutting), codecs, audio sample rate and channels, plus loudness from ffmpeg's `ebur128` filter.
4. Extract the audio as 16 kHz mono WAV. Detect the language if it's unclear (`whisper-cli -m <model> -f audio.wav -l auto -dl` prints "auto-detected language").
5. Transcribe with word-level timestamps: `npx hyperframes transcribe <wav> --model large-v3 --language <code> --json`. The valid models are `tiny.en`, `base.en`, `small.en`, `medium.en` and `large-v3`. For any language other than English use `large-v3`, never a `.en` model. If the command fails (it may only print "Command failed"), use faster-whisper with `word_timestamps=True` and the same language.
6. **Guard against Whisper's two typical failures:**
   - **Repetition loops** on longer clips (the same sentence repeated many times). If any phrase repeats unnaturally, split the audio at real silences into chunks of 15 to 20 seconds, transcribe each chunk, and stitch the words back together with each chunk's start offset.
   - **Invented words on breaths and wrong word times.** Whisper can be off by up to a second at the start of a clip and can "hear" a word in a breath or lip noise. Never place cuts from word times alone (see Step 3).
7. Write `edit/work/transcript.json` (every word with start and end) and `edit/work/transcript.txt` (one sentence per line).
8. Write `edit/work/glossary.md` with every name, brand and technical term, and flag words that look misheard (for example "Cloud Code" for "Claude Code", "like a bro" for "like a pro"). If the take mixes languages, re-transcribe those spans in the other language before the gate.

Never change what the person actually said. A script, if they gave one, is only a reference.

Proof and gate: show the first and last 10 words with their times, the word count, the full text and the flagged words. Wait for corrections and "approved".

### Step 3: Clean cut

1. **Find cut points on the waveform, not in the transcript.** Compute a 10 ms RMS envelope of the 16 kHz audio (numpy is enough), find the noise floor (10th percentile), and list every stretch below about -45 dBFS (or floor + 6 dB, whichever is higher) that lasts 0.25 s or more. Those stretches are the only places you may cut. Use the transcript only to know which words lie on either side.
2. Remove the lead-in silence and any pre-take breath, keeping 0.1 to 0.2 s before the first word. Shorten pauses longer than about 0.4 s to 0.2 to 0.35 s. Leave pauses under 0.35 s alone: removing two or three frames from a talking face shows as a visible jump. Remove false starts and repeated sentences, keeping the last complete version. Never reorder sentences or drop a complete sentence without asking.
3. Cut on whole frames of the delivery frame rate (30 fps): convert the source with `fps=30` before `trim=start_frame=…:end_frame=…`, and cut the audio at the matching sample (`atrim=start_sample=…`, 48 kHz). Give every audio segment a 5 to 30 ms fade at the cut and join with `concat`. Never use `acrossfade`: it shortens the audio at every cut and drifts out of sync. Never speed up or stretch anything.
4. End 0.8 s after the last word on real footage (don't freeze a frame if the recording continues).
5. Write `edit/work/cut-map.json` (kept segments with source and edit in/out in seconds and frames, removed segments with reasons) and `edit/render/rough-cut.mp4` (no graphics, no music), encoded as H.264 with a keyframe every second (`-g 30 -keyint_min 30`) and 48 kHz AAC, so it seeks cleanly in HyperFrames. Confirm audio and video have exactly the same length and the frame count is duration × 30.
6. **Prove nothing got clipped.** Transcribe each kept piece on its own, and separately 1.5 s either side of every cut, and compare word for word with the transcript. Write `edit/qa/cut-check.md`. A difference far from any cut is recognition noise, not a cut error: re-check that spot with a second pass. If a word is doubled in one pass and single in another (for example "alles. Alles,"), ask the person which they said. Save the word-level transcript of the cut as `edit/work/transcript-cut.json`. Every later step is timed against this file.

Fix problems and re-check until clean.

Proof and gate: a clean cut check. Ask the person to watch the rough cut once with headphones. After "approved", create `edit/work/CUT.lock`. From then on, never change the cut's timing except to fix a clipped or doubled word.

### Step 4: Brand file

If the `brand-setup` skill is installed and `edit/brand/brand.md` doesn't exist yet, run it now; it covers this whole step. Otherwise:

Interview the person one question at a time, then write `edit/brand/brand.md`: colours with hex codes and roles (background, primary text, secondary text, accent, emphasis), a headline font and a label font (free Google Fonts if needed, stored locally as woff2 with an `@font-face` each), which logos, screenshots and product images to use where, a persistent header (for example name and URL), chapter label style, motion values (entrance duration and ease, exit duration, blur amount), banned effects, an exceptions list (see below), and one or two references with what they like about them.

Never redraw or recreate a logo. Use only files the person supplied. If the person's own assets use a colour that isn't in the brand (for example an orange name pill), translate it to the brand palette and say so.

Build a one-page brand board as a small HTML page, capture it with `npx hyperframes snapshot` or a headless browser, and save `edit/brand/brand-board.png`.

Proof: the brand board, approved.

### Step 5: Storyboard

Write `edit/work/storyboard.md` as a table with one row per sentence: number and chapter label, the sentence as spoken, start and end in the cut, the motion pattern from `references/motion-library.md`, every animated beat with the spoken word that triggers it and its time from `transcript-cut.json`, and the face layout (hero, split or circle).

Rules:

- Only words actually said, approved facts and files in `edit/brand/`. On-screen text may shorten a sentence ("Not a video pro.") but must never add a claim.
- One idea per visual; continue a visual when the thought continues. A card may stay for several sentences if its content keeps building.
- **Say it, see it.** Every concrete thing the person names (each list item, tool, channel, format, step or number) gets its own visual element that appears or lights up on its spoken word, within 3 frames. For named items use a purpose-drawn mini-graphic from the motion library, never a generic icon or emoji.
- **Meta moments are literal.** When the person describes what the video itself does ("I just say subtitles in pink and it happens"), make it happen on screen at that word, even if it briefly breaks a brand rule. Record the exception in brand.md and ask first.
- At least two animated beats per scene; nothing stands still for more than about 2 seconds while the person speaks. **No card enters empty:** if the first named thing comes more than about a second after the card appears, open the card with a headline that lands on the phrase being spoken.
- Open and close in hero layout with a kinetic headline. Face-first at least every third or fourth sentence. Don't use the same pattern more than twice in a row.
- Plan the ending for the destination: a call-to-action close (URL types in, cursor clicks) for stage and LinkedIn, plus an end-screen hold of about 20 seconds for YouTube.

Build nothing yet.

Proof and gate: the storyboard, approved line by line.

### Step 6: Build scenes and place the face

1. **Measure the face.** Detect it in the rough cut (at least 5 samples per second) with OpenCV's Haar face detector and write `edit/work/face-track.json`. OpenCV 5 no longer ships the Haar models: if the installed version is 5 or newer, install `opencv-python-headless<5` into a separate environment first. Always confirm the result on two or three stills with a grid drawn over them, and record the face centre and the head box from hairline to chin in source pixels.
2. Create the HyperFrames project: run `npx hyperframes init video --non-interactive --example blank --resolution landscape` inside `edit/` (let it create `edit/video/` itself; don't create that folder beforehand), 1920x1080 at 30 fps. Use the rough cut as the base `<video>`. Either keep its own audio (`data-has-audio="true"`, not muted) or mute it and add the voice as a separate `<audio>`; never both. Follow hyperframes-core for the composition contract.
3. **Drive the face crop with simple, checkable maths.** Each layout is a clipping box (left, top, width, height, corner radius) holding an inner wrapper with a fixed translate and scale; the timed `<video>` sits inside that wrapper at its native pixel size. Never put timing attributes on the wrapper. For a box of height H and a source of height h at scale s with vertical offset ty, the video must cover the box: `ty ≤ 0` and `ty + h·s ≥ H` (the same for x and width). Pick s from the head height you want, centre the face, then clamp ty and tx to that rule. A gap at the edge of a face card is the most common bug here.
4. Build one scene per storyboard row, following `brand.md` and the pattern specs, persistent chrome and motion language in `references/motion-library.md`. For a film of up to about a dozen short scenes, one `index.html` with one timeline is fine; split into sub-compositions when it grows beyond that. Search the HyperFrames registry before hand-building a named visual. Apply the face layouts:
   - **Hero:** the footage fills the right part of the frame at full height and fades into the background on its left edge (a CSS mask gradient over 300 to 400 px). The headline sits in its own column on the left, **entirely outside the fade**: white type over the faded edge of a bright wall fails contrast. About 600 px wide at 1920 is safe; reflow the headline into more, shorter lines rather than widening the column. If the source doesn't cover the frame height at the zoom you want, use the largest crop the source supports.
   - **Split:** face in a rounded portrait card in the left third, a glass panel with the visual on the right.
   - **Circle:** face in a small circle bottom left, the visual takes a wider panel.
   Morph the face frame between layouts (animate the clipping box and the inner wrapper together) instead of cutting.
5. Keep text and logos inside the title-safe area. Never cover the face or crop through chin or forehead. Change layouts only at sentence boundaries. Never zoom past what the source resolution supports.
6. **Engineering rules that prevent the usual bugs:**
   - Typed text: wrap each character in a span with `display: none` and reveal it with `display: inline`. Hiding with opacity keeps the invisible characters' width and the caret sits far to the right.
   - `fromTo` applies its from-values at frame 0. Anything whose from-state is visible (a click ripple, a highlight ring) needs `immediateRender: false`, or it shows in the first frame.
   - Write every `<audio>` (music and each sound effect) as a static tag in the HTML with an `id`; don't create media from script.
   - Scope CSS for SVG and shared class names with ids (`#pipeline svg`, not `#card svg`), or a rule meant for one diagram moves a tick ring somewhere else.
   - Between cards, start the incoming card about 0.2 s after the outgoing card begins to leave, so they never sit on top of each other and the frame is never empty.
   - Drive every loop from the timeline with a finite repeat count. No wall-clock animation, no `repeat: -1`.
7. Add the persistent chrome now (header pill, typed chapter chip, background glow), so the stills show the real look.
8. Run `npx hyperframes check` and fix every error, every contrast finding and every overlap that isn't a deliberate transition. Then run the pacing check from the motion library.

Proof, in this order:
- **10-second sample:** render the first 10 seconds (temporarily set the root `data-duration` to 10) and show three stills from it. Wait for an OK on the look before building the rest.
- **Contact sheet:** one still per storyboard row at the middle of each sentence, combined into `edit/qa/contact-sheet.png`, approved.

### Step 7: Captions

Add word-synced captions timed from `transcript-cut.json`, with glossary spellings, built as one sub-composition marked `data-track-kind="captions"` or as a captions block in the main file if the project is a single file. Phrase-level: two to four words at a time, **one line**, bold, with a short fade in and out (no bounce).

- Never split a name or fixed phrase across two captions ("Claude Code", "B2B Communication Days", "video sales letters").
- While a kinetic headline already shows the spoken words in big type (usually the hook), leave the captions out for those words.
- In split and circle layouts, centre the captions under the visual; in hero layout, under the headline column. Never over the face.
- Over footage, give them the backing pill from brand.md. Tool names and the key term of a sentence may switch to the emphasis colour; at most one or two words per sentence.
- Word for word, never paraphrased. Also write `edit/render/captions.srt`.
- For YouTube, ask whether to burn captions in at all; many creators rely on the platform's subtitles there.

Proof: zero mismatches between captions and transcript, `npx hyperframes check` passing, three stills with captions.

### Step 8: Sound

Add the music from `edit/music/` as an `<audio>` element with an `id` (without one, HyperFrames renders it silent). Pick the section that fits the energy of the take and say why. If the track is shorter than the video, loop it with long crossfades (2 s or more) in ffmpeg before placing it. Keep the music 18 to 24 dB below the voice, duck it under speech, fade in over 1 s and fade it out only in the 0.8 s after the last word. Never fade the voice. Use the hyperframes-audio skill for fades and ducking: install its helper with `npm i -D @hyperframes/core`, then run its carve script naming both tracks explicitly (`--bed <music id> --voice <voice id>`). Write `data-automation` attributes as JSON in single quotes (or double-quoted with `&quot;` inside).

Sound effects are optional and quiet: a soft whoosh on a card change, a click on a button press, a pop when a tile lights up. Only on real on-screen events, each on its own track index so two never overlap on one track.

Render a draft (`npx hyperframes render --quality draft`), measure with `ebur128`, and write `edit/qa/audio-report.md`. A mono voice played on both stereo channels measures about 3 LU louder than the mono file; measure the render, not the source.

Proof: the audio report, with any spot where the voice is hard to understand.

### Step 9: Render the video and the Reel

1. Confirm the brand header and chapter labels are on every scene that needs them.
2. Run `npx hyperframes check` until it passes.
3. Render `edit/render/final-16x9.mp4` (1920x1080, 30 fps, `--quality delivery`). Tell the person it can take several minutes.
4. Normalise loudness with ffmpeg `loudnorm` in two passes to -14 LUFS integrated, true peak -1 dBTP, then `aresample=48000`. Write a fresh delivery encode: H.264 High, `yuv420p`, `scale=…:out_range=tv:out_color_matrix=bt709` plus `-color_range tv -colorspace bt709 -color_primaries bt709 -color_trc bt709`, AAC 48 kHz, `-movflags +faststart`.
5. Destination-specific endings:
   - **Stage or event screen:** no YouTube end cards; finish on the call-to-action close and hold its last frame for the 0.8 s tail. Check the file plays from a laptop without a network.
   - **YouTube:** reserve the last 15 to 20 seconds for end-screen elements (leave space, keep it calm).
   - **LinkedIn and websites:** burned-in captions, call-to-action close.
6. Build the vertical Reel (follow the `reel-format` skill if it's installed) as its own project in `edit/video/reel/` (its own index.html, assets linked from `../assets`): a 1080x1920 composition that re-lays out every scene. Never just crop. Render `edit/render/final-9x16.mp4` and encode it the same way.
7. If a file is too large for the target platform, add a compressed copy and report its size.

Proof: both files play from start to finish and match the preview.

### Step 10: Final gate

Run `npx hyperframes check --strict` on both compositions, then the check in `references/final-check.md`. Write `edit/qa/final-check.md` with pass or fail and evidence for each point, including a contact sheet at 1 frame per second of each final file that you have looked at yourself. Fix every fail, re-render, re-check. Show the person the report only when every point passes. Then ask them to watch it once with sound on the device it's meant for.

### Step 11: Another language (optional)

When the person records the same video in another language and wants it "exactly like the first one":

1. Run Steps 2 and 3 on the new take (new transcript, new locked cut). Measure the face again if the camera or resolution changed.
2. Copy the finished project. Swap the video, the voice and the music length; keep every scene, layout and animation.
3. **Re-time with an anchor table instead of by hand.** List pairs of matching moments, old word time → new word time (the start of each sentence, every named item, every word a beat lands on), strictly increasing in both columns. Map every timeline position through this table with piecewise-linear interpolation, for example by wrapping the timeline's `to`, `fromTo` and `set` so a numeric position `p` becomes `T(p)`. Map sound-effect start times the same way. Durations stay as they are.
4. Translate every piece of on-screen text, including text inside SVG mini-graphics (a "NEU" badge becomes "NEW") and typed strings. Reflow headlines that get longer. Keep supplied logos and images unchanged.
5. Write the captions fresh from the new language's word times; don't map them.
6. Run the full check, contact sheet and final gate again.

## When something goes wrong

Use `references/fixes.md`. After every fix, offer to add the lesson to the project's Studio Rules so the next video avoids it.

## Notes

- Everything here is free and open source apart from the person's AI plan and any music licence. Don't suggest paid video tools.
- HyperFrames renders through headless Chrome; a 75-second 1080p video takes several minutes on a laptop.
- The more precise the person's input (supplied logos, screenshots, references), the closer the result gets to what they have in mind. Ask for files rather than inventing look-alikes. An interface mockup (a typed prompt, a terminal) is a motion pattern, not a screenshot: recreate it only when the person asks for it, in the real product's colours, and never present it as a real capture.

---
Credit: Prof. Dr. Andreas Fuchs, [futureofmarketing.de](https://www.futureofmarketing.de)
