# EXP-01 Results Summary: MLP vs Decision Tree in Learn++

**Date**: 2026-06-24  
**Dataset**: Fashion-MNIST (70,000 images, 10 classes)  
**Preprocessing**: PCA(n_components=50) — 86.3% variance retained  
**Protocol**: Cumulative (Set 2) introducing classes in the same order as in the article (4 sequential batches based on `batch_classes_dist`)

### Batch Composition Strategies

#### Strategy and distribution as in `research paper`

| Class | Batch 1 | Batch 2 | Batch 3 | Batch 4 | Total Samples |
| ----- | ------- | ------- | ------- | ------- | ------------- |
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

#### Strategy: `cumulative`

| Batch   | Class IDs           |
| ------- | ------------------- |
| Batch 1 | `[0,2,4]`           |
| Batch 2 | `[0,2,6,1,5]`       |
| Batch 3 | `[0,8,4,6,1,3,7,9]` |
| Batch 4 | `[4,6,1,3,5,9,8]`   |

**Outcome**: Introduces heavy overlap; forces model to continuously reconcile old and new boundaries.

#### Strategy: `dist`

| Batch   | Class IDs           |
| ------- | ------------------- |
| Batch 1 | `[0,2,4,6,8]`       |
| Batch 2 | `[0,2,4,6,1,3,5,9]` |
| Batch 3 | `[0,2,4,1,3,5,9,7]` |
| Batch 4 | `[0,2,6,8,3,5,9,7]` |

**Outcome**: Sequential introduction with growing set; best for measuring cumulative learning progress.

#### Strategy: `no_rep`

| Batch   | Class IDs       |
| ------- | --------------- |
| Batch 1 | `[0,2,4,6,8]`   |
| Batch 2 | `[1,3,5]`       |
| Batch 3 | `[7,9]`         |
| Batch 4 | `[0,1,3,4,8,7]` |

**Outcome**: Disjoint introduction; highlights forgetting phenomena in early stages of ensemble growth.

### EXP-01 Results (Fashion-MNIST + PCA(50), 5 seeds)

**Setup**: $T_k=10$ hypotheses per batch, 5 random seeds.

| Metric                        | MLP (paper)     | Decision Tree (depth=5) | Decision Tree Stronger (depth=300) | Winner            |
| ----------------------------- | --------------- | ----------------------- | ---------------------------------- | ----------------- |
| **Final Accuracy**            | 87.10% – 87.54% | 73.08% – 74.28%         | 82.60% – 82.92%                    | **MLP**           |
| **Final F1 (Macro)**          | 87.03% – 87.47% | 73.38% – 74.67%         | 82.45% – 82.78%                    | **MLP**           |
| **Final BalAcc**              | 87.10% – 87.54% | 73.08% – 74.28%         | 82.60% – 82.92%                    | **MLP**           |
| **Total Training Time**       | ~405s – 601s    | **~35s**                | ~55s                               | **DT (depth=5)**  |
| **CompositeScore** (Seed 42)  | 0.6308          | **0.8563**              | 0.8531                             | **DT (depth=5)**  |
| **Per-seed consistency (vs MLP)** | —           | MLP wins 5/5 seeds      | MLP wins 5/5 seeds                 | **MLP (Quality)** |

### Interpretation & Deep Dive

- **Classification Quality**: The MLP (the original paper's classifier) remains the superior model in terms of accuracy and F1 score, outperforming the basic Decision Tree by ~13.1 percentage points and the Stronger Decision Tree by ~4.7 percentage points.
- **Tree Depth Impact**: Restricting tree depth to 5 (the recommended setting for medical/tabular data) is insufficient for image features even after PCA. However, increasing `max_depth` to 300 dramatically bridges the quality gap, improving the F1 score from ~74.2% to ~82.6%, while only increasing the training time slightly (from ~35s to ~55s).
- **The Quality-Cost Trade-Off**: Although MLP delivers the best classification performance, it is computationally expensive (taking up to 11–17× longer to train than the decision trees). Consequently, under the balanced **CompositeScore** formula (which weights F1 at 40%, Balanced Accuracy at 15%, and penalizes training time, inference time, and memory footprint at 15% each), the **Decision Tree** wins (0.8563 vs 0.6308 for MLP).
- **On statistical testing**: We deliberately do **not** report a pooled Wilcoxon p-value over the $5\text{ seeds} \times 4\text{ batches} = 20$ scores. That pooling violates the test's independence assumption (cumulative batches are repeated measures on a growing ensemble; all seeds reuse the same batch partition; a single fixed test set is reused). Instead we report the **effect size** (MLP leads by ~13 F1 points over DT and ~5 over DT-Strong) and **direction consistency** — MLP wins in **5/5** seeds and at every batch step.

### Quality Advantage: Effect Size & Consistency

Comparison on the independent unit (final-batch F1 per seed, 5 replicates) rather than a pooled hypothesis test (see note above on why pooling violates independence):

- **MLP vs. DT (depth=5)**: MLP wins 5/5 seeds, ~+13 F1 points
- **MLP vs. DT Stronger (depth=300)**: MLP wins 5/5 seeds, ~+5 F1 points

### Key questions answered:

- ✅ **Does MLP hold up as a superior learner for image data?** → Yes, strongly (F1 of ~87.3% vs DT's ~74.2%), winning on every seed and batch.
- ✅ **Is DT a viable alternative?** → Purely in terms of accuracy/F1 on image features, even a stronger DT lags behind MLP. However, for applications where resource constraints or deployment costs are critical, DT is extremely competitive.
- ✅ **Quality-cost trade-off?** → If computation is penalized, the basic Decision Tree wins the CompositeScore (0.8563 vs MLP's 0.6308) due to being 12× faster to train.
- ✅ **Is the quality gap reliable?** → Yes by effect size and consistency (MLP wins 5/5 seeds). We avoid a formal hypothesis test because the nested/cumulative design and shared data partition violate its independence assumption.

### Remaining work:

1. **EXP-02**: Run on BraTS MRI (ROI features) — Decision Trees and stronger variants are expected to perform exceptionally well on structured tabular medical features, potentially closing the quality gap while maintaining their computational advantage.
2. **Confusion Matrices Analysis**: Evaluate confusion matrices to identify specific class confusions (e.g., T-shirt vs Shirt, Pullover vs Coat) to see which features each base learner captures best.
3. **Hyperparameter sensitivity**: Analyze the effect of varying the ensemble size per batch $T_k \in \{5, 10, 20\}$.
4. **PCA Ablation**: Investigate how different numbers of PCA components $N\_components \in \{25, 50, 100\}$ impact quality and training speed.
