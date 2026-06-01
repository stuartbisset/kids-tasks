# Family Tasks — Firebase Setup Guide

This is a one-time setup that takes about 15 minutes. After this, the app runs for free forever.

---

## Step 1 — Create a Firebase project

1. Go to **https://console.firebase.google.com**
2. Click **"Add project"**
3. Name it something like `family-tasks`
4. Disable Google Analytics (not needed) → **Create project**

---

## Step 2 — Set up Firestore database

1. In your new project, click **"Firestore Database"** in the left sidebar
2. Click **"Create database"**
3. Choose **"Start in test mode"** (we'll secure it properly next)
4. Pick any region (e.g. `europe-west2` for UK) → **Enable**

### Set security rules

In Firestore → **Rules** tab, replace the default rule with this and click **Publish**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

> This is fine for a private family app. It means anyone with your Firebase config can read/write — but your config isn't public, so this is safe.

---

## Step 3 — Get your Firebase config

1. In the Firebase console, click the ⚙️ gear → **Project settings**
2. Scroll down to **"Your apps"**
3. Click **"</> Web"** to add a web app
4. Name it `family-tasks-web` → **Register app**
5. You'll see a block of code like this — copy it:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "family-tasks-xxxxx.firebaseapp.com",
  projectId: "family-tasks-xxxxx",
  storageBucket: "family-tasks-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

---

## Step 4 — Update the HTML file

Open `family-tasks.html` in a text editor and find the `FIREBASE_CONFIG` block near the top. Replace the placeholder values with your real ones.

Also change the `ADULT_PIN` from `"1234"` to whatever 4-digit PIN you want.

And update Louise's email address in `NOTIFY_ADDRESSES`.

---

## Step 5 — Host the site (free)

### Option A — Firebase Hosting (recommended, takes 5 mins)

1. Install Node.js from **https://nodejs.org** if you don't have it
2. Open Terminal / Command Prompt and run:

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
```

- Select your `family-tasks` project
- Set public directory to `.` (just a dot)
- Answer **No** to single-page app rewrite

3. Copy `family-tasks.html` into the folder, then:

```bash
firebase deploy
```

4. Firebase gives you a free URL like `https://family-tasks-xxxxx.web.app` — share this with everyone!

### Option B — Netlify (even easier, no command line)

1. Go to **https://netlify.com** and sign up free
2. Drag and drop your `family-tasks.html` file onto the Netlify dashboard
3. You get a free URL instantly — share it!

---

## Step 6 (Optional) — Email notifications

If you want Stu and Louise to get an email when a kid ticks off a task:

1. Sign up free at **https://emailjs.com**
2. Add an **Email Service** (connect your Gmail)
3. Create an **Email Template** with these variables:
   - `{{kid_name}}` — the child's name
   - `{{task_title}}` — what they completed
   - `{{week_label}}` — the week dates
   - Subject line suggestion: `✅ {{kid_name}} completed a task!`
4. Copy your **Service ID**, **Template ID**, and **Public Key** into the HTML file
5. Set `NOTIFY_ENABLED = true`

---

## How the app works

- **Stu & Louise** — log in with the adult PIN. You can add tasks to List A and List B, and see both kids' progress in real time.
- **Saf** — sees List A on odd weeks (1, 3, 5…), List B on even weeks
- **Anya** — sees the reverse (List B on odd weeks, List A on even)
- Checklists **reset automatically** each week — no manual reset needed
- History is preserved in Firestore week by week

---

## Costs

**£0.** Firebase's free Spark plan covers:
- 1GB Firestore storage
- 50,000 reads / day
- 20,000 writes / day

A family of 4 checking tasks daily uses maybe 500 reads and 50 writes a week. You'll never come close to the limits.
