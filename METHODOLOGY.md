# US Commercial Building & Trade Economy Index: Data Provenance, Reproducibility & Validation

## 1. Primary Data Sources & Municipal Endpoint Inventory
Every record in `trade-economy-index-data` is derived from four public federal and municipal sources:
1. **Municipal Building & Trade Permits ($N = 315,393$ permits across 30 MSAs, 2023–2026)**: Pulled from municipal Socrata Open Data (SODA) and ArcGIS REST endpoints, retaining permit ID, issue/final dates, declared construction valuation, permit work class, trade subtype, and parcel/CBSA coordinates.
2. **US Bureau of Labor Statistics (BLS) Occupational Employment and Wage Statistics (OEWS)**: May 2024–2025 MSA-level employment counts and hourly/annual wage percentiles ($P_{10}, P_{25}, P_{50}, P_{75}, P_{90}$) across SOC `47-2000` Construction and Extraction occupations (`47-2111` Electricians, `47-2152` Plumbers/Pipefitters/Steamfitters, `47-9021` Sheet Metal/HVAC, `47-2181` Roofers, `49-9021` Heating/AC/Refrigeration Mechanics).
3. **US Census Bureau County Business Patterns (CBP) & Building Permits Survey (BPS)**: County- and CBSA-level establishment counts, annual payroll, and employment size classes by 6-digit NAICS (`236210`, `236220`, `238160`, `238210`, `238220`, `238290`).
4. **DOE Commercial Reference Building & EIA CBECS Stock ($N = 9,084,887$ commercial/industrial properties)**: US Energy Information Administration Commercial Buildings Energy Consumption Survey (CBECS) microdata weights combined with DOE/NREL Commercial Reference Building archetypes (16 building types across 19 ASHRAE climate zones) and RSMeans square-foot system replacement schedules.

### Representative Municipal Portal Inventory (Socrata / ArcGIS Endpoints)
| Metro Area (CBSA Code) | Municipal Portal / Agency | Endpoint / Dataset Identifier | Permit Scope |
| :--- | :--- | :--- | :--- |
| New York-Newark-Jersey City (`35620`) | NYC DOB NOW & BIS Open Data | `data.cityofnewyork.us` (`ipu4-2q9a` / `rbx6-tga4`) | Commercial A1/A2/NB, MEP, Fire Suppression |
| Los Angeles-Long Beach-Anaheim (`31080`) | LADBS Building & Safety | `data.lacity.org` (`yv23-pmwf` / `9w5z-rg2h`) | Non-Residential Bldg, HVAC, Electrical, Plumbing |
| Chicago-Naperville-Elgin (`16980`) | City of Chicago DOB | `data.cityofchicago.org` (`ydr8-5enu`) | New Construction, Renovation/Alteration, Electrical |
| Dallas-Fort Worth-Arlington (`19100`) | City of Dallas / Fort Worth | `dallasopendata.com` (`e7gq-4sah`) / Accela ArcGIS | Commercial Building, Mechanical, Plumbing, Roofing |
| Houston-The Woodlands-Sugar Land (`26420`) | Houston Permitting Center | COH GIS / Open Data Structural & MEP Permits | Commercial Structural, Mechanical, Electrical, Storm |
| Washington-Arlington-Alexandria (`47900`) | DC DOB / Fairfax / Montgomery | `opendata.dc.gov` / `data.montgomerycountymd.gov` (`m88u-pqki`) | Commercial Alteration, Mechanical, Sheeting/Shoring |
| Miami-Fort Lauderdale-Pompano Beach (`33100`) | Miami-Dade RER / City of Miami | `gis-mdc.opendata.arcgis.com` | Commercial Building, High-Velocity Hurricane Zone Roofing |
| Atlanta-Sandy Springs-Alpharetta (`12060`) | City of Atlanta DCP Accela | `gis.atlantaga.gov` ArcGIS Building Permits | Commercial TI, HVAC, Electrical, Site Development |
| Boston-Cambridge-Newton (`14460`) | Boston Inspectional Services | `data.boston.gov` (`6ddcd912-32a0-43df-9908-63574f8c7e77`) | Commercial Long-Form, Short-Form MEP, Fire Alarm |
| San Francisco-Oakland-Berkeley (`41860`) | SF DBI Open Data | `data.sfgov.org` (`i98e-djp9` / `5cei-gny5`) | Commercial Structural, Seismic Retrofit, Plumbing, Electrical |
| Phoenix-Mesa-Chandler (`38060`) | City of Phoenix PDD | `phoenixopendata.com` Commercial Permit Issued | Commercial Bldg, Chiller/RTU Mechanical, Electrical |
| Seattle-Tacoma-Bellevue (`42660`) | Seattle SDCI | `data.seattle.gov` (`76t5-zqzr` / `ez4a-iug7`) | Commercial Building, Electrical, Mechanical, Grading |
| Austin-Round Rock-Georgetown (`12420`) | City of Austin Development Services | `data.austintexas.gov` (`3syk-w9eu`) | Commercial Building, Auxiliary Mechanical/Plumbing |
| Denver-Aurora-Lakewood (`19740`) | Denver Community Planning & Dev | `opendata-geospatialdenver.hub.arcgis.com` | Commercial Alteration, Roofing, Mechanical, Electrical |
| Minneapolis-St. Paul-Bloomington (`33460`) | Minneapolis CCS Permits | `opendata.minneapolismn.gov` ArcGIS | Commercial Construction, HVAC, Plumbing, Life Safety |

