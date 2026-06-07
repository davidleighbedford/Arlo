# Arlo · Play & Progress

A bright, installable web app tracking Arlo's development against the ASQ-3
9–10 month milestones, with research-based activities, a "What to do today"
priority list, and **live sync between two phones** via Firebase.

Your Firebase project (`arlo-5d8a6`) is **already configured inside
`index.html`** — you just need to switch on the database and publish. (A
Firebase web config/API key is meant to be public; security comes from the
database rule in step 2, not from hiding the key.)

> **Important:** this app uses **Cloud Firestore**, *not* the Realtime Database.
> The `databaseURL` line in the config is harmless but unused.

## Files (upload them all together, in one folder)
- `index.html` – the app (your Firebase config is inside it)
- `manifest.json` – makes it installable (web-app name + icons)
- `sw.js` – offline support (cache version `arlo-v3`)
- `icon-192.png`, `icon-512.png`, `apple-touch-icon.png` – home-screen icons

---

## Step 1 — Turn on Cloud Firestore
1. Go to <https://console.firebase.google.com> and open project **arlo-5d8a6**.
2. Left menu → **Build → Firestore Database → Create database**.
3. Pick a location (e.g. `europe-west2` for the UK), and start in
   **Production mode** (we set the rule next). Click **Enable**.

## Step 2 — Publish the security rule
In **Firestore Database → Rules**, replace what's there with this and click
**Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /trackers/{id} {
      allow read, write: if true;
    }
  }
}
```

This lets the app read/write one shared document (`trackers/arlo`). See the
privacy note at the bottom.

## Step 3 — Publish to GitHub Pages
1. Put all the files above into your GitHub Pages repo (root, or a folder like
   `/arlo/`). Commit & push.
2. Your app is live at e.g. `https://YOURNAME.github.io/` (or `…/arlo/`).

## Step 4 — Install on both phones
1. Open the address in **Safari** (iPhone) on both your phones.
2. Tap **Share → Add to Home Screen** for a one-tap app icon.
3. Open it and tap **⇪ Share & sync** at the top. You should see
   **🟢 Live sync is ON**. Tick something on one phone — it appears on the
   other within a second or two.

That's it. Both phones load the same file, so both use the same config and the
same `trackers/arlo` document — one shared tracker.

---

## What syncs
Everything writes to the shared document: activity sliders, skill tick-boxes,
and the free-text **review notes** in the Overall section. There's a built-in
guard so a live update from your partner won't yank a slider you're dragging or
interrupt you mid-typing. There's also a manual **copy/paste sync code** in the
Share panel as a backup (works offline / without Firebase).

## Updating later
The service worker caches the app, so after you push changes, bump the version
inside `sw.js` (e.g. `arlo-v3` → `arlo-v4`) so phones that already installed it
pull the new code and icon on next open.

## Privacy note
The rule above lets anyone who knows your page URL read or edit the tracker. For
a baby activity list that's usually fine. To lock it down you can:
- change `SHARE_ID` in `index.html` from `"arlo"` to a hard-to-guess word (use
  the same value on both phones), and/or
- ask me to switch the app to require a simple Firebase sign-in.

## Troubleshooting
- **Badge says "this device only":** Firestore isn't enabled yet, the rule
  isn't published, or the browser blocked the connection. Recheck steps 1–2 and
  make sure you're opening the page over `https://` (GitHub Pages always is).
- **Nothing syncs between phones:** confirm both are on the published URL (not a
  local file) and both show the 🟢 badge.
