# Brokerage & portal source list

The canonical set of French château / character-property sources the daily routine
mines (via WebSearch — we don't scrape these sites or Instagram directly; many return
403 to automated fetch). Grouped by tier. Verify URLs occasionally; agencies rebrand.

## 1. Specialist château / character-property agents (primary signal)
| Agency | URL | Notes |
| --- | --- | --- |
| Patrice Besse | patrice-besse.com | Castles, manors, mills, character buildings nationwide |
| Cabinet Le Nail | cabinetlenail.com | Châteaux & manoirs, strong in the west/centre |
| Sifex | sifex.co.uk | Châteaux, vineyard estates, manors since 1989 |
| Emile Garcin – Propriétés & Châteaux | emilegarcin.com | Prestige estates; Burgundy, Loire, Provence |
| Groupe Mercure | groupe-mercure.fr | Character property, regional network (Lyon, Auvergne…) |
| French Character Homes | frenchcharacterhomes.com | Character homes all price ranges, SW France |
| Agence Newton | agencenewton.com | Mills, manors, châteaux; English-French |
| Leggett Prestige | leggettprestige.com | Châteaux & manoirs, many under €1M |
| Belles Demeures | bellesdemeures.com | Maisons de maître & prestige aggregator |
| Le Figaro Propriétés | proprietes.lefigaro.com | Prestige listings aggregator |

## 2. Prestige international (occasional bargains, good photos)
| Agency | URL |
| --- | --- |
| Savills France | savills.com |
| Knight Frank | knightfrank.com |
| Barnes International | barnes-international.com |
| Christie's International Real Estate | christiesrealestate.com |
| Michaël Zingraf | michaelzingraf.com |

## 3. National portals & aggregators (volume)
| Portal | URL | Notes |
| --- | --- | --- |
| SeLoger | seloger.com | #2 traffic; agency-heavy |
| Bien'ici | bienici.com | Built by professionals |
| Leboncoin | leboncoin.fr | #1 traffic; private + agency |
| Green-Acres | green-acres.fr | Rural/countryside, multilingual, fast-growing |
| Ouest-France Immo | ouestfrance-immo.com | Strong in the west |
| ParuVendu | paruvendu.fr | The original 11 verified listings came from here |
| French-Property.com | french-property.com | English-language portal + agents |
| My French House | my-french-house.com | English-language château listings |
| Lux-Residence | lux-residence.com | Prestige aggregator |

## 4. Notaire portals (often-cheaper, less-marketed stock)
| Portal | URL |
| --- | --- |
| Immobilier des Notaires | immobilier.notaires.fr |
| Immonot | immonot.com |

## 5. Budget / renovation-focused
| Source | URL | Notes |
| --- | --- | --- |
| Beaux Villages Immobilier | beauxvillages.com | Affordable homes, award-winning |
| Clé France | clefrance.co.uk | 7000+ houses, cheap-to-luxury |
| Town & Country Property France | towncountrypropertyfrance.com | Cheap/bargain + château sections |
| French Estate Agents (Leggett) | frenchestateagents.com | Big bargain + renovation filters |
| Complete France | completefrance.com | Editorial round-ups of châteaux to renovate |

## 6. Regional corridor agencies (the fast-rail sweet spots)
| Area | Agency | URL |
| --- | --- | --- |
| Drôme / Vaucluse | Christine Miranda | christinemiranda.com |
| Drôme / south | BellesPierres | bellespierres.com |
| Drôme (Valence) | lesiteimmo | lesiteimmo.com |
| Aude/Hérault, Gers | Ma-Propriété | ma-propriete.fr |
| Nationwide mandataires | Propriétés-Privées | proprietes-privees.com |
| Tarn/Aude/Gers | Leggett (regional) | leggett-immo.com |

## Instagram curation accounts (watch, don't scrape)
Browse manually for *which brokers are hot*; paste a post's caption or its outbound
listing link into a session to add that property directly.
- @dreamfrenchproperties — 258k followers, reposts the agencies above
- (add accounts you follow here)

## How the routine uses this
For each run, query a rotating subset of these by name + corridor + price, e.g.
`Patrice Besse château Drôme under 700000`, `Beaux Villages maison de maître Aude`,
`immobilier.notaires.fr château Saône-et-Loire`. Capture new listings only; flag
`verified:false` until confirmed. See `docs/daily-search.md` for the full procedure.
