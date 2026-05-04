# Classifier Equations Detailed Guide

## Purpose
This file explains every important equation used in your planning docs, with explicit meaning for each letter and symbol.

## Scope
1. Learn++ core equations.
2. Composite ranking equation used for model selection.
3. Per-classifier equations for all shortlisted weak and strong families.

## Companion Files
1. CLASSIFIER_EQUATIONS_WORKED_EXAMPLES.md: numeric worked examples for key equations.
2. CLASSIFIER_EQUATIONS_SELF_CHECK.md: short exercises with answer key.
3. LEARNPP_EQUATION_FLOW_MAP.md: visual map of where each Learn++ equation is applied.

## Reading Guide
1. First read the equation block.
2. Then read the symbol table directly below it.
3. Then read the plain-language meaning and why it matters.

## Global Symbols and Shapes
Use this legend unless a section says otherwise.

| Symbol | Meaning | Typical shape or type |
|---|---|---|
| $i$ | sample index | integer, $1,\dots,m$ |
| $j$ | feature index | integer, $1,\dots,d$ |
| $k$ | class index or dataset index (context-dependent) | integer |
| $t$ | Learn++ iteration index inside a batch | integer |
| $m$ | number of samples in current dataset or batch | integer |
| $d$ | number of features | integer |
| $C$ | number of classes | integer |
| $K$ | number of incremental datasets or batches | integer |
| $T_k$ | number of Learn++ iterations for dataset $k$ | integer |
| $x_i$ | feature vector of sample $i$ | $\mathbb{R}^d$ |
| $y_i$ | true label of sample $i$ | class ID in $\{1,\dots,C\}$ |
| $\hat y$ | predicted class label | class ID |
| $Y$ | set of all class labels | finite set |
| $\mathbb{1}[\cdot]$ | indicator function | 1 if true, else 0 |

---

## Part A: Learn++ Equations (Detailed)

### A1) Distribution normalization
$$
D_t(i)=\frac{w_t(i)}{\sum_{r=1}^{m} w_t(r)}
$$

Symbol explanation:
1. $w_t(i)$: unnormalized weight of sample $i$ at iteration $t$.
2. $D_t(i)$: normalized probability-like weight after dividing by total weight.
3. $r$: summation index over all samples.
4. $\sum_{r=1}^{m} w_t(r)$: total weight mass across samples.

Plain meaning:
1. Raw weights are converted into a valid distribution.
2. Samples with higher $D_t(i)$ are more likely to be emphasized in the next weak learner step.

Why this matters:
1. Learn++ focuses on difficult samples by shifting distribution mass toward them.

### A2) Weak hypothesis error
$$
\epsilon_t = \sum_{i: h_t(x_i)\neq y_i} D_t(i), \qquad 0<\epsilon_t<\frac{1}{2}
$$

Symbol explanation:
1. $h_t$: weak hypothesis built at iteration $t$.
2. $h_t(x_i)$: class predicted by weak learner for sample $i$.
3. $y_i$: true class of sample $i$.
4. $D_t(i)$: current normalized weight for sample $i$.
5. $\epsilon_t$: weighted error of weak hypothesis $h_t$.

Plain meaning:
1. Add weights only for the samples this weak learner gets wrong.
2. If many high-weight samples are misclassified, $\epsilon_t$ is large.

Why the bound matters:
1. Learn++ expects a weak learner better than random for binary settings.
2. If $\epsilon_t\ge\frac{1}{2}$, the hypothesis is usually rejected and retried.

### A3) Hypothesis normalized error
$$
\beta_t = \frac{\epsilon_t}{1-\epsilon_t}, \qquad 0<\beta_t<1
$$

Symbol explanation:
1. $\epsilon_t$: weighted error from A2.
2. $\beta_t$: transformed error that controls voting strength.

Plain meaning:
1. Smaller $\epsilon_t$ gives smaller $\beta_t$.
2. Smaller $\beta_t$ means larger vote weight via $\log(1/\beta_t)$ later.

Why this matters:
1. Better weak hypotheses contribute more strongly to the composite decision.

### A4) Composite vote at iteration $t$
$$
H_t(x)=\arg\max_{y\in Y}\sum_{j=1}^{t}\mathbb{1}[h_j(x)=y]\log\left(\frac{1}{\beta_j}\right)
$$

