# Conservative additive xtar correction for the HMS 2018 reconstruction

Study date: 12 November 2026. This document records the purpose, method,
results and interpretation of the completed correction experiment and its
saved-candidate tolerance scan. It is a reference for thesis writing, with
links to the original numerical evidence. It does not establish historical
replay closure or report a new optics refit.

## 1. Purpose and hypothesis

The practical aim was to use the ample 2018 sieve-slit data to determine
whether a conservative xtar fit could improve resolution beyond the completed
fit of the other coefficients. The study motivation was that correlations
between xtar and other reconstruction variables make a separate fit difficult.
A reduced basis selected with Elastic Net and beam search might improve xtar
while strict limits preserve the performance of the other reconstructed
variables. A successful method could support NPS optics at both high and low
momentum. Transfer to those NPS settings is an intended application, rather
than a result established by this 2018 study.

The completed experiment implemented this idea as a small, regularized,
measured-input additive correction to reconstructed xtar. It did not refit
xtar-dependent transport coefficients. Downstream resolution and bias were
allowed to change within explicit limits, rather than being held exactly
constant.

All coefficients of the completed 2018 transport matrix remained fixed.
Changing its xtar input can nevertheless change reconstructed xptar, yptar,
ytar and delta. The reported downstream safeguards apply to xptar, yptar and
ytar. The four residual targets tabulated in this study are xptar, yptar,
ytar and xtar.

The experiment is distinct from fitting a direct predictor of true xtar, from
refitting xptar transport coefficients, and from running a new replay. Earlier
coordinate and direct-predictor studies motivated the conservative design but
are outside the numerical results summarized here.

## 2. Comparisons and reconstruction conventions

The **original 2018 matrix**, stored as `oldfit.dat`, is the historical
reference. The **completed 2018 refit** is the baseline for the incremental
correction. EN and Beam labels denote correction models relative to that
baseline. In the original experiment, the selection rule chooses zero for
both families, so `final` equals the completed refit.

Each matrix retains its own stored constants. External additions are zero for
all comparisons. All models use the same iterative reconstruction and saved
reaction vertex. An earlier discrepancy involved removing stored constants
and comparing saved xtar against iterative xtar. Offset changes from such
conventions must not be described as resolution improvements.

The original plots and metrics support refit/original, EN/refit, Beam/refit,
Beam/EN and final/original comparisons. The tolerance scan reports the
strongest eligible models separately from the original simplicity selection.
The five-term model discussed below belongs to this informational comparison.
It is not the model encoded by the original zero-correction final result.

### Geometry and units

Geometry truth uses centimetres and radians:

```text
xv     = -reactycalc - xmis
zv     = zfoil * cos(angle)
xptarT = (xsT - xv) / (168 - zv)
xtarT  = xv - xptarT * zv
```

The loader and runner check these relations, including
`xtarT + 168*xptarT = xsT`. The truth coordinate `xtarT` is distinct from the
reconstructed xtar coordinate. Position inputs to the transport calculation
are metres. Published residual tables use mrad for xptar/yptar and mm for
ytar/xtar.

### Correction application

The correction enters the existing fixed-saved-vertex iteration as

```text
xtar_proposal = anchor - xptar * saved_react_z * cos(angle) + g
```

Here `anchor` includes the existing beam and mispointing convention. The model
recomputes `g` from fixed measured inputs and adds it once to each proposal.
It never accumulates corrections across iterations. The reconstruction allows
at most five updates and uses the established 2 mrad xptar stopping criterion.
It does not retrack the vertex. Zero correction was checked against the
established afterburner on every training event. This parity check validates
the common offline baseline, rather than a historical replay configuration.

## 3. Data and validation design

The training sample contains **118,850 events** from 11 runs:
1337–1343 and 1345–1348. The existing exclusion for run 1347 at foil −10 cm
remains in force. Existing event IDs, masks and allocations are preserved.

The allocation contains 80% core and 20% inner shoulder. Those percentages
describe the training population composition and are not a train/test split.
Three validation folds keep complete runs together. The saved `folds.tsv`,
when present in the original output, records event-to-fold assignments.

Models and coefficients are frozen before loading the reserved sample.
Training and reserved event identities are checked for overlap. The reserved
loader returns 103,268 events. The eligible core/shoulder union has 99,769
events after the existing masks and exclusions. The reserved
sample had already been inspected in previous work. It therefore provides
development evidence and cannot be called a fresh blind test. Retuning on
these results and subsequently describing the same sample as blind would
misstate the design.

Foil/delta cells use original saved delta for common membership. Delta edges
in percent are:

```text
−15, −12, −10, −8, −5, 0, 5, 8, 10, 12, 14, 15
```

