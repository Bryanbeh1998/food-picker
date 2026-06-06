# Mall Directory — data & update workflow

The Mall Directory feature reads `malls.json`. Each mall is an object:

```json
{
  "VivoCity": {
    "source": "https://…",          // where the outlet list was compiled from
    "updated": "2026-06-03",          // YYYY-MM-DD, shown in the app
    "outlets": [
      { "name": "Shake Shack", "cuisine": "American", "note": "Burgers" }
    ]
  }
}
```

The app shows `"<N> outlets · updated <date>"` so freshness is visible.

## How updates work (on demand)

Mall tenants change often. There is **no auto-update** — updates happen when the user asks.

**User prompt:** e.g. *"update VivoCity"* or *"update all malls"*.

**Assistant procedure:**
1. Read `malls.json`, get the mall's `source` URL.
2. Re-fetch that URL (and cross-check a second source if useful) for the current outlet list.
3. Replace the mall's `outlets`, set `updated` to today's date.
4. Bump `CACHE_VERSION` in `sw.js` (so installed apps refetch).
5. `git add malls.json sw.js && git commit && git push`.

## Adding a new mall

User says e.g. *"add Jewel Changi"*. Assistant:
1. Find a reliable food-directory page for that mall.
2. Add a new top-level key with `source`, `updated` (today), and `outlets`.
3. Bump `CACHE_VERSION`, commit, push.

The new mall appears in the directory dropdown automatically (sorted A–Z).

## Notes
- Directories from third-party guides can be slightly stale; prefer the mall's official dining page as `source` when it lists tenants in plain HTML.
- Keep `note` short (a few words). `cuisine` powers the search and the badge.
