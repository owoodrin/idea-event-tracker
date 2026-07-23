# IDEA Event Registration Tracker

Internal Board page, auto-updated hourly. Static site — no backend.

## One-time setup
1. Push this whole folder (including `.github/`) to the root of the
   `idea-event-tracker` repo; enable GitHub Pages (main branch, `/` root).
2. Set the registration Google Sheet's sharing to
   **"Anyone with the link: Viewer"** (required — the GitHub Action fetches
   the sheet's CSV export without Google credentials).
3. Repo → Settings → Actions → General → Workflow permissions →
   **"Read and write permissions"** (lets the Action commit updated data).
4. Optional: Actions tab → "Update registration data" → **Run workflow**
   to test immediately.

## How updating works
`.github/workflows/update-data.yml` runs hourly (at :17). It executes
`scripts/update_data.py`, which fetches each sheet in `events.config.json`,
extracts ONLY the "Registration Time" column, and rewrites `data.js`.
If nothing changed, no commit is made. If a fetch fails, the cached copy
in `data/` is used, so the page never goes blank.

## Adding a new event
Add an entry to `events.config.json`:

    {
      "id": "unique-slug",
      "name": "Event Name",
      "eventDate": "YYYY-MM-DD",
      "note": "optional line under the title",
      "csv_url": "https://docs.google.com/spreadsheets/d/SHEET_ID/export?format=csv&gid=0",
      "timestamp_column": "Registration Time"
    }

Commit — the next hourly run picks it up (or trigger manually).

## Notes
- GitHub cron is best-effort; runs can be delayed 5–30 min at busy times.
- GitHub disables scheduled workflows after ~60 days with no repo activity;
  the Action's own commits count as activity while registrations keep coming,
  but if it stops, re-enable it in the Actions tab.
- Only timestamps are published; names/emails never enter the repo.
  Note that "Anyone with the link: Viewer" makes the FULL sheet readable by
  anyone who obtains the sheet URL — keep that URL internal.
