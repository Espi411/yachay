# How to Create the WiFi-for-Email Capture Flow

Step-by-step guide. Build this before Jul 31 (next guest arrivals).

Total time: ~30 minutes if you have the WiFi password(s) ready.

---

## BEFORE YOU START

You need:
- The WiFi network name (SSID) and password for each property
- A Google account (any Gmail works)
- 5 minutes with KW or LM to confirm the WiFi details

Question to answer first: do all 3 units share one WiFi network/password, or does each unit have its own?
- One shared WiFi = one form, one QR code, one card design
- Separate WiFi per unit = one form per unit (or one form with a dropdown to select unit), three QR codes

The guide below assumes ONE shared WiFi. If separate, repeat per unit.

---

## STEP 1 — Create the Google Form

1. Go to forms.google.com (on your Mac or phone browser)
2. Click the blank "+" to start a new form
3. Name it: "Guest WiFi Access" (this title shows on the form — keep it simple)
4. Add the description: "Welcome! Enter your name and email to get the WiFi password. We'll also send you occasional updates about availability and special rates for returning guests."

### Add the fields

Question 1:
- Click "Untitled question"
- Type: "Your name"
- Question type: Short answer (default)
- Click "Required" toggle — ON

Question 2:
- Click the "+" on the right sidebar to add another question
- Type: "Your email"
- Question type: Short answer (default)
- Click the three dots (...) in the question box → "Response validation" → select "Text" → "Email" — this ensures they enter a valid email
- Click "Required" toggle — ON

That's the whole form. Two fields: name + email.

---

## STEP 2 — Set the Confirmation Message (where the WiFi password shows)

This is the key step. After someone submits the form, Google shows a confirmation message. You put the WiFi password there.

1. Click the "Settings" tab at the top of the form
2. Scroll to "Responses" section, expand it
3. Turn ON "Collect email addresses" — optional but useful (Google collects a verified email separately from the form field)
4. Go to "Presentation" section (also under Settings)
5. Find "Confirmation message"
6. Turn ON "Custom"
7. Type your message:

   Here's your WiFi password! 📶

   Network: [WIFI NETWORK NAME]
   Password: [WIFI PASSWORD]

   Save it to your phone now. Enjoy your stay!

   Follow us on Instagram: @[INSTAGRAM HANDLE]

8. Click "Save"

Now when a guest fills out the form, they immediately see the WiFi password on screen.

---

## STEP 3 — Get the Form Link

1. Click the "Send" button (top right of the form)
2. Click the link icon (looks like a chain)
3. Click "Shorten URL"
4. Copy the shortened URL — it looks like: https://forms.gle/abc123XYZ

Save this link. You need it for the QR code.

---

## STEP 4 — Generate the QR Code

Use any free QR code generator. No signup needed.

Recommended: goqr.me (qr-code-generator.com also works but may want signup)

1. Go to goqr.me in your browser
2. Choose the "URL" type
3. Paste your shortened Google Form link
4. Download the QR code as a PNG image

That's it. You now have a QR code that opens the form when scanned.

---

## STEP 5 — Design the Welcome Card

This is the physical card that goes in each property. It needs:
- A welcome message
- Instructions ("scan to get WiFi")
- The QR code
- Your Instagram handle

### Card template (copy this into Canva, Word, or even Google Docs)

```
┌─────────────────────────────────────┐
│                                     │
│   Welcome! 🏡                       │
│                                     │
│   To get the WiFi password:         │
│                                     │
│   1. Scan the QR code below         │
│   2. Enter your name and email      │
│   3. We'll show you the password    │
│                                     │
│   ┌─────────────┐                  │
│   │             │                  │
│   │  [QR CODE]  │                  │
│   │             │                  │
│   └─────────────┘                  │
│                                     │
│   Or follow us on Instagram:        │
│   @[INSTAGRAM HANDLE]              │
│                                     │
│   Questions about your stay?        │
│   Message us on Instagram or        │
│   through Airbnb.                   │
│                                     │
└─────────────────────────────────────┘
```

### Where to place the card

- On the kitchen counter or table where guests arrive first
- Next to the coffee maker or welcome basket (wherever they naturally look)
- NOT in a drawer — they won't find it

### Printing

- Print at home on cardstock, or
- Use a print shop (Staples, VistaPrint) for 3-5 copies per property
- Laminate them — they last longer in a rental

---

## STEP 6 — Test the Full Flow

Before placing in properties:

1. Open the QR code image on your phone
2. Scan it with your phone camera
3. Fill in the form (use your own name/email)
4. Submit
5. Verify: do you see the WiFi password in the confirmation message?
6. Check: does your email show up in the Google Sheet?

### Where the emails go

Google Forms automatically saves all responses to a Google Sheet.
1. Go to your form
2. Click "Responses" tab
3. Click the green spreadsheet icon (top right of responses)
4. It opens a Google Sheet with all submissions

This is your contact list. Save the link. This is the asset.

---

## STEP 7 — Place the Cards

KW and LM do this part:

1. Print the cards (or have Mescalito print)
2. Place one card per property in a visible spot
3. That's it. The system runs itself from here.

---

## TROUBLESHOOTING

**Guest says the confirmation didn't show the password:**
- Check the confirmation message is still set in Settings → Presentation → Confirmation message
- Make sure you clicked "Save" after editing

**QR code doesn't scan:**
- Make sure the image is large enough (at least 2cm x 2cm on the card)
- Print at high resolution — blurry QR codes don't scan

**Someone filled the form but no email in the sheet:**
- Open the form → Responses tab → click the spreadsheet icon
- If the sheet doesn't exist yet, Google will create one on first response

**Want to change the WiFi password later:**
- Just update the confirmation message in the form — no need to reprint the card (the QR code still points to the same form)

---

## WHAT THIS GIVES YOU

- Every guest who wants WiFi gives you their email — automatically
- No one has to remember to ask
- Emails land in a Google Sheet you control
- The card also drives Instagram follows
- Changing the WiFi password is a 30-second edit (no reprinting)
- Cost: $0 (Google Forms, goqr.me, all free)

## WHAT COMES NEXT (NOT YET)

- Nurture email: a welcome email 2 weeks after their stay ("How was your trip? Here's what's coming up...")
- Direct booking process: when a past guest DMs or emails, how KW/LM handle it
- Instagram content: photos of the properties, local tips, availability posts
- Per-property forms: if you want separate WiFi per unit
- Guest WiFi captive portal: a more polished version that redirects to the form instead of showing the password (requires router changes — v2, not now)
