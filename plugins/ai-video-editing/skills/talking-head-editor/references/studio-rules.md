# Studio Rules: Talking-Head Videos

Copy this file into `CLAUDE.md` (Claude Code) or `AGENTS.md` (Codex) in your video folder and adapt it. Your agent reads it at the start of every session.

## Principles
- Never change, invent or paraphrase what I said.
- Every step ends with proof. Never move on without showing evidence.
- Ask before anything irreversible or creative that I haven't approved.

## Workflow (always in this order)
setup check -> transcribe -> cut and lock -> brand -> storyboard (approval) -> build scenes and face layouts -> captions -> sound -> render -> final check

## Tools
- Render with HyperFrames. Run "npx hyperframes check" before every render.
- Face detection with OpenCV's Haar detector (OpenCV below version 5).

## Cutting
- Cut in the gaps between words, confirmed in the audio, never inside a word.
- Short fades (20 to 30 ms) on every audio segment, joined with concat. Never acrossfade.
- Keep about 0.2 s of breath between sentences. Remove false starts, keep the last complete take.
- After CUT.lock exists, never change the timing. Time everything against transcript-cut.json.

## Design
- Read edit/brand/brand.md before designing anything.
- One sentence, one idea, one visual. Continue a visual when the thought continues.
- Elements appear on their spoken word, within 3 frames.
- Say it, see it: everything I name (each item of a list, each tool, format, step or number) gets its own small, purpose-drawn mini-graphic (a mini slide with bars for presentations, a player for video, a browser window for websites) that animates when I say it. No generic icons, no emoji.
- Only use my logos, my screenshots and approved facts. Never redraw a logo.
- Real motion graphics, never static slides: kinetic headlines, building cards, check-off grids, typed interface mockups, drawing diagrams (see the motion library).
- At least two animated beats per scene; nothing stands still for more than about 2 seconds while I speak.
- One motion language: blur-in entrances, blur dissolves between scenes. No bounce, no spin, no stock-footage look.

## Face
- Three layouts: hero (face right, fading into the background), split (portrait card left), circle (small, bottom left). Morph between them, never cut.
- Never cover or awkwardly crop my face. Layout changes only at sentence boundaries.
- Face-first at least every third or fourth sentence.

## Captions
- Word for word, phrase-level (two to four words), white and bold, inside safe zones, never over my face.
- Emphasis colour for tool names and one key term per sentence at most.

## Sound
- Music 18 to 24 dB below the voice, ducked under speech.
- Final files at -14 LUFS integrated, true peak at or below -1 dBTP, two-pass loudnorm, audio at 48 kHz.

## Exports
- 16:9 at 1920x1080, 30 fps. 9:16 Reel at 1080x1920, re-laid out, not cropped.
- Run the 15-point final check before showing me any final file.

## Lessons learned
- (Add one line here after every fix, so the next video avoids it.)