Symbol explanation:
1. $x$: input sample being classified.
2. $H_t(x)$: composite prediction after combining hypotheses $1$ through $t$.
3. $\arg\max_{y\in Y}$: choose class $y$ with largest total vote.
4. $j$: index over prior weak hypotheses.
5. $\mathbb{1}[h_j(x)=y]$: contributes 1 only if hypothesis $j$ votes for class $y$.
6. $\log(1/\beta_j)$: vote weight for hypothesis $j$.

Plain meaning:
1. Each weak model votes for one class.
2. Better weak models get stronger vote weights.
3. Final class is the one with largest weighted vote.

Why this matters:
1. This is where ensemble strength emerges from multiple weak learners.

### A5) Composite hypothesis error
$$
E_t=\sum_{i=1}^{m} D_t(i)\,\mathbb{1}[H_t(x_i)\neq y_i]
$$

Symbol explanation:
1. $H_t(x_i)$: ensemble prediction on sample $i$.
2. $\mathbb{1}[H_t(x_i)\neq y_i]$: 1 if ensemble misses sample $i$.
3. $E_t$: weighted error of current composite hypothesis.

Plain meaning:
1. Same idea as A2, but now for the ensemble, not one weak learner.

Why this matters:
1. Learn++ updates sample emphasis based on composite performance.

### A6) Composite normalized error
$$
B_t=\frac{E_t}{1-E_t}
$$

Symbol explanation:
1. $E_t$: composite weighted error from A5.
2. $B_t$: transformed composite error used in weight update.

Plain meaning:
1. Converts composite error into a scaling factor for sample weights.

Why this matters:
1. Drives how aggressively correctly-classified samples are downweighted.

### A7) Weight update rule
$$
w_{t+1}(i)=
\begin{cases}
w_t(i)B_t, & \text{if } H_t(x_i)=y_i \\
w_t(i), & \text{if } H_t(x_i)\neq y_i
\end{cases}
$$

Symbol explanation:
1. $w_t(i)$: current unnormalized weight of sample $i$.
2. $w_{t+1}(i)$: next-iteration weight.
3. $B_t$: factor from A6, usually in $(0,1)$.

Plain meaning:
1. If sample $i$ is correctly classified by the composite model, reduce its weight by multiplying with $B_t$.
2. If sample $i$ is misclassified, keep weight unchanged.

Why this matters:
1. Hard samples become relatively more important at next iteration.

### A8) Final hypothesis across incremental datasets
$$
H_{\text{final}}(x)=\arg\max_{y\in Y}
\sum_{k=1}^{K}\sum_{t=1}^{T_k}
\mathbb{1}[h_t^{(k)}(x)=y]\log\left(\frac{1}{\beta_t^{(k)}}\right)
$$

Symbol explanation:
1. $k$: dataset or batch index in incremental learning.
2. $t$: hypothesis index inside dataset $k$.
3. $h_t^{(k)}$: hypothesis $t$ trained on dataset $k$ process.
4. $\beta_t^{(k)}$: normalized error term associated with that hypothesis.
5. $H_{\text{final}}$: final prediction after all incremental datasets.

Plain meaning:
1. Same weighted voting principle, but now across all batches seen over time.

Why this matters:
1. Enables incremental learning without retraining on all old data at once.

---

## Part B: CompositeScore Equation (Selection Rule)

### B1) Ranking equation used in planning
$$
\text{CompositeScore}=
0.50\cdot\text{MacroF1}
+0.20\cdot\text{BalancedAccuracy}
+0.10\cdot(1-\text{TrainTimeNorm})
+0.10\cdot(1-\text{InferenceTimeNorm})
+0.10\cdot(1-\text{MemoryNorm})
$$

Symbol explanation:
1. $\text{MacroF1}$: class-balanced harmonic summary of precision and recall.
2. $\text{BalancedAccuracy}$: average recall across classes.
3. $\text{TrainTimeNorm}$: normalized training time in $[0,1]$ (higher means slower).
4. $\text{InferenceTimeNorm}$: normalized prediction latency in $[0,1]$.
5. $\text{MemoryNorm}$: normalized memory usage in $[0,1]$.
6. Coefficients $0.50,0.20,0.10,0.10,0.10$: metric importance weights.

Plain meaning:
1. This score balances quality and resource efficiency.
2. Higher score is better.

Important note:
1. This equation ranks candidates.
2. It does not describe the internal training math of any classifier.

---

## Part C: Weak Classifier Equations (Detailed)

### C1) Decision Tree

Gini impurity:
$$
\text{Gini}(S)=1-\sum_{c=1}^{C} p(c\mid S)^2
$$

Entropy impurity:
$$
\text{Entropy}(S)=-\sum_{c=1}^{C} p(c\mid S)\log_2 p(c\mid S)
$$

