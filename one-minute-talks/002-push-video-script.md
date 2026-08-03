# PUSH
## A 4-minute video about learning to code by hitting a wall and walking through it

**Format:** Short documentary-style monologue, screen-recorded with voiceover
**Length:** ~4 minutes
**Audience:** People who think they can't do tech because nobody showed them
**Status:** Script / storyboard — ready for JWM

---

## PREMISE

A 55-year-old woman who has governed $650 million bank programs cannot push a file to a website she owns. The wall isn't the code. It's the door. This is the four minutes where she walks through it.

---

## STRUCTURE

Three movements: the wall, the door, the other side.

**0:00-1:00 — THE WALL**
**1:00-2:30 — THE DOOR**
**2:30-3:30 — THE OTHER SIDE**
**3:30-4:00 — WHAT IT MEANT**

---

## SHOT LIST + SCRIPT

### ACT 1: THE WALL (0:00 - 1:00)

**SHOT 1 — 0:00-0:08**
Visual: Black screen. Text appears, one line at a time:

> She has governed $650 million bank programs.
> She cannot push a file to a website she owns.

Audio: Silence, then a single terminal cursor blink sound.

**SHOT 2 — 0:08-0:20**
Visual: Screen recording. A Mac terminal. Clean prompt. The cursor blinks.

Audio (VO):
> This is my terminal. It's where I've been learning to work with AI agents. Tonight I wanted to do something simple: take a document I wrote and push it to a private GitHub repository. Four files. My files. To my own page on the internet.

**SHOT 3 — 0:20-0:35**
Visual: Screen recording. She types: `./setup-and-push.sh`

Audio (VO):
> I had a script. One command. I pressed enter.

**SHOT 4 — 0:35-0:52**
Visual: Screen recording. The output appears line by line:

```
=== cyborg-infra setup ===
Not logged into GitHub. Starting login...
./setup-and-push.sh: line 17: gh: command not found
```

Audio (VO):
> Command not found. I don't have the tool the script assumed I'd have. First wall.

**SHOT 5 — 0:52-1:00**
Visual: Screen recording. She types: `brew install gh` and waits.

Audio (VO):
> So I install it. This part works. I try again.

---

### ACT 2: THE DOOR (1:00 - 2:30)

**SHOT 6 — 1:00-1:15**
Visual: Screen recording. The script runs again. GitHub login flow appears in browser. She authenticates. Returns to terminal.

Audio (VO):
> Now it asks me to log into GitHub. A browser opens. I authenticate. It works. The script creates my private repository. I can see it — empty, but it exists.

**SHOT 7 — 1:15-1:30**
Visual: Screen recording. The script continues. Then an error appears (or appears to — the push silently fails). The repo stays empty.

Audio (VO):
> But no files land. The repository is created but nothing is in it. The script hit SSH, I authenticated with HTTPS. The door opened but nothing went through.

**SHOT 8 — 1:30-1:50**
Visual: Screen recording. She types the manual commands, one at a time:

```
git init
git remote add origin git@github.com:Espi411/cyborg-infra.git
git add -A
git commit -m "init: infrastructure design document and project README"
git branch -M main
git push -u origin main
```

Audio (VO):
> So I do it by hand. Six commands. I don't fully understand each one yet. But I type them. I watch what happens. Each one does a small thing — initialize, connect, stage, commit, name, push.

**SHOT 9 — 1:50-2:10**
Visual: Screen recording. The push fails with an SSH permission error.

Audio (VO):
> The push fails. SSH key issue. The authentication method I set up in the browser doesn't match the method the script uses to connect. Second wall.

**SHOT 10 — 2:10-2:30**
Visual: Screen recording. She types one command:

```
git remote set-url origin https://github.com/Espi411/cyborg-infra.git
git push -u origin main
```

Audio (VO):
> One line. Switch the connection from SSH to HTTPS. Try again.

---

### ACT 3: THE OTHER SIDE (2:30 - 3:30)

**SHOT 11 — 2:30-2:45**
Visual: Screen recording. The push succeeds. Terminal shows the file upload progress. Files land.

