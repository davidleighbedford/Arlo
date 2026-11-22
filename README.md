# Arlo · Play & Progress

An installable web app tracking Arlo's development from **12 to 18 months**,
guided by the **Ages & Stages Questionnaire (ASQ-3)** and the CDC/AAP and NHS
milestone checklists. It has research-based activities with cited evidence, a
priority "What to do today" list, 18–24 month stretch targets, a Potty plan,
a research page,
and **live sync between two phones** behind a **family password**.

Your Firebase project (`arlo-5d8a6`) is already configured inside `index.html`.
It uses **Cloud Firestore** for the data and **Firebase Authentication
(email/password)** for the password. You sign in once per phone — the session is
remembered, so you don't re-enter it every time.

> The `databaseURL` line in the config is harmless but unused (this app uses
> Firestore, not the Realtime Database).

## Files (upload them all together, in one folder)
- `index.html` – the app (config inside)
- `manifest.json` – installable web-app name + icons
- `research.html` – the evidence behind the activities, with an interactive chart
- `sw.js` – offline support (cache `arlo-v7`)
- `icon-192.png`, `icon-512.png`, `apple-touch-icon.png` – home-screen icons

---

## One-time setup in the Firebase console (`arlo-5d8a6`)
<https://console.firebase.google.com>

**1. Turn on Cloud Firestore**
Build → **Firestore Database** → **Create database** → choose a location
(e.g. `europe-west2`) → start in **Production mode** → Enable.

**2. Turn on the password (Authentication)**
Build → **Authentication** → **Get started** → **Sign-in method** tab →
enable **Email/Password** → Save.

**3. Set the family password**
Easiest: once the site is published (step 5), open it, type a password, and tap
**"First time here? Set the password."** That creates the shared account.
*(Console alternative: Authentication → Users → Add user, email
`family@arlo-5d8a6.web.app` and your chosen password.)*

**4. Publish the security rule**
Firestore Database → **Rules** → paste this → **Publish**:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /trackers/{id} {
      allow read, write: if request.auth != null;
    }
  }
}
```
This blocks anyone who isn't signed in — the data is now behind the password.

---

## 5. Publish to GitHub Pages
Put all the files in your GitHub Pages repo (root or a folder), commit & push.
Your app is live at e.g. `https://YOURNAME.github.io/` (or `…/arlo/`).

## 6. Install + sign in on both phones
1. Open the address in **Safari** (iPhone) / **Chrome** (Android).
2. Sign in with the family password (set it the first time via the link).
3. **Add to Home Screen** (iPhone: Share → Add to Home Screen).
4. Open the app → **⇪ Share & sync** should show **🟢 Live sync is ON**.

You only enter the password once per phone; it's remembered after that. If you
ever need to sign out, there's a **🔒 Lock app** button in the Share & sync panel.

---

## Your existing data is kept
Everything you've already entered lives in the `trackers/arlo` document and is
untouched by these changes. After you sign in, it loads exactly as before.

## What syncs
Activity sliders, skill tick-boxes, the review notes, **and** the stretch-target
switches/sliders — all write to the one shared document. A guard stops a live
update from interrupting a slider you're dragging or text you're typing.

## Updating later
After pushing changes, bump the version in `sw.js` (`arlo-v7` → `arlo-v8`) so
already-installed phones pull the new code/icon. On Android, if an old icon
sticks, remove and re-add the home-screen shortcut.

## Privacy
With the rule above, only someone signed in with the password can read or edit
the tracker. Use a password you're happy sharing just between the two of you.

## 12–18 month update (November 2026)
- Milestones are grouped **by 15 months** and **by 18 months**. Ticks for
  milestones that carried over (first words, following a request without a
  gesture, standing alone, helping with dressing) are kept.
- The 9–12 month milestones and activities sit in collapsed **Earlier** sections
  in each category, with your ticks and slider values intact. They no longer
  count towards levels or the Today list.
- New activities start fresh at "not quite there". Stretch targets are now
  18–24 months with their own ids, so earlier stretch switches don't carry over.
- "What to do today" shows the top 8, with a button to show the rest.
- Nothing in the Firestore document changes shape: same `trackers/arlo` doc,
  same `payload` field.

## Potty plan (November 2026)
- Open it from the **Potty plan** button under "What to do today". Close with
  the X or a swipe down from the top.
- **Timeline:** stages from 12 months to 5 years, dated from Arlo's birthday,
  with a "you are here" marker. Tap a stage to set its status and see what to
  focus on, the research, and its activities.
- **Planned start and life events:** set the date you plan to stop daytime
  nappies and add events such as nursery or holidays. You'll see a warning if
  the date is within about a month of an event, or outside 18 to 30 months.
- **Family plan:** words, sit times, praise, accidents, naps and nights, and
  notes for a carer, pre-filled from the research. Print or share a clean copy.
- Potty activities for the current stage also appear in "What to do today",
  with a potty label. They don't count towards the doughnuts or levels.
- Everything is stored under `potty` in the same `trackers/arlo` document.
  Saves now wait until the first load from Firebase has finished, so an early
  tap can't overwrite your saved data.
