# Arlo · Play & Progress

A bright, installable web app that tracks Arlo's development against the ASQ-3
9–10 month milestones, with research-based activities. Built to run on **GitHub
Pages** and **sync live between two phones** via a free Firebase database.

## Files (keep them all in the same folder)
- `index.html` – the app
- `manifest.webmanifest` – makes it installable
- `sw.js` – offline support
- `icon-192.png`, `icon-512.png`, `apple-touch-icon.png` – home-screen icons

---

## 1. Publish to GitHub Pages
1. Drop all of these files into your GitHub Pages repo (e.g. the repo root, or a
   folder like `/arlo/`). Commit & push.
2. Your app is live at, for example:
   `https://YOURNAME.github.io/`  (or `https://YOURNAME.github.io/arlo/`).
3. Open that URL on both phones. On iPhone: open in **Safari → Share →
   Add to Home Screen** for a one-tap app icon.

At this point it already works — but progress saves **on each phone separately**.
To share the *same* data live, do step 2.

---

## 2. Turn on live sharing (free Firebase, ~5 minutes)
GitHub Pages can't store shared data on its own (it only serves files), so we use
Firebase Firestore as a tiny shared database.

1. Go to <https://console.firebase.google.com> → **Add project** (any name).
   You can skip Google Analytics.
2. In the project, left menu → **Build → Firestore Database → Create database**.
   Choose a location, start in **production mode** (we'll set a rule next).
3. Left menu → **Project settings (gear) → General**. Scroll to *Your apps* →
   click the **</>** (web) icon → register an app (any nickname, no hosting
   needed). Firebase shows you a `firebaseConfig = { ... }` object.
4. Open `index.html`, find the `FIREBASE_CONFIG` block near the top of the
   `<script>`, and paste your values in (uncomment the lines). Example:
   ```js
   const FIREBASE_CONFIG = {
     apiKey: "AIza...",
     authDomain: "your-app.firebaseapp.com",
     projectId: "your-app",
     storageBucket: "your-app.appspot.com",
     messagingSenderId: "000000000000",
     appId: "1:000:web:abc123"
   };
   const SHARE_ID = "arlo";   // keep identical on both phones
   ```
5. In Firestore → **Rules**, paste this and **Publish** (simple open rule for one
   family tracker):
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /trackers/{id} { allow read, write: if true; }
     }
   }
   ```
6. Commit & push the edited `index.html`. Reload on both phones — the
   "Share & sync" panel will show **🟢 Live sync is ON**. Every tick now syncs
   between you both in real time.

### A note on privacy
The config and the open rule above mean anyone who finds your page URL could read
or edit the tracker. For a baby activity list that's usually fine. If you'd like
it locked down (e.g. require a sign-in, or a secret SHARE_ID that's hard to
guess), I can set that up — just ask.

---

## No Firebase? Use the sync code
Tap **⇪ Share & sync** in the app to copy a "progress code". Your partner pastes
it into the same panel on her phone to load your data. It's manual (not live),
but needs no setup and works offline.
