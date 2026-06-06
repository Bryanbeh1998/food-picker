# 🍽️ Food Picker

A personal web app that helps you decide where to eat. Filter your restaurant list and get a random suggestion — solo or with a group. Keep a separate bucket list of places you want to try, and move them over once you've been.

🔗 **Live app:** [bryanbeh1998.github.io/food-picker](https://bryanbeh1998.github.io/food-picker/)

---

## Features

- **Three tabs** — 🍽️ *Eat* (places you go to), ⭐ *Want to Try* (your bucket list), and 🍰 *Dessert* (sweet spots), each backed by its own Google Sheet tab with its own full picker
- **Multi-country** — group tabs by country; a 📍 location pill switches the whole app between countries, with an optional City sub-filter detected from the sheet
- **Random pick** — get a single suggestion based on your filters
- **Group mode** — generate 3–5 options at once for group decisions
- **Smart filters** — narrow down by Type, Cuisine, Price Range, and Location. Filters **auto-hide** when a tab has no data for them (e.g. Dessert hides Cuisine & Price), so you only see what's relevant
- **All Matches list** — every place that fits your filters, grouped by cuisine with a count per category; tap any to expand its notes, visit counter, and map link
- **Smart suggestions (visit counter)** — mark how many times you've been somewhere; the more you've visited, the less often it's suggested (but never fully excluded)
- **Mark as Tried** — on the wishlist, one tap moves a place into your main list automatically
- **Open in Google Maps** — every pick and outlet links straight to directions
- **Multi-location support** — chains with several branches show each outlet in an expandable sublist
- **Add / Edit / Delete** — manage places directly from the app; changes write straight back to Google Sheets. The **Type field suggests existing types but lets you type a new one**, with a duplicate-name guard
- **🏬 Mall Directory** — a separate reference of every food outlet in a Singapore mall. Pick a mall to see its list, or **search across all malls at once** (e.g. "sukiya" lights up every mall that has it)
- **Installable (PWA)** — Add to Home Screen for a fullscreen, app-like experience that loads instantly and works offline
- **Remembers your place** — a refresh keeps your country, tab, and open mall directory (the country gate only shows on first visit)
- **Dark mode** — toggle in the top-right corner; your choice is remembered
- **Pastel green theme** with a built-in, compact "How to Use" guide
- **Live data** — powered by Google Sheets, no redeployment needed to update the lists

---

## How the data works

The lists live in **one Google Sheet**, with tabs grouped by country and named by a fixed convention: **`<Country> Eat`**, **`<Country> Wishlist`**, **`<Country> Dessert`**. Each tab is published to the web as CSV and loaded live whenever the app opens.

| Tab (per country) | Purpose |
|---|---|
| **`<Country> Eat`** | Places you've been to / go to regularly |
| **`<Country> Wishlist`** | Places you want to try |
| **`<Country> Dessert`** | Dessert / sweet spots |

The app **auto-discovers countries** by reading the spreadsheet's published tab list — so you never edit code or copy gids. A country shows up as soon as all three of its tabs exist and are published.

To add another country, create another set of three tabs (e.g. *Japan Eat*, *Japan Wishlist*, *Japan Dessert*) and register them in the `COUNTRIES` config in `index.html` — see [Adding a country](#adding-a-country) below. **City** is handled by an optional `City` column within each tab (not separate tabs), and the app detects the distinct cities automatically.

You can update them two ways:

**From the app**
- Use **➕ Add a Place** to add an entry to the active tab
- Tap **✏️ Edit** on any place to edit or delete it
- On the wishlist, **🍽️ Mark as Tried** moves a place to the main tab
- All of these write back via a Google Apps Script

**From the sheet directly**
1. Open the relevant tab in your Google Sheet
2. Add, edit, or remove rows
3. Refresh the app — changes appear (the published CSV can take a few minutes to refresh)

### Sheet columns (same for every tab, in this order)

| Column | Required | Description |
|---|---|---|
| Name | ✅ | Restaurant name |
| Type | | `Restaurant`, `Hawker`, `Eatery`, or `Café` |
| Cuisine | | e.g. `Chinese`, `Japanese`, `Western` |
| Price Range | | `$`, `$$`, `$$$`, or `$$$$` |
| Location | | Single or multiple areas separated by commas |
| Notes | | Any personal notes — shown when you tap the restaurant |
| City | | Optional. e.g. `Tokyo`. Detected automatically to power the City sub-filter |

> The visit counts are stored in your **browser** (localStorage), not the sheet — they're personal to your device.

---

## Adding a country (no code, no copy-paste)

Thanks to auto-discovery, adding a country is purely a spreadsheet task:

1. **Create three tabs** named exactly:
   - `<Country> Eat` (e.g. `Thailand Eat`)
   - `<Country> Wishlist`
   - `<Country> Dessert`
   
   …with the usual column headers.
2. **Publish each tab as CSV**: File → Share → Publish to web → select the tab → CSV → Publish. *(Publish each tab individually — "Entire Document" doesn't reliably expose CSV.)*
3. *(Optional)* Fill the **`City`** column (e.g. `Bangkok`, `Phuket`) to enable the city sub-filter for that country.
4. **Refresh the app.** The country appears in the gate automatically.

That's it — **you don't paste URLs or gids anywhere**, and there's no code change. The app reads the published tab list, groups tabs by name, and shows any country that has all three tabs.

Notes:
- A country only appears once **all three** of its tabs exist and are published.
- The tab names must match the convention exactly (`<Country> Eat` / `Wishlist` / `Dessert`).
- Filters auto-hide when a country has no data for them (e.g. a country with no Cuisine values won't show the Cuisine filter).
- No per-row Country column is needed — the country **is** the tab group.

---

## Mall Directory

A read-only reference of every food outlet in a Singapore mall — separate from your personal lists. Tap **🏬 Mall Directory**, then either pick a mall to browse its outlets, or use the **search-all-malls** box to find a chain/dish across every mall (results are grouped by mall and tappable).

The data lives in **`malls.json`**:
```json
{
  "VivoCity": {
    "source": "https://…",        // where the list was compiled from
    "updated": "2026-06-03",        // shown in the app as "N outlets · updated <date>"
    "outlets": [ { "name": "Shake Shack", "cuisine": "American", "note": "Burgers" } ]
  }
}
```

Malls currently included: **Nex, VivoCity, Junction 8, Jewel Changi, Hougang Mall** (~378 outlets).

Mall tenants change often, so there is **no auto-update** — updates happen on request. Because each mall stores its `source` URL, refreshing it is reproducible. See [`MALLS.md`](MALLS.md) for the exact add/update procedure.

---

## Tech stack

- Vanilla HTML / CSS / JavaScript — no frameworks, single `index.html` file
- Google Sheets as the database — one spreadsheet, tabs grouped by country, each published as CSV for reads
- Google Apps Script as the write API (handles add, edit, delete, and move-between-tabs)
- `malls.json` as a static reference dataset for the Mall Directory
- A **service worker** (`sw.js`) + `manifest.json` make it an installable PWA
- Hosted on GitHub Pages (free, permanent)

## Architecture at a glance

```
        reads (CSV, 3 per country)        writes (POST)
Google Sheet ──────────►   App   ◄────────────────────  Google Apps Script
 ├─ Singapore: Eat / Wishlist / Dessert                    (write API)
 ├─ Japan: Eat / Wishlist / Dessert  (browser)
 └─ …                        │
                     GitHub Pages (static host)
```

- **Reads:** the app fetches both tabs' published CSVs on load and parses them in the browser
- **Writes:** Add / Edit / Delete / Mark-as-Tried send a POST to the Apps Script, which updates the right tab (`sheet` and `action` fields in the payload control where and how)
- All filtering, picking, grouping, and the visit weighting happen entirely in the browser — there is no backend server

---

## How to update and redeploy the app

If you change the app code itself (not the data), redeploy with:

```bash
cd ~/Desktop/food-picker
git add index.html
git commit -m "your message"
git push
```

The live site updates within ~30 seconds.

> **When you change `index.html` or `malls.json`, bump `CACHE_VERSION` in `sw.js`** (e.g. `food-picker-v7` → `v8`) so installed apps refetch. On a phone, fully close and reopen the app to pick up the new version.

> If you ever recreate the Apps Script deployment, you get a **new URL** — update `APPS_SCRIPT_URL` in `index.html`. To avoid this, redeploy via **Manage deployments → Edit → New version**, which keeps the same URL. The deployment must be set to **Execute as: Me** and **Who has access: Anyone**.

---

## Local development

Just open `index.html` in your browser. No build step or server needed.
