# Final Check: 15 Points

Run `npx hyperframes check --strict` on both compositions first. Then report pass or fail for each point in `edit/qa/final-check.md`, with evidence (timestamp, still frame or measurement).

1. Every spoken word is present once, nothing clipped or doubled at cuts, and audio and video stay in sync to the end.
2. Captions match the transcript word for word, glossary spellings are correct.
3. Every caption is readable: contrast, size, max two lines, inside the safe zone.
4. Every concrete thing the person names (list items, tools, formats, steps, numbers) has its own visual element, timed to its spoken word in transcript-cut.json (within 3 frames).
5. No scene contains a fact, number, quote or logo that isn't in the transcript, approved facts or brand folder.
6. The face is never covered or awkwardly cropped.
7. Layout changes happen only at sentence boundaries (a transition that starts up to 0.3 s before a sentence ends counts as at the boundary).
8. Colours and fonts match brand.md exactly.
9. The header and chapter labels are present and correctly numbered.
10. No banned effects from brand.md appear, and nothing stands still for more than about 2 seconds while the person speaks (scrub at 2 frames per second).
11. Loudness is -14 LUFS integrated (plus or minus 1), true peak at or below -1 dBTP.
12. Music never competes with the voice.
13. No black frames, frozen frames or flicker at transitions.
14. Start and end feel intentional: no abrupt cut at the beginning, a clean end card.
15. File specs are correct for each version (resolution, frame rate, codecs, size).

Fix every fail, re-render, and run the check again. Show the report only when all 15 points pass.
