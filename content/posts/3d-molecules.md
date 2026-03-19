---
title: "Is 3D a Step Too Far for Optimizing Molecules?"
date: 2024-12-14
paper_url: "https://openreview.net/pdf?id=AnPEfzBstD"
math: true
---

## Motivation

Molecules are intrinsically 3D objects, so it is tempting to assume that **3D molecular representations** should dominate 1D and 2D representations in Bayesian optimization for materials discovery. In practice, however, chemists often use SMILES strings, fingerprints, or 2D graph features instead.

<!-- TODO: What prompted you to question the 3D assumption? Was there a result in the lab or a conversation that sparked this investigation? -->

Instead of proposing yet another molecular model, we decided to study the question empirically and mathematically through the lens of **Bayesian optimization (BO)**: what matters is not just predictive accuracy, but whether a representation helps a surrogate model find high-value molecules in as few expensive evaluations as possible.

## Key Ideas

### Sample-efficient black-box search

Bayesian optimization seeks the maximizer of an expensive objective

$$
x^* = \arg\max_{x\in \mathcal{X}} f(x),
$$

where $x$ is a molecule and $f(x)$ is some property of interest (for example, a quantum or materials property). Because evaluating $f$ is expensive, BO builds a probabilistic surrogate $p(f\mid \mathcal{D}_t)$ from observations $\mathcal{D}_t = \{(x_i, f(x_i))\}_{i=1}^t$ and uses that surrogate to decide what to evaluate next.

So the representation question becomes: **which representation lets the surrogate model the objective well enough to optimize it quickly?**

### Better geometry is not automatically better BO

3D features may contain more geometric information, but that does not automatically imply better BO performance. A richer representation can also create a harder statistical problem: it may need more data to estimate reliably, increase model complexity and computational cost, and its extra information may be irrelevant for the target property. We evaluate this trade-off across 1D, 2D, and 3D feature extractors rather than assuming that more physical realism is always better.

### The surrogate's uncertainty matters

We compare Gaussian processes and neural-network surrogates with a **linearized Laplace approximation (LLA)**. After fitting a neural network at parameters $\theta^*$, one places a Gaussian approximation around that mode:

$$
p(\theta\mid \mathcal{D}) \approx \mathcal{N}(\theta^*, \Sigma_*^{-1}),
$$

with covariance determined by the Hessian of the negative log posterior. Linearizing the network around $\theta^*$ yields an approximate predictive distribution

$$
f(x) \sim \mathcal{N}\big(f(x;\theta^*),\; J(x;\theta^*)\Sigma_*^{-1}J(x;\theta^*)^\top\big),
$$

where $J(x;\theta^*)$ is the Jacobian of the network output with respect to parameters. This matters because BO needs calibrated uncertainty, not just accurate point predictions.

### Measuring what BO actually cares about

To compare methods across datasets, we use the normalized GAP metric

$$
\mathrm{GAP}_i = \frac{y_i - y_0}{y^* - y_0},
$$

where $y_i$ is the best value found by step $i$, $y_0$ is the initial best value, and $y^*$ is the true optimum in the search set. This directly measures **how fast a method closes the gap to the optimum** — which is what BO is actually for.

## Results

Across the benchmark, 1D and especially 2D representations usually outperform 3D representations in Bayesian optimization. The aggregate results show that 3D models do **not** generally justify their additional computational cost.

One of the most striking findings: 3D models are dramatically more sample-hungry. On simpler datasets they often need more than **10,000 observations** before they begin to close the gap to strong 2D baselines — a prohibitive number in the small-data regime where BO is meant to operate.

Transfer learning helps somewhat, but not enough to reverse the main conclusion. In short, richer geometric detail does not automatically translate into better optimization performance.

<!-- TODO: Was there a specific dataset or comparison where the result surprised you? Did you expect 3D to do better in any particular setting? -->

## Takeaways

The intuition is simple: **3D is only useful if the optimization loop can afford to learn from it**. A 3D model may be more physically faithful, but BO lives in a small-data regime where every evaluation is precious. If the surrogate needs far more data and computation before the extra geometry pays off, then 3D loses in practice.

The paper's key contribution is not a new theorem or optimizer, but a **decision-theoretic reframing**: the relevant object is not the raw expressive power of a representation — it is the induced posterior quality inside a BO loop, and that quality must be judged by optimization progress, not by representation faithfulness alone.

In many materials-discovery settings, 2D already captures most of the signal that matters for ranking and searching candidates, while 3D adds cost faster than it adds useful decision-making power. The LLA formula captures why: uncertainty quality depends on the interaction between representation, model class, and available data. A more complex representation can worsen this interaction when data are limited.
