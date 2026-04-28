# Foundever EMEA — UK Campaign Dashboard

A static dashboard tracking UK LinkedIn horizontal performance, Influ2 verticalised cohorts (T&H, BFS), and (when ready) SEA. Deployed via GitHub Pages — updates are a single small change to `data.json`.

## What's in this folder

| File | What it is | When to touch it |
|---|---|---|
| `index.html` | Layout, styling, rendering logic. Reads `data.json` on load. | Only if you want to change the design or add new sections. |
| `data.json` | All the numbers and copy. | **Every weekly update.** This is the only file you need to edit. |
| `README.md` | This file. | When you change the workflow. |

## One-time setup — push to GitHub Pages

1. Create a new GitHub repo (private or public — Pages works on both with a free account).
2. From your machine, in this `uk-dashboard/` folder, run:
   ```bash
   git init
   git add .
   git commit -m "Initial UK campaign dashboard"
   git branch -M main
   git remote add origin git@github.com:YOUR_USERNAME/foundever-uk-dashboard.git
   git push -u origin main
   ```
3. On GitHub: **Settings → Pages → Build and deployment → Source: Deploy from a branch → main / root → Save**.
4. Wait ~1 minute. Your dashboard will be live at:
   `https://YOUR_USERNAME.github.io/foundever-uk-dashboard/`

If you'd rather not use the command line, GitHub's web UI lets you drag-and-drop the three files straight into a new repo via **Add file → Upload files**.

## The weekly update workflow

1. I (or you) re-pull the numbers from LinkedIn Campaign Manager + Influ2 (and SEA when wired up).
2. Edit `data.json` — change the metric values, top contacts, top companies, `last_updated`, and `read_of_the_week`. The structure is documented inline.
3. Commit the change:
   ```bash
   git add data.json
   git commit -m "Weekly update — YYYY-MM-DD"
   git push
   ```
4. GitHub Pages rebuilds automatically. The dashboard is live with the new numbers within ~1 minute.

You can wire this into the existing `emea-campaign-weekly-report` scheduled task — once the numbers are pulled into Notion, the same data can be written to `data.json` and pushed.

## Adding the SEA section

When the SEA data lands, replace the `sea` block in `data.json` with something like:

```json
"sea": {
  "date_range": "30 Mar — 28 Apr 2026",
  "note": "UK paid search across BFS + T&H verticalised pages",
  "metrics": {
    "Spend": "£X,XXX",
    "Impressions": "XX,XXX",
    "Clicks": "X,XXX",
    "CTR": "X.X%",
    "Conversions": "XX",
    "CPL": "£XXX"
  }
}
```

The SEA section will appear in the dashboard automatically — no `index.html` changes needed.

## Adding more verticals or geos

The current dashboard is UK-only. If you want to extend it:
- For another geo (e.g. France), copy this folder to `france-dashboard/` and swap the data.
- For more Influ2 cohorts in the same dashboard, add a third `influ2_xx` block in `data.json` and add a third `<div class="card" id="xx_card"></div>` plus a `renderInflu2("xx_card", data.influ2_xx)` call in `index.html`.

## Local preview before pushing

```bash
cd uk-dashboard
python3 -m http.server 8000
```

Then open `http://localhost:8000` in your browser. Useful for sanity-checking edits before you push.
