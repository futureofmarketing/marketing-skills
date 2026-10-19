<p align="center">
  <a href="https://www.futureofmarketing.de"><img src="assets/future-of-marketing-logo.png" alt="Future of Marketing. Decoded. By Prof. Fuchs." width="200"></a>
</p>

# Marketing Skills by Future of Marketing

**Agent skills for marketers who build.** Install them once, and your AI agent (Claude Code, Codex or the Claude app) knows how to do real marketing work at a professional standard, step by step, with a quality check at every stage.

By [Prof. Dr. Andreas Fuchs](https://www.futureofmarketing.de), Professor of Marketing & Digital Business, author of *MarTech, AI & Automation: The Future of Marketing* (Springer) and writer of the [Future of Marketing](https://www.futureofmarketing.de) newsletter.

---

## What's inside

| Plugin | Skill | What it does |
|---|---|---|
| `ai-video-editing` | `talking-head-editor` | Turns one talking-head take into a polished 16:9 video and a 9:16 Reel with motion graphics, word-synced captions, your brand and music. |
| `ai-video-editing` | `brand-setup` | Captures your visual brand once as `brand.md` plus a brand board, so every video looks like you. |
| `ai-video-editing` | `reel-format` | Turns a horizontal video into a clean 1080x1920 Reel, TikTok or Short, inside the safe zone, with an upload check. |

More skills will follow in their own plugin folders.

---

## Install

### Any coding agent (Claude Code, Codex, Cursor and others)

```bash
npx skills add futureofmarketing/marketing-skills
```

Run it inside your project folder. Start a new agent session afterwards so the skill loads.

### Claude Code plugin

```
/plugin marketplace add futureofmarketing/marketing-skills
/plugin install ai-video-editing@future-of-marketing
```

### Claude app (desktop or web)

Download the folder `plugins/ai-video-editing/skills/talking-head-editor`, zip it, and upload the zip under Customize, then Skills.

---

## talking-head-editor

Record yourself once. Your agent does the rest: transcript, clean cut, brand board, storyboard, real motion graphics for every sentence (kinetic headlines, typed interface mockups, check-off grids, drawing diagrams), face layouts that morph, captions, music, a 16:9 video and a vertical Reel.

**How it works:** every step ends with proof (a transcript, a cut check, a contact sheet, a loudness report), and you approve at three gates: transcript, rough cut and storyboard. A 15-point final check runs before you see any final file.

**What you need:** an AI plan that includes Claude Code (or Codex), ffmpeg, Node.js 22 or newer and Python 3. The skill checks your setup first and proposes install commands. Everything else is free and open source. You don't need any paid video tool.

**Start with:**

> Edit my video in this folder with the talking-head-editor skill.

**Inside the skill:**

- `SKILL.md`: the full workflow, Steps 0 to 10
- `references/motion-library.md`: the target look and ten motion patterns, from kinetic headlines to system maps
- `references/studio-rules.md`: copy into your `CLAUDE.md` or `AGENTS.md` and make it yours
- `references/recording-guide.md`: how to record one take that's easy to edit
- `references/final-check.md`: the 15-point gate
- `references/fixes.md`: what to say when something goes wrong

The full playbook behind this skill, with examples, is on [futureofmarketing.de](https://www.futureofmarketing.de).

---

## Built on

- [HyperFrames](https://github.com/heygen-com/hyperframes) by HeyGen (Apache 2.0), which renders HTML into video. The skill works best with its agent skills installed: `npx skills add heygen-com/hyperframes`.
- [ffmpeg](https://ffmpeg.org), [Whisper](https://github.com/openai/whisper) / [faster-whisper](https://github.com/SYSTRAN/faster-whisper) and [OpenCV](https://opencv.org).

## License

MIT. Use, adapt and share these skills freely. Please keep the copyright notice and, if you write about them, link back to [futureofmarketing.de](https://www.futureofmarketing.de).

🦊🎓
