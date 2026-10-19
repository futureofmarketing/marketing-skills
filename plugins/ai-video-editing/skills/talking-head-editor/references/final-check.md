# Final Check: 20 Points

Run `npx hyperframes check --strict` on both compositions first: zero errors, zero contrast findings, and only deliberate overlaps. Then report pass or fail for each point in `edit/qa/final-check.md`, with evidence (timestamp, still frame or measurement). Look at a contact sheet of each final file at 1 frame per second yourself before you write the report.

## Words and timing
1. Every spoken word is present once, nothing clipped or doubled at cuts (checked 1.5 s either side of every cut), and audio and video stay in sync to the end.
2. The last 8 seconds, transcribed from the final file, contain the whole closing sentence and call to action.
3. Captions match the transcript word for word, glossary spellings are correct, no name is split across two captions.
4. Every caption is one line, readable (contrast, size) and inside the safe zone, never over the face.

## Content
5. Every concrete thing the person names (list items, tools, formats, steps, numbers) has its own visual element, timed to its spoken word in transcript-cut.json (within 3 frames).
6. No scene contains a fact, number, quote or logo that isn't in the transcript, approved facts or brand folder. Interface mockups are only those the person asked for.
7. All on-screen text is in the language of the video, including text inside mini-graphics.

## Picture
8. The face is never covered or awkwardly cropped, and every face frame is fully covered by footage (no gap at any edge, at rest or mid-morph).
9. Layout changes happen only at sentence boundaries (a transition that starts up to 0.3 s before a sentence ends counts as at the boundary).
10. No panel enters empty, and nothing stands still for more than about 2 seconds while the person speaks.
11. No stray element in the first frame, no empty frame and no two panels stacked at scene changes, no black frames, frozen frames or flicker.
12. Colours and fonts match brand.md exactly; every exception (real logos, a recreated interface, a meta moment) is listed in brand.md as approved.
13. The header and chapter labels are present and correctly numbered.
14. No banned effects from brand.md appear, and no movement uses linear easing.

## Sound
15. Loudness is -14 LUFS integrated (plus or minus 1), true peak at or below -1 dBTP, audio at 48 kHz.
16. The voice is never faded; the music never competes with it and fades only after the last word.
17. Audio and video have the same length (within 0.05 s), and the frame count equals duration × frame rate (within 1 frame).

## Delivery
18. Start and end feel intentional: no abrupt cut at the beginning, an ending that fits the destination (call-to-action close, or end-screen space for YouTube).
19. File specs are correct for each version: H.264, `yuv420p`, `color_range=tv`, bt709, resolution, frame rate, one AAC track, `+faststart`, a full decode with zero errors.
20. The size fits the target platform, or a compressed copy exists and its size is reported.

Fix every fail, re-render, and run the check again. Show the report only when all 20 points pass.
