# Presentation Draft: Learn++ Base Learner Comparison
## ML: Learning, Adaptation, and Uncertainty 2026
### ~10–15 minutes

---

## Slide 1: Title

**Comparing Base Learners in Learn++: MLP vs Decision Tree**

- Course: ML — Learning, Adaptation, and Uncertainty 2026
- Authors: [names]
- Date: June 2026

> **Speaker notes**: Brief greeting, introduce the project topic — comparing base learner capacity inside an incremental learning algorithm.

---

## Slide 2: Motivation & Problem Statement

**Why incremental learning?**

- Real-world data arrives in batches (sensors, clinical trials, production streams)
- Classical ML assumes all data available at once → retrain from scratch is expensive
- Goal: learn new classes without forgetting old ones ("stability–plasticity dilemma")

**Our question**: Does base learner complexity matter inside Learn++?

> **Speaker notes**: Frame the problem — traditional ML can't handle streaming/sequential data efficiently. Incremental learning solves this. But the choice of base classifier inside the framework is understudied.

---

## Slide 3: Learn++ Algorithm Overview

**Learn++ (Polikar et al., 2001)**

- Ensemble-based incremental learning
- Inspired by AdaBoost: weighted training, weighted voting
- Key property: **never discards old hypotheses** — only adds new ones

**Per-batch process (for each D_k):**
1. Draw weighted subsample from D_k
2. Train T_k base learners on weighted subsets
3. Compute error, update weights (boost hard examples)
4. Add new hypotheses to ensemble
5. Predict via weighted majority vote across ALL accumulated hypotheses

> **Code reference** (1)(2)(3)
>
> 1. [`01_learnpp_walkthrough.ipynb` L122–275 — full `LearnPP` class](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L122) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 2. [`01_learnpp_walkthrough.ipynb` L151 — `partial_fit()` one batch step](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L151) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 3. [`01_learnpp_walkthrough.ipynb` L241 — `predict()` weighted majority vote](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L241) — `01_learnpp_walkthrough.ipynb` · `a8a3625`

> **Speaker notes**: Explain D_k = data batch, T_k = number of hypotheses per batch (we use 10). The key insight is that old classifiers remain and vote alongside new ones — this is both a strength (no catastrophic forgetting) and a weakness (hypothesis imbalance).

---

## Slide 4: Learn++ — Visual Flow

```
D₁ (classes 0–3) → Train T₁=10 hypotheses → Ensemble: 10 models
D₂ (classes 4–6) → Train T₂=10 hypotheses → Ensemble: 20 models  
D₃ (classes 7–9) → Train T₃=10 hypotheses → Ensemble: 30 models
                                                    ↓
                                        Weighted majority vote
```

**Class introduction protocol:**
- D₁: classes 0–3 (digits/items the system "sees first")
- D₂: classes 4–6 (new classes arrive)
- D₃: classes 7–9 (final classes)

