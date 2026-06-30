# ML_project

Machine learning project for the course "ML: Learning, Adaptation, and Uncertainty 2026".

## Project Goal

Compare base learners of varying capacity (simple vs complex) within a Learn++ incremental learning framework, using quality and compute cost as core selection criteria. Apply the selected strategy to medical image classification.

## Current Focus

Phase 1 (active): algorithm design and base learner selection — documentation only, no implementation code.

## Phase 1 Scope

### Objectives
1. Evaluate Learn++ as the primary incremental learning framework (alternatives acknowledged).
2. Compare 2 base learners: MLP (from the original Learn++ paper) vs Decision Tree (recommended for MRI data, used in later Learn++ variants).
3. MLP chosen as it is the base classifier from the original Learn++ paper (Polikar et al., 2001). Decision Tree used in Learn++.NSE (Elwell & Polikar, 2011) and shown to perform well on medical imaging tasks.
4. Define data strategy: Fashion-MNIST (public benchmark) + medical MRI (BraTS).
5. Document Learn++ equations, flow, and decision logic.
6. Produce planning package for implementation handoff.

### Out of Scope in Phase 1
1. No model implementation code.
2. No production training pipeline.
3. No deployment or swarm-control execution.

### Exit Criteria
1. Final base learner shortlist is documented and justified.
2. Learn++ algorithm flow and equations are fully documented.
3. Evaluation rubric (CompositeScore) and scorecards are fixed.
4. Risks, assumptions, and handoff brief are ready for Phase 2.

## Roadmap

1. **Phase 1** (active): Base learner selection and algorithm design documentation.
2. **Phase 2**: Medical image recognition implementation (NIfTI / BIDS priority).
3. **Phase 3**: Vision domain expansion (photo/drone → area mapping → swarm integration).

## Repository Structure

```
ML_project/
├── README.md
├── comments_from_M.txt          # Reviewer feedback (v1, addressed)
├── M_notes_v2.txt               # Meeting notes and scope decisions
├── planning_seed/               # Planning documentation (13 files)
│   ├── 01_ROADMAP.md
│   ├── 02_ARCHITECTURE.md
│   ├── 03_EXPERIMENT_PLAN.md
│   ├── 04_DATA_STRATEGY.md
│   ├── 05_CLASSIFIER_CATALOG.md
│   ├── 06_LEARNPP_ALGORITHM.md
│   ├── 07_REFINEMENT_PROCESS.md
│   ├── 08_RISKS_AND_ASSUMPTIONS.md
│   ├── 09_DECISION_LOG.md
│   ├── 10_TODO.md
│   ├── 11_GLOSSARY.md
│   ├── 12_HANDOFF_TEMPLATE.md
│   ├── 13_PRESENTATION_PLAN.md
│   └── CHANGES_FROM_V1.md
├── src/                         # Implementation code
│   ├── learnpp/                 # Learn++ from-scratch implementation
│   ├── features/                # ROI feature extraction pipeline
│   └── experiments/             # Experiment runners
├── notebooks/                   # Step-by-step walkthroughs
│   └── 01_learnpp_walkthrough.ipynb
├── data/                        # Datasets (gitignored)
└── results/                     # Experiment outputs (gitignored)
```

## Team Split

| Member | Focus |
|--------|-------|
| **Second group** | Introduction to ensemble classifiers, Learn++ background |
| **Our group** | MRI feature extraction, dataset pipeline, implementation, results |

### Key Documents (start here)
- **[01_ROADMAP.md](planning_seed/01_ROADMAP.md)** — Project phases and governance
- **[03_EXPERIMENT_PLAN.md](planning_seed/03_EXPERIMENT_PLAN.md)** — Experiment matrix, baselines, metrics
- **[06_LEARNPP_ALGORITHM.md](planning_seed/06_LEARNPP_ALGORITHM.md)** — All equations, flow, worked examples
- **[01_learnpp_walkthrough.ipynb](notebooks/01_learnpp_walkthrough.ipynb)** — Running Learn++ implementation