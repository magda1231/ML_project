# Learn++ Equation Flow Map

## Purpose
This file shows exactly where each Learn++ equation is used in the full pipeline.

## End-to-End Visual Flow
```mermaid
flowchart TD
    A[Start batch D_k with current sample weights w_t] --> B[Normalize weights to distribution D_t]
    B --> C[Sample TR_t and TE_t from D_t]
    C --> D[Train weak learner h_t on TR_t]
    D --> E[Compute weak error epsilon_t on current batch]
    E --> F{epsilon_t < 1/2?}
    F -- No --> C
    F -- Yes --> G[Compute beta_t = epsilon_t/(1 - epsilon_t)]
    G --> H[Build composite vote H_t via weighted voting]
    H --> I[Compute composite error E_t]
    I --> J[Compute B_t = E_t/(1 - E_t)]
    J --> K[Update sample weights w_{t+1}]
    K --> L{t < T_k?}
    L -- Yes --> B
    L -- No --> M{More batches?}
    M -- Yes --> N[Move to next batch D_{k+1}]
    N --> B
    M -- No --> O[Output final H_final across all hypotheses]
```

## Equation Map by Pipeline Step

### Step 1: Normalize raw weights
$$
D_t(i)=\frac{w_t(i)}{\sum_{r=1}^{m}w_t(r)}
$$

Meaning:
1. Convert raw weights into a proper distribution.
2. Larger $D_t(i)$ means sample $i$ is emphasized more in this iteration.

### Step 2: Train weak learner on weighted sample draw
No single Learn++ equation here.

Meaning:
1. This step calls the base classifier training math.
2. Example: tree split criteria, Naive Bayes likelihoods, SVM margin objective.

### Step 3: Compute weak hypothesis error
$$
\epsilon_t = \sum_{i:h_t(x_i)\neq y_i}D_t(i)
$$

Meaning:
1. Add only the weights of misclassified samples.
2. If $\epsilon_t \ge 1/2$, reject this weak hypothesis and resample.

### Step 4: Convert weak error to vote-control term
$$
\beta_t = \frac{\epsilon_t}{1-\epsilon_t}
$$

Meaning:
1. Smaller error gives smaller $\beta_t$.
2. Smaller $\beta_t$ yields a larger vote weight through $\log(1/\beta_t)$.

### Step 5: Weighted majority vote for composite hypothesis
$$
H_t(x)=\arg\max_{y\in Y}\sum_{j=1}^{t}\mathbb{1}[h_j(x)=y]\log\left(\frac{1}{\beta_j}\right)
$$

Meaning:
1. Every prior weak hypothesis votes for a class.
2. Better hypotheses have stronger vote weights.

### Step 6: Composite hypothesis error
$$
E_t=\sum_{i=1}^{m}D_t(i)\,\mathbb{1}[H_t(x_i)\neq y_i]
$$

Meaning:
1. Measure weighted error of the ensemble, not a single weak learner.

### Step 7: Composite error normalization
$$
B_t=\frac{E_t}{1-E_t}
$$

Meaning:
1. Turns ensemble error into the factor used for the next weight update.

### Step 8: Update sample weights
$$
w_{t+1}(i)=
\begin{cases}
w_t(i)B_t, & \text{if }H_t(x_i)=y_i \\
w_t(i), & \text{if }H_t(x_i)\neq y_i
\end{cases}
$$

Meaning:
1. Correctly classified samples are downweighted.
2. Misclassified samples keep their weight, so they gain relative importance after normalization.

### Step 9: Final output across all batches
$$
H_{\text{final}}(x)=\arg\max_{y\in Y}
\sum_{k=1}^{K}\sum_{t=1}^{T_k}
\mathbb{1}[h_t^{(k)}(x)=y]\log\left(\frac{1}{\beta_t^{(k)}}\right)
$$

Meaning:
1. Aggregate weighted votes from all accepted hypotheses across all incremental datasets.

## Where Classifier Equations Plug In
Use this map to connect Learn++ flow with model-specific math:
1. Step 2 calls base classifier equations from [CLASSIFIER_EQUATIONS_DETAILED.md](CLASSIFIER_EQUATIONS_DETAILED.md):
2. Decision Tree: impurity and information gain.
3. Gaussian Naive Bayes: class posterior from Gaussian likelihoods.
4. Logistic Regression: sigmoid plus cross-entropy.
5. Linear SVM: margin objective and hinge loss.
6. LDA: linear discriminant score.
7. MLP: layer transforms, softmax, cross-entropy.
8. Random Forest: tree-vote aggregation.
9. RBF SVM: kernel decision function.
10. Gradient Boosting: residual-based additive updates.
11. Encoder plus linear head: representation and softmax head.

## Where CompositeScore Belongs
CompositeScore is not part of the Learn++ training loop.

Use it after experiments are run to rank candidates:
$$
\text{CompositeScore}=
0.50\cdot\text{MacroF1}+0.20\cdot\text{BalancedAccuracy}
+0.10\cdot(1-\text{TrainTimeNorm})
+0.10\cdot(1-\text{InferenceTimeNorm})
+0.10\cdot(1-\text{MemoryNorm})
$$

## Suggested Reading Order
1. This flow map first.
2. [CLASSIFIER_EQUATIONS_DETAILED.md](CLASSIFIER_EQUATIONS_DETAILED.md) for symbol-by-symbol detail.
3. [CLASSIFIER_EQUATIONS_WORKED_EXAMPLES.md](CLASSIFIER_EQUATIONS_WORKED_EXAMPLES.md) for numeric walkthroughs.
4. [CLASSIFIER_EQUATIONS_SELF_CHECK.md](CLASSIFIER_EQUATIONS_SELF_CHECK.md) for quick mastery checks.
