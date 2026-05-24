# Changes from v1 (output_improved → output_improved_v2)

**Date**: 2026-05-11

---

## Structural Changes

### File Consolidation: 21 files → 12 + this changelog

| v1 Files | v2 File | What Changed |
|----------|---------|-------------|
| PHASED_ROADMAP.md | 01_ROADMAP.md | Phase 3 reduced to brief outline, governance rules tightened |
| architecture.md | 02_ARCHITECTURE.md | Added alternative frameworks table (EWC, PackNet, etc.), justified Learn++ as primary, rebalanced CompositeScore weights |
| EXPERIMENT_PLAN.md | 03_EXPERIMENT_PLAN.md | Added hyperparameter strategy, statistical significance testing, reframed weak/strong → simple/complex |
| DATA_STRATEGY.md | 04_DATA_STRATEGY.md | Replaced UCI HAR recommendation with Fashion-MNIST |
| CLASSIFIER_PROFILES.md + SKILLS.md + CLASSIFIER_PAIRING_STRATEGY.md | 05_CLASSIFIER_CATALOG.md | Merged 3 files into one, eliminated redundancy |
| CLASSIFIER_EQUATIONS.md + CLASSIFIER_EQUATIONS_DETAILED.md + CLASSIFIER_EQUATIONS_WORKED_EXAMPLES.md + LEARNPP_EQUATION_FLOW_MAP.md + appendix/CLASSIFIER_EQUATIONS_SELF_CHECK.md | 06_LEARNPP_ALGORITHM.md | Consolidated 5 equation files into one, added table of contents |
| CLASSIFIER_REFINEMENT_GUIDE.md + SCORECARD_TEMPLATE.md | 07_REFINEMENT_PROCESS.md | Merged gate process with scorecard, added statistical comparison section |
| ASSUMPTIONS.md + RISKS.md | 08_RISKS_AND_ASSUMPTIONS.md | Combined into one file, added R10 (hyperparameter fairness risk) |
| DECISION_LOG.md | 09_DECISION_LOG.md | Added 7 new decisions (#6–#12) documenting v2 changes |
| TODO.md | 10_TODO.md | Added experiment execution checklist, hyperparameter tasks |
| GLOSSARY.md | 11_GLOSSARY.md | Updated all terminology (simple/complex replaces weak/strong), streamlined |
| IMPLEMENTATION_HANDOFF.md | 12_HANDOFF_TEMPLATE.md | Trimmed to essentials, removed excessive placeholder sections |

### Files Removed
| v1 File | Reason |
|---------|--------|
| INDEX.md | Numbered files are self-navigating; INDEX added overhead |
| CHANGES_SUMMARY.md | Replaced by this file |
| SKILLS.md | Merged into 05_CLASSIFIER_CATALOG.md |
| appendix/ folder | Self-check exercises moved into 06_LEARNPP_ALGORITHM.md |

---

## Conceptual Changes

### 1. Learn++ is Not Mandatory
- v1: Learn++ was treated as the only option with no alternatives discussed.
- v2: Learn++ is the **primary candidate** with alternatives acknowledged (EWC, PackNet, Progressive Neural Networks, Learn++.NSE, ELLA). Rationale for choosing Learn++ is documented.

### 2. "Weak vs Strong" → "Base Learner Selection"
- v1: Framed as comparing "weak classifiers" vs "strong classifiers" inside Learn++, which conflated model capacity with model performance.
- v2: Reframed as **base learner selection** — testing classifiers of varying capacity (simple vs complex) as Learn++'s base learner component.

### 3. CompositeScore Weights Rebalanced
- v1: 0.50 MacroF1 + 0.20 BalancedAccuracy + 0.10 each for costs.
- v2: 0.40 MacroF1 + 0.15 BalancedAccuracy + 0.15 each for costs.
- Rationale: MacroF1 and BalancedAccuracy are correlated; 70% combined weight over-counted quality.

### 4. Public Benchmark Changed
- v1: Recommended UCI HAR (tabular sensor data).
- v2: Recommended Fashion-MNIST (grayscale images).
- Rationale: Fashion-MNIST's grayscale format is more representative of medical imaging than tabular accelerometer data.

### 5. Hyperparameter Strategy Added
- v1: No hyperparameter approach defined.
- v2: Constrained random search (20 configs/model), tuned on D₁ and frozen for subsequent batches.

### 6. Statistical Significance Added
- v1: No significance testing mentioned.
- v2: Wilcoxon signed-rank test at p < 0.05 for paired model comparisons.

### 7. Phase 3 Scope Reduced
- v1: Phase 3A/3B/3C described with full deliverables and exit gates.
- v2: Phase 3 outlined briefly for project context, with detailed planning deferred.
