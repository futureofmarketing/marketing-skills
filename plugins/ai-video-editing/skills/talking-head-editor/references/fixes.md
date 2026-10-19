# Fixes for the Most Common Problems

Each fix is written as the person would say it. Apply it, verify it, and offer to add the lesson to the Studio Rules.

## 1. Captions drift out of sync
Re-derive all caption timings from `edit/work/transcript-cut.json`, not from the original source. If the drift grows over time, check whether audio and video in the rough cut have the same length. Re-check sync at the start, middle and end and show three stills with timestamps.

## 2. A word is clipped or doubled at a cut
Move that cut point into the nearest gap between words and confirm it in the waveform. This is the only timing change allowed under CUT.lock. Update `transcript-cut.json` and re-run the cut check for the whole video.

## 3. A scene shows something the person never said
Remove it and rebuild the scene using only the words from that sentence. Then scan every scene for content that isn't in the transcript, approved facts or brand folder, and list what you find.

## 4. The face is covered or badly cropped
Fix the layout using `face-track.json`. Re-render the contact sheet for the whole video and check every frame for the same problem.

## 5. It looks generic, not like the brand
Re-read `brand.md` and the reference images. List the five biggest differences between the current scenes and the references, propose a fix for each, and wait for an OK before rebuilding.

## 6. The music is too loud or the voice sounds muffled
Lower the music and strengthen the ducking under speech. Check the voice for over-processing and reduce it if needed. Measure loudness again and update the audio report.

## 7. The animations are too busy
Reduce motion: fewer elements per scene, slower and simpler entrances, no background movement while the person speaks. Show three before and after stills from the busiest moments.

## 8. The render fails or doesn't match the preview
Run `npx hyperframes doctor` and `npx hyperframes check`, read the output, and explain the cause in one sentence before fixing it. If HyperFrames' Chrome is missing, run `npx hyperframes browser ensure`. Render a 5-second draft of the problem section first, then the full video.

## 9. The transcript repeats the same sentence over and over
Whisper has fallen into a repetition loop. Split the audio at real silences (from the RMS envelope) into chunks of 15 to 20 seconds, transcribe each chunk on its own, and stitch the words together with each chunk's start offset. Show the stitched text before the gate.

## 10. The transcript has a word nobody said, or the first words are timed wrong
Whisper invents short words in breaths and lip noise ("Ja.", "Yeah.") and can be off by up to a second at the start. Check the level at that spot: real speech sits 20 to 30 dB above the floor, a breath only slightly above it. Remove the phantom word, and take every cut point from the waveform, never from word times.

## 11. A word appears doubled in one pass and single in another
That is recognition noise unless it sits on a cut. If it's far from any cut, the audio there is untouched: ask the person what they said and use their answer for the captions.

## 12. White text is hard to read at the start
The hero headline runs over the faded edge of the footage, where a bright wall shows through. Narrow the headline column so it ends before the fade starts, or move the footage further right. Reflow the headline into shorter lines. Re-run `npx hyperframes check` until the contrast finding is gone.

## 13. The cursor sits far to the right while text is being typed
The hidden characters still take up space. Hide them with `display: none` and reveal them with `display: inline`, not with opacity.

## 14. Something appears in the first frame that belongs to the end
A `fromTo` draws its from-values at frame 0. Add `immediateRender: false` to that tween (typically a click ripple, a glow or a highlight ring), re-render and look at frame 0.

## 15. A panel stands there half empty
The first item lands too long after the panel appears. Open the panel with a short headline that lands on the phrase being spoken, and let it make room when the build starts. Check every panel entry in the contact sheet.

## 16. There's a gap at the edge of the face card
The footage doesn't cover the clipping box. For each layout check `ty ≤ 0` and `ty + h·s ≥ box height` (and the same for x). Increase the scale or move the offset until it holds, at rest and mid-morph.

## 17. "npx canceled due to missing packages"
npx tried to fetch a newer HyperFrames release than the one that is cached. Use the project's pinned version (`npx hyperframes@<version> …` or the scripts in `package.json`) and allow the install once.

## 18. The person sends a corrected file
Compare the audio streams with `ffmpeg -i <file> -map 0:a -f md5 -`. If the audio is identical, keep the transcript and the cut and only swap the picture (then measure the face again). If it differs, start again at Step 2.
