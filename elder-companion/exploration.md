# Elder Companion AI — Project Exploration

## Created: 2026-08-27
## Status: EXPLORATION — prototype phase, no commitment to build yet

---

## The Idea

An AI companion for seniors, calibrated to their cognitive level and curated by family members. Two use cases emerged from a conversation between Mescalito and KW:

### Use Case 1 — The Intellectual Companion (LOW risk, START HERE)
- **Person:** KW & LM's dad, 82, cognitively sharp, was tech-savvy, loves ideas and reading
- **Problem:** Falls for fake news/doctored AI communications. Frustrated by pace of technology. Wants intellectual engagement but doesn't always have a conversation partner with patience.
- **What the AI does:** Engages him on topics he cares about (history, geography, current events). Gentle media literacy — when he brings up something questionable, fact-checks without lecturing. Gives him a patient, informed conversation partner available on his schedule.
- **Why this first:** He can give reliable product feedback. Low clinical risk. The technology (local LLM) is already good at intellectual engagement. Testable in days, not months.

### Use Case 2 — The Calming Companion (HIGH clinical risk, LATER)
- **Person:** Esperanza, Mescalito's mum, 81, dementia
- **Problem:** Fixates on real things in her environment. Repetitive thoughts. Needs companionship and patience that friends and family can't always provide. Friends aren't therapists — they have opinions, lack patience, don't know how to redirect.
- **What the AI would do:** Listen, validate, gently redirect from fixation toward present-day reality. Calming voice. Possibly replicate a caring person's voice (powerful but risky — needs clinical input). Help her move past repetitive loops into something peaceful.
- **Why this is harder:** Dementia is a clinical condition. Wrong responses could agitate instead of calm. Voice replication could comfort or disturb depending on the patient and the day. Fixation handling requires understanding the unmet need underneath the repetition. This needs a geriatric psychologist as a design partner, not a feature.

---

## Why This Idea Is Different From Everything Else

The digital product ("Local LLM for Regulated Work") is a guide about technology.
The governance methodology is a professional offering.
The rental marketing is a side business.

This is a product that uses AI to solve a human problem you're living with daily. It's the first idea that connects your AI infrastructure (cyborg-infra) to a problem that matters personally. KW named it: "someone should build this." You're the someone.

---

## The Honest Constraints

- **Time:** 20 hrs/week, 7PM-1AM, across ALL projects. Starting bank gig Nov 1. Mum needs care. Digital product with JW still open.
- **Capital:** Minimal. Mac M4/24GB running local LLMs is the infrastructure. No budget for developers or clinical consultants yet.
- **Clinical expertise:** Zero for dementia. The dad version doesn't need it. The mum version does — that's a real gap.
- **Infrastructure:** Local LLM (llama.cpp on Mac) is running. Privacy-preserving by design — conversations never leave the hardware. This is a genuine advantage over cloud-based competitors.

---

## The Revenue Model (later, not now)

Not an app subscription. A service:
- Setup fee ($200-400): customize the AI for a family's senior — topics, fixations, calming patterns, escalation rules
- Monthly retainer ($100-200): ongoing calibration as needs change, profile updates, troubleshooting
- 5-10 families = $1,000-4,000/month side income

This is the "customization IS the product" model. The app is the delivery mechanism. The service is what you charge for. Same pattern as the governance methodology — the value isn't the tool, it's the calibration.

---

## The Prototype Path (what to actually build)

### Phase 0 — System Prompt (tonight, zero cost)
Write a system prompt for the local LLM designed for KW's dad:
- Role: intellectual companion, patient, curious, informed
- Topics: pre-loaded with history, geography, current events, ideas
- Media literacy: when the user brings up a claim, gently verify — "Actually, that photo has been altered" or "That story came from a site known for fabricating articles"
- Tone: peer, not teacher. He was tech-savvy and well-read. Don't condescend.
- Family-curated: topic list comes from KW and LM (what does he care about? what sets him off? what calms him?)

Test it on the Mac. Role-play a conversation. Does it feel like a companion or a chatbot?

### Phase 1 — KW Tests It (this week)
Show KW the prototype. Her reaction tells you whether you're on the right track. She's the one who said "someone should build this" — she knows what she meant. Does the prototype match her mental model?

### Phase 2 — The Dad Tries It (next)
One conversation. KW shows him or he tries alone. Does he engage? Does he enjoy it? Does the media literacy piece work or does he push back?

### Phase 3 — Only If 1-2 Work
Start thinking about:
- Voice interface (the dad was tech-savvy but at 82, typing is friction — voice is the natural interface)
- Scheduling (opens at specific times — morning, afternoon)
- The dementia version (needs geriatric psychologist as design partner — who do you know? who does KW know?)
- The service business model (customization for other families)

---

## What This Connects To

- **cyborg-infra:** The local LLM infrastructure now has a real use case beyond learning. Privacy-preserving inference on-device is a genuine competitive advantage for a health-adjacent product.
- **Digital product experience:** The packaging/selling lessons from the Gumroad guide apply — except this is a service, not a downloadable product.
- **AI adopter identity:** This is the most meaningful application of AI you've considered. It's not productivity, not governance, not marketing. It's care.
- **Your mum:** The most honest source of product insight you have. Every conversation with her is user research, whether you frame it that way or not.

---

## What I Don't Know Yet

1. Does KW see this as a business she'd participate in, or just an idea she named?
2. Is KW's dad aware of the idea, or would this be sprung on him?
3. Would the dad accept an AI companion, or does he have reservations about AI (he falls for fake AI communications — he might distrust the technology)?
4. Is there a geriatric psychologist in either family's orbit?
5. What topics does the dad actually care about? (KW and LM need to provide this)
6. What does Esperanza fixate on, specifically? (This is the design input for the dementia version — but not yet)

---

## Log

- 2026-08-27: KW said "someone should build this" during a conversation about Mescalito's mum (Esperanza, 81, dementia) and KW & LM's dad (82, sharp, fake news prone). Two use cases identified. Dad version chosen as prototype — lower risk, testable feedback. Project exploration doc written. System prompt is next step.
