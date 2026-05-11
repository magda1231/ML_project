# Project Roadmap

## Project Goal

Compare base learner strategies within an incremental ensemble learning framework (Learn++ as primary candidate) to determine which base learners deliver the best quality-to-cost trade-off for medical image classification. The project progresses from algorithm design through medical imaging implementation and eventually to broader vision applications.

> **Terminology note**: Earlier versions of this project used "weak vs strong classifier comparison." This has been reframed as **base learner selection** — we are selecting which classifiers to use as Learn++'s base learner component, ranging from simple (low-capacity) to complex (high-capacity). See [11_GLOSSARY.md](11_GLOSSARY.md) for definitions.

## Phase Overview

| Phase | Focus | Status |
|-------|-------|--------|
| Phase 1 | Algorithm design and base learner selection (documentation only) | Active |
| Phase 2 | Medical image recognition implementation | Planned |
| Phase 3 | Expansion to broader vision domains | Future |

---

## Phase 1: Algorithm Design and Base Learner Selection

### Objective
1. Evaluate and select an incremental learning framework (Learn++ is the primary candidate; alternatives are acknowledged in [02_ARCHITECTURE.md](02_ARCHITECTURE.md)).
2. Select base learners (simple and complex) for comparative experiments.
3. Design the evaluation protocol and scoring methodology.
4. Produce complete planning documentation for implementation handoff.

### Deliverables
1. Final base learner shortlist with selection rationale.
2. Equation references with detailed explanations and worked examples.
3. Evaluation scorecards and decision log.
4. Implementation handoff brief.

### Exit Gate
1. Base learner choices are justified by quality and compute criteria.
2. Incremental learning flow and metrics are fully documented.
3. Risks and assumptions are reviewed and accepted.
4. Handoff document is complete and signed off.

---

## Phase 2: Medical Image Recognition Implementation

### Objective
1. Build and validate the medical imaging recognition pipeline.
2. Apply the Phase 1 base learner strategy in practical experiments.

### Scope
1. Prioritize NIfTI and BIDS inputs.
2. Use fixed metrics and compute budget from Phase 1.
3. Emphasize reproducibility and clinical-data handling constraints.

### Deliverables
1. Implemented training and evaluation pipeline.
2. Reproducible experiment reports with per-batch results.
3. Updated recommendation based on empirical results.

### Exit Gate
1. Stable performance on medical track with reproducible results.
2. Results within agreed compute budget.
3. Known limitations and deployment caveats documented.

---

## Phase 3: Vision Domain Expansion

Phase 3 extends the validated pipeline to broader domains. It is split into sub-phases to reduce delivery risk.

### Phase 3A: Photo and Drone-Image Recognition
- Extend recognition from medical domain to natural photos and drone imagery.
- Deliverable: cross-domain evaluation report showing transfer performance.

### Phase 3B: Area Mapping
- Convert image-level outputs into area-level mapping products.
- Deliverable: map-generation workflow with spatial consistency validation.

### Phase 3C: Swarm Integration
- Use recognition and mapping outputs to support swarm planning.
- Deliverable: simulation-level validation (simulation-first, no field deployment without validation).

> Phase 3 sub-phases are outlined for project context. Detailed planning will happen after Phase 2 exit gate is met.

---

## Governance Rules

1. No phase starts before the previous phase exit gate is met.
2. Every phase requires an explicit risk review and decision-log update.
3. Metric definitions remain consistent unless a documented change is approved.
4. Phase 1 is documentation-only — no implementation code until handoff is complete.
