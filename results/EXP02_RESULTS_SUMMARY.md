# EXP-02 Results Summary: MLP vs Decision Tree in Learn++ (MNIST Digits)

**Date**: 2026-06-24  
**Dataset**: MNIST Digits (70,000 images, 10 classes)  
**Preprocessing**: PCA(n_components=50) — 82.5% variance retained  
**Protocol**: Cumulative (Set 2) introducing classes in the same order as in the article (4 sequential batches based on `batch_classes_dist`)

- **Batch 1**: Classes `[0, 2, 4, 6, 8]`
- **Batch 2**: Classes `[0, 2, 4, 6, 1, 3, 5, 9]`
- **Batch 3**: Classes `[0, 2, 4, 1, 3, 5, 9, 7]`
- **Batch 4**: Classes `[0, 2, 6, 8, 3, 5, 9, 7]`  
  **Seeds**: 5 (42, 123, 456, 789, 1024)  
  **T_k**: 10 hypotheses per batch (total ensemble size = 40 hypotheses)  
  **Total runtime**: ~1 min (MLP ~8.2s/seed, DT depth=5 ~1.0s/seed, DT depth=300 ~1.2s/seed)

---

## Raw Results (per seed)

### MLP (Polikar et al., 2001 — original paper)

| Seed | Final Acc | Final F1 (Macro) | Final BalAcc | Train Time |
| ---- | --------- | ---------------- | ------------ | ---------- |
| 42   | 89.13%    | 0.8898           | 0.8900       | 8.3s       |
| 123  | 88.46%    | 0.8833           | 0.8837       | 8.1s       |
| 456  | 88.78%    | 0.8861           | 0.8865       | 8.2s       |
| 789  | 88.12%    | 0.8803           | 0.8804       | 8.2s       |
| 1024 | 88.75%    | 0.8862           | 0.8864       | 8.2s       |

### Decision Tree (depth=5 — recommended baseline)

| Seed | Final Acc | Final F1 (Macro) | Final BalAcc | Train Time |
| ---- | --------- | ---------------- | ------------ | ---------- |
| 42   | 70.52%    | 0.7016           | 0.7026       | 1.0s       |
| 123  | 74.10%    | 0.7387           | 0.7369       | 1.0s       |
| 456  | 73.55%    | 0.7323           | 0.7323       | 1.0s       |
| 789  | 72.61%    | 0.7254           | 0.7232       | 1.0s       |
| 1024 | 72.53%    | 0.7185           | 0.7201       | 1.0s       |

### Decision Tree Stronger (depth=300 — high-capacity variant)

| Seed | Final Acc | Final F1 (Macro) | Final BalAcc | Train Time |
| ---- | --------- | ---------------- | ------------ | ---------- |
| 42   | 79.93%    | 0.7959           | 0.7961       | 1.2s       |
| 123  | 80.34%    | 0.7995           | 0.8007       | 1.2s       |
| 456  | 79.70%    | 0.7939           | 0.7944       | 1.2s       |
| 789  | 79.31%    | 0.7895           | 0.7904       | 1.2s       |
| 1024 | 80.28%    | 0.7992           | 0.7999       | 1.1s       |

---

## CompositeScore (seed=42)

Formula: `0.40·MacroF1 + 0.15·BalancedAcc + 0.15·(1-TrainTimeNorm) + 0.15·(1-InfTimeNorm) + 0.15·(1-MemoryNorm)`

_Note: Normalization scales from min (0.0) to max (1.0) across the evaluated models. High normalized values represent long runtimes or large memory footprints, meaning `1 - Norm` rewards efficient models._

| Metric              | MLP (paper) | DT (depth=5) | DT Stronger (depth=300) |
| ------------------- | ----------- | ------------ | ----------------------- |
| MacroF1             | 0.8898      | 0.7016       | 0.7959                  |
| BalancedAcc         | 0.8900      | 0.7026       | 0.7961                  |
| TrainTimeNorm       | 1.0000      | 0.0000       | 0.0180                  |
| InferenceTimeNorm   | 1.0000      | 0.0000       | 0.0854                  |
| MemoryNorm          | 0.0000      | 0.0000       | 0.0000                  |
| **COMPOSITE SCORE** | **0.6394**  | **0.8360**   | **0.8722** ← Winner     |

