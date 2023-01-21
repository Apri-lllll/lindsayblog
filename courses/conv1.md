# CMU 10-725 Convex Optimization Notes - 1

**Theory 1: Fundamentals**



>This series of posts will summarize key points from the course [Convex Optimization](https://www.stat.cmu.edu/~ryantibs/convexopt-F18/). I plan on doing a total of 5 posts following the large topics given in the course schedule:
>
>- Theory 1: Fundamentals
>- Algorithms 1: First-Order Methods
>- Theory 2: Optimality and Duality
>- Algorithms 2: Second-Order Methods
>- Advanced Topics
>
> I am reviewing the material mainly to prepare for my finals for the course Operations Research, so I might put extra emphasis on overlapping topics.



### Introduction

Optimization is an important topic in statistics as well as the core problem of most machine learning tasks. The goal of studying optimization is to familiarize ourselves with different algorithms and how they perform under different problem settings.

For instance, in the 2d total variation denoising problem formulated as follows:

![0117-1](/images/0117-1.png)

![0117-1](/images/0117-2.png)

The ADMM method achieves good results and converges fast because the problem consists of structured subproblems. On the contrary, the proximal gradient method and the coordinate descent method both perform poorly, due to the poor conditioning and large active set of the problem.

Numerous taxonomies can be used to classify optimization problems. An intuitive consideration is linearity. However, researchers soon noticed that linearity is not an effective indicator of the difficulty of a problem - some nonlinear problems can be much more challenging to solve than others. It is now widely recognized that a more reasonable criterion is convexity, since nonconvex problems are generally harder than convex problems. This course mainly focuses on convex problems.



### Convexity

**Convex Sets**

- examples of convex sets:
  - empty set
  - hyperplane
  - half-space
  - affine space
  - simplex
- operations preserving convexity:
  - intersection
  - affine images & pre-images
  - scaling & translation
  - perspective images & pre-images
  - linear-fractional images & pre-images
- separating hyperplane theorem: two disjoint convex sets have a separating hyperplane between them
  - converse: if two convex sets are separated by a hyperplane, one of them is closed and the other is open, then the two sets are disjoint
- supporting hyperplane theorem: a boundary point of a convex set has a supporting hyperplane passing through it
  - if a non-empty set is closed, and has a supporting hyperplane at every boundary point, then it is a convex set

**Convex Hulls & Cones**

**Convex Functions**

- modifications
  - strictly convex: same definition but with strict inequalities; has greater curvature than a linear function
  - strongly convex with parameter *m>0*: *f − m/2 · norm(x)^2* is convex; has greater curvature than a quadratic function
- examples of convex functions
  - quadratic functions where *Q>=0* (positive semi-definite)
  - least squares (expands to a quadratic function where Q is always semi-definite)
  - all norms
  - indicator function of a convex set
- operations preserving convexity
  - non-negative linear combination
  - pointwise maximization
  - partial minimization
- compositions preserving convexity
  - affine
  - if *h* is convex, *g* is convex non-decreasing, then their composition *g·h* is convex; if *g* is non-increasing, their composition is concave

**Properties of Convex Functions**

![0117-1](/images/0117-3.png)



### Optimization Basics

**Properties of Convex Problems:**

- The solution set of a convex problem is convex. (Proved by the definition of convex sets & convex problems)
- The solution of a strongly convex problem is unique. (Proof by contradiction & the definition of strong convexity)

Examples:

1. Lasso

![0117-1](/images/0121-1.png)

The problem is convex. If *X* has full column rank, *X^TX* is positive definite, and the problem is strictly convex. Therefore, the solution is unique. On the other hand, if *X* doesn't have full column rank, there can be multiple solutions.

2. SVM

![0117-1](/images/0121-2.png)

The problem is convex. Since the second term is linear, the problem is not strictly convex in general. However, strict convexity applies to *beta* (the objective is strictly convex w.r.t. *beta*).

**First Order Optimality Conditions**

![0117-1](/images/0121-3.png)

**Transformations and Changes of Variables**

Transformations under monotone increasing functions retain the solution set. For monotonically increasing *h(x)*, min *f(x)* has the same solution set as min *h(f(x))*. This property can be used for:

- Discovering hidden convexity: For many exponential distributions, the original distribution is not convex, but taking logarithm leads to convexity.
- Change of variables, most often with linear transformations.

**Slack Variables**

Slack variables can be used to eliminate inequalities. For instance:

![0117-1](/images/0121-4.png)

However, this transformation may cause the problem to loose convexity, since *g(x)+s=0* is not necessarily convex. Slack variables are often used in linear problems. In such cases, *g(x)* is affine, therefore the problem retains convexity.

**Relaxations**

Relaxation refers to modifying the original problem to form one with looser constraints. The optimal value of the relaxed problem is always better than or equal to that of the original. A tight relaxation refers to when the solution of the new problem is a feasible point for the original.

- Relaxing non-affine equality constraints: Replacing non-affine equalities with inequalities may lead to a problem becoming convex.



### Canonical Problem Forms

The following graph illustrates the relationship between different types of conic programs.

- Conic programs belong to the larger set of convex problems; convex problems belong to the set of non-convex problems.
- Second-order cone programs are between QPs and SDPs.

![0117-1](/images/0121-5.png)

The following section covers each of these types of problems.

**Linear Programs (LPs)**

![0121-6](/images/0121-6.png)

The two forms are equivalent, and the general form can be transformed into standard form using slack variables. The constraints define the feasible region, which is a polytype (the optimum always lies in a corner of the polytype).

- Examples: Diet Problem, Basic Pursuit, Danzig Selector.

**Convex Quadratic Programs (QPs)**

![0121-6](/images/0121-7.png)

The above forms are only convex when *Q* is positive semi-definite. Transferring between the two forms can be similarly achieved through slack variables.

- Examples: Portfolio Optimization, SVM, Lasso

**Semi-Definite Programs (SDPs)**

![0121-6](/images/0121-8.png)

SDPs are a generalization of LPs.

- Examples: Trace Norm Minimalization

**Conic Programs**

![0121-6](/images/0121-9.png)