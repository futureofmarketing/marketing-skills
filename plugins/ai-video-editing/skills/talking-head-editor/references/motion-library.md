# Motion Library: Talking-Head Videos That Feel Produced

This is the target look. A finished video should feel like a motion designer built it: something moves on screen every one to two seconds, every element is triggered by the word that names it, and no scene is a static slide.

Use these patterns in the storyboard (Step 5) and build them in Step 6. Before hand-building a named visual (terminal window, chart, map, cursor), search the HyperFrames registry (`hyperframes-registry` skill); it already covers many of them.

## Global rules

- **Never static.** Every scene has at least two animated beats (an entrance, a build, a state change, a moving playhead, a typed line). Idle elements get micro-motion: a blinking cursor, a slow glow drift, a pulsing status dot.
- **Say it, see it.** Every concrete thing the person names gets its own visual the moment it's spoken: each item of a list ("every image, every logo, the music, the pauses"), each tool, channel, format, step or number. A named item gets a tile, icon, chip, mockup or node of its own that appears or lights up on its word, never just a line of text. Lists of three or more become a check-off grid, steps become a pipeline, tools become brand chips or a system map.
- **Synced to speech.** Each beat starts on the timestamp of its word in `transcript-cut.json`, 2 to 4 frames early so it lands with the word.
- **One motion language.** The values come from `brand.md`; these are the defaults when it is silent. Entrances: blur-in (blur 8 to 12 px to 0, opacity 0 to 1, y +24 to +40 px to 0) over 0.5 to 0.8 s with `power4.out` or `expo.out`. Exits are quicker than entrances (0.3 to 0.4 s, `power3.in`) and leave in the direction the next element arrives from. Builds stagger 0.06 to 0.12 s. Never linear easing for anything that moves. No spin, no hard bounce; a soft spring (`back.out(1.4)` to `back.out(1.6)`) is fine for small UI elements such as chips and buttons if the brand allows it.
- **No card enters empty.** A panel carries content the moment it appears. If the first named item comes later, open with a short headline that lands on the phrase being spoken, then let it make room for the build.
- **Transitions between scenes:** the outgoing panel blurs out towards the left (0.4 s), the incoming panel blurs in from the right starting about 0.2 s later, so there is never an empty frame and never two panels on top of each other. Layout changes morph the face frame (position, size, corner radius) instead of cutting.
- **Living, not finished.** Once a diagram, grid or mockup has built, it keeps a quiet idle motion until the scene ends: data dots travel along connectors, equaliser bars keep moving at low amplitude, a progress bar loops, a cursor blinks, the active element glows. Drive every loop from the composition timeline (repeat with a known duration), never from wall-clock time.
- **Seek-safe.** One paused GSAP timeline per composition, no `setTimeout`, no CSS animations that run on wall-clock time (see hyperframes-animation).

## Persistent chrome (on every scene)

- **Header pill, top left:** name and URL in small caps on a dark translucent pill.
- **Chapter chip, top right:** `NN · LABEL` in a monospace font with a blinking text cursor and a small accent dot. The label is typed in (about 25 characters per second) when the chapter changes, the number counts up.
- **Background:** deep brand colour with a soft radial glow that drifts slowly (8 to 12 s cycle).
- **Panels:** "glass" cards, slightly lighter at the top than at the bottom, 1 px light border at low opacity, large corner radius. A small monospace kicker line in caps sits at the top of each panel.

## Face layouts

- **Hero:** the face fills the right part of the frame at full height, fading softly into the background on its left edge (gradient mask over 300 to 400 px, no hard border). The headline sits in a column on the left that stays completely outside the fade (about 600 px at 1920 wide). Reflow long headlines into more lines rather than letting type run over the faded wall, where it loses contrast. For opening, key statements, chapter questions, close.
- **Split:** face in a rounded portrait card on the left third, a glass panel with the visual on the right. The default for explaining.
- **Circle:** face in a small circle bottom left while a dense visual (interface, diagram) takes the stage.
- Morph between layouts at sentence boundaries; mid-morph, the face card may pass through a larger rounded rectangle.

## Patterns

