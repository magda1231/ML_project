# ML_project

Machine learning project for the course "ML: Learning, Adaptation, and Uncertainty 2026".

## Project Goal

Compare base learners of varying capacity (simple vs complex) within a Learn++ incremental learning framework, using quality and compute cost as core selection criteria. Apply the selected strategy to medical image classification.

## Current Focus

Phase 1 (active): algorithm design and base learner selection — documentation only, no implementation code.

## Phase 1 Scope

### Objectives
1. Evaluate Learn++ as the primary incremental learning framework (alternatives acknowledged).
2. Select base learners ranging from simple (Decision Tree, Naive Bayes, Linear SVM) to complex (Gradient Boosting, Random Forest, RBF SVM).
3. Design experiment protocol with paired simple-complex comparisons.
4. Define data strategy: Fashion-MNIST (public benchmark) + OASIS-3 MRI (medical track).
5. Document Learn++ equations, flow, and decision logic.
6. Produce a complete planning package for implementation handoff.

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
├── comments_from_M.txt          # Reviewer feedback on earlier versions
├── output_improved/              # v1 planning documentation (21 files)
└── output_improved_v2/           # v2 planning documentation (12 files, current)
    ├── 01_ROADMAP.md
    ├── 02_ARCHITECTURE.md
    ├── 03_EXPERIMENT_PLAN.md
    ├── 04_DATA_STRATEGY.md
    ├── 05_CLASSIFIER_CATALOG.md
    ├── 06_LEARNPP_ALGORITHM.md
    ├── 07_REFINEMENT_PROCESS.md
    ├── 08_RISKS_AND_ASSUMPTIONS.md
    ├── 09_DECISION_LOG.md
    ├── 10_TODO.md
    ├── 11_GLOSSARY.md
    ├── 12_HANDOFF_TEMPLATE.md
    └── CHANGES_FROM_V1.md
```

### Key Documents (start here)
- **[01_ROADMAP.md](output_improved_v2/01_ROADMAP.md)** — Project phases and governance
- **[02_ARCHITECTURE.md](output_improved_v2/02_ARCHITECTURE.md)** — System architecture and scoring formula
- **[03_EXPERIMENT_PLAN.md](output_improved_v2/03_EXPERIMENT_PLAN.md)** — Experiment matrix and metrics
- **[06_LEARNPP_ALGORITHM.md](output_improved_v2/06_LEARNPP_ALGORITHM.md)** — All equations, flow, worked examples