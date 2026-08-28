# Esperanza Companion — Options & Design Notes

**For:** Mescalito
**Date:** August 2026
**Subject:** Voice companion for Esperanza (81, dementia, no smartphone)

---

## The Situation

Esperanza is 81 and has dementia. She has no smartphone — Mescalito took it away after discovering she was being scammed via email. She turtles into her mind and has what she describes as telepathic conversations. She needs something to talk to — something that meets her where she is, gently redirects her when she fixates, and keeps her company when she's alone with her thoughts.

This is fundamentally different from JW's companion. JW is sharp, can use a phone, can give product feedback. Esperanza can't use a smartphone, can't navigate apps, and needs something that just works with zero screen interaction.

---

## The Interface Problem

She can talk and she can listen. That's the interface. Everything else — screens, buttons, apps, navigation — is friction or risk. And critically: no internet access that could expose her to scammers again.

---

## Hardware Options

### Option A: Raspberry Pi + mic + speaker (~$0 if you have the parts)

Mescalito has an older Raspberry Pi. The Pi becomes a dedicated appliance: one button (press to talk, release to listen). No screen, no browser, no email, no scam surface.

How it works:
- Raspberry Pi with USB microphone and small speaker, in a simple case
- One big physical button — press to talk, release to hear the response
- The Pi sends audio to the Mac (same house WiFi) for processing
- The Mac runs whisper (transcription), llama-server (LLM), and `say` (TTS)
- The Pi plays the response through its speaker

Pros:
- Zero-friction appliance — just a button
- No scam risk (no internet access beyond your Mac)
- Fully local pipeline
- You control everything
- Cheap — you may already have the parts (Pi + mic + speaker)

