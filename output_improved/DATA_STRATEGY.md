# Data Strategy

## Goal
Define a practical data plan for Learn++ experiments using:
1. One public benchmark dataset.
2. One medical imaging dataset, prioritizing NIfTI and BIDS.

## Tracks
1. Public benchmark track
   - Purpose: fast sanity checks for experiment flow and metric consistency.
   - Target characteristics: clear labels, stable train/val/test split, easy reproducibility.
2. Medical imaging track (NIfTI and BIDS first)
   - Purpose: domain-relevant evaluation for project goals.
   - Target characteristics: documented labels, usable metadata, legal reuse clarity.

## Dataset Selection Criteria
1. Label quality and class balance.
2. License and academic usage permission.
3. Ease of split definition and reproducibility.
4. Compute feasibility under available hardware.
5. Metadata quality for batch creation (site, session, scanner, acquisition details).

## Candidate Datasets (Concrete Examples)

### Public Benchmark Track Candidates

#### Option A: CIFAR-10
- **Source**: https://www.cs.toronto.edu/~kriz/cifar.html
- **License**: Public domain (academic use permitted)
- **Classes**: 10 (airplane, automobile, bird, cat, deer, dog, frog, horse, ship, truck)
- **Samples**: 50k training + 10k test
- **Format**: 32×32 RGB images
- **Pros**:
  - Widely known benchmark
  - Balanced classes (5k samples per class in training)
  - Fast experiments (small images)
  - Well-documented splits
- **Cons**:
  - Low resolution may not reflect medical imaging complexity
  - Too easy for strong models
- **Best for**: Quick proof-of-concept and algorithm validation
- **Batch strategy**: Split by class pairs (D1: classes 0-3, D2: classes 4-6, D3: classes 7-9)

#### Option B: Fashion-MNIST
- **Source**: https://github.com/zalandoresearch/fashion-mnist
- **License**: MIT License
- **Classes**: 10 (clothing categories)
- **Samples**: 60k training + 10k test
- **Format**: 28×28 grayscale images
- **Pros**:
  - Similar difficulty to CIFAR-10 but grayscale (closer to medical images)
  - Balanced classes
  - Fast training
  - Drop-in replacement for MNIST
- **Cons**:
  - Still toy dataset, limited realism
- **Best for**: Efficient baseline when compute is very limited
- **Batch strategy**: Split by temporal acquisition simulation (random splits with different class distributions)

#### Option C: UCI HAR (Human Activity Recognition)
- **Source**: https://archive.ics.uci.edu/ml/datasets/human+activity+recognition+using+smartphones
- **License**: Public domain
- **Classes**: 6 (walking, walking upstairs, walking downstairs, sitting, standing, laying)
- **Samples**: ~7.3k training samples
- **Format**: 561 tabular features (accelerometer/gyroscope data)
- **Pros**:
  - Tabular data (good for classical ML classifiers)
  - Real-world sensor data (closer to medical time-series)
  - Clear incremental learning potential (different subjects/sessions)
- **Cons**:
  - Not image-based
  - Smaller dataset
- **Best for**: Testing feature-based pipelines before medical imaging
- **Batch strategy**: Split by subject groups (D1: subjects 1-10, D2: subjects 11-20, D3: subjects 21-30)

#### **Recommended**: Option C (UCI HAR)
**Rationale**: Best balance of realism, appropriate difficulty for weak classifiers, and structural similarity to medical feature pipelines. Can be processed quickly while maintaining scientific validity.

---

### Medical Imaging Track Candidates

#### Option A: Brain MRI - OASIS-3 (via OpenNeuro)
- **Source**: https://www.oasis-brains.org/
- **License**: Open access with data use agreement
- **Task**: Alzheimer's diagnosis or cognitive decline classification
- **Format**: NIfTI volumes, BIDS-compatible
- **Modality**: T1-weighted MRI
- **Samples**: ~1000+ subjects with longitudinal sessions
- **Classes**: Typically 2-3 (cognitively normal, MCI, dementia)
- **Pros**:
  - Real clinical data
  - Rich metadata (age, sex, clinical scores, scanner info)
  - BIDS format perfect for pipeline development
  - Well-documented
  - Multiple sessions per subject (natural incremental learning)
