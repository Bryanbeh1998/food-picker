# 🍽️ Food Picker

A personal web app that helps you decide where to eat. Filter your restaurant list and get a random suggestion — solo or with a group. Keep a separate bucket list of places you want to try, and move them over once you've been.

🔗 **Live app:** [bryanbeh1998.github.io/food-picker](https://bryanbeh1998.github.io/food-picker/)

---

## Features

- **Three tabs** — 🍽️ *Eat* (places you go to), ⭐ *Want to Try* (your bucket list), and 🍰 *Dessert* (sweet spots), each backed by its own Google Sheet tab with its own full picker
- **Random pick** — get a single suggestion based on your filters
- **Group mode** — generate 3–5 options at once for group decisions
- **Filters** — narrow down by Type, Cuisine, Price Range, and Location
- **All Matches list** — every restaurant that fits your filters, grouped by cuisine with a count per category; tap any to expand its notes, visit counter, and map link
- **Smart suggestions (visit counter)** — mark how many times you've been somewhere; the more you've visited, the less often it's suggested (but never fully excluded)
- **Mark as Tried** — on the wishlist, one tap moves a place into your main list automatically
- **Open in Google Maps** — every pick and outlet links straight to directions
- **Multi-location support** — chains with several branches show each outlet in an expandable sublist
- **Add / Edit / Delete** — manage places directly from the app; changes write straight back to Google Sheets
- **Dark mode** — toggle in the top-right corner; your choice is remembered
- **Pastel green theme** with a built-in "How to Use" manual
- **Live data** — powered by Google Sheets, no redeployment needed to update the lists

---

## How the data works

The restaurant lists live in a **Google Sheet** with three tabs, each published to the web as CSV and loaded live whenever the app opens:

| Tab | Purpose |
|---|---|
| **Main tab** (e.g. *Food Places*) | Places you've been to / go to regularly |
| **Wishlist** | Places you want to try |
| **Dessert** | Dessert / sweet spots |

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

### Sheet columns (same for both tabs)

| Column | Required | Description |
|---|---|---|
| Name | ✅ | Restaurant name |
| Type | | `Restaurant`, `Hawker`, `Eatery`, or `Café` |
| Cuisine | | e.g. `Chinese`, `Japanese`, `Western` |
| Price Range | | `$`, `$$`, `$$$`, or `$$$$` |
| Location | | Single or multiple locations separated by commas |
| Notes | | Any personal notes — shown when you tap the restaurant |

> The visit counts are stored in your **browser** (localStorage), not the sheet — they're personal to your device.

---

## Tech stack

- Vanilla HTML / CSS / JavaScript — no frameworks, single `index.html` file
- Google Sheets as the database — two tabs, each published as CSV for reads
- Google Apps Script as the write API (handles add, edit, delete, and move-between-tabs)
- Hosted on GitHub Pages (free, permanent)

## Architecture at a glance

```
        reads (CSV ×3)                  writes (POST)
Google Sheet ──────────►   App   ◄────────────────────  Google Apps Script
 ├─ Main tab             (browser)                          (write API)
 ├─ Wishlist tab            │
 └─ Dessert tab     GitHub Pages (static host)
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

> If you ever recreate the Apps Script deployment, you get a **new URL** — update `APPS_SCRIPT_URL` in `index.html`. To avoid this, redeploy via **Manage deployments → Edit → New version**, which keeps the same URL. The deployment must be set to **Execute as: Me** and **Who has access: Anyone**.

---

## Local development

Just open `index.html` in your browser. No build step or server needed.
