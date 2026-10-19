---
name: reel-format
description: Make a vertical 1080x1920 version of a horizontal talking-head video for Instagram Reels, TikTok and YouTube Shorts. Re-lays out every scene from the HyperFrames project so the face is large, motion graphics stay readable and nothing hides behind the app's buttons; works from a finished MP4 too. Ends with a technical upload check. Use for "make a Reel", "vertical version", "9:16", "Shorts", "TikTok" or any wide video headed for a phone feed.
---

# Reel Format

A wide video dropped into a vertical feed becomes a thin strip in the middle of the screen. A good Reel is designed for the phone: big face, big type, one idea at a time. This skill builds that version and makes sure the platforms accept the file as it is.

Read `edit/brand/brand.md` first. If it doesn't exist, run `brand-setup`.

## 1. Choose the source

- **From the project (preferred):** the 16:9 video was built with HyperFrames, for example by `talking-head-editor`. Build a separate 1080x1920 project in `edit/video/reel/` from the same scenes, timing and captions, with assets linked from `../assets`.
- **From a finished file:** only an MP4 exists. Rebuild it as a 1080x1920 composition with the MP4 as its video layer, a face crop from it, and the headline and captions as HTML on top.

Tell the person which route you're using.

## 2. Plan the vertical layout

Think in bands, top to bottom. These positions on a 1080x1920 canvas have worked well; adapt them to the brand, keep the margins.

1. **Chrome band (y about 200 to 400):** the name pill, and below it a full-width prompt-bar pill that types the current chapter label (`01 · …`) with a blinking caret and a small send button in the accent colour.
2. **Visual band (y about 430 to 1170):** one glass panel, 60 px in from both sides, holding the scene's motion graphic rebuilt for a narrow canvas: fewer columns, larger type (headlines around 100 px, labels 22 px or more), stacked instead of side by side. A check-off grid becomes two columns, a system map becomes a vertical chain, a pipeline wraps into two rows, a list of real assets becomes one tall tile plus smaller ones.
3. **Caption band:** phrase-level captions, one line, about 56 px bold, sitting **between the panel and the face**, never on the face.
4. **Face band (y about 1140 to 1650):** a rounded face card (about 700 x 500) when the person makes a point, a round bubble (about 370 px) when the panel is the proof and needs more height. Morph between the two at sentence boundaries.
5. **Progress line (y about 1790):** an optional thin accent line that fills beat by beat.

**Hook (first 3 to 5 seconds):** the face fills the full width from about y 520 down, fading into the background at its top edge. The opening words build above the head in huge uppercase type (about 130 px, one or two words per line, the key word in the accent colour), each on its spoken word. No captions while the big type says the words. Then the face shrinks into its card and the first panel enters.

**Pace:** the Reel keeps the 16:9 version's timing and transcript, but moves inside panels a little calmer (about 0.9 s with a soft settle); a panel's content should change roughly every second, while the panel itself stays for the whole sentence.

For hero scenes (opening, close), the face may fill the screen with the headline over a gradient at the top.

## 3. Keep clear of the app interface

Social apps lay their own controls over every Reel: profile and caption text at the bottom, action buttons on the right, sometimes a tab bar at the top. On a 1080x1920 frame, keep faces, words and logos:

- below about y 200
- above about y 1500 for captions, words and logos (the eyes and mouth of a face card should stay above about y 1450; its lower edge may run further down)
- left of about x 940 for anything that sits on the right-hand side

Backgrounds and decorative motion may run to the edges. Check the layout against the bottom quarter of the screen especially: that's where most videos lose their captions.

Logos sit on a plate in a brand colour so they hold on any frame. A logo with a solid background is clipped to its shape first.

## 4. Render and encode

Run `npx hyperframes check` on the vertical project and look at a contact sheet (one still per scene, plus the first frame), then render. The upload file must be:

- H.264 High, 1080x1920, 30 fps, `yuv420p`
- tagged and converted as bt709 in limited (TV) range: `scale=out_color_matrix=bt709:out_range=tv` in the filter chain, plus `-color_range tv -colorspace bt709 -color_primaries bt709 -color_trc bt709`
- AAC at 48 kHz (a silent track from `anullsrc` if there's no sound)
- `-movflags +faststart`
- -14 LUFS integrated, true peak -1 dBTP: two-pass `loudnorm`, then `aresample=48000`

Always write a fresh encode for upload, even if the render looks fine; phone footage and intermediate files often carry colour settings the platforms misread.

## 5. Check before handing over

```
ffprobe -v error -show_entries stream=codec_type,codec_name,width,height,pix_fmt,color_range,color_space,sample_rate -of compact reel.mp4
```

The result must show `h264`, `1080`x`1920`, `yuv420p`, `color_range=tv`, `bt709` and one `aac` stream at `48000`. Measure loudness with `ebur128`. Export stills at the start, middle and end, view them at phone size, and confirm the face, captions and logos are inside the clear area.

Show the person the probe result, the loudness and the three stills.

---
Credit: Prof. Dr. Andreas Fuchs, [futureofmarketing.de](https://www.futureofmarketing.de)
