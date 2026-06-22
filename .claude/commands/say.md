---
description: Synthesize Claude's previous response to audio; autoplays locally with controls.
argument-hint: [stop|--dry-run|--no-play]
allowed-tools: Bash
---

Turn Claude's previous response into audio you can play with speed/seek/pause.
No copy-paste into a separate TTS app.

`/say` synthesizes the last assistant prose response with OpenAI TTS, writes it
to a stable mp3, and (on macOS, local, with mpv installed) AUTOPLAYS it in a new
Terminal window so your keyboard drives speed, seek, and pause. A real terminal
takes key presses; a background player cannot. Over SSH, without mpv, off macOS,
or with `--no-play`, it skips the window and just prints the play command.
`/say stop` clears any stray playback.

Call the script FIRST, with no preamble text. (The script reads the last *prose*
assistant message from the transcript; any text emitted before the tool call
would become that message and get read instead of the intended response.)

```bash
python3 "${CLAUDE_PROJECT_DIR}/scripts/say-last-response.py" $ARGUMENTS
```

After it runs, report its stdout verbatim (it prints the autoplay status + the
ready-to-run play command). If it exits non-zero, surface stderr verbatim (e.g.
the no-key setup line) and do not retry.

## Playing it manually (SSH, --no-play, or a replay)

Autoplay opens a Terminal window for you locally. To drive playback yourself
instead - over SSH, or to replay:

- Local: `mpv ~/.config/claude-voice/say-last.mp3`
- Laptop audio over an existing SSH session: `ssh <host> 'cat ~/.config/claude-voice/say-last.mp3' | mpv -`

mpv keys: `[` `]` speed | `<-`/`->` seek 5s | `up`/`down` 60s | `space` pause | `q` quit.
Needs `brew install mpv` (ffplay is a no-speed fallback if mpv is absent).