> **Code reference** (1)(2)
>
> 1. [`01_learnpp_walkthrough.ipynb` L463–486 — batch creation (Fashion-MNIST)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L463) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 2. [`02_learnpp_mnist_digits.ipynb` L248–263 — batch creation (MNIST Digits)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/02_learnpp_mnist_digits.ipynb#L248) — `02_learnpp_mnist_digits.ipynb` · `a8a3625`

> **Speaker notes**: Walk through the diagram. Emphasize that after D₃, classes 0–3 are "backed" by 30 hypotheses while classes 7–9 only have 10. This creates a potential imbalance — we'll come back to this.

---

## Slide 5: Base Learners Under Comparison

| | MLP (original paper) | Decision Tree |
|---|---|---|
| **Source** | Polikar et al., 2001 | Learn++.NSE (Elwell & Polikar, 2011) |
| **Capacity** | High (nonlinear, many params) | Low (max_depth=5, 31 splits) |
| **Training speed** | Slow (~95–1100s/seed) | Fast (~30–97s/seed) |
| **Decision boundaries** | Smooth, nonlinear | Axis-aligned, piecewise |

**Hypothesis**: MLP should dominate on quality; DT should be faster. But which wins overall?

> **Code reference** (1)(2)
>
> 1. [`01_learnpp_walkthrough.ipynb` L73–106 — `make_paper_learner()` and `make_recommended_learner()`](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L73) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 2. [`02_learnpp_mnist_digits.ipynb` L32–47 — base learner factories (MNIST)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/02_learnpp_mnist_digits.ipynb#L32) — `02_learnpp_mnist_digits.ipynb` · `a8a3625`

> **Speaker notes**: Explain why these two — they represent opposite ends of the complexity spectrum within the Learn++ literature. MLP = original paper, DT = later medical imaging variant.

---

## Slide 6: Experimental Setup

**Datasets:**
- **Fashion-MNIST** (70k images, 10 classes) — harder visual recognition
- **MNIST Digits** (70k images, 10 classes) — original paper's domain

**Preprocessing:** PCA(50 components) — retains 82–86% variance, reduces noise

**Protocol:**
- 3 batches: incremental class introduction
- T_k = 10 hypotheses per batch
- 5 random seeds (reproducibility)
- Metrics: Macro F1, Balanced Accuracy, training time

> **Code reference** (1)(2)(3)(4)
>
> 1. [`01_learnpp_walkthrough.ipynb` L349–396 — Fashion-MNIST data loading](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L349) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 2. [`01_learnpp_walkthrough.ipynb` L438–460 — `PCA(n_components=50)` fitting (Fashion-MNIST)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L438) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 3. [`02_learnpp_mnist_digits.ipynb` L149–196 — MNIST Digits data loading](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/02_learnpp_mnist_digits.ipynb#L149) — `02_learnpp_mnist_digits.ipynb` · `a8a3625`
> 4. [`02_learnpp_mnist_digits.ipynb` L236–245 — `PCA(n_components=50)` fitting (MNIST Digits)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/02_learnpp_mnist_digits.ipynb#L236) — `02_learnpp_mnist_digits.ipynb` · `a8a3625`

> **Speaker notes**: PCA is justified — raw 784 pixels contain redundant features. 50 components capture the essential structure. Both datasets are standard benchmarks.

---

## Slide 7: Results — Fashion-MNIST (EXP-01)

| Metric | MLP | Decision Tree |
|--------|-----|---------------|
| Final F1 | 0.43–0.48 | 0.20–0.21 |
| Final BalAcc | 0.47–0.51 | 0.30–0.31 |
| Training time | ~17 min/seed | ~1.5 min/seed |
| **MLP advantage** | **2.2×** quality | DT **9× faster** |

**Wilcoxon signed-rank**: p = 0.0012 → MLP significantly better

*[Show: 3-panel comparison plot + confusion matrices]*

> **Code reference** (1)(2)(3)(4)
>
> 1. [`01_learnpp_walkthrough.ipynb` L559–593 — multi-seed experiment run](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L559) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 2. [`01_learnpp_walkthrough.ipynb` L604–646 — comparison plot → `exp01_fashion_mnist_comparison.png`](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L604) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 3. [`01_learnpp_walkthrough.ipynb` L747–789 — confusion matrices → `exp01_confusion_matrices.png`](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L747) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 4. [`EXP01_RESULTS_SUMMARY.md`](https://github.com/magda1231/ML_project/blob/a8a3625/results/EXP01_RESULTS_SUMMARY.md) — `results/EXP01_RESULTS_SUMMARY.md` · `a8a3625`

> **Speaker notes**: MLP clearly dominates on quality — more than double the F1. DT is much faster but the accuracy is too low to be useful. Statistical test confirms this isn't random variation.

---

## Slide 8: Results — MNIST Digits (EXP-02)

| Metric | MLP | Decision Tree |
|--------|-----|---------------|
| Final F1 | 0.62–0.64 | 0.30–0.34 |
| Final BalAcc | 0.65–0.67 | 0.36–0.39 |
| Training time | ~95s/seed | ~30s/seed |
| **MLP advantage** | **2.0×** quality | DT **3× faster** |

**Wilcoxon signed-rank**: p = 0.0001 → MLP significantly better

Higher absolute scores than Fashion-MNIST → confirms MNIST digits are "easier"

> **Code reference** (1)(2)(3)(4)
>
> 1. [`02_learnpp_mnist_digits.ipynb` L301–329 — multi-seed experiment run](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/02_learnpp_mnist_digits.ipynb#L301) — `02_learnpp_mnist_digits.ipynb` · `a8a3625`
> 2. [`02_learnpp_mnist_digits.ipynb` L332–368 — comparison plot → `exp02_mnist_digits_comparison.png`](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/02_learnpp_mnist_digits.ipynb#L332) — `02_learnpp_mnist_digits.ipynb` · `a8a3625`
> 3. [`02_learnpp_mnist_digits.ipynb` L438–472 — confusion matrices → `exp02_mnist_confusion_matrices.png`](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/02_learnpp_mnist_digits.ipynb#L438) — `02_learnpp_mnist_digits.ipynb` · `a8a3625`
> 4. [`EXP02_RESULTS_SUMMARY.md`](https://github.com/magda1231/ML_project/blob/a8a3625/results/EXP02_RESULTS_SUMMARY.md) — `results/EXP02_RESULTS_SUMMARY.md` · `a8a3625`

> **Speaker notes**: Same pattern — MLP always wins quality by ~2×. But note training time gap is smaller here (3× vs 9×). This matters for the cost-adjusted score.

---

## Slide 9: CompositeScore — Quality vs Cost Trade-off

**Formula:** `0.40·F1 + 0.15·BalAcc + 0.15·(1-TrainTime) + 0.15·(1-InfTime) + 0.15·(1-Memory)`

| Dataset | MLP Score | DT Score | **Winner** |
|---------|-----------|----------|------------|
| Fashion-MNIST | **0.54** | 0.43 | **MLP** |
| MNIST Digits | 0.50 | **0.63** | **DT** |

**The flip:** MLP wins when quality gap overwhelms cost (Fashion-MNIST). DT wins when it's only slightly worse but much cheaper (MNIST).

> **Code reference** (1)(2)
>
> 1. [`01_learnpp_walkthrough.ipynb` L649–715 — `compute_composite_score()` (EXP-01)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L649) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 2. [`02_learnpp_mnist_digits.ipynb` L371–411 — `compute_composite_score()` (EXP-02)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/02_learnpp_mnist_digits.ipynb#L371) — `02_learnpp_mnist_digits.ipynb` · `a8a3625`

> **Speaker notes**: This is the most interesting finding. The "best" classifier depends on how you weight quality vs efficiency. If you only care about accuracy, MLP always wins. But if training budget matters, DT can win on easier tasks.

---

## Slide 10: Cross-Dataset Comparison

| | Fashion-MNIST | MNIST Digits |
|---|---|---|
| Task difficulty | Harder (visual similarity) | Easier (distinct shapes) |
| MLP Final F1 | 0.43–0.48 | 0.62–0.64 |
| DT Final F1 | 0.20–0.21 | 0.30–0.34 |
| MLP/DT ratio | ~2.2× | ~2.0× |
| Speed gap | 9× slower | 3× slower |
| CompositeScore winner | MLP | DT |

**Key insight**: MLP's quality advantage is consistent (~2×), but whether it "wins" depends on the cost penalty relative to the task difficulty.

> **Code reference** (1)(2)(3)
>
> 1. [`CROSS_DATASET_COMPARISON.md`](https://github.com/magda1231/ML_project/blob/a8a3625/results/CROSS_DATASET_COMPARISON.md) — `results/CROSS_DATASET_COMPARISON.md` · `a8a3625`
> 2. [`01_learnpp_walkthrough.ipynb` L718–744 — Wilcoxon test EXP-01 (p=0.0012)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L718) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 3. [`02_learnpp_mnist_digits.ipynb` L414–435 — Wilcoxon test EXP-02 (p=0.0001)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/02_learnpp_mnist_digits.ipynb#L414) — `02_learnpp_mnist_digits.ipynb` · `a8a3625`

> **Speaker notes**: The ratio is remarkably stable across datasets. MLP always doubles DT's F1. But the speed gap varies — MLP converges faster on easier data (fewer iterations needed), shrinking the cost penalty.

---

## Slide 11: Hypothesis Imbalance — A Learn++ Limitation

**Problem:** After 3 batches, classes 0–3 have 30 "supporting" hypotheses, classes 7–9 only have 10.

**Evidence from confusion matrices:**
- Earlier-introduced classes tend to have higher recall
- Later-introduced classes get fewer votes in ensemble

**Implication:** Learn++ has a structural bias toward early classes — weighted voting doesn't fully compensate for numerical advantage.

> **Code reference** (1)(2)(3)(4)
>
> 1. [`01_learnpp_walkthrough.ipynb` L241–275 — `predict()` voting logic](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L241) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 2. [`notes_31_05_2026_edited.md` — hypothesis imbalance analysis plan (Q6)](https://github.com/magda1231/ML_project/blob/a8a3625/docs/notes/notes_31_05_2026_edited.md) — `docs/notes/notes_31_05_2026_edited.md` · `a8a3625`
> 3. [`exp01_confusion_matrices.png`](https://github.com/magda1231/ML_project/blob/a8a3625/results/exp01_confusion_matrices.png) — `results/exp01_confusion_matrices.png` · `a8a3625`
> 4. [`exp02_mnist_confusion_matrices.png`](https://github.com/magda1231/ML_project/blob/a8a3625/results/exp02_mnist_confusion_matrices.png) — `results/exp02_mnist_confusion_matrices.png` · `a8a3625`

> **Speaker notes**: This is a known limitation. We can verify it by checking per-class recall in our confusion matrices — if classes 0–3 consistently outperform 7–9, and this flips when we reverse batch order, it's a structural issue not a data issue. Proposed future test: reverse batch order experiment.

---

## Slide 12: Why PCA Matters

**Without PCA (raw 784 features):**
- MLP: convergence warnings, slow training, noisy gradients
- DT: axis-aligned splits on individual pixels — poor for images

**With PCA (50 components):**
- MLP: faster convergence, better generalization, 86% variance retained
- DT: still limited by capacity (max_depth=5), but less noisy splits

**Conclusion:** PCA is essential preprocessing for Learn++ on image data — reduces noise, speeds training, and lets base learners focus on meaningful structure.

> **Code reference** (1)(2)(3)
>
> 1. [`01_learnpp_walkthrough.ipynb` L438–460 — `PCA(n_components=50).fit_transform()` (Fashion-MNIST)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/01_learnpp_walkthrough.ipynb#L438) — `01_learnpp_walkthrough.ipynb` · `a8a3625`
> 2. [`02_learnpp_mnist_digits.ipynb` L236–245 — `PCA(n_components=50).fit_transform()` (MNIST Digits)](https://github.com/magda1231/ML_project/blob/a8a3625/notebooks/02_learnpp_mnist_digits.ipynb#L236) — `02_learnpp_mnist_digits.ipynb` · `a8a3625`
> 3. [`PCA_explainer.md` — full PCA rationale](https://github.com/magda1231/ML_project/blob/a8a3625/docs/presentation/PCA_explainer.md) — `docs/presentation/PCA_explainer.md` · `a8a3625`

> **Speaker notes**: This isn't "cheating" — PCA is a standard, well-understood preprocessing step. It doesn't add information, it removes noise and redundancy. Our contribution is showing it's necessary for Learn++ to work well on images.

---

## Slide 13: Limitations & Future Work

**Current limitations:**
- Only 2 base learners compared (could add SVM, Random Forest, etc.)
- Only image classification (not yet medical data)
- CompositeScore weights are somewhat arbitrary (40/15/15/15/15)

**Next steps:**
- BraTS MRI data (medical imaging application) — pending data access
- Hypothesis imbalance test (reverse batch order)
- Per-class analysis (which digits/items are hardest for each learner?)
- Explore T_k sensitivity (what if T_k=5 or T_k=20?)

> **Speaker notes**: Be honest about limitations. The CompositeScore is a proposal, not a standard metric — the exact weights could be debated. Medical imaging (BraTS) is the ultimate goal but blocked on data access.

---

## Slide 14: Conclusions

1. **MLP consistently outperforms DT** on classification quality (~2× F1) across both datasets
2. **Statistical significance confirmed** (Wilcoxon p < 0.005 both experiments)
3. **Cost-adjusted ranking can flip** — DT wins on easier tasks when speed matters
4. **Learn++ works** for incremental class introduction, but has a structural bias toward early classes
5. **PCA preprocessing is essential** for image-based Learn++ experiments

**Take-home:** The original paper's choice of MLP is validated — higher capacity base learners are worth the computational cost for challenging tasks.

> **Speaker notes**: Wrap up with clear, numbered conclusions. Emphasize that the answer isn't simply "MLP is better" — it depends on the cost function. This nuance is the project's main contribution.

---

## Slide 15 (optional): Q&A / Discussion

**Discussion questions to anticipate:**
- "Why not use a deeper network / modern architecture?"
  → Learn++ was designed for weak learners; deep nets would overfit on small weighted subsamples
- "Is the CompositeScore standard?"
  → No, it's our proposed multi-criteria metric. Weights are debatable.
- "Why not just retrain from scratch each batch?"
  → That's the baseline comparison — Learn++ should be better when retraining is expensive
- "What about catastrophic forgetting?"
  → Learn++ avoids it by keeping all hypotheses — but at the cost of growing ensemble size

> **Speaker notes**: Prepare for these likely questions. The key defense: this project compares base learners within a fixed framework, not frameworks against each other.

---

## Appendix: Technical Details (backup slides)

### CompositeScore Formula
```
Score = 0.40 × MacroF1 
      + 0.15 × BalancedAccuracy
      + 0.15 × (1 - TrainTimeNormalized)
      + 0.15 × (1 - InferenceTimeNormalized)  
      + 0.15 × (1 - MemoryNormalized)
```

### Wilcoxon Signed-Rank Test
- Non-parametric test for paired samples
- 15 paired observations (5 seeds × 3 batches)
- Tests whether MLP–DT difference is systematically non-zero

### PCA Configuration
- n_components = 50
- Fashion-MNIST: 86.3% variance explained
- MNIST Digits: 82.5% variance explained