- **Cons**:
  - Large file sizes (need storage and compute)
  - Requires preprocessing (skull stripping, normalization)
  - Class imbalance typical
  - Data use agreement required
- **Best for**: Realistic medical imaging pipeline development
- **Batch strategy**:
  - D1: Baseline scans from Site A
  - D2: Baseline scans from Site B
  - D3: Follow-up scans from both sites

#### Option B: Chest X-ray - NIH ChestX-ray14
- **Source**: https://nihcc.app.box.com/v/ChestXray-NIHCC
- **License**: Public domain (CC0)
- **Task**: Multi-label disease classification
- **Format**: PNG images (can convert to NIfTI)
- **Modality**: Chest X-ray (frontal view)
- **Samples**: ~112k images from ~30k patients
- **Classes**: 14 diseases + normal (multi-label possible)
- **Pros**:
  - Large dataset
  - No data use agreement required
  - Clinically relevant
  - Can simplify to binary or multi-class task
- **Cons**:
  - Not in NIfTI/BIDS format (requires adaptation)
  - Multi-label nature adds complexity
  - Label noise documented in literature
  - 2D only (not volumetric)
- **Best for**: Large-scale medical image classification without 3D complexity
- **Batch strategy**:
  - D1: Normal vs single disease (e.g., pneumonia)
  - D2: Add second disease class
  - D3: Add third disease class

#### Option C: Brain Tumor Segmentation - BraTS (simplified classification)
- **Source**: https://www.med.upenn.edu/cbica/brats2020/data.html
- **License**: Registration required, academic use permitted
- **Task**: Tumor type classification (instead of full segmentation)
- **Format**: NIfTI volumes, multiple MRI sequences
- **Modality**: T1, T1ce, T2, FLAIR MRI
- **Samples**: ~300+ subjects per year
- **Classes**: 3-4 (glioblastoma, low-grade glioma, normal)
- **Pros**:
  - Standard benchmark in medical imaging
  - NIfTI format native
  - Multiple modalities (rich features)
  - Annual competition keeps data fresh
- **Cons**:
  - Registration process required
  - Originally designed for segmentation (need to adapt)
  - Smaller sample size
  - Complex preprocessing for multi-modal fusion
- **Best for**: Multi-modal medical imaging research
- **Batch strategy**:
  - D1: T1-weighted only, all classes
  - D2: T1 + T2, all classes
  - D3: All modalities, all classes

#### **Recommended**: Option A (OASIS-3 Brain MRI)
**Rationale**:
- True BIDS format (project goal)
- Natural incremental learning through longitudinal sessions
- Excellent metadata for batch design (site, scanner, time)
- Manageable sample size with clinical relevance
- Supports both weak (radiomics features) and strong (deep features) pipelines

**Backup recommendation**: Option B (ChestX-ray14) if OASIS access is delayed or storage constraints prohibit 3D volumes.

---

## Suggested First Pair (Planning Stage → Concrete Selection)

### **Phase 1 Recommended Configuration**

| Track | Dataset | Classes | Samples | Rationale |
|-------|---------|---------|---------|-----------|
| **Public** | UCI HAR | 6 activities | ~7.3k | Fast, tabular, good for weak classifiers |
| **Medical** | OASIS-3 MRI | 2-3 cognitive states | ~1000+ | BIDS format, real clinical data |

### Access Plan
1. **UCI HAR**: Direct download from UCI repository (no registration)
2. **OASIS-3**:
   - Register at https://www.oasis-brains.org/
   - Complete data use agreement (typically 1-3 days)
   - Download via XNAT interface or AWS S3 bucket

---

## Learn++-Ready Data Contract
Use these fields in a shared data index file:
1. **sample_id** - unique identifier for each sample
2. **dataset_name** - "uci_har" or "oasis3_mri"
3. **source_track** - "public" or "medical"
4. **label** - class label (integer or string)
5. **split** - "train", "val", or "test"
6. **batch_id** - "D1", "D2", "D3", etc.
7. **modality** - "accelerometer" for HAR, "T1w" for MRI, etc.
8. **format** - "tabular", "nifti", or "bids"
9. **site_or_scanner** - site identifier or scanner model
10. **notes** - any sample-specific notes (missing data, quality issues, etc.)

