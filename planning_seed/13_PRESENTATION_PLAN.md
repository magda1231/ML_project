# Presentation Plan

## Deliverables

1. **Presentation** — slides for class delivery.
2. **Report** — written report submitted after presentation.

---

## Team Split

| Team Member | Responsibility |
|-------------|---------------|
| **Second group** | Introduction to ensemble classifiers, Learn++ background |
| **Our group** | MRI feature extraction, dataset pipeline, implementation results |

---

## Presentation Structure (Suggested Slides)

### Part 1: Introduction and Background (Second group)

| Slide # | Title | Content |
|---------|-------|---------|
| 1 | Title Slide | Project title, team members, date |
| 2 | Problem Statement | What is incremental learning? Why does it matter? |
| 3 | Ensemble Classifiers Overview | What are ensemble methods? (bagging, boosting, stacking) |
| 4 | Learn++ Algorithm | Key idea: weighted voting of base learners across batches |
| 5 | Learn++ vs Other Methods | Brief comparison with EWC, replay methods, etc. |
| 6 | Our Research Question | Does a complex base learner outperform a simple one in Learn++? |

### Part 2: Method and Implementation (Our group)

| Slide # | Title | Content |
|---------|-------|---------|
| 7 | Selected Classifiers | Decision Tree (simple) vs Gradient Boosting (complex) — why this pair |
| 8 | Dataset: Fashion-MNIST | Why chosen, batch design, class distribution |
| 9 | Dataset: Medical MRI | Source (OASIS-3/BraTS/OpenNeuro), fMRI vectorization approach |
| 10 | fMRI Vectorization Pipeline | How brain images become feature vectors for classifiers |
| 11 | Experiment Setup | Learn++ pipeline, hyperparameters, evaluation protocol |
| 12 | Results: Public Benchmark | CompositeScore, per-batch metrics, confusion matrices |
| 13 | Results: Medical Track | CompositeScore, per-batch metrics, comparison |
| 14 | State of the Art Comparison | Our results vs published Learn++ baselines |
| 15 | Conclusions | Which base learner wins? Quality-cost trade-off summary |
| 16 | Future Work | Phase 2 plans, limitations, potential improvements |

---

## Report Structure (Suggested Sections)

1. **Abstract** (~200 words)
2. **Introduction** (divided into two parts)
   - 2a. Ensemble classifiers and incremental learning (Second group)
   - 2b. Medical imaging and MRI feature extraction (Our group)
3. **Related Work** — Learn++ paper, state of the art, published baselines
4. **Method**
   - Learn++ algorithm description
   - Classifier selection rationale
   - Dataset description and preprocessing
   - fMRI vectorization approach
   - Evaluation metrics (CompositeScore, MacroF1, etc.)
5. **Experiments**
   - Setup and hyperparameters
   - Batch design
   - Results on Fashion-MNIST
   - Results on medical MRI
6. **Discussion**
   - Simple vs complex: what did we learn?
   - Comparison with published results
   - Limitations
7. **Conclusion**
8. **References**

---

## Key Figures to Produce

- [ ] Learn++ algorithm flowchart (from 06_LEARNPP_ALGORITHM.md mermaid)
- [ ] Per-batch performance line chart (MacroF1 across D₁, D₂, D₃)
- [ ] CompositeScore bar chart (Decision Tree vs Gradient Boosting)
- [ ] Confusion matrices (per model, per track)
- [ ] fMRI vectorization pipeline diagram
- [ ] Cost comparison table (training time, inference time, memory)

---

## Timeline

- [ ] Presentation slides drafted
- [ ] Presentation rehearsal
- [ ] Presentation delivered
- [ ] Report written and submitted
