# EXP-02 Results Summary: MLP vs Decision Tree in Learn++ (MNIST Digits)

**Date**: 2026-06-01  
**Dataset**: MNIST Digits (70,000 images, 10 classes)  
**Preprocessing**: PCA(n_components=50) — 82.5% variance retained  
**Protocol**: Incremental class introduction — D₁(classes 0–3), D₂(classes 4–6), D₃(classes 7–9)  
**Seeds**: 5 (42, 123, 456, 789, 1024)  
**T_k**: 10 hypotheses per batch  
**Total runtime**: ~10 min (MLP ~95s/seed, DT ~30s/seed)

---

## Raw Results (per seed)

### MLP (Polikar et al., 2001 — original paper)
| Seed | Final F1 | Final BalAcc | Train Time |
|------|----------|--------------|------------|
| 42   | 0.6189   | 0.6534       | 91.6s      |
| 123  | 0.6295   | 0.6618       | 94.2s      |
| 456  | 0.6341   | 0.6701       | 96.8s      |
| 789  | 0.6417   | 0.6732       | 99.7s      |
| 1024 | 0.6302   | 0.6645       | 95.1s      |

### Decision Tree (Learn++.NSE, Elwell & Polikar 2011)
| Seed | Final F1 | Final BalAcc | Train Time |
|------|----------|--------------|------------|
| 42   | 0.3032   | 0.3615       | 29.9s      |
| 123  | 0.3156   | 0.3721       | 30.2s      |
| 456  | 0.3098   | 0.3689       | 30.1s      |
| 789  | 0.3395   | 0.3934       | 30.5s      |
| 1024 | 0.3201   | 0.3782       | 30.3s      |

---

## CompositeScore (seed=42)

Formula: `0.40·MacroF1 + 0.15·BalancedAcc + 0.15·(1-TrainTimeNorm) + 0.15·(1-InfTimeNorm) + 0.15·(1-MemoryNorm)`

| Metric             | MLP (paper) | DT (recommended) |
|--------------------|-------------|------------------|
| MacroF1            | 0.6189      | 0.3032           |
| BalancedAcc        | 0.6534      | 0.3615           |
| TrainTimeNorm      | 1.0000      | 0.0000           |
| InferenceTimeNorm  | 1.0000      | 0.0000           |
| MemoryNorm         | 0.0000      | 0.0000           |
| **COMPOSITE SCORE**| **0.4991**  | **0.6257** ← DT wins |

- Total train time: MLP=91.6s, DT=29.9s → **3× faster**
- Total inference time: MLP=1.4s, DT=1.2s → similar but MLP slightly slower
- Memory (ensemble size): Both = 30 hypotheses (T_k=10 × 3 batches)

**Why DT wins CompositeScore here**: The quality gap (2×) is the same as Fashion-MNIST, but the speed gap is only 3× (vs 9× in EXP-01). The cost formula normalizes time to [0,1], so MLP gets penalized on both train and inference time, pushing its total score below DT despite much better F1.

---

## Statistical Significance (Wilcoxon Signed-Rank)

- Paired observations: 15 (5 seeds × 3 batches)
- MLP mean F1 (all batches): 0.4628 ± 0.1604
- DT mean F1 (all batches): 0.2732 ± 0.0356
- **Wilcoxon statistic**: 0.0
- **p-value**: 0.0001
- **Result**: MLP significantly better across all batch-level observations (p < 0.05)

---

## Interpretation

### MLP dominates on quality, DT wins on cost
- **Final F1**: MLP 0.62–0.64 vs DT 0.30–0.34 — **2× quality advantage**
- **CompositeScore**: DT wins (0.63 vs 0.50) — cost normalization flips the winner
- **Statistical significance**: p=0.0001, unambiguous quality difference

### Comparison to EXP-01 (Fashion-MNIST)
- Both datasets show ~2× MLP quality advantage
- MNIST digits are easier (higher absolute F1 for both learners)
- Speed gap shrinks from 9× to 3× → flips CompositeScore winner
- Validates the original paper: MLP works well on its "home turf" (digit recognition)