Audio (VO):
> It works. Four files. They land on a private page that nobody will ever see, except me. And I'm proud. Not because it was hard — it wasn't, not really. Because the wall I hit wasn't the code. It was the door.

**SHOT 12 — 2:45-3:05**
Visual: Screen recording. She opens the GitHub repo in browser. The files are there: README.md, infra-design.md, setup-and-push.sh. A small private repository. Her name on it.

Audio (VO):
> This is what the door looks like from the other side. It's just a webpage with four files on it. But I built the files, I wrote the script, I hit the wall, and I walked through it. Nobody showed me. I learned it by doing it.

**SHOT 13 — 3:05-3:30**
Visual: Slow zoom on the repo file list. Hold.

Audio (VO):
> The lesson isn't about git. It's not about GitHub. It's about the fact that every tool like this has a door, and the door is always easier than it looks from outside, and nobody who's already through it remembers to tell you that.

---

### ACT 4: WHAT IT MEANT (3:30 - 4:00)

**SHOT 14 — 3:30-3:45**
Visual: Black screen. Text:

> The script was written by an AI agent I work with.
> The wall was real.
> The door was real.
> Walking through it was mine.

Audio (VO):
> The script I ran was written by an AI agent I've been learning to work with. It wrote the commands. It couldn't run them for me. The wall was real. The door was real. Walking through it — that was mine.

**SHOT 15 — 3:45-4:00**
Visual: Black screen. Text:

> PUSH
> A 4-minute video about learning to code
> by hitting a wall and walking through it
> Mescalito, 2026

Audio: Terminal cursor blink sound. Silence.

---

## PRODUCTION NOTES

### Tone
- Quiet. Not triumphant. The pride is real but understated.
- VO is Mescalito's voice, recorded clean, close-mic.
- No music in the wall/door sections. Optional low ambient pad under Act 4.
- The screen recordings are the hero — let them breathe.

### Visual style
- Actual terminal screen recordings from the real session (re-record if needed for clarity)
- No b-roll. No talking head. Just the terminal, the browser, the text.
- Black screen text cards between acts are the only "editing" flourish.
- Font for text cards: monospace. Same as the terminal.

### What JWM needs
- Screen recordings of: the failed script run, the gh install, the auth flow, the manual commands, the failed SSH push, the HTTPS fix, the successful push, the repo in browser
- If the real screen recordings weren't captured: re-create them by running the commands again. The errors are reproducible.
- VO script is above. ~600 words. At conversational pace that's about 4 minutes.
- Text cards are specified inline — black screen, monospace text, timed to the VO.

### Pacing
- Act 1 (wall): brisk. Errors come fast. Frustration is in the pace, not the VO.
- Act 2 (door): slow down. Each command gets its own beat. Let the viewer see each one.
- Act 3 (other side): hold. Let the success sit. Don't rush to meaning.
- Act 4 (what it meant): the VO lands the theme. Text cards close it.

### Length check
- 600 words at ~150 wpm = 4 minutes. Right on target.
- If VO runs long, cut Act 1 shots 4-5 (the brew install can be implied).
- If VO runs short, hold Act 3 shot 13 longer — let the repo file list sit on screen.

---

## WHY THIS VIDEO WORKS

1. **It's honest.** No fake struggle, no manufactured drama. A real woman hit a real wall and walked through it.

2. **It's specific.** Terminal commands, real errors, real fixes. Not abstract "learn to code" messaging.

3. **It's universal.** Every person who has ever felt locked out of tech recognizes this exact experience. The $650M detail isn't bragging — it's context that makes the wall feel real.

4. **It's the AI adopter identity made tangible.** She's not a coder. She's not pretending to be one. She's someone who uses AI to write code she then learns to run. That's the model. This video shows it.

5. **It's four minutes.** Long enough to feel something. Short enough to actually watch.

---

## YACHAY NOTES

This is a public-facing artifact. It belongs in yachay because:
- It demonstrates the "learning through other mediums" approach
- It models the AI-adopter identity (not coder, not passive user — something between)
- It's shareable and self-contained
- The script/storyboard itself is a reusable template for future learning-story videos

If produced, the finished video could live on YouTube. The script stays here.
