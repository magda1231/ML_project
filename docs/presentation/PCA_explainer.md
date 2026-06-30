# PCA in Our Learn++ Experiments

## What is PCA?

Principal Component Analysis finds the directions of maximum variance in high-dimensional data and projects it onto a lower-dimensional subspace. Each "principal component" is a linear combination of the original features, ordered by how much variance it captures.

## Why we use it here

**Input**: 28×28 grayscale images = 784 raw pixel features  
**Output**: 50 principal components (PCA(50))

### Problem without PCA

| Issue | MLP impact | DT impact |
|-------|-----------|-----------|
| 784 noisy, correlated features | Slow convergence, convergence warnings, noisy gradients | Axis-aligned splits on individual pixels — meaningless for visual patterns |
| Redundant information | Overfits on weighted subsamples | Wastes splits on uninformative pixels |
| High dimensionality | ~10× slower training per hypothesis | Marginal effect on speed |

### What PCA gives us

1. **Noise removal** — discards the ~600 least-informative directions (mostly pixel noise, background variation)
2. **Decorrelated features** — components are orthogonal, so each one adds independent information
3. **Compact representation** — 50 features instead of 784, with 82–86% of the original variance retained
4. **Faster training** — fewer input dimensions = fewer weights to learn (MLP) or fewer candidate splits to evaluate (DT)

## Variance explained

| Dataset | Components | Variance retained |
|---------|-----------|-------------------|
| Fashion-MNIST | 50 | 86.3% |
| MNIST Digits | 50 | 82.5% |

The remaining ~15% is mostly high-frequency noise and per-pixel variation that doesn't help classification.

## How it interacts with Learn++

PCA is applied **once** to the full training set before batching:

```
Raw data (784 dims) → PCA fit on train → transform train + test → 50 dims
                                              ↓
                            Split into batches D₁, D₂, D₃
                                              ↓
                            Learn++ trains on 50-dim features
```

This is valid because:
- PCA is unsupervised — it doesn't use labels, so no information leakage
- It's fitted on training data only, then applied to test data (no data leakage)
- It's a standard preprocessing step, not a model component

## Impact on results

### MLP
- Without PCA: often fails to converge in 500 iterations on 784 features
- With PCA: converges reliably, achieves 0.43–0.64 F1 depending on dataset

### Decision Tree
- Without PCA: splits on individual pixels (meaningless for images)
- With PCA: splits on meaningful variance directions, but still limited by max_depth=5 (31 possible splits for 10 classes)

## Is it "cheating"?

No. PCA is:
- Unsupervised (no label information used)
- Applied identically to both classifiers (fair comparison)
- A standard step in the original Learn++ literature for image data
- Necessary for MLP to converge within reasonable time on weighted subsamples

The alternative would be raw pixels, which makes MLP unusable and DT equally bad — that comparison teaches us nothing.
