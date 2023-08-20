---
layout: post
title:  "Architectures by symmetry"
date:   2023-08-18 09:00:00
mathjax: true
---

### Introduction

[Geometric deep learning](https://arxiv.org/abs/2104.13478) provides a framework for viewing and deriving architectures via symmetry. Namely, imposing invariances and equivariances on a system with respect to 
a group of symmetries brings us prominent architectures present in machine learning, such as transformers and CNNs, that are empirically highly effective. This post aims to condense the core ideas of this framework.
* The notion of using symmetry to derive models is significant in physics. Three out of the four fundamental forces can be derived this way; for example, the Maxwell equations for the electromagnetic force can be derived 
by imposing $$U(1)$$ symmetry, and this approach was discovered long after the empirical finding of the Maxwell equations via experiment.

### Setup

Say one wishes to create information processing systems that receive some data signal, and output some property of the data.

Notation:
* Data signals come from the space $$\mathcal{X}(\Omega, \mathcal{C}) := \{f: \Omega \to \mathcal{C}\}$$, the set of all functions from 
$$\Omega$$ to $$\mathcal{C}$$. $$\Omega$$ is called the **domain** and 
the dimensions of $$\mathcal{C}$$ are called channels.

	As a concrete example, 10x10 RGB images can be described with $$\Omega := \mathbb{Z}_{10} \times \mathbb{Z}_{10}$$ and $$\mathcal{C} := \mathbb{R}^3$$, assigning RGB values to each point in a grid.
		
	This is equivalent to representing the data as a tensor of type $$\mathbb{R}^{10 \times 10 \times 3}$$. But here, we make a seperation of the shape $$(10, 10, 3)$$ into $$(\Omega, \mathcal{C})$$. 
	The reason for this will become clearer, but we define our symmetries by a group $$G$$ that acts on $$\Omega$$, whereas $$\mathcal{C}$$ is not involved in these symmetries.


* Our system $$f: \mathcal{X}(\Omega, \mathcal{C}) \to \mathcal{Y}$$ takes a data signal, and returns a property of the signal.

Universal Approximation theorems tell us that we can choose $$f = f(x; \theta)$$ to be a single hidden layer NN and we can get arbitrarily close to any function for some choice of parameters $$\theta$$. But finding these parameters by optimization 
can require an infeasible number of data points from the true underlying data distribution. How do we circumvent this?

Luckily, when we are creating an information processing system, we know something beforehand about the **structure** of data it will receive and the **task** we wish to solve, and hence the **underlying symmetries** of the data signal domain $$\Omega$$. As seen in the example above, for 
classification of images, it is reasonable to expect that $$f$$ should output the same result even if the image is translated, since the object class will not change under such translation. 

So we know beforehand that $$f$$ should be invariant under certain transformations of its input signals, 
which **narrows the function space** we are optimizing over if we can properly encode this into the **architecture** of $$f$$.
* In contrast, a basic NN does not make any assumptions about symmetries of its input data, though the optimization process may impose its own biases.

The concept of built-in exploitation of symmetry into the model architecture is an example of an **inductive bias**. Regularization techniques are also inductive biases, with weight decay biasing towards low weight norms. But here we are just concerned about 
inductive biases built into the architecture of the system, not those built into the optimization process.
* Tangent propagation uses a regularizing term in the optimization objective to incentivize local invariances to transformations. Data augmentation also produces such an effect. However in geometric deep learning, the goal is to build 
these invariances into the functional form/architecture of the model itself.

	Sidenote (not important): an example of tangent propagation: for data $$x \in \mathbb{R}^n$$ and model $$y(x) \in \mathbb{R}^m$$ under transformations parameterized by, for convenience, a scalar $$\xi$$, then $$\xi \cdot x \in \mathbb{R}^n$$ is the transformed data point (with $$0 \cdot x = x$$). Then
		
	$$\frac{\partial y(\xi \cdot x)_i}{\partial \xi}\bigg\rvert_{\xi=0} = \sum_{j=1}^{n} \frac{\partial y(x)_i}{\partial x_j} \frac{\partial (\xi \cdot x)_j}{\partial \xi}\bigg\rvert_{\xi=0} =: \sum_{j=1}^{n} J_{ij} \tau_j$$

	with Jacobian $$J_{ij} := \frac{\partial y_i}{\partial x_j}$$ and tangent vector $$\tau := \frac{\partial (\xi \cdot x)}{\partial \xi}\rvert_{\xi=0}$$. Then tangent propagation includes a regularizing term

	$$\lambda \sum_{i=1}^{m} \left(\frac{\partial y(\xi \cdot x)_i}{\partial \xi}\bigg\rvert_{\xi=0}\right)^2 = \lambda \sum_{i=1}^{n} \left(\sum_{j=1}^{n} J_{ij} \tau_j\right)^2$$

	into the objective function to incentivize local invariances, with $$\lambda$$ chosen to balance this invariance effect.


### Framework

#### Group Actions

With knowledge of the kind of data we are working with, and the task we wish to solve, we can determine symmetries 
on the domain $$\Omega$$, and describe these symmetries by a group $$G$$.

$$G$$ acts on $$\Omega$$ via $$\bullet: G \times \Omega \to \Omega$$, and $$\bullet_g \in \text{Sym}(\Omega)$$ is the group action restricted to $$g \in G$$, with $$\bullet_g(u) = g \bullet u$$. 

We can define another group action $$\ast: G \times \mathcal{X}(\Omega, \mathcal{C}) \to \mathcal{X}(\Omega, \mathcal{C})$$ by $$g \ast x := x(g^{-1} \bullet \; \cdot \;) \in \mathcal{X}(\Omega, \mathcal{C})$$, now instead 
acting on a space of functions on $$\Omega$$ rather than $$\Omega$$ itself. This is a valid 
group action if $$\bullet$$ is a valid group action.
* $$g^{-1}$$ must be applied instead of $$g$$ so that the compositionality requirement of a group action, $$\ast_g \circ \ast_h = \ast_{gh}$$, holds.

#### Key Definitions

> **Definition:** $$f: \mathcal{X}(\Omega, \mathcal{C}) \to \mathcal{Y}$$ is called $$G$$-invariant if
> 
> $$f \circ \ast_g = f$$
> 
> $$\forall \; g \in G$$.

* Equivalent definition of $$G$$-invariance: $$f(x \circ \bullet_g) = f(x) \; \forall \; x \in \mathcal{X}(\Omega, \mathcal{C}), g \in G$$.
* $$G$$-invariance says that applying the action $$\bullet_g$$ onto the domain input $$u \in \Omega$$ does not effect the output of $$f$$.

> **Definition:** $$f: \mathcal{X}(\Omega, \mathcal{C}) \to \mathcal{X}(\Omega', \mathcal{C}')$$ is called $$G$$-equivariant if
> 
> $$f \circ \ast_g = \ast'_g \circ f$$
> 
> $$\forall \; g \in G$$, where $$\ast$$, $$\ast'$$ are the group actions of $$G$$ on $$\mathcal{X}(\Omega, \mathcal{C})$$, $$\mathcal{X}(\Omega', \mathcal{C}')$$ respectively.

* In the special case of $$\ast \equiv \ast'$$, $$G$$-equivariance says that $$f$$ and $$\ast_g$$ commute.

And two key properties:
1. $$G$$-equivariances are closed under composition, i.e. if $$A$$ and $$B$$ are $$G$$-equivariances, then $$A \circ B$$ is a $$G$$-equivariance.
2. If $$A$$ is a $$G$$-invariance, and $$B$$ is a $$G$$-equivariance, then $$A \circ B$$ is a $$G$$-invariance.

#### Pertubative Stability

There is a problem with a function $$f$$ having just $$G$$-invariance. It says nothing regarding how robust $$f$$ is to transformations that are close 
to the transformations of $$G$$, but not exactly in $$G$$.
* e.g. under a transformation of an image in a way that is **not exactly** an element of the translation group, but is very close to being a translation, we would except 
that $$f$$ still effectively acts invariantly under this transformation. But the definition of $$G$$-invariance says nothing about these small pertubations from $$G$$.
* The same is true for the definition of $$G$$-equivariance.

We can formalize the idea of $$f$$ being $$G$$-invariant while also being robust to pertubations outside of $$G$$ by 
the notion of **approximate invariance**. A **complexity measure** $$c: \text{Diff}(\Omega) \to \mathbb{R}^+$$ 
measures how 'close' a transformation $$\tau \in \text{Diff}(\Omega)$$ is to the transformations of a group $$G \subset \text{Diff}(\Omega)$$, with $$c(g) = 0 \; \forall \; g \in G$$. Then $$f$$ is 
**approximately invariant** if

$$\lVert f(\ast_{\tau}(x)) - f(x) \rVert \leq C c(\tau) \lVert x \rVert$$

$$\forall \; x \in \mathcal{X}(\Omega, \mathcal{C}), \tau \in \text{Diff}(\Omega)$$ for some constant $$C$$.

Then from the above definition, such an $$f$$ is both $$G$$-invariant (case of $$\tau \in G$$) and is also sufficiently stable to pertubations outside $$G$$, depending on the chosen complexity measure $$c$$.

Similarly, $$f$$ is called **approximately equivariant** if

$$\lVert f(\ast_{\tau}(x)) - \ast'_{\tau}(f(x)) \rVert \leq C c(\tau) \lVert x \rVert$$

$$\forall \; x \in \mathcal{X}(\Omega, \mathcal{C})$$, $$\tau \in \text{Diff}(\Omega)$$.

#### Locality

How do we achieve pertubative stability as described by approximate invariance/equivariance? 
One heuristic for achieving pertubative stability is that of **locality**.

Consider $$\Omega = \mathbb{R}$$ and group $$(\mathbb{R}, +)$$ with $$G = \mathbb{R}$$, acting as translations by 
$$\ast_v(x)(u) := x(u - v)$$. Also consider a transformation $$\tau \in \text{Diff}(\mathbb{R})$$, which has action $$\ast_{\tau}(x)(u) = x(u - \tilde{\tau}(u))$$, where 
$$\tilde{\tau}$$ is **not** a constant function, hence $$\tau$$ is not a translation. However, $$\lVert \nabla \tilde{\tau} \rVert_{\infty} \leq \epsilon$$ for some small $$\epsilon$$, hence 
$$\tau$$ can be said to be an **approximate translation**.

Let $$f(x) := |\hat{x}|$$, the Fourier modulus, with $$\hat{x}(\xi) = \int_{-\infty}^{\infty} x(u) e^{-i\xi u} du$$ the Fourier transform. Then since $$\hat{\ast_v(x)}(\xi) = \hat{x}(\xi) e^{-i \xi v}$$, we have $$f(\ast_v(x)) = |\hat{x}| \equiv f(x)$$, hence $$f$$ is $$G$$-invariant. However, it fails 
to be approximately invariant, as one can show that for the approximate translation $$\tau$$,

$$\frac{\lVert f(\ast_{\tau}(x)) - f(x) \rVert}{\lVert x \rVert} = \mathcal{O}(1)$$

i.e. it is **independent** of $$\epsilon > 0$$.

In constrast, let $$f(x) := W_{\psi}(x)(\cdot, \xi)$$ for a fixed $$\xi \in \mathbb{R}^+$$, with $$W_{\psi}(x)(u, \xi) = \xi^{-1/2} \int_{-\infty}^{\infty} \psi\left(\frac{v - u}{\xi}\right) x(v) dv$$, called the wavelet transform. Then 
it can be shown that

$$\frac{\lVert f(\ast_{\tau}(x)) - f(x) \rVert}{\lVert x \rVert} = \mathcal{O}(\epsilon)$$

hence $$f$$ is approximately equivariant.

How could this be interpreted? One way is to note that the Fourier transform extracts global properties, namely, frequencies featured in the signal, but does not 
give any local temporal information. And from above, the Fourier transform is not stable to pertubations outside $$G$$. 

On the other hand, wavelets provide a balance of global and local information, allowing one to gain information regarding frequencies at localised points in the signal, and from above, the wavelet transform is stable to pertubations. 

Hence, roughly, **locality** can be viewed as a principle that $$f$$ should follow to possibly give some stability to its invariances/equivariances. In the Architecture section, this locality 
property is often imposed, and it can be roughly justified by the above.

#### Building Invariances

We want our system $$f: \mathcal{X}(\Omega, \mathcal{C}) \to \mathcal{Y}$$ to be $$G$$-invariant. How do we go about achieving this?

If we restrict $$f$$ to be linear, then $$G$$-invariance implies that

$$
\begin{align*}
f(x) &= \frac{1}{\mu(G)} \int_G f(x) d\mu(g)\\
&= \frac{1}{\mu(G)} \int_G f(\ast_g(x)) d\mu(g) && \text{(by $G$-invariance)}\\
&= f\left(\frac{1}{\mu(G)} \int_G \ast_g(x) d\mu(g)\right) && \text{(by linearity)}\\
&=: f(\bar{x})
\end{align*}
$$

with $$\bar{x} := \frac{1}{\mu(G)} \int_G \ast_g(x) d\mu(g) \in \mathcal{X}(\Omega, \mathcal{C})$$ representing the average action signal of $$G$$ on signal $$x \in \mathcal{X}(\Omega, \mathcal{C})$$.

What this says is that $$f$$ linear and invariant means $$f$$ can only depend on $$\bar{x}$$, and $$\bar{x}$$ contains very little information about $$x$$ in some cases.
* e.g. for a 2D image $$x$$ and $$G$$ the group of translations, $$\bar{x}$$ will be the average pixel intensity across the whole image. Global pooling is an example of this, but is typically performed after many equivariant operations, to be discussed below.

Hence if we want $$f$$ to be invariant while being effective at a non-trivial task, we require that $$f$$ is non-linear. How do we construct an invariant, non-linear $$f$$?

If we have some non-linear equivariances $$\{f_i\}_{i=1}^{N}$$ and a (possibly linear) invariance $$A$$, we could produce a non-linear $$G$$-invariant system $$f$$ by

$$f := A \circ f_N \circ \cdots \circ f_1$$

where $$f$$ is $$G$$-invariant as a consequence of the two key properties noted at the end of the Key Definitions section.

Say instead we have some linear equivariances $$\{f_i\}_{i=1}^{N}$$ that we wish to use to build an invariant system. In order for $$f$$ to be expressive we want some non-linearity involved too. A useful result is that: 

If $$A: \mathcal{X}(\Omega, \mathcal{C}) \to \mathcal{X}(\Omega', \mathcal{C}')$$ is a $$G$$-equivariance, and $$\Sigma: \mathcal{X}(\Omega', \mathcal{C}') \to \mathcal{X}(\Omega', \mathcal{C}'')$$ is defined by $$\Sigma(x) := \sigma \circ x$$ with 
$$\sigma: \mathcal{C}' \to \mathcal{C}''$$ (e.g. a non-linearity), then $$\Sigma \circ A$$ is $$G$$-equivariant.
* This follows as $$\Sigma \circ A \circ \ast_g = \Sigma \circ \ast'_g \circ A$$ 
and $$\Sigma(\ast'_g(x)) = \sigma \circ x(g^{-1} \bullet \; \cdot \;) = \ast''_g(\sigma \circ x) = \ast''_g(\Sigma(x))$$ where 
$$\ast''_g$$ is just $$\ast'_g$$ but extended from $$\mathcal{X}(\Omega', \mathcal{C}')$$ to $$\mathcal{X}(\Omega', \mathcal{C}'')$$. This only 
works as $$\Sigma$$ only modifies the channel dimensions, and leaves the domain unchanged.

So we can choose some non-linearities $$\{\Sigma_i\}_{i=1}^{L}$$ and build a $$G$$-invariant $$f$$ by

$$f := A \circ \Sigma_N \circ f_N \circ \cdots \circ \Sigma_1 \circ f_1$$

* The convolution, seen in CNNs, is an example of a linear equivariance. Typically a non-linearity such as ReLU is applied after the convolution.

### Architectures

#### CNN

A general definition of the concept of a convolution is that of the 
**group convolution**.

> **Definition:** For a group $$G$$ and $$\theta \in \mathcal{X}(\Omega, \mathcal{C})$$, the group convolution $$C_{\theta}: \mathcal{X}(\Omega, \mathcal{C}) \to \mathcal{X}(G, \mathcal{C})$$ is defined by
> 
> $$C_{\theta}(x)(g) := \int_{\Omega} \langle x(u), \theta(g^{-1} \bullet u) \rangle_{\mathcal{C}} du$$
> 
> where $$\langle\cdot, \cdot\rangle_{\mathcal{C}}$$ is an inner product on $$\mathcal{C}$$.

The group convolution is special in that $$f: \mathcal{X}(G, \mathcal{C}) \to \mathcal{X}(G, \mathcal{C})$$ is $$G$$-equivariant and linear iff $$f$$ is a group convolution (see [Kondor, Trivedi (2018)](https://arxiv.org/abs/1802.03690)). So for functions on $$\mathcal{X}(G, \mathcal{C})$$, we can fully characterise 
linear $$G$$-equivariances.

In the case of $$f: \mathcal{X}(\Omega, \mathcal{C}) \to \mathcal{X}(G, \mathcal{C})$$ however, certain conditions must be met. If $$\Omega$$ is countable, or $$\Omega$$ uncountable with $$\det\left(\frac{\partial \bullet_g(u)}{\partial u}\right) = 1$$, 
then the group convolution is $$G$$-equivariant. The former result is proven below, and the latter is proven similarly.

> **Claim:** If $$\Omega$$ is countable, then for any $$\theta \in \mathcal{X}(\Omega, \mathcal{C})$$, the group convolution is $$G$$-equivariant. That is,
> 
> $$C_{\theta} \circ \ast_g = \ast'_g \circ C_{\theta}$$
> 
> $$\forall \; g \in G$$, where $$\ast'_g$$ is defined by action $$\bullet'_g: G \to G$$ with $$\bullet'_g(h) := gh$$.
> 
> 
> **Proof:** Since $$\Omega$$ is countable, the integral becomes a sum:
> 
> $$C_{\theta}(x)(g) = \sum_{u \in \Omega} \langle x(u), \theta(g^{-1} \bullet u) \rangle_{\mathcal{C}}$$
> 
> To show $$G$$-equivariance, we make use of the fact that $$\bullet_g \in \text{Sym}(\Omega)$$ to make the substitution $$v = h^{-1} \bullet u$$:
> 
> $$
> \begin{align*}
> C_{\theta}(\ast_h(x))(g) &= \sum_{u \in \Omega} \langle x(h^{-1} \bullet u), \theta(g^{-1} \bullet u) \rangle_{\mathcal{C}}\\
> &= \sum_{v \in \Omega} \langle x(v), \theta(g^{-1} \bullet (h \bullet v)) \rangle_{\mathcal{C}}\\
> &= \sum_{u \in \Omega} \langle x(u), \theta(g^{-1}h \bullet u) \rangle_{\mathcal{C}}
> \end{align*}
> $$
> 
> Compare this to the outer application of $$\ast'_h$$:
> 
> $$\ast'_h(C_{\theta}(x))(g) = C_{\theta}(x)(h^{-1}g) = \sum_{u \in \Omega} \langle x(u), \theta(g^{-1}h \bullet u) \rangle_{\mathcal{C}}$$
> 
> These are identical, and so
> 
> $$C_{\theta} \circ \ast_g = \ast'_g \circ C_{\theta}$$
> 
> $$\forall \; g \in G, \theta \in \mathcal{X}(\Omega, \mathcal{C})$$ as required, hence $$C_{\theta}$$ is $$G$$-equivariant.

Classical CNNs can be derived by choosing to use a **linear** equivariance wrt. translations, which 
is equivalently a convolution $$C_{\theta}$$ (since $$G = \Omega = \mathbb{Z}^2$$), and then imposing locality by choosing a 
localised filter $$\theta$$ such that $$C_{\theta}(x)((u, v)^T)$$ depends 
only on a small $$f_H \times f_W$$ grid in the original input. Since $$G$$ is the group of translations, the group convolution is

$$C_{\theta}(x)(u) = \sum_{v \in \Omega} x(v) \theta(u^{-1} \bullet v) = \sum_{v \in \Omega} x(v) \theta(v - u)$$

Let $$\Omega := \mathbb{Z}^2$$, with values outside of a $$H \times W$$ grid set to be $$0$$, and let $$\mathcal{C} = \mathbb{R}$$ for now. Then a localised filter $$\theta \in \mathcal{X}(\mathbb{Z}^2, \mathbb{R})$$ represented by a $$f_H \times f_W$$ grid can be written in the basis 
$$\{\theta_{1, 1}, \ldots, \theta_{f_H, f_W}\}$$ with $$\theta_{i, j}(u, v) := \delta(u - i, v - j)$$ like so:

$$\theta = \sum_{i=1}^{f_H} \sum_{j=1}^{f_W} w_{i, j} \theta_{i, j}$$

with $$\{w_{i, j}\}_{i, j} \subset \mathbb{R}$$ learnable.

Then since $$C$$ is linear wrt. $$\theta$$ (since $$C_{\alpha \theta_1 + \beta \theta_2} = \alpha C_{\theta_1} + \beta C_{\theta_2}$$), then the corresponding localised convolution is

$$C_{\theta} = \sum_{i=1}^{f_H} \sum_{j=1}^{f_W} w_{i, j} C_{\theta_{i, j}}$$

$$C_{\theta}$$ corresponds to a stride of 1, as

$$C_{\theta_{i, j}}(x)((u, v)^T) = \sum_{w \in \Omega} x(w) \theta_{i, j}(w - (u, v)^T) = x((u+i, v+j)^T)$$

and so

$$C_{\theta}(x)((u, v)^T) = \sum_{i=1}^{f_H} \sum_{j=1}^{f_W} w_{i, j} x((u+i, v+j)^T)$$

* A stride $$k > 1$$ convolution is not equivariant to all translations; only translations that step in multiples of $$k$$, i.e. $$G = (k\mathbb{Z})^2$$.

In the case of $$C$$ channels, with $$\mathcal{C} := \mathbb{R}^C$$, mapping to $$C'$$ channels, then by writing $$\theta \in \mathcal{X}(\mathbb{Z}^2, \mathbb{R}^C)$$ in the basis $$\{\theta_{1, 1, 1}, \ldots, \theta_{f_H, f_W, C}\}$$, the localised 
convolution is

$$C_{\theta}(x)((u, v)^T)_{c'} = \sum_{i=1}^{f_H} \sum_{j=1}^{f_W} \sum_{c=1}^{C} w_{i, j, c, c'} x((u+i, v+j)^T)_c$$

since there are $$C'$$ filters each of shape $$(f_H, f_W, C)$$.

#### GNN

Consider the case of the domain $$\Omega = V$$, where $$V$$ are the nodes of a graph $$\mathcal{G} = (V, E)$$, and $$\mathcal{C} = \mathbb{R}^d$$. 
Then the signal $$x \in \mathcal{X}(V, \mathbb{R}^d)$$ can be equivalently represented by a matrix $$X \in \mathbb{R}^{n \times d}$$, where $$n := |V|$$. Here we 
are assuming there are only node representations, and none for edges.

We can represent connection information by an adjacency matrix $$A \in \mathbb{R}^{n \times n}$$, with $$A_{ij} = 1$$ if $$(i, j) \in E$$, and $$A_{ij} = 0$$ otherwise.

Naturally we want the system to be $$G$$-invariant wrt. $$G = \text{Sym}(\{1, \ldots, n\})$$ such that an arbitrary renumbering of nodes has no effect. To build such invariances, we need 
$$G$$-equivariances. Specifically, for $$f: \mathbb{R}^{n \times d_{I}} \times \mathbb{R}^{n \times n} \to \mathbb{R}^{n \times d_{O}}$$ to be $$G$$-equivariant, we need

$$f(PX, PAP^T) = Pf(X, A)$$

$$\forall \; P \in S_n$$, where $$S_n$$ is the group of $$n \times n$$ permutation matrices.

One way of imposing locality here is to impose that $$f(X, A)_i \in \mathbb{R}^{d_{O}}$$ only depends on $$x_i$$ and $$X_{\mathcal{N}_i(A)} \in \mathbb{R}^{\vert \mathcal{N}_i(A)\vert \times d_I}$$, the node representations of neighbours of $$i$$. This means that

$$f(X, A)_i = \phi(x_i, X_{\mathcal{N}_i(A)})$$

In this case, $$\phi$$ must be **invariant** to permutations of the input neighbours, otherwise $$f$$ wont necessarily be equivariant. This means that we need

$$\phi(x_i, PX_{\mathcal{N}_i(A)}) = \phi(x_i, X_{\mathcal{N}_i(A)})$$

$$\forall \; P \in S_{\vert \mathcal{N}_i(A)\vert }$$.

One way of imposing that $$\phi$$ is invariant is to enforce that $$\phi$$ only depends on a permutation invariant operator across the neighbours, i.e.

$$\phi(x_i, X_{\mathcal{N}_i(A)}) = \phi\left(x_i, \bigoplus_{j \in \mathcal{N}_i(A)} \psi(x_i, x_j)\right)$$

which is the most general form of a GNN.
* Choosing $$\psi(x_i, x_j) = c_{ij} \tilde{\psi}(x_j)$$ gives convolutional GNNs.
* Choosing $$\psi(x_i, x_j) = a(x_i, x_j) \tilde{\psi}(x_j)$$ gives attentional GNNs.

#### Transformer

A text prompt with $$n$$ tokens can be represented as a graph $$\mathcal{G} = (V, E)$$ with $$V := \{1, \ldots, n\}$$ and with connectivity $$E$$ s.t. 
$$\mathcal{N}_i(A) := \{1, \ldots, i\} \; \forall \; i \in V$$, i.e. each token is connected to itself and all tokens before itself, but none after.

As for GNNs, $$X \in \mathbb{R}^{n \times d}$$ represents the tokens, with $$d$$ called the residual stream dimension in the context of transformers.

In the case of $$f: \mathbb{R}^{n \times d} \times \mathbb{R}^{n \times n} \to \mathbb{R}^{n \times d}$$ being a general GNN, i.e.

$$f(X, A)_i = \phi\left(x_i, \bigoplus_{j \in \mathcal{N}_i(A)} \psi(x_i, x_j)\right)$$

then by defining learnable parameters $$Q_h, K_h, V_h, O_h \in \mathbb{R}^{d_{\text{head}} \times d}$$ for each $$h = 1, \ldots, H$$, where $$d_{\text{head}}$$ is the head dimension, 
we can arrive at a transformer attention layer with $$H$$ heads by choosing
* $$\bigoplus := \sum$$,
* $$\psi(x_i, x_j) := \sum_{h=1}^{H} \hat{\psi}_h(x_i, x_j)$$,
* with $$\hat{\psi}_h(x_i, x_j) := a_h(x_i, x_j) \tilde{\psi}_h(x_j)$$ (attentional),
* $$\tilde{\psi}_h(x_j) := O_h^T V_h x_j$$,
* softmax-normalised attention scores

	$$a_h(x_i, x_j) := \text{softmax}(\{(Q_h x_i)^T K_h x_k : k \in \mathcal{N}_i(A)\})_j = \frac{e^{(Qx_i)^T Kx_j}}{\sum_{k \in \mathcal{N}_i(A)} e^{(Qx_i)^T Kx_k}}$$

* and $$\phi(x_i, y) := x_i + y$$ (residual connection)

These choices give

$$f(X, A)_i = x_i + \sum_{h=1}^{H} O_h^T\left(\sum_{j=1}^{i} a_h(x_i, x_j) V_h x_j\right)$$

which is exactly the attention layer of the GPT models.
* The choice $$\tilde{\psi}_h(x_j) := O_h^T V_h x_j$$ maps $$x_j$$ into a low-dimensional subspace, as $$\text{rank}(O_h^T V_h) \leq d_{\text{head}}$$ and typically $$d_{\text{head}} \ll d$$ is chosen. Choosing 
$$\tilde{\psi}_h(x_j) := W_h x_j$$ instead would allow for mapping into a space of higher dimension than $$d_{\text{head}}$$. 

#### RNN gating

An RNN has an update equation of the form

$$h_{t+1} := R(z_t, h_t)$$

* The SimpleRNN chooses $$R(z_t, h_t) := \text{tanh}(Wz_t + Uh_t + b)$$.

By undiscretizing by $$h_{t+1} \mapsto h(t+1)$$, and using a Taylor expansion of 

$$h(t+1) \approx h(t) + \frac{dh(t)}{dt}$$

we can get a continuous update rule:

$$h(t) + \frac{dh(t)}{dt} = R(z(t), h(t))$$

Consider a **time warping** $$\tau: \mathbb{R}^+ \to \mathbb{R}^+$$, which is monotone increasing, i.e. $$\frac{d\tau(t)}{dt} > 0$$. It turns out that the gating mechanism present in GRUs and LSTMs can be derived 
by requiring that the RNN model class is invariant to such time warping operations.

* The concept of a model class being invariant can be seen as a generalization of $G$-invariance. Model class invariance of model class $$M$$ 
says that for any $$f \in M$$, $$f \circ \tau = g$$ for some $$g \in M$$. $$G$$-invariance is the case of $$f \equiv g$$.
	TODO: rewrite this point

First make a substitution $$t \mapsto \tau(t)$$:

$$\frac{dh(\tau(t))}{d\tau(t)} = R(z(\tau(t)), h(\tau(t))) - h(\tau(t))$$

And using the chain rule,

$$\frac{dh(\tau(t))}{dt} = \frac{dh(\tau(t))}{d\tau(t)} \frac{d\tau(t)}{dt} = \frac{d\tau(t)}{dt}(R(z(\tau(t)), h(\tau(t))) - h(\tau(t)))$$

Using a Taylor expansion

$$h(\tau(t+1)) \approx h(\tau(t)) + \frac{dh(\tau(t))}{dt}$$

(assuming that $$\frac{d\tau(t)}{dt} < 1$$), and defining $$\Gamma := \frac{d\tau(t)}{dt}$$, 
this can be rewritten as

$$h(\tau(t+1)) = \Gamma R(z(\tau(t)), h(\tau(t))) + (1 - \Gamma)h(\tau(t))$$

Since the time warping $$\tau$$ is unknown, the inputs received are effectively $$z_t := z(\tau(t))$$. This also means 
that $$\Gamma$$ should be **learnable**.

If we now impose that the model class is invariant to time warping, such that $$h(\tau(t)) = h(t)$$, and then discretizing, we obtain:

$$h_{t+1} = \Gamma R(z_t, h_t) + (1 - \Gamma)h_t$$

And since $$0 < \Gamma < 1$$, it is natural to choose $$\Gamma$$ to be of the form of the SimpleRNN model, but instead with sigmoid $$\sigma: \mathbb{R} \to [0, 1]$$ to match the range of $$\Gamma$$. Then,

$$\Gamma := \sigma(Wz_t + Uh_t + b)$$

which is exactly the expression for a gate in the GRU and LSTM models.