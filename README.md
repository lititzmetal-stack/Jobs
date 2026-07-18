# Lititz Metal Co — Jobs & Time

A small internal web app for tracking shop and job hours. One worker taps their
name, clocks in/out of the shop and individual jobs. The Summary tab shows hours
by day and by job, and lets you mark chunks of days as paid.

## What's in this folder

```
index.html                    The app
manifest.json                 Makes it installable to a phone home screen
sw.js                         Service worker (install + basic offline)
icons/                        App icons (do not rename or move)
  icon-192.png
  icon-512.png
  icon-512-maskable.png
  apple-touch-icon.png
  favicon-32.png
README.md                     This file
```

Keep the `icons/` folder next to `index.html`. Don't rename anything.

---

## Step 1 — Create a Firebase project (one time)

1. Go to https://console.firebase.google.com and sign in with the Google
   account that should own this.
2. Click **Add project**. Name it something like `lititz-metal`.
   You can turn **off** Google Analytics — it isn't needed. Click **Create**.

## Step 2 — Create the database

1. Left sidebar → **Build → Firestore Database → Create database**.
2. Choose a location near you (any `us-east` region is fine for PA).
3. Start in **production mode** → **Enable**.

## Step 3 — Set the security rules

1. In Firestore, click the **Rules** tab.
2. Replace everything there with exactly this, then click **Publish**:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} { allow read, write: if true; }
     }
   }
   ```

   This leaves the database open, which is fine for a small private tool with no
   login. Anyone with the link can read/write, so treat the URL like a password
   and don't post it publicly.

## Step 4 — Get your config

1. Click the **gear icon** (top left, next to "Project Overview") → **Project settings**.
2. Scroll to **Your apps** → click the **`</>`** (web) icon.
3. Nickname it `lititz-metal-web`. Do **not** check "Firebase Hosting" here.
   Click **Register app**.
4. It shows a `const firebaseConfig = { ... }` block. Copy the whole object.

## Step 5 — Paste the config into the app

1. Open `index.html` in any text editor.
2. Near the top of the `<script>` section, find the `firebaseConfig` block with
   placeholder values (`YOUR_API_KEY`, etc.).
3. Replace it with the block you copied. It will look like:

   ```
   const firebaseConfig = {
     apiKey: "AIza...",
     authDomain: "lititz-metal.firebaseapp.com",
     projectId: "lititz-metal",
     storageBucket: "lititz-metal.appspot.com",
     messagingSenderId: "1234567890",
     appId: "1:1234567890:web:abcdef"
   };
   ```

4. Save the file.

## Step 6 — Put it online (pick ONE)

The app needs **https** to install to a phone. Both options below give you that
for free.

### Option A — GitHub Pages (same as your other tools)
1. Create a repo (or a folder in an existing one) and upload the **contents** of
   this folder — `index.html`, `manifest.json`, `sw.js`, and the `icons/` folder.
2. Repo → **Settings → Pages** → set the source to your branch → Save.
3. After a minute you'll get a URL like
   `https://yourname.github.io/lititz-time/`.

### Option B — Firebase Hosting
In a terminal in this folder:
```
npm install -g firebase-tools
firebase login
firebase init hosting     # pick your lititz-metal project; public dir = . ; single-page app = yes; overwrite index.html = NO
firebase deploy
```
You'll get a URL like `https://lititz-metal.web.app`.

## Step 7 — First run

1. Open your live URL.
2. Go to the **Summary** tab.
   - Under **Workers**, add your worker(s) (e.g. Carson).
   - Under **Jobs**, add the jobs people clock into.
3. Go to the **Clock** tab — it's ready. Pick a name, clock in to the shop,
   tap a job to clock into it.

## Step 8 — Install on a phone (optional)

- **iPhone (Safari):** Share button → **Add to Home Screen**.
- **Android (Chrome):** menu (⋮) → **Install app** / **Add to Home Screen**.

It lands on the home screen with the Lititz icon and opens full-screen.

---

## How the app works

**Clock tab**
- Pick your name (no PIN).
- **Clock In to Shop** starts your overall shift.
- Tap a job to clock into it. If you tap a job without being clocked into the
  shop, it starts your shop time automatically.
- Tapping your current job again clocks out of it. Switching jobs closes the old
  one. Clocking out of the shop closes whatever job you're on.

**Summary tab** (open to everyone — the worker can see their own hours)
- **Shop hours by day** — unpaid days with hours. Check the days you're paying
  and hit **Mark selected days as paid**. The rest stay outstanding.
- **Recently paid** — log of what's been settled.
- **Workers** — add/remove people.
- **Jobs** — add jobs, toggle active/inactive.
- **Hours by job** — tap a job to see its day-by-day breakdown and total.

## Changing things later

- **Add/remove workers or jobs:** do it in the Summary tab, no code editing.
- **The icon:** replace the files in `icons/` with same-named files.
- **Locking the "Mark as paid" action** behind a code, or adding a real login,
  are both straightforward additions if you want them later.

## A note on privacy

Because the Firestore rules are open, anyone with the URL can view and change
data. That's a reasonable trade for a tiny internal tool, but if you ever want it
genuinely locked down, the next step is Firebase Authentication (a simple Google
sign-in). Ask and it can be added.
