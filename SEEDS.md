# Random Seeds

All stochastic steps are seeded for full reproducibility.

| Seed | Used for | Where set |
|------|----------|-----------|
| **3407** | FP32 training, QAT fine-tuning, all main results | `set_seed(3407)` for Python / NumPy / PyTorch / CUDA at the top of the training and QAT scripts (released upon acceptance) |
| **2026** | On-board test bookkeeping (full-set run, no subsampling) | board-test script (released upon acceptance); the full-set streamed indices are frozen in the board manifest |
| **{3407, 42, 123, 2024, 7}** | Five-seed residual/Focal-Loss ablation (Table on validation only) | multi-seed ablation script (released upon acceptance) |

## Notes
- The main deployed model (FP32 champion → QAT INT8) uses seed **3407** end-to-end,
  matching Table III of the manuscript ("random seed 3407 for Python, NumPy, PyTorch and CUDA").
- Seed **3407** is fixed for `random`, `numpy`, `torch` (CPU) and `torch.cuda`
  (all GPU streams), plus `cudnn.deterministic = True`, so a re-run reproduces the
  reported weights bit-for-bit on the same PyTorch/CUDA build (PyTorch 2.11.0, CUDA 12.8).
- The on-board experiment streams the **entire** held-out CF_9 test day — all
  **31,828** samples, not a subset — so no random sampling is involved in the reported
  board results. Seed 2026 remains recorded only for provenance/bookkeeping of the
  earlier incremental (50- and 2,000-sample) validation runs that preceded the full-set
  run. The complete set of 31,828 streamed identifiers, their golden outputs, and the
  full board-vs-reference pairing (31,828/31,828 bit-exact) are released upon acceptance.
