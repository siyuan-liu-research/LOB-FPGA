# LOB-FPGA: Microsecond-Latency On-Chip Limit Order Book Prediction on an Edge FPGA

## Status

This repository currently hosts **preliminary verification materials** that support the
results reported in the manuscript. The **full model-training code, HLS kernel source,
and trained model weights will be released here upon acceptance** of the paper.

## Contents (available now)

| Path | Description |
|------|-------------|
| `dataset_manifest_and_leakage/` | Per-fold dataset manifests (file, days, tick range, sample count, class distribution) and automated leakage self-check logs for all nine anchored folds, plus the timestamped test-set access audit. |
| `postroute_reports/` | Vivado post-place-and-route timing / utilization / power reports for the five feasible design variants on the Xilinx Kintex UltraScale KU040 (xcku040-ffva1156-2-e). |
| `NORMALIZATION.md` | Z-score normalization specification (causal, prior-day statistics). |
| `SEEDS.md` | Random seeds used throughout the study. |

## To be released upon acceptance

- Model-training and QAT pipeline (PyTorch)
- HLS kernel source (`lob_kernel.cpp` / `.h`) for all variants
- Trained model weights (FP32, QAT, and exported INT8 fixed-point parameters)
- HLS testbenches and the on-board test driver

## License

Released under CC BY 4.0 (see `LICENSE`). If you use these materials, please cite the paper.
