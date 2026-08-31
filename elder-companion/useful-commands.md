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
say -v Daniel "Good to talk with you. What's on your mind today?"
```

You should hear it spoken. To list all English voices:

```
say -v '?' | grep en_
```

Note: On macOS Sonoma, only a few voices are pre-installed (Samantha, Daniel, Aman/Siri). The others are listed but need to be downloaded from System Settings → Accessibility → Spoken Content → Voices. If `say -v Tom "test"` sounds the same as the default, Tom isn't installed and `say` is falling back to Samantha. Daniel is the best pre-installed male voice — calm, British, not robotic.

To change the voice, edit `ELDER_TTS_VOICE=Daniel` in `elder-companion.env`.

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
cd ~/.config/sbx/cyborg-infra/scripts
set -a && source elder-companion.env && set +a && python3 elder-companion-bot.py
```

Note: `set -a` is required — it exports the env variables so Python can see them.

## Stop Everything

- Bot: Ctrl+C in terminal 2
- LLM server: Ctrl+C in terminal 1
- If you get a 409 Conflict error, you have two bot instances running. Kill all and start one:
```
ps aux | grep elder-companion-bot
killall python3
```
Then start fresh with one instance.

## Restart After a Reboot

Same two commands as "Start Everything" above. The `set -a` is required — without it the bot can't see the env variables and will fail with "ELDER_BOT_TOKEN is not set."

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

## Bot Commands (for JW)

| Command | What it does |
|---------|-------------|
| /start | Greeting + lists all commands |
| /reset | Clears conversation history (fresh start) |
| /feedback | Share thoughts on the experience (goes to Mescalito) |
| /remember [text] | Save something to come back to later |
| /recall | See what you've asked to remember |

## Reading JW's Conversation Logs

```
ls -la ~/.config/sbx/cyborg-infra/scripts/conversations/
```

Daily logs: `YYYY-MM-DD_userID.md`
Feedback: `feedback.md`
Remember items: `remember_userID.md`

All stay on your Mac. Nothing leaves the hardware.

## Running the Analytics Dashboard

```
cd ~/.config/sbx/cyborg-infra/scripts
python3 conversation-analytics.py
```

This produces a summary of JW's conversations without you reading every message:

- **Time of day** — when does JW talk? Is he up at 3am? Morning person?
- **Daily breakdown** — first message, last message, time span per day
- **Topic themes** — what he talks about (US politics, health, history, etc.)
- **Hallucination signals** — did JW push back on anything? How many factual claims did the LLM make? Pushback rate.
- **Feedback** — his /feedback messages
- **Remember items** — what he chose to save

Options:
```
python3 conversation-analytics.py --days 7    # last 7 days only
python3 conversation-analytics.py --detail   # show per-conversation breakdown
```

What to look for:
- **Topic frequency** — what KW will ask about (Trump, US politics, Parkinson's, neuropathy)
- **Late night activity** — is JW up at 3am? Over-rotating?
- **Pushback rate** — is JW catching hallucinations? If pushback rate is 0% over many conversations, he's either not hallucinating or not catching them
- **Conversation span** — is he talking for 10 minutes or 2 hours?
- **Factual claim density** — higher = more surface for hallucination. Watch the ratio over time.

---

## Maintenance Check (monthly)

Check if anything needs updating:

```
brew outdated whisper-cpp ffmpeg
```

If anything shows up, update it:
```
brew upgrade whisper-cpp ffmpeg
```

Then restart the bot.

### When to upgrade the model

The model (Qwen 14B) is a fixed file. It never changes on its own. Consider upgrading when:
- JW's pushing back a lot (high hallucination rate in analytics)
- A new model lands on the HN digest that's clearly better for conversation
- The Mac mini arrives (more RAM = can run bigger models)

### How to upgrade the model

1. Check if a GGUF exists on HuggingFace:
```
curl -s "https://huggingface.co/api/models?search=glm-5.3-flash+gguf&sort=downloads&direction=-1&limit=5" | python3 -c "import sys,json; [print(m['id']) for m in json.load(sys.stdin)]"
```

2. Add it to start-server.sh (the script already has a model selector)

3. Test with the system prompt before switching JW over:
```
cd ~/.config/sbx/cyborg-infra && ./scripts/start-server.sh new-model 8192
```

4. Keep the old model — don't delete Qwen 14B until you're sure the new one is better.

### Better TTS voices (free, on macOS)

System Settings → Accessibility → Spoken Content → Voices → download Siri voices (1-4). They're higher quality than Daniel. Test with:
```
say -v 'Siri' "Good to talk with you. What's on your mind today?"
```

If better, change `ELDER_TTS_VOICE=Siri` in the env file and restart.

### Restart after any update

```
# Terminal 1: LLM server (restart to pick up new model/binary)
cd ~/.config/sbx/cyborg-infra && ./scripts/start-server.sh qwen-14b 8192

# Terminal 2: Bot (restart to pick up code changes)
cd ~/.config/sbx/cyborg-infra/scripts
set -a && source elder-companion.env && set +a && python3 elder-companion-bot.py
```

### Pull code updates from GitHub

If Corina made changes to the bot or docs, pull them:
```
cd ~/.config/sbx/cyborg-infra && git pull
cd ~/.config/sbx/yachay && git pull
```

Then restart the bot to pick up any changes.

---

## File Locations

| What | Where |
|------|-------|
| Bot script | `~/.config/sbx/cyborg-infra/scripts/elder-companion-bot.py` |
| Config file (env) | `~/.config/sbx/cyborg-infra/scripts/elder-companion.env` |
| System prompt (JW) | `~/.config/sbx/yachay/elder-companion/system-prompt-dad.txt` |
| Setup guide | `~/.config/sbx/yachay/elder-companion/voice-setup-guide.md` |
| This file | `~/.config/sbx/yachay/elder-companion/useful-commands.md` |
| Whisper models | `~/whisper-models/` |
| Conversation logs | `~/.config/sbx/cyborg-infra/scripts/conversations/` |
| Feedback from JW | `~/.config/sbx/cyborg-infra/scripts/conversations/feedback.md` |
| JW's remember items | `~/.config/sbx/cyborg-infra/scripts/conversations/remember_userID.md` |

---

## Log

- 2026-08-29: Created. Useful commands for checking, starting, stopping, and troubleshooting the elder companion voice bot.