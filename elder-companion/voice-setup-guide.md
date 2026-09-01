# Elder Companion Voice Bot — Setup Guide

**For:** Mescalito
**Date:** August 2026
**Purpose:** Get voice input working for JW (KW's dad, 82) via Telegram + local LLM

---

## What This Is

A Telegram bot that runs on your Mac. JW sends voice messages from his phone. The bot transcribes them locally (whisper.cpp), sends the text to your local LLM (llama-server), and sends the response back as a voice message (macOS `say` + ffmpeg).

**Everything runs on your Mac.** The only internet transit is the Telegram message itself — no cloud LLM, no cloud speech-to-text, no cloud text-to-speech.

```
JW's phone (Telegram voice message)
      ↓
your Mac: whisper.cpp transcribes → llama-server responds → say + ffmpeg → voice message
      ↓
JW's phone (he hears the response)
```

---

## Prerequisites

### 1. Install ffmpeg and whisper-cpp (5 min)

Open Terminal:

```bash
brew install ffmpeg whisper-cpp
```

### 2. Download a Whisper model (2 min)

The English "base" model is the right balance of accuracy and speed for an 82-year-old English speaker:

```bash
mkdir -p ~/whisper-models
curl -L https://huggingface.co/ggerganov/whisper.cpp/resolve/main/ggml-base.en.bin \
  -o ~/whisper-models/ggml-base.en.bin
```

This is ~142MB. If the download is slow, try the "tiny" model instead (faster, slightly less accurate):

```bash
curl -L https://huggingface.co/ggerganov/whisper.cpp/resolve/main/ggml-tiny.en.bin \
  -o ~/whisper-models/ggml-tiny.en.bin
```

### 3. Pick a TTS voice (2 min)

macOS `say` has several built-in voices. List them:

```bash
say -v ? | grep en_
```

For a calm male voice that works well for an 82-year-old, try:

```bash
say -v Tom "Hello, good to talk with you. What's on your mind today?"
say -v Daniel "Hello, good to talk with you. What's on your mind today?"
```

Pick whichever sounds more natural. The default in the bot is "Tom" — change it in the env file (see Step 5).

If you want more voices, go to:
System Settings → Accessibility → Spoken Content → Voices (download new ones, they're free).

---

## Step 1: Create the Telegram Bot (5 min)

1. Open Telegram on your phone (or Mac)
2. Search for `@BotFather` (blue checkmark)
3. Send `/newbot`
4. Name it: anything (e.g., "JW Companion")
5. Username: must end in `bot` (e.g., `jw_companion_bot`)
6. Copy the bot token (looks like `7812345678:AAHx...long-string`)

After step 6, you're done in Telegram. The rest happens on your Mac.

You have the token now — next you create the env file, start the LLM server, and start the bot. Here's what to do:

1. Create the env file:
```bash
nano ~/.config/sbx/cyborg-infra/scripts/elder-companion.env
```

2. Paste in this content (replace the values with your real ones):
```bash
ELDER_BOT_TOKEN=7812345678:AAHx-your-actual-token-here
ELDER_ALLOWED_USERS=123456789,987654321
ELDER_WHISPER_MODEL=~/whisper-models/ggml-base.en.bin
ELDER_TTS_VOICE=Tom
ELDER_TTS_RATE=175
ELDER_MAX_HISTORY=16
ELDER_MAX_TOKENS=200
```

3. For ELDER_ALLOWED_USERS — you need your Telegram user ID and JW's. To get them: message `@userinfobot` on Telegram from each phone, it replies with the numeric ID. Put both in, comma-separated.

4. Save and exit (in nano: Ctrl+O, Enter, Ctrl+X)

5. Start the LLM server in one terminal:
```bash
cd ~/.config/sbx/cyborg-infra && ./scripts/start-server.sh qwen-14b 8192
```

6. Start the bot in another terminal:
```bash
cd ~/.config/sbx/cyborg-infra/scripts
set -a && source elder-companion.env && set +a && python3 elder-companion-bot.py
```

Note: `set -a` is required — it exports the env variables so Python can see them. Without it, `source` loads the variables into your shell but they don't get passed to the bot process.

7. Test it yourself — find your bot in Telegram, send `/start`, then hold the mic button and talk

---

## Step 2: Get JW's Telegram User ID (2 min)

You need JW's numeric Telegram user ID so the bot only responds to him (and you, for testing).

**Option A — ask JW to do this:**
1. JW opens Telegram
2. Searches for `@userinfobot`
3. Sends it any message
4. It replies with his numeric user ID (e.g., `123456789`)

**Option B — you do it yourself for testing:**
1. Open Telegram
2. Message `@userinfobot`
3. Get your own user ID
4. Add both yours and JW's IDs to the config

---

## Step 3: Start the LLM Server (1 min)

In a Terminal window (this stays open while the bot runs):

```bash
cd ~/.config/sbx/cyborg-infra
./scripts/start-server.sh qwen-14b 8192
```

Note: 8192 context (not the default 4096) — the system prompt is long, and we want room for conversation history.

Wait until you see "server is listening on http://localhost:8080".

Test it in your browser: open http://localhost:8080 — you should see the chat UI.

---

## Step 4: Create the Config File (2 min)

Create a file at `~/.config/sbx/cyborg-infra/scripts/elder-companion.env`:

```bash
# Telegram bot token (from BotFather)
ELDER_BOT_TOKEN=7812345678:AAHx-your-actual-token-here

# Allowed Telegram user IDs (comma-separated, no spaces)
# Add your ID for testing + JW's ID
ELDER_ALLOWED_USERS=123456789,987654321

# Whisper model path
ELDER_WHISPER_MODEL=~/whisper-models/ggml-base.en.bin

# TTS voice (run `say -v ?` to see options)
ELDER_TTS_VOICE=Tom

# Speaking rate (words per minute, default 175 — lower = slower)
ELDER_TTS_RATE=175

# Max conversation history (messages kept in memory)
ELDER_MAX_HISTORY=16

# LLM response length limit (tokens, ~200 = ~130 words = 2-4 sentences)
ELDER_MAX_TOKENS=200
```

Replace the token and user IDs with your real values.

---

## Step 5: Start the Bot (1 min)

In a second Terminal window:

```bash
cd ~/.config/sbx/cyborg-infra/scripts
set -a && source elder-companion.env && set +a && python3 elder-companion-bot.py
```

You should see:
```
[HH:MM:SS] [INFO] Elder Companion Voice Bot starting...
[HH:MM:SS] [INFO] Bot is live. JW can send voice messages via Telegram now.
```

Keep this terminal open. The bot runs as long as this terminal is open.

---

## Step 6: Test It Yourself First (5 min)

Before involving JW, test with your own Telegram:

1. Find your bot in Telegram (search for the username you created)
2. Send `/start`
3. Hold the mic button and say something: "Hi, I read an interesting article about the Roman Empire today."
4. Wait ~5-10 seconds for the response (you'll see a typing indicator)
5. You should get a voice message back

**What to watch for:**
- Does the transcription work? (Check the bot's terminal log — it prints what it heard)
- Does the response feel like a conversation or a lecture?
- Is the voice quality acceptable?
- How long is the delay? (5-10 sec is normal; 15+ means the model is slow)

**If the voice sounds bad:** try a different voice (`ELDER_TTS_VOICE`). "Siri" voices on macOS Sequoia are high quality. "Daniel" is calm and British.

**If the response is too long:** lower `ELDER_MAX_TOKENS` to 150.

**If the response is too short:** raise it to 300.

**If transcription fails:** try the "tiny" model, or make sure the .ogg file downloaded correctly (check the terminal log for errors).

---

## Step 7: JW Tries It (the real test)

Once you've tested it yourself and it works:

1. Tell JW to open Telegram and find the bot
2. Have him send `/start`
3. Have him hold the mic and talk naturally
4. **Be present for the first conversation** — you're 15 mins away, or he can come to you
5. Watch the terminal log to see what's being transcribed and what the LLM responds
6. After the conversation, ask JW:
   - Did it feel like talking to someone or talking to a machine?
   - Was the voice pleasant or annoying?
   - Did it understand you?
   - Would you talk to it again?

---

## How to Stop Everything

- Bot: Ctrl+C in the bot's terminal window
- LLM server: Ctrl+C in the server's terminal window

---

## How to Restart

```bash
# Terminal 1: LLM server
cd ~/.config/sbx/cyborg-infra && ./scripts/start-server.sh qwen-14b 8192

# Terminal 2: Bot
cd ~/.config/sbx/cyborg-infra/scripts
set -a && source elder-companion.env && set +a && python3 elder-companion-bot.py
```

---

## What Could Go Wrong

| Problem | Likely Cause | Fix |
|---------|-------------|-----|
| Bot doesn't respond | Wrong token, or bot not running | Check terminal for errors, verify ELDER_BOT_TOKEN |
| Bot responds but no voice | `say` or ffmpeg not installed | `brew install ffmpeg`; check `say -v Tom "test"` |
| Voice message is silent | TTS voice not installed | Run `say -v ?` and pick an installed voice |
| Transcription is wrong | Bad audio quality or wrong model | Try "base" model instead of "tiny"; check ffmpeg conversion |
| LLM response is slow | Model too large, or Mac busy | Try `start-server.sh qwen-7b 8192` instead of 14B |
| LLM gives no response | llama-server not running | Check http://localhost:8080 in browser |
| Bot crashes | Missing config | Check all ELDER_* env vars are set |

---

## Privacy Notes

- **What stays local:** transcription (whisper.cpp), LLM inference (llama-server), speech synthesis (say + ffmpeg). None of this touches the internet.
- **What transits the internet:** the Telegram voice messages themselves. Telegram stores them on their servers. This is NOT end-to-end encrypted. For the dad version (intellectual companion), this is an acceptable prototype tradeoff. For a dementia patient version later, a fully local path (no third-party transport) would be needed.
- **Conversation history:** kept in memory (RAM) while the bot runs. Lost when the bot restarts. Not written to disk.

---

## Upgrading Later (NOT now)

- **Better TTS:** Piper (https://github.com/rhasspy/piper) has more natural voices. Install when voice quality becomes the bottleneck.
- **Better STT:** Larger whisper models (medium, large) are more accurate but slower.
- **Always-on:** If JW uses it regularly and the Mac sleep becomes an issue, consider running on the Mac mini (when purchased) or using `caffeinate` to prevent sleep.
- **Voice cloning:** For the dementia version (Esperanza), replicating a familiar voice could be powerful. This needs clinical input and is a later phase.

---

## Log

- 2026-08-28: Voice bot built. Zero-dependency Python script. Pipeline: Telegram → whisper.cpp → llama-server → say → Telegram. Setup guide written. Ready for Mescalito to install prerequisites and test.