# Speed Shows Up, Breaks Don't: What Combine Tracking Tells Us About NFL Receiver Separation

## Overview

This project studies whether NFL Combine sensor-tracking data reveals
wide-receiver traits that carry over to regular-season games---and
whether those traits add information beyond traditional Combine
measurements.

Using the 2023--2025 draft classes, we link Combine tracking to NFL game
tracking and route-level separation outcomes. The central finding is
narrow but actionable: **tracked top speed transfers to NFL game speed,
while single-rep Combine route-break metrics do not show reliable
transfer or predictive value for separation.**

## Research questions

1.  Does a Combine movement metric measure a stable trait?
2.  Does that trait reappear in NFL game tracking?
3.  Does it explain receiver separation beyond traditional Combine
    measurements?

## Key findings

-   **Tracked top speed transfers to games.** Combine-tracked top speed
    is associated with NFL GO-route speed (`r = 0.48`), compared with an
    absolute correlation of `0.36` for the official 40-yard dash.
-   **Same-40 comparisons favor tracked speed.** Among 93 matched
    receiver pairs with similar 40-yard times, the receiver with higher
    tracked speed was faster on NFL GO routes in 70% of pairs. This
    supports using tracked speed as an additional vertical-route
    projection signal---not as an overall player ranking.
-   **Single-session break metrics are unreliable.** Combine break-index
    reliability was `0.38`; break deceleration and re-acceleration
    showed weak agreement across drills.
-   **Break metrics did not transfer to NFL hitch routes or explain
    separation.** NFL hitch-break deceleration had essentially no
    association with hitch separation (`r = -0.02` in the split-plays
    analysis).
-   **Tracking did not reliably improve separation prediction.** Adding
    tracking features did not provide consistent out-of-sample gains
    beyond traditional Combine metrics across repeated cross-validation
    and leave-one-draft-year-out validation.

## Data and linkage

The analysis covers 106 NFL-listed wide receivers from the 2023--2025
draft classes. Combine tracking was aggregated into drill- and
attempt-level movement features, then linked using `nfl_id` to
regular-season player-play records and game tracking.

The analysis cohorts vary by outcome availability:

-   106 WRs in the initial cohort
-   105 with a break index
-   82 with regular-season separation data
-   68 with at least 50 routes
-   56 with complete traditional Combine measurements for the primary
    adjusted association analysis

The source analysis reports that joins were validated for cardinality,
draft years agreed across tables, no play predated a player's draft
year, and 99.8% of route plays had tracking frames. Separation was
sourced from the Next Gen Stats (`NGS`) `separation_at_pass_forward`
field.

## Methodology

### Combine movement features

The analysis derives movement features from 10 Hz tracking, including:

-   Tracked top speed
-   Launch acceleration
-   Route peak acceleration
-   Break deceleration and re-acceleration
-   A pre-specified break index, calculated as the mean within-drill
    z-score of deceleration and re-acceleration across three hard-break
    drills

Break detection uses smoothed speed: a stem peak must be at least 4 yd/s
in Combine drills (3.5 yd/s in games), followed within two seconds by a
speed minimum at least 1.5 yd/s lower. Deceleration is derived from
changes in speed because the tracking feed's `a` field is an unsigned
magnitude.

### Separation Over Expected (SOE)

Raw separation varies substantially by route and alignment. The analysis
estimates play-level separation using route, coverage, cushion,
alignment, down, distance, formation, and season, then uses a
random-intercept model to shrink each receiver's mean residual.
**Positive SOE means a receiver was more open than expected, measured in
yards per route.**

### Statistical analysis and validation

The primary association analysis tests whether the break index is
associated with SOE after adjustment for height, weight, 40-yard dash,
10-yard split, vertical jump, broad jump, and draft year. It uses
precision-weighted least squares with HC3 standard errors and a player
bootstrap. Secondary features are adjusted using the Benjamini--Hochberg
false-discovery-rate procedure.

For prediction, ridge models are compared on identical players and
folds, with tuning inside each fold. The SOE target is rebuilt within
each fold using non-test players' plays to avoid target leakage.
Validation includes repeated five-fold cross-validation and
leave-one-draft-year-out evaluation.

