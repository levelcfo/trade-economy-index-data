# The Trade Economy Index

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21762674.svg)](https://doi.org/10.5281/zenodo.21762674)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-teal.svg)](https://creativecommons.org/licenses/by/4.0/)

US skilled-trade economics for 13 commercial trades, with a citation attached to
every figure: labor, market structure, unit economics, cash cycle, AI exposure,
geography, and valuation.

Companion site: **[tradesindex.org](https://tradesindex.org)**.
Also on [Hugging Face](https://huggingface.co/datasets/LevelCFO/trade-economy-index).
Published by [Level](https://levelcfo.com).

## Why this exists

Most industry data about the trades is either a paywalled market report or a vendor
blog post with no sample size. This index publishes the figures with their
provenance attached, so any number can be audited without leaving the table.

Every numeric row carries its provenance beside the value:

| column | meaning |
|---|---|
| `source` | the citation recorded for the figure |
| `source_access` | `url`, `citation-text-only`, or `missing` |
| `research_pass_count` | repeated research review runs, not independent evidence sources |
| `confidence` | `high-primary` (a government or SEC filing), `high`, `adjudicated` (a model reconciled disagreeing sources), `med`, `low` (single source), or `null` where the figure is prose |
| `derived` | `true` when the index computed the figure by synthesizing cited inputs rather than reading it off one |
| `shared_across` | how many trade or segment records publish this exact value. Greater than 1 means it is an industry-wide benchmark, not a measurement that distinguishes this trade |

**`research_pass_count` describes the research process, not source independence.**
Several passes can cite the same publisher or evidence family. Use `source_access`
to distinguish a clickable citation from a text-only citation, then evaluate the
recorded source directly.

**How each figure is attributed.** Every figure in `research`, `subtrade`,
`revenue_bands`, `geo_states`, `geo_metros` and `comps` carries its own citation in the row, and a
gate refuses to publish one that does not. The remaining tables are attributed at the TABLE level
rather than per cell, because their figures are not third-party quotations: `trades` holds index
scores computed from the published methodology, `level_benchmarks` holds Level's own measured
percentile distributions, and `permits` and `building_stock` are aggregations of public permit and
county tax-assessor records. Those provenance statements are in this card and on
[the methodology page](https://tradesindex.org/methodology/), not in a per-row column.

## The data

Every table ships twice: `.csv` (GitHub renders it as a sortable table, so you can
read it in the browser) and `.json` (same rows, for code).

| table | rows | one row is |
|---|---|---|
| [`trades`](data/trades.csv) | 13 | a trade, with its AI-Resilience and AI-Leverage subscores |
| [`research`](data/research.csv) | 1,007 | one figure for one trade on one of ~42 research topics |
| [`subtrade`](data/subtrade.csv) | 342 | the same, split by residential / commercial / industrial |
| [`revenue_bands`](data/revenue_bands.csv) | 167 | a unit-economics metric by revenue band (under $1M to $20M+) |
| [`geo_states`](data/geo_states.csv) | 650 | a trade in a state: median wage, differential, licensing regime |
| [`geo_metros`](data/geo_metros.csv) | 650 | a trade in a metro: contractor density, job value, permit trend |
| [`permits`](data/permits.csv) | 15 | permit volume and job-value percentiles by trade and year |
| [`comps`](data/comps.csv) | 69 | a public company mapped to the trades it operates in |
| [`level_benchmarks`](data/level_benchmarks.csv) | 11 | an operating metric as a p10/p25/median/p75/p90 distribution |
| [`building_stock`](data/building_stock.csv) | 612 | commercial and industrial building age and size by state |

### Quick start

```python
import pandas as pd

RAW = "https://raw.githubusercontent.com/levelcfo/trade-economy-index-data/main/data"
trades = pd.read_csv(f"{RAW}/trades.csv")
research = pd.read_csv(f"{RAW}/research.csv")

# figures reviewed in more than one research pass
reviewed = research[research.research_pass_count > 1]

# what does the index say about labor for HVAC, and where did it come from?
print(research[(research.trade == "hvac") & (research.topic == "wages_detail")]
      [["field", "value_num", "source", "confidence"]])
```

```r
trades <- read.csv("https://raw.githubusercontent.com/levelcfo/trade-economy-index-data/main/data/trades.csv")
```

## Coverage

13 trades: HVAC and refrigeration, plumbing, electrical, roofing, glass and glazing,
doors and access, landscaping, commercial cleaning, painting, concrete and masonry,
fire and life safety, low-voltage and security, restoration.

`level_benchmarks` is Level's own operating data, aggregated and anonymized from
contractor financial reviews. It is a BLENDED multi-trade pool reported as percentile
distributions with per-metric sample sizes. **`n` counts COMPANIES**, not jobs or
invoices; where Level measures a per-job quantity the company count is not meaningful
and `n` is empty rather than a large record count. No individual company is
identified or identifiable, and there is no per-trade split of these figures.

## Limitations

Read these before citing.

- **The scores carry judgment.** AI-Resilience and AI-Leverage are weighted
  composites. The weights are documented on
  [the methodology page](https://tradesindex.org/methodology/) but they are a
  considered opinion, not a measurement. Read the tiers, not the decimals.
- **Not third-party.** Level sells financial operations services to contractors. We
  publish sources so the figures can be checked rather than asking anyone to take our
  word for it, but this is not an independent index.
- **Occupational mapping is imperfect.** Employment and wage figures map trades to
  federal SOC codes, and some trades share a broad code, so a few counts reflect a
  wider occupation than the trade name suggests.
- **Vintages differ across layers.** National wage figures in `research` and the
  state figures in `geo_states` come from different BLS releases, so a national wage
  back-solved from the state differentials will be close to, but not identical to,
  the national figure in `research`.
- **Evidence depth varies.** `research_pass_count` is workflow metadata and must not
  be interpreted as an independent-source count. Check `source` and `source_access`
  directly. Sample size `n` is separate.
- **Percentile pools skew to established firms.** The companies in Level's data chose
  to work with a CFO service, which is not a random sample of the trade.

## License

Level's own aggregates and the index scores are **CC BY 4.0** ([full text](LICENSE),
[what it covers](LICENSE-SCOPE.md)), free to quote, download, and reanalyze with
attribution to Level (levelcfo.com).

The `source` columns cite **third-party** publications (BLS, SEC filings, CFMA,
IBISWorld, trade associations, and others). Those cited figures belong to their
publishers and are **not ours to license**; the citation is provided so you can
consult the original. Attribution here covers this compilation, not the underlying
sources.

## Citation

This repository carries a [`CITATION.cff`](CITATION.cff), so GitHub's "Cite this
repository" button produces a formatted citation.

```bibtex
@misc{trade_economy_index_2026,
  title  = {The Trade Economy Index},
  author = {Level},
  year   = {2026},
  doi    = {10.5281/zenodo.21762674},
  url    = {https://doi.org/10.5281/zenodo.21762674},
  note   = {CC BY 4.0}
}
```
