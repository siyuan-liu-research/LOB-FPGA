# Training and Quantization Hyperparameters

This table lists the exact hyperparameters used for every trained model in the
manuscript, corresponding to Table III. All values are the actual constants used
in the experiments. Across the whole study the random seed is fixed to **3407**,
the Focal-Loss focusing factor to **γ = 2.0**, and the batch size to **512**.
All model-selection decisions are made on the validation set only; the test set
is evaluated exactly once per configuration (see the test-set access audit log in
`../4_dataset_manifest_and_leakage/`).

## Main hyperparameter table

| # | Model / experiment | Features | Optimizer | lr | wd | batch | epochs | patience | scheduler (T_max / η_min) |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Proposed FP32 (deployed backbone) | 40/144 | Adam | 1e-3 | — | 512 | 100 | 10 | Cosine 100 / 1e-5 |
| 2 | Proposed QAT→INT8 (per-tensor input) | 40/144 | Adam | 1e-5 | — | 512 | 15 | none* | Cosine 15 / 1e-6 |
| 3 | **Deployed INT8** (per-channel input, weight-fold) | 144 | Adam | 1e-5 | — | 512 | 30 | none* | Cosine 30 / 1e-6 |
| 4 | PTQ baseline (calibrated on 5000 training-head samples) | 144 | — (no training) | — | — | 512 | — | — | — |
| 5 | Bit-width sweep 16/8/6/4-bit (simulation) | 144 | — (no training) | — | — | 512 | — | — | — |
| 6 | Structure / input / protocol ablation matrix | 40/144 | AdamW | 1e-3 | 1e-4 | 512 | 120 | 20 | Cosine 120 / 1e-5 |
| 7 | DeepLOB baseline (144-adapted) | 144 | Adam | 1e-3 | — | 512 | 100 | 10 | Cosine 100 / 1e-5 |
| 8 | TLOB baseline (dual-attention, 144-adapted) | 144 | Adam | 1e-4 | — | 512 | 100 | 10 | Cosine 100 / 1e-6 |
| 9 | MLP / LSTM baselines | 144 | Adam | 1e-3 | — | 512 | 100 | 10 | Cosine 100 / 1e-5 |

\* The two QAT runs use no early stopping: the per-tensor run keeps the best
validation checkpoint each epoch; the deployed per-channel-input run selects the
best checkpoint every 5 epochs using a faithful integer-forward evaluation on the
validation set.
† PTQ and the bit-width sweep involve no training; the Focal-Loss factor is used
only for the evaluation loss, not for optimization.

## Model-specific hyperparameters

| Model | Structural hyperparameters |
|---|---|
| Proposed LOB_1DCNN | kernel_size = 3 (deployed; the ablation matrix also sweeps 5/7); channels conv1→64→ResBlock(64)→conv3→128; FC 128→64→3 |
| DeepLOB (144-adapted) | temporal conv (4×1)@16 + Inception@32 + LSTM 64 units; the front-end stride convolutions are adapted to the 144-dimensional input |
| TLOB (144-adapted) | hidden = 40, num_layers = 4, heads = 1, seq_len = 10 (128 in the original); BiN replaced by LayerNorm |
| MLP baseline | Flatten(10×144) → 128 → 64 → 3, BN + ReLU + Dropout 0.3 |
| LSTM baseline | LSTM(144→64, 2 layers, dropout 0.3) → FC(64→3) |

## Quantization scheme details

- **Per-tensor-input QAT:** fbgemm backend, `get_default_qat_qconfig('fbgemm')`,
  per-channel symmetric weights + per-tensor asymmetric activations, Conv+BN+ReLU fusion.
- **Deployed per-channel-input QAT:** as above, but the 144 input features use a
  custom per-channel 8-bit fake-quantizer (uint8 [0,255] + zero point, straight-through
  estimator); on hardware this is realized by weight-folding (the per-channel input
  scale s_in is absorbed into the conv1 weights). Internal layers keep per-channel
  weights + per-tensor activations.
- **PTQ:** `get_default_qconfig('fbgemm')` (fully per-tensor); the calibration set is
  the first 5000 training samples; the test set is never used for calibration.

## Two training recipes (as reported in Table III)

**Recipe A — standard training (proposed FP32 and all comparison models, same protocol)**
Adam, lr 1e-3 (TLOB 1e-4), batch 512, 100 epochs, early-stopping patience 10,
CosineAnnealing (η_min 1e-5, TLOB 1e-6), Focal Loss γ = 2, dropout 0.3 (TLOB 0.1), seed 3407.

**Recipe B — QAT fine-tuning (INT8 deployed model)**
Starting from the FP32 144-dimensional champion weights: Adam lr 1e-5, batch 512,
30 epochs (no early stopping; selected on the validation set by a faithful
integer-forward evaluation), CosineAnnealing (η_min 1e-6), per-channel 8-bit input +
per-channel weights + per-tensor activations (fbgemm), weight-folding.

The ablation matrix (AdamW, wd 1e-4, 120 epochs, patience 20) is a separate
feature-and-kernel-size search recipe for the proposed model, kept distinct from the
same-protocol comparison recipe above.