Information gain style split score:
$$
\text{IG}(S,\text{split})=I(S)-\sum_{v\in\text{children}}\frac{|S_v|}{|S|}I(S_v)
$$

Symbol explanation:
1. $S$: current node sample set.
2. $C$: number of classes.
3. $p(c\mid S)$: fraction of samples in $S$ belonging to class $c$.
4. $I(\cdot)$: impurity measure (Gini or Entropy).
5. $v$: child node index after a split.
6. $S_v$: subset of samples routed to child $v$.
7. $|S|,|S_v|$: number of samples.

Plain meaning:
1. Good split reduces impurity strongly.
2. Tree chooses splits with large information gain.

### C2) Gaussian Naive Bayes
$$
P(y\mid x)\propto P(y)\prod_{j=1}^{d}
\frac{1}{\sqrt{2\pi\sigma_{y,j}^2}}
\exp\left(-\frac{(x_j-\mu_{y,j})^2}{2\sigma_{y,j}^2}\right)
$$

Prediction rule:
$$
\hat y=\arg\max_y P(y\mid x)
$$

Symbol explanation:
1. $P(y)$: prior probability of class $y$.
2. $j$: feature index.
3. $x_j$: feature $j$ value for current sample.
4. $\mu_{y,j}$: mean of feature $j$ for class $y$.
5. $\sigma_{y,j}^2$: variance of feature $j$ for class $y$.
6. $d$: number of features.

Plain meaning:
1. Each feature contributes an independent Gaussian likelihood per class.
2. Multiply all feature likelihoods and class prior, then pick highest posterior.

### C3) Logistic Regression (binary form)
$$
P(y=1\mid x)=\sigma(w^T x+b)=\frac{1}{1+e^{-(w^T x+b)}}
$$

Cross-entropy loss:
$$
\mathcal{L}=-\sum_{i=1}^{m}\left[y_i\log \hat p_i+(1-y_i)\log(1-\hat p_i)\right]
$$

Symbol explanation:
1. $w$: weight vector for features.
2. $b$: bias term.
3. $w^T x+b$: linear score (logit).
4. $\sigma(\cdot)$: sigmoid function mapping real values to $(0,1)$.
5. $\hat p_i$: predicted probability for sample $i$ being class 1.
6. $\mathcal{L}$: loss minimized during training.

Plain meaning:
1. Model learns a linear boundary in feature space.
2. Probabilities come from sigmoid-transformed linear score.

### C4) Linear SVM / Hinge formulation
Decision:
$$
f(x)=w^T x+b,\qquad \hat y=\operatorname{sign}(f(x))
$$

Soft-margin objective:
$$
\min_{w,b,\xi}\;\frac{1}{2}\|w\|^2+C\sum_{i=1}^{m}\xi_i
\quad
\text{s.t. } y_i(w^T x_i+b)\ge 1-\xi_i,\;\xi_i\ge0
$$

Symbol explanation:
1. $w,b$: hyperplane parameters.
2. $\|w\|^2$: margin regularization term.
3. $\xi_i$: slack variable for margin violations.
4. $C$: trade-off between margin size and training errors.
5. Constraint $y_i(w^T x_i+b)\ge 1-\xi_i$: enforces margin with allowed slack.

Plain meaning:
1. SVM finds a separating hyperplane with maximum margin.
2. Slack allows imperfect separation while controlling overfitting.

### C5) LDA
$$
\delta_k(x)=x^T\Sigma^{-1}\mu_k-\frac{1}{2}\mu_k^T\Sigma^{-1}\mu_k+\log\pi_k
$$

Prediction:
$$
\hat y=\arg\max_k \delta_k(x)
$$

Symbol explanation:
1. $\mu_k$: class-$k$ mean vector.
2. $\Sigma$: shared covariance matrix across classes.
3. $\Sigma^{-1}$: inverse covariance.
4. $\pi_k$: prior probability of class $k$.
5. $\delta_k(x)$: discriminant score for class $k$.

Plain meaning:
1. LDA assumes class-conditional Gaussians with equal covariance.
2. Choose class with highest linear discriminant score.

### C6) Small or Medium MLP
Hidden layer update:
$$
h^{(\ell)}=\phi\left(W^{(\ell)}h^{(\ell-1)}+b^{(\ell)}\right)
$$

Output and loss:
$$
\hat y=\operatorname{softmax}(W^{(L)}h^{(L-1)}+b^{(L)}),
\qquad
\mathcal{L}=-\sum_{i=1}^{m}\sum_{c=1}^{C} y_{ic}\log \hat y_{ic}
$$

