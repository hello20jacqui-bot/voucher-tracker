# LG-Voucher-Tracker — Firebase Edition

A web app for tracking gift/store voucher barcodes, PINs, and remaining balances — Coles, Myer, or anywhere else — shared between two phones with **real-time sync via Firebase Realtime Database**.

No app store. No server to run. It's a static page hosted on GitHub Pages, with data synced instantly across both phones using Firebase's free tier (1 GB storage, 10 GB/month, plenty for this use case).

## Why Firebase?

- **Real-time**: changes appear on both phones within 1–2 seconds, no refresh needed
- **Simple setup**: just 5 minutes, no collaborator invites or token management
- **Free tier is enough**: 1 GB storage, 100 concurrent connections
- **Offline-first**: the app works without signal (syncs when reconnected)

## Setup — 5 minutes

### Step 1: Create a Firebase project

1. Go to **console.firebase.google.com**
2. Click **Create a new project**
3. Enter a name (e.g. "LG-Vouchers")
4. **Disable Analytics** (not needed for this app)
5. Click **Create project** → wait for it to provision

### Step 2: Add a web app

1. In the Firebase console, click the **</> (web)** icon
2. Enter an app name (e.g. "LG-Voucher-Tracker")
3. Click **Register app**
4. You'll see a config block with these 4 values — **copy all of them**:
   - `apiKey`
   - `authDomain`
   - `databaseURL`
   - `projectId`

### Step 3: Create and configure the Realtime Database

1. Left sidebar → **Build → Realtime Database**
2. Click **Create Database**
3. Choose a location closest to you
4. Click **Start in test mode** (temporary, we'll fix it next)
5. Once it's created, click the **Rules** tab
6. Delete the existing rules and paste this:

```json
{
  "rules": {
    "rooms": {
      "$roomId": {
        ".read": "auth != null",
        ".write": "auth != null"
      }
    }
  }
}
```

7. Click **Publish**

This ensures your data stays secure and doesn't expire after 30 days (test mode expires).

### Step 4: Enable Anonymous Authentication

1. Left sidebar → **Build → Authentication**
2. Click **Get started**
3. Click **Anonymous** → toggle it **on**
4. Click **Save**

### Step 5: Connect both phones

1. Open the app on the first phone at `https://<your-username>.github.io/voucher-tracker`
2. Tap **Settings** (gear icon, top right)
3. Enter the 4 Firebase config values you copied in Step 2
4. Enter a **room code** (any word, e.g. `our-vouchers`)
5. Tap **Connect**
6. Repeat on the second phone using the **same** room code

That's it — you're now synced.

## Using the app

The app has three modes, toggled at the top:

### Use vouchers (default)
For checkout. Shows vouchers grouped by store with remaining balance. Tap one to see its barcode (full-screen for scanning), PIN, and balance. Record what you spent — balance updates instantly across both phones.

### Add vouchers
Set up or manage your collection. Add a new voucher with store, value, PIN, purchase date, and a barcode photo. Edit or delete existing vouchers from this mode (delete is locked behind `#admin` URL suffix for safety).

### Summary
Spending dashboard. Two stat tiles show total for the selected period and all-time total. A bar chart toggles between weekly (last 8 weeks) and monthly (last 6 months) — tap a bar to see exact amount and date range. Below that, a by-store breakdown shows which stores got how much.

## Managing vouchers

- **Add**: Switch to Add mode → **+ Add a new voucher** → fill in details → take a barcode photo (clear, well-lit) → save
- **At checkout**: open the voucher in Use mode → tap the barcode photo → goes full-screen on white background with balance and PIN button underneath, ready to scan
- **Record spending**: go back into the voucher after checkout → record the date and amount spent → balance updates automatically
- **Expired vouchers**: once balance hits zero, the voucher is marked EXPIRED automatically. You can delete it from Add mode, or delete all at once with a "clear N expired" button
- **Sync**: automatic, real-time. Both phones stay in sync — no manual refresh needed

## Delete protection (`#admin`)

Delete actions (removing individual vouchers, clearing expired ones, deleting transactions) are only visible when you open the app with `#admin` at the end of the URL:

```
https://<your-username>.github.io/voucher-tracker/#admin
```

This prevents accidental deletions in normal use. You can bookmark this URL on your PC if you need admin access regularly.

## Offline

The app works without internet signal. It syncs when you reconnect — Firebase handles the queueing automatically.

## Barcode photos

Photos are compressed before saving (resized + JPEG quality 72%) so they don't bloat your Firebase storage.

## Free tier limits

- **1 GB total storage** — each voucher with a barcode photo is roughly 50–100 KB, so you can store hundreds
- **10 GB/month download** — syncing 100 vouchers once per day uses ~3 MB/month
- **100 concurrent connections** — two phones = 2 connections, plenty of headroom

## Troubleshooting

**"Not connected to Firebase"**
- Check your 4 config values are correct (copy-paste from Firebase console)
- Check your `authDomain` includes `.firebaseapp.com` and your `databaseURL` is the full `https://...` URL
- Make sure Anonymous auth is enabled in Firebase console
- Wait a moment and try again

**Changes not syncing**
- Check the sync dot under the title — should be green/live, not red/offline
- Open Chrome DevTools console (F12) and look for error messages
- Refresh the page once if offline, then reconnect

**Barcode photo won't save**
- Make sure it's a valid image file (JPG, PNG, GIF, WebP)
- Try a smaller, simpler image
- If the photo is huge, the compression step might fail — try a different photo

**Lost data or need to start fresh**
- Go to Settings → **Disconnect from Firebase**
- Go to Firebase console → **Build → Realtime Database** → click the 3-dot menu → delete the database
- Reconnect in the app — it will create a fresh database with just what's on that phone

## On your second phone

Once the first phone is set up, the second phone just needs the same 4 config values and the same room code. It will instantly sync with whatever is already in the database.

## Moving back to GitHub storage (if you ever want to)

If you decide Firebase isn't for you, the old GitHub-based version is in this repo's commit history. You can revert to it, but Firebase is simpler and more reliable for real-time sync.

---

**Questions?** The Firebase console is pretty straightforward — if you get stuck on any step, their documentation is helpful. The app itself shows hints in the Settings screen too.
