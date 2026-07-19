# LG-Voucher-Tracker — setup guide

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

The app has three modes, switched with the toggle at the top:

- **Use vouchers** (default, opens first every time) — for checkout. Shows
  your vouchers grouped by store. Tap one to see its barcode, PIN, and
  balance, and to record what you just spent. This mode can't add, edit, or
  rename vouchers — it's deliberately kept to just viewing and spending, so
  there's nothing to fumble while you're at the register.
- **Add vouchers** — for setting things up or cleaning up. Add a new
  voucher (choose or type the store, then value, PIN, purchase date, and a
  barcode photo), or edit/delete any existing voucher from the list below.
- **Summary** — a spending dashboard across every voucher combined. Two
  stat tiles up top show the total for the selected period and your
  all-time total. Below that, a bar chart toggles between **Weekly** (last
  8 weeks) and **Monthly** (last 6 months) — tap any bar to reveal its
  exact amount and date range. A "by store" breakdown ranks how much went
  to each store within that same period. It updates automatically as you
  record transactions elsewhere in the app.

**Deleting expired vouchers**: once a voucher's balance hits zero it's
marked EXPIRED automatically. You can remove expired ones two ways —
tap "clear N expired" in the Use-mode summary line (or the equivalent
button in Add mode) to wipe all of them at once, or open a single expired
voucher in Use mode and tap "Delete this voucher" to remove just that one.
Non-expired vouchers can only be deleted from Add mode, as a safeguard
against removing one by accident while you're still using it.

- **Add a voucher**: switch to Add mode → "+ Add a new voucher" → choose
  or type the store (Coles, Myer, etc. — start typing for suggestions),
  then enter the value, PIN, purchase date, and a photo of the barcode
  (camera or photo library). The dashboard groups vouchers under their
  store, each with its own active-count and remaining-balance subtotal.
- **At checkout**: open the voucher, tap the barcode photo — it opens
  full-screen on a white background with the balance and a PIN reveal
  button underneath, ready to scan.
- **After paying**: go back into the voucher and record the date and amount
  spent. The balance and status (NEW / IN-USE / EXPIRED) update
  automatically — EXPIRED is set automatically once the balance hits zero.
- **Both phones**: whoever records a transaction saves it straight to the
  shared private repo. Pull down isn't wired up — tap the refresh icon
  (top right) if you want to double-check you're seeing the other person's
  latest update; the app also auto-refreshes whenever you reopen it. The
  small sync line under the header shows both a relative time ("Synced 2m
  ago") and the exact date/time of the last successful sync.

## Troubleshooting

**"Can't find that repository — check the owner and repo name in Settings"**
This means GitHub couldn't find the repo *or* the token can't see it (GitHub
returns the same error for both, to avoid revealing private repos exist).
Check, in order:

1. **Owner** in Settings is the exact GitHub username that owns the private
   data repo — not a display name, and not necessarily *your* username if
   your friend owns it.
2. **Data repo name** is spelled exactly right, with no `.git` suffix and
   no owner prefix (just `voucher-data`, not `username/voucher-data`).
3. The repo actually exists — open `github.com/<owner>/<repo>` in a browser
   and confirm it loads.
4. **If this is the second phone**, the invited collaborator must actually
   **accept the invite** (check email or github.com notifications) — until
   accepted, their token gets exactly this error, as if the repo doesn't
   exist.
5. The **personal access token** was created with *Repository access: Only
   select repositories* → this exact repo selected, and **Contents: Read
   and write** permission ticked. A token scoped to the wrong repo, the
   wrong account, or with no repo selected will also produce this error.
6. The token hasn't expired (fine-grained tokens can have short default
   expirations — check under Developer settings → Personal access tokens).

The app now checks repo access as its own step when you hit **Connect**, so
this error should appear right away if something's off, rather than only
showing up later when you try to save a voucher.

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
