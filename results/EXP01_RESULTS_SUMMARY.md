# EXP-01 Results Summary: MLP vs Decision Tree in Learn++

**Date**: 2026-05-31  
**Dataset**: Fashion-MNIST (70,000 images, 10 classes)  
**Preprocessing**: PCA(n_components=50) — 86.3% variance retained  
**Protocol**: Incremental class introduction — D1(classes 0–3), D2(classes 4–6), D3(classes 7–9)  
**Seeds**: 5 (42, 123, 456, 789, 1024)  
**T_k**: 10 hypotheses per batch  
**Total runtime**: ~100 min (MLP ~17 min/seed, DT ~1.5 min/seed)

---

## Raw Results (per seed)

### MLP (Polikar et al., 2001 — original paper)
| Seed | Final F1 | Final BalAcc | Train Time |
|------|----------|--------------|------------|
| 42   | 0.4338   | 0.4694       | 854.1s     |
| 123  | 0.4389   | 0.4760       | 1112.7s    |
| 456  | 0.4362   | 0.4721       | 1113.7s    |
| 789  | 0.4820   | 0.5100       | 1132.2s    |
| 1024 | 0.4672   | 0.4954       | 1260.1s    |

### Decision Tree (Learn++.NSE, Elwell & Polikar 2011)
| Seed | Final F1 | Final BalAcc | Train Time |
|------|----------|--------------|------------|
| 42   | 0.2130   | 0.3083       | 97.7s      |
| 123  | 0.2057   | 0.3053       | 94.3s      |
| 456  | 0.1993   | 0.3017       | 97.1s      |
| 789  | 0.1963   | 0.3005       | 96.1s      |
| 1024 | 0.2031   | 0.3039       | 94.4s      |

---

## CompositeScore (seed=42)

Formula: `0.40·MacroF1 + 0.15·BalancedAcc + 0.15·(1-TrainTimeNorm) + 0.15·(1-InfTimeNorm) + 0.15·(1-MemoryNorm)`

| Metric             | MLP (paper) | DT (recommended) |
|--------------------|-------------|------------------|
| MacroF1            | 0.4338      | 0.2130           |
| BalancedAcc        | 0.4694      | 0.3083           |
| TrainTimeNorm      | 1.0000      | 0.0000           |
| InferenceTimeNorm  | 0.0000      | 1.0000           |
| MemoryNorm         | 0.0000      | 0.0000           |
| **COMPOSITE SCORE**| **0.5439**  | **0.4314** ← MLP wins |

- Total train time: MLP=854.1s (~14 min), DT=97.7s (~1.6 min) → **9× faster**
- Total inference time: MLP=3.8s, DT=4.0s → similar
- Memory (ensemble size): Both = 30 hypotheses (identical, T_k=10 × 3 batches)

---

## Statistical Significance (Wilcoxon Signed-Rank)

- Paired observations: 15 (5 seeds × 3 batches)
- MLP mean F1 (all batches): 0.3699 ± 0.0908
- DT mean F1 (all batches): 0.2055 ± 0.0313
- **Wilcoxon statistic**: 7.0
- **p-value**: 0.0012
- **Result**: MLP significantly better across all batch-level observations (p < 0.05)

---

## Interpretation

### MLP clearly dominates with PCA preprocessing
- **Final F1**: MLP 0.43–0.48 vs DT 0.20–0.21 — **more than 2× the quality**
- **CompositeScore**: MLP wins (0.54 vs 0.43) — quality advantage outweighs 9× speed penalty
- **Statistical significance**: p=0.0012, no ambiguity

### Why does PCA help MLP so much?
1. **Noise reduction**: Raw 784 pixels contain redundant/noisy features. PCA extracts the 50 most informative directions.
2. **Faster convergence**: 50 inputs vs 784 means MLP converges within 500 iterations (previously often didn't converge)
3. **Better generalization**: Fewer parameters to overfit on each weighted subsample

### Why does DT still fail with PCA?
- DT with max_depth=5 has only 31 axis-aligned splits
- PCA components are linear combinations of pixels — still requires nonlinear boundaries to separate fashion categories
- The fundamental limitation is capacity, not dimensionality

### Why are DT's F1 scores ~0.20?
This is expected for incremental class introduction:
- After D1: test set has 10 classes but model only knows 4 → max possible recall ≤ 40%
- After D3: full knowledge, but ensemble is diluted with old single-class hypotheses
- DT's limited capacity means it can't recover even with full class exposure

### Comparison to raw-pixel baseline (no PCA)
| Metric | MLP (raw 784d) | MLP (PCA 50d) | Improvement |
|--------|---------------|---------------|-------------|
| Final F1 | 0.22 | 0.45 | **+100%** |
| Train time/seed | ~2500s | ~1000s | **2.5× faster** |
| CompositeScore winner | DT | **MLP** | Flipped |

---

## Key Findings for Report

1. **MLP strongly outperforms DT** on image data (2× F1, p=0.001) — validates original paper's choice
2. **MLP wins CompositeScore** (0.54 vs 0.43) — quality gain > speed penalty
3. **PCA is essential**: doubles MLP accuracy while halving training time (noise removal + faster convergence)
4. **DT is fundamentally unsuited** for image features regardless of dimensionality (capacity-limited)
5. **Memory is identical** — both produce 30 hypotheses (T_k=10 × 3 batches)

---

## Remaining Work
1. **EXP-02**: BraTS MRI data (tabular ROI features — DT expected to perform better here)
2. **Confusion matrices**: Per-class breakdown to understand which classes MLP handles best
3. **Hyperparameter sensitivity**: Vary T_k = {5, 10, 20}
4. **PCA ablation**: Compare n_components = {25, 50, 100}
