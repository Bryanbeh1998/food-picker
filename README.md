# 🍽️ Food Picker

A personal web app that helps you decide where to eat. Filter your restaurant list and get a random suggestion — solo or with a group.

🔗 **Live app:** [bryanbeh1998.github.io/food-picker](https://bryanbeh1998.github.io/food-picker/)

---

## Features

- **Random pick** — get a single suggestion based on your filters
- **Group mode** — generate 3–5 options at once for group decisions
- **Filters** — narrow down by Type, Cuisine, Price Range, and Location
- **All Matches list** — see every restaurant that fits your filters, tap any to reveal notes
- **Add a Place** — submit a new restaurant directly from the app, writes instantly to Google Sheets
- **Live data** — powered by Google Sheets, no redeployment needed to update the list

---

## How the data works

The restaurant list is stored in a **Google Sheet** and loaded live every time the app opens. To update your list:

1. Open your Google Sheet
2. Add, edit, or remove rows
3. Refresh the app — changes appear immediately

### Sheet columns

| Column | Required | Description |
|---|---|---|
| Name | ✅ | Restaurant name |
| Type | | `Restaurant`, `Hawker`, `Eatery`, or `Café` |
| Cuisine | | e.g. `Chinese`, `Japanese`, `Western` |
| Price Range | | `$`, `$$`, `$$$`, or `$$$$` |
| Location | | Single or multiple locations separated by commas |
| Notes | | Any personal notes — shown when you tap the restaurant |

---

## How to update and redeploy

If you make changes to the app code itself (not the data), redeploy with:

```bash
cd ~/Desktop/food-picker
git add index.html
git commit -m "your message"
git push
```

The live site updates within ~30 seconds.

---

## Tech stack

- Vanilla HTML / CSS / JavaScript — no frameworks
- Google Sheets as the database (published as CSV)
- Google Apps Script as the write API
- Hosted on GitHub Pages (free, permanent)

---

## Local development

Just open `index.html` in your browser. No build step or server needed.