- **Total train time**: MLP = 8.3s, DT = 1.0s, DT Stronger = 1.2s (DT depth=5 is **8.3× faster** than MLP, DT depth=300 is **6.9× faster**)
- **Total inference time**: MLP = 1.6s, DT = 1.5s, DT Stronger = 1.5s
- **Memory (ensemble size)**: All = 40 hypotheses (identical, T_k=10 × 4 batches)

---

## Statistical Significance (Wilcoxon Signed-Rank)

- **Paired observations**: 20 (5 seeds × 4 batches)
- **MLP vs. DT (depth=5)**:
  - MLP mean F1 (all batches): 0.6487 ± 0.2109
  - DT mean F1 (all batches): 0.5092 ± 0.1752
  - Wilcoxon statistic: 0.0
  - p-value: $1.907 \times 10^{-6}$
  - **Result**: MLP performs significantly better than DT depth=5 (p < 0.05)
- **MLP vs. DT Stronger (depth=300)**:
  - MLP mean F1 (all batches): 0.6487 ± 0.2109
  - DT Stronger mean F1 (all batches): 0.5912 ± 0.1924
  - Wilcoxon statistic: 0.0
  - p-value: $1.907 \times 10^{-6}$
  - **Result**: MLP performs significantly better than DT depth=300 (p < 0.05)

---

## Interpretation

### 1. MLP remains the gold standard for image recognition

- **Final F1**: MLP 0.880–0.890 vs. DT depth=5 of 0.702–0.739 and DT Stronger of 0.789–0.800.
- **Statistical significance**: $p \approx 1.91 \times 10^{-6}$ highlights MLP's persistent, robust quality advantage on raw pixel visual feature distributions.
- **Protocol shift impact**: Previously, scores were restricted by disjoint/refresh class scenarios (~0.63 F1 for MLP, ~0.30 F1 for DT). Moving to the **Cumulative (Set 2)** scenario allows the models to properly accumulate class structures sequentially, bringing the MLP close to ~89% F1 at final evaluation.

### 2. High-capacity Decision Trees are highly competitive

- Restricting tree depth to 5 limits the ensemble too heavily, capping the final F1 score at ~72.3% on average.
- Overriding `max_depth` to 300 allows trees to create extremely intricate division boundaries, raising the final F1 to **~79.6%** on average (+7.3 percentage points) while keeping the training time almost identical (~1.2s vs. 1.0s).

### 3. Why DT Stronger Wins the Composite Score in EXP-02

- In **EXP-01 (Fashion-MNIST)**, the simpler DT (depth=5) won the Composite Score over DT Stronger because the training time penalty for DT Stronger was slightly more pronounced on the larger, more complex fashion categories.
- In **EXP-02 (MNIST Digits)**, however, the overall training times are incredibly short (MLP takes only ~8.2s, while DT variants take ~1.0–1.2s).
- Because these absolute training times are so small, the normalized penalty for DT Stronger is negligible (only 0.0180). Thus, the **quality advantage** of DT Stronger (F1 of ~79.6% vs. DT depth=5 of ~72.3%) completely dominates the Composite Score formula, crowning **DT Stronger as the optimal choice** (Score = 0.8722).

---

## Key Findings for Report

1. **MLP holds its crown on quality**: Outperforms standard DT by ~16.5 pp and DT Stronger by ~9.0 pp with highly statistically significant confidence ($p < 0.0001$).
2. **DT Stronger is exceptionally cost-effective**: By allowing deeper trees, we get an ~80% F1 classifier that trains in only **1.2 seconds** (over 6.9× faster than MLP), representing a highly practical sweet spot.
3. **The Composite Score winner flips**: Because absolute training time is tiny on MNIST, the 1.2s training time of DT Stronger incurs almost no cost penalty, letting its 7.3 pp quality gain easily beat DT depth=5 in total score.

---

## Remaining Work

1. **Confusion Matrices Analysis**: Evaluate confusion matrices to identify specific digit confusions (e.g., 3 vs. 5, 4 vs. 9) to see which features each base learner captures best.
2. **Hyperparameter sensitivity**: Analyze the effect of varying the ensemble size per batch $T_k \in \{5, 10, 20\}$.
3. **PCA Ablation**: Investigate how different numbers of PCA components $N\_components \in \{25, 50, 100\}$ impact quality and training speed.
