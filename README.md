# Performance Tracker — Dairy Queen

Weekly performance and quarterly bonus tracker for Dairy Queen store managers (general, operations and assistant managers). Internal tool — not an official Dairy Queen product. Bonus figures are estimates; the owner's payout sheet is final.

## Screens
- **All stores** — bonus on track across stores, biggest opportunities, store scorecard (8 bonus criteria), latest week per store.
- **Store detail** — projected GM/AM bonus, where to focus, criteria status, Thursday entry, week-by-week history (tap any week to view or edit it), monthly roll-up.
- **Setup** — your name/role, stores, bonus goals, sync sign-in, Backup / Restore / CSV.

**Thursday entry:** sales, transactions, cakes, cupcakes, daily counts & waste, labor, drive-thru, fan score (store actual vs store DMA — must be 2+ above), deliveries, truck counts, missed receipt uploads and missed clock-outs ($50 off each), complaints & reviews. COGS (actual vs target) is entered once a month.

## Where the data is saved
- **Without sync:** only in the browser on that device.
- **With sync (below):** in each person's own account in Google Firebase (free). Sign in with the same email on phone and laptop and you see the same numbers. Each person can only read and write their own data. A copy also stays on the device so it works offline and syncs when back online.

---

## One-time setup: turn on sync (about 15 minutes, free)

You need a Google account. Firebase's free "Spark" plan needs no credit card.

1. Go to **console.firebase.google.com** → **Create a project** → name it `performance-tracker` → turn **off** Google Analytics → Create.
2. Left menu **Build → Authentication** → **Get started** → **Email/Password** → turn on the first switch → **Save**.
3. Still in Authentication → **Settings** tab → **Authorized domains** → **Add domain** → `abhigami25.github.io` (your GitHub Pages domain) → Add.
4. Left menu **Build → Firestore Database** → **Create database** → pick a US location → **Start in production mode** → Create.
5. In Firestore → **Rules** tab → replace everything with the rules below → **Publish**.
6. Click the **gear icon → Project settings** → scroll to **Your apps** → click the **`</>` (Web)** icon → nickname `tracker` → **Register app** (no hosting needed). Copy the `firebaseConfig = { ... }` block it shows.
7. In GitHub open `index.html` → pencil icon (Edit) → find the line
   `window.TRACKER_FIREBASE_CONFIG = null;`
   and replace `null` with the config, e.g.
   ```js
   window.TRACKER_FIREBASE_CONFIG = {
     apiKey: "AIza...",
     authDomain: "performance-tracker-xxxx.firebaseapp.com",
     projectId: "performance-tracker-xxxx",
     storageBucket: "performance-tracker-xxxx.appspot.com",
     messagingSenderId: "1234567890",
     appId: "1:1234567890:web:abc123"
   };
   ```
   → **Commit changes**. (These values are not secret — Firebase web apps always ship them. Security comes from the rules and sign-in.)
8. Wait ~1 minute, open the site → **Setup → Sync across devices** → enter email + password → **Create account**. On your other device use **Sign in** with the same email. Data already on the first device is uploaded automatically.

### Firestore rules (step 5)
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{uid}/{col}/{docId} {
      allow read, delete: if request.auth != null && request.auth.uid == uid;
      allow create, update: if request.auth != null && request.auth.uid == uid
                            && col in ['meta', 'weeks']
                            && request.resource.data.size() <= 40;
    }
  }
}
```

### Install on phone
iPhone: Safari → Share → **Add to Home Screen**. Android: Chrome → ⋮ → **Add to Home screen**. Sign in once inside the installed app.
