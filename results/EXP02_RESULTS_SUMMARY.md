# EXP-02 Results Summary: MLP vs Decision Tree in Learn++ (MNIST Digits)

### EXP01 Reference Data

- **Strategy `no_rep`**: Batch 1:`[0,2,4,6,8]` | Batch 2:`[1,3,5]` | Batch 3:`[7,9]` | Batch 4:`[0,1,3,4,8,7]`
- **Results (MLP, Seed=42)**: `ocr_dist`: 79.1% Acc | 0.794 F1 | 14s; `cumulative`: 76.5% Acc | 0.726 F1 | 376s; `dist`: 87.3% Acc | 0.873 F1 | 424s; `no_rep`: 49.9% Acc | 0.429 F1 | 293s

---

**Date**: 2026-06-25
**Dataset**: MNIST Digits (70,000 images, 10 classes)
**Preprocessing**: PCA(n_components=50) — 82.5% variance retained
**Protocol**: Cumulative (Set 2) introducing classes in the same order as in the article (4 sequential batches based on `batch_classes_dist`)

## 6. Summary & Next Steps

### EXP-02 Results (MNIST Digits + PCA(50), 5 seeds)

**Setup**: MNIST (70k images, 10 classes), PCA to 50 dims, incremental class introduction across 4 sequential batches. $T_k=10$ hypotheses per batch, 5 random seeds.

**Key findings**:

| Metric                        | MLP (paper)     | Decision Tree          | Decision Tree Stronger | Winner            |
| ----------------------------- | --------------- | ---------------------- | ---------------------- | ----------------- |
| **Final Accuracy**            | 88.12% – 89.13% | 70.52% – 74.10%        | 79.31% – 80.34%        | **MLP**           |
| **Final F1 (Macro)**          | 88.03% – 88.98% | 70.16% – 73.87%        | 78.95% – 79.95%        | **MLP**           |
| **Final BalAcc**              | 88.04% – 89.00% | 70.26% – 73.69%        | 79.04% – 80.07%        | **MLP**           |
| **Total Training Time**       | ~8.3s           | **~1.1s**              | ~1.2s                  | **DT**            |
| **CompositeScore** (Seed 42)  | 0.6394          | 0.8360                 | **0.8722**             | **DT Stronger**   |
| **Wilcoxon p-value (vs MLP)** | —               | $1.907 \times 10^{-6}$ | $1.907 \times 10^{-6}$ | **MLP (Quality)** |

**Interpretation & Deep Dive**:

- **Classification Quality**: The MLP (the original paper's classifier) significantly outperforms both Decision Tree variants in accuracy and F1 score, demonstrating the effectiveness of the original design for digit recognition.
- **Tree Depth Impact**: The "Stronger" Decision Tree (depth=300) closes a significant portion of the performance gap compared to the basic DT (depth=5), improving F1 from ~72.4% to ~79.6% with negligible increase in training time (~0.1s difference).
- **The Quality-Cost Trade-Off**: While MLP delivers best classification performance, it is ~7-8× slower to train than DT variants. Consequently, under the balanced **CompositeScore** formula, the **DT Stronger** wins (0.8722 vs 0.6394 for MLP) by effectively balancing predictive performance and computational efficiency.
- **Statistical Significance**: The quality advantage of the MLP over both DT variants is highly statistically significant ($p \approx 1.907 \times 10^{-6}$, Wilcoxon signed-rank test).

### Remaining work:

1. **Confusion Matrices Analysis**: Evaluate specific class confusions to identify where DT variants fail to match MLP performance.
2. **Hyperparameter sensitivity**: Analyze the effect of varying the ensemble size per batch $T_k \in \{5, 10, 20\}$.
3. **PCA Ablation**: Investigate how different numbers of PCA components $N\_components \in \{25, 50, 100\}$ impact quality and training speed.

### Key questions answered:

- ✅ **Does MLP hold up as a superior learner for digit image data?** → Yes, significantly (F1 of ~89.0% vs ~80% for Stronger DT, $p < 0.05$).
- ✅ **Is DT a viable alternative?** → Yes, particularly if computational cost is prioritized, the DT Stronger variant provides highly competitive performance with much lower training overhead.
- ✅ **Quality-cost trade-off?** → If computational efficiency is penalized in the metrics, the **DT Stronger** variant wins the CompositeScore by balancing accuracy and speed.
- ✅ **Statistically significant?** → Yes, the MLP quality advantage is statistically significant ($p \approx 1.91 \times 10^{-6}$, Wilcoxon).

---

### Class Distribution Across Batches

| Class | Batch 1 | Batch 2 | Batch 3 | Batch 4 | Total Samples |
| :---- | :------ | :------ | :------ | :------ | :------------ |
| 0     | 100     | 50      | 50      | 25      | 225           |
| 1     | 0       | 150     | 50      | 0       | 200           |
| 2     | 100     | 50      | 50      | 25      | 225           |
| 3     | 0       | 150     | 50      | 25      | 225           |
| 4     | 100     | 50      | 50      | 0       | 200           |
| 5     | 0       | 150     | 50      | 25      | 225           |
| 6     | 100     | 50      | 0       | 100     | 250           |
| 7     | 0       | 0       | 150     | 50      | 200           |
| 8     | 100     | 0       | 0       | 150     | 250           |
| 9     | 0       | 50      | 100     | 50      | 200           |
