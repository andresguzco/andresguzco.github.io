---
title: "Flow Matching Meets Exponential Families for Tabular Data"
date: 2025-06-08
paper_url: "https://arxiv.org/abs/2506.05940"
math: true
---

## Motivation

Tabular data is awkward for modern generative modeling because a single table usually mixes **continuous**, **binary**, and **categorical** variables. Standard flow matching works naturally in continuous spaces, but a table is not a single Euclidean object: each column has its own geometry and its own natural loss.

<!-- TODO: What originally motivated you to work on this? Was there a specific frustration or failed experiment that sparked the idea? -->

Our goal was to make flow matching work **natively** on mixed-type tables rather than forcing everything into a continuous encoding. The key insight: treat each variable through an appropriate **exponential-family distribution** — Gaussian for continuous columns, Bernoulli for binary columns, Categorical for discrete ones — giving one common mathematical language for heterogeneous data.

## Key Ideas

### Flow matching as transport

Flow matching learns a time-dependent velocity field $v_t(x)$ that transports a simple source distribution $p_0$ into the data distribution $p_1$. Samples evolve by the ODE

$$
\frac{d x_t}{dt} = v_t(x_t).
$$

Instead of simulating the ODE during training, flow matching regresses a model $v_\theta(x,t)$ toward a target conditional velocity field. This is what makes flow matching efficient.

### The variational view

Variational Flow Matching (VFM) replaces an intractable posterior over endpoints with a tractable variational distribution $q_\phi(z\mid x_t,t)$. For tabular data, this is ideal: the variational distribution can be chosen to match the type of each column.

Under the mean-field factorization we use,

$$
q_\phi(z\mid x_t,t)=\prod_{j=1}^d q_{\phi,j}(z_j\mid x_t,t),
$$

so each column is modeled with its own univariate family while the network still conditions jointly on the whole table.

### Exponential families make the objective column-aware but unified

An exponential-family distribution has the form

$$
q_\phi(z\mid x_t,t)
= h(z)\exp\big(\eta_\phi(x_t,t)^\top T(z)-A(\eta_\phi(x_t,t))\big),
$$

where $T(z)$ are sufficient statistics, $\eta$ are natural parameters, and $A(\eta)$ is the log-partition function. This one formula covers Gaussian, Bernoulli, and categorical distributions. The core observation is that when VFM uses an exponential-family variational distribution, the optimization reduces to **matching sufficient statistics**.

### Training reduces to moment matching

The gradient of the VFM objective depends on the gap between the sufficient statistics under the posterior path and those predicted by the model:

$$
\nabla_\phi \mathcal{L}
\propto
\big(\mathbb{E}_{p}[T(z)]-\mathbb{E}_{q_\phi}[T(z)]\big)
\,\nabla_\phi \eta_\phi.
$$

Optimization stops when the model matches the right moments. For a Gaussian column, this recovers a squared-error style objective. For a categorical column, it recovers cross-entropy-like behavior. The framework automatically induces the "right" loss for each type.

### The Bregman divergence connection

One of the most elegant results: once the variational family is exponential, the training objective becomes equivalent to minimizing the **Bregman divergence** induced by the convex dual of the log-partition function. This explains *why* Gaussian columns lead to MSE-like losses and categorical columns to cross-entropy-like losses — both are special cases of the same geometry. It unifies what would otherwise look like ad hoc per-column loss choices into a single principled framework.

## Results

We introduce **TabbyFlow**, built on this Exponential Family Variational Flow Matching (EF-VFM) framework, and it achieves state-of-the-art performance on standard tabular benchmarks against strong baselines.

<!-- TODO: Is there a specific benchmark number or comparison you'd like to highlight? A figure reference? -->

The empirical takeaway is not only that the method works, but that it works **without breaking the mixed nature of the data**. The approach is competitive on fidelity and downstream utility because it models each feature with the right probabilistic object instead of using a one-size-fits-all continuous relaxation.

<!-- TODO: What result surprised you most? Was there a dataset or baseline where you didn't expect this approach to win? -->

## Takeaways

The cleanest intuition behind this work: **don't force every column to look the same; force every column to obey the right probability law.**

A good table generator should not treat "income," "clicked ad," and "country" as three real numbers living in the same geometry. EF-VFM says: each column should move through time according to its own natural statistical family, while the network coordinates these motions jointly. Training simply asks whether the model is reproducing the right moments at each time.

The central mathematical move is:

$$
q_\phi(z\mid x_t,t)
= h(z)\exp\big(\eta_\phi^\top T(z)-A(\eta_\phi)\big)
\quad\Longrightarrow\quad
\nabla_\phi \mathcal{L}
\text{ is a statistics-matching gradient.}
$$

That turns a potentially messy mixed discrete/continuous learning problem into a single principle: **predict the correct sufficient statistics along the probability path**. That is why the method is both principled and practical.
