# FragiliTidy

Tidyverse-compatible, high-performance fragility metrics for two-arm clinical
trials — for both **dichotomous** outcomes (Fragility Index / Reverse
Fragility Index) and **continuous** outcomes (Continuous Fragility Index and
Reverse Continuous Fragility Index).

`FragiliTidy` is designed to be fast
(~25x faster than `stats::fisher.test()` / `stats::chisq.test()`) incorporating rejection sampling and an iterative Welch t-test substitution algorithm
for the continuous indices. Everything plugs directly into `tidyverse` syntax.

## Installation

```r
# install.packages("remotes")
remotes::install_github("tomdrake/fragilitidy")
```

## Functions

| Function | Purpose |
| --- | --- |
| `fragility_index()` | Add a fragility-index column to a data frame (dichotomous outcomes). |
| `revfragility_index()` | Add a reverse-fragility-index column to a data frame. |
| `fragility_index_vec()` / `revfragility_index_vec()` | Vectorised forms for `dplyr::mutate()`. |
| `continuous_fragility_index()` | Add a Continuous Fragility Index column to a data frame. |
| `reverse_continuous_fragility_index()` | Add a Reverse Continuous Fragility Index column to a data frame. |
| `continuous_fragility_index_summary()` | CFI from a single set of summary statistics (mean, SD, n per arm). |
| `reverse_continuous_fragility_index_summary()` | Reverse CFI from a single set of summary statistics. |
| `continuous_fragility_index_raw()` | CFI from raw per-patient outcome vectors. |
| `continuous_fragility_index_vec()` / `reverse_continuous_fragility_index_vec()` | Vectorised summary-stat forms. |

## Quick start

### Dichotomous outcomes

```r
library(dplyr)
library(FragiliTidy)

trials <- tibble::tribble(
  ~study,    ~ie, ~ce, ~in_, ~cn,
  "Trial A",  10,  20,  100,  100,
  "Trial B",   5,  15,   80,   80,
  "Trial C",  30,  30,  200,  200
)

trials |>
  fragility_index(ie, ce, in_, cn) |>
  revfragility_index(ie, ce, in_, cn)
```

### Continuous outcomes

```r
trials_continuous <- tibble::tribble(
  ~study,    ~m1, ~s1, ~k1, ~m2, ~s2, ~k2,
  "Trial X",  70,  10,  50,  50,  10,  50,
  "Trial Y",  60,  15,  40,  55,  15,  40
)

trials_continuous |>
  continuous_fragility_index(m1, s1, k1, m2, s2, k2) |>
  reverse_continuous_fragility_index(m1, s1, k1, m2, s2, k2)
```

Or, for a single trial from summary statistics:

```r
continuous_fragility_index_summary(
  mean1 = 70, sd1 = 10, n1 = 100,
  mean2 = 50, sd2 = 10, n2 = 100,
  seed  = 1
)

reverse_continuous_fragility_index_summary(
  mean1 = 55, sd1 = 10, n1 = 30,
  mean2 = 50, sd2 = 10, n2 = 30,
  seed  = 1
)
```

## Background

- The **Fragility Index** (Walsh et al., 2014) is the minimum number of event
  reassignments in the smaller-event arm required to flip a statistically
  significant dichotomous result to non-significance.
- The **Reverse Fragility Index** is the analogous quantity for
  non-significant dichotomous results.
- The **Continuous Fragility Index** (Caldwell et al., 2021) extends the
  concept to continuous outcomes compared via Welch's t-test, via an
  iterative substitution algorithm.
- The **Reverse Continuous Fragility Index** here estimates *how many
  additional participants per arm* would have been required to drive a
  non-significant continuous outcome to significance, given the observed
  mean and SD per arm.

See `vignette("FragiliTidy")` for a walkthrough.

## References

- Walsh M, Srinathan SK, McAuley DF, et al. The statistical significance of
  randomized controlled trial results is frequently fragile. *J Clin
  Epidemiol* 2014;67:622-628.
- Caldwell JE, Youssefzadeh K, Limpisvasti O. A method for calculating the
  fragility index of continuous outcomes. *J Clin Epidemiol* 2021;136:20-25.

## License

GPL-3. See `LICENSE`.