### 1. Kinetic headline (hero)
Heavy uppercase headline, two to five short lines, built word by word as the words are spoken (blur-in per word). One key word in the accent colour. Optional: small monospace chips appear below on their cue (for example `● IN 60 SECONDS` with a spinning ring, or `● MY STUDENTS`). Exit: blur-out of the whole block.
*Use for:* the opening line, the core message, the close.

### 2. Building statement card (split)
Inside the panel: optional tab row (`01 First point` / `02 Second point`, the active tab highlighted, a finished tab gets a check mark), then chips for supporting questions, then a dark quote box. The first sentence appears, the continuation appears as a second line in the accent colour, and the key phrase gets an underline that draws from left to right.
*Use for:* a thought that develops over two or three sentences.

### 3. Check-off grid
Two by two or two by three tiles, each with a small icon and a label. Tiles start dim. When a tile's word is spoken, it brightens, its icon animates (an image icon draws, equaliser bars bounce, a "NEW" badge pops, a play button pulses), a small spinner turns into a check mark.
*Use for:* lists ("every image, every logo, the music, the pauses"; "presentations, websites, sales letters").

### 4. Interface mockup: chat input
A rounded input field. The spoken instruction is typed in character by character, the send button lights up, and a result preview appears below (for example the words styled the way the instruction asked).
*Use for:* "I just say X and it happens."

### 5. Interface mockup: terminal or app window
Brand chips for the tools land one by one on their names at the top of the panel (supplied logos only, shown as app icons on a light tile). A terminal window scales in from 0.96, shows a welcome box, and the person's instruction is typed in sync with the speech, ending with a "Thinking…" status. Build it in the real product's own colours (dark window, its accent colour, real window controls), only when the person asks for it, and never call it a screenshot.
*Use for:* naming the tools and showing how you use them.

### 6. Timeline mockup
A small editing timeline with tracks (VIDEO, TEXT, FX, AUDIO) and coloured clip bars; a playhead sweeps across. A headline above it, and a chip that gets struck through on the matching word (for example "GOOD CUT?").
*Use for:* talking about editing, process or time.

### 7. System map
A hub card on the left (the orchestrating tool, with its supplied logo, a kicker such as `OPERATOR HUB` and a one-line tagline), curved branch lines to channel rows (for example LinkedIn, Instagram, YouTube, Newsletter, each with a small monospace subtitle), and per row a chain of step chips, each with a monospace verb kicker (`MAKE`, `BUILD`, `EDIT`, `PUBLISH`) and the tool name, joined by short connectors. A footer bar, bracketed to the rows, names the system of record.

Choreography, synced to speech:
1. Panel and kicker blur in; the hub card scales in from 0.96 and its logo pops.
2. Branch lines draw from the hub to each channel (stroke-dashoffset, 0.4 s each, staggered 0.1 s) as the channels are named; channel labels blur in at the line ends.
3. Each row's chips cascade left to right (0.08 s stagger), connectors draw between them.
4. The footer bracket draws, the footer bar slides up.
5. **Living phase** until the scene ends: small glowing dots travel along the branch lines and connectors in a steady loop (one to two seconds per hop, offset per row), the chip a dot arrives at brightens briefly, and the hub glows softly. A diagram never freezes once it's built.

*Use for:* agents, ecosystems, tool stacks, "how it all connects".

### 8. Process pipeline
Five to seven nodes in a row with labels in monospace caps (for example IDEA, BRIEF, MAKE, QA, PUBLISH). Nodes appear in sequence, connecting arcs draw between them, and a pulse dot travels the full chain.
*Use for:* breaking work into steps, orchestration.

### 9. Logo or event card
A supplied logo or event image placed in a glass panel with a short caption chip; gentle scale-in and a soft glint.
*Use for:* naming a real brand, event or product. Only with files the person supplied.

### 10. Call-to-action close
Hero layout. The closing headline builds, then a URL pill types in the address, an oversized cursor enters from outside the frame, glides to the button and clicks it on the last word (button presses, a ring ripples out, the pill fills with the accent).
*Use for:* the last sentence, especially on stage and LinkedIn.

