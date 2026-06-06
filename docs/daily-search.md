# Daily listing search — routine playbook

This is the self-contained task for the scheduled **Routine** that refreshes the
château finder dataset in `index.html`. The routine runs autonomously once a day.

## Goal
Find genuinely new château / maison de maître / chartreuse listings in the
rail-reachable corridors and add them to the dataset as **unverified** entries,
committed straight to the working branch. Never overwrite or edit verified data.

## Steps each run
1. **Branch for the run.** The routine clones the repo (default branch `main`). Make a
   dated run branch off `main` — routines may push `claude/`-prefixed branches without
   extra permission:
   ```
   git checkout main && git pull
   git checkout -b claude/daily-search-$(date +%F)
   ```
2. **Read the current dataset.** Parse the `const LIST=[...]` array in `index.html`.
   Note the highest `id` and the existing `{commune, price}` pairs.
3. **Search** (use the WebSearch tool) across these corridors, newest listings first:
   - Drôme / Rhône (26, 07): Valence, Crest, Montélimar, Die
   - Languedoc / Med line (34, 11): Béziers, Narbonne, Lézignan, Carcassonne
   - Burgundy / Mâconnais (71): Mâcon, Tournus, Cluny, Chalon
   - Occitanie · Tarn (81) and Gers (32)
   Query terms: `maison de maître | château | chartreuse à vendre <area> <price> pierre caractère`.
   Also query the **curation-fed broker portals** (see "Sources" below) by name +
   corridor, e.g. `Leggett Prestige château <region> under 700000`, to catch the same
   listings the Instagram accounts repost.
4. **For each candidate that is NOT already in `LIST`** (match on commune + approx price),
   extract what the search result gives: `commune, dept, region, price, m2, bd,
   land_ha, year (era), typ/typLabel, feats[], station, carMin, hrs, changes, lat, lng, url`.
   - Geocode `lat,lng` to the commune centre if not given.
   - Estimate `hrs`/`changes` from the verified anchors already in `LIST` (same corridor).
5. **Flag honestly.** New entries get `verified:false`. Use `priceNote:"listed"` only
   when the search result states a real asking price **and** you have a real listing
   `url`; otherwise `priceNote:"est"` and `url:null` (the UI renders a search link).
6. **Cap & dedupe.** Add at most **8** new listings per run. Skip anything already present.
7. **Insert** the new objects into `LIST` with incrementing `id`s. Do not edit the
   verified entries (ids 1–11). Keep the field shape identical to existing rows.
8. **Validate**:
   ```
   awk '/<script>/{f=1;next} /<\/script>/{f=0} f' index.html > /tmp/full.js && node --check /tmp/full.js
   ```
9. **If new listings were added**: commit with message `Daily listing search — <date>`
   (body lists each addition: commune, price, region, why it passed). Push the
   `claude/daily-search-<date>` branch — Cloudflare builds a **preview** from it. Then open
   a **PR into `main`** titled `Daily listing search — <date>`. Merging the PR is what
   promotes the day's finds to **production** (Cloudflare deploys `main`), so unverified
   finds sit on the preview URL until a human reviews and merges.
   **If nothing new**: do not commit; end the run noting "no new listings today".

   > Branch/deploy model: `main` = default + production (never hand-edited). Humans work on
   > `dev`; the routine uses dated `claude/daily-search-*` branches. Both get a Cloudflare
   > preview; merging a PR into `main` releases to production. No "unrestricted branch
   > pushes" permission needed — the routine only pushes `claude/`-prefixed branches.

## Sources
The full, categorized brokerage & portal list lives in **`docs/sources.md`** — query a
rotating subset of those by name + corridor + price each run.

We do **not** scrape Instagram or the broker sites directly — they block automated
access (403/login wall) and scraping violates their ToS. The popular property accounts
are only a curation layer; mine the brokers they repost (in `docs/sources.md`) instead.
When the owner pastes an Instagram post's caption or its outbound listing link into a
session, add that specific property directly. The only compliant route to true Instagram
data is the official **Graph API** hashtag search (Business account + app review, limited
recent media) — out of scope for this routine.

## Guardrails
- Portals (ParuVendu, French-Property, etc.) return 403 to automated fetch — rely on
  WebSearch result snippets only; never fabricate a price or URL.
- New rows stay `verified:false` until a human confirms them in the UI; the daily
  commit (and its diff) is the audit trail. Review the run's session to see what landed.
- Don't touch the calculator, the prose tabs, or the verified rows (ids 1–11).
