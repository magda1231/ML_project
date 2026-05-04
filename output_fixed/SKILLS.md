# Classifier Catalog and Project Skills

## What "Weak" vs "Strong" Means in This Project
Use a combined interpretation based on model capacity and cost.

Weak classifier profile:
1. Lower capacity and simple decision boundary.
2. Fast training and low memory use.
3. Useful diversity source in ensemble methods like Learn++.

Strong classifier profile:
1. Higher capacity and richer decision boundary.
2. Usually better standalone quality.
3. Higher training or inference cost.

## Weak Classifier Candidates
| Candidate | Why It Fits Learn++ | Compute Cost | Notes |
|---|---|---|---|
| Decision Tree (depth 1-3) | High diversity from data resampling | Low | Very common weak learner baseline |
| Gaussian Naive Bayes | Very fast and stable | Very low | Good baseline for high-dimensional sparse features |
| Logistic Regression (regularized) | Simple linear boundary | Low | Strong regularization keeps it weak enough |
| Linear SVM / SGD Hinge | Margin-based linear model | Low | Good speed-quality tradeoff |
| LDA | Linear generative assumption | Low | Useful when class covariances are informative |
| Small MLP | Slightly nonlinear but compact | Medium | Keep hidden layers small to preserve weakness |

## Strong Classifier Candidates
| Candidate | Strength Rationale | Compute Cost | Notes |
|---|---|---|---|
| Random Forest | Nonlinear, robust, strong tabular baseline | Medium | Good for engineered/radiomics features |
| RBF SVM | Flexible boundary, often strong quality | Medium to high | Kernel scaling can become expensive |
| Gradient Boosting Family | Frequently top performer on tabular tasks | Medium to high | Includes XGBoost/LightGBM/CatBoost-style models |
| Medium MLP | Better representational power | Medium | Requires careful regularization |
| Feature Encoder + Linear Head | Strong when feature extractor is rich | High | Best for advanced imaging representation settings |

## Suggested Comparison Matrix
1. Public benchmark track:
   - Weak: Decision Tree depth 2, Gaussian Naive Bayes, Linear SVM.
   - Strong: Random Forest, RBF SVM, Gradient Boosting.
2. Medical track (NIfTI/BIDS first):
   - Weak: Gaussian Naive Bayes, Decision Tree depth 2.
   - Strong: Random Forest, Gradient Boosting.

## Skills Needed for the Team
1. Incremental learning fundamentals (Learn++, boosting, ensemble voting).
2. Classifier selection and fair benchmarking.
3. Cost profiling (training time, inference latency, memory).
4. Medical imaging data handling (NIfTI and BIDS conventions).
5. Experimental reporting (clear markdown scorecards and rationale).
6. Reproducibility discipline (dataset versions, metadata, assumptions).

## Deliverable-Focused Work (No Code)
1. Finalize weak and strong shortlist.
2. Define experiment scorecard template.
3. Define data schema template for batch-wise Learn++ inputs.
4. Write decision criteria for moving from shortlist to final model choice.
