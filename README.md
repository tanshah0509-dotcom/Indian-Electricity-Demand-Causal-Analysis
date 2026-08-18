# Did India's Staggered Reopening Change Electricity Demand?
### A Difference-in-Differences Analysis

## Problem
India's COVID lockdown was uniform nationally through 31 May 2020. From
1 June 2020 ("Unlock 1.0"), states gained reopening discretion and
diverged sharply. This project isolates the causal effect of that policy
divergence on state-level electricity demand.

## Data
State-level daily electricity demand, reshaped from the same Grid-India/
POSOCO dataset used in the companion forecasting project (see
india-electricity-demand-forecast repo). Duplicates and the one verified
data-entry outlier were already resolved upstream.

## Design
- **Treatment state:** Maharashtra — extended lockdown restrictions
  (malls, hotels, religious places, restaurants) through 30 June 2020,
  followed by a slower staged reopening ("Mission Begin Again").
- **Control state selection — tested two candidates, not assumed:**
  - Uttar Pradesh — largely followed the central Unlock 1.0 schedule.
  - Gujarat — also largely followed the central schedule (offices/
    transport/shops from 1 June, malls/hotels pending 8 June guidance,
    matching the national cadence).
  - Selection criterion was each state's real-world reopening timeline
    relative to the national schedule, verified against contemporary
    news sources — not chosen based on any electricity-demand outcome.
- **Pre-period:** 15 Apr – 31 May 2020 (uniform national lockdown)
- **Post-period:** 1 Jun – 31 Jul 2020 (state policies diverged)

## Parallel Trends Check — Both Candidate Pairs Tested
| Pair | Treated:time_trend | p-value | Holds? |
|---|---|---|---|
| Maharashtra vs Uttar Pradesh | -1.445 | 0.003 | **No** |
| Maharashtra vs Gujarat | -0.018 | 0.901 | **Yes** |

The Maharashtra–UP pair failed the parallel trends assumption and was
excluded from the main analysis. This is reported as a finding in its
own right, not hidden — control state selection through actual testing,
rather than assumption, materially changed the analysis.

## Main Result (Maharashtra vs Gujarat)
| Term | Coefficient | p-value |
|---|---|---|
| Treated_Post (the DiD estimate) | **-48.47 MU/day** | < 0.001 |

Maharashtra's electricity demand fell by an additional ~48.47 MU/day
(~11-12% of its pre-period baseline of ~420 MU) specifically attributable
to its extended lockdown, relative to Gujarat.

Independently cross-validated with a plain Excel PivotTable average —
matched the regression-implied values in all four state/period cells to
within rounding error.

The `Post` term alone (-1.67, p=0.694) was not significant — no strong
general shift in Gujarat's demand pre- to post-June. Two unverified
hypotheses for this: offsetting effects between reopening and monsoon
onset, or continuous industrial/agricultural load dominating total
demand over the smaller commercial slice affected by reopening. Neither
could be tested with the data available (no weather or sector-level data).

## Robustness: Two Placebo Tests
| Fake cutoff | Treated_Post | p-value | As % of baseline |
|---|---|---|---|
| 1 May 2020 | -16.36 | 0.028 | ~4.2% |
| 15 April 2020 | -12.60 | 0.032 | ~3.3% |
| **Real cutoff (1 June)** | **-48.47** | **<0.001** | **~11-12%** |

Both placebo tests, in non-overlapping windows with no real policy event,
showed a smaller but statistically significant effect — suggesting a
persistent structural divergence between these two states' trends
(likely non-linear growth not fully captured by a linear model), rather
than a clean null. The real effect is 3-4x larger than this baseline
noise, suggesting a genuine additional effect on top of it, though this
tempers full confidence in the exact magnitude of -48.47.

## Limitations
- Two-state comparison; a larger multi-state panel would strengthen
  external validity.
- Durbin-Watson statistics were low across all models (0.35-0.63),
  indicating residual autocorrelation typical of daily time series —
  reported p-values are likely somewhat optimistic as a result.
- No weather or sector-level (industrial/commercial/residential) data
  was available to test the two hypotheses offered for the flat Post term.
- Both placebo tests showed a small persistent effect, indicating some
  non-parallel structural trend between Maharashtra and Gujarat beyond
  what a linear specification fully captures.

## Tools
Python (pandas, statsmodels, Matplotlib), Excel (PivotTables, cross-validation),
Power BI (Power Query, comparative visualization)
