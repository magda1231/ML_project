# Decision Log

## Purpose
Track important project decisions and the reasoning behind them. Update this log whenever a material decision is made.

## Entries

| # | Date | Decision | Options Considered | Why Chosen | Impact |
|---|------|----------|-------------------|------------|--------|
| 1 | 2026-04-26 | Documentation-only Phase 1 | Code now vs docs first | Faster alignment, cleaner handoff, separates design from implementation | Implementation deferred to Phase 2 |
| 2 | 2026-04-26 | Use quality + compute cost for model ranking | Accuracy-only vs mixed CompositeScore | Reflects practical deployment constraints, not just lab metrics | More robust model selection |
| 3 | 2026-04-26 | Two data tracks (public + medical) | Public-only vs medical-only vs both | Better generalization of findings, dual validation | Extra planning effort, stronger evidence |
| 4 | 2026-04-26 | Medical track starts with NIfTI/BIDS | DICOM-first vs NIfTI/BIDS-first | Matches project goals and available tooling | Simplifies first-cycle data pipeline |
| 5 | 2026-04-27 | Phased roadmap with split Phase 3 | Single broad phase vs phased split | Lower delivery risk, clearer progression | Improved governance and execution clarity |
| 6 | 2026-05-11 | Reframe "weak vs strong" as "base learner selection" | Keep original framing vs reframe | Original terminology conflated model capacity with model performance, causing confusion | Clearer documentation, less ambiguity |
| 7 | 2026-05-11 | Learn++ as primary framework (not mandatory) | Learn++ only vs acknowledge alternatives | Learn++ is best for comparing diverse base learners but alternatives should be documented for future phases | Flexibility for Phase 2 if Learn++ proves insufficient |
| 8 | 2026-05-11 | Adjust CompositeScore weights (0.40/0.15/0.15/0.15/0.15) | Original 0.50/0.20/0.10/0.10/0.10 vs balanced weights | MacroF1 and BalancedAccuracy are correlated; original weights over-counted quality vs cost | Better quality-cost balance in ranking |
| 9 | 2026-05-11 | Fashion-MNIST as public benchmark | UCI HAR vs CIFAR-10 vs Fashion-MNIST | Grayscale format closer to medical imaging; better difficulty level than MNIST/CIFAR-10 | More relevant cross-track comparison |
| 10 | 2026-05-11 | Add hyperparameter strategy (constrained random search) | No strategy vs grid search vs random search | Fair comparison requires consistent tuning budget; random search is more efficient than grid | Prevents complex models from unfair tuning advantage |
| 11 | 2026-05-11 | Add statistical significance testing | No testing vs t-test vs Wilcoxon | Prevents claiming superiority based on point estimates alone | More reliable conclusions |
| 12 | 2026-05-11 | Consolidate 21 files → 12 numbered files | Keep v1 structure vs consolidate | Eliminates redundancy (equations appeared in 4 files), removes SKILLS/PROFILES confusion, self-navigating via numbering | Easier maintenance, no INDEX.md needed |
| 13 | 2026-05-11 | Scope to 1 simple + 1 complex (Decision Tree + Gradient Boosting) | 1+1 vs 3+3 shortlist vs full matrix | Assignment requires comparing one simple and one complex classifier, not a selection tournament | 2 experiments instead of 6–8, cleaner comparison |
