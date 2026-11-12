# HMS xtar correction study

This repository archives the 12 November 2026 study of a small correction to
reconstructed **xtar** in the completed HMS 2018 optics refit. The purpose was
to improve that reconstruction before using it for NPS optics refits.

The hypothesis was that a regularized correction using measured inputs could
narrow xtar residuals without materially degrading downstream reconstruction.
The transport coefficients remained fixed. The study used three validation
folds grouped by run, followed by evaluation on a previously inspected reserved
development sample.

The strongest eligible five-term correction reduced xtar residual SD by
**0.196% in validation** and **0.221% on the reserved sample**, relative to the
completed 2018 refit. It passed the original downstream safeguards. Relaxing the
SD limits admitted 13 additional saved candidates but retained the same
strongest model.

**The original simplicity rule selected zero correction. No correction was
adopted or installed in replay.** The gain was small, and the scan did not
perform a new search under relaxed limits.

- [High-level slide deck](slides/xtar_study_summary.pptx)
- [Detailed study reference](STUDY_REFERENCE.md)
- [Original experiment evidence](archive/original/results/)
- [Tolerance scan evidence](archive/tolerance_scan/)
- [Methods and source](source/)
- [Plot and table index](EVIDENCE_INDEX.md)

The archive preserves the real plots, TSV tables and model records. Reserved
results are development evidence, rather than a fresh blind test.
