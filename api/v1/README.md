# GFI data API, v1 (free tier)

Base: `https://huggingface.co/datasets/globalfinancialinfo/global-policy-rate-map/resolve/main/api/v1/`

Free to cite, paid to compute with. No key, no sign-up, no rate limit: every series level
with its unit, as-of date, source status, coverage tier and a finished citation string.

Data as of 2026-11-05. Surface generated 2026-11-11T01:09:23+00:00.

## What is here

| File | Holds |
|---|---|
| `index.json` | Counts, data as of, tier, endpoint map |
| `catalog.json` | All 193 series: id, subject, value, unit, as-of, source status, tier, definition, citation |
| `series/<ID>.json` | One series in full: latest, prior, change, definition, source, citation |
| `country/<ISO3>.json` | Country packet for 39 countries: standing state, shifts, news, sources, citation |
| `changes.json` | Verified shift rows only. An empty set is declared as `not_collected`, never rendered as zero |
| `openapi.json` | The contract, for tooling and agents |

## Series id grammar

`GEO.FAMILY.MEASURE`, uppercase, dots, issued once and never reused. GEO is ISO3.

Families: `POLICY` (RATE, REAL), `PRICE` (CPI_YOY), `FISCAL` (DEBT_GDP), `MACRO` (GDP_GROWTH),
`DEMO` (POP, MEDIAN_AGE, AGE65_SHARE, DEPENDENCY, URBAN_SHARE, LABOR_FORCE_PART),
`RULE` (eight statutory limits, licensed).

Example: `https://huggingface.co/datasets/globalfinancialinfo/global-policy-rate-map/resolve/main/api/v1/series/USA.POLICY.RATE.json`

## Four rules the data follows

1. A source is named only when the source actually carries that figure. Where it does not,
   `source` is null and `source_status` is `unresolved`. 115
   of 193 series are in that state today, rather than borrowing a neighbour's name.
2. Annual figures are periods, not dates: `as_of` is null, `period` is the year,
   `is_estimate` is carried. No synthesised December 31.
3. A derived figure publishes its inputs and a comparability verdict. `POLICY.REAL` carries
   `inputs` and `comparability`; 3 series are flagged
   `review_required`, including the United States, whose real rate still rests on 2024 inflation.
4. Empty is declared. An uncollected block says `not_collected`; it never appears as a blank
   cell or a zero.

## How to cite

Every series and packet carries a finished string, for example:

> Global Financial Information, "United States policy rate" (USA.POLICY.RATE), data as of
> 2026-11-03, retrieved 2026-11-10. https://huggingface.co/datasets/globalfinancialinfo/global-policy-rate-map/resolve/main/api/v1/series/USA.POLICY.RATE.json

## Licence and what is paid

Free tier: CC BY 4.0, cite Global Financial Information. That covers every level, unit, as-of
date, source, definition and citation here.

Licensed: history deep enough to compute a change over years, revision records and changelog
ids, whole-family CSV and parquet export, the statutory rules layer, watch levels with
resolve-by dates, and the right to redistribute.
