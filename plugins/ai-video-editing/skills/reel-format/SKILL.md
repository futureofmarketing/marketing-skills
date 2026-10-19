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

Think in three bands, top to bottom:

1. **Face band:** the face as large as the source allows, in a rounded frame or full width, eyes roughly a third of the way down the screen. Chrome (name pill, chapter chip) sits in the margin above it.
2. **Visual band:** the scene's motion graphic, rebuilt for a narrow canvas: fewer columns, larger type, stacked instead of side by side. A check-off grid becomes two columns, a system map becomes a vertical chain, a pipeline wraps into two rows.
3. **Caption band:** phrase-level captions, large and bold.

For hero scenes (opening, close), the face may fill the screen with the headline overlaid on a gradient at the top.

## 3. Keep clear of the app interface

Social apps lay their own controls over every Reel: profile and caption text at the bottom, action buttons on the right, sometimes a tab bar at the top. On a 1080x1920 frame, keep faces, words and logos:

- below about y 220
- above about y 1500
- left of about x 940

Backgrounds and decorative motion may run to the edges. Check the layout against the bottom quarter of the screen especially: that's where most videos lose their captions.

Logos sit on a plate in a brand colour so they hold on any frame. A logo with a solid background is clipped to its shape first.

## 4. Render and encode

Run `npx hyperframes check` on the vertical project, then render. The upload file must be:

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
