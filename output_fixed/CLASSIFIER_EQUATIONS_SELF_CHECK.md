# Self-Check Exercises for Classifier Equations

## Purpose
This companion file helps you verify understanding by solving short equation tasks.

## How to Use
1. Try each question without looking at the solution.
2. Check only after you write your own steps.
3. If your answer differs, compare each intermediate value.

## Exercises

### Exercise 1: Learn++ weak error and beta
Given weighted sample distribution for one iteration:
1. D_t(1)=0.12
2. D_t(2)=0.08
3. D_t(3)=0.30
4. D_t(4)=0.10
5. D_t(5)=0.40

Suppose weak hypothesis h_t misclassifies samples 2 and 4.

Tasks:
1. Compute epsilon_t.
2. Compute beta_t.
3. Compute vote weight log(1/beta_t).

### Exercise 2: Learn++ weight update direction
Assume composite error E_t=0.2.

Tasks:
1. Compute B_t.
2. For a correctly classified sample, does weight increase or decrease?
3. For a misclassified sample, does weight change in this rule?

### Exercise 3: Gaussian Naive Bayes quick posterior
Binary classes A and B with priors P(A)=0.5 and P(B)=0.5.
For query x, unnormalized likelihood products are:
1. class A: 0.018
2. class B: 0.012

Tasks:
1. Compute normalized posteriors.
2. Pick predicted class.

### Exercise 4: Linear SVM hinge loss
Given y=+1 and f(x)=0.4.

Tasks:
1. Compute hinge loss max(0,1-yf(x)).
2. Is this point inside margin, on margin, or outside margin?

### Exercise 5: Gradient boosting update
Given current prediction F_0=10, weak learner output h_1(x)=-2, learning rate nu=0.1, and rho_1=1.

Tasks:
1. Compute F_1(x).
2. Did prediction move up or down?

### Exercise 6: CompositeScore
Use:
1. MacroF1=0.76
2. BalancedAccuracy=0.80
3. TrainTimeNorm=0.25
4. InferenceTimeNorm=0.50
5. MemoryNorm=0.30

Task:
1. Compute CompositeScore.

---

## Answer Key

### Solution 1
1. Misclassified indices are 2 and 4.
$$
\epsilon_t=D_t(2)+D_t(4)=0.08+0.10=0.18
$$
2. Beta:
$$
\beta_t=\frac{0.18}{1-0.18}=\frac{0.18}{0.82}=0.2195
$$
3. Vote weight:
$$
\log\left(\frac{1}{0.2195}\right)=\log(4.556)\approx1.516
$$

### Solution 2
1. $$B_t=\frac{E_t}{1-E_t}=\frac{0.2}{0.8}=0.25$$
2. Correctly classified sample: weight decreases (multiplied by 0.25).
3. Misclassified sample: weight stays unchanged in this update rule.

### Solution 3
Unnormalized scores:
1. A: 0.018
2. B: 0.012

Normalize with total 0.030:
1. $$P(A\mid x)=0.018/0.030=0.60$$
2. $$P(B\mid x)=0.012/0.030=0.40$$

Prediction: class A.

### Solution 4
1. Hinge:
$$
\max(0,1-yf(x))=\max(0,1-(+1)(0.4))=\max(0,0.6)=0.6
$$
2. Since yf(x)=0.4 < 1, point is inside margin.

### Solution 5
$$
F_1(x)=F_0+\nu\rho_1 h_1(x)=10+0.1*1*(-2)=9.8
$$
Prediction moved down.

### Solution 6
$$
\text{CompositeScore}=
0.50(0.76)+0.20(0.80)+0.10(1-0.25)+0.10(1-0.50)+0.10(1-0.30)
$$
$$
=0.38+0.16+0.075+0.05+0.07=0.735
$$

---

## Interpretation Tips
1. If you can compute exercises 1, 2, and 4 quickly, your Learn++ and SVM basics are strong enough for project meetings.
2. If exercise 6 is confusing, revisit difference between ranking math and classifier training math.