Symbol explanation:
1. $\ell$: layer index.
2. $h^{(\ell)}$: hidden representation at layer $\ell$.
3. $W^{(\ell)},b^{(\ell)}$: layer parameters.
4. $\phi$: nonlinear activation function.
5. $L$: final layer index.
6. $\hat y_{ic}$: predicted probability for sample $i$ and class $c$.
7. $y_{ic}$: one-hot target indicator.

Plain meaning:
1. MLP repeatedly transforms input through affine map plus nonlinearity.
2. Final softmax gives class probabilities.

---

## Part D: Strong Classifier Equations (Detailed)

### D1) Random Forest
Majority vote:
$$
\hat y=\operatorname{mode}\{T_b(x)\}_{b=1}^{B}
$$

Class probability:
$$
\hat p(c\mid x)=\frac{1}{B}\sum_{b=1}^{B}\mathbb{1}[T_b(x)=c]
$$

Symbol explanation:
1. $B$: number of trees.
2. $T_b(x)$: class predicted by tree $b$.
3. $\operatorname{mode}$: most frequent class among tree votes.
4. $\hat p(c\mid x)$: fraction of trees voting for class $c$.

Plain meaning:
1. Many randomized trees vote together.
2. Aggregation reduces variance and improves robustness.

### D2) RBF SVM
Kernel:
$$
K(x,x')=\exp\left(-\gamma\|x-x'\|^2\right)
$$

Decision function:
$$
f(x)=\sum_{i=1}^{m}\alpha_i y_i K(x_i,x)+b
$$

Symbol explanation:
1. $x'$: another sample used in kernel comparison.
2. $\|x-x'\|^2$: squared Euclidean distance.
3. $\gamma$: kernel width control, larger means narrower influence radius.
4. $\alpha_i$: dual coefficient learned by SVM.
5. $b$: bias term.

Plain meaning:
1. Kernel maps similarity without explicit high-dimensional transform.
2. Prediction combines weighted support vectors.

### D3) Gradient Boosting Family
Initial model:
$$
F_0(x)=\arg\min_{\gamma}\sum_{i=1}^{m}L(y_i,\gamma)
$$

Pseudo-residuals at stage $m$:
$$
r_{im}=-\left[\frac{\partial L(y_i,F(x_i))}{\partial F(x_i)}\right]_{F=F_{m-1}}
$$

Additive update:
$$
F_m(x)=F_{m-1}(x)+\nu\rho_m h_m(x)
$$

Symbol explanation:
1. $F_m(x)$: ensemble prediction after stage $m$.
2. $L(\cdot)$: chosen loss function.
3. $r_{im}$: negative gradient target for sample $i$ at stage $m$.
4. $h_m(x)$: weak learner fitted at stage $m$.
5. $\rho_m$: step size found for stage $m$.
6. $\nu$: learning rate shrinkage factor.

Plain meaning:
1. Each stage adds a learner that corrects current model errors.
2. Learning rate controls how much each stage changes the ensemble.

### D4) Feature Encoder + Linear Head
Representation and classifier:
$$
z=f_{\theta}(x),
\qquad
\hat y=\operatorname{softmax}(Wz+b)
$$

Cross-entropy:
$$
\mathcal{L}=-\sum_{c=1}^{C} y_c\log \hat y_c
$$

Symbol explanation:
1. $f_{\theta}(\cdot)$: encoder with parameters $\theta$.
2. $z$: learned representation (embedding).
3. $W,b$: linear classifier head parameters.
4. $\hat y_c$: predicted probability of class $c$.
5. $y_c$: target indicator for class $c$.

Plain meaning:
1. Encoder extracts useful features from raw input.
2. Linear head maps those features to class probabilities.

---

## Part E: Equation Usage in Your Current Project

## E1) Which equations explain classifier internals?
1. All equations in Parts C and D.
2. Learn++ equations in Part A explain ensemble update and voting behavior.

## E2) Which equation is only for model ranking?
1. CompositeScore in Part B.

## E3) Suggested minimum for team discussions
1. A2, A3, A4, A7 for Learn++ logic.
2. C1, C2, C4 for weak-model intuition.
3. D1, D2, D3 for strong-model intuition.
4. B1 for final decision ranking.

## E4) Common confusion to avoid
1. High CompositeScore does not mean mathematically complex model.
2. Classifier equations define how predictions are produced or optimized.
3. CompositeScore only combines measured outputs from experiments.
