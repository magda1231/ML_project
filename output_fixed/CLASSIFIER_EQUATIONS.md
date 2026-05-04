# Classifier and Learn++ Equations

## Purpose
This file centralizes readable equations used to:
1. Understand Learn++ mechanics.
2. Understand each shortlisted classifier family.
3. Distinguish ranking equations from model equations.

## Notation
1. $x_i$: feature vector for sample $i$.
2. $y_i$: true class label for sample $i$.
3. $h_t$: weak hypothesis generated at Learn++ iteration $t$.
4. $H_t$: composite hypothesis after combining hypotheses up to iteration $t$.
5. $D_t(i)$: normalized sample weight distribution at iteration $t$.
6. $m$: number of samples in the current batch.
7. $\mathbb{1}[\cdot]$: indicator function (1 if condition is true, 0 otherwise).

## Learn++ Core Equations

### 1) Weak hypothesis error
$$
\epsilon_t = \sum_{i: h_t(x_i) \neq y_i} D_t(i), \qquad 0 < \epsilon_t < \frac{1}{2}
$$

### 2) Normalized hypothesis error
$$
\beta_t = \frac{\epsilon_t}{1 - \epsilon_t}, \qquad 0 < \beta_t < 1
$$

### 3) Composite vote at iteration $t$
$$
H_t(x) = \arg\max_{y \in Y} \sum_{j=1}^{t} \mathbb{1}[h_j(x)=y] \log\left(\frac{1}{\beta_j}\right)
$$

### 4) Composite hypothesis error
$$
E_t = \sum_{i=1}^{m} D_t(i)\,\mathbb{1}[H_t(x_i) \neq y_i]
$$

### 5) Composite normalized error
$$
B_t = \frac{E_t}{1 - E_t}
$$

### 6) Weight update rule
$$
w_{t+1}(i) =
\begin{cases}
w_t(i)\,B_t, & \text{if } H_t(x_i)=y_i \\
w_t(i), & \text{otherwise}
\end{cases}
$$

### 7) Final Learn++ hypothesis across all batches
$$
H_{\text{final}}(x) = \arg\max_{y \in Y}
\sum_{k=1}^{K}\sum_{t=1}^{T_k}
\mathbb{1}[h_t^{(k)}(x)=y]\log\left(\frac{1}{\beta_t^{(k)}}\right)
$$

## Project Ranking Equation (Selection Only)
This equation ranks candidates by quality and compute cost. It does not define how a classifier itself works.

$$
\text{CompositeScore} =
0.50\,\text{MacroF1} +
0.20\,\text{BalancedAccuracy} +
0.10\,(1-\text{TrainTimeNorm}) +
0.10\,(1-\text{InferenceTimeNorm}) +
0.10\,(1-\text{MemoryNorm})
$$

## Classifier Equations by Candidate Family

### Decision Tree (weak)
Impurity options:
$$
\text{Gini}(S) = 1 - \sum_{c=1}^{C} p(c\mid S)^2
$$
$$
\text{Entropy}(S) = -\sum_{c=1}^{C} p(c\mid S)\log_2 p(c\mid S)
$$
Split criterion (information gain style):
$$
\text{IG}(S,\text{split}) = I(S) - \sum_{v \in \text{children}} \frac{|S_v|}{|S|}I(S_v)
$$

### Gaussian Naive Bayes (weak)
$$
P(y\mid x) \propto P(y)\prod_{j=1}^{d}
\frac{1}{\sqrt{2\pi\sigma_{y,j}^2}}
\exp\left(-\frac{(x_j-\mu_{y,j})^2}{2\sigma_{y,j}^2}\right)
$$
Prediction:
$$
\hat y = \arg\max_{y} P(y\mid x)
$$

### Logistic Regression (weak)
Binary form:
$$
P(y=1\mid x) = \sigma(w^T x + b) = \frac{1}{1+e^{-(w^T x + b)}}
$$
Cross-entropy loss:
$$
\mathcal{L} = -\sum_{i=1}^{m}\left[y_i\log \hat p_i + (1-y_i)\log(1-\hat p_i)\right]
$$

### Linear SVM / SGD Hinge (weak)
Decision function:
$$
f(x)=w^T x + b, \qquad \hat y=\operatorname{sign}(f(x))
$$
Soft-margin objective:
$$
\min_{w,b,\xi}\; \frac{1}{2}\|w\|^2 + C\sum_{i=1}^{m}\xi_i
\quad
\text{s.t. } y_i(w^T x_i + b) \ge 1-\xi_i,\; \xi_i\ge0
$$

### LDA (weak)
Class discriminant function:
$$
\delta_k(x)=x^T\Sigma^{-1}\mu_k - \frac{1}{2}\mu_k^T\Sigma^{-1}\mu_k + \log\pi_k
$$
Prediction:
$$
\hat y = \arg\max_k \delta_k(x)
$$

### MLP (small or medium)
Layer update:
$$
h^{(\ell)} = \phi\left(W^{(\ell)}h^{(\ell-1)} + b^{(\ell)}\right)
$$
Output and loss:
$$
\hat y = \operatorname{softmax}(W^{(L)}h^{(L-1)} + b^{(L)}),
\qquad
\mathcal{L} = -\sum_{i=1}^{m}\sum_{c=1}^{C} y_{ic}\log \hat y_{ic}
$$

### Random Forest (strong)
Ensemble vote:
$$
\hat y = \operatorname{mode}\{T_b(x)\}_{b=1}^{B}
$$
Class probability estimate:
$$
\hat p(c\mid x)=\frac{1}{B}\sum_{b=1}^{B}\mathbb{1}[T_b(x)=c]
$$

### RBF SVM (strong)
Kernel:
$$
K(x,x') = \exp\left(-\gamma\|x-x'\|^2\right)
$$
Decision function:
$$
f(x)=\sum_{i=1}^{m}\alpha_i y_i K(x_i,x)+b
$$

### Gradient Boosting Family (strong)
Additive model:
$$
F_0(x)=\arg\min_{\gamma}\sum_{i=1}^{m}L(y_i,\gamma)
$$
At boosting stage $m$:
$$
r_{im}=-\left[\frac{\partial L(y_i,F(x_i))}{\partial F(x_i)}\right]_{F=F_{m-1}}
$$
$$
F_m(x)=F_{m-1}(x)+\nu\,\rho_m\,h_m(x)
$$

### Feature encoder + linear head (strong)
Representation and prediction:
$$
z=f_{\theta}(x),
\qquad
\hat y = \operatorname{softmax}(Wz+b)
$$
Loss:
$$
\mathcal{L} = -\sum_{c=1}^{C} y_c\log \hat y_c
$$

## Minimum Equation Set for Fast Decision Meetings
If time is limited, review these first:
1. Learn++ equations: $\epsilon_t$, $\beta_t$, $H_t$, and weight update.
2. One equation per weak family: Decision Tree impurity, Naive Bayes posterior, Linear SVM objective.
3. One equation per strong family: Random Forest vote, RBF kernel, Boosting additive update.
4. CompositeScore equation for final ranking only.
