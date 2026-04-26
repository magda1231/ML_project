# ML_project

Machine learning project for the course "ML: Learning, Adaptation, and Uncertainty 2026".

## Project Goal
Compare weak and strong classifiers in a Learn++ incremental learning setup, using quality and compute cost as core selection criteria.

## Current Focus
This repository is currently focused on Phase 1 only.

Phase 1 is the active and prioritized scope:
1. Select weak and strong classifier strategy.
2. Finalize Learn++ design documentation and decision rules.
3. Prepare implementation handoff for the human owner.

Future phases are planned but not active execution scope yet.

## Phase 1 Detailed Scope

### Objectives
1. Define what weak and strong means using both predictive quality and compute cost.
2. Build and refine classifier candidate lists.
3. Create shortlists for weak and strong model families.
4. Document Learn++ equations, flow, and decision logic in readable form.
5. Define data strategy and evaluation protocol for later implementation.
6. Produce a complete planning package that can be directly used by the human implementer.

### Core Outputs
1. Architecture and planning documents.
2. Classifier profiles and pairing strategy.
3. Equation references, detailed explanations, worked examples, and self-checks.
4. Risk, assumptions, decision log, and scorecard template.
5. Phased roadmap and transition gates.

### Out of Scope in Phase 1
1. No model implementation code.
2. No production training pipeline.
3. No deployment or swarm-control execution.

### Phase 1 Exit Criteria
1. Final weak and strong shortlist is documented and justified.
2. Learn++ flow and key equations are clearly documented.
3. Evaluation rubric and scorecard are fixed.
4. Risks, assumptions, and handoff notes are ready for implementation.

## Roadmap
1. Phase 1: classifier selection and Learn++ design documentation.
2. Phase 2: medical image recognition implementation (NIfTI and BIDS priority).
3. Phase 3A: photo and drone-image recognition.
4. Phase 3B: area mapping and geospatial outputs.
5. Phase 3C: swarm control integration (simulation-first).

## Repository Notes
1. Planning and documentation artifacts are maintained under output/output.
2. Project input materials and planning references are maintained in input and input_docs.
3. Input folders are part of the project context and should be tracked together with planning updates.
4. Current active phase is Phase 1 documentation and preparation only.