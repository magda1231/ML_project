# Worked Numeric Examples for Classifier Equations

## Purpose
This companion file shows concrete numeric examples so equations become easier to read and apply.

## How to Use This File
1. Read one example at a time.
2. Recompute numbers with a calculator.
3. Compare your results to the shown result.

## Example 1: Learn++ Error, Beta, Voting Weight, and Weight Update

### Setup
Suppose one batch has 5 samples with current normalized weights:

| i | D_t(i) | true label y_i | h_t(x_i) |
|---|---:|---:|---:|
| 1 | 0.10 | 1 | 0 |
| 2 | 0.20 | 0 | 0 |
| 3 | 0.15 | 1 | 0 |
| 4 | 0.25 | 0 | 0 |
| 5 | 0.30 | 1 | 1 |

Misclassified samples are i=1 and i=3.

### Step A: Weak hypothesis error
$$
\epsilon_t = \sum_{i: h_t(x_i) \neq y_i} D_t(i) = D_t(1)+D_t(3)=0.10+0.15=0.25
$$

Interpretation:
1. Error is 0.25, so this weak hypothesis is acceptable because 0.25 < 0.5.

### Step B: Normalized error
$$
\beta_t = \frac{\epsilon_t}{1-\epsilon_t} = \frac{0.25}{0.75}=0.3333
$$

### Step C: Vote weight
$$
\log\left(\frac{1}{\beta_t}\right)=\log(3)=1.0986
$$

Interpretation:
1. Lower beta gives higher vote weight.

### Step D: Composite error and update factor
Assume current composite classifier H_t misclassifies only sample i=3.

$$
E_t = D_t(3)=0.15
$$
$$
B_t = \frac{E_t}{1-E_t}=\frac{0.15}{0.85}=0.1765
$$

### Step E: Unnormalized weight update
Rule:
1. If H_t(x_i)=y_i, multiply by B_t.
2. If H_t(x_i) != y_i, keep weight unchanged.

Given only i=3 is wrong:

| i | old w_t(i) | update rule | new w_{t+1}(i) |
|---|---:|---|---:|
| 1 | 0.10 | correct -> multiply by 0.1765 | 0.01765 |
| 2 | 0.20 | correct -> multiply by 0.1765 | 0.03530 |
| 3 | 0.15 | wrong -> unchanged | 0.15000 |
| 4 | 0.25 | correct -> multiply by 0.1765 | 0.04413 |
| 5 | 0.30 | correct -> multiply by 0.1765 | 0.05295 |

Sum is 0.30003 (rounding). Normalize to get next D_{t+1}:

| i | D_{t+1}(i) approx |
|---|---:|
| 1 | 0.0588 |
| 2 | 0.1176 |
| 3 | 0.4999 |
| 4 | 0.1471 |
| 5 | 0.1765 |

Key insight:
1. Sample i=3 now carries about half of the probability mass, so next weak learner focuses on it.

---

## Example 2: Gaussian Naive Bayes Posterior

### Setup
Binary classes A and B, two features x1 and x2.

Priors:
1. P(A)=0.6
2. P(B)=0.4

Class Gaussian parameters:
1. Class A: mu_A1=2, sigma_A1^2=1, mu_A2=1, sigma_A2^2=1
2. Class B: mu_B1=0, sigma_B1^2=1, mu_B2=0, sigma_B2^2=1

Query sample: x=(1,0)

Useful Gaussian values:
1. N(1; mean 2, var 1)=0.2420
2. N(0; mean 1, var 1)=0.2420
3. N(1; mean 0, var 1)=0.2420
4. N(0; mean 0, var 1)=0.3989

### Step A: Unnormalized class scores
$$
\text{score}_A = P(A) \cdot N(x_1;2,1) \cdot N(x_2;1,1)
$$
$$
\text{score}_A = 0.6 \cdot 0.2420 \cdot 0.2420 = 0.0351
$$

$$
\text{score}_B = P(B) \cdot N(x_1;0,1) \cdot N(x_2;0,1)
$$
$$
\text{score}_B = 0.4 \cdot 0.2420 \cdot 0.3989 = 0.0386
$$

### Step B: Normalize to posterior
Total score = 0.0351 + 0.0386 = 0.0737

$$
P(A\mid x)=\frac{0.0351}{0.0737}=0.476
$$
$$
P(B\mid x)=\frac{0.0386}{0.0737}=0.524
$$

