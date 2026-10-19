# Studio Rules: Talking-Head Videos

Copy this file into `CLAUDE.md` (Claude Code) or `AGENTS.md` (Codex) in your video folder and adapt it. Your agent reads it at the start of every session.

## Principles
- Never change, invent or paraphrase what I said.
- Every step ends with proof. Never move on without showing evidence.
- Ask before anything irreversible or creative that I haven't approved.

## Workflow (always in this order)
setup check -> transcribe -> cut and lock -> brand -> storyboard (approval) -> build scenes and face layouts -> captions -> sound -> render -> final check

## Tools
- Render with HyperFrames, pinned to one version per project. Run "npx hyperframes check" before every render.
- Transcribe with large-v3 for any language other than English. Long clips are transcribed in chunks split at silences.
- Face detection with OpenCV's Haar detector (OpenCV below version 5), confirmed on gridded stills.

## Cutting
- Cut only in true silence found on the waveform (RMS envelope), never from word times alone, never inside a word.
- Cut on whole frames. Short fades on every audio segment, joined with concat. Never acrossfade, never speed anything up.
- Keep about 0.2 s of breath between sentences. Leave pauses under 0.35 s alone. Remove false starts, keep the last complete take.
- After cutting, transcribe 1.5 s either side of every cut and check for clipped or doubled words.
- After CUT.lock exists, never change the timing. Time everything against transcript-cut.json.

## Design
- Read edit/brand/brand.md before designing anything.
- One sentence, one idea, one visual. Continue a visual when the thought continues.
- Elements appear on their spoken word, within 3 frames.
- Say it, see it: everything I name (each item of a list, each tool, format, step or number) gets its own small, purpose-drawn mini-graphic (a mini slide with bars for presentations, a player for video, a browser window for websites) that animates when I say it. No generic icons, no emoji.
- Only use my logos, my screenshots and approved facts. Never redraw a logo.
- Real motion graphics, never static slides: kinetic headlines, building cards, check-off grids, typed interface mockups, drawing diagrams (see the motion library).
- At least two animated beats per scene; nothing stands still for more than about 2 seconds while I speak. No panel enters empty.
- Show me a 10-second sample of the look before building the whole video.
- One motion language from brand.md: blur-in entrances, quicker exits towards where the next element comes from, never linear easing. No bounce, no spin, no stock-footage look.

## Face
- Three layouts: hero (face right, fading into the background), split (portrait card left), circle (small, bottom left). Morph between them, never cut.
- Never cover or awkwardly crop my face. Layout changes only at sentence boundaries.
- Face-first at least every third or fourth sentence.

## Captions
- Word for word, phrase-level (two to four words), one line, white and bold, inside safe zones, never over my face.
- Never split a name across two captions. No captions while a big kinetic headline already shows the words.
- Emphasis colour for tool names and one key term per sentence at most.

## Sound
- Voice at one fixed level, never faded. Music 18 to 24 dB below the voice, ducked under speech, faded only after the last word.
- Hold 0.8 s after the last word.
- Final files at -14 LUFS integrated, true peak at or below -1 dBTP, two-pass loudnorm, audio at 48 kHz.

## Exports
- 16:9 at 1920x1080, 30 fps. 9:16 Reel at 1080x1920, re-laid out, not cropped.
- Run the final check (references/final-check.md) before showing me any final file.

## Lessons learned
- Look at the clip before transcribing: a screen recording with a webcam bubble is not a camera take, and may show private windows.
- Hero headline stays outside the fade of the footage; otherwise white type sits on a bright wall.
- Typed text: hide characters with display:none, not opacity.
- A fromTo whose from-state is visible needs immediateRender:false, or it shows in frame 0.
- Write every audio tag statically in the HTML. Scope SVG CSS with ids.
- (Add one line here after every fix, so the next video avoids it.)