### Example data index (CSV format)
```csv
sample_id,dataset_name,source_track,label,split,batch_id,modality,format,site_or_scanner,notes
HAR_001,uci_har,public,walking,train,D1,accelerometer,tabular,subject_01,
HAR_002,uci_har,public,walking_upstairs,train,D1,accelerometer,tabular,subject_01,
OASIS_001,oasis3_mri,medical,normal,train,D1,T1w,bids,site_A_scanner_X,baseline_session
OASIS_002,oasis3_mri,medical,MCI,train,D2,T1w,bids,site_B_scanner_Y,baseline_session
```

---

## Batch Design Guidance

### General Principles
1. **D1 (Foundation Batch)** should include stable base classes with clean labels.
   - Goal: Establish initial model behavior
   - Sample size: 30-50% of training data
   - Quality: Highest quality labels and metadata

2. **D2 (Expansion Batch)** should introduce either new samples, harder cases, or new classes.
   - Goal: Test incremental learning and adaptation
   - Sample size: 20-30% of training data
   - Can include: New site, different scanner, temporal shift

3. **D3 and beyond** should stress-test the model.
   - Goal: Evaluate robustness to distribution shift
   - Can include: Edge cases, minority classes, noisy labels

### UCI HAR Batch Strategy (Subject-Based)
```
D1: Subjects 1-10    (n ≈ 2400)  - Diverse activities, clean labels
D2: Subjects 11-20   (n ≈ 2400)  - Same activities, different individuals
D3: Subjects 21-30   (n ≈ 2500)  - Remaining subjects, potential drift
```

### OASIS-3 MRI Batch Strategy (Site + Time Based)
```
D1: Site A, Baseline visits      (n ≈ 300)  - Single site, single timepoint
D2: Site B, Baseline visits      (n ≈ 300)  - New site (scanner shift)
D3: Sites A+B, Follow-up visits  (n ≈ 400)  - Temporal progression
```

---

## Data Access Timeline

### Week 1-2: Dataset Acquisition
- [ ] Download UCI HAR (immediate)
- [ ] Register for OASIS-3 access (1-3 business days)
- [ ] Complete data use agreement
- [ ] Verify data integrity (checksums)

### Week 2-3: Preprocessing and QC
- [ ] UCI HAR: Load and validate feature matrix
- [ ] OASIS-3: Run BIDS validator
- [ ] OASIS-3: Preprocessing pipeline (skull strip, normalize)
- [ ] Generate data index CSV for both datasets

### Week 3-4: Batch Creation
- [ ] Split UCI HAR by subject groups
- [ ] Split OASIS-3 by site and session
- [ ] Document class distributions per batch
- [ ] Create train/val/test splits with fixed random seed

### Week 4: Validation
- [ ] Verify batch balance and overlap
- [ ] Check for data leakage across splits
- [ ] Confirm metadata completeness
- [ ] Update ASSUMPTIONS.md with validated data characteristics

---

## Risk Mitigation

### Dataset Access Risk (R2 from RISKS.md)
**Risk**: Medical dataset access or license restrictions delay progress

**Mitigation implemented**:
1. ✅ Backup option identified (ChestX-ray14 requires no registration)
2. ✅ UCI HAR requires no approval (can start immediately)
3. ✅ OASIS-3 registration initiated early in Phase 1
4. 🔄 Timeline includes 3-day buffer for approval

### Data Quality Risk
**Risk**: Metadata quality inconsistent across sites

**Mitigation**:
1. Prioritize OASIS-3 (known for high-quality metadata)
2. Include metadata completeness in batch design criteria
3. Document missing fields in data index "notes" column
4. Fail-fast: Validate metadata before experiment starts

---

## Next Steps (Immediate Actions)

1. **Confirm dataset selections** with stakeholders
2. **Initiate OASIS-3 registration** (if not already done)
3. **Download UCI HAR** and validate format
4. **Create data index template** CSV file
5. **Update DECISION_LOG.md** with dataset selection rationale
6. **Fill concrete values** in [IMPLEMENTATION_HANDOFF.md](IMPLEMENTATION_HANDOFF.md)
