# Phased Project Roadmap

## Confirmed Structure
1. Phase 1: select weak and strong classifier strategy for Learn++ and finalize algorithm design documentation.
2. Phase 2: implement medical image recognition ML algorithm.
3. Phase 3: expand to non-medical vision and drone use cases.
4. Phase 3 is split into 3A, 3B, and 3C to reduce risk.

## Phase 1: Learn++ Selection and Design

### Objective
1. Finalize weak and strong classifier choices.
2. Finalize Learn++ design and evaluation protocol.
3. Keep this stage documentation-first with implementation performed by the human user.

### Deliverables
1. Final weak and strong classifier shortlist.
2. Equation references and worked examples.
3. Evaluation scorecard and decision logs.
4. Handoff brief for implementation.

### Exit Gate
1. Classifier choices are approved and justified by quality and compute criteria.
2. Learn++ flow and metrics are fully documented.
3. Risks and assumptions are updated and accepted.

## Phase 2: Medical Image Recognition Implementation

### Objective
1. Build and validate the medical imaging recognition pipeline.
2. Apply selected Phase 1 classifier strategy in practical experiments.

### Scope
1. Prioritize NIfTI and BIDS inputs.
2. Use fixed metrics and compute budget from Phase 1.
3. Emphasize reproducibility and clinical-data handling constraints.

### Deliverables
1. Implemented training and evaluation pipeline.
2. Reproducible experiment reports.
3. Updated recommendation of strong and weak model use in medical context.

### Exit Gate
1. Stable performance on medical track.
2. Reproducible results under agreed budget.
3. Clear known limitations and deployment caveats.

## Phase 3: General Vision and Drone Expansion

### Phase 3A: Photo and Drone-Image Recognition
1. Objective: extend recognition from medical-only domain to natural photos and drone imagery.
2. Deliverables: domain-expanded dataset protocol and cross-domain model evaluation.
3. Exit gate: robust recognition quality across non-medical image sources.

### Phase 3B: Area Mapping and Geospatial Layer
1. Objective: convert image-level outputs into area-level mapping products.
2. Deliverables: map-generation workflow and spatial consistency validation.
3. Exit gate: mapping outputs are stable and interpretable for operations.

### Phase 3C: Swarm Control Integration
1. Objective: use recognition and mapping outputs to support swarm planning and control.
2. Deliverables: swarm decision logic, simulation scenarios, and safety constraints.
3. Exit gate: successful simulation-level validation before any field use.

## Cross-Phase Governance Rules
1. No phase starts before previous phase exit gate is met.
2. Every phase requires explicit risk review and decision-log update.
3. Metric definitions remain consistent unless a documented change is approved.

## Why This Split Is Better
1. Reduces complexity and protects delivery quality.
2. Keeps implementation risk isolated to phase boundaries.
3. Makes project narrative clearer for class, review, and future extension.
