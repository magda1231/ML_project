# Data Strategy

## Goal

Define a practical data plan for Learn++ experiments using two tracks:
1. One public benchmark dataset — for fast iteration and algorithm validation.
2. One medical imaging dataset — for domain-relevant evaluation.

---

## Track Purposes

### Public Benchmark Track
- Fast sanity checks for experiment flow and metric consistency.
- Target: clear labels, stable splits, fast training, relevant to image classification.

### Medical Imaging Track (NIfTI / BIDS)
- Domain-relevant evaluation aligned with project goals.
- Target: documented labels, usable metadata, legal reuse clarity.

---

## Dataset Selection Criteria

1. Label quality and class balance.
2. License and academic usage permission.
3. Ease of split definition and reproducibility.
4. Compute feasibility under available hardware.
5. Metadata quality for batch creation (site, session, acquisition details).
6. Relevance to the downstream medical imaging task.

---

## Public Benchmark: Candidate Datasets

### Option A: CIFAR-10
- **Source**: https://www.cs.toronto.edu/~kriz/cifar.html
- **License**: Public domain (academic use permitted)
- **Classes**: 10 (airplane, automobile, bird, cat, deer, dog, frog, horse, ship, truck)
- **Samples**: 50k training + 10k test
- **Format**: 32×32 RGB images
- **Pros**: Widely known, balanced classes (5k per class), fast experiments, well-documented
- **Cons**: Very low resolution, may be too easy for complex models, limited visual complexity
- **Batch strategy**: Split by class groups (D₁: classes 0–3, D₂: classes 4–6, D₃: classes 7–9)

### Option B: Fashion-MNIST
- **Source**: https://github.com/zalandoresearch/fashion-mnist
- **License**: MIT License
- **Classes**: 10 (clothing categories)
- **Samples**: 60k training + 10k test
- **Format**: 28×28 grayscale images
- **Pros**: Grayscale (closer to medical images), balanced, fast, harder than MNIST
- **Cons**: Still a toy dataset with limited realism
- **Batch strategy**: Subject-simulated splits with varying class distributions per batch

### **Recommended: Option B (Fashion-MNIST)**

**Rationale**:
- Grayscale format is more representative of medical imaging (MRI, X-ray) than CIFAR-10's RGB.
- Moderate difficulty — complex enough to differentiate base learners, simple enough for fast iteration.
- 10 classes with balanced distribution enable clean incremental batch design.
- MIT license with zero access barriers.
- Well-established benchmark with extensive published baselines for comparison.

**Batch strategy for Fashion-MNIST**:
```
D₁: Classes 0–3 (T-shirt, Trouser, Pullover, Dress)     n ≈ 24,000
D₂: Classes 4–6 (Coat, Sandal, Shirt)                    n ≈ 18,000
D₃: Classes 7–9 (Sneaker, Bag, Ankle boot)               n ≈ 18,000
```
This simulates incremental class introduction — a realistic scenario for Learn++.

---

## Medical Imaging: Candidate Datasets

### Option A: Brain MRI — OASIS-3
- **Source**: https://www.oasis-brains.org/
- **License**: Open access with data use agreement
- **Task**: Alzheimer's diagnosis / cognitive decline classification
- **Format**: NIfTI volumes, BIDS-compatible
- **Modality**: T1-weighted MRI
- **Samples**: ~1000+ subjects with longitudinal sessions
- **Classes**: 2–3 (cognitively normal, MCI, dementia)
- **Pros**: Real clinical data, rich metadata, BIDS format, longitudinal sessions (natural incremental learning), well-documented
- **Cons**: Large file sizes, requires preprocessing (skull stripping, normalization), class imbalance typical, data use agreement required
- **Batch strategy**:
  - D₁: Baseline scans from Site A (~300 subjects)
  - D₂: Baseline scans from Site B (~300 subjects, scanner shift)
  - D₃: Follow-up scans from both sites (~400 subjects, temporal shift)

