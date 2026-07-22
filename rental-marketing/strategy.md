# Rental Marketing — Strategic Plan
## KW & LM Properties · Coordinated by Mescalito
### Created: 2026-07-19

---

## The Opportunity

3 rental units (KW and LM co-host, each owns their own spaces). Currently 100% dependent on Airbnb for bookings. ~50 past guests, 2 currently on property.

Airbnb takes ~15% in host fees and — more importantly — owns the guest relationship. You never see their email. You can't market to them. Every booking starts from scratch on the platform.

The shift: capture guest contact during their stay → nurture the relationship → direct rebookings at full margin. Repeat guests who book directly are the highest-margin customers you'll ever have.

## The Funnel

```
Airbnb stay → In-stay capture → Nurture (email + Instagram) → Direct rebooking → Repeat
```

Each stage has one job:

1. **Capture** — get a way to reach them after they leave (email, Instagram follow, ideally both)
2. **Nurture** — stay present between stays so they think of you when they're ready to travel again
3. **Convert** — make direct booking easy and more attractive than Airbnb (price advantage, flexibility, first access to dates)
4. **Retain** — after a direct booking, the loop shortens — they're already in your system

## Immediate Action — CAPTURE THE REMAINING GUEST + SET UP FOR NEXT ARRIVALS

One guest has departed (email not captured — that window is closed). One guest remains (2 weeks). Next arrivals in 10 days (Jul 31).

The manual ask approach failed for the departed guest. The lesson: relying on someone to remember to ask is unreliable. We need a mechanism that captures emails automatically.

### The WiFi-for-Email Exchange (primary capture method)

This replaces the manual ask as the primary mechanism. The welcome card becomes the capture tool.

**The concept:**

Guests want WiFi. You want their email. Make it a trade.

1. Guest arrives → finds a welcome card in the property
2. Card says: "To get the WiFi password, scan this QR code and enter your email"
3. QR code → Google Form (name + email fields)
4. On form submit → Google Forms confirmation message displays the WiFi password
5. Email lands in a Google Sheet automatically — no one had to ask

**Why this works better than the manual ask:**

- Works for every guest, every time — doesn't depend on KW/LM remembering
- Fair value exchange — they get something they want (WiFi), you get something you want (email)
- Captures at check-in, not checkout — no last-minute rush
- Zero ongoing effort for KW/LM — the card does the work
- The card can also include the Instagram handle for a soft follow

**What Mescalito needs to build (this week, before Jul 31):**

- A Google Form with: name, email, and a confirmation message containing the WiFi password
- A QR code linking to that form (free QR generators work fine)
- A welcome card template that says: "Welcome! For WiFi access, scan the QR code and enter your email — we'll show you the password. Follow us on Instagram: @[handle]"
- Print cards for all 3 properties

**For the remaining current guest (test run):**

- KW or LM can try the card approach now — it's a low-pressure test: "We're setting up a new check-in system for future guests — would you mind testing it? You can get the WiFi password through this QR code"

**Google Forms confirmation message — how to set it up:**

Google Forms has a "Confirmation message" setting (Settings → Presentation → Confirmation message). Put the WiFi password there. When the guest submits the form, they see the password. Simple, no code needed.

Alternative: use a form add-on or Apps Script to auto-email the password, but that's more complexity than needed for v1.

## First 30 Days

### Week 1 (Jul 19-25)
- [ ] Mescalito: Create Google Form (name + email, confirmation message = WiFi password)
- [ ] Mescalito: Generate QR code linking to the form
- [ ] Mescalito: Create Instagram account (one combined account for all 3 properties)
- [ ] Mescalito: Draft welcome card template (WiFi instructions + QR code + Instagram handle)
- [ ] KW/LM: Test the card with the 1 remaining guest (low-pressure test run)

### Week 2 (Jul 26-Aug 1)
- [ ] First Instagram posts — the properties themselves (clean, well-lit, honest)
- [ ] KW/LM: Print and place welcome cards in all 3 properties BEFORE next arrivals (Jul 31)
- [ ] Test the signup flow end to end (have someone scan the QR and fill the form — does the WiFi password show?)
- [ ] Next guests arrive ~Jul 31 — first real-world test of the WiFi-for-email capture

### Week 3-4 (Aug 2-15)
- [ ] Set up a simple nurture email — one welcome email when they sign up, one follow-up 2 weeks later
- [ ] Instagram posting rhythm: 2-3 posts per week minimum (property photos, local tips, availability announcements)
- [ ] Document the direct booking process: how a guest books directly (email/DM → confirm dates → payment method → done)

## The Direct Booking Process (Keep It Simple)

When a past guest wants to rebook:

1. They DM you on Instagram or reply to an email
2. KW/LM confirm the dates are available
3. Send them a payment link (Stripe, PayPal, or even Interac e-Transfer — no platform needed)
4. Confirm booking

No website required for this stage. The Instagram DM or email IS the booking channel. A proper website comes later when volume justifies it.

## Content Approach for Instagram

Don't overthink this. The account's job is to stay present between stays.

Post types:
- **Property photos** — clean, natural light, show what it actually looks like
- **Local area** — restaurants, walks, things to do nearby (this is what guests actually engage with)
- **Availability** — "Aug 15-22 open at [property name] — DM to book direct"
- **Guest moments** — with consent, show people enjoying the space

Tone: warm, personal, not corporate. This is KW and LM's personality, not a brand.

## What to Measure (the only 3 numbers that matter right now)

1. **Capture rate** — what % of guests give you their email during their stay?
   - Target: 50%+ (if you're asking in person, this should be easy)
   - If below 30%, the ask or the card needs work

2. **Direct rebooking rate** — what % of captured guests book directly within 12 months?
   - Target: 15-20% (industry benchmark for first-year direct repeat)
   - This is the number that justifies the whole effort

3. **Instagram followers who are past guests** — the overlap between "stayed with us" and "follows us"
   - This is your warmest audience. Track it even if it's small.

Don't measure: follower count (vanity), post likes (noise), email open rates (premature — list is too small to be statistically meaningful yet).

## CASL Note (Canada's Anti-Spam Law)

You need consent to email people. Express consent is what you're collecting with the signup form — they're giving you their email specifically to receive booking communications. Keep a record of when and how they consented (the Google Form timestamp is your proof).

Don't add past guests to an email list without their consent — even if you somehow got their email. The signup form is your consent mechanism.

## What Comes Later (Not Now)

- A direct-booking website (when volume justifies the cost and maintenance)
- Email automation beyond a simple welcome + follow-up
- Paid Instagram ads (organic first — you're too small for ads to make sense)
- Per-property Instagram accounts (start with one combined, split later if one property dominates)
- A loyalty program or referral incentive

## Roles

- **KW & LM**: Guest interaction, placing cards, Instagram content (photos of their properties), responding to DMs, managing bookings
- **Mescalito**: Tools and systems (signup form, QR codes, Instagram setup, email setup), strategy, tracking, the "computer" piece
- **Corina (agent)**: Weekly review of state, suggest next actions, flag what's stuck, think through improvements

## Agent Workstream

A weekly cron job reviews the state file and delivers suggestions for next actions, flags what's stuck, and thinks through improvements. See `state.md` for the living document it reads from.

Until Telegram gateway is set up, the weekly review output is saved locally (viewable via `cronjob list`). Once Telegram is connected, it can deliver to the group there.
