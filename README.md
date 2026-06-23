# claude-voice

A `/say` slash command for Claude Code. It reads Claude's last response out loud.

I read fast but I miss things on screen. Some answers I'd rather hear while I
look away from the terminal. So `/say` takes the previous response, sends it to
OpenAI TTS, and plays it back in a window where I can speed it up, seek, and
pause. No copy-paste into a separate app.

## What it does

- Pulls the last prose response from the current Claude Code session transcript.
- Strips markdown so it voices words, not punctuation.
- Synthesizes speech with OpenAI TTS, writes a stable `say-last.mp3`.
- On macOS, opens a new Terminal window playing it with `mpv`, so your keyboard
  drives speed/seek/pause. Elsewhere it prints the play command.

## Why a Terminal window

A player launched in the background takes no key presses, so you lose speed and
pause. A real terminal window does take them. The window is opened with `open -a
Terminal`, not AppleScript, on purpose: a slash command runs inside a sandbox
that blocks AppleScript control of other apps, but `open` is allowed. That one
detail is the whole reason this works from inside Claude Code.

## Install

1. Copy two things into your project:
   - `.claude/commands/say.md`
   - `scripts/say-last-response.py`

   ```bash
   mkdir -p .claude/commands scripts
   curl -o .claude/commands/say.md https://raw.githubusercontent.com/assafkip/claude-voice/main/.claude/commands/say.md
   curl -o scripts/say-last-response.py https://raw.githubusercontent.com/assafkip/claude-voice/main/scripts/say-last-response.py
   ```

2. Give it an OpenAI key, either way:

   ```bash
   export OPENAI_API_KEY=sk-...
   # or
   mkdir -p ~/.config/claude-voice
   printf '%s' 'sk-...' > ~/.config/claude-voice/openai-key && chmod 600 ~/.config/claude-voice/openai-key
   ```

3. For the playback window with controls (macOS):

   ```bash
   brew install mpv
   ```

## Use

In a Claude Code session, after a response you want to hear:

```
/say
```

A Terminal window opens and plays it. Keys: `[` `]` speed, `<-`/`->` seek 5s,
`up`/`down` 60s, `space` pause, `q` quit.

Other forms:

- `/say --no-play` - make the audio, skip the window.
- `/say stop` - clear any stray playback.

## Platform notes

- **Autoplay-with-controls is macOS only.** It uses macOS `open` and Terminal.
- **Linux / Windows / SSH / no mpv:** the audio is still made; the command prints
  how to play `~/.config/claude-voice/say-last.mp3` yourself.
- **Over SSH:** the file lives at a stable path, so you can pull it to your
  laptop and play there: `ssh <host> 'cat ~/.config/claude-voice/say-last.mp3' | mpv -`.

## Config

- `OPENAI_API_KEY` or `~/.config/claude-voice/openai-key` - the key.
- `SAY_TTS_MODEL` - default `gpt-4o-mini-tts`.
- `SAY_TTS_VOICE` - default `alloy`.

## Requirements

- Claude Code.
- An OpenAI API key (TTS is a paid call).
- `mpv` for the controls window (optional; `ffplay` is a no-speed fallback).
- Python 3, standard library only. No pip install.

## License

MIT. See `LICENSE`.


---

## Built by Assaf

I spent 12 years in threat intelligence watching teams find the same failure and fix it four times. The learning never stuck. I build tools that make it stick.

This is the free version. The paid kits live at [claudedaddy.io](https://claudedaddy.io).

**Want this wired into your team's repo, or a heavier spec-and-review pipeline?** That's the consulting. [Book a call.](https://calendar.app.google/cMFvhvDsfi9iyWYy9)
