# Higgs signal classification

An undergraduate project comparing interpretable event-selection cuts with a dense neural network for signal-background separation in simulated ATLAS H → bb events.

This portfolio presents my completed analysis, selected code and historical results. The original data and provided helper implementations are omitted; full reproducibility is not claimed. No neural networks were retrained to prepare this presentation.

## Explore the analysis

1. [Cut-based analysis](notebooks/01_cut_based_analysis.ipynb): threshold directions, single-variable scans and joint cut optimisation.
2. [Neural-network optimisation](notebooks/02_neural_network_optimisation.ipynb): scaling, optimiser, architecture and training-setting comparisons.
3. [Evaluation and diagnostics](notebooks/03_neural_network_evaluation.ipynb): historical test comparison, training statistics, variable removal, training uncertainty and high-score regions.

Read the notebooks with their saved outputs. Running all cells would clear the historical output archive. Selected code illustrates model-building, seeding, sampling and plotting logic; experiment execution is described rather than rerun. Plotting excerpts reference historical aggregate tables that are not reconstructed as live variables.

## Main results

| Study | Historical result |
| --- | --- |
| Final cut selection | Mtop > 200 GeV and dRBB < 1.45 |
| Cut sensitivity on the combined sample | 1.918, versus 1.499 before cuts |
| Cut sensitivity on the test split | 1.878 |
| Repeated NN test sensitivity | 2.271 ± 0.026; mean ± sample standard deviation across five retrainings |
| Individual NN test run | Approximately 2.27 ± 0.02; propagated metric uncertainty |
| Selected NN | Standard scaling; dense [16, 8]; ReLU; sigmoid output; Adam; binary cross-entropy; 20 epochs; batch size 64 |

The NN mean was about 21% above the historical cut benchmark on the same test split. Removing mBB produced the largest drop in validation sensitivity. Background training statistics affected performance more strongly than signal statistics in the saved fraction scans.

Sensitivity was evaluated from weighted signal and background yields using

$$Z = \sqrt{\sum_i 2[(s_i+b_i)\ln(1+s_i/b_i)-s_i]}.$$

The historical cut analysis used dijet-mass bins; the NN analysis used adaptive score bins from provided helper functions. Their implementation and handling of bin edges, weights and degenerate bins are not reconstructed here.

## Scientific limitations

- Cut optimisation used the combined dataset, including test events. The cut-versus-NN comparison is therefore not a strictly untouched-test comparison. NN configuration comparisons used validation results.
- The final implemented cut uses dRBB < 1.45. The historical overlap diagnostic uses dRBB < 1.4; its overlap and purity results describe that stricter reference region.
- The five-run test spread 0.026 is a sample standard deviation, not the approximately 0.02 propagated uncertainty of an individual run. Most other scan summaries use population standard deviations.
- The training-fraction loops seed subsampling and shuffling but do not reset model-training seeds in each iteration. Their repeats depend on execution order and evolving RNG state, rather than being fully controlled paired training-seed comparisons.
- Assigning PYTHONHASHSEED inside a running interpreter is not retroactive, and TensorFlow deterministic operations were not explicitly enabled.
- The hyperparameter search changed settings sequentially. Small differences relative to run-to-run spread do not establish a globally optimal network. The displayed validation scores of 1.02 versus 0.91 imply about 12% improvement; the approximately 21% result above instead compares the NN and cut benchmark on the test split.
- Variable-removal error bars combine population spreads in quadrature. They are not standard errors of the means and do not account for covariance between paired seeds.
- The high-score diagnostic used the model left by the fixed-data validation uncertainty experiment (seed 111). The displayed contribution plot uses a fixed bar width for visualisation despite unequal adaptive bin widths; it represents squared-sensitivity contributions, not event-count percentages.

## Provenance and scope

University-provided data and baseline methodology supported the historical analysis. My contributions include cut optimisation, neural-network configuration studies and the follow-up diagnostics. See [PROVENANCE.md](PROVENANCE.md) for the attribution boundary.

The portfolio contains selected aggregate tables and ten historical plots. No event-level records, event identifiers, prediction arrays, supplied helper implementations or supplied model artifacts are included. This is an independent undergraduate project, not an official ATLAS analysis. No repository-wide licence has been assigned.
