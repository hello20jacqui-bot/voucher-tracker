# Voucher Tracker — setup guide

A small web app for tracking gift/store voucher barcodes, PINs, and remaining
balances — Coles, Myer, or anywhere else — shared between two phones. No app
store, no server to run — it's a static page on GitHub Pages, with your voucher
data stored in a separate **private** GitHub repo that the app reads and writes
to directly.

## Why two repos

GitHub Pages on a free account only publishes **public** repos, and the published
page itself is reachable by anyone with the link regardless of plan — GitHub
doesn't gate the *site* to specific people (only Enterprise plans can do that).
So this app splits things in two:

- **`voucher-tracker`** (public) — just the app's code (this HTML file, icons).
  Nothing sensitive lives here. This is what GitHub Pages hosts.
- **`voucher-data`** (private) — holds one file, `vouchers.json`, with your
  actual voucher barcodes, PINs and transaction history. It's never public.
  The app reads/writes it using a personal access token that only that repo
  will accept, entered once per phone and stored only in that phone's browser.

You chose no in-app password screen, which is fine — the app *shell* being
public doesn't expose your data, because without a valid token to the private
data repo, the app has nothing to show. Just don't post the Pages link
publicly (e.g. in a public tweet); treat it like any semi-private link.

## 1. Create the private data repo

1. On GitHub, **New repository** → name it `voucher-data` (or anything you like)
   → set visibility to **Private** → Create.
2. Leave it empty — the app creates `vouchers.json` inside it automatically
   the first time you save a voucher.
3. Go to **Settings → Collaborators** on this repo → **Add people** → invite
   your friend by their GitHub username. They'll need to accept the invite
   (check their email or GitHub notifications).

## 2. Create the public app repo and turn on Pages

1. **New repository** → name it `voucher-tracker` → set visibility to
   **Public** → Create.
2. Upload every file in this folder (`index.html`, `manifest.json`, the
   `icons/` folder) to that repo — easiest via the GitHub web UI's
   **Add file → Upload files**, or `git push` if you're comfortable with git.
3. Go to **Settings → Pages** → under "Build and deployment", set **Source**
   to *Deploy from a branch*, branch `main`, folder `/ (root)` → **Save**.
4. GitHub will show your live URL after a minute or two, something like:
   `https://<your-username>.github.io/voucher-tracker/`

## 3. Create a personal access token (one per phone/person)

Each of you needs your own token, scoped to **only** the private data repo:

1. GitHub → click your profile photo → **Settings** → **Developer settings**
   (bottom of the left sidebar) → **Personal access tokens** →
   **Fine-grained tokens** → **Generate new token**.
2. Give it a name like "Voucher Tracker – [your name]'s phone".
3. **Resource owner**: yourself. **Repository access**: *Only select
   repositories* → choose `voucher-data`.
4. Under **Permissions → Repository permissions**, set **Contents** to
   **Read and write**. Leave everything else as "No access".
5. **Generate token** → copy it immediately (GitHub only shows it once).

Your friend does the same thing under their own GitHub account, once they've
accepted the collaborator invite from step 1.

## 4. Open the app and connect

1. On each iPhone, open the Pages URL from step 2 in Safari.
2. You'll land on the **Connect to GitHub** screen. Fill in:
   - **GitHub owner**: your GitHub username (the owner of `voucher-data`)
   - **Data repo name**: `voucher-data`
   - **Access token**: paste the token from step 3
3. Tap **Connect**. The app confirms it can read the repo, then shows your
   (empty) voucher list.
4. Repeat on the second phone with *their own* token, pointed at the same
   owner/repo.

## 5. Add it to the home screen (so it feels like a real app)

In Safari: tap the **Share** button → **Add to Home Screen** → **Add**.
It'll launch full-screen, no browser bar, with the app icon.

## Using it

- **Add a voucher**: tap the **+** button, choose or type the store (Coles,
  Myer, etc. — start typing for suggestions), then enter the value, PIN,
  purchase date, and a photo of the barcode (camera or photo library). The
  dashboard groups vouchers under their store, each with its own
  active-count and remaining-balance subtotal.
- **At checkout**: open the voucher, tap the barcode photo — it opens
  full-screen on a white background with the balance and a PIN reveal
  button underneath, ready to scan.
- **After paying**: go back into the voucher and record the date and amount
  spent. The balance and status (NEW / IN-USE / EXPIRED) update
  automatically — EXPIRED is set automatically once the balance hits zero.
- **Both phones**: whoever records a transaction saves it straight to the
  shared private repo. Pull down isn't wired up — tap the refresh icon
  (top right) if you want to double-check you're seeing the other person's
  latest update; the app also auto-refreshes whenever you reopen it.

## Things worth knowing

- **No offline mode.** Every open/save talks to GitHub, so you'll need
  signal or Wi-Fi. The last-loaded data is cached on-device so the screen
  isn't blank if a request briefly fails, but new saves need a connection.
- **Conflicts.** If you both edit within the same few seconds, whichever
  save reaches GitHub last wins — fine for two people who aren't editing
  at the exact same moment, which is the expected case here.
- **Losing a phone.** Revoke that phone's token from GitHub → Settings →
  Developer settings → Personal access tokens at any time; the other
  phone keeps working.
- **The barcode photo** is compressed before saving (resized + JPEG) so it
  doesn't bloat the data file or slow syncing down on mobile data.
