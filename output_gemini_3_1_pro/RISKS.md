# Risks

## Purpose
Capture planning risks before implementation starts, with clear mitigations.

## Risk Register
| ID | Risk | Probability | Impact | Early Signal | Mitigation | Owner |
|---|---|---|---|---|---|---|
| R1 | Public and medical datasets are not label-compatible for fair comparison | Medium | High | Label mapping becomes ambiguous | Define a label harmonization sheet before experiments | Team |
| R2 | Medical dataset access or license restrictions delay progress | Medium | High | Access requests remain pending | Keep backup dataset options and pre-approve licenses | Team |
| R3 | Batch design introduces strong distribution shifts that hide true model behavior | Medium | High | Sudden unstable metrics between D1 and D2 | Document class distribution per batch and run drift checks | Team |
| R4 | Compute cost too high for stronger candidates | High | Medium | Training time exceeds budget repeatedly | Set strict compute caps and downselect early | Team |
| R5 | Weak and strong definitions stay subjective | Medium | Medium | Repeated debate on model categories | Use fixed quality-plus-cost thresholds in decision rubric | Team |
| R6 | Class imbalance causes misleading aggregate scores | High | High | High accuracy with weak minority recall | Prioritize Macro F1, Balanced Accuracy, per-class recall | Team |
| R7 | Medical metadata quality is inconsistent across sites | Medium | Medium | Missing scanner/session fields | Add required metadata fields to intake checklist | Team |
| R8 | No-code phase drifts into premature implementation | Medium | Medium | Pull requests include prototype code | Enforce documentation-only gate until sign-off | Team |
| R9 | Final recommendation lacks reproducibility context | Medium | High | Results cannot be traced to dataset versions | Track data version, split seed, and metric configuration in scorecards | Team |

## Top 3 Priority Risks
1. R2 dataset access and licensing risk.
2. R3 batch shift risk in incremental learning.
3. R6 class imbalance risk.

## Immediate Actions
1. Lock one primary and one backup dataset per track.
2. Approve a standard batch report section for every experiment.
3. Freeze evaluation metric policy before any implementation starts.
