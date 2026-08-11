# Normalization Statistics

## Source
All experiments use the **FI-2010 `NoAuction_Zscore`** subset, i.e. the officially
Z-score–normalized version of the FI-2010 benchmark
(Ntakaris et al., *Journal of Forecasting*, 2018, ref. [21] in the manuscript).

## Method (causal, no look-ahead)
Each feature `j` is standardized as

    x_tilde[t,j] = (x[t,j] - mu_j) / sigma_j,   j = 1..144

where `mu_j` and `sigma_j` are the per-feature mean and standard deviation
**computed from prior trading days only** (the FI-2010 convention), so the
normalization is strictly causal and introduces no look-ahead bias. This matches
Eq. (7) and Section III-C of the manuscript.

## Why no separate statistics file is shipped
The FI-2010 `NoAuction_Zscore` files distributed by the benchmark authors are
**already normalized**; the mean/variance were applied by the dataset provider
using the anchored day convention above. This pipeline therefore consumes the
normalized files directly and does not recompute or store its own `mu/sigma`.

The exact files, day ranges, per-split sample counts and class distributions
consumed for every fold are documented in
`../4_dataset_manifest_and_leakage/dataset_manifest_CF*.{csv,json}`.

## To reproduce on a new instrument/venue
Recompute `mu_j, sigma_j` from a causal (prior-day) window on the target feed
and apply the same Eq. (7); no other change to the datapath is required
(see manuscript Section VII-C, third limitation).