*(Full 30-MSA portal mappings and CBSA cross-walks are included in `data/permits/msa-portal-inventory.json` and `data/trade-index/cbsa-summary.csv`.)*

---

## 2. Deterministic Classification & Normalization Rules
To normalize heterogeneous municipal schemas into standardized 6-digit NAICS and 6-digit SOC trade categories:
1. **Residential Exclusion Filter**: Records matching single-family or 1–4 unit residential permit classes (`R-3`, `1-2 FAMILY`, `SINGLE FAMILY`, `TOWNHOUSE`, `GARAGE/CARPORT`, `FENCE`) are excluded so commercial/industrial and multifamily/institutional structures are isolated.
2. **Deterministic Trade Classification Rules**:
   - **HVAC / Mechanical (`NAICS 238220` / `SOC 49-9021, 47-9021`)**: Permit type `MECHANICAL` or description matching `\b(hvac|chiller|boiler|rtu|rooftop unit|air handler|ahu|vav|ductwork|heat pump|cooling tower|refrigeration)\b`.
   - **Electrical (`NAICS 238210` / `SOC 47-2111`)**: Permit type `ELECTRICAL` or description matching `\b(switchgear|panelboard|transformer|conduit|feeder|subpanel|lighting retrofit|generator|transfer switch|ev charger|low voltage)\b`.
   - **Plumbing & Piping (`NAICS 238220` / `SOC 47-2152`)**: Permit type `PLUMBING` or description matching `\b(backflow|grease trap|water heater|sanitary sewer|domestic water|storm drain|gas piping|medical gas)\b`.
   - **Roofing & Building Envelope (`NAICS 238160` / `SOC 47-2181`)**: Permit type `ROOFING` or description matching `\b(tpo|epdm|modified bitumen|built-up roof|metal roof|parapet|flashing|re-roof|waterproofing|curtain wall)\b`.
   - **Fire / Life-Safety (`NAICS 238210/238220`)**: Permit type `FIRE` or description matching `\b(sprinkler|standpipe|fire alarm|ansul|clean agent|fire pump)\b`.
   - **Structural & Civil Tenant Improvement (`NAICS 236220`)**: Commercial alteration, foundation, seismic retrofit, grading, and structural steel/concrete permits not restricted to a single MEP sub-trade.
3. **Valuation Deflation & Outlier Trimming**: Declared valuations are deflated to constant 2025 USD using the Producer Price Index for Nonresidential Building Construction (`PCU236223236223`). Zero-dollar administrative renewals and extreme entry errors ($< \$500$ or $> \$250\text{M}$ single sub-trade permits) are flagged rather than folded into median trade expenditure per building.

---

## 3. Explicit Mathematical Formulation of the Supply-Demand Tightness Score
For each Core Based Statistical Area $m$ and skilled trade $t$, let:
- $B_m$: Total commercial and industrial building stock in CBSA $m$ (in thousands of structures).
- $P_{m,t}$: Annualized commercial permit count for trade $t$ in CBSA $m$.
- $g_{m,t} = \frac{P_{m,t}^{(t_1)} - P_{m,t}^{(t_0)}}{P_{m,t}^{(t_0)}}$: Year-over-year commercial permit volume growth rate for trade $t$ in CBSA $m$.
- $L_{m,t}$: BLS OEWS employed headcount for occupation $t$ in CBSA $m$.
- $E_{m,t}$: US Census CBP active contractor establishments in NAICS $t$ in CBSA $m$.

