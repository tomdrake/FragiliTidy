# FragiliTidy

Tidyverse-compatible, high-performance Fragility Index and Reverse Fragility
Index calculations for 2x2 contingency tables from clinical trials.

`FragiliTidy` uses customized hypergeometric and algebraic 2x2 routines (≈25×
faster than `stats::fisher.test()` / `stats::chisq.test()`) plus binary-search
algorithms that yield an additional 10×–1000× speedup on large trials,
while plugging directly into `dplyr` pipelines.

## Installation

```r
# install.packages("remotes")
remotes::install_github("tomdrake/fragilitidy")
```

## Quick start

```r
library(dplyr)
library(FragiliTidy)

trials <- tibble::tribble(
  ~study,   ~ie, ~ce, ~in_, ~cn,
  "Trial A", 10,  20,  100,  100,
  "Trial B",  5,  15,   80,   80,
  "Trial C", 30,  30,  200,  200
)

trials %>%
  tidy_fragility_index(ie, ce, in_, cn) %>%
  tidy_revfragility_index(ie, ce, in_, cn)
```

## Functions

| Function | Purpose |
| --- | --- |
| `tidy_fragility_index()` | Add a fragility-index column to a data frame. |
| `tidy_revfragility_index()` | Add a reverse-fragility-index column to a data frame. |
| `fragility_index_vec()` | Vectorised FI, usable inside `dplyr::mutate()`. |
| `revfragility_index_vec()` | Vectorised reverse FI, usable inside `dplyr::mutate()`. |

All functions accept a `conf.level` argument (default `0.95`) and a `verbose`
flag that returns the full p-value progression for each iteration.

## Background

The **Fragility Index** (Walsh et al., 2014) is the minimum number of event
reassignments in the smaller-event arm required to flip a statistically
significant result to non-significance. The **Reverse Fragility Index** is the
analogous quantity for non-significant results. Both are increasingly reported
alongside p-values to convey the robustness of trial findings.

## License

GPL-3. See `LICENSE`.
