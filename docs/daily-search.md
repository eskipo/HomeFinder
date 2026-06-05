# Daily listing search — routine playbook

This is the self-contained task for the scheduled **Routine** that refreshes the
château finder dataset in `index.html`. The routine runs autonomously once a day.

## Goal
Find genuinely new château / maison de maître / chartreuse listings in the
rail-reachable corridors, add them to the dataset as **unverified** entries, and
open a PR for human review. Never silently overwrite verified data.

## Steps each run
1. **Check out the working branch** (it holds the app; the default branch may not):
   ```
   git fetch origin claude/chateau-train-riviera-VYitI
   git checkout claude/chateau-train-riviera-VYitI
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
9. **If new listings were added**: commit, push the dated branch, and open a PR
   targeting `claude/chateau-train-riviera-VYitI` titled `Daily listing search — <date>`,
   with a body listing each addition (commune, price, region, why it passed).
   **If nothing new**: do not commit; end the run noting "no new listings today".

## Guardrails
- Portals (ParuVendu, French-Property, etc.) return 403 to automated fetch — rely on
  WebSearch result snippets only; never fabricate a price or URL.
- Keep `verified:false` until a human confirms. The PR is the review gate.
- Don't touch the calculator, the prose tabs, or the verified rows.
