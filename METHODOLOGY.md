# US Commercial Building & Trade Economy Index: Data Provenance, Linkage & Validation

## 1. Primary Data Sources & Traceable Provenance
Every record in `trade-economy-index-data` is linked from four public federal/municipal sources:
1. **Municipal Building & Trade Permits ($N = 315,393$ permits)**: Pulled from municipal open-data portals (Socrata / ArcGIS REST APIs) across 30 major US metropolitan areas (2023–2026), retaining permit valuation, permit type (electrical, plumbing, HVAC/mechanical, roofing, commercial TI), issue date, and parcel/address coordinates.
2. **US Bureau of Labor Statistics (BLS) Occupational Employment and Wage Statistics (OEWS)**: May 2024–2025 MSA-level employment counts and hourly/annual wage percentiles ($P_{10}, P_{25}, P_{50}, P_{75}, P_{90}$) across SOC `47-2000` Construction and Extraction occupations (Electricians `47-2111`, Plumbers/Pipefitters `47-2152`, HVAC Mechanics `47-9021`, Roofers `47-2181`).
3. **US Census Bureau County Business Patterns (CBP)**: Establishment counts and payroll by 6-digit NAICS (`238210` Electrical, `238220` Plumbing/HVAC, `238160` Roofing, `236220` Commercial Building).
4. **Commercial Building Archetypes ($N = 52$)**: RSMeans / DOE Commercial Reference Building square-footage and system-replacement schedules cross-walked to municipal permit valuations.

## 2. Entity Normalization & Geographic Linkage
- Municipal permit records are geocoded and joined to **OMB Core Based Statistical Areas (CBSA / MSA FIPS codes)**.
- Free-text municipal permit descriptions are classified into standardized **6-digit NAICS** and **6-digit SOC** trade categories using deterministic keyword + valuation-band rules.
- Supply-Demand Tightness Scores ($0–100$) are computed as the z-score composite of per-establishment permit volume growth vs. BLS OEWS licensed trade headcount per 1,000 commercial structures.

## 3. Validation Against External Benchmarks
- MSA permit counts and valuations are cross-checked against US Census Bureau Building Permits Survey (BPS) metropolitan totals.
- Trade contractor revenue-per-building estimates are validated against Census Economic Census (NAICS Sector 23) receipts per establishment.

## 4. Known Limitations
- Municipal open-data portals vary in how self-reported permit valuations are audited at permit issuance vs. final inspection.
- Rural non-CBSA counties outside the 30 tracked metropolitan areas are excluded from the MSA tightness index.