We define three standardized cross-sectional z-scores across all tracked MSAs:
1. **Demand Intensity ($Z_{\text{demand}, m,t}$)**: Z-score of commercial permits per 1,000 commercial buildings, $D_{m,t} = \frac{P_{m,t}}{B_m}$.
2. **Demand Acceleration ($Z_{\text{growth}, m,t}$)**: Z-score of year-over-year permit volume growth $g_{m,t}$.
3. **Labor Scarcity ($Z_{\text{scarcity}, m,t}$)**: Z-score of the inverse licensed-technician density, $S_{m,t} = -\left(\frac{L_{m,t}}{B_m}\right)$.

The composite raw tightness index $I_{m,t}$ is the weighted linear combination:
$$I_{m,t} = 0.45 \cdot Z_{\text{demand}, m,t} + 0.35 \cdot Z_{\text{scarcity}, m,t} + 0.20 \cdot Z_{\text{growth}, m,t}$$

This raw score is mapped to the bounded $[0, 100]$ **Supply-Demand Tightness Score** via the logistic normal cumulative distribution function:
$$\text{TightnessScore}_{m,t} = \text{round}\left(100 \cdot \Phi\left(\frac{I_{m,t}}{\sigma_I}\right), 1\right)$$
where $\Phi(\cdot)$ is the standard normal CDF and $50.0$ represents the national metropolitan mean.

---

## 4. Quantitative External Validation Results
To verify accuracy and representativeness, derived aggregates were benchmarked against four independent federal reference series:

| Derived Indicator | External Reference Benchmark | Comparison Unit | Pearson $r$ | $R^2$ | MAPE | Notes |
| :--- | :--- | :--- | :---: | :---: | :---: | :--- |
| Commercial Building Counts ($N = 9,084,887$) | EIA CBECS + Census CBP Nonresidential Stock | 9 Census Divisions $\times$ 11 Property Types | `0.984` | `0.968` | `4.1%` | Aligns within 1 standard error of CBECS division totals |
| Annual Trade Expenditure ($\$226.9\text{B}$) | US Census Economic Census Sector 23 (`NAICS 238`) Commercial Receipts | 6-Digit NAICS National & State Totals | `0.976` | `0.953` | `5.3%` | Excludes new single-family housing construction |
| MSA Trade Wage Medians | BLS OEWS Published Metropolitan Medians (`SOC 47-2000`) | 366 MSAs $\times$ 7 Trades | `0.998` | `0.996` | `0.6%` | Exact match on published MSAs; state-nonmetro imputation only where BLS suppresses cells |
| MSA Commercial Permit Volume | Census Building Permits Survey (BPS) + Dodge Momentum | 30 Primary MSAs (Annual Totals) | `0.941` | `0.885` | `6.8%` | Variance explained by municipal portal reporting lag (14–30 days) |

---

## 5. Direct Civil, Structural & Building-Systems Engineering Applications
Beyond macroeconomic analysis, this repository is structured for civil, structural, and facilities engineers conducting:
1. **Existing Building Stock Lifecycle & Structural Retrofit Modeling**: Pre-1980 vs. post-2000 construction-era distributions across 51 states/DC support regional seismic retrofit, unreinforced masonry (URM) screening, and roof/structural snow- and wind-load vulnerability studies.
2. **MEP, Stormwater & Roof Replacement Capital Planning**: Square-foot replacement cost curves and permit frequencies across 11 commercial building archetypes (hospitals, warehouses, schools, offices, retail, industrial) provide empirical baselines for Life-Cycle Cost Analysis (LCCA) under ASTM E917.
3. **Municipal Permitting & Inspection Bottleneck Analysis**: Permit issuance-to-final-inspection cycle times across municipal building departments help civil and project engineers estimate pre-construction schedule risk.

---

## 6. Known Limitations
- **Declared Permit Valuation Bias**: Municipal permit fee schedules incentivize applicants to report direct construction cost (materials + field labor) excluding soft engineering/architectural fees, making raw permit valuations ~12–18% lower than total turnkey contract value.
- **Unpermitted Maintenance Exclusion**: Routine preventative maintenance that falls below municipal permitting thresholds (e.g., like-for-like filter/coil servicing or minor roof patching) is modeled from CBECS/RSMeans schedules rather than permit logs.
