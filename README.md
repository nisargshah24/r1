# Intern Year — your live schedule site

A single-page site for your UCSF Internal Medicine intern year that renders **your own live QGenda schedule**: year timeline, block-by-block cards with condensed rotation guides, who's on with you, and a day-level live grid.

**[▶ See a live demo](https://maxweiss10.github.io/intern-year-schedule-template/?demo)** (sample data).

Everything on the page is computed in your browser from three inputs:

| Data | Source | Freshness |
|---|---|---|
| Your rotations, shifts, vacations | **Your QGenda iCal feed**, synced daily by a GitHub Action (or on demand) | Live |
| "Interns/residents on with you / before you" | A program-wide QGenda export cached in `data/coresidents.json` | **Static — printed 7/8/2026.** Swaps after that date won't show |
| Rotation guides on each card | The site "Ultimate Guides" (Parnassus / SFGH / VA), condensed in `data/rotation-guides.json` | Static — last year's structures |

## Set up your own copy (~10 minutes)

### 1. Get your QGenda iCal link

In QGenda (web or app): your profile → **Calendar Sync / Subscribe** → copy the personal **iCal URL**. It looks like `https://app.qgenda.com/ical/...?key=...`.

> That URL contains a private key that exposes your schedule — treat it like a password. In this setup it only ever lives in a GitHub secret.

### 2. Create your repo from this template

Click **Use this template → Create a new repository** (top-right on GitHub). Name it anything (e.g. `intern-year`). Keep it **public** (required for free GitHub Pages).

### 3. Add the feed as a secret

In your new repo: **Settings → Secrets and variables → Actions → New repository secret**

- Name: `QGENDA_ICS_URL`
- Value: the iCal URL from step 1

### 4. Run the first sync

**Actions** tab → enable workflows if prompted → open **Sync QGenda feed** → **Run workflow**. After ~30s it commits `schedule-live.json`. It then re-syncs once a day automatically — re-run the workflow anytime for an instant update, or edit the cron in `.github/workflows/qgenda-sync.yml` if you want it more often.

### 5. Turn on GitHub Pages

**Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)` → Save.**

Your site goes live at `https://<your-username>.github.io/<repo-name>/` a minute later.

### 6. (Optional) Put your name on it

Edit `config.json`:

```json
{
  "name": "Jordan Holthe",
  "year": "2026–27",
  "qgendaName": "Holthe, J",
  "program": "UCSF Internal Medicine"
}
```

- `name` — shown in the header (defaults to the name detected from your feed).
- `qgendaName` — your name as QGenda prints it ("Last, F"); used to exclude you from your own co-resident lists. Usually auto-detected; set it only if you see yourself listed on your own cards.

## No-GitHub quick preview

Don't want to set anything up? Open any hosted copy of this site, and use **Import a .ics file** — export your QGenda calendar as `.ics` and drop it in. It's parsed in your browser and stored in localStorage only (nothing is uploaded), but it's a snapshot: re-import to refresh.

## Privacy notes, please read

- **Your day-level schedule becomes public** on GitHub Pages (the repo and `schedule-live.json` are public). If you're not OK with that, don't publish — use the .ics import instead.
- Your **QGenda URL/key stays secret** (repo secret, never committed).
- `data/coresidents.json` contains rotation-level assignments (name + rotation + dates, nothing else) for the program, from the 7/8/2026 export. Door codes, shared logins, phone numbers, and pagers were deliberately excluded from all cached data.

## Updating the cached data

- **Rotation guides** (`data/rotation-guides.json`): edit freely — each rotation code maps to `{sub, src, html}`.
- **Co-residents** (`data/coresidents.json`): regenerate from a fresh "Individual Provider Schedule" QGenda PDF export when someone shares one; the parser script lives in the original repo.
