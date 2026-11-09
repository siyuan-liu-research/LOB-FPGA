# Microsecond-Latency, Fully On-Chip Limit Order Book Prediction on an Edge FPGA: A Lightweight INT8 1D-CNN Hardware–Software Co-Design
## Status

The manuscript has now been accepted. The paper PDF is available in this repo. Full experimental artifacts will be released as soon as possible.

## Contents (available now)

| Path | Description |
|------|-------------|
| `dataset_manifest_and_leakage/` | Per-fold dataset manifests (file, days, tick range, sample count, class distribution) and automated leakage self-check logs for all nine anchored folds, plus the timestamped test-set access audit. |
| `postroute_reports/` | Vivado post-place-and-route timing / utilization / power reports for the five feasible design variants on the Xilinx Kintex UltraScale KU040 (xcku040-ffva1156-2-e). |
| `NORMALIZATION.md` | Z-score normalization specification (causal, prior-day statistics). |
| `SEEDS.md` | Random seeds used throughout the study. |
|`paper.pdf`|	Published manuscript PDF.|

## To be released upon acceptance

- Model-training and QAT pipeline (PyTorch)
- HLS kernel source (`lob_kernel.cpp` / `.h`) for all variants
- Trained model weights 
- HLS testbenches and the on-board test driver

## License

Released under CC BY 4.0 (see `LICENSE`). If you use these materials, please cite the paper.
