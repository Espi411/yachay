# Elder Companion — Useful Commands

Quick reference for checking, starting, stopping, and troubleshooting the voice bot.

---

## Check Everything Is Installed

```
which whisper-cli ffmpeg say
```

All three should return a path. If any says "not found":
- `whisper-cli not found` → `brew install whisper-cpp`
- `ffmpeg not found` → `brew install ffmpeg`
- `say not found` → you're not on macOS (say is built into macOS)

## Check the Whisper Model Downloaded

```
ls -lh ~/whisper-models/
```

Should show `ggml-base.en.bin` (~142MB) or `ggml-tiny.en.bin` (~75MB).

## Test Whisper Manually

```
whisper-cli -m ~/whisper-models/ggml-base.en.bin -f /path/to/audio.wav -l en -nt -otxt
```

If it produces text, whisper works. If it errors, check the model path.

## Test TTS (say) Manually

```
say -v Tom "Good to talk with you. What's on your mind today?"
```

You should hear it spoken. To list all English voices:

```
say -v '?' | grep en_
```

## Test the LLM Server

```
curl -s http://localhost:8080/v1/models | python3 -m json.tool
```

Should return a JSON list of models. If it errors, the server isn't running.

---

## Start Everything (2 terminals)

Terminal 1 — LLM server:
```
cd ~/.config/sbx/cyborg-infra && ./scripts/start-server.sh qwen-14b 8192
```

Terminal 2 — voice bot:
```
cd ~/.config/sbx/cyborg-infra/scripts && source elder-companion.env && python3 elder-companion-bot.py
```

## Stop Everything

- Bot: Ctrl+C in terminal 2
- LLM server: Ctrl+C in terminal 1

## Restart After a Reboot

Same two commands as "Start Everything" above.

---

## Check the Bot Is Running

```
ps aux | grep elder-companion-bot
```

If you see the python process, the bot is running. If not, it crashed or you stopped it.

## Check the Bot's Logs

The bot prints to the terminal it's running in. If it's running in terminal 2, just look at that window. If something went wrong, you'll see the error there.

Common log messages:
- `[INFO] Bot is live` → running fine
- `[ERROR] Telegram API ... failed: 401` → bad bot token
- `[ERROR] LLM connection failed` → llama-server not running
- `[ERROR] whisper-cli not found` → whisper-cpp not installed or not in PATH
- `[INFO] Transcribed: ...` → working, it heard what you said
- `[INFO] Response: ...` → working, the LLM responded

---

## Quick Troubleshooting

| Symptom | Check | Fix |
|---------|-------|-----|
| Bot doesn't respond in Telegram | `ps aux \| grep elder-companion-bot` | Restart the bot |
| Bot responds but no voice | `which say ffmpeg` | `brew install ffmpeg`; `say` is built into macOS |
| Transcription is wrong | Try speaking more clearly; check mic | Switch to `ggml-base.en.bin` (more accurate than tiny) |
| LLM response is slow | Check if Mac is busy | Try `start-server.sh qwen-7b 8192` (lighter model) |
| Bot says "I can't hear you" | Whisper model path in env file | Check `ELDER_WHISPER_MODEL` points to the right file |
| Telegram says "bot not found" | You're messaging the wrong bot | Search for the username you created in BotFather |

---

## Check Telegram Settings for JW

On JW's phone, in Telegram:
1. Settings → Chat Settings → Voice Messages → **Raise to Listen = ON**
2. Settings → Notifications → make sure the bot chat is not muted
3. In the bot chat → three dots (top right) → Notifications → ON

---

## File Locations

| What | Where |
|------|-------|
| Bot script | `~/.config/sbx/cyborg-infra/scripts/elder-companion-bot.py` |
| Config file (env) | `~/.config/sbx/cyborg-infra/scripts/elder-companion.env` |
| System prompt (JW) | `~/.config/sbx/yachay/elder-companion/system-prompt-dad.txt` |
| Setup guide | `~/.config/sbx/yachay/elder-companion/voice-setup-guide.md` |
| Whisper models | `~/whisper-models/` |
| This file | `~/.config/sbx/yachay/elder-companion/useful-commands.md` |

---

## Log

- 2026-08-29: Created. Useful commands for checking, starting, stopping, and troubleshooting the elder companion voice bot.