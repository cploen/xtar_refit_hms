# Plot and table index

All scientific figures and tables below come from the completed ifarm outputs.
The original experiment selected zero correction. The tolerance scan separately
reports the informational five-term model. This distinction matters when
comparing figures or model names across the two directories.

## Start here

| File | Use |
|---|---|
| [Study reference](STUDY_REFERENCE.md) | Method, exact results, interpretation and reproducibility |
| [Slide deck](slides/xtar_study_summary.pptx) | Eight summary slides, two figure reading guides and two original summary figures |
| [Tolerance selection](archive/tolerance_scan/selection.tsv) | Strongest candidate and simplicity result for each policy/family |
| [Reserved summary](archive/tolerance_scan/reserved_summary.tsv) | Five-term gain, downstream changes and reserved guard outcomes |
| [Five-term model](archive/tolerance_scan/models/cell2_overall2_Beam.json) | Feature labels, final coefficients, rank and condition number |

## Original experiment tables

These files are under `archive/original/results/`.

| File | Contents |
|---|---|
| [search_candidates.tsv](archive/original/results/search_candidates.tsv) | All 5,838 saved candidates, their validation scores and original eligibility |
| [enet_cv.tsv](archive/original/results/enet_cv.tsv) | Elastic-net validation records |
| [folds.tsv](archive/original/results/folds.tsv) | Training event identities and run-group fold assignments |
| [reserved_metrics.tsv](archive/original/results/reserved_metrics.tsv) | Original/refit/selected EN/selected Beam/final residual metrics by population and cell |

In the reserved metrics, `sd` is residual population SD, `bias` is the signed
mean, and `rms` combines spread and bias. Units are mrad for xptar/yptar and mm
for ytar/xtar. `n` is the group count and `sparse=True` identifies N < 100.
Original cell labels use `population|foil|delta_bin`. Original EN, Beam and
final predictions coincide with the refit because selection retained zero.

## Tolerance scan tables

These files are under `archive/tolerance_scan/`.

| File | Contents |
|---|---|
| [candidate_scores.tsv](archive/tolerance_scan/candidate_scores.tsv) | 5,838 rescored candidates and eligibility under all three policies |
| [selection.tsv](archive/tolerance_scan/selection.tsv) | Six family/policy selections, including separate simplicity IDs |
| [reserved_summary.tsv](archive/tolerance_scan/reserved_summary.tsv) | Six frozen-model reserved evaluations and downstream safeguards |
| [reserved_metrics.tsv](archive/tolerance_scan/reserved_metrics.tsv) | Eight models including oldfit/refit, all four residual targets, populations and cells |

### Fields and signs

- Candidate `id` is the zero-based row order of the original saved candidate
  table. Candidate 5208 is the strongest Beam under every policy.
- `score` is pooled within-cell xtar residual variance in mm². `mse` is xtar
  residual mean square in mm². `terms`, `mask`, `kind`, `alpha`, `l1`, and
  `amplitude` describe the candidate. `correction_max_cm` is a validation
  diagnostic and `physical` summarizes the physical/numerical eligibility.
- Candidate `global_*` and `cell_*` are fractional downstream SD changes,
  maximized across the applicable validation folds, pooled populations and
  populated cells. Multiply them by 100 to obtain percentages. `bias_*` is the
  increase in absolute bias divided by baseline SD, rather than a native-unit
  signed mean shift. Policy columns are booleans.
- Selection fields ending `_pct` are already percentages.
  `validation_xtar_width_gain_pct` is positive for narrower xtar residuals.
  `worst_global_loss_pct` and `worst_cell_loss_pct` are positive for worsening.
  `simplicity_id` and `simplicity_terms` describe the original selection rule.
  `eligible` counts the family-specific pool, including zero. Zero belongs to
  both family pools, so summing the EN and Beam counts double-counts it once.
- Reserved `xtar_width_gain_pct` and `xtar_mse_gain_pct` are positive for
  improvement. Downstream fields ending `_change_pct` are positive for worse
  SD. `overall` describes the eligible union. `worst_population` maximizes over
  core, shoulder and union. `worst_cell` maximizes over their populated cells.
  Worst values for different variables can come from different groups.
- Reserved `*_worst_bias_increase_in_sigma` is dimensionless, normalized to
  baseline SD. The guard limit is 0.02. `reserved_guards_pass` is the combined
  guard outcome for that policy.
- Detailed reserved metrics use `group`, `target`, `unit`, `n`, `sd`, `bias`
  and `sparse`. Tolerance cell labels use `population|foil:delta_bin`. Cell
  membership always uses original saved delta. Read the source's `EDGES` and
  `searchsorted(..., side='right')` convention for exact boundary behavior.

The model `cell2_overall2_Beam` is the informational five-term correction.
The `cell5_overall2_Beam` and `cell10_overall5_Beam` records contain the same
selected candidate and coefficients. All corresponding EN models are zero.
The labels describe the SD-limit policy, not a new transport fit.

## Plots