### Option B: Chest X-ray — NIH ChestX-ray14
- **Source**: https://nihcc.app.box.com/v/ChestXray-NIHCC
- **License**: Public domain (CC0)
- **Task**: Multi-label disease classification (simplifiable to binary/multi-class)
- **Format**: PNG images (convertible to NIfTI)
- **Samples**: ~112k images from ~30k patients
- **Classes**: 14 diseases + normal
- **Pros**: Large dataset, no registration required, clinically relevant
- **Cons**: Not in NIfTI/BIDS natively, multi-label complexity, documented label noise, 2D only
- **Batch strategy**:
  - D₁: Normal vs pneumonia (binary)
  - D₂: Add cardiomegaly class
  - D₃: Add effusion class

### Option C: Brain Tumor — BraTS
- **Source**: https://www.med.upenn.edu/cbica/brats2020/data.html
- **License**: Registration required, academic use permitted
- **Task**: Tumor type classification (simplified from segmentation)
- **Format**: NIfTI volumes, multi-sequence MRI
- **Samples**: ~300+ subjects per year
- **Classes**: 3–4 (glioblastoma, low-grade glioma, normal)
- **Pros**: NIfTI native, standard benchmark, multi-modal
- **Cons**: Registration required, originally segmentation-focused, smaller dataset
- **Batch strategy**:
  - D₁: T1-weighted only, all classes
  - D₂: T1 + T2, all classes
  - D₃: All modalities

### **Recommended: Option A (OASIS-3)**

**Rationale**:
- True BIDS format aligns with project priority.
- Longitudinal sessions provide natural incremental learning batches.
- Rich metadata enables site-based and time-based batch design.
- Manageable sample size with real clinical relevance.
- Supports both feature extraction approaches (radiomics for simple learners, deep features for complex learners).

**Backup**: Option B (ChestX-ray14) if OASIS access is delayed or storage constraints prohibit 3D volumes.

---

## Recommended Dataset Configuration

| Track | Dataset | Classes | Samples | Format | Access |
|-------|---------|---------|---------|--------|--------|
| **Public** | Fashion-MNIST | 10 clothing categories | 70k | 28×28 grayscale | Direct download, no registration |
| **Medical** | OASIS-3 MRI | 2–3 cognitive states | ~1000+ subjects | NIfTI / BIDS | Data use agreement (~1–3 days) |

---

## Data Contract

Use these fields in a shared data index file:

| Field | Description | Example |
|-------|-------------|---------|
| sample_id | Unique identifier | FMNIST_00001, OASIS_001 |
| dataset_name | Dataset identifier | fashion_mnist, oasis3_mri |
| source_track | Track category | public, medical |
| label | Class label | ankle_boot, normal, MCI |
| split | Data partition | train, val, test |
| batch_id | Incremental batch | D1, D2, D3 |
| modality | Data modality | grayscale_image, T1w |
| format | Storage format | png, nifti, bids |
| site_or_source | Origin identifier | default, site_A, scanner_X |
| notes | Sample-specific notes | baseline_session, noisy_label |

---

## Batch Design Principles

### D₁ (Foundation Batch)
- Stable base classes with clean labels.
- 30–50% of training data.
- Highest quality labels and metadata.
- Goal: establish initial model behavior.

### D₂ (Expansion Batch)
- Introduce new samples, harder cases, or new classes.
- 20–30% of training data.
- May include: new site, different scanner, distribution shift.
- Goal: test incremental learning and adaptation.

### D₃+ (Stress-Test Batches)
- Edge cases, minority classes, noisy labels.
- Goal: evaluate robustness to distribution shift.

---

## Access Timeline

### Week 1–2: Dataset Acquisition
- [ ] Download Fashion-MNIST (immediate)
- [ ] Register for OASIS-3 access (1–3 business days)
- [ ] Complete data use agreement
- [ ] Verify data integrity

### Week 2–3: Preprocessing and QC
- [ ] Fashion-MNIST: validate image matrix and label distribution
- [ ] OASIS-3: run BIDS validator
- [ ] OASIS-3: preprocessing (skull strip, normalize)
- [ ] Generate data index CSV for both datasets

### Week 3–4: Batch Creation
- [ ] Split Fashion-MNIST by class groups
- [ ] Split OASIS-3 by site and session
- [ ] Document class distributions per batch
- [ ] Create train/val/test splits with fixed random seed

### Week 4: Validation
- [ ] Verify batch balance and check for data leakage
- [ ] Confirm metadata completeness
- [ ] Update assumptions with validated data characteristics