Core, shoulder and their union are evaluated separately. Cells with fewer
than 100 events appear in the diagnostics but remain uncertified. A favorable
overall number cannot certify a sparse cell or replace examination of all
foils and all four residual targets.

## 4. Model and search

The candidate basis contains 20 linear and quadratic monomials of five
measured quantities: xfp, xpfp, yfp, ypfp and beam y. Each basis column includes
`cos(angle)`. Position features use metres and angular features use radians.
There is no free correction intercept and no run, foil, hole identity or truth
predictor.

Run, foil and hole identity mean calibration labels: the data-taking run,
the known target foil and the known sieve aperture. Including them as model
inputs could let the fit memorize calibration settings. Geometric truth means
quantities derived from the known calibration geometry, such as xtarT and
the true target angles. These provide supervised training targets and residual
references, but an ordinary event does not supply its own true answer as an
input. The exclusion applies to predictors, not to the calibration events or
to the use of truth for training and evaluation. The aim is a correction that
uses measured quantities available during reconstruction. This design choice
does not itself prove that the correction transfers to a new setting.

Elastic net (EN) screens the basis using its penalized coefficients. The beam
search has width 30 and a maximum of six nonzero terms. Ridge strength 0.03
remains in the final fit. Candidate amplitudes are 0.1, 0.25, 0.5 and 1, with
zero correction always available. The numerical solve scales the design and
uses direct augmented-design SVD, rather than solving normal equations.

Rank-deficient designs and scaled condition numbers above 10,000 are rejected.
The absolute correction must not exceed 0.2 cm on validation or reserved
events. Oversized corrections are rejected rather than clipped. Candidates
must not introduce additional iteration-limit failures.

The original beam search evaluated 5,838 candidates through stage 5 and stopped
when additional exploration did not give material gain. These saved candidates
are the complete input to the subsequent tolerance scan. They are not an
exhaustive enumeration of possible bases.

## 5. Metrics, guards and selection

Let the xtar residual in foil/delta cell c be r, with cell mean μc and event
count nc. The primary score is the event-count-weighted within-cell variance:

```text
Vc = sum((r − μc)^2) / nc
Vpooled = sum(nc * Vc) / sum(nc)
SDpooled = sqrt(Vpooled)
SD improvement (%) = 100 * (1 − SDcandidate / SDbaseline)
```

The implementation uses population variances (`ddof=0`). Cell means are removed
before pooling. Overall population SD and per-cell SD also appear in the
safeguards and tables, but an overall SD is not interchangeable with the
primary pooled within-cell score. RMS/MSE additionally depend on bias and must
not be substituted for resolution SD.

The original policy permits at most a **2% downstream SD increase** overall
and in each populated foil/delta cell, separately for core, shoulder and union.
The guards apply in each validation fold and in pooled validation, then again
after the final solve on the reserved sample. For each downstream target,
additional absolute mean bias may not exceed **2% of its baseline SD**. A
reduction in offset cannot conceal a width loss or count as a width gain.

The term **bias guard** denotes this separate mean-residual constraint:

```text
abs(mean_candidate) <= abs(mean_baseline) + 0.02 * SD_baseline
```

It limits the additional absolute displacement, rather than requiring a zero
mean or limiting total bias to 2% of SD. For example, a baseline SD of 3 mrad
permits at most 0.06 mrad additional absolute mean residual. The resolution
metric removes cell means to prevent pure offset shifts from appearing as
width improvements, while the bias guard still checks those shifts.

A **candidate** is one proposed correction model, specified by its basis,
regularization and amplitude. An **eligible candidate** passes every applicable
residual-SD, mean-bias, correction-size, numerical-conditioning and iteration
requirement. Counts of eligible candidates therefore count models. Events are
the observations used to fit and evaluate them, and terms are the nonzero
basis components within one model.

Selection requires no increase in xtar MSE and at least 0.1% xtar SD improvement
to retain a correction. Among candidates within 0.5% **variance** of the best,
the rule prefers fewer terms. This tolerance is on variance, not SD. The
five-term model's approximately 0.196% SD improvement corresponds to roughly
0.392% variance reduction, small enough for the simplicity rule to retain
zero correction.

The accepted original result is zero if the predeclared beam proposal fails
the reserved safeguards or improvement requirement. No correction is adopted
automatically by the tolerance scan.

## 6. Results

### Original experiment

The original experiment completed successfully and selected the completed
refit with **zero correction**. EN and simplicity-selected Beam both have
zero nonzero terms. The final transport matrix remains byte-identical to the
completed 2018 refit. No correction was installed in hcana or replay.

The strongest passing saved beam candidate has five terms. Its small validation
gain does not overcome the original simplicity preference. The tolerance scan
evaluates and reports this model without suppressing it through that preference.

### Saved-candidate tolerance scan