| Original experiment | Interpretation |
|---|---|
| [xtar_refit_summary.png](archive/original/results/plots/xtar_refit_summary.png) | Validation gain/term count, conditioning and the original selected reserved models |
| [core_sd_change.png](archive/original/results/plots/core_sd_change.png) | Core foil/delta SD percentage changes |
| [shoulder_sd_change.png](archive/original/results/plots/shoulder_sd_change.png) | Shoulder foil/delta SD percentage changes |
| [core_absolute_sd.png](archive/original/results/plots/core_absolute_sd.png) | Core residual widths in the reported physical units |
| [shoulder_absolute_sd.png](archive/original/results/plots/shoulder_absolute_sd.png) | Shoulder residual widths in the reported physical units |
| [core_bias_change.png](archive/original/results/plots/core_bias_change.png) | Core signed residual mean changes |
| [shoulder_bias_change.png](archive/original/results/plots/shoulder_bias_change.png) | Shoulder signed residual mean changes |

| Tolerance scan | Interpretation |
|---|---|
| [tolerance_summary.png](archive/tolerance_scan/plots/tolerance_summary.png) | Candidate gain/loss tradeoffs and the unchanged strongest model across policies |
| [core_sd_changes.png](archive/tolerance_scan/plots/core_sd_changes.png) | Core SD changes for refit/original and each strongest policy/family model |
| [shoulder_sd_changes.png](archive/tolerance_scan/plots/shoulder_sd_changes.png) | The corresponding shoulder SD changes |
| [core_bias_changes.png](archive/tolerance_scan/plots/core_bias_changes.png) | Core signed bias changes, separate from SD |
| [shoulder_bias_changes.png](archive/tolerance_scan/plots/shoulder_bias_changes.png) | The corresponding shoulder bias changes |

On SD-change heatmaps, blue indicates narrower residuals and red indicates
wider residuals. Bias maps have a different meaning: their sign is the direction
of the mean shift, not a general improvement/worsening label. All four targets
and all foils appear in the archived full-resolution pages. N < 100 cells
remain uncertified. Small rounded plot labels do not replace the exact TSVs.

### Reading the tolerance summary

The upper panels plot one correction model per dot after the physical and
mean-bias checks. Vertical position is validation xtar SD reduction, so higher
is better. The horizontal coordinate is the worst downstream SD increase:
across populated cells at left and whole core/shoulder/union populations at
right. Moving right permits more downstream degradation. Dotted vertical lines
mark the SD limits. Favorable tradeoffs lie toward the upper left, subject to
all the remaining constraints.

The lower-left panel compares the strongest eligible EN and Beam models across
the three tolerance policies. Positive means narrower xtar residuals. The flat
Beam curves show that the same model leads under all three policies. In the
lower-right panel the sign convention changes: negative SD change means a
narrower residual. The approximately −0.370% xptar and −0.221% xtar values are
constant across policies. The former is overall population SD, while the latter
is pooled within-cell SD.

### Reading the original search summary

Panel A plots validation xtar SD in mm against nonzero correction term count.
Lower is better. The dashed line is zero correction, and the stars identify
the original simplicity-selected models at zero terms. The blue curve shows
the best passing Beam model at each term count.

Panel B uses **variance improvement**, not SD improvement. Approximately 0.392%
variance reduction corresponds to 0.196% SD reduction. That small gain leaves
zero correction within the original 0.5% variance simplicity allowance.
Panel C shows numerical conditioning of the scaled correction design, with a
logarithmic vertical axis. Smaller condition numbers indicate a more stable
design; the plotted points describe the best passing model at each term count.

Panel D shows reserved pooled within-cell xtar SD. The completed refit narrows
the residuals relative to original 2018. The EN and Beam bars represent the
original selected zero-correction models and therefore coincide with the refit.
They do not depict the informational five-term model's reserved result.

## Models, provenance and source

The original [manifest](archive/original/results/manifest.json) records the
zero-correction selection, event counts, hashes, settings and limitations.
Its [final correction](archive/original/results/final_correction.json) is zero.
The [refit matrix](archive/original/results/matrices/refit.dat) and
[final transport matrix](archive/original/results/matrices/final_transport.dat)
are byte-identical. The original [oldfit matrix](archive/original/results/matrices/oldfit.dat)
is the historical reference.

The tolerance [manifest](archive/tolerance_scan/manifest.json) records strict
parity, event counts and input hashes. Its [models directory](archive/tolerance_scan/models/)
preserves all six final-fit JSON records. The original
[report](archive/original/results/REPORT.md) is a short generated run summary.

[source/README.md](source/README.md) is the frozen correction protocol.
[source/TOLERANCE_SCAN.md](source/TOLERANCE_SCAN.md) describes the rescoring.
The source directory also preserves both runners, plotting code, targeted
tests, the adapter commit, and nine dependency snapshots matching the recorded
source hashes. `SHA256SUMS` covers the curated repository contents except
itself and Git metadata.

This repository contains no synthetic layout-test plots, full ROOT campaign,
or event-level NPZ predictions. The remote output paths and commands are in
the study reference. Full refitting still requires the original campaign data.
