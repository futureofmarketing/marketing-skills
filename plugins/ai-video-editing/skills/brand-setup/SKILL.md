---
name: brand-setup
description: Turn a person's or company's visual identity into one brand file that every video and graphic skill follows, plus a brand board they approve. Covers positioning, colours with roles and contrast, fonts, logos, the on-screen chrome (header, chapter chip, panels) and motion rules for talking-head videos. Use before any video or motion work, or when someone says "set up my brand", "make it look like us", "brand setup", "write my brand file" or shares logos, colours or style references.
---

# Brand Setup

A video agent without a brand file falls back on generic defaults. This skill gives it a single source of truth, `edit/brand/brand.md`, and a brand board the person signs off. It's done once per brand and reused for every video.

## How to run it

Keep it conversational and fast. Ask in two short blocks, show a draft, show the board, done. Don't explain the process up front; start with the first block of questions.

### Block A: the message

- In one sentence: who are you, and what do your videos help people understand?
- Who is watching, and what should they do afterwards? (Include the real link.)
- Pick three adjectives for the feel of your videos.

### Block B: the look

- Colours: hex codes for background, text and accent, plus one colour for emphasis. A website or brand PDF works too; say which values you took from it.
- Fonts you own, or "use free fonts".
- Logo files, and any screenshots or product images that should appear. Ask the person to drop them into `edit/brand/`.
- Two to five examples of a look they like (their own slides, stills from videos they admire), saved to `edit/brand/references/`, each with a line on what they like.
- What must never appear?

Where the person has no preference, offer the **Deep Blue Glass** preset below and say so. Where an answer is missing and there's no sensible preset, write `OPEN` in the brand file and list it at the end. Don't invent brand facts.

## Check the material

- **Logos:** note format, size and whether the background is transparent. A logo with a solid background gets a clip shape (circle, rounded rectangle) recorded in the brand file.
- **References:** describe for each one the background, text colours, how the accent is used, typography, density and where the person's face sits. If the references point in different directions, ask which one leads.
- **Contrast:** test every text colour against the backgrounds it will sit on (4.5:1 for small text, 3:1 for large). When a colour fails, suggest the nearest tint that passes and record both values: the original for shapes and accents, the tint for text.
- **Typed values first:** hex codes the person typed win over values read from images.

## Write edit/brand/brand.md

```
# Brand

## Message
Who, what the videos explain, audience, next step (link)

## Feel
Three adjectives

## Colour
| Role | Hex | Contrast | Use |
Background · Panel · Primary text · Secondary text · Accent · Emphasis (shapes) · Emphasis (text tint) · Caption backing

## Type
Headline font and weight · Label font (monospace works well) · Caption font · Minimum sizes

## Assets
Each logo, screenshot and image: file, where it's used, clip shape if needed

## Chrome on every scene
Header pill text · Chapter chip format (for example "01 · TOPIC") · Panel style · Background treatment

## Motion
Entrance, exit and transition style · Pace · What may move while idle

## Never
Banned effects and looks

## Open
Everything still marked OPEN
```

Aim for about 300 to 450 words. Show the draft before saving.

## Build the brand board

Build a single-frame HyperFrames composition (root with `data-composition-id`, `data-duration="1"`, a paused timeline) that shows: the colour roles as swatches with their hex codes, the fonts at real sizes, the header pill and chapter chip, one panel with a kicker and a headline, one caption with an emphasis word, and the logo in its clip shape. Capture it with `npx hyperframes snapshot` and save `edit/brand/brand-board.png`.

Ask: "Is this how you want to look in front of an audience?" Iterate until it's a yes.

## Connect it to the agent

Append this to `CLAUDE.md` (Claude Code) or `AGENTS.md` (Codex) in the project root, creating the file if needed:

```
## Brand
- edit/brand/brand.md is the only source for colours, fonts, chrome and motion. Read it before any visual work.
- Use the text tints from brand.md for all text; use the original emphasis colour only for shapes.
- Bold ideas are welcome; new colours, fonts or effects are not.
- When brand.md is silent on something, ask.
- Compare every frame with brand.md before showing it.
```

## Assets that arrive later

Add the file to `edit/brand/`, update brand.md and the board, and get a new yes before it appears in a video.

## Preset: Deep Blue Glass

A calm, premium look for experts explaining technology. Offer it when the person has no strong references.

- Background: deep navy with a soft radial glow in a brighter blue, drifting slowly
- Panels: translucent "glass" cards, lighter at the top, 1 px light border, large radius, a small monospace kicker in caps
- Text: near-white headlines, pale blue secondary text, one bright blue accent; a pink or magenta emphasis colour for single key words (checked for contrast)
- Chrome: name and URL on a dark pill top left; a chapter chip top right with a typed label, blinking cursor and accent dot
- Type: a heavy grotesque for headlines (uppercase in hero scenes), a monospace for labels
- Motion: blur-in entrances, blur dissolves, no bounce

Next: the `talking-head-editor` skill. Until the board is approved, no video may be called on-brand.

---
Credit: Prof. Dr. Andreas Fuchs, [futureofmarketing.de](https://www.futureofmarketing.de)
