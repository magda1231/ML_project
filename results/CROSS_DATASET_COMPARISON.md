# Cross-Dataset Comparison: EXP-01 vs EXP-02

## Overview

|                    | EXP-01: Fashion-MNIST                      | EXP-02: MNIST Digits                      |
| ------------------ | ------------------------------------------ | ----------------------------------------- |
| **Task**           | Clothing classification                    | Digit recognition                         |
| **Difficulty**     | Harder (visual similarity between classes) | Easier (distinct digit shapes)            |
| **Samples**        | 70,000 (28×28)                             | 70,000 (28×28)                            |
| **PCA variance**   | 86.3%                                      | 82.5%                                     |
| **Batch protocol** | Cumulative (Set 2) — 4 sequential batches  | Cumulative (Set 2) — 4 sequential batches |

---

## Head-to-Head Results

### Classification Quality (Final F1, 5 seeds)

| Dataset           | MLP (paper) | DT (depth=5) | DT Stronger (depth=300) | MLP/DT-5 Ratio | MLP/DT-300 Ratio |
| ----------------- | ----------- | ------------ | ----------------------- | -------------- | ---------------- |
| **Fashion-MNIST** | 0.870–0.875 | 0.733–0.747  | 0.821–0.827             | **~1.18×**     | **~1.06×**       |
| **MNIST Digits**  | 0.880–0.890 | 0.702–0.739  | 0.789–0.800             | **~1.23×**     | **~1.11×**       |

**Observation**: Under the proper Cumulative protocol, the previous 2× quality gap is heavily debunked.

- MLP's quality advantage is actually much narrower: **~6% better** on Fashion-MNIST and **~11% better** on MNIST Digits compared to the high-capacity DT Stronger.
- This proves that with sufficient tree depth (unconstrained capacity), Decision Trees are highly capable of representing visual concepts.

---

### Training Speed (per seed)

| Dataset           | MLP       | DT (depth=5) | DT Stronger (depth=300) | DT-5 Speedup | DT-300 Speedup |
| ----------------- | --------- | ------------ | ----------------------- | ------------ | -------------- |
| **Fashion-MNIST** | ~405–601s | ~35s         | ~54s                    | **12.2×**    | **7.9×**       |
| **MNIST Digits**  | ~8.2s     | ~1.0s        | ~1.2s                   | **8.3×**     | **6.9×**       |

**Observation**: MLP is computationally heavy, especially on larger/more complex datasets. Decision Trees are consistently **7–12× faster** to train, representing a massive computational advantage.

---

### CompositeScore (Seed 42)

Formula: `0.40·MacroF1 + 0.15·BalancedAcc + 0.15·(1-TrainTimeNorm) + 0.15·(1-InfTimeNorm) + 0.15·(1-MemoryNorm)`

| Dataset           | MLP (paper) | DT (depth=5) | DT Stronger (depth=300) | Winner                      |
| ----------------- | ----------- | ------------ | ----------------------- | --------------------------- |
| **Fashion-MNIST** | 0.6308      | **0.8563**   | 0.8531                  | **DT (depth=5)**            |
| **MNIST Digits**  | 0.6394      | 0.8360       | **0.8722**              | **DT Stronger (depth=300)** |

**Observation**: The cost-accuracy trade-off flips the optimal base learner depending on dataset characteristics:

- **Fashion-MNIST (Larger/Harder)**: The training time penalty for DT Stronger is slightly larger, making the basic DT (depth=5) the overall CompositeScore winner.
- **MNIST Digits (Faster/Easier)**: Because absolute training times are incredibly short overall (1s to 1.2s), DT Stronger's time penalty is negligible. Its **+7.3 percentage point F1 advantage** dominates the calculation, crowning **DT Stronger** the winner.

---

### Statistical Significance (Wilcoxon Signed-Rank)

- **Paired observations**: 20 (5 seeds × 4 batches)

| Comparison                          | Fashion-MNIST p-value  | MNIST Digits p-value   | Winner (Quality) |
| ----------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **MLP vs. DT (depth=5)**            | $1.907 \times 10^{-6}$ | $1.907 \times 10^{-6}$ | **MLP**          |
| **MLP vs. DT Stronger (depth=300)** | $1.907 \times 10^{-6}$ | $1.907 \times 10^{-6}$ | **MLP**          |

**Observation**: Despite the narrower margins, MLP's quality advantage remains highly statistically significant ($p < 0.05$) on both datasets across all seeds and batches.

---

## Key Insights

### 1. The Power of Tree Capacity

Limiting tree depth to 5 (standard for medical datasets) is highly limiting for image categories. Elevating the tree depth to 300 (practically unlimited depth) allows the Decision Tree ensemble to build highly precise, nonlinear dividing boundaries. This single adjustment boosts F1 scores by **+7 to +8 percentage points** on both datasets with almost no extra time overhead.

### 2. Diminishing Quality Advantage

When properly evaluated sequentially under the cumulative batching scenario, the MLP's lead over the strongest Decision Tree is only 6–11%. This indicates that while MLP remains superior for visual data (as proposed in Polikar's original 2001 paper), modern high-capacity tree ensembles are an extremely viable alternative.

### 3. Absolute Pacing Affects the Balanced Winner

Under the CompositeScore cost-penalization formula, the winner flips:

- On large visual tasks with non-negligible training times, **DT depth=5** is rewarded for its sheer speed.
- On fast-running or low-compute visual tasks (like digit recognition), the training time penalty is too minor to punish **DT Stronger**, which wins easily due to its superior quality.

---

## Implications for Base Learner Selection

| Scenario                                         | Recommendation              | Rationale                                                                                                       |
| ------------------------------------------------ | --------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Maximal accuracy/quality is paramount**        | **MLP**                     | Always delivers the highest F1 (~87–89%) with full statistical significance.                                    |
| **Extremely tight compute budget**               | **DT (depth=5)**            | Trains in under 1.0–35s (up to 12× faster) with decent quality.                                                 |
| **Low-compute visual tasks (Digit Recognition)** | **DT Stronger (depth=300)** | Trains in 1.2s, reaches ~80% F1, and wins the balanced CompositeScore.                                          |
| **Large-scale, complex visual tasks**            | **MLP or DT Stronger**      | MLP is optimal for top performance; DT Stronger is optimal if MLP's training time (10 min+) is too restrictive. |
| **Real-time inference systems**                  | **Any**                     | Inference times are virtually identical across all models (~1.4–1.7s total).                                    |

---

## Figures and Artifacts

- [exp01_fashion_mnist_comparison.png](exp01_fashion_mnist_comparison.png) — EXP-01 4-panel comparison plot
- [exp01_confusion_matrices.png](exp01_confusion_matrices.png) — EXP-01 Confusion Matrices (per-class breakdown)
- [exp02_mnist_comparison_matrices.png](exp02_mnist_comparison_matrices.png) — EXP-02 4-panel comparison plot
- [exp02_mnist_confusion_matrices.png](exp02_mnist_confusion_matrices.png) — EXP-02 Confusion Matrices (per-class breakdown)
