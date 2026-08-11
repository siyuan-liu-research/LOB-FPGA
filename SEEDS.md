# Random Seeds

All stochastic steps are seeded for full reproducibility.

| Seed | Used for | Where set |
|------|----------|-----------|
| **3407** | FP32 training, QAT fine-tuning, all main results | `set_seed(3407)` for Python / NumPy / PyTorch / CUDA at the top of the training and QAT scripts (`../1_qat_config/qat_perchannel_input.py`, and the FP32 trainer) |
| **2026** | Deterministic sampling of the 2,000 on-board test subset | board-test script (`../7_board_test/board_test_144.py`); the exact drawn indices are frozen in `../7_board_test/board_manifest.csv` |
| **{3407, 42, 123, 2024, 7}** | Five-seed residual/Focal-Loss ablation (Table on validation only) | multi-seed ablation script (`ablation_multiseed.py`) |

## Notes
- The main deployed model (FP32 champion → QAT INT8) uses seed **3407** end-to-end,
  matching Table III of the manuscript ("random seed 3407 for Python, NumPy, PyTorch and CUDA").
- Seed **3407** is fixed for `random`, `numpy`, `torch` (CPU) and `torch.cuda`
  (all GPU streams), plus `cudnn.deterministic = True`, so a re-run reproduces the
  reported weights bit-for-bit on the same PyTorch/CUDA build (PyTorch 2.11.0, CUDA 12.8).
- The board-sampling seed (2026) only selects *which* 2,000 test samples are streamed
  to the FPGA; it does not affect training. The selected identifiers are frozen in
  `board_manifest.csv` so the on-board experiment is reproducible regardless of seed.
