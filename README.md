# Arlo · Play & Progress

A bright, installable web app tracking Arlo's development against the **10-month
Ages & Stages Questionnaire (ASQ-3)**, with research-based activities, a
priority "What to do today" list, stretch targets, and **live sync between two
phones** — now protected by a **family password**.

Your Firebase project (`arlo-5d8a6`) is already configured inside `index.html`.
It uses **Cloud Firestore** for the data and **Firebase Authentication
(email/password)** for the password. You sign in once per phone — the session is
remembered, so you don't re-enter it every time.

> The `databaseURL` line in the config is harmless but unused (this app uses
> Firestore, not the Realtime Database).

## Files (upload them all together, in one folder)
- `index.html` – the app (config inside)
- `manifest.json` – installable web-app name + icons
- `sw.js` – offline support (cache `arlo-v3`)
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
After pushing changes, bump the version in `sw.js` (`arlo-v3` → `arlo-v4`) so
already-installed phones pull the new code/icon. On Android, if an old icon
sticks, remove and re-add the home-screen shortcut.

## Privacy
With the rule above, only someone signed in with the password can read or edit
the tracker. Use a password you're happy sharing just between the two of you.