### 11. Negation: struck and gone
For "without X", "no need for Y", "I'm not a Z". The things being dismissed appear first as a cascade of chips (for example the names of programming languages, or "GOOD CUT?" and "EFFECTS?"), a strike line draws through each on the word that negates them, then they drop out of the panel with a blur. What remains is the short statement ("No code needed.").
*Use for:* any sentence whose point is that something is no longer necessary. It is far stronger than the same words as plain text.

### 12. One big number
A single huge figure in the accent colour with a short word beside it ("1 Take.") and a monospace line underneath. Holds the panel while the sentence builds up to the thing it introduces, then makes way (blur-out) for the real asset, for example the event logo on its word.
*Use for:* "once", "in 60 seconds", "two things", a count the person says out loud. Only numbers that were spoken.

### 13. Chapter question (hero)
The face returns to hero layout and the question builds word group by word group in big uppercase type ("WHAT DOES THE / MARKETER OF / TOMORROW NEED?"), then gives way to the answer in the accent colour ("TWO / THINGS."). Captions pause while the type says the words. The next panel then opens a two-tab chapter map (pattern 2) whose tabs carry the two answers and tick off as the talk moves on.
*Use for:* the turning point of the talk.

### 14. Meta moment
When the person says what the edit is doing ("I just say subtitles in pink and it happens"), the video does exactly that on the spoken word: the instruction types into a chat input, the send button is pressed, a preview line changes, and the real captions switch for a few seconds. It is the most convincing proof in the whole video. It may break a brand rule for those seconds; record it as an approved exception in brand.md.

## Mini-graphics: show the thing itself

Every named item gets a small, purpose-drawn mini-graphic that shows the thing itself, built from simple shapes in the brand colours, never a generic icon-font symbol or an emoji. It sits in its tile, starts dim, and plays a short animation of its own when its word is spoken (draw-in, fill, bounce-free pop, a moving bar), then settles with a check mark if the pattern is a check-off grid.

Start from this vocabulary and invent new ones in the same style:

| Named thing | Mini-graphic | Its animation |
|---|---|---|
| Image, photo | Frame with a mountain and sun | Outline draws, sun fades in |
| Logo, brand | Speech bubble or badge with initials | Badge pops to size |
| Music | Equaliser bars | Bars rise and fall |
| Pauses, timing | Three dots in a row | Dots fill one after another |
| Motion graphics | A dot on a curved path | Dot travels the path |
| Presentation, PowerPoint | Mini slide with title line and rising bar chart | Bars grow left to right |
| Product launch, "new" | Rounded "NEW" badge with soft glow underneath | Badge lifts, glow spreads |
| Video, sales letter | Player frame with play button and progress bar | Progress bar fills, button pulses |
| Website, landing page | Browser window with three dots and content blocks | Blocks slide in |
| Agents, agent systems | One node above three nodes, connected | Lines draw, nodes light up |
| Newsletter, email | Envelope or page with lines | Lines write in |
| Data, analytics | Small line chart | Line draws, last point pulses |
| Customer, audience | Person outline or small group | Outline draws |
| Trust | Shield with check | Check draws |
| Process, workflow | Three linked boxes | Arrows draw between them |

After its entrance, each mini-graphic keeps a subtle idle loop (bars breathing, a dot circling, a playhead creeping) at low amplitude, so a finished grid still feels alive.

Mini-graphics are line-and-fill drawings in the panel's colours: one light stroke colour, one fill, the accent for the moving part. Inline SVG is fine for each mini-graphic (it is one object), animated with the composition's GSAP timeline.

## Captions

Phrase-level, two to four words at a time, one line, white, bold, centred under the visual (in hero layout under the headline column). Never split a name across two captions. Leave captions out while a kinetic headline already shows the same words in big type. Names of tools and the key term of a sentence may switch to the emphasis colour. Captions come and go with a short fade, never with a bounce.

## Pacing check before rendering

Scrub the composition at 2 frames per second, or render a contact sheet at 1 to 4 frames per second and look at every frame. If anywhere nothing moves for more than about 2 seconds while the person is speaking, add a beat from the pattern in use (a build step, a chip, a cursor, a state change). Look in particular at:

- the first second after every panel enters (is there content yet?),
- the gap between a headline and the first item it introduces,
- the first frame of the video (no stray elements from a later scene),
- the frames around every scene change (no empty frame, no two panels stacked).
