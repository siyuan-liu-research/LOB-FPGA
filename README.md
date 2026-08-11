# LOB-FPGA: Microsecond-Latency On-Chip Limit Order Book Prediction on an Edge FPGA

## Status

This repository currently hosts **preliminary verification materials** that support the
results reported in the manuscript. The **full model-training code, HLS kernel source,
and trained model weights will be released here upon acceptance** of the paper.

The materials provided now are sufficient to inspect the experimental protocol
(leakage control, data splits), the post-place-and-route hardware results, and the
training configuration, without exposing the model implementation prior to publication.

## Contents (available now)

| Path | Description |
|------|-------------|
| `dataset_manifest_and_leakage/` | Per-fold dataset manifests (file, days, tick range, sample count, class distribution) and automated leakage self-check logs for all nine anchored folds, plus the timestamped test-set access audit. |
| `postroute_reports/` | Vivado post-place-and-route timing / utilization / power reports for the five feasible design variants on the Xilinx Kintex UltraScale KU040 (xcku040-ffva1156-2-e). |
| `HYPERPARAMETERS.md` | Full training and quantization hyperparameters (Table III of the paper). |
| `NORMALIZATION.md` | Z-score normalization specification (causal, prior-day statistics). |
| `SEEDS.md` | Random seeds used throughout the study. |

## To be released upon acceptance

- Model-training and QAT pipeline (PyTorch)
- HLS kernel source (`lob_kernel.cpp` / `.h`) for all variants
- Trained model weights (FP32, QAT, and exported INT8 fixed-point parameters)
- HLS testbenches and the on-board test driver

## Key facts

- **Dataset:** FI-2010 (NoAuction, Z-score), 144-dimensional features, horizon k = 100.
- **Protocol:** official anchored walk-forward, main fold CF_9 (train days 1–9, test day 10);
  purge/embargo of T+k = 110 ticks at every split boundary; test set evaluated once.
- **Model:** residual 1D-CNN, 86,211 parameters; FP32 Macro-F1 0.8509 → INT8 0.8331.
- **Hardware:** KU040 at 100 MHz; fully-unrolled design at 57 µs, 2.288 W; board-level
  execution bit-exact with the PyTorch INT8 reference on all 2,000 tested samples.

## License

Released under CC BY 4.0 (see `LICENSE`). If you use these materials, please cite the paper.

## Citation

```bibtex
@article{liu2026lobfpga,
  title   = {Microsecond-Latency, Fully On-Chip Limit Order Book Prediction on an Edge FPGA:
             A Lightweight INT8 1D-CNN Hardware--Software Co-Design},
  author  = {Liu, Siyuan},
  journal = {IEEE Access},
  year    = {2026},
  note    = {Under review}
}
```
