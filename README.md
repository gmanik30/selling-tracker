# Selling Tracker — Android setup

Total cost: $0. Two things to set up once — Firebase (free cloud database) and GitHub Pages (free hosting) — then it installs on your phone like a real app.

## 1. Create a free Firebase project (~5 min)

1. Go to https://console.firebase.google.com and sign in with any Google account.
2. Click **Add project**, name it (e.g. "selling-tracker"), skip Google Analytics (not needed).
3. Once created, click the **</> (web)** icon to add a web app. Nickname it anything, skip hosting.
4. Firebase shows you a `firebaseConfig` object with keys like `apiKey`, `projectId`, etc. Copy those values.
5. Open `firebase-config.js` in this folder and paste your values in place of the `PASTE_...` placeholders. Save.

## 2. Turn on Firestore + Anonymous sign-in (~2 min)

1. In the Firebase console sidebar: **Build → Firestore Database → Create database**. Choose any region close to you, start in **production mode**.
2. Go to the **Rules** tab of Firestore and replace the rules with:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{userId}/sales/{saleId} {
         allow read, write: if request.auth != null && request.auth.uid == userId;
       }
     }
   }
   ```
   Click **Publish**. This means only your own signed-in device can read/write your own sales — nobody else's.
3. **Build → Authentication → Get started → Sign-in method → Anonymous → Enable**. This lets the app quietly sign you in without a login screen, so it stays one-tap simple.

## 3. Put the app online with GitHub Pages (~5 min)

1. Create a free GitHub account if you don't have one: https://github.com
2. Create a new repository (e.g. `selling-tracker`), public, no README/gitignore needed.
3. Upload all the files in this folder (`index.html`, `manifest.json`, `sw.js`, `firebase-config.js`, the three `icon*.png` files) to that repo — easiest via the GitHub web UI: **Add file → Upload files**, drag them all in, commit.
4. In the repo: **Settings → Pages → Source → Deploy from a branch → main → / (root) → Save**.
5. GitHub gives you a live URL after a minute or two, like:
   `https://yourusername.github.io/selling-tracker/`

## 4. Install it on your Android phone

1. Open that URL in **Chrome** on your Android phone.
2. Tap the **⋮** menu → **Add to Home screen** (Chrome may also just prompt you automatically).
3. Confirm. You'll get a real app icon on your home screen that opens full-screen, no browser bar.

That's it — entries you log now save to Firestore under your own anonymous account and will sync to any device signed into that same install. Next step when you're ready: wrapping the same site for iPhone (it's the same URL, just added to home screen via Safari instead of Chrome).

## Notes
- All your data lives in **Firestore** (Google Cloud), not Google Drive — you won't see it as a spreadsheet, but the free tier comfortably covers personal use (50K reads/20K writes per day, 1GB storage).
- `firebase-config.js` values are safe to be public in a client app — your Firestore **rules** are what actually protect your data, not hiding this file.
- If sync status shows an error at the top of the app, double check steps 1–2 above.