## Results in detail

### Reliability and transfer

-   Combine break deceleration agreement across drills: `r = 0.20`
-   Combine break re-acceleration agreement across drills: `r = 0.07`
-   Combine break-index reliability: `0.38`
-   Tracked top-speed agreement across the Go route and 40-yard test:
    `r = 0.45`
-   NFL hitch deceleration reliability: `0.69`
-   NFL GO-route speed reliability: `0.86`
-   Combine tracked top speed vs. NFL GO-route speed: `r = 0.48` (95% CI
    `0.26–0.64`)
-   Launch acceleration vs. NFL GO-route speed: `r = -0.01`
-   Combine break deceleration vs. NFL hitch break deceleration:
    `r = 0.01` (95% CI `-0.30–0.31`)

Adding tracked top speed to the 40-yard dash increased explained
variance in NFL game speed from `12.9%` to `21.3%`.

### Association with separation

The primary break-index estimate was `-0.034` yards per route per
standard deviation (95% CI `-0.073` to `+0.005`; `p = 0.084`; `n = 56`).
The estimate was negative in all 11 reported robustness specifications.
No secondary feature survived false-discovery-rate correction.

### Predictive performance

The reported fold-safe SOE model comparison (`n = 68`) produced these
cross-validation RMSEs:

  Model                                                  CV RMSE
  -------------------------------------------------- -----------
  Draft year only                                          0.141
  Traditional Combine                                  **0.119**
  Traditional Combine + break index                        0.119
  Traditional Combine + tracked top speed                  0.121
  Traditional Combine + all five tracking features         0.125

The full tracking-feature model performed worse than the traditional
Combine baseline in cross-validation and in two of the three held-out
draft classes. The break index's small leave-one-draft-year-out gains
were at most about 1% of RMSE, with confidence intervals including zero.

## Practical implications for football

-   **Draft evaluation:** When two receivers have similar 40-yard times,
    tracked top speed may help distinguish vertical-route speed
    potential.
-   **Player usage:** Consider tracked speed as one input for vertical
    and clear-out route projection, not as a substitute for a complete
    player evaluation.
-   **Route-running evaluation:** Do not grade route breaks from a
    single Combine rep as though they are stable traits. More repeated
    reps---or multi-game tracking---would be needed to assess break
    mechanics more reliably.
-   **Separation prediction:** No tested model showed reliable
    out-of-sample value for predicting separation beyond traditional
    Combine measurements.

## Limitations

-   Analysis samples are small (`n = 56–68` depending on the question),
    limiting power to detect modest effects.
-   Results are subject to survivorship and playing-opportunity
    differences; the 2025 draft class has only one season of NFL
    exposure.
-   Combine drills often have about one rep per player, which limits
    trait reliability.
-   SOE does not explicitly account for quarterback, scheme, or defender
    identity.
-   Leave-one-draft-year-out validation has only three held-out classes.
-   Findings are associational and should not be interpreted causally.

## Conclusion

Combine sensor tracking contains a useful signal that carries into NFL
games---but in this analysis, **the clearest signal is speed, not route
breaks**. Tracked top speed transfers to game speed better than the
40-yard dash alone, while single-rep Combine break metrics did not
reliably transfer or improve separation prediction. The practical
recommendation is to use tracked top speed to refine vertical-route
projections and collect more reps before trusting Combine agility
traces.

## Reproducibility

The analysis writeup reports that results are generated from the
notebook `NFL_BDB2027_Combine_Break_to_Separation.ipynb`, using seed
`2027`. Key outputs are exported to `outputs/key_results.json` and
`outputs/tables/`.

Figures referenced in the writeup are expected under `outputs/figures/`:

-   `fig1_linkage.png`
-   `fig2_break_detection.png`
-   `fig3_translation.png`
-   `fig4_soe_construction.png`
-   `fig5_association_forest.png`
-   `fig6_model_comparison.png`
-   `fig7_scouting_profile.png`

Run the public Kaggle notebook in its intended environment to reproduce
the results. This README summarizes the accompanying writeup; it does
not independently rerun or verify the analysis.