The scan reproduced the original strict validation scores and eligibility
before loading reserved events. All **5,838 saved candidates** were rescored.
Only the downstream cell/overall SD limits changed. The bias guard and other
constraints remained fixed.

| Cell / overall SD limit | All eligible candidates | Strongest Beam | Strongest EN | Simplicity result |
|---|---:|---|---|---|
| 2% / 2% | 2,536 | ID 5208, five terms | Zero | Zero |
| 5% / 2% | 2,549 | Same model | Zero | Zero |
| 10% / 5% | 2,549 | Same model | Zero | Zero |

These totals include both families. Family-specific `eligible` values in
`selection.tsv` count the corresponding family pool, including zero, and
therefore differ from the all-candidate totals.

Relaxing the limits admitted 13 additional saved candidates. It did not change
the strongest model under the xtar selection metric. The scan performed no
new beam exploration. Since the original search used strict gates, it may
have pruned paths to bases that a new relaxed-gate search could explore.

### Informational five-term model

All changes below compare the strongest eligible correction against the
completed 2018 refit. SD gain is positive for improvement. Downstream SD change
is positive for worsening.

| Quantity | Result |
|---|---:|
| Validation xtar within-cell SD improvement | 0.196411% |
| Worst validation downstream populated-cell SD increase | 0.877151% |
| Reserved xtar within-cell SD improvement | 0.220569% |
| Reserved overall xptar SD change | −0.370241% |
| Reserved worst populated xptar cell SD change | +0.505304% |
| Reserved worst populated yptar cell SD change | approximately +0.001603% |
| Reserved worst populated ytar cell SD change | approximately +0.002814% |

Reserved overall yptar and ytar changes are negligible. The five-term model
passes the reserved safeguards even under the original 2%/2% policy. The exact
coefficients, feature labels, final-fit conditioning and all per-cell values
belong in the model JSON and TSV evidence, rather than being inferred from
rounded plot labels.

The original experiment's EN/Beam/final labels describe zero-correction models.
They should not be used as evidence for the informational five-term result.
Use the tolerance scan's `cell2_overall2_Beam` model and matching metrics for
that result. The other tolerance policies choose the same candidate.

The reserved pooled within-cell xtar SD is **0.327185720 mm** for the completed
refit and **0.326464048 mm** for the five-term model. The absolute narrowing is
about **0.000722 mm**, or **0.722 micrometres**. This differs from the
global xtar SD, which is 0.385379689 mm and 0.383430584 mm, respectively. The
primary score removes each cell mean before pooling, which explains the
different widths and relative changes.

### Five-term model record

The final-fit design has rank 5 and scaled condition number 2.118008. Candidate
5208 uses ridge strength 0.03 and amplitude 0.5. Its recorded maximum
validation correction is 0.0119104 cm, below the 0.2 cm cap. This maximum is
the candidate validation diagnostic, rather than a statement about arbitrary
inputs outside the sample.

The nonzero entries in
[`cell2_overall2_Beam.json`](archive/tolerance_scan/models/cell2_overall2_Beam.json)
are listed below. Evaluate `g` in cm using position inputs in metres, angular
inputs in radians, and the full feature in the first column. The coefficient
units depend on that feature. The JSON coefficients already include the
amplitude 0.5 and must not be multiplied by it again.

| Feature | Coefficient multiplying the feature |
|---|---:|
| cos(angle) × beam_y_m | 0.2993899400917299 |
| cos(angle) × xpfp_rad × yfp_m | 0.5432637138208494 |
| cos(angle) × xpfp_rad × ypfp_rad | 3.3474895958585975 |
| cos(angle) × xpfp_rad × beam_y_m | −14.971715715526258 |
| cos(angle) × yfp_m × beam_y_m | 9.463687531491916 |

All remaining coefficients and the correction intercept are zero. These
coefficients describe the informational model and do not change the adopted
zero-correction result.

## 7. Conclusions and limits for thesis use

The study identifies a small eligible improvement in xtar resolution. It also
shows a modest overall xptar improvement, accompanied by a small loss in the
worst populated xptar cell. The original simplicity rule still prefers zero
correction. The adopted reconstruction therefore remains the completed 2018
refit without the additive correction.

The tolerance scan establishes that relaxing the specified limits does not
change the leading saved candidate under the xtar objective. It does not
establish that no better correction exists, that the additional candidates are
optimal for an xptar objective, or that a new relaxed-gate search would return
the same basis. No statistical uncertainty estimate for the approximately
0.2% gain is established by the summary numbers, so the result should not be
described as statistically significant without additional analysis.

The reserved results remain development diagnostics. Sparse cells remain
uncertified. All comparisons use the documented common offline reconstruction
convention, and none certify historical replay offsets or closure. The study
does not evaluate a new replay deployment.

Suggested thesis wording:

> A constrained additive correction to the reconstructed xtar coordinate was
> studied with the completed 2018 transport matrix held fixed. The strongest
> eligible five-term correction reduced the pooled within-cell xtar residual
> standard deviation by 0.196% in run-group validation and 0.221% on a previously
> inspected reserved development sample. It satisfied the original downstream
> safeguards. The predefined simplicity rule nevertheless retained zero
> correction. Rescoring the saved candidate set with relaxed downstream SD
> limits admitted 13 additional candidates without changing the strongest model.

## 8. Evidence map and reproducibility

- `archive/original/results/` preserves the original experiment's plots, tables,
  manifest and selected correction models. Its final correction is zero.
- `archive/tolerance_scan/` preserves the three-policy scan, including
  `selection.tsv`, `candidate_scores.tsv`, `reserved_summary.tsv`,
  `reserved_metrics.tsv`, model JSON and plots.
- `source/` preserves the analysis code and available protocol records.
- `slides/` provides the high-level summary for presentation.
- `SHA256SUMS` records the archived file contents for later integrity checks.

See [the evidence index](EVIDENCE_INDEX.md) for direct links and table-field
definitions. The original `source/source_sha256.txt` also identifies dependency
snapshots. The nine dependency files archived under `source/dependencies/`
match those recorded SHA256 values. The separately added frozen protocol is
`source/README.md`.

Start with [reserved_summary.tsv](archive/tolerance_scan/reserved_summary.tsv)
for the five-term gains and guard outcomes.
Use `reserved_metrics.tsv` for model/group/target SD and signed bias with event
counts and sparse flags. Use `candidate_scores.tsv` for candidate-level
validation scores and eligibility under each tolerance. `selection.tsv`
distinguishes the strongest eligible candidate from the simplicity selection.
`models/cell2_overall2_Beam.json` gives the informational correction.

The summary figures show the search and policy comparison. The core and
shoulder SD heatmaps show foil/delta dependence for all four targets. Blue
means narrower residuals and red means wider residuals on the SD-change maps.
The separate bias pages show signed mean changes in the target's native
reporting units. The absolute-SD pages provide widths rather than percentage
changes. Read sparse-cell labels before interpreting any cell.

Remote source and output locations:

```text
Host/user: ifarm2402 / cploen
Experiment: /home/cploen/nps_link/xtar_correction_2018_20261112
Original output: results/
Tolerance output: tolerance_scan_5_2_and_10_5/
Adapter checkout: /u/home/cploen/xtar_refit_2018_20261111
Campaign: /home/cploen/nps_link/ML_HMS_OPTICS_JOINT_SURFACE/HMS_LowMom_LowDelta_2018_Accepted
```

Recorded source commits in `cploen/hallc_spectrometer_angular_optics`:

- Original correction runner: `10f03bb1ecf116a53be8baf622083ee8deb517d4`
- Tolerance scan: `1c13142879d9b8a225693925a72f7262568ebfb6`
- Geometry-checked loader/adapter: `b184e5ad6c0d96c91135948b429aabae2a9c19a8`

Matrix SHA256 values checked by the runner:

```text
Original 2018: b2036a935db3f17189fcf489d1017da030a8bb58de7d5a091384ef3d3b60c92c
Completed refit: 8644a345f39c6a352b0cd0431f4f06f1ac8e976902b167ada1aa4fb097c53fd6
```

The original protocol gives the fit command. To repeat only the saved-candidate
rescoring on ifarm, choose a new output directory:

```bash
E=/home/cploen/nps_link/xtar_correction_2018_20261112
cd "$E"
python3 -B -u xtar_tolerance_scan.py --experiment "$E" \
  --checkout /u/home/cploen/xtar_refit_2018_20261111 \
  --out "$E/tolerance_scan_REPEAT_UNIQUE_NAME"
```

This command does not start a new relaxed search. Full numerical reproduction
requires the original campaign inputs, event allocation and adapter dependencies.
The public archive contains study evidence and source, rather than the full
event-level campaign or saved NPZ predictions. Those predictions remain in the
remote output directories. Preserve the original outputs and select a new
directory for any repeat.

Execution records report seven targeted tests before the original experiment
and ten targeted tests for the scan, with strict-score and eligibility parity
reproduced in the completed remote scan. Preparing this archive does not rerun
the fit or add new scientific validation. Local `layout_check` plots from the
development directory are synthetic and must not be included as study evidence.

The archived scan manifest reports zero original-eligibility mismatches and a
maximum normalized validation-score difference of 2.3282162144005518e−15 when
reproducing the strict run. Archive preparation independently checked the
5,838-row candidate count, three eligibility totals, source/dependency hashes,
and byte identity of the refit and final transport matrices. These are archive
integrity and reconciliation checks, rather than another fit or search.
