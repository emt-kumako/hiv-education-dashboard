# CLAUDE.md

Guidance for Claude Code when working in this repository.

## Project overview

A single-file, static HTML dashboard that presents pre-test/post-test results
for a 2026 HIV/STD prevention training course aimed at first-line firefighters
and EMS personnel in Taiwan (「115年度第一線消防救護人員愛滋及性傳染病防治教育訓練」).
All UI copy and content is in Traditional Chinese (zh-Hant).

It renders as a full-screen slide deck (`#s0`–`#s9`) covering: cover page,
overview, overall scores, six category breakdowns, per-person results, wrong-answer
rankings, attitude-change charts, and a full score table with CSV export.

## Tech stack

- Plain HTML/CSS/JS in a single file — **no build step, no bundler, no package.json**.
- Charting: [Chart.js](https://www.chartjs.org/) (loaded via CDN, `chart.umd.min.js`).
- CSV parsing: [PapaParse](https://www.papaparse.com/) (loaded via CDN).
- Fonts: Google Fonts (`Cinzel`, `Noto Sans TC`).

## File structure

- `index.html` — the entire application (markup, styles, and script all inline).
- `README.md` — just the repo name, no build/run instructions.

There is no `src/`, no test suite, and no CI configuration in this repo.

## Running / previewing

There's nothing to install or build. Open `index.html` directly in a browser,
or serve the directory locally, e.g.:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000/index.html
```

## Data flow

Scores are pulled from two published Google Sheets (pre-test and post-test),
with fallbacks:

1. **Live data** — fetched as CSV from published Google Sheets URLs
   (`PUB_PRE_CSV` / `PUB_POST_CSV`, with a `gviz/tq?tqx=out:csv` fallback via
   `PRE_SHEET_ID` / `POST_SHEET_ID` if the direct pub URL fails, e.g. due to CORS).
2. **Manual CSV upload** — file inputs (`#preFile`, `#postFile`) let a user
   upload the two CSVs directly if live fetch fails.
3. **Demo data** — a `demoBtn` fallback so the dashboard is viewable without
   any real data source.

The current mode (live / demo / uploaded file) is shown via a status badge
(`setMode()`).

## Scoring logic

- `KEY` is the answer key for the 38 scored questions, order-sensitive and
  tied to a specific answer-sheet revision noted in the code comment
  (衛福部 2026/2/1 修訂版題庫). If the question bank or column layout changes,
  `KEY`, `CATEGORIES`, `Q_CAT`, and the column index ranges in `parseRow()` /
  `buildModel()` must be updated together.
- Answers are matched against `KEY` per-question; scores are aggregated into
  six categories (`CATEGORIES`) and used to compute pre/post gain per person.
- Attitude questions (8 items, `ATTITUDE_LABELS`) are tracked separately and
  are not scored.
- Google Sheet column indices are hardcoded (e.g. `row[2]` = name, columns
  D–AJ / AK–AR / AS–AW for different question groups) — these depend on the
  exact form structure and will break silently if the form layout changes.

## Conventions

- Keep all user-facing strings in Traditional Chinese, consistent with the
  existing tone (formal but warm, addressed to 第一線消防救護人員).
- This is a single-file project by design — avoid splitting into multiple
  files/build tooling unless explicitly asked to restructure the project.
- An anonymization feature (`ANON` / `anonMap`) assigns joke aliases (JoJo's
  Bizarre Adventure character puns, `JOJO_ANON_NAMES`) to participant names
  when anonymized display is toggled on. Preserve this behavior if editing
  that code path.
- No linter or formatter is configured; match the existing (unminified but
  compact) code style when editing.

## Git workflow

- Prior history was created via GitHub's web "Add files via upload" flow,
  not local commits — there's no established local commit message convention
  to follow beyond normal clear, descriptive messages.
