# Evaluation protocol

Declared 8 November 2026, before any model is trained. The splits and metrics below are
fixed from this date; the test set is evaluated once, after all tuning is complete.

## Data

BindingDB_Kd via Therapeutics Data Commons. 52,284 raw drug-target pairs; duplicate
measurements of the same pair are merged by keeping the strongest reported binding,
leaving 42,236 pairs. Labels are converted to pKd = -log10(Kd in M), so larger values
mean tighter binding.

## Splits

Cold-drug split, grouped by **canonical SMILES**, so no compound in the test set appears
in training.

| Set | Share of drugs | Approx. pairs |
| --- | --- | --- |
| Train | 70% | 29,600 |
| Validation | 10% | 4,200 |
| Test | 20% | 8,400 |

- Tool: `sklearn.model_selection.GroupShuffleSplit`
- **Split seed: 42** (fixed; must not change between runs)
- Pair counts deviate from the drug proportions because the number of measurements per
  drug varies.

Order of operations, which must not be reordered:

1. Canonicalize SMILES
2. Merge duplicate (drug, protein) measurements, keeping the strongest affinity
3. Split by drug group

Canonicalizing before the merge matters: the same compound written two different ways
would otherwise be treated as two drugs and could land on both sides of the split.

Because drugs and not proteins are held out, results do not demonstrate generalization
to unseen protein targets.

## Leakage safeguards

- Every quantity learned from data is fit on training pairs only: the global mean pKd,
  the per-target mean lookup table, and the CNN's SMILES and amino-acid vocabularies.
- Where a test protein has no training measurements, the per-target-mean baseline falls
  back to the global training mean.
- Hyperparameters are chosen on validation only. The CNN's stopping epoch comes from
  early stopping on validation loss, never on test loss.
- The data preparation script asserts zero drug overlap across train, validation and
  test, and aborts if any is found:

```python
assert not (set(train.Drug) & set(val.Drug))
assert not (set(train.Drug) & set(test.Drug))
assert not (set(val.Drug) & set(test.Drug))
```

## Metrics

The task is regression, so classification accuracy does not apply.

| Metric | Role |
| --- | --- |
| RMSE (pKd units) | Primary. Error in log-affinity units a chemist can interpret. |
| Pearson r | Secondary |
| Spearman rho | Secondary |
| Concordance index (C-index) | Secondary. Standard benchmark metric in the DTA literature, for comparability with published work. |

Every metric is reported twice: on the full test set, and on the subset with pKd > 5.0.
About 47% of labels sit at the pKd 5 floor and are likely censored ("> X") rather than
measured, so the full-set figures partly reflect that artifact.

Screening view: threshold at pKd >= 7 (Kd <= 100 nM) and report AUROC, AUPRC, and
sensitivity and specificity at that threshold.

## Uncertainty

- Each model is trained under **5 random seeds**, reported as mean +/- SD. The data split
  stays fixed at seed 42 across all five, so the spread reflects training variability
  only.
- 95% bootstrap confidence interval on RMSE, 1,000 resamples of the test set, resampled
  **at the drug level** so the grouping structure is preserved.
- The global-mean and per-target-mean baselines are deterministic, so they carry the
  bootstrap interval only, with no SD.
- One model is judged better than another only when a bootstrap interval on the
  difference between them, computed on paired resamples, excludes zero.

## Interpretability artifact

Predicted against measured pKd on the test set, with censored pairs marked, plus
residuals binned by measured pKd. This shows whether error is uniform across the
affinity range or concentrated at the high-affinity end, which is the range that matters
for prioritization, and whether the model predicts close to 5.0 for all weak binders
rather than discriminating among them.

## Compute budget

Free-tier cloud notebook, single T4 GPU, sessions of up to 12 hours.

| Step | Estimate |
| --- | --- |
| XGBoost fit (CPU) | ~10 min |
| DeepDTA-style CNN (GPU) | ~45 min per seed |
| Five CNN seeds | ~4 h |

These two per-run figures are estimates from the dataset size and model scale, not
measurements. Replace them with timings from an actual run.
