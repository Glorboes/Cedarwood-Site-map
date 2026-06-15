# Cedarwood Development — Site Map App

Interactive cabinetry-installation tracker for the Cedarwood Development.

- **Client:** MAVS Property Developers
- **Contractor:** My Cabinet Guy

The app is a **single HTML file** (`index.html`) that reads live data from a
published Google Sheet CSV. No build step, no framework, no login. Works on a
phone on site.

```
cedarwood-sitemap/
├─ index.html            ← the whole app (open this in a browser)
├─ cedarwood_units.csv   ← starter data for all 48 units (import into Sheets)
└─ README.md             ← this file
```

---

## Step 1 — Build the Google Sheet

The app expects a sheet whose **first row is exactly these column headers** (lower-case, underscores):

| unit_number | block | floor | unit_type | status | cabinet_types | snagging | missing_parts | notes |
|-------------|-------|-------|-----------|--------|---------------|----------|---------------|-------|

Fastest way to get there:

1. Go to <https://sheets.google.com> and create a new blank spreadsheet. Name it e.g. **Cedarwood Units**.
2. **File ▸ Import ▸ Upload** and select `cedarwood_units.csv` from this folder.
3. On the import dialog choose **Replace spreadsheet** (or *Replace current sheet*), separator **Comma**, then **Import data**.

You now have all 48 units pre-filled with status `Not Started`. Just edit the
cells as work progresses — the app picks up changes automatically.

### Column reference

| Column | What goes in it |
|---|---|
| `unit_number` | 1–48. **Must match** — this is how the app links a row to a tile. |
| `block` | `A` `B` `C` `D` `E` `F` `Middle` `Duplex` |
| `floor` | `1`–`4` for the blocks; leave **blank** for Middle Flats / Duplexes |
| `unit_type` | `Top Flat` `Main Flat` `Middle Flat` `Duplex` |
| `status` | one of the 7 values below (spelling/case is forgiving but keep it close) |
| `cabinet_types` | free text, e.g. `Kitchen, Bathroom Vanity, BIC` (commas are fine) |
| `snagging` | free text — what needs fixing |
| `missing_parts` | free text — what's outstanding |
| `notes` | anything else |

> The layout (which tile is which block/floor) is **fixed inside the app**, so
> you can't break the map by editing `block`/`floor` in the sheet — those
> columns are just there for reference. Only `unit_number` matters for linking.

### Status values → tile colour

| status | colour |
|---|---|
| `Not Started` | grey |
| `In Progress` | orange |
| `Carcasses Done` | blue |
| `Doors & Drawers Done` | purple |
| `Snagging` | red |
| `Complete` | green |
| `On Hold` | yellow |

Anything the app doesn't recognise falls back to **Not Started (grey)**.

### Tips for free-text fields

- Put several cabinet types in one cell separated by commas — the app shows
  them as separate pills: `Kitchen, Bathroom Vanity, BIC`.
- For multiple snagging items, separate with `;` or new lines (Alt/⌥+Enter
  inside a cell) so they read clearly in the detail panel.

---

## Step 2 — Publish the sheet as CSV

1. In the sheet: **File ▸ Share ▸ Publish to web**.
2. Under **Link**, choose the specific tab (e.g. *Sheet1*) and format **Comma-separated values (.csv)**.
3. Click **Publish**, confirm, and **copy the URL**. It looks like:

   ```
   https://docs.google.com/spreadsheets/d/e/2PACX-xxxxxxxx/pub?gid=0&single=true&output=csv
   ```

> "Publish to web" exposes only a read-only CSV of this sheet — it does **not**
> make your whole Google account or other files public. To stop sharing later,
> open the same dialog and click **Stop publishing**.

---

## Step 3 — Connect the app to the sheet

Pick **one** of these (any works):

- **Easiest (no editing):** open the app, tap the **⚙ gear** button, paste the
  CSV URL. It's saved on that device.
- **Permanent for everyone:** open `index.html`, find `CSV_URL: ""` near the
  bottom (in the `CONFIG` block) and paste the URL between the quotes:
  ```js
  const CONFIG = {
    CSV_URL: "https://docs.google.com/.../pub?...&output=csv",
    REFRESH_MINUTES: 0   // set e.g. 5 to auto-refresh every 5 min
  };
  ```
- **One-off / sharing a link:** add `?csv=<the-url>` to the page address.

Tap **↻** any time to pull the latest from the sheet. The badge in the header
shows **● Live** (with the last refresh time), **Placeholder data**, or
**⚠ Offline** if it can't reach the sheet.

---

## Step 4 — Host on GitHub Pages

1. Create a GitHub repo (e.g. `cedarwood-sitemap`).
2. Upload `index.html` to the **root** of the repo (the `.csv` and this README
   are optional to upload — the app doesn't need them once `CSV_URL` is set).
3. Repo **Settings ▸ Pages**:
   - **Source:** *Deploy from a branch*
   - **Branch:** `main`, folder `/ (root)`, then **Save**.
4. After a minute the site is live at:
   ```
   https://<your-username>.github.io/cedarwood-sitemap/
   ```
5. Open it on your phone and add it to the home screen for app-like access.

To update a status: edit the Google Sheet — the published CSV updates within a
minute or two, and the app shows it on the next refresh. **You never have to
re-deploy** to change unit data; only re-deploy if you change `index.html`
itself.

---

## Using it on site

- **Tap a tile** → slide-up panel with type, block/floor, status, cabinet
  types, snagging, missing parts and notes.
- **Filter chips** (top) → tap e.g. *Snagging* to highlight only those units;
  tap again to clear.
- **Status colour key** → tap to expand the legend.
- Top Flats (floor 4) have a small **▲ roof marker** and a lighter top edge.
- The block map scrolls sideways on a narrow phone; Middle Flats and Duplexes
  sit in their own sections below.
