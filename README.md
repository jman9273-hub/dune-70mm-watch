# dune-70mm-watch

Pings my phone the moment AMC Lincoln Square releases new **IMAX 70mm** showtimes for **Dune: Part Three** (in theaters December 18, 2026).

## Why this exists

The first batch of IMAX 70mm tickets — one 7pm show per night, opening weekend Dec 17–20 — went on sale in April 2026 and sold out within minutes. The next batches will drop with no announced schedule. This watcher checks AMC's site every 20 minutes and sends a push notification (via ntfy) within about half an hour of new showtimes appearing, with a link straight to seat selection.

## How it works

Every 20 minutes, a GitHub Actions run:

1. Loads the AMC Lincoln Square 13 showtimes page and works out how to request specific dates.
2. Scans every date from **Dec 10, 2026 through Jan 19, 2027** (set by `SCAN_START` and `DAYS_AHEAD` in `.github/workflows/monitor.yml`).
3. Keeps only showtimes where the movie title contains "Dune" **and** the format section is IMAX 70MM — Dolby, Laser, and standard screenings are ignored.
4. Compares against `state.json` (the memory file in this repo, updated automatically by the bot commits).
5. Anything never seen before → phone notification labeled **"Dune 3 IMAX 70mm - Lincoln Sq"**, then it's recorded so it never re-alerts.

Empty December pages are expected for months and do not stop the scan. The first-ever run seeds memory silently and sends a one-time "watch is live" ping.

## Operating notes

- **Nothing to maintain.** Runs on GitHub's servers around the clock; no computer needs to stay on.
- **Force a check now:** Actions tab → dune-70mm-watch → Run workflow.
- **Extend the window** (if AMC posts dates past mid-January): edit `.github/workflows/monitor.yml` and raise `DAYS_AHEAD`.
- **Shut it down** (after tickets are secured): Actions tab → dune-70mm-watch → ⋯ menu → Disable workflow.
- **Notifications land via** the ntfy app, using the topic stored in the `NTFY_TOPIC` repository secret (Settings → Secrets and variables → Actions).

## Files

| File | What it is |
|---|---|
| `monitor.py` | The script that scans, diffs, and notifies |
| `requirements.txt` | The two Python libraries the script needs |
| `.github/workflows/monitor.yml` | The schedule + Dune-specific settings |
| `state.json` | Auto-generated memory of every showtime seen |

Sibling project: [odyssey-70mm-watch] — same script watching The Odyssey's IMAX 70mm run at the same theater.