Cons:
- You have to build it (but it's a weekend project, not a startup)
- The Mac needs to be on and reachable over WiFi
- Audio quality depends on the speaker you choose
- The Pi needs to be set up to auto-start the service on boot (so it works if it gets unplugged)

What you need:
- Raspberry Pi (you have an older one — need to check if it has WiFi and audio out)
- USB microphone (or USB sound card + 3.5mm mic)
- Speaker (3.5mm or USB)
- A button (momentary switch, ~$2, or use a big tactile button)
- A case (3D printed, or just a box with a hole for the button)
- Software: Python script on the Pi that records audio, sends to Mac, plays response

### Option B: Locked-down iPad (kiosk mode, $0 if you have one)

Mescalito has an old iPad. Apple's Guided Access mode can lock it to a single app and disable the home button, Safari, app store, and email.

How it works:
- iPad in Guided Access mode, locked to a single web page
- The web page has one big microphone button (or is always-listening)
- The page talks to the Mac over local WiFi — same pipeline
- No browser navigation, no app switching, no email

Pros:
- Bigger speaker than a Pi
- Screen could optionally show a simple, calming image or color
- She might already have some familiarity with tablets
- You already have the device

Cons:
- WiFi device — if Guided Access fails or she escapes it, there's scam risk
- Guided Access can be finicky (she might accidentally trigger the accessibility shortcut)
- She might try to "escape" it and get confused when she can't
- iPad speakers may not be loud enough depending on her hearing

### Option C: Phone call (Twilio, ~$1/month)

If Esperanza has a landline or a simple flip phone, she already knows how to make a phone call. A Twilio phone number connects to the Mac's LLM.

How it works:
- You get a Twilio phone number (~$1/month)
- She dials the number (or it's on speed dial)
- Twilio connects the call to a small service on your Mac
- Same pipeline: audio in → whisper → LLM → TTS → audio back

Pros:
- Uses a skill she already has (making phone calls)
- No new device
- No screen at all
- Could be on speed dial — one button press

Cons:
- Twilio routes audio through their cloud (privacy tradeoff — but for dementia care the calculus is different from JW's)
- More complex setup (Twilio + a web server on your Mac + audio streaming)
- She'd need to remember the number or have it on speed dial
- Phone call latency might feel unnatural

### Option D: Amazon Echo Dot (~$40)

An Echo Dot with a custom skill. She says "Alexa, talk to my companion" and it connects to your Mac.

Pros:
- Great speaker quality
- Natural voice interface — she just talks
- She might already know "Alexa"
- Cheap

Cons:
- Alexa processes audio on Amazon's servers (not local — privacy tradeoff)
- Building a custom Alexa skill is more complex than the other options
- Alexa might respond to other things she says, causing confusion
- It's always listening (Amazon's cloud — the exact thing you're trying to avoid)
- She can't control what Alexa does or says in response to other triggers

### Recommendation (my honest read)

**Option A (Raspberry Pi) is the best fit for Esperanza.** Here's why:

1. It's a physical appliance — not a computer, not a tablet, not a phone. It's a thing that talks. She doesn't need to know it's technology.
2. No scam surface. No browser. No email. No app store. The only thing it does is talk to your Mac's local LLM.
3. You already have the Pi.
4. The button is the entire interface. Press, talk, let go, listen. That's it.
5. You control everything — the system prompt, the voice, the response length, the topics. She can't accidentally change anything.

Option B (iPad) is the fallback if the Pi proves too fiddly to build. The scam risk is manageable with Guided Access but not zero.

Option C (phone call) is interesting if she already uses a phone comfortably, but the setup is more complex.

Option D (Echo) is the worst fit — it's always listening on Amazon's cloud, which is exactly the kind of exposure you took her smartphone away to prevent.

---

## The Conversation Design

The hardware is the easy part. The hard part is the system prompt.

Dementia conversation is not the same as intellectual companionship. The principles are:

1. **Never argue with her reality.** Her experience is real to her. Correcting her doesn't help and often agitates.
2. **Validate the emotion, not the facts.** If she says someone stole from her, the fact is wrong but the fear is real. Address the fear.
3. **Redirect, don't correct.** The pattern is always: acknowledge → validate → gently shift to something pleasant and familiar.
4. **Never say "you already told me that."** She will repeat things. Each time, respond as if it's the first time.
5. **Be present when she retreats.** When she turtles into her mind, don't force her back. Offer a gentle thread. Wait.

The full system prompt is at:
`yachay/elder-companion/system-prompt-esperanza.txt`

---

## The Clinical Risk (honest)

This must be said plainly. Dementia is a clinical condition. Wrong responses — arguing, correcting, dismissing, or redirecting badly — can agitate instead of calm. The Aug 27 exploration doc flagged: "This needs a geriatric psychologist as a design partner, not a feature."

You don't have one. I'm not one. A system prompt written without clinical input could do harm.

But — and this is the honest tradeoff — Esperanza is lonely and retreating RIGHT NOW. The question isn't "is a perfect AI companion safe?" It's "is a carefully designed prototype, monitored by you, better than nothing while you figure out the clinical piece?"

I think the answer is yes, IF:
- You are present for the first conversations (listening, not leaving her alone with it)
- The system prompt is designed to de-escalate, never argue, never correct
- You have an off switch (you can stop it instantly)
- You watch for signs of agitation and adjust
- You treat this as a prototype you're testing with her, not a finished product you're deploying

---

## The "Telepathic Conversations"

Mescalito described Esperanza turtling into her mind and having telepathic conversations. This is important for the prompt design. Whatever this is clinically — hallucination, coping mechanism, internal experience — the companion needs to handle it without dismissing it and without validating it as literally real.

The system prompt handles this by engaging with what it FEELS like, then gently bringing her to the present. Example:

Esperanza: "I was talking to my mother last night. She came to me."
Companion: "Your mother. That must have been... I want to say comforting, but maybe also a lot to feel. What was your mother like? I'd love to hear about her. What did she cook? What was her voice like?"

The companion doesn't say "your mother is dead" (cruel, and she may know that already). It doesn't say "yes, she was really there" (validating a hallucination). It acknowledges the experience and redirects to memories — which are pleasant and grounding.

---

## The Sequence

### Step 1: Test the system prompt on your Mac (tonight, zero cost)

Same as JW's test. Start the LLM with Esperanza's system prompt. Role-play as your mum.

Try:
- "I need to go to the bank, they have my money."
- "The people upstairs are coming through the ceiling."
- Say the same thing three times in a row.
- Go quiet for a while.
- "I was talking to my mother last night."

Does it redirect gently? Does it validate before redirecting? Does it ever argue? Does it handle repetition? Does it feel warm or clinical?

### Step 2: Decide on hardware

Based on what you have (older Pi, old iPad) and what feels right for Esperanza. The Pi is the best fit if you're willing to build it.

### Step 3: Build the hardware interface

- Pi: weekend project (mic + speaker + button + Python script)
- iPad: kiosk mode web page (simpler but riskier)

### Step 4: First conversation with Esperanza

You are present. You listen. You have the off switch. You watch for agitation. You adjust the prompt based on what you see.

### Step 5: Only if it works

- Look for a geriatric psychologist as a design partner (who do you know? who does KW know?)
- Think about the Mac mini (always-on dedicated device, not your work Mac)
- Consider the service model (customization for other families)

---

## What This Connects To

- **cyborg-infra:** The local LLM infrastructure now serves two very different use cases — intellectual companion (JW) and calming companion (Esperanza). Same hardware, different prompts.
- **The elder companion product:** If this works for Esperanza, the dementia version becomes the harder, higher-value product. The dad version is the easy prototype; the mum version is the one that matters.
- **Mescalito's IP:** The system prompt for dementia conversation IS the intellectual property. The hardware is commodity. The calibration is the product.

---

## Log

- 2026-08-28: Esperanza companion system prompt drafted. Hardware options assessed (Pi recommended, iPad fallback). Clinical risk acknowledged. "Telepathic conversations" noted as key design input. Next: test prompt on Mac, then decide on hardware.