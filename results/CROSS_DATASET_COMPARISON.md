# Cross-Dataset Comparison: EXP-01 vs EXP-02

## Overview

| | EXP-01: Fashion-MNIST | EXP-02: MNIST Digits |
|---|---|---|
| **Task** | Clothing classification | Digit recognition |
| **Difficulty** | Harder (visual similarity between classes) | Easier (distinct digit shapes) |
| **Samples** | 70,000 (28×28) | 70,000 (28×28) |
| **PCA variance** | 86.3% | 82.5% |
| **Batch protocol** | D₁: 0–3, D₂: 4–6, D₃: 7–9 | D₁: 0–3, D₂: 4–6, D₃: 7–9 |

---

## Head-to-Head Results

### Classification Quality (Final F1, 5 seeds)

| Dataset | MLP | DT | MLP/DT ratio |
|---------|-----|----|--------------|
| Fashion-MNIST | 0.43–0.48 | 0.20–0.21 | **~2.2×** |
| MNIST Digits | 0.62–0.64 | 0.30–0.34 | **~2.0×** |

**Observation**: MLP's quality advantage is remarkably stable (~2×) regardless of task difficulty.

### Training Speed

| Dataset | MLP (per seed) | DT (per seed) | DT speedup |
|---------|---------------|---------------|-------------|
| Fashion-MNIST | ~17 min | ~1.5 min | **9×** |
| MNIST Digits | ~95s | ~30s | **3×** |

**Observation**: MLP converges faster on easier data (fewer gradient steps needed), shrinking the speed gap.

### CompositeScore

| Dataset | MLP | DT | Winner |
|---------|-----|----|---------| 
| Fashion-MNIST | **0.54** | 0.43 | **MLP** |
| MNIST Digits | 0.50 | **0.63** | **DT** |

**Observation**: Winner flips depending on cost-quality trade-off.

### Statistical Significance (Wilcoxon)

| Dataset | p-value | Winner (quality) |
|---------|---------|-----------------|
| Fashion-MNIST | 0.0012 | MLP |
| MNIST Digits | 0.0001 | MLP |

**Observation**: MLP always wins statistically on raw quality. No ambiguity.

---

## Key Insights

### 1. Consistent quality ratio
MLP achieves ~2× the F1 of DT on both datasets. This suggests the advantage comes from model capacity, not dataset-specific tuning.

### 2. Variable speed ratio
The speed gap depends on convergence difficulty:
- Fashion-MNIST (harder) → MLP needs more iterations → 9× slower
- MNIST Digits (easier) → MLP converges quickly → only 3× slower

### 3. CompositeScore sensitivity
The cost-adjusted metric is sensitive to the speed ratio:
- When speed gap > ~5×: quality wins → MLP
- When speed gap < ~5×: cost wins → DT

This threshold depends on the CompositeScore weights (40% quality, 30% cost). Different weight schemes would shift the crossover point.

### 4. Task difficulty affects absolute performance, not relative ranking
Both learners improve on easier data (MNIST > Fashion-MNIST), but MLP consistently doubles DT. The algorithm (Learn++) handles both domains equally well.

---

## Implications for Base Learner Selection

| Scenario | Recommendation |
|----------|---------------|
| Quality is paramount | **MLP** — always 2× better |
| Tight compute budget | **DT** — 3–9× faster, acceptable on easy tasks |
| Hard visual tasks | **MLP** — DT F1 drops to ~0.20 (unusable) |
| Easy/structured tasks | **Either** — DT reaches ~0.33 (marginal), MLP ~0.63 |
| Real-time inference | **Either** — inference times are similar (~1–4s) |

---

## Figures

- [exp01_fashion_mnist_comparison.png](exp01_fashion_mnist_comparison.png) — EXP-01 3-panel plot
- [exp01_confusion_matrices.png](exp01_confusion_matrices.png) — EXP-01 confusion matrices
- [exp02_mnist_digits_comparison.png](exp02_mnist_digits_comparison.png) — EXP-02 3-panel plot
- [exp02_mnist_confusion_matrices.png](exp02_mnist_confusion_matrices.png) — EXP-02 confusion matrices
