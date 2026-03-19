---
title: "Making Natural Gradient Descent Practical"
date: 2025-05-18
paper_url: "https://arxiv.org/abs/2505.12149"
math: true
---

## Motivation

Physics-Informed Neural Networks (PINNs) are often hard to train because the optimization landscape is badly conditioned. Natural-gradient and second-order methods can help a lot, but the main obstacle is **cost**: the curvature matrix lives in parameter space, so solving the natural-gradient step can become prohibitively expensive for modern networks.

<!-- TODO: What was your personal experience training PINNs that led to this work? Was there a specific failure mode that motivated it? -->

This paper asks a practical question: **can we keep the optimization benefits of Energy Natural Gradient Descent (ENGD) while making it cheap enough to use routinely?** The answer is yes, through three ideas: compute the update in sample space with **Woodbury**, accelerate convergence with **SPRING-style momentum**, and optionally use **randomized Nyström approximations** when batches are very large.

## Key Ideas

### ENGD is a preconditioned gradient method

For PINNs, the loss can be written as a nonlinear least-squares problem built from PDE residuals and boundary residuals. ENGD updates parameters by solving a regularized linear system:

$$
\theta_{k+1}=\theta_k-\tau_k (G_k+\lambda I)^{-1} g_k,
$$

where $g_k$ is the gradient of the loss, $G_k$ is the ENGD curvature / Gauss–Newton-type matrix, $\lambda$ is damping, and $\tau_k$ is a step size. The problem is that $G_k\in\mathbb{R}^{P\times P}$, where $P$ is the number of parameters. Inverting or even solving with this matrix directly is expensive.

### The Woodbury trick: from parameter space to sample space

The curvature matrix has a low-rank form built from Jacobians: $G = J^\top J$ (up to weighting / PDE structure). Naively, solving with $G+\lambda I$ costs parameter-space linear algebra. But using the push-through / Woodbury identity,

$$
(J^\top J + \lambda I)^{-1}J^\top
= J^\top (J J^\top + \lambda I)^{-1},
$$

the solve rewrites in terms of the **kernel matrix** $K = J J^\top$, which lives in sample space. If the batch size $N$ is much smaller than the number of parameters $P$, then solving with $K\in\mathbb{R}^{N\times N}$ is dramatically cheaper than solving with $G\in\mathbb{R}^{P\times P}$.

This is the main scaling result: it computes the **exact same ENGD direction** up to numerical precision, but in the cheaper space.

### SPRING adds momentum in the natural-gradient geometry

Once ENGD is cheap enough to run, the next question is how to make it converge faster. We adapt the SPRING method from variational Monte Carlo. Instead of treating each step independently, SPRING transports curvature information across iterations and introduces a momentum-like residual correction:

$$
\Delta \theta_k \approx (G_k+\lambda I)^{-1}(g_k + \text{momentum correction}).
$$

The important practical point: SPRING removes the need for the expensive line search used by standard ENGD and still improves convergence.

### Randomization for large batches

After Woodbury, the main cost is no longer parameter space but the sample-space kernel matrix $K$. If the batch is very large, even $K$ becomes expensive. We study a randomized **Nyström approximation** $K \approx \widetilde K$, constructed from a low-rank sketch, so the natural-gradient solve becomes $(\widetilde K + \lambda I)^{-1}$ instead of the full kernel.

The engineering contribution here is a **GPU-friendly Nyström variant** that avoids expensive SVDs and uses Cholesky-based operations instead, which are much better aligned with current ML hardware.

## Results

The headline number: Woodbury alone delivers more than a **30× increase in iteration speed** on the 5D Poisson experiments, while also improving overall optimization performance. That is not a trade-off — it is strictly better on both axes.

Adding SPRING further improves final accuracy and performs particularly well on high-dimensional PDEs, reaching results competitive with or better than more complex alternatives such as K-FAC. Randomized Nyström methods help mainly in the early stages of low-dimensional, large-batch problems; in many settings, the exact Woodbury formulation remains the best overall choice.

<!-- TODO: Was the 30× speedup expected, or did it surprise you? Any other result that stood out? -->

## Takeaways

Natural gradient is powerful because it rescales the update by local curvature, but that curvature is usually too expensive to use. The key insight of this work is that in PINNs the curvature is not arbitrary — it comes from Jacobians of residuals, so it has hidden low-rank structure. Woodbury exposes that structure. SPRING then says: once we can afford these geometry-aware steps, we should also avoid starting from scratch every iteration and instead carry useful directional information forward.

The central identity tells the whole story:

$$
(J^\top J + \lambda I)^{-1}J^\top
= J^\top (J J^\top + \lambda I)^{-1}.
$$

This is what turns ENGD from a parameter-space method into a sample-space method. The paper's mathematical contribution is not a new natural gradient in principle; it is a new **computational factorization** that makes the existing geometry practical.
