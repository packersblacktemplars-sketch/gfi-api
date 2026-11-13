# Data defects disclosed with this release

Published with the v1 API release on 2026-11-10. Revision published 2026-11-13.

**Revision 2026-11-13, source attribution.** CPI, debt/GDP and GDP growth were
previously shipped with `source_status: unresolved` and no named source. They are now
re-sourced from a named vintage with a per-series URL: IMF World Economic Outlook,
April 2025 vintage, via DBnomics, except euro-area HICP, which comes from Eurostat
tec00118. 111 of 114 values changed. The previous unattributed reading is retained on
each series as `latest.superseded_value`. Largest corrections: Argentina CPI 219.88 to
35.91, Japan debt 204.4 to 233.73, Kuwait debt 22.3 to 10.77. No series now ships
without a named source; `assert_professional_data.py` refuses any build that would.

Measured against the live dataset `globalfinancialinfo/global-policy-rate-map` (39 countries,
last modified 2026-11-09T15:07Z). None of these are API bugs; each is a defect in the map
pipeline that the API now refuses to hide.

1. **One source column for five families.** The CSV stamps `source = BIS WS_CBPOL` on every
   row. BIS WS_CBPOL carries policy rates. It does not carry consumer price inflation, general
   government debt or real GDP growth. 115 of 193 series (39 CPI, 38 debt, 38 growth) are
   therefore attributed to a source that does not publish them. Fix: per-family source columns
   when the map is rebuilt; until then the API serves `source: null, source_status: "unresolved"`.

2. **The United States real policy rate runs on 2024 inflation.** `USA.POLICY.REAL` = 0.93%,
   computed as a 3.88% rate dated 2026-11-03 less a 2.95% CPI figure for **2024**, 28 months
   apart. Argentina is the same construction (29.00% rate dated 2025-07-10 less 219.88% CPI for
   2024, giving -190.88%). Two of 38 real-rate series are affected, and they happen to be the
   most-read country and the most extreme value on the map. A third real rate,
   `MAR.POLICY.REAL` = 1.55%, is flagged `review_required` for a different reason: its policy
   rate is itself marked stale as of 2026-08-31 (item 5), so the derived figure inherits the
   flag. Three series therefore enter this release flagged, which is the count `index.json`
   publishes. Fix: refresh the CPI inputs to the latest annual or monthly print per country;
   the API flags `stale_inputs` and `review_required` until then.

3. **Annual periods rendered as December 31 dates.** `debt_gdp_year` and `gdp_growth_year` are
   2026 for all 38 countries carrying them, which the map pipeline turns into `2026-12-31`, a
   date five weeks in the future. Same for `inflation_year` 2025 becoming `2025-12-31`. Fix:
   carry `period` and `is_estimate`, never a synthesised date.

4. **Philippines has a rate date and no rate.** `policy_rate` is null with
   `policy_rate_date = 2026-11-01`. A cell that renders that row prints an empty value with a
   date beside it. Fix: either fill the rate or drop the date.

5. **Morocco and Argentina are flagged `rate_status = stale` in the store** while still
   carrying a rate date in 2026-08 and 2025-07. Worth a look at the staleness rule: two months
   old is not obviously stale for a central bank that meets quarterly.