Prediction:
1. Choose class B because posterior is larger.

---

## Example 3: Linear SVM Decision and Hinge Loss

### Setup
Take:
1. w=(1.2,-0.8)
2. b=-0.3
3. C=1

Samples:
1. x_a=(2,1), y_a=+1
2. x_b=(0.2,1.8), y_b=-1
3. x_c=(1,1), y_c=+1

### Step A: Compute decision function f(x)=w^T x + b
1. f(x_a)=1.2*2 + (-0.8)*1 - 0.3 = 1.3
2. f(x_b)=1.2*0.2 + (-0.8)*1.8 - 0.3 = -1.5
3. f(x_c)=1.2*1 + (-0.8)*1 - 0.3 = 0.1

Predictions via sign(f):
1. x_a -> +1 (correct)
2. x_b -> -1 (correct)
3. x_c -> +1 (correct sign, but weak margin)

### Step B: Hinge losses max(0, 1 - y_i f(x_i))
1. For x_a: 1 - (+1)*1.3 = -0.3 -> hinge 0
2. For x_b: 1 - (-1)*(-1.5) = -0.5 -> hinge 0
3. For x_c: 1 - (+1)*0.1 = 0.9 -> hinge 0.9

Total hinge = 0.9

Regularizer:
$$
\frac{1}{2}\|w\|^2 = \frac{1}{2}(1.2^2 + (-0.8)^2)=\frac{1}{2}(2.08)=1.04
$$

Objective estimate:
$$
\frac{1}{2}\|w\|^2 + C\sum_i \xi_i \approx 1.04 + 0.9 = 1.94
$$

Key insight:
1. Even with correct sign, low-margin points still contribute training loss.

---

## Example 4: Gradient Boosting Stage Update (Squared Error)

### Setup
Two samples with targets y=[3,5].

For squared error, a common initialization is mean target:
$$
F_0 = \frac{3+5}{2}=4
$$

### Step A: Residuals at stage 1
For squared error, residual is y - prediction.
1. r_1 = 3 - 4 = -1
2. r_2 = 5 - 4 = +1

Suppose weak learner h_1 predicts [-1, +1] exactly.

### Step B: Additive update
Take learning rate nu=0.3 and step size rho_1=1.

$$
F_1(x)=F_0(x)+\nu\rho_1 h_1(x)=4+0.3h_1(x)
$$

New predictions:
1. For sample 1: 4 + 0.3*(-1)=3.7
2. For sample 2: 4 + 0.3*(+1)=4.3

New residuals:
1. 3 - 3.7 = -0.7
2. 5 - 4.3 = +0.7

Key insight:
1. Each stage moves predictions toward targets gradually.
2. Smaller nu means slower but often safer learning.

---

## Example 5: CompositeScore Comparison

### Equation
$$
\text{CompositeScore}=
0.50\cdot\text{MacroF1}
+0.20\cdot\text{BalancedAccuracy}
+0.10\cdot(1-\text{TrainTimeNorm})
+0.10\cdot(1-\text{InferenceTimeNorm})
+0.10\cdot(1-\text{MemoryNorm})
$$

### Candidate A
1. MacroF1=0.82
2. BalancedAccuracy=0.80
3. TrainTimeNorm=0.60
4. InferenceTimeNorm=0.30
5. MemoryNorm=0.40

Score A:
$$
0.50(0.82)+0.20(0.80)+0.10(0.40)+0.10(0.70)+0.10(0.60)
=0.41+0.16+0.04+0.07+0.06=0.74
$$

### Candidate B
1. MacroF1=0.78
2. BalancedAccuracy=0.83
3. TrainTimeNorm=0.20
4. InferenceTimeNorm=0.20
5. MemoryNorm=0.30

Score B:
$$
0.50(0.78)+0.20(0.83)+0.10(0.80)+0.10(0.80)+0.10(0.70)
=0.39+0.166+0.08+0.08+0.07=0.786
$$

Result:
1. Candidate B wins by CompositeScore because it is much more efficient.
2. This shows why ranking is not equivalent to MacroF1 only.

---

## Quick Recap
1. Learn++ equations show how sample emphasis shifts over time.
2. Classifier equations show how each model computes scores or updates.
3. CompositeScore is a decision layer, not model-training math.
