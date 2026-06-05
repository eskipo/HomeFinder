# Daily listing search — routine playbook

This is the self-contained task for the scheduled **Routine** that refreshes the
château finder dataset in `index.html`. The routine runs autonomously once a day.

## Goal
Find genuinely new château / maison de maître / chartreuse listings in the
rail-reachable corridors and add them to the dataset as **unverified** entries,
committed straight to the working branch. Never overwrite or edit verified data.

## Steps each run
1. **Work on the default branch.** `claude/chateau-train-riviera-VYitI` is the repo's
   default branch and holds the app, so the routine clones it directly — no checkout
   or dated branch needed. Pull latest before editing.
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
9. **If new listings were added**: commit straight to `claude/chateau-train-riviera-VYitI`
   with message `Daily listing search — <date>` whose body lists each addition (commune,
   price, region, why it passed), then push the branch.
   **If nothing new**: do not commit; end the run noting "no new listings today".

## Sources (Instagram-derived, but mined compliantly)
We do **not** scrape Instagram — it blocks automated access (403/login wall) and it
violates their ToS. The popular property accounts are only a curation layer; mine the
brokers they repost instead. Run the corridor queries above against these portals:

- Cabinet Le Nail · Patrice Besse · Leggett Prestige · Sifex · French-Property.com
- Belles Demeures · Green-Acres · Ma-Propriété · BellesPierres · Le Figaro Propriétés
- Curation accounts to watch for *which brokers are hot* (browse manually, don't scrape):
  @dreamfrenchproperties and similar. When the owner pastes an Instagram post's caption
  or its outbound listing link into a session, add that specific property directly.

If you ever want true Instagram data, the only compliant route is the official
**Instagram Graph API** hashtag search — which needs a Business/Creator account plus app
review and returns only limited recent media. It is out of scope for this routine.

## Guardrails
- Portals (ParuVendu, French-Property, etc.) return 403 to automated fetch — rely on
  WebSearch result snippets only; never fabricate a price or URL.
- New rows stay `verified:false` until a human confirms them in the UI; the daily
  commit (and its diff) is the audit trail. Review the run's session to see what landed.
- Don't touch the calculator, the prose tabs, or the verified rows (ids 1–11).
