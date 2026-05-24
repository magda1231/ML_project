# TODO

## Repo Hygiene

- [x] Remove old output folders (claude_sonnet, fixed, gemini).
- [x] Consolidate to planning_seed/ folder.
- [x] Rename output_improved_v2 → planning_seed.
- [x] Scrub personal names from all docs.
- [x] Remove obsolete .txt feedback files.
- [x] Set up .gitignore (data/, results/, __pycache__, .ipynb_checkpoints).
- [ ] Commit and push current changes (PR to main).

## Implementation Structure (created)

```
src/
├── learnpp/       # Learn++ from-scratch implementation
├── features/      # ROI-based feature extraction for BraTS
└── experiments/   # Experiment runners (EXP-01, EXP-02)
notebooks/
└── 01_learnpp_walkthrough.ipynb  # Step-by-step Learn++ with explanations
data/              # .gitignored, download datasets here
results/           # .gitignored, experiment outputs go here
```

## Immediate Next Steps

- [ ] Download Fashion-MNIST (`torchvision.datasets` or direct).
- [ ] Register at synapse.org and download BraTS.
- [x] Hardware decided: local RTX A1000 + i9.
- [x] MRI vectorization decided: ROI-based feature extraction (see 04_DATA_STRATEGY.md).

## Learn++ Preparation

- [ ] Define batch sequence D₁, D₂, D₃ with class distribution notes.
- [ ] Lock hyperparameter search spaces for Decision Tree and Gradient Boosting.
- [ ] Agree on fixed CompositeScore weights (currently 0.40/0.15/0.15/0.15/0.15).
- [ ] Decide on Learn++ iteration budget T_k per batch.

## Implementation

- [x] Learn++ scaffold created (notebooks/01_learnpp_walkthrough.ipynb).
- [ ] Validate Learn++ on synthetic data (run notebook cells 4–6).
- [ ] Build ROI feature extraction (src/features/) using BraTS segmentation masks.
- [ ] Run EXP-01: Decision Tree vs Gradient Boosting on Fashion-MNIST.
- [ ] Run EXP-02: Decision Tree vs Gradient Boosting on BraTS MRI.
- [ ] Compare results: CompositeScore + Wilcoxon significance test.
- [ ] Produce static Gradient Boosting upper-bound baseline (no Learn++).

## Presentation & Report

- [ ] Second group: draft intro slides on ensemble classifiers and Learn++.
- [ ] Our group: draft method/results slides on MRI feature extraction and experiments.
- [ ] Produce key figures (per-batch line chart, confusion matrices, cost comparison).
- [ ] Rehearse presentation.
- [ ] Write and submit report after presentation.

## Phase 3 Preparation (Future)

- [ ] Define scope and success criteria for Phase 3A (photo/drone recognition).
- [ ] Define scope and success criteria for Phase 3B (area mapping).
- [ ] Define simulation-first constraints for Phase 3C (swarm integration).
