---
layout: post
title:  "Constructing quantum field theories"
date:   2025-06-20 09:00:00
mathjax: true
---

$$\newcommand{\slashed}[1]{\rlap{\,/}#1}$$

The aim of this post is to motivate and demonstrate the guiding principles that underlie the construction of quantum field theories, and particularly the Standard Model -- arguably one of the most successful scientific theories of all time.

Notation: We will denote the space of smooth maps between a manifold $$M$$ and a vector space $$V$$ by $$C^{\infty}(M, V)$$. For a functional $$F: C^{\infty}(M, V) \to \mathbb{R}$$, we will denote its functional derivative at $$f \in C^{\infty}(M, V)$$ evaluated at point $$x \in M$$ by $$\frac{\delta F[f]}{\delta f(x)} \in \mathbb{R}$$. We will denote the space of invertible linear maps from $$V$$ to $$V$$ by $$\text{GL}(V)$$.

**Introduction.** In quantum field theory, a theory is described by a functional $$S: \mathcal{C} \to \mathbb{R}$$ called the action, mapping field configurations $$\Psi \in \mathcal{C}$$ to a real number $$S[\Psi]$$. $$\Psi$$ will generally be made up of a collection of fields $$\Psi = (\Psi_1, \ldots, \Psi_N)$$ relevant to our theory. With an action, we can integrate over the space of field configurations $$\mathcal{C}$$ via the measure

$$\text{D}\Psi \, \mathbb{P}[\Psi] \equiv \left[\prod_{i=1}^{N} \text{D}\Psi_i\right] \mathbb{P}[\Psi], \qquad \text{with} \quad \mathbb{P}[\Psi] := \frac{1}{Z} e^{-S[\Psi]}$$

with probability density $$\mathbb{P}: \mathcal{C} \to [0, \infty)$$, defining the normalization constant

$$Z = \int_{\mathcal{C}} \text{D}\Psi \, e^{-S[\Psi]}$$

often called the *partition function* (we will discuss the meaning of $$\text{D}\Psi$$ below). Explicitly, the field configuration space will take the form

$$\begin{align*}
\mathcal{C} &= C^{\infty}(M, V^{(1)}) \times \cdots \times C^{\infty}(M, V^{(N)})\\
&\cong C^{\infty}(M, V)
\end{align*}$$

with $$\Psi_i \in C^{\infty}(M, V^{(i)})$$ for a (finite-dimensional) vector space $$V^{(i)}$$ and spacetime manifold $$M$$, and defining $$V := V^{(1)} \oplus \cdots \oplus V^{(N)}$$.

Physical quantities that can be measured via experiment -- such as scattering probabilities, as demonstrated by the LSZ formula (see Appendix A.2) -- are directly related to field expectances, also called *correlators*, that take the general form

$$\mathbb{E}_{\Psi \sim S}[\Psi_{i_1}(x_1) \cdots \Psi_{i_n}(x_n)] := \frac{1}{Z} \int_{\mathcal{C}} \text{D}\Psi \, \Psi_{i_1}(x_1) \cdots \Psi_{i_n}(x_n) e^{-S[\Psi]}$$

for arbitrary $$n \in \mathbb{Z}_{+}$$, indices $$(i_1, \ldots, i_n) \in \{1, \ldots, N\}^n$$, and points $$(x_1, \ldots, x_n) \subset M$$. Given that such expectances are directly related to physical quantities, an important constraint will be for $$S$$ to be such that these expectances are invariant under transformations that leave $$\Psi$$ physically equivalent (e.g. Lorentz transformations), which we will soon make precise.

Constructing a well-behaved definition of the path integral measure $$\text{D}\Psi$$ is non-trivial. The approach that we will use is to expand each field $$\Psi_i \in C^{\infty}(M, V^{(i)})$$ in an eigenfunction basis $$\{\psi_i^j\}_j$$ of $$C^{\infty}(M, V^{(i)})$$, letting us write $$\Psi_i(x) = \sum_j a_i^j \psi_i^j(x)$$ for expansion coefficients $$\{a_i^j\}_j$$ and allowing us to define

$$\text{D}\Psi_i := \prod_j da_i^j$$

but since this is an infinite product, it will generally result in divergences, requiring some form of *regulation*, either via Fujikawa regulation (as used when computing anomalies, as in Section 4), or by truncating the infinite product via a cutoff (as used in the context of Wilsonian renormalization, as in Section 8), or some other regulation method.

To motivate the guiding principles behind the construction of our theory $$S$$, we first must introduce some central concepts. First, we should view the configuration space $$\mathcal{C}$$ as exhibiting *physical redundancy*, containing many configurations that are physically equivalent. In particular, we can think of there as being some true non-redundant physical configuration space $$\mathcal{P}$$, and with $$\mathcal{C}$$ being partitioned as

$$\mathcal{C} = \bigcup_{\Phi \in \mathcal{P}} [\Phi]$$

for equivalence classes $$[\Phi] = \{\Phi' \in \mathcal{C}: \Phi \sim \Phi'\}$$ defined by a physical equivalence relation $$\sim$$ over $$\mathcal{C}$$. Said differently, we have

$$\mathcal{P} \cong \mathcal{C}/\sim$$

The physical equivalence relation $$\sim$$ will be defined by the orbits of a collection of *Lie groups* $$(G^{(0)}, G^{(1)}, \ldots, G^{(K)})$$ (i.e. groups that are also manifolds), consisting of a *spacetime* symmetry group $$G^{(0)}$$ inherent to the particular manifold and metric $$(M, g)$$ under consideration, as well as a collection of *gauge* symmetry groups $$(G^{(1)}, \ldots, G^{(K)})$$. We will denote the overall symmetry group by $$G := G^{(0)} \times G^{(1)} \times \cdots \times G^{(K)}$$.
* Physical equivalence to orbits of the spacetime group $$G^{(0)}$$ is reasonably intuitive since (as discussed in Section 1) it essentially describes coordinate transformations that leave the metric invariant (isometries), and it is reasonable to expect that physical predictions should be independent of such choices. However, physical equivalence under the gauge groups is less intuitive. It turns out that the choice $$(G^{(1)}, G^{(2)}, G^{(3)}) = (U(1), SU(2), SU(3))$$ agrees extraordinarily closely with our universe, however the reason why appears unknown. As we will see in detail, this choice for gauge groups is a particularly simple choice you can make while satisfying Equation 1 and 2, and defines the Standard Model.

In order to define this physical equivalence relation $$\sim$$ over $$\mathcal{C}$$ using these groups, we require *representations* of these groups that describe how they actually act on the field content $$\Psi$$. Concretely, the representation of $$G^{(k)}$$ acting on $$\Psi_i \in C^{\infty}(M, V^{(i)})$$ will be denoted $$\rho^{(i, k)}: G^{(k)} \to \text{GL}(V^{(i, k)})$$, where each representation is assigned its own *sector* $$V^{(i, k)}$$ of $$V^{(i)}$$ on which to act, with

$$V^{(i)} = \underbrace{V^{(i, 0)}}_{\text{spacetime sector}} \oplus \underbrace{V^{(i, 1)} \oplus \cdots \oplus V^{(i, K)}}_{\text{gauge sectors}}$$

Namely, though $$\Psi_i$$ lives in an infinite-dimensional space $$C^{\infty}(M, V^{(i)})$$, the representations that we will consider will only act on (a subspace of) the finite-dimensional output space $$V^{(i)}$$. The overall action of $$G$$ on $$\Psi_i$$ is described by $$\rho^{(i)} := \rho^{(i, 0)} \oplus \rho^{(i, 1)} \oplus \cdots \oplus \rho^{(i, K)}$$, with $$\rho^{(i)}: G \to \text{GL}(V^{(i)})$$.

With such representations, we can now define a physical equivalence relation $$\sim$$. Recall that we can view $$\mathcal{C} = C^{\infty}(M, V)$$. Then we can condense the representations introduced above into a single representation $$\rho := \rho^{(1)} \oplus \cdots \oplus \rho^{(N)}$$, where $$\rho: G \to \text{GL}(V)$$, allowing us to denote the overall transformation of field content by $$\Psi \mapsto \rho_g \Psi$$ under $$g = (g_0, \ldots, g_K) \in G$$. This lets us define the physical equivalence class associated with $$\Psi \in \mathcal{C}$$ to be

$$[\Psi] := \{\rho_{g} \Psi: g \in C^{\infty}(M, G)\} \subset \mathcal{C}$$

where note that $$\rho_{g} \Psi \in C^{\infty}(M, V)$$ with $$[\rho_{g} \Psi](x) \equiv \rho_{g(x)} \Psi(x) \in V$$. This provides us with a physical equivalence relation on $$\mathcal{C}$$:

$$\Psi \sim \Psi' \iff \Psi' \in [\Psi]$$

* The fact that we are integrating over a highly physically-redundant configuration space $$\mathcal{C}$$ within our expectances can have some problems, namely regarding cases where the redundancy results in divergences of the path integral. This is a problem that we explore in Section 7, and comment on in Appendix A.4.

**Guiding principles.** With these concepts introduced, we can now formulate the central guiding principles to constructing physical theories in this framework.

The main principle that we will work towards satisfying is *invariance* of correlators:

$$\mathbb{E}_{\Psi \sim S}\left[[\rho^{(i_1)}_{g} \Psi_{i_1}](x_1) \cdots [\rho^{(i_n)}_{g} \Psi_{i_n}](x_n)\right] = \mathbb{E}_{\Psi \sim S}[\Psi_{i_1}(x_1) \cdots  \Psi_{i_n}(x_n)] \quad \forall \; \; g \in C^{\infty}(M, G)$$

for arbitrary $$n \in \mathbb{Z}_{+}$$, indices $$(i_1, \ldots, i_n) \in \{1, \ldots, N\}^n$$, and points $$(x_1, \ldots, x_n) \subset M$$. Or written schematically,

$$\mathbb{E}_{\Psi \sim S} \circ \rho_g = \mathbb{E}_{\Psi \sim S}$$

* This principle can be motivated by the fact that physical quantities like scattering amplitudes can be written in terms of correlators (see Appendix A.2), meaning that correlators are of direct physical relevance. As a result, given that we have defined physical equivalence via the relation $$\sim$$, we should ensure that correlators are invariant under $$\sim$$ (or equivalently, under $$\rho$$) to ensure a consistent physical theory.

Importantly, in order for this condition to hold generically, one can see that we require two independent conditions to hold:

$$\begin{equation}
\label{eqn:Sinvar}
S[\rho_g \Psi] = S[\Psi] \qquad \forall \; \; g \in C^{\infty}(M, G) \qquad\qquad (\text{Equation 1})
\end{equation}$$

and

$$\begin{equation}
\label{eqn:Dinvar}
\text{D}(\rho_g \Psi) = \text{D}\Psi \qquad \forall \; \; g \in C^{\infty}(M, G) \qquad\qquad (\text{Equation 2})
\end{equation}$$

Our theory is ultimately described by the triple $$(S, G, \rho)$$ consisting of the action, the group content, and representation content respectively. Equation 2 only places constraints on $$(G, \rho)$$, whereas Equation 1 constrains all of $$(S, G, \rho)$$. The focus of Section 1-3 and Sec 5-7 will be constructing a theory that achieves Equation 1, while the implications of Equation 2 are examined in Section 4.

In constructing a theory $$S$$ that satisfies Equation 1, the spacetime group $$G^{(0)}$$ will play a particularly special role. Namely, we will begin with a theory of empty field content $$\Psi = \emptyset$$ and add to the field content incrementally, using representations of $$G^{(0)}$$ to construct the initial field content of the theory (i.e. spinors), with $$S$$ designed to be invariant (in the sense of Equation 1) to $$G^{(0)}$$ by design. From here, we will then obtain invariance to $$(G^{(1)}, \ldots, G^{(K)})$$ via a process called *minimal coupling*. A total overview:

* Section 1: We motivate the spacetime group $$G^{(0)} = \text{Spin}(1, 3) \cong \text{SL}(2; \mathbb{C})$$, allowing us to begin constructing the field content $$\Psi = \{\psi_{L, i}\}_{i=1}^{N_L} \cup \{\chi_{R, j}\}_{j=1}^{N_R}$$ consisting of $$N_L$$ left-handed spinors and $$N_R$$ right-handed spinors.
* Section 2: We then design an action $$S[\Psi]$$ that is invariant to $$G^{(0)}$$ in the sense of Equation 1.
* Section 3: To make $$S[\Psi]$$ invariant to the gauge groups $$(G^{(1)}, \ldots, G^{(K)})$$, we perform *minimal coupling*, which involves extending the field content

    $$\Psi \mapsto \Psi \cup \{A^{(k)}\}_{k=1}^{K}$$

    for a 4-vector $$A^{(k)}$$ associated with each gauge group $$G^{(k)}$$ that transforms appropriately under $$G^{(k)}$$, and adding appropriate terms to $$S[\Psi]$$ involving these new fields.
* Section 4: Checking that Equation 2 is satisfied requires us to compute *anomalies*, describing the extent to which $$\text{D}\Psi$$ fails to be invariant under $$\rho$$. Anomalies must vanish in order for Equation 2 to be satisfied. We will see that the Standard Model, defined by the gauge groups

    $$(G^{(1)}, G^{(2)}, G^{(3)}) = (U(1), SU(2), SU(3))$$

    indeed has vanishing anomalies as required.
* Section 5: To add non-trivial interaction terms between spinors to the action $$S[\Psi]$$, extend the field content

    $$\Psi \mapsto \Psi \cup \{H\}$$

    for a single scalar $$H$$ that transforms appropriately under the gauge groups to preserve gauge invariance of $$S[\Psi]$$. In the context of the Standard Model, $$H$$ is called the *Higgs boson*, and the associated interaction terms are called the *Yukawa terms*.
* Section 6: We will promote the auxiliary fields $$\{A^{(k)}\}_{k=1}^{K}$$ and $$H$$ to be dynamical fields in their own right, possessing their own kinetic terms in the action $$S[\Psi]$$.
* Section 7: To remedy divergences that arise from integrating over physically-equivalent configuriations, we must introduce *ghost fields* $$\bar{c}^{(k)}, c^{(k)} \in C^{\infty}(M, \mathfrak{g}^{(k)})$$ for each gauge field $$A^{(k)}$$:

    $$\Psi \mapsto \Psi \cup \{\bar{c}^{(k)}, c^{(k)}\}_{k=1}^{K}$$

    This requires adding certain contributions to the action that *break gauge invariance*. However, remnants of gauge invariance still persist through *BRST invariance*.
* Section 8: Experiments on Earth are restricted to a relatively low ``anthropic'' energy scale, which means that we can only expect our theories (e.g. the Standard Model) to be accurate at low energies if we determine the values of their couplings via such experiments. We show how the procedure of *renormalization* allows us to extrapolate such low-energy theories to higher energy scales beyond that which we can observe, and in the special case of *asymptotic freedom*, to arbitrarily large energy scales.
* Section 9: Since quantities of physical relevance can be written in terms of correlators, extracting physical predictions from a theory requires computing correlators. We outline how correlators can be computed perturbatively.

Main references of relevance: David Tong's [Standard Model notes](https://www.damtp.cam.ac.uk/user/tong/standardmodel.html) are most relevant to Section 1-6, and David Skinner's [Advanced QFT notes](https://www.damtp.cam.ac.uk/user/dbs26/AQFT.html) are relevant to Section 7-9. Section 4 is also closely based on David Tong's [Gauge Theory notes](https://www.damtp.cam.ac.uk/user/tong/gaugetheory.html).


### 1. The spacetime symmetry group and its representations

We begin with an empty theory of no field content $$\Phi = \emptyset$$. To start things off, we must choose some ($$d$$-dimensional) spacetime manifold $$M$$ to embed our theory into, as well as a metric $$g$$ over this manifold. Roughly, the manifold $$M$$ describes the *topology* of our spacetime, and the metric $$g$$ describes the *geometry* of our spacetime.
* For example, with a metric $$g$$, we can define a notion of distance between any two points $$x, y \in M$$ on the manifold using geodesics. A metric $$g$$ also gives us a notion of curvature of spacetime.

Our manifold $$M$$ comes equipped with a tangent space $$T(M)$$, and in particular, a (local) coordinate basis $$\{\partial_{\mu}\}_{\mu=0}^{d-1} \subset T(M)$$ of this tangent space, acting as partial derivatives that can act on our (to be constructed) field content of type $$C^{\infty}(M, V)$$.

**Motivating the spacetime symmetry group.** To begin defining the objects and fields $$\Psi_i$$ relevant to $$(M, g)$$, our starting point will be to consider the isometries of the metric $$g$$, corresponding to the coordinate transformations that leave $$g$$ invariant. Particularly, this set of transformations forms a group $$\text{Iso}(M, g)$$. We would like our spacetime symmetry group $$G^{(0)}$$ to be related to $$\text{Iso}(M, g)$$ in some capacity.

Our metric $$g$$ will have some *signature*, describing the signs of its eigenvalues. By the assumption of $$g$$ being non-degenerate (i.e. invertible as a matrix), we have that $$g$$ has no zero eigenvalues. We say that $$g$$ has signature $$(r, s)$$ if it has $$r$$ positive eigenvalues, and $$s$$ negative eigenvalues (where $$r+s = n$$). We then define the corresponding signature matrix $$\Omega_{\mu\nu}^{(r, s)} := \text{diag}(\underbrace{1, \ldots, 1}_{r \, \text{times}}, \underbrace{-1, \ldots, -1}_{s \, \text{times}})$$ for $$g$$. Then we have the following result: at any point $$x \in M$$, there exists a basis $$\{e_{\mu}\}_{\mu} \subset T_x(M)$$ such that

$$g_x(e_{\mu}, e_{\nu}) = \Omega_{\mu\nu}^{(r, s)}$$

That is, locally, we can always reduce $$g$$ to the signature matrix $$\Omega_{\mu\nu}^{(r, s)}$$. As a result, the isometry group $$\text{Iso}(M, g)$$ locally reduces to $$\text{Iso}(M, \Omega^{(r, s)})$$, and note that $$\text{Iso}(M, \Omega^{(r, s)}) \cong \text{IO}(r, s)$$, the inhomogeneous orthogonal group of signature $$(r, s)$$. Further, note that we can decompose $$\text{IO}(r, s)$$ as

$$\text{IO}(r, s) = \mathbb{R}^{r, s} \rtimes \text{O}(r, s)$$

with $$\text{O}(r, s)$$ the linear/homogeneous orthogonal group of signature $$(r, s)$$, explicitly defined as

$$\text{O}(r, s) = \{A \in \text{GL}(r+s; \mathbb{R}): A^T \Omega^{(r,s)} A = \Omega^{(r,s)}\}$$

We will restrict our attention to the local linear isometries $$\text{O}(r, s)$$ of $$g$$.

To construct some initial field content $$\Psi$$, we would like to understand the available representations of $$O(r, s)$$, which can most easily be performed by looking at its Lie algebra $$\mathfrak{o}(r, s)$$. Simply connected Lie groups are particularly nice for this purpose, since there is a one-to-one correspondence between Lie group representations and Lie algebra representations in this case. However, $$O(r, s)$$ is not simply connected.

But, in general, for any finite-dimensional Lie algebra $$\mathfrak{g}$$, there is a unique simply connected group $$G$$ whose Lie algebra is $$\mathfrak{g}$$. Further, for a Lie group $$H$$ that also has this algebra $$\mathfrak{g}$$, then the corresponding simply connected Lie group $$G$$ is isomorphic to the universal covering group of the connected component of $$H$$ that contains the identity.

In our case, this means that the unique simply connected Lie group associated with $$\mathfrak{o}(r, s)$$ is the universal covering group of $$SO^{+}(r, s)$$ (where $$SO^{+}(r, s)$$ is the connected component of $$O(r, s)$$ that contains the identity). Of particular interest to us will be the case of $$(r, s) = (1, d-1)$$, in which case we have that for $$d \geq 4$$, the universal covering group of $$SO^{+}(1, d-1)$$ is the spin group $$\text{Spin}(1, d-1)$$. As a result, we will choose our spacetime group to be

$$G^{(0)} = \text{Spin}(1, d-1)$$

In total, we have chosen $$G^{(0)}$$ to be the unique simply connected Lie group corresponding to (the Lie algebra of) the local linear isometries of $$g$$. We will now study its representations.
* Note that if both $$r, s > 1$$, then $$\text{Spin}(r, s)$$ is no longer the universal covering group; it is only the double cover of $$SO^{+}(r, s)$$ (by definition).
* In the above, we restricted to the local isometry group $$\text{Iso}(M, \Omega^{(r, s)})$$. Can we say something about the representations of the ``true'' global isometry group $$\text{Iso}(M, g)$$ (or its linear component)?

**Representations.** Of particular interest to our universe is the choice $$d=4$$, corresponding to the signature $$(r, s) = (1, 3)$$, with manifold $$M \cong \mathbb{R}^{1, 3}$$, representing 1 temporal dimension and 3 spatial dimensions. The signature matrix is $$\eta_{\mu\nu}$$, called the *Minkowski metric*, taking the form $$\eta = \text{diag}(1, -1, -1, -1)$$.

In this case, we can identify

$$G^{(0)} = \text{Spin}(1, 3) \cong SL(2; \mathbb{C})$$

with $$SO^{+}(1, 3) \cong SL(2; \mathbb{C})/\mathbb{Z}_2$$. In particular, by construction, we have that

$$\mathfrak{o}(1, 3) \cong \mathfrak{sl}(2; \mathbb{C})$$

We therefore wish to classify the spacetime representations of $$G^{(0)}$$ via studying the algebra $$\mathfrak{sl}(2; \mathbb{C})$$. As shown in Appendix A.1, we can classify all irreducible representations of complex simple Lie algebras, and we have that $$\mathfrak{sl}(2; \mathbb{C})$$ is semi-simple, decomposing as the direct sum of simple Lie algebras:

$$\begin{equation}
\label{eqn:sliso}
\mathfrak{sl}(2; \mathbb{C})_{\mathbb{C}} \cong \mathfrak{su}(2)_{\mathbb{C}} \oplus \mathfrak{su}(2)_{\mathbb{C}}
\end{equation}$$

* This follows from there being a set of generators $$\{L_i\}_{i=1}^{3} \cup \{R_i\}_{i=1}^{3}$$ of $$\mathfrak{sl}(2; \mathbb{C})_{\mathbb{C}}$$ such that $$\{L_i\}_{i=1}^{3}$$ and $$\{R_i\}_{i=1}^{3}$$ each satisfy the $$\mathfrak{su}(2)_{\mathbb{C}}$$ algebra independently, i.e.

    $$[L_i, L_j] = \epsilon_{ijk} L_k, \qquad [R_i, R_j] = \epsilon_{ijk} R_k$$

    with $$[L_i, R_j]= 0$$ for all $$i, j$$. This corresponds to the Lie algebra isomorphism $$\mathfrak{sl}(2; \mathbb{C})_{\mathbb{C}} \cong \mathfrak{su}(2)_{\mathbb{C}} \oplus \mathfrak{su}(2)_{\mathbb{C}}$$.
* In more detail, $$\mathfrak{sl}(2; \mathbb{C})$$ (non-complexified) has generators with representations

    $$\text{Boosts:} \quad K_1 \sim \begin{bmatrix}
    &1&&\\
    1&&&\\
    &&&\\
    &&&
    \end{bmatrix}, \quad K_2 \sim \begin{bmatrix}
    &&1&\\
    &&&\\
    1&&&\\
    &&&
    \end{bmatrix}, \quad K_3 \sim \begin{bmatrix}
    &&&1\\
    &&&\\
    &&&\\
    1&&&
    \end{bmatrix}$$

    $$\text{Rotations:} \quad J_1 \sim \begin{bmatrix}
    &&&\\
    &&&\\
    &&&-1\\
    &&1&
    \end{bmatrix}, \quad J_2 \sim \begin{bmatrix}
    &&&\\
    &&&1\\
    &&&\\
    &-1&&
    \end{bmatrix}, \quad J_3 \sim \begin{bmatrix}
    &&&\\
    &&-1&\\
    &1&&\\
    &&&
    \end{bmatrix}$$

    allowing us to write any element $$X \in \mathfrak{sl}(2; \mathbb{C})$$ as $$X = \theta^i J_i + \chi^i K_i$$ for some $$\theta^i, \chi^j \in \mathbb{R}$$. This lets us define generators for the complexified algebra $$\mathfrak{sl}(2; \mathbb{C})_{\mathbb{C}}$$:

    $$L_i := \frac{1}{2}(J_i + iK_i), \qquad R_i = \frac{1}{2}(J_i - iK_i)$$

    Namely, we can write any element $$X \in \mathfrak{sl}(2; \mathbb{C})_{\mathbb{C}}$$ as $$X = \alpha^i L_i + \beta^i R_i$$ for some $$\alpha^i, \beta^j \in \mathbb{C}$$. Then explicitly, the isomorphism is

    $$X = \alpha^i L_i + \beta^i R_i \mapsto \alpha^i L_i \oplus \beta^i R_i =: X_L \oplus X_R$$
* Note that we can write

    $$\alpha^i = \theta^i - i\chi^i, \qquad \beta^i = \theta^i + i\chi^i$$

    for coefficients $$\theta^i, \chi^j$$ in the original uncomplexified basis (rotation and boost parameters respectively).

In the following, we will denote the irreducible representation of $$\mathfrak{su}(2)_{\mathbb{C}}$$ of highest weight $$\Lambda$$ by

$$d^{(\Lambda)}: \mathfrak{su}(2)_{\mathbb{C}} \to \mathfrak{gl}(V_{\Lambda})$$

with $$\dim d^{(\Lambda)} \equiv \dim V_{\Lambda} = \Lambda + 1$$. For more details regarding why we can classify the irreducible representations of complex simple Lie algebras by highest weights $$\Lambda$$, see Appendix A.1.

The isomorphism of Equation \ref{eqn:sliso} lets us determine all irreducible representations $$d^{(\Lambda_1, \Lambda_2)}$$ of $$\mathfrak{sl}(2; \mathbb{C})_{\mathbb{C}}$$, constructed as

$$d^{(\Lambda_1, \Lambda_2)}: \mathfrak{sl}(2; \mathbb{C})_{\mathbb{C}} \to \mathfrak{gl}(V_{\Lambda_1, \Lambda_2})$$

$$d_X^{(\Lambda_1, \Lambda_2)} := d_{X_L}^{(\Lambda_1)} \otimes I + I \otimes d_{X_R}^{(\Lambda_2)}$$

defining $$V_{\Lambda_1, \Lambda_2} := V_{\Lambda_1} \otimes V_{\Lambda_2}$$, with $$\dim d^{(\Lambda_1, \Lambda_2)} = (\Lambda_1 + 1)(\Lambda_2 + 1)$$. Here, $$X_L, X_R \in \mathfrak{su}(2)_{\mathbb{C}}$$ are related to $$X \in \mathfrak{sl}(2; \mathbb{C})_{\mathbb{C}}$$ through the isomorphism outlined above, where

$$X = \alpha^i L_i + \beta^i R_i \implies X_L = \alpha^i L_i, \quad X_R = \beta^i R_i$$

We can extend this algebra representation $$d^{(\Lambda_1, \Lambda_2)}$$ to a group representation $$D^{(\Lambda_1, \Lambda_2)}$$ via the exponential map:

$$D^{(\Lambda_1, \Lambda_2)}: SL(2; \mathbb{C}) \to \text{GL}(V_{\Lambda_1, \Lambda_2})$$

$$D_{A}^{(\Lambda_1, \Lambda_2)} := \exp\left(d_{X(A)}^{(\Lambda_1, \Lambda_2)}\right)$$

for $$A \in SL(2; \mathbb{C})$$, with $$X(A) \in \mathfrak{su}(2)$$ related to $$A$$ via $$A =: \exp(X(A))$$.

Iterating through the first couple of these representations is sufficient to define the core objects of interest:
* A *scalar* $$S$$ lives in the 1-dimensional vector space $$V_{0, 0}$$ and transforms as

    $$S \mapsto_{A} D_{A}^{(0, 0)} S \equiv S$$

    under $$A \in SL(2; \mathbb{C})$$.
* A *left-handed spinor* $$\psi_L$$ lives in the 2-dimensional vector space $$V_{1, 0}$$ and transforms as

    $$\psi_L \mapsto_{A} D_{A}^{(1, 0)} \psi_L \equiv \exp\left(d_{X_L(A)}^{(1)}\right) \psi_L$$

    under $$A \in SL(2; \mathbb{C})$$.
* A *right-handed spinor* $$\psi_R$$ lives in the 2-dimensional vector space $$V_{0, 1}$$ and transforms as

    $$\psi_R \mapsto_{A} D_{A}^{(0, 1)} \psi_R \equiv \exp\left(d_{X_R(A)}^{(1)}\right) \psi_R$$

    under $$A \in SL(2; \mathbb{C})$$.
* A *4-vector* $$V$$ lives in the 4-dimensional vector space $$V_{1, 1}$$ and transforms as
    
    $$V \mapsto_{A} D_{A}^{(1, 1)} V \equiv \left(\exp\left(d_{X_L(A)}^{(1)}\right) \otimes \exp\left(d_{X_R(A)}^{(1)}\right)\right) V$$

    under $$A \in SL(2; \mathbb{C})$$.

These 4 objects are all we need to consider to construct the Standard Model.

**Spinor transformations.** We can write the spinor transformation rules more explicitly. Note that $$d^{(1)}$$ describes the *fundamental representation* of $$\mathfrak{su}(2)_{\mathbb{C}}$$, and the Pauli matrices $$\{-i\sigma^j/2\}_{j=1}^{3}$$ act as a fundamental representation of $$\mathfrak{su}(2)_{\mathbb{C}}$$, allowing us to choose

$$d^{(1)}_{L_i} = -\frac{i}{2} \sigma^i, \qquad d^{(1)}_{R_i} = -\frac{i}{2} \sigma^i$$

$$\implies d_{X_L(A)}^{(1)} = -\frac{i}{2} \boldsymbol{\theta}(A) \cdot \boldsymbol{\sigma} - \frac{1}{2} \boldsymbol{\chi}(A) \cdot \boldsymbol{\sigma}, \qquad d_{X_R(A)}^{(1)} = -\frac{i}{2} \boldsymbol{\theta}(A) \cdot \boldsymbol{\sigma} + \frac{1}{2} \boldsymbol{\chi}(A) \cdot \boldsymbol{\sigma}$$

for $$A \in SL(2; \mathbb{C})$$. For brevity we can define the real-valued (anti-symmetric) matrix $$\omega_{\mu\nu}(A)$$ by

$$\omega_{ij}(A) := \epsilon_{ijk} \theta^k(A), \qquad \omega_{0i}(A) := \chi^i(A)$$

Then, also defining

$$\sigma^{\mu\nu} := \frac{i}{4}(\sigma^{\mu} \bar{\sigma}^{\nu} - \sigma^{\nu} \bar{\sigma}^{\mu}), \qquad \bar{\sigma}^{\mu\nu} := \frac{i}{4}(\bar{\sigma}^{\mu} \sigma^{\nu} - \bar{\sigma}^{\nu} \sigma^{\mu})$$

(related via $$(\sigma^{\mu\nu})^{\dagger} = \bar{\sigma}^{\mu\nu}$$) we can write 

$$d_{X_L(A)}^{(1)} = -\frac{i}{2} \omega_{\mu\nu}(A) \sigma^{\mu\nu}, \qquad d_{X_R(A)}^{(1)} = -\frac{i}{2} \omega_{\mu\nu}(A) \bar{\sigma}^{\mu\nu}$$

which gives us the spinor transformation rules:

$$\psi_L \mapsto_{A} \underbrace{\exp\left(-\frac{i}{2} \omega_{\mu\nu}(A) \sigma^{\mu\nu}\right)}_{=: \, L(A)} \psi_L, \qquad \psi_R \mapsto_{A} \underbrace{\exp\left(-\frac{i}{2} \omega_{\mu\nu}(A) \bar{\sigma}^{\mu\nu}\right)}_{=: \, R(A)}\psi_R$$

Note that $$L(A)^{\dagger} = R(A)^{-1}$$.
* One can also introduce a *Dirac spinor* $$\psi$$ to live in the 4-dimensional space $$V_{1, 0} \oplus V_{0, 1}$$ and transform as

    $$\psi \mapsto_{A} (D_{A}^{(1, 0)} \oplus D_{A}^{(0, 1)}) \psi = \exp\left(-\frac{i}{2} \omega_{\mu\nu}(A) S^{\mu\nu}\right)$$

    for $$S^{\mu\nu} := \frac{i}{4} [\gamma^{\mu}, \gamma^{\nu}]$$ (with $$S^{\mu\nu} = \text{diag}(\sigma^{\mu\nu}, \bar{\sigma}^{\mu\nu})$$). In particular we can view $$\psi = \psi_L \oplus \psi_R$$. This construction will be useful when computing anomalies in Section 4.

**4-vector transformations.** The transformation rule for a 4-vector $$V$$ corresponds to

$$V \mapsto_{A} (L(A) \otimes R(A)) V$$

where recall that $$V \in V_1 \otimes V_1$$ (with $$\dim V_1 = 2$$). Explicitly in indices,

$$V_{ij} \mapsto_{\Lambda} L(A)^k{}_{i} R(A)^{l}{}_{j} V_{kl}$$

It turns out that there is a one-to-one correspondence between $$V_{ij}$$ and an object $$V_{\mu}$$ (with indices $$\mu = 0, 1, 2, 3$$) that transforms as

$$V_{\mu} \mapsto_{A} \Lambda(A)^{\nu}{}_{\mu} V_{\nu}$$

with $$\Lambda(A) \in SO^{+}(1, 3)$$ constructed from $$A \in SL(2; \mathbb{C})$$ via

$$\Lambda(A)^{\mu}{}_{\nu} := \frac{1}{2} \text{tr}(\bar{\sigma}^{\mu} A \sigma_{\nu} A^{\dagger})$$

which is related to the double-cover correspondence $$SO^{+}(1, 3) \cong SL(2; \mathbb{C})/\mathbb{Z}_2$$ (see that $$\Lambda(A) = \Lambda(-A)$$, reflecting the fact that $$SL(2; \mathbb{C})$$ is a double-cover). We can write $$\Lambda(A) \in SO^{+}(1, 3)$$ more conveniently: define the collection of matrices $$\{M^{\mu\nu}\}_{\mu, \nu}$$ by

$$(M^{\mu\nu})^{\sigma}{}_{\rho} = i(\delta^{\nu}{}_{\rho} \eta^{\mu\sigma} - \delta^{\mu}{}_{\rho} \eta^{\nu\sigma})$$

(note that $$M^{ij} = i\epsilon^{ijk} J_k, M^{0i} = iK_i$$) which allows us to write

$$\Lambda(A) = \exp\left(-\frac{i}{2} \omega_{\mu\nu}(A) M^{\mu\nu}\right)$$

For infinitesimal $$\omega_{\mu\nu}$$, this expands as $$\Lambda^{\mu}{}_{\nu} = \delta^{\mu}{}_{\nu} + \omega^{\mu}{}_{\nu} + O(\omega^2)$$.
* Todo: should show this correspondence in more detail, may be some errors in the above.

Due to this correspondence, when talking of 4-vectors, we will be referring to the object $$V_{\mu}$$ that transforms by $$\Lambda(A) \in SO^{+}(1, 3)$$ rather than $$V_{ij}$$ itself, since $$V_{\mu}$$ is much more convenient to work with.

Recall that our theory comes equipped with partial derivatives $$\{\partial_{\mu}\}_{\mu=0}^{3} \subset T(M)$$. These objects transform under a coordinate transformation described by the matrix $$B^{\mu}{}_{\nu}$$ as

$$\partial_{\mu} \mapsto_B B^{\nu}{}_{\mu} \partial_{\nu}$$

meaning that, under a Lorentz transformation $$B = \Lambda(A)$$, partial derivatives transform as a 4-vector:

$$\partial_{\mu} \mapsto_A \Lambda(A)^{\nu}{}_{\mu} \partial_{\nu}$$

for $$A \in SL(2; \mathbb{C})$$. As we will soon see, this transformation rule helps us to construct non-trivial (dynamical) scalars by combining spinors.

### 2. Constructing scalars

We will now consider the field content $$\Psi = \{\psi_{L, i}\}_{i=1}^{N_L} \cup \{\chi_{R, j}\}_{j=1}^{N_R}$$ consisting of $$N_L$$ left-handed spinors and $$N_R$$ right-handed spinors. Our first goal is to construct an action $$S[\Psi]$$ that is invariant to the spacetime symmetry group $$G^{(0)}$$ (in the sense of \ref{eqn:Sinvar}), which is essentially the statement that $$S[\Psi]$$ must transform as a scalar.

To achieve this, we will need to understand how we can combine the spinors $$\{\psi_{L, i}\}_{i=1}^{N_L} \cup \{\chi_{R, j}\}_{j=1}^{N_R}$$ in a way that produces a scalar. In the following, we will show that contractions of the form

$$\psi_{L}^{\dagger} \chi_{R}, \qquad \psi_{L}^{T} \sigma^2 \chi_{L}, \qquad \psi_{L}^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \chi_L$$

and, flipping the handedness,

$$\chi_{R}^{\dagger} \psi_L, \qquad \psi_R^T \sigma^2 \chi_R, \qquad \psi_R^{\dagger} \sigma^{\mu} \partial_{\mu} \chi_R$$

all transform as scalars. There are many more scalars than this, however these are arguably the simplest scalars we can write down and are sufficient for constructing the Standard Model.

It is easy to show that $$\psi_{L}^{\dagger} \chi_{R}$$ and $$\chi_{R}^{\dagger} \psi_L$$ are scalars.
* See that, since $$L(A)^{\dagger} = R(A)^{-1}$$,

    $$\begin{align*}
    \psi_L^{\dagger} \chi_R &\mapsto_A \psi_L^{\dagger} L(A)^{\dagger} R(A) \chi_R\\
    &= \psi_L^{\dagger} R(A)^{-1} R(A) \chi_R\\
    &= \psi_L^{\dagger} \chi_R
    \end{align*}$$

    as required. $$\chi_{R}^{\dagger} \psi_L$$ follows identically.

$$\psi_L^T \chi_L$$ (and $$\psi_R^T \chi_R$$) are not scalars due to the fact that $$(\sigma^{\mu})^T \neq \sigma^{\mu}$$ for all $$\mu$$. Indeed, $$(\sigma^2)^T = -\sigma^2$$ while $$(\sigma^{\mu})^T = \sigma^{\mu}$$ for $$\mu \neq 2$$. Inserting $$\sigma^2$$ results in $$\psi_{L}^{T} \sigma^2 \chi_{L}$$ and $$\psi_R^T \sigma^2 \chi_R$$ being scalars.
* This follows from using $$\sigma^i \sigma^j = \delta_{ij} I + i\epsilon_{ijk} \sigma^k$$ to show that

    $$\sigma^2 \sigma^{ij} \sigma^2 = i\epsilon_{ijk} \sigma^2 \sigma^{0k} \sigma^2, \qquad \sigma^2 \sigma^{0i} \sigma^2 = \frac{i}{2} (-1)^{1\{i=2\}} \sigma^i$$

    which can be used to show

    $$\sigma^2 L(A) \sigma^2 = (L(A)^T)^{-1}$$

    and therefore

    $$\begin{align*}
    \psi_L^T \sigma^2 \chi_L &\mapsto_A \psi_L^T L(A)^T \sigma^2 L(A) \chi_L\\
    &= \psi_L^T L(A)^T \underbrace{(\sigma^2 L(A)^T \sigma^2)}_{(L(A)^T)^{-1}} \sigma^2 \chi_L\\
    &= \psi_L^T \sigma^2 \chi_L
    \end{align*}$$

    as required. $$\psi_R^T \sigma^2 \chi_R$$ follow similarly.

Showing that $$\psi_{L}^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \chi_L$$ (and $$\psi_R^{\dagger} \sigma^{\mu} \partial_{\mu} \chi_R$$) are scalars is more involved. First, we can show that $$\psi_{L}^{\dagger} \bar{\sigma}^{\mu}\chi_L$$ transforms as a 4-vector.
* We will show this infinitesimally. We must first derive the infinitesimal transformation rule for a 4-vector $$X_{\mu} \mapsto_A \Lambda(A)^{\nu}{}_{\mu} X_{\nu}$$. We will write $$\Lambda(A) = \exp(-\frac{i}{2} \omega_{\mu\nu} M^{\mu\nu})$$. Then for infinitesimal $$\omega_{\mu\nu}$$,

    $$\begin{align*}
    X_{\mu} \mapsto_{A} \Lambda(A)^{\nu}{}_{\mu} X_{\nu} &= X_{\mu} -\frac{i}{2} \omega_{\sigma\rho} (M^{\sigma\rho})^{\nu}{}_{\mu} X_{\nu} + O(\omega^2)\\
    &= X_{\mu} - i\omega_{0i} (M^{0i})^{\nu}{}_{\mu} X_{\nu} - \frac{i}{2} \omega_{ij} (M^{ij})^{\nu}{}_{\mu} X_{\nu} + O(\omega^2)\\
    &= X_{\mu} + \chi^i(\delta^i_{\mu} \eta^{0\nu} - \delta^0_{\mu} \eta^{i\nu}) X_{\nu}  + \frac{1}{2} \epsilon_{ijk} \theta^k (\delta_{\mu}^j \eta^{i\nu} - \delta_{\mu}^i \eta^{j\nu}) X_{\nu} + O(\omega^2)\\
    &= X_{\mu} - \delta^0_{\mu} \chi^i X^i + \delta_{\mu}^i(\chi^i X^0 + \epsilon_{ijk} \theta^j X^k) + O(\omega^2)
    \end{align*}$$

    and so, raising the index, we have the infinitesimal 4-vector transformation rule

    $$X^{\mu} \mapsto_{A} X^{\mu} - \delta^{\mu}_0 \chi^i X^i - \delta^{\mu}_i (\chi^i X^0 + \epsilon_{ijk} \theta^j X^k) + O(\omega^2)$$

* Now we must show that $$\psi_{L}^{\dagger} \bar{\sigma}^{\mu}\chi_L$$ also has this transformation rule. See that

    $$\begin{align*}
    \psi_L^{\dagger} \bar{\sigma}^{\mu} \chi_L &\mapsto_{\Lambda} \psi_L^{\dagger} e^{\frac{i}{2} \omega_{\nu\rho} \bar{\sigma}^{\nu\rho}} \bar{\sigma}^{\mu} e^{-\frac{i}{2} \omega_{\nu\rho} \sigma^{\nu\rho}} \chi_L\\
    &= \psi_L^{\dagger} \bar{\sigma}^{\mu} \chi_L + \frac{i}{2} \omega_{\nu\rho} \psi_L^{\dagger} (\bar{\sigma}^{\nu\rho} \bar{\sigma}^{\mu} - \bar{\sigma}^{\mu} \sigma^{\nu\rho}) \chi_L + O(\omega^2)
    \end{align*}$$

    Now using $$\omega_{\mu\nu} \bar{\sigma}^{\mu\nu} = \theta^i \sigma^i + i \chi^i \sigma^i$$ and $$\omega_{\mu\nu} \sigma^{\mu\nu} = \theta^i \sigma^i - i\chi^i \sigma^i$$, we can write

    $$\begin{align*}
    \frac{i}{2} \omega_{\nu\rho} (\bar{\sigma}^{\nu\rho} \bar{\sigma}^{\mu} - \bar{\sigma}^{\mu} \sigma^{\nu\rho}) &= \frac{i}{2} \theta^i [\sigma^i, \bar{\sigma}^{\mu}] - \frac{1}{2} \chi^i \{\sigma^i, \bar{\sigma}^{\mu}\}\\
    &= \begin{cases}
    -\chi^i \sigma^i, & \mu = 0\\
    -\epsilon^{jik} \theta^i \sigma^k - \chi^j \sigma^0, & \mu = j
    \end{cases}
    \end{align*}$$

    where we have used $$\chi^i \delta^{ij} \equiv \chi^i \eta^{ik} \delta^j_k = -\chi^i \delta^j_i = -\chi^j$$. This exactly matches the transformation rule of a 4-vector.

Further, we have that the contraction of any two 4-vectors is a scalar, telling us that $$\psi_{L}^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \chi_L$$ and $$\psi_R^{\dagger} \sigma^{\mu} \partial_{\mu} \chi_R$$ are scalars, since $$\partial_{\mu}$$ transforms as a 4-vector.
* In more detail, given two 4-vectors $$X_{\mu}$$ and $$Y_{\mu}$$, see that from their transformation rules,

    $$\begin{align*}
    X_{\mu} Y^{\mu} &\mapsto_{A} X_{\mu} Y^{\mu} - \chi^i X_0 Y^i - X_i(\chi^i Y^0 + \epsilon_{ijk} \theta^j Y^k) - \chi^i Y^0 X^i + Y^i(\chi^i X^0 + \epsilon_{ijk} \theta^j X^k) + O(\omega^2)\\
    &= X_{\mu} Y^{\mu} + O(\omega^2)
    \end{align*}$$

    with all terms cancelling. As a result, contracting two 4-vectors gives a scalar as required.

For our initial action, we will start with only ``kinetic''-like spinor terms, involving only first-order derivative terms:

$$\begin{equation}
\label{eqn:Skinetic}
S[\Psi] = i\int_M d^4 x \, \left(\sum_{i=1}^{N_L} \psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \psi_{L, i} + \sum_{j=1}^{N_R} \chi_{R, j}^{\dagger} \sigma^{\mu} \partial_{\mu} \chi_{R, j}\right)
\end{equation}$$

Importantly, by construction, this action will satisfy Equation \ref{eqn:Sinvar} restricted to only the spacetime symmetry group $$G^{(0)}$$.
* The factor of $$i$$ in this action ensures that the integrand is real-valued up to a total derivative. And assuming appropriate boundary conditions (such that the total derivative term vanishes under integration), this will ensure that $$S[\Psi] \in \mathbb{R}$$. In more detail, see that

    $$\begin{align*}
    (i\psi_L^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \psi_L)^{\dagger} &= -i(\partial_{\mu} \psi_L^{\dagger}) \bar{\sigma}^{\mu} \psi_L\\
    &= i\psi_L^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \psi_L + \partial_{\mu}(-i\psi_L^{\dagger} \bar{\sigma}^{\mu} \psi_L)
    \end{align*}$$

    hence, up to a total derivative, $$i\psi_L^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \psi_L$$ is real-valued.

Terms of the form $$\psi_L^{\dagger} \chi_R$$ and $$\chi_R^{\dagger} \psi_L$$ will be later used as interaction terms for our theory (Section 5), corresponding to Yukawa terms in the Standard Model.

### 3. Gauge invariance via minimal coupling

Use the notation $$\psi_{L, i} \in C^{\infty}(M, V^{(L, i)})$$ and $$\chi_{R, j} \in C^{\infty}(M, V^{(R, j)})$$.

We will now begin to consider gauge invariance. Currently, the field content consists of left-handed and right-handed spinors that only possess a spacetime sector $$V^{(L, i)} = V^{(L, i, 0)} = V_{1, 0}$$ (for all $$i = 1, \ldots, N_L$$) and $$V^{(R, j)} = V^{(R, j, 0)} = V_{0, 1}$$ (for all $$j = 1, \ldots, N_R$$) respectively.

When considering gauge groups, we promote the output spaces of these spinors:

$$V^{(L, i)} = V^{(L, i, 0)} \to V^{(L, i, 0)} \otimes V^{(L, i, 1)} \otimes \cdots \otimes V^{(L, i, K)},$$

$$V^{(R, j)} = V^{(R, j, 0)} \to V^{(R, j, 0)} \otimes V^{(R, j, 1)} \otimes \cdots \otimes V^{(R, j, K)},$$

As outlined in the introduction, we will consider gauge groups $$(G^{(1)}, \ldots, G^{(K)})$$ that come with (unitary) left-handed representations $$\{\rho^{(L, i, k)}\}_{i, k}$$ each acting on the gauge sector $$V^{(L, i, k)}$$ of $$\psi_{L, i} \in C^{\infty}(M, V^{(L, i)})$$, and (unitary) right-handed representations $$\{\rho^{(R, j, k)}\}_{j, k}$$ each acting on the gauge sector $$V^{(R, j, k)}$$ of $$\chi_{R, j} \in C^{\infty}(M, V^{(R, j)})$$. By Equation \ref{eqn:Sinvar}, we require that our action $$S[\Psi]$$ is invariant to these representations.
* The representation of a compact group is always equivalent to a unitary representation, so we just take our representations to be unitary.

We therefore ask: how can we modify the kinetic action \ref{eqn:Skinetic} to make it gauge invariant? A starting point is to understand the extent to which it fails to be gauge invariant. See that, for a gauge element $$g \in C^{\infty}(M, G^{(k)})$$,

$$\begin{align*}
\psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \psi_{L, i} &\mapsto_g \psi_{L, i}^{\dagger} \rho_{g}^{(L, i, k)\dagger} \bar{\sigma}^{\mu} \partial_{\mu} (\rho_{g}^{(L, i, k)} \psi_{L, i})\\
&= \psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \psi_{L, i} + \psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} [\rho_{g}^{(L, i, k)\dagger} (\partial_{\mu} \rho_{g}^{(L, i, k)})] \psi_{L, i}
\end{align*}$$

where in the second line we have used unitarity and that $$\rho_g \bar{\sigma}^{\mu} \equiv \bar{\sigma}^{\mu} \rho_g$$ since $$\rho_g$$ does not interact with the spacetime sector. The second term captures the failure to achieve gauge invariance. What can we add to our initial action Equation \ref{eqn:Skinetic} in order to remedy this? In general, it appears we must modify the kinetic term

$$\psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \psi_{L, i} \to \psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} (\partial_{\mu} - X_{\mu}^{(L, i, k)}) \psi_{L, i}$$

for some 4-vector $$X^{(L, i, k)}$$ (must be a 4-vector as otherwise the term would no longer be a scalar) that interacts with the gauge sector $$V^{(L, i, k)}$$. Denote its transformation under $$G^{(k)}$$ as $$X_{\mu}^{(L, i, k)} \mapsto_g \tilde{X}_{\mu}^{(L, i, k)}$$. Then this new term transforms as:

$$\begin{align*}
\psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} (\partial_{\mu} - X_{\mu}^{(L, i, k)}) \psi_{L, i} \mapsto_g \; &\psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} \partial_{\mu} \psi_{L, i} + \psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} [\rho_{g}^{(L, i, k)\dagger} (\partial_{\mu} \rho_{g}^{(L, i, k)})] \psi_{L, i}\\
&- \psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} [\rho_g^{(L, i, k)\dagger} \tilde{X}_{\mu}^{(L, i, k)} \rho_g^{(L, i, k)}] \psi_{L, i}
\end{align*}$$

One can see that we will achieve gauge invariance, meaning

$$\psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} (\partial_{\mu} - X_{\mu}^{(L, i, k)}) \psi_{L, i} \mapsto_g \psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} (\partial_{\mu} - X_{\mu}^{(L, i, k)}) \psi_{L, i}$$

if we choose $$X^{(L, i, k)}$$ to transform as

$$X^{(L, i, k)} \mapsto_g \tilde{X}^{(L, i, k)} = \rho_g^{(L, i, k)} X_{\mu}^{(L, i, k)} \rho_g^{(L, i, k)\dagger} + (\partial_{\mu} \rho_g^{(L, i, k)}) \rho_g^{(L, i, k)\dagger}$$

* The same procedure follows identically for all left-handed and right-handed kinetic terms, with associated 4-vectors $$\{X^{(L, i, k)}\}_{i=1}^{N_L} \cup \{X^{(R, j, k)}\}_{j=1}^{N_R}$$ each having their own contribution in the action.

**Infinitesimal gauge transformations.** Writing $$g = \exp(\alpha)$$ for $$\alpha \in C^{\infty}(M, \mathfrak{g}^{(k)})$$, note that $$\rho_g^{(L, i, k)} = \exp(d_{\alpha}^{(L, i, k)})$$ for a representation $$d^{(L, i, k)}$$ of $$\mathfrak{g}^{(k)}$$. Unitarity of $$\rho^{(L, i, k)}$$ implies that $$d^{(L, i, k)}$$ is anti-Hermitian.

See that by Taylor expansion, for infinitesimal $$\alpha$$, $$X_{\mu}^{(L, i, k)}$$ transforms under $$g = \exp(\alpha)$$ as

$$X_{\mu}^{(L, i, k)} \mapsto_{g} X_{\mu}^{(L, i, k)} + [d_{\alpha}^{(L, i, k)}, X_{\mu}^{(L, i, k)}] + \partial_{\mu} d_{\alpha}^{(L, i, k)} + O(\alpha^2)$$

One can see that this transformation rule will take a particularly convenient form if we write

$$X_{\mu}^{(L, i, k)} = d^{(L, i, k)}_{A_{\mu}^{(k)}}$$

for some $$A_{\mu}^{(k)} \in \mathfrak{g}^{(k)}$$, which gives

$$\begin{align*}
X_{\mu}^{(L, i, k)} &\mapsto_g X_{\mu}^{(L, i, k)} + [d_{\alpha}^{(L, i, k)}, d^{(L, i, k)}_{A_{\mu}^{(k)}}] + \partial_{\mu} d_{\alpha}^{(L, i, k)} + O(\alpha^2)\\
&= X_{\mu}^{(L, i, k)} + d_{[\alpha, A_{\mu}^{(k)}]}^{(L, i, k)} + \partial_{\mu} d_{\alpha}^{(L, i, k)} + O(\alpha^2)
\end{align*}$$

which can equivalently be described by a transformation of $$A_{\mu}^{(k)}$$:

$$A_{\mu}^{(k)} \mapsto_{g} A_{\mu}^{(k)} + [\alpha, A_{\mu}^{(k)}] + \partial_{\mu} \alpha + O(\alpha^2)$$

Hence, infinitesimally, we can view the role of all of $$\{X^{(L, i, k)}\}_{i=1}^{N_L} \cup \{X^{(R, j, k)}\}_{j=1}^{N_R}$$ as reducible to a single *gauge field* $$A^{(k)}$$, transforming via the above rule.

**Achieving gauge invariance.** Repeating this for each gauge group $$k=1, \ldots, K$$, the above process corresponds to extending our field content

$$\Psi \mapsto \Psi \cup \{A^{(k)}\}_{k=1}^{K}$$

to include a *gauge field* $$A^{(k)} \in C^{\infty}(M, \mathfrak{g}^{(k)})$$ for each gauge group $$G^{(k)}$$, with $$g = \exp(\alpha) \in C^{\infty}(M, G^{(k)})$$ transforming the field content as

$$\psi_{L, i} \mapsto_g \rho_g^{(L, i, k)} \psi_{L, i}, \quad \chi_{R, j} \mapsto_g \rho_g^{(R, j, k)} \chi_{R, j}, \quad A_{\mu}^{(k)} \mapsto_g A_{\mu}^{(k)} + [\alpha, A_{\mu}^{(k)}] + \partial_{\mu} \alpha + O(\alpha^2)$$

(and leaving $$A^{(k')}$$ invariant for $$k' \neq k$$).

This procedure for achieving gauge invariance is called *minimal coupling* (since we are essentially performing the minimal modification to the kinetic action that achieves gauge invariance...?).

We can write the new gauge invariant action as

$$\begin{equation}
\label{eqn:Sgauged}
S[\Psi] = i\int_M d^4 x \, \left(\sum_{i=1}^{N_L} \psi_{L, i}^{\dagger} \bar{\sigma}^{\mu} D_{\mu}^{(L, i)} \psi_{L, i} + \sum_{j=1}^{N_R} \chi_{R, j}^{\dagger} \sigma^{\mu} D_{\mu}^{(R, j)} \chi_{R, j}\right)
\end{equation}$$

where we have defined the *covariant derivatives*:

$$D_{\mu}^{(L, i)} := \partial_{\mu} - \sum_{k=1}^{K} g_k d^{(L, i, k)}_{A_{\mu}^{(k)}}, \qquad D_{\mu}^{(R, j)} := \partial_{\mu} - \sum_{k=1}^{K} g_k d^{(R, j, k)}_{A_{\mu}^{(k)}}$$

and where we have introduced a *coupling constant* $$g_k$$ for each gauge field $$A^{(k)}$$, equivalent to the rescaling $$A^{(k)} \mapsto g_k A^{(k)}$$, which slightly modifies the transformation rule to:

$$A_{\mu}^{(k)} \mapsto_{g} A_{\mu}^{(k)} + [\alpha, A_{\mu}^{(k)}] + \frac{1}{g_k}\partial_{\mu} \alpha + O(\alpha^2)$$

**Field strengths.** For our discussion of anomalies, it will be useful to introduce the *field strengths* $$F_{\mu\nu}^{(L, i)}$$:

$$F_{\mu\nu}^{(L, i)} := -[D_{\mu}^{(L, i)}, D_{\nu}^{(L, i)}] = \sum_{k=1}^{K} g_k d^{(L, i, k)}_{f_{\mu\nu}^{(k)}} =: \sum_{k=1}^{K} g_k F_{\mu\nu}^{(L, i, k)}$$

where we have introduced $$f_{\mu\nu}^{(k)} \in C^{\infty}(M, \mathfrak{g}^{(k)})$$, defined

$$f_{\mu\nu}^{(k)} := \partial_{\mu} A_{\nu}^{(k)} - \partial_{\nu} A_{\mu}^{(k)} - g_k[A_{\mu}^{(k)}, A_{\nu}^{(k)}]$$

and $$F_{\mu\nu}^{(L, i, k)} := d^{(L, i, k)}_{f_{\mu\nu}^{(k)}}$$.

### 4. Anomalies

In the above, we have restricted our attention to satisfying Equation \ref{eqn:Sinvar}. In this section, we will address the second condition of Equation \ref{eqn:Dinvar}: the measure $$\text{D}\Psi$$ must be invariant to the symmetry group $$G$$ of our theory. If the measure fails to be invariant, we say that there is an *anomaly* in our theory. The condition that the anomaly vanishes places strong constraints on the group content $$G$$ and representation content $$\rho$$ that we can consider, as we will see shortly.
* Note that the measure $$\text{D}\Psi_i$$ associated with non-spinor fields, such as gauge bosons, do not contribute anything to the anomaly, which is why we can restrict our attention to spinor measures only below. Further, since the spacetime group $$G^{(0)}$$ acts non-chirally on left and right-handed spinors (i.e. acts identically), transformations under $$G^{(0)}$$ will not introduce an anomaly, allowing us to further restrict our attention to gauge groups $$(G^{(1)}, \ldots, G^{(K)})$$.

**Gauge transformation rule for spinor measures.** The study of whether $$\text{D}\Psi$$ is invariant requires proposing a specific definition for $$\text{D}\Psi$$. As mentioned in the introduction, we can do so by expanding $$\Psi$$ in a basis of $$C^{\infty}(M, V)$$ and performing some form of regulation. To construct such a basis, we will consider the Dirac operators

$$\slashed{D}^{(L, i)} := \gamma^{\mu} D_{\mu}^{(L, i)}, \quad \slashed{D}^{(R, j)} := \gamma^{\mu} D_{\mu}^{(R, j)}$$

which have eigenfunctions $$\{\phi_{n}^{(i)}\}_{n}$$ and $$\{\xi_{m}^{(j)}\}_m$$ satisfying

$$i\slashed{D}^{(L, i)} \phi_{n}^{(i)} = \lambda_n^{(i)} \phi_n^{(i)}, \qquad i\slashed{D}^{(R, j)} \xi_{n}^{(j)} = \mu_n^{(j)} \xi_n^{(j)}$$

for eigenvalues $$\{\lambda_n^{(i)}\}_n$$ and $$\{\mu_n^{(j)}\}_n$$ respectively.

Since $$\{\phi_{n}^{(i)}\}_{n}$$ and $$\{\xi_{m}^{(j)}\}_m$$ are each a basis for Dirac spinors, we can project them to a basis for left-handed and right-handed spinors using the projection matrices

$$P_L := \frac{1}{2}(I + \gamma^5), \qquad P_R : = \frac{1}{2}(I - \gamma^5)$$

for the $$4 \times 4$$ gamma matrix $$\gamma^5 = \begin{bmatrix}
I&0\\
0&-I
\end{bmatrix}$$. Since $$\gamma^{\mu} \gamma^5 = -\gamma^5 \gamma^{\mu}$$, then

$$i\slashed{D}^{(L, i)} \phi_{n}^{(i)} = \lambda_n^{(i)} \phi_n^{(i)} \iff i\slashed{D}^{(L, i)} (\gamma^5 \phi_{n}^{(i)}) = -\lambda_n^{(i)} (\gamma^5 \phi_n^{(i)})$$

and similarly for $$\slashed{D}^{(R, j)}$$. This implies that the projected eigenfunctions $$\{P_L \phi^{(i)}_n\}_n$$ contain two copies of $$\{P_L \phi^{(i)}_n\}_{n: \lambda_n > 0}$$ since $$P_L \gamma^5 = \gamma^5 P_L = P_L$$. To avoid overcompleteness and keep orthonormality, we must restrict to some subset $$\{P_L \phi_{\gamma}^{(i)}\}_{\gamma}$$ of projected eigenfunctions for left-handed spinors (and $$\{P_R \xi_{\beta}^{(j)}\}_{\beta}$$ for right-handed spinors). This lets us write

$$\psi_{L, i}(x) = \sum_{\gamma} a_{\gamma}^{(i)} P_L \phi_{\gamma}^{(i)}, \qquad \psi_{L, i}^{\dagger}(x) = \sum_{\gamma} b_{\gamma}^{(i)} \phi_{\gamma}^{(i)\dagger} P_L$$

$$\chi_{R, j}(x) = \sum_{\beta} c_{\beta}^{(j)} P_R \xi_{\beta}^{(j)}, \qquad \chi_{R, j}^{\dagger}(x) = \sum_{\beta}  d_{\beta}^{(j)} \xi_{\beta}^{(j)\dagger} P_R$$

By orthonormality, we have

$$\int d^4 x \, \phi_{\gamma}^{(i)\dagger}(x) P_L \phi_{\gamma'}^{(i)}(x) = \delta_{\gamma\gamma'}, \qquad \int d^4 x \, \xi_{\beta}^{(j)\dagger}(x) P_R \xi_{\beta'}^{(j)}(x) = \delta_{\beta\beta'}$$

We can then take

$$\text{D}\psi_{L, i} := \prod_{\gamma} da_{\gamma}^{(i)}, \qquad \text{D}\psi_{L, i}^{\dagger} := \prod_{\gamma} db_{\gamma}^{(i)}$$

$$\text{D}\chi_{R, j} := \prod_{\beta} dc_{\beta}^{(j)}, \qquad \text{D}\chi_{R, j}^{\dagger} := \prod_{\beta} dd_{\beta}^{(j)}$$

To achieve Equation \ref{eqn:Dinvar}, we wish to understand how these measures transform under the gauge transformation associated with $$g = \exp(\alpha) \in C^{\infty}(M, G^{(k)})$$ (for $$\alpha \in C^{\infty}(M, \mathfrak{g}^{(k)})$$) for arbitrary $$k$$:

$$\psi_{L, i} \mapsto_{g} \psi_{L, i} + d^{(L, i, k)}_{\alpha} \psi_{L, i} + O(\alpha^2), \qquad \chi_{R, j} \mapsto_{g} \chi_{R, j} + d^{(R, j, k)}_{\alpha} \chi_{R, j} + O(\alpha^2)$$

Using orthonormality, one can show that this transformation is equivalent to transforming the expansion coefficients as

$$a_{\gamma}^{(i)} \mapsto_g \sum_{\gamma'} a_{\gamma'}^{(i)} (\delta_{\gamma\gamma'} + A_{\gamma\gamma'}^{(i, k)}(\alpha)), \qquad b_{\gamma}^{(i)} \mapsto_g \sum_{\gamma'} b_{\gamma'}^{(i)} (\delta_{\gamma\gamma'} + A_{\gamma\gamma'}^{(i, k)\dagger}(\alpha))$$

$$c_{\beta}^{(j)} \mapsto_g \sum_{\beta'} c_{\beta'}^{(j)} (\delta_{\beta\beta'} + B_{\beta\beta'}^{(j, k)}(\alpha)), \qquad d_{\beta}^{(j)} \mapsto_g \sum_{\beta'} d_{\beta'}^{(j)} (\delta_{\beta\beta'} + B_{\beta\beta'}^{(j, k)\dagger}(\alpha))$$

defining

$$A_{\gamma\gamma'}^{(i, k)}(\alpha) := \int d^4 x \, \phi_{\gamma}^{(i)\dagger}(x) d_{\alpha}^{(L, i, k)} P_L \phi_{\gamma'}^{(i)}(x)$$

$$B_{\beta\beta'}^{(j, k)}(\alpha) := \int d^4 x \,  \xi_{\beta}^{(j)\dagger}(x) d_{\alpha}^{(R, j, k)} P_R \xi_{\beta'}^{(j)}(x)$$

This results in the transformation rules

$$\text{D}\psi_{L, i} \mapsto_{g} \det(I+A^{(i, k)}(\alpha))^{-1} \text{D}\psi_{L, i}, \qquad \text{D}\psi_{L, i}^{\dagger} \mapsto_{g} \det(I+A^{(i, k)\dagger}(\alpha))^{-1} \text{D}\psi_{L, i}^{\dagger}$$

$$\text{D}\chi_{R, j} \mapsto_{g} \det(I+B^{(j, k)}(\alpha))^{-1} \text{D}\chi_{R, j}, \qquad \text{D}\chi_{R, j}^{\dagger} \mapsto_{g} \det(I+B^{(i, k)\dagger}(\alpha))^{-1} \text{D}\chi_{R, j}^{\dagger}$$

* We get factors like $$\det(I+X)^{-1}$$ rather than $$\det(I+X)$$ since spinors are Grassman-valued, meaning the expansion coefficients are also Grassman-valued, which results in an inverted Jacobian.

Using $$\det(I+X) \approx \det(e^X) = e^{\text{tr}(X)}$$ for infinitesimal $$X$$, we have that overall:

$$\text{D}\psi_{L, i} \text{D}\psi_{L, i}^{\dagger} \mapsto_{g} J_g^{(L, i, k)} \text{D}\psi_{L, i} \text{D}\psi_{L, i}^{\dagger}, \qquad J_g^{(L, i, k)} := \exp(-2\text{Re}(\text{tr}(A^{(i, k)}(\alpha))))$$

$$\text{D}\chi_{R, j} \text{D}\chi_{R, j}^{\dagger} \mapsto_{g} J_g^{(R, j, k)} \text{D}\chi_{R, j} \text{D}\chi_{R, j}^{\dagger}, \qquad J_g^{(R, j, k)} := \exp(-2\text{Re}(\text{tr}(B^{(i, k)}(\alpha))))$$

The total anomaly associated with gauge elements $$(g_1, \ldots, g_K) \in G^{(1)} \times \cdots \times G^{(K)}$$ (with $$g_k = \exp(\alpha_k)$$) is

$$\begin{align*}
\mathcal{A}(g_1, \ldots, g_K) &:= \prod_{k=1}^{K} \left[\left(\prod_{i=1}^{N_L} J_{g_k}^{(L, i, k)}\right) \left(\prod_{j=1}^{N_R} J_{g_k}^{(R, j, k)}\right)\right]\\
&= \exp\left(-2\sum_{k=1}^{K} \left[\sum_{i=1}^{N_L} \text{Re}(\text{tr}(A^{(i, k)}(\alpha_k))) + \sum_{j=1}^{N_R} \text{Re}(\text{tr}(B^{(j, k)}(\alpha_k)))\right]\right)
\end{align*}$$

with

$$\text{D}\Psi \mapsto_{(g_1, \ldots, g_K)} \; \mathcal{A}(g_1, \ldots, g_K) \text{D}\Psi$$

**Fujikawa regulation.** One problem is that the trace terms, e.g.

$$\text{tr}(A^{(i, k)}(\alpha)) = \sum_{\gamma} \int d^4 x \, \phi_{\gamma}^{(i)\dagger}(x) d_{\alpha}^{(L, i, k)} P_L \phi_{\gamma}^{(i)}(x)$$

appear that they will diverge in general. One can see this problem as downstream of the fact that we defined e.g. $$\text{D}\psi_{L, i}$$ to be an infinite product of expansion coefficients. One way of resolving such divergences is to apply a cutoff to make the product finite (as we will do for Wilsonian renormalization in Section 8), however applying a cutoff breaks the gauge invariance of our theory. Instead, we will consider *Fujikawa regulation*, which maintains gauge invariance. This involves replacing such trace terms with their regulated variant:

$$\begin{align*}
\text{tr}(A^{(i, k)}(\alpha)) \to \text{tr}_{\Lambda}(A^{(i, k)}(\alpha)) &:= \sum_{\gamma} e^{-\lambda_{\gamma}^2/\Lambda^2}\int d^4 x \, \phi_{\gamma}^{(i)\dagger}(x) d_{\alpha}^{(L, i, k)} P_L \phi_{\gamma}^{(i)}(x)\\
&= \sum_{\gamma} \int d^4 x \, \phi_{\gamma}^{(i)\dagger}(x) d_{\alpha}^{(L, i, k)} P_L e^{(\slashed{D}^{(L, i)})^2/\Lambda^2}\phi_{\gamma}^{(i)}(x)
\end{align*}$$

for some regularization parameter $$\Lambda$$. We will eventually take $$\Lambda \to \infty$$ at the end. The regulated anomaly is then

$$\mathcal{A}^{(\Lambda)}(g_1, \ldots, g_K) := \exp\left(-2\sum_{k=1}^{K} \left[\sum_{i=1}^{N_L} \text{Re}(\text{tr}_{\Lambda}(A^{(i, k)}(\alpha_k))) + \sum_{j=1}^{N_R} \text{Re}(\text{tr}_{\Lambda}(B^{(j, k)}(\alpha_k)))\right]\right)$$

We will now determine the required conditions on group content $$G$$ and representation content $$\rho$$ to ensure that

$$\begin{equation}
\label{eqn:reganom}
\lim_{\Lambda \to \infty} \mathcal{A}^{(\Lambda)}(g_1, \ldots, g_K) = 1 \quad \forall \; (g_1, \ldots, g_K) \in G^{(1)} \times \cdots \times G^{(K)}
\end{equation}$$

holds, such that the regulated measure $$\text{D}\Psi^{(\Lambda)}$$ is gauge invariant. Note that $$\lim_{\Lambda \to \infty} \mathcal{A}^{(\Lambda)} \neq \mathcal{A}$$.

**Computing the anomaly.** We can write this regulated trace as

$$\begin{align*}
\text{tr}_{\Lambda}(A^{(i, k)}(\alpha)) &= \int d^4 x \, \left[\sum_{\gamma} \phi_{\gamma}^{(i)\dagger} d_{\alpha}^{(L, i, k)} P_L e^{(\slashed{D}^{(L, i)})^2/\Lambda^2}\phi_{\gamma}^{(i)}\right](x)\\
&= \int d^4 x \, \text{tr}(d_{\alpha}^{(L, i, k)} P_L e^{(\slashed{D}^{(L, i)})^2/\Lambda^2})(x)\\
&= \int d^4 x \frac{d^4 k}{(2\pi)^4} \, \text{tr}_{s, g}(d_{\alpha}^{(L, i, k)} P_L e^{-ik\cdot x} e^{(\slashed{D}^{(L, i)})^2/\Lambda^2} e^{ik\cdot x})
\end{align*}$$

where $$\text{tr}_{s, g}$$ is a trace over spacetime and gauge indices (i.e. not taken over function space).

Now see that

$$\begin{align*}
(\slashed{D}^{(L, i)})^2 &= (D^{(L, i)})^2 +\frac{1}{2} \gamma^{\mu} \gamma^{\nu} [D_{\mu}^{(L, i)}, D_{\nu}^{(L, i)}]\\
&= (D^{(L, i)})^2 - \frac{1}{2} \gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}
\end{align*}$$

This lets us write

$$\begin{align*}
\text{tr}_{\Lambda}(A^{(i, k)}(\alpha)) &= \int d^4 x \frac{d^4 k}{(2\pi)^4} \,  \text{tr}_{s, g}(d_{\alpha}^{(L, i, k)} P_L e^{-ik\cdot x} e^{(\slashed{D}^{(L, i)})^2/\Lambda^2} e^{ik\cdot x})\\
&= \int d^4 x \frac{d^4 k}{(2\pi)^4} \, \text{tr}_{s, g}(d_{\alpha}^{(L, i, k)} P_L e^{-\gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}/2\Lambda^2} e^{-ik\cdot x} e^{(D^{(L, i)})^2/\Lambda^2} e^{ik\cdot x})\\
&= \int d^4 x \frac{d^4 k}{(2\pi)^4} \, \text{tr}_{s, g}(d_{\alpha}^{(L, i, k)} P_L e^{-\gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}/2\Lambda^2} e^{(D^{(L, i)} + ik)^2/\Lambda^2})\\
&= \int d^4 x \frac{d^4 k}{(2\pi)^4} \, \text{tr}_{s, g}(d_{\alpha}^{(L, i, k)} P_L e^{-\gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}/2\Lambda^2} e^{(\partial + ik)^2/\Lambda^2})\\
&= \int d^4 x \frac{d^4 k}{(2\pi)^4} \, e^{-k^2/\Lambda^2} \text{tr}_{s, g}(d_{\alpha}^{(L, i, k)} P_L e^{-\gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}/2\Lambda^2})\\
&= \int d^4 x \frac{d^4 k}{(2\pi)^4} \, e^{-k^2/\Lambda^2} \text{tr}_{g}(d_{\alpha}^{(L, i, k)} \text{tr}_s(P_L e^{-\gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}/2\Lambda^2}))
\end{align*}$$

where in the fourth line we have translated $$k \to k - i\sum_{k=1}^{K} g_k d_{A_{\mu}^k}^{(L, i, k)}$$.

We have that

$$\begin{align*}
\text{tr}_s(P_L e^{-\gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}/2\Lambda^2}) &= \frac{1}{2} \text{tr}_s(e^{-\gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}/2\Lambda^2}) + \frac{1}{2} \text{tr}_s(\gamma^5 e^{-\gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}/2\Lambda^2})\\
&= 2 - \frac{1}{2\Lambda^4} \left(F_{\mu\nu}^{(L, i)} F^{(L, i)\mu\nu} + iF_{\mu\nu}^{(L, i)} * F^{(L, i)\mu\nu}\right) + O(1/\Lambda^6)
\end{align*}$$

by using

$$\text{tr}_s(e^{-\gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}/2\Lambda^2}) = 4 - \frac{1}{\Lambda^4} F_{\mu\nu}^{(L, i)} F^{(L, i)\mu\nu} + O(1/\Lambda^6)$$

$$\text{tr}_s(\gamma^5 e^{-\gamma^{\mu} \gamma^{\nu} F_{\mu\nu}^{(L, i)}/2\Lambda^2}) = -\frac{i}{\Lambda^4} F_{\mu\nu}^{(L, i)} * F^{(L, i)\mu\nu} + O(1/\Lambda^6)$$

and where $$*$$ is the Hodge star operator, with $$F_{\mu\nu}^{(L, i)} * F^{(L, i)\mu\nu} \equiv \frac{1}{2} \epsilon^{\mu\nu\sigma\rho} F_{\mu\nu}^{(L, i)} F^{(L, i)}_{\sigma\rho}$$.
* We have also made use of the identities

    $$\text{tr}(\gamma^{\mu} \gamma^{\nu}) = 4\eta^{\mu\nu}, \quad \text{tr}(\gamma^{\mu} \gamma^{\nu} \gamma^{\sigma} \gamma^{\rho}) = 4(\eta^{\mu\nu} \eta^{\sigma\rho} - \eta^{\mu\sigma} \eta^{\nu\rho} + \eta^{\mu\rho} \eta^{\nu\sigma})$$

    $$\text{tr}(\gamma^5 \gamma^{\mu} \gamma^{\nu}) = 0, \quad \text{tr}(\gamma^5 \gamma^{\mu} \gamma^{\nu} \gamma^{\sigma} \gamma^{\rho}) = -4i\epsilon^{\mu\nu\sigma\rho}$$

    found [here](https://en.wikipedia.org/wiki/Gamma_matrices#:~:text=Trace%20identities,-%5Bedit%5D).

This lets us write

$$\begin{align*}
\text{tr}_{\Lambda}(A^{(i, k)}(\alpha))  &= \int d^4 x \left[\frac{\Lambda^4}{8\pi^2} \text{tr}(d_{\alpha}^{(L, i, k)}) - \frac{1}{32\pi^2} \text{tr}(d_{\alpha}^{(L, i, k)} F_{\mu\nu}^{(L, i)} F^{(L, i)\mu\nu} + i d_{\alpha}^{(L, i, k)} F_{\mu\nu}^{(L, i)} * F^{(L, i)\mu\nu}) + O(1/\Lambda^2)\right]
\end{align*}$$

where we have used $$\int \frac{d^4 k}{(2\pi)^4} \, e^{-k^2/\Lambda^2} = \frac{\Lambda^4}{16\pi^2}$$.

Relevant to the anomaly is the real part of this regulated trace. By anti-Hermitianity of $$d^{(L, i, k)}$$ (due to unitarity of $$\rho^{(L, i, k)}$$), we have that $$\text{tr}(d_{\alpha}^{(L, i, k)})$$ is purely imaginary, as well as $$\text{tr}(d_{\alpha}^{(L, i, k)} F_{\mu\nu}^{(L, i)} F^{(L, i)\mu\nu})$$, while we have that the third term satisfies

$$(-i\text{tr}(d_{\alpha}^{(L, i, k)} F_{\mu\nu}^{(L, i)} * F^{(L, i)\mu\nu}))^* = -i\text{tr}(d_{\alpha}^{(L, i, k)} F_{\mu\nu}^{(L, i)} * F^{(L, i)\mu\nu})$$

meaning it is purely real. As a result, we have that

$$\text{Re}(\text{tr}_{\Lambda}(A^{(i, k)}(\alpha))) = \exp\left(\frac{i}{16\pi^2} \int d^4 x \, \text{tr}(d_{\alpha}^{(L, i, k)} F_{\mu\nu}^{(L, i)} * F^{(L, i)\mu\nu})\right)$$

Now recall that we can write

$$F_{\mu\nu}^{(L, i)} = \sum_{k=1}^{K} g_k f_{\mu\nu}^{(k)a} d_{T_a^{(k)}}^{(L, i, k)}$$

for some choice of generators $$\{T_a^{(k)}\}_a \subset \mathfrak{g}_k$$, and writing $$f_{\mu\nu}^{(k)} = f_{\mu\nu}^{(k)a} T_a^{(k)}$$. This lets us explicitly write

$$\begin{align*}
\text{Re}(\text{tr}_{\Lambda}(A^{(i, k)}(\alpha))) &= \exp\left(\frac{i}{32\pi^2} \epsilon^{\mu\nu\sigma\rho} \int d^4 x \, \text{tr}(d_{\alpha}^{(L, i, k)} F_{\mu\nu}^{(L, i)} F^{(L, i)}_{\sigma\rho})\right)\\
&= \exp\left(\frac{i}{32\pi^2} \epsilon^{\mu\nu\sigma\rho} \sum_{r=1}^{K} \sum_{s=1}^{K} g_r g_s \text{tr}(d_{T_a^{(k)}}^{(L, i, k)} d_{T_b^{(r)}}^{(L, i, r)} d_{T_c^{(s)}}^{(L, i, s)}) \int d^4 x \, \alpha^a f_{\mu\nu}^{(r) b} f_{\sigma\rho}^{(s) c}\right)
\end{align*}$$

The right-handed anomaly is identical but with a minus sign, which stems from the fact that $$P_R = \frac{1}{2}(1 - \gamma^5)$$ whereas $$P_L = \frac{1}{2}(1+\gamma^5)$$. Namely, we have

$$\text{Re}(\text{tr}_{\Lambda}(B^{(j, k)}(\alpha))) = \exp\left(-\frac{i}{32\pi^2} \epsilon^{\mu\nu\sigma\rho} \sum_{r=1}^{K} \sum_{s=1}^{K} g_r g_s \text{tr}(d_{T_a^{(k)}}^{(R, j, k)} d_{T_b^{(r)}}^{(R, j, r)} d_{T_c^{(s)}}^{(R, j, s)}) \int d^4 x \, \alpha^a f_{\mu\nu}^{(r) b} f_{\sigma\rho}^{(s) c}\right)$$

This gives the total regulated anomaly:

$$\begin{align*}
\mathcal{A}^{(\Lambda)}(g_1, \ldots, g_K) &= \exp\Bigg(-\frac{i}{16\pi^2} \epsilon^{\mu\nu\sigma\rho} \sum_{k=1}^{K} \sum_{r=1}^{K} \sum_{s=1}^{K} g_r g_s \int d^4 x \, \alpha_k^a f_{\mu\nu}^{(r)b} f_{\sigma\rho}^{(s)c}\\
&\qquad\qquad \left[\sum_{i=1}^{N_L} \text{tr}(d_{T_a^{(k)}}^{(L, i, k)} d_{T_b^{(r)}}^{(L, i, r)} d_{T_c^{(s)}}^{(L, i, s)}) - \sum_{j=1}^{N_R} \text{tr}(d_{T_a^{(k)}}^{(R, j, k)} d_{T_b^{(r)}}^{(R, j, r)} d_{T_c^{(s)}}^{(R, j, s)})\right]\Bigg)
\end{align*}$$

If we require the anomaly to cancel for any values of couplings $$\{g_k\}_{k=1}^{K}$$ and any gauge elements $$\{\alpha_k\}_{k=1}^{K}$$, then the total anomaly of the theory cancels (i.e. Equation \ref{eqn:reganom} is satisfied) iff

$$\begin{equation}
\label{eqn:anomalycond}
\boxed{\sum_{i=1}^{N_L} C_{L, i}(k, r, s) \text{tr}(d_{T_a^{(k)}}^{(L, i, k)} d_{T_b^{(r)}}^{(L, i, r)} d_{T_c^{(s)}}^{(L, i, s)}) = \sum_{j=1}^{N_R} C_{R, j}(k, r, s) \text{tr}(d_{T_a^{(k)}}^{(R, j, k)} d_{T_b^{(r)}}^{(R, j, r)} d_{T_c^{(s)}}^{(R, j, s)}) \quad \forall \; \; k, r, s \quad \forall \; \; a, b, c}
\end{equation}$$

where we have factored out dimensional factors from the trace over redundant dimensions, captured by

$$C_{L, i}(a, b, \ldots, c) := \prod_{k \notin \{a, b, \ldots, c\}} \dim d^{(L, i, k)}, \qquad C_{R, j}(a, b, \ldots, c) := \prod_{k \notin \{a, b, \ldots, c\}} \dim d^{(R, j, k)}$$

We can split this condition into four distinct cases:

$$\sum_{i=1}^{N_L} C_{L, i}(k) \text{tr}(d_{T_a^{(k)}}^{(L, i, k)} \{d_{T_b^{(k)}}^{(L, i, k)}, d_{T_c^{(k)}}^{(L, i, k)}\}) = \sum_{j=1}^{N_R} C_{R, j}(k) \text{tr}(d_{T_a^{(k)}}^{(R, j, k)} \{d_{T_b^{(k)}}^{(R, j, k)}, d_{T_c^{(k)}}^{(R, j, k)}\}) \quad \forall \; \; k,$$

$$\sum_{i=1}^{N_L} C_{L, i}(k, r) \text{tr}(d_{T_a^{(k)}}^{(L, i, k)}) \text{tr}(d_{T_b^{(r)}}^{(L, i, r)} d_{T_c^{(r)}}^{(L, i, r)}) = \sum_{j=1}^{N_R} C_{R, j}(k, r) \text{tr}(d_{T_a^{(k)}}^{(R, j, k)}) \text{tr}(d_{T_b^{(r)}}^{(R, j, r)} d_{T_c^{(r)}}^{(R, j, r)}) \quad \forall \; \; r \neq k,$$

$$\sum_{i=1}^{N_L} C_{L, i}(k, r) \text{tr}(d_{T_a^{(k)}}^{(L, i, k)} d_{T_b^{(k)}}^{(L, i, k)}) \text{tr}(d_{T_c^{(r)}}^{(L, i, r)}) = \sum_{j=1}^{N_R} C_{R, j}(k, r) \text{tr}(d_{T_a^{(k)}}^{(R, j, k)} d_{T_b^{(k)}}^{(R, j, k)}) \text{tr}(d_{T_c^{(r)}}^{(R, j, r)}) \quad \forall \; \; r \neq k,$$

$$\sum_{i=1}^{N_L} C_{L, i}(k, r, s) \text{tr}(d_{T_a^{(k)}}^{(L, i, k)}) \text{tr}(d_{T_b^{(r)}}^{(L, i, r)}) \text{tr}(d_{T_c^{(s)}}^{(L, i, s)}) = \sum_{j=1}^{N_R} C_{R, j}(k, r, s) \text{tr}(d_{T_a^{(k)}}^{(R, j, k)}) \text{tr}(d_{T_b^{(r)}}^{(R, j, r)}) \text{tr}(d_{T_c^{(s)}}^{(R, j, s)}) \quad \forall \; \; r \neq s, \; r \neq k, \; s \neq k$$

The final three conditions are called *mixed* anomaly conditions, since they mix together the representations of different gauge groups.

If the representation content $$\rho$$ of our theory satisfies this condition, then the (regulated) measure $$\text{D}\Psi$$ will be gauge invariant.

Some things to consider:
* What about gravitational anomalies that apparently emerge from diffeomorphism invariance?
* Witten anomalies are missing from the above: sometimes the anomaly condition can be satisfied, yet the theory can still fail to be gauge invariant. This is related to the hopotopy group of the gauge group. If $$\Pi_4(G^{(k)}) = 0$$, we are fine and only need the above anomaly condition to hold. But otherwise -- e.g. $$\Pi_4(\text{Sp}(N)) = \mathbb{Z}_2$$ for all $$N \geq 1$$ -- then there can still be a type of anomaly called the Witten anomaly. This is related to the zero modes of the Dirac operators, and is discussed [here](https://www.damtp.cam.ac.uk/user/tong/gaugetheory/3anom.pdf).


**The special case of the Standard Model.** A theory is defined by the triple $$(S, G, \rho)$$ as well as the field content $$\Psi$$. Here we will describe the choices for these components in the Standard Model.

The Standard Model corresponds to the spacetime group $$G^{(0)} = \text{Spin}(1, 3) \cong \text{SL}(2; \mathbb{C})$$ as considered above, and gauge groups

$$(G^{(1)}, G^{(2)}, G^{(3)}) = (U(1), SU(2), SU(3))$$

The field content includes 2 left-handed spinors, 3 right-handed spinors, and a Higgs boson:

$$\Psi = (Q_L, L_L, u_R, d_R, e_R, H)$$

To succintly state the representation content, we will denote the trivial rep of a gauge group by $$\mathbf{1}$$, the fundamental rep of $$SU(2)$$ by $$\mathbf{2}$$, and the fundamental rep of $$SU(3)$$ by $$\mathbf{3}$$. Further, since the reps of $$U(1)$$ are labelled by real numbers $$c \in \mathbb{R}$$, we will denote the corresponding rep by $$\mathbf{c}$$. We will then use the notation $$\phi: (\mathbf{a}, \mathbf{b})_{c}$$ to say that the field $$\phi$$ transforms under rep $$\mathbf{a}$$ of $$SU(2)$$, rep $$\mathbf{b}$$ of $$SU(3)$$, and rep $$\mathbf{c}$$ of $$U(1)$$ (where $$a \in \{1, 2\}$$, $$b \in \{1, 3\}$$, and $$c \in \mathbb{R}$$ for our purposes).

Then the representation content of the Standard Model amounts to:

$$Q_L: (\mathbf{2}, \mathbf{3})_{1/6}, \quad L_L: (\mathbf{2}, \mathbf{1})_{-1/2}, \quad u_R: (\mathbf{1}, \mathbf{3})_{2/3},$$

$$d_R: (\mathbf{1}, \mathbf{3})_{-1/3}, \quad e_R: (\mathbf{1}, \mathbf{1})_{-1}, \quad H: (\mathbf{2}, \mathbf{1})_{1/2}$$

Why these particular choices? The above is arguably one of the simplest setups that satisfies the anomaly condition Equation \ref{eqn:anomalycond}. This is argued in the notes (gauge theory).

Todo: should better justify why this particular choice is one of the most simple, and what the other choices are.


### 5. Interactions

**(This section is very unfinished)**

The action \ref{eqn:Sgauged} has achieved our goal of satisfying \ref{eqn:Sinvar}: it is both invariant to the spacetime group $$G^{(0)}$$ by construction, and through minimal coupling, we have achieved invariance to the gauge groups $$G^{(1)} \times \cdots \times G^{(K)}$$. However, the action does not involve any *interactions*, with no contractions between distinct spinors (e.g. no terms of the form $$\psi_L^{\dagger} \chi_R$$).

We previously found that terms of the form $$\psi_L^{\dagger} \chi_R$$ (and $$\chi_R^{\dagger} \psi_L$$) were invariant to the spacetime group $$G^{(0)}$$. But gauge invariance is problematic since, generally,

$$\rho^{(L, i, k)\dagger} \rho^{(R, j, k)} \neq I$$

Indeed, as we will see, the choice of representations in the Standard Model are such that no term of the form $$\psi_L^{\dagger} \chi_R$$ can be gauge invariant. This differs from the case of minimal coupling (Section 3), as here it is not an additive term that causes gauge invariant to fail. To achieve gauge invariance in this case, one could imagine introducing a scalar field $$H$$ that transforms under the gauge groups precisely such that

$$\psi_L^{\dagger} H \chi_R$$

is gauge invariant. Note that $$H$$ must transform as a scalar in order for the term to remain a scalar. Is there some *maximal* choice of the transformation properties of $$H$$, such that we can produce the maximum number of interaction terms possible?

In the Standard Model, $$H$$ transforms under the gauge groups as $$(\mathbf{2}, \mathbf{1})_{1/2}$$, which allows for the interaction terms... Todo


### 6. Making auxiliary fields dynamical

**(This section is unfinished)**

In the above, we have introduced auxiliary fields $$\{A^{(k)}\}_{k=1}^{K}$$ and $$H$$ to our theory in order to help us achieve gauge invariance. Here, we make a key step: we promote these fields to be *dynamical* in their own right, possessing their own kinetic terms in the action $$S[\Psi]$$. In particular, these kinetic terms should include derivatives of these auxiliary fields, and preserve overall spacetime & gauge invariance.
* The idea that derivative terms make the fields ``dynamical'' can be seen by considering the classical equations of motion (i.e. the Euler-Lagrange equations), which give non-trivial dynamics when the action depends on a derivative of the field.

**Kinetic terms for $$A^{(k)}$$.** In order to make $$A^{(k)}$$ dynamical, we would like to construct a spacetime \& gauge invariant term that depends on $$A_{\mu}^{(k)}$$ and its derivatives $$\partial_{\nu} A_{\mu}^{(k)}$$. Recall that we defined

$$f_{\mu\nu}^{(k)} := \partial_{\mu} A_{\nu}^{(k)} - \partial_{\nu} A_{\mu}^{(k)} - g_k [A_{\mu}^{(k)}, A_{\nu}^{(k)}]$$

We further have that $$f_{\mu\nu}^{(k)}$$ transforms as

$$f_{\mu\nu}^{(k)} \mapsto_{\alpha} f_{\mu\nu}^{(k)} + [\alpha, f_{\mu\nu}^{(k)}] + O(\alpha^2)$$

under a gauge transformation by $$\alpha \in C^{\infty}(M, \mathfrak{g}^{(k)})$$.

As shown in Appendix A.1.1, each semi-simple Lie algebra $$\mathfrak{g}^{(k)}$$ has its own natural metric $$\kappa^{(k)}: \mathfrak{g}^{(k)} \times \mathfrak{g}^{(k)} \to \mathbb{C}$$, called the *Killing form*. Importantly, the Killing form satisfies the identity

$$\kappa^{(k)}(X, [Y, Z]) = \kappa^{(k)}(Y, [Z, X]) = \kappa^{(k)}(Z, [X, Y])$$ 

(also proven in Appendix A.1.1) for any $$X, Y, Z \in \mathfrak{g}^{(k)}$$. This identity gives us that the inner product (under metric $$\kappa$$) transforms as

$$\kappa^{(k)}(f_{\mu\nu}^{(k)}, f^{(k)\mu\nu}) \mapsto_{\alpha} \kappa^{(k)}(f_{\mu\nu}^{(k)}, f^{(k)\mu\nu}) + O(\alpha^2)$$

i.e. $$\kappa(f_{\mu\nu}^{(k)}, f^{(k)\mu\nu})$$ is (infinitesimally) gauge invariant. As a result, it is a natural candidate as a kinetic term for $$A^{(k)}$$. And since all 4-vector indices are contracted, it will be a Lorentz scalar.

**Free and kinetic terms for $$H$$.** $$H^{\dagger} H$$ is the simplest Lorentz and gauge invariant term for $$H$$ (recall that $$H$$ is a Lorentz scalar). As a result, we can consider the contributions of the form $$(H^{\dagger} H)^n$$ for $$n \in \{1, 2, \ldots\}$$. We will focus specifically on the cases of $$n = 1,2$$.
* We focus only on $$H^{\dagger} H$$ and $$(H^{\dagger} H)^2$$ terms because $$(H^{\dagger} H)^k$$ for $$k > 2$$ are not *relevant* or *marginal*, as defined at the end of Section 8. In the notation of Section 8, assuming canonical normalization of the kinetic contribution ($$(m, n) = (a, b)$$), an $$(m, n)$$ contribution is said to be irrelevant at low energies iff $$n-d+m(d-b)/a > 0$$. For us, $$(a, b) = (2, 2)$$, and since $$d=4$$, the condition for irrelevance becomes
    
    $$n+m > 4$$

    And since $$(H^{\dagger} H)^k$$ has $$(m, n) = (2k, 0)$$, terms with $$k > 2$$ are irrelevant.

We can also construct terms that involve derivatives of $$H$$. Most naively we could consider $$(\partial_{\mu} H)^{\dagger} \partial^{\mu} H$$, however this will not be gauge invariant for the same reasons as seen in Section 3. Instead, we must do something analogous to minimal coupling. By proceeding similarly to Section 3, one can find that

$$(D_{\mu}^{(H)} H)^{\dagger} D^{(H)\mu} H$$

is both Lorentz and gauge invariant, defining the associated covariant derivative

$$D_{\mu}^{(H)} := \partial_{\mu} - \sum_{k=1}^{K} g_k d_{A_{\mu}^{(k)}}^{(H, k)}$$

where we have denoted the gauge representations that act on $$H$$ by $$\{d^{(H, k)}\}_{k=1}^{K}$$.

This motivates adding the contribution

$$(D_{\mu}^{(H)} H)^{\dagger} D^{(H)\mu} H + \alpha H^{\dagger} H + \beta (H^{\dagger} H)^2$$

or equivalently

$$(D_{\mu}^{(H)} H)^{\dagger} D^{(H)\mu} - \frac{\lambda}{2}(H^{\dagger} H - v^2)^2$$

which allows us to interpret the contribution as a kinetic term + potential term, where the potential has ground states defined by $$H^{\dagger} H = v^2$$. This interpretation will be useful for the discussion of symmetry breaking.

*Symmetry breaking.* Todo: Reparameterize $$H$$ by expanding around ground state, and perform appropriate gauge transformation to $$S[\Psi]$$. Demonstrate that this has given ``mass'' to the spinors.

Todo: argue that the reps chosen for the Higgs boson are `maximal' in some sense for achieving interactions.

Todo: show that, via symmetry breaking, fermions gaining mass.


### 7. Overcounting in the path integral

There is some kind of ``overcounting'' happening when integrating over any field object $$\Psi_i \in \mathcal{C}_i$$ due to $$\mathcal{C}_i$$ including physically-equivalent field configurations that are related by a gauge transformation. In the specific case of gauge fields, this overcounting is detrimental and results in divergences. To remedy this issue requires *breaking gauge invariance*, but some remnants of gauge invariance still persist through *BRST invariance*. We will now outline these concepts.
* Some brief notes on the differences between integrating over the redundant configuration space $$\mathcal{C}$$ compared to the underlying physical configuration space $$\mathcal{P}$$ can be found in Appendix A.4.

In the following, we will restrict our attention to a particular gauge field $$A \in C^{\infty}(M, \mathfrak{g}^{(k)})$$ associated with gauge group $$G^{(k)}$$ -- the following analysis will apply to each of the $$K$$ gauge fields individually. For gauge element $$\alpha \in C^{\infty}(M, \mathfrak{g}^{(k)})$$, we will use the notation

$$A_{\mu}^{\alpha} := A_{\mu} + [\alpha, A_{\mu}] + \frac{1}{g_k} \partial_{\mu} \alpha$$

for the gauge transformation of $$A$$ by $$\alpha$$. We will work with a set of generators $$\{T_a\}_a \subset \mathfrak{g}^{(k)}$$, with $$[T_a, T_b] = f^c{}_{ab} T_c$$ for structure constants $$\{f^c{}_{ab}\}_{a, b, c}$$. We can write the components of $$A^{\alpha}$$ explicitly as

$$(A_{\mu}^{\alpha})^a = A_{\mu}^a + \alpha^b A_{\mu}^c f^a{}_{bc} + \frac{1}{g_k} \partial_{\mu} \alpha^a$$

where $$A_{\mu} = A_{\mu}^a T_a$$. For the following we will define the matrix of differential operators

$$\Delta_{\mu}(A)^a{}_{b} := f^a{}_{bc} A_{\mu}^c + \frac{1}{g_k} \delta^a_b \partial_{\mu}$$

which lets us write

$$(A_{\mu}^{\alpha})^a = A_{\mu}^a + \Delta_{\mu}(A)^a{}_{b} \alpha^b$$

In the following we will work in Minkowski signature, meaning $$\mathbb{P}[\Psi] \propto e^{iS[\Psi]}$$ (rather than $$\propto e^{-S[\Psi]}$$).

**Remedying gauge overcounting.** By looking at the partition function, we can interpret the effects of gauge overcounting and identify the divergent contribution. We will make use of the two identities

$$1 = N(\xi) \int D\omega \, e^{-i\int d^d x \, \omega(x)^2/2\xi}, \qquad 1 = \int D\alpha \, \delta(G_{\omega}(A^{\alpha}))\det\left(\frac{\delta G_{\omega}(A^{\alpha})}{\delta \alpha}\right)$$

introducing a constant $$\xi$$ and normalization factor $$N(\xi)$$, as well as a *gauge condition* $$G_{\omega}(A) := \partial^{\mu} A_{\mu} - \omega$$ (though the identity holds for generic conditions). Multiplying these two identities together gives

$$1 = N(\xi) \int D\alpha \, e^{-i\int(\partial^{\mu} A_{\mu}^{\alpha})^2/2\xi} \det\left(\frac{\delta G_{\omega}(A^{\alpha})}{\delta \alpha}\right)\bigg|_{\omega = \partial^{\mu} A_{\mu}^{\alpha}}$$

$$\iff 1 = N(\xi) \det(\Xi(A)) \int D\alpha \, e^{-i\int(\partial^{\mu} A_{\mu}^{\alpha})^2/2\xi}$$

introducing the differential operator

$$\Xi(A)^a{}_{b} := \partial^{\mu} \Delta_{\mu}(A)^a{}_{b} =  f^a{}_{bc} A_{\mu}^c \partial^{\mu} + f^a{}_{bc} (\partial^{\mu} A_{\mu}^c) + \frac{1}{g_k} \delta_b^a \partial^{\mu} \partial_{\mu}$$

Inserting this result, and writing the field content as $$\Psi = (\Phi, A)$$, our partition function can be written in the form

$$\begin{align*}
Z &= \int \text{D}\Phi \, \text{D}A \; e^{iS[\Phi, A]}\\
&\propto \int \text{D}\Phi \, \text{D}A \, \text{D}\alpha \, \det(\Xi(A)) e^{iS[\Phi, A]-i\int(\partial^{\mu} A_{\mu}^{\alpha})^2/2\xi}
\end{align*}$$

*Abelian gauge fields.* In the case of $$G^{(k)}$$ being abelian, then $$f^a{}_{bc} = 0$$ for all $$a, b, c$$, which means that

$$\Xi(A^{\alpha}) = \Xi(A)$$

In particular, $$\Xi(A)$$ is independent of $$A$$ in this case. As a result, we can make use of gauge invariance (of both $$S$$ and measures $$\text{D}\Phi, \text{D}A$$) to find

$$Z = \underbrace{\left[\int \text{D}\alpha\right]}_{\text{divergent overcounting factor}} \det\left(\frac{1}{g_k} \partial^{\mu} \partial_{\mu}\right) \int \text{D}\Phi \, \text{D}A \, e^{iS[\Phi, A]-i\int(\partial^{\mu} A_{\mu})^2/2\xi}$$

i.e. we have been able to pull an infinite overcounting factor outside of our partition function. In order for our theory to be valid, we divide out by this overcounting factor, instead considering the partition function

$$\tilde{Z} := \int \text{D}\Phi \, \text{D}A \, e^{iS[\Phi, A]-i\int(\partial^{\mu} A_{\mu})^2/2\xi}$$

That is, our action has now become

$$S[\Phi, A] \to S[\Phi, A] - \frac{1}{2\xi} \int d^d x \, (\partial^{\mu} A_{\mu}^a(x)) (\partial^{\nu} A_{\nu a}(x))$$

But significantly, this new contribution is \textbf{not gauge invariant}, breaking the original gauge invariance of our theory. It turns out that there are still remnants of gauge invariance left through the BRST transformations, as we will describe further below.

*Non-abelian gauge fields.* In the abelian case, $$\Xi(A)$$ was actually independent of $$A$$, which let us pull the $$\det(\Xi(A))$$ factor out of the partition function. However, in the general case of $$G^{(k)}$$ non-abelian, this is no longer possible since $$\Xi(A)$$ generally depends on $$A$$. In particular, $$\Xi(A^{\alpha}) \neq \Xi(A)$$. The partition takes the form (up to proportionality constants)

$$Z = \int \text{D}\Phi \, \text{D}A \, \text{D}\alpha \, \det(\Xi(A^{-\alpha})) e^{iS[\Phi, A] - i\int (\partial^{\mu} A_{\mu})^2/2\xi}$$

But interestingly we can write

$$\det(\Xi(A)) = \int \text{D}\bar{c} \, \text{D}c \, e^{-i \int d^d x \, \bar{c}_a \Xi(A)^a{}_{b} c^b}$$

for so-called *ghost fields* $$c, \bar{c} \in C^{\infty}(M, \mathfrak{g}^{(k)})$$ associated with $$A$$. This lets us consider the partition function

$$\tilde{Z} :=  \int \text{D}\Phi \, \text{D}A \, e^{iS[\Phi, A]-i\int(\partial^{\mu} A_{\mu})^2/2\xi-i \int \bar{c}_a \Xi(A)^a{}_{b} c^b}$$

corresponding to modifying our action as

$$S[\Phi, A] \to S[\Phi, A] - \frac{1}{2\xi} \int d^d x \, (\partial^{\mu} A_{\mu}^a(x)) (\partial^{\nu} A_{\nu a}(x)) -\int d^d x \, \bar{c}_a(x) \Xi(A)^a{}_{b} c^b(x)$$

Again, these extra contributions will generally break gauge invariance.

In total, for the generic non-abelian case, we must extend our field content to include ghost fields for each gauge field:

$$\Psi \mapsto \Psi \cup \{\bar{c}^{(k)}, c^{(k)}\}_{k=1}^{K}$$

and modifying the action as

$$S[\Psi] \to S[\Psi] - \sum_{k=1}^{K} \left[\frac{1}{2\xi_k} \int d^d x \, (\partial^{\mu} A_{\mu}^{(k)a}(x)) (\partial^{\nu} A_{\nu a}^{(k)}(x)) +\int d^d x \, \bar{c}_a^{(k)}(x) \Xi^{(k)}(A^{(k)})^a{}_{b} c^{(k) b}(x)\right]$$

We will derive gauge transformation rules for these ghost fields below.

**BRST invariance.** Though this new action no longer satisfies the total gauge invariance we originally had, a restricted case of gauge invariance still remains, called *BRST invariance*.

In particular, recall that gauge transformations act infinitesimally as

$$A_{\mu}^{(k)} \mapsto_{\alpha} A_{\mu}^{(k)} + \Delta_{\mu}(A^{(k)})\alpha + O(\alpha^2)$$

for $$\alpha \in C^{\infty}(M, \mathfrak{g}^{(k)})$$. Under this transformation (and a corresponding transformation on the remaining field content), one can show that the modified Lagrangian transforms as $$\mathcal{L} \mapsto_{\alpha} \mathcal{L} + \delta\mathcal{L}$$ for

$$\begin{align*}
\delta\mathcal{L} &= -\frac{1}{\xi_k}(\partial^{\nu} A_{\nu a}^{(k)}) (\partial^{\mu} \partial_{\mu} \alpha^a + f^{(k)a}{}_{bc} (\alpha^b \partial^{\mu} A_{\mu}^{(k) c} + A_{\mu}^{(k) c} \partial^{\mu} \alpha^b)) - \delta\bar{c}_a^{(k)} (f^{(k)a}{}_{bc} c^{(k)b} \partial^{\mu} A_{\mu}^{(k)c} + f^{(k)a}{}_{bc} A_{\mu}^{(k)c} \partial^{\mu} c^{(k)b} + \partial^{\mu} \partial_{\mu} c^{(k)a})\\
&\quad\,\;-\bar{c}_a^{(k)}(f^{(k)a}{}_{bc} f^{(k)c}{}_{de} [A_{\mu}^{(k)e}\partial^{\mu} \alpha^d + \alpha^d \partial^{\mu} A_{\mu}^{(k)e}] + f^{(k)a}{}_{bc} \partial^{\mu} \partial_{\mu} \alpha^c) c^{(k)b} - \bar{c}_a^{(k)} (f^{(k)a}{}_{bc} f^{(k)c}{}_{de} \alpha^d A^{(k)e}_{\mu} + f^{(k)a}{}_{bc} \partial_{\mu} \alpha^c) \partial^{\mu} c^{(k)b}\\
&\quad\,\;-\bar{c}_a^{(k)} (f^{(k)a}{}_{bc} \delta c^{(k)b} \partial^{\mu} A_{\mu}^{(k)c}+ f^{(k)a}{}_{bc} A_{\mu}^{(k)c} \partial^{\mu}[\delta c^{(k)b}] + \partial^{\mu} \partial_{\mu} [\delta c^{(k)a}])
\end{align*}$$

with $$c^{(k)} \mapsto_{\alpha} c^{(k)} + \delta c^{(k)}$$ and $$\bar{c}^{(k)} \mapsto_{\alpha} \bar{c}^{(k)} + \delta\bar{c}^{(k)}$$.

One can convince themseves that the second derivative terms will nicely cancel if we choose $$\alpha^a = c^a$$, and take the ghost fields to transform as

$$\delta \bar{c}^{(k)a} = -\frac{1}{\xi_k} \partial^{\mu} A_{\mu a}^{(k)}, \qquad \delta c^{(k)a} = -\frac{1}{2} f^{(k)a}{}_{bc} \alpha^c c^{(k)b}$$

where note that we must be careful when commuting variables since $$c^a c^b = -c^b c^a$$ (Grassmann valued). We have also made use of

$$f^a{}_{e[b} f^e{}_{cd]} = 0$$

which follows from the Jacobi identity. One can then check that, under these choices, the remaining terms also cancel and we get $$\delta\mathcal{L} = 0$$.

As a result, our original gauge invariance has been reduced to a restricted set of gauge transformations that involve our new ghost fields. This restricted set are called the *BRST transformations*.

### 8. Renormalization

In the following, denote $$p = \dim V$$ (where recall $$\Psi \in C^{\infty}(M, V)$$). Recall that physical quantities are related to field expectances of the general form

$$\mathbb{E}_{\Psi \sim S}[\Psi_{i_1}(x_1) \cdots \Psi_{i_n}(x_n)] \equiv \int_{\mathcal{C}} \left[\prod_{j=1}^{N} \text{D}\Psi_j\right] \Psi_{i_1}(x_1) \cdots \Psi_{i_n}(x_n) e^{-S[\Psi]}$$

As outlined in the introduction, in order to construct a well-behaved definition of $$\text{D}\Psi$$, one can consider expanding $$\Psi$$ in some discrete basis $$\{\psi_n\}_n$$ of $$C^{\infty}(M, V)$$:

$$\Psi(x) = \sum_{n \in \mathbb{Z}} a_n \psi_n(x)$$

Then naively, we could define

$$\text{D}\Psi := \prod_{n \in \mathbb{Z}} d a_n$$

but since this is an infinite product, it will generally result in divergences, necessitating *regulation*. In the following, we will consider cutoff regulation: truncate the product by some cutoff parameter $$N \in \mathbb{Z}_{+}$$:

$$\text{D}\Psi^{(N)} := \prod_{n\in \mathbb{Z}_N} d a_n$$

using the notation $$\mathbb{Z}_N := \{-N, -N+1, \ldots, N-1, N\}$$. Of particular relevance to renormalization is the *Fourier basis*, corresponding to $$n \in \mathbb{Z}_N^d$$ (for spacetime dimension $$\dim M = d$$) and $$\psi_n(x) = e^{i\epsilon n \cdot x}$$ for some fixed discretization scale $$\epsilon$$. In this basis, we may write

$$\begin{align*}
\Psi^{(N)}(x) &= \sum_{n \in \mathbb{Z}_N^d} a_n e^{i\epsilon n\cdot x}\\
&= \sum_{k \in \epsilon \mathbb{Z}_N^d} \tilde{\Psi}(k) e^{ik\cdot x}
\end{align*}$$

allowing us to interpret $$k$$ as momentum, using the notation $$\tilde{\Psi}(k)$$ for components. Namely, at a cutoff scale $$N$$ and discretization scale $$\epsilon$$, the maximum allowable energy is $$\Lambda := \sqrt{d} \epsilon N$$. In the following we will use the notation $$\Psi^{(\Lambda)} \equiv \Psi^{(N)}$$, and denote the configuration space spanned by $$\{\psi_n\}_{n \in \mathbb{Z}_N}$$ by $$\mathcal{C}^{(\Lambda)}$$.

Given that the space of field configurations $$\mathcal{C}^{(\Lambda)}$$ is defined relative to some energy scale $$\Lambda$$, this means that theories $$S^{(\Lambda)}: \mathcal{C}^{(\Lambda)} \to \mathbb{R}$$ are \textbf{inherently energy-dependent}, describing physics at a particular energy scale. We say that $$S^{(\Lambda)}$$ is a $$\Lambda$$-energy theory.
* Since experiments on Earth are limited to a low anthropic energy scale $$\mu$$, we can only understand the values of couplings for $$\mu$$-energy theories via experiment. We can attempt to extrapolate this theory to a $$\Lambda$$-energy theory $$S^{(\Lambda)}$$ (for $$\Lambda > \mu$$), which renormalization will allow us to consider, as we will see below.


**Lowering the cutoff parameter.** We will now show that there is a natural procedure for lowering the energy of a theory $$S^{(\Lambda)}$$, resulting in a theory $$S^{(\mu)}$$ for $$\mu < \Lambda$$. First see that

$$\begin{align*}
\text{D}\Psi^{(\Lambda)} = \prod_{k \in \epsilon\mathbb{Z}_N^d} d^d \tilde{\Psi}(k) &= \prod_{k \in \epsilon\mathbb{Z}_M^d} d^d \tilde{\Psi}(k) \prod_{k \in \epsilon\mathbb{Z}_{M+1,N}^d} d^d \tilde{\Psi}(k)\\
&= \text{D}\Psi^{(\mu)} \text{D}\Psi^{(\mu, \Lambda)}
\end{align*}$$

for some lower cutoff $$M \in \mathbb{Z}_{+}$$ with energy scale $$\mu := \sqrt{d} \epsilon M$$, where

$$\Psi^{(\mu)}(x) = \sum_{k \in \epsilon \mathbb{Z}_M^d} \tilde{\Psi}(k) e^{ik\cdot x}, \qquad \Psi^{(\mu, \Lambda)} = \sum_{k \in \epsilon \mathbb{Z}_{M+1, N}^d} \tilde{\Psi}(k) e^{ik\cdot x}$$

which allows us to write

$$\Psi^{(\Lambda)}(x) = \Psi^{(\mu)}(x) + \Psi^{(\mu, \Lambda)}(x)$$

In the following we will write

$$\int \text{D}\Psi^{(\Lambda)} \equiv \int_{\mathcal{C}^{(\Lambda)}} \text{D}\Psi$$

Now see that, for a $$\Lambda$$-energy theory $$S^{(\Lambda)}: \mathcal{C}^{(\Lambda)} \to \mathbb{R}$$, we can write the $$\Lambda$$-energy partition function as

$$\begin{align*}
Z^{(\Lambda)} &= \int_{\mathcal{C}^{(\Lambda)}} \text{D}\Psi \, e^{-S^{(\Lambda)}[\Psi]}\\
&= \int_{\mathcal{C}^{(\mu)}} \text{D}\chi \int_{\mathcal{C}^{(\mu, \Lambda)}} \text{D} \eta \, e^{-S^{(\Lambda)}[\chi + \eta]}\\
&=: \int_{\mathcal{C}^{(\mu)}} \text{D}\chi \, e^{-S^{(\mu)}[\chi]}
\end{align*}$$

where we have defined the *effective action* $$S^{(\mu)}: \mathcal{C}^{(\mu)} \to \mathbb{R}$$ by

$$S^{(\mu)}[\chi] := -\log\left(\int_{\mathcal{C}^{(\mu, \Lambda)}} \text{D} \eta \, e^{-S^{(\Lambda)}[\chi + \eta]}\right)$$

Furthermore, since in general we can decompose

$$S^{(\Lambda)}[\chi + \eta] = S^{(\Lambda)}[\chi] + S_0^{(\Lambda)}[\eta] + S_I^{(\Lambda)}[\chi, \eta]$$

we can write

$$S^{(\mu)}[\chi] = S^{(\Lambda)}[\chi] + S^{(\Lambda\to\mu)}[\chi]$$

defining

$$S^{(\Lambda\to\mu)}[\chi] := -\log\left(\int_{\mathcal{C}^{(\mu, \Lambda)}} \text{D} \eta \, e^{-S_0^{(\Lambda)}[\eta] - S_I^{(\Lambda)}[\chi, \eta]}\right)$$

* Note that $$S^{(\Lambda)}[\chi]$$ -- which is defined on $$\mathcal{C}^{(\Lambda)}$$ -- is still well-defined for $$\chi \in \mathcal{C}^{(\mu)}$$ since $$\mathcal{C}^{(\mu)} \subset \mathcal{C}^{(\Lambda)}$$.

Denote the space of $$\Lambda$$-energy theories by $$\mathcal{S}^{(\Lambda)} \subseteq (\mathcal{C}^{(\Lambda)} \to \mathbb{R})$$. Above, we have shown that there is a particularly natural lowering operation

$$\mathfrak{L}^{(\Lambda\to\mu)}: \mathcal{S}^{(\Lambda)} \to \mathcal{S}^{(\mu)}, \quad S^{(\Lambda)} \mapsto S^{(\mu)} = S^{(\Lambda)} + S^{(\Lambda\to\mu)}$$

mapping $$\Lambda$$-energy theories to $$\mu$$-energy theories by integrating out momentum modes between $$(\mu, \Lambda]$$, which has the effect of shifting by $$S^{(\Lambda\to\mu)}$$.

**Extrapolating to higher energies.** In reality, the experiments we can run on Earth are at a relatively low ``anthropic`` energy scale $$\mu$$ and so the situation is reversed: experiments help us to determine a $$\mu$$-energy theory $$S^{(\mu)}$$ (e.g. the Standard Model) that agrees with experimental results on Earth, and we wish to extrapolate this theory to a theory $$S^{(\Lambda)}$$ that works at higher energy levels $$\Lambda > \mu$$. One may even wish to extend the theory to arbitrarily high energies $$\Lambda \to \infty$$.

Concretely, given a low-energy theory $$S^{(\mu)}$$ (determined via experiments on Earth at energy scale $$\mu$$), the requirement that the higher-energy theory $$S^{(\Lambda)}$$ agrees with experiments at energy $$\mu$$ corresponds to $$S^{(\Lambda)}$$ satisfying

$$\begin{equation}
\label{eqn:lower}
\mathfrak{L}^{(\Lambda\to\mu)}[S^{(\Lambda)}] = S^{(\mu)}
\end{equation}$$

*Asymptotic freedom.* We say that $$S^{(\mu)}$$ is *asymptotically free* if such a theory $$S^{(\Lambda)}$$ exists for $$\Lambda = \infty$$. For this theory $$S^{(\infty)}$$ to be valid, physical predictions must be finite, corresponding to finite correlators:

$$\mathbb{E}_{\Psi \sim S^{(\infty)}}[\Psi_{i_1}(x_1) \cdots \Psi_{i_n}(x_n)] \;\; \text{finite}$$

In the following, given some fixed $$S^{(\mu)}$$, we will consider constructing such a $$S^{(\Lambda)}$$ via the approach of *counterterms*. In particular, denoting $$S^{(\mu\to\Lambda)} \equiv -S^{(\Lambda\to\mu)}$$, we may write

$$S^{(\Lambda)}[\chi] = S^{(\mu)}[\chi] + S^{(\mu\to\Lambda)}[\chi]$$

As we will see, we will then design the contributions $$S^{(\mu\to\Lambda)}$$ to be such that $$S^{(\Lambda)}$$ is well-defined in the limit $$\Lambda \to \infty$$ (i.e. finite correlators).

In the following we will write

$$\begin{equation}
\label{eqn:actcounter}
S^{(\Lambda)}[\chi] = \sum_{m, n} \lambda_{m, n}^{(\Lambda)} \int_M d^d x \, L_{m, n}[\chi; x]
\end{equation}$$

where $$L_{m, n}[\chi; x]$$ denotes all contributions with $$m$$ powers of $$\chi$$ and $$n$$ derivatives of $$\chi$$, with $$\{\lambda_{m, n}^{(\Lambda)}\}_{m, n}$$ the associated couplings. Similarly, we will denote the couplings of $$S^{(\mu)}$$ by $$\{\lambda_{m,n}^{(\mu)}\}_{m, n}$$, and of $$S^{(\mu\to\Lambda)}$$ by $$\{\delta\lambda_{m, n}^{(\mu\to\Lambda)}\}_{m, n}$$. Note that $$\lambda_{m, n}^{(\Lambda)} = \lambda_{m, n}^{(\mu)} + \delta\lambda_{m, n}^{(\mu\to\Lambda)}$$. We will denote the kinetic contribution by the indices $$(m, n) = (a, b)$$ (usually $$(a, b) = (2, 2)$$).


We will assume that $$S^{(\mu)}$$ is *canonically normalized*, in the sense that its kinetic contribution has a coefficient of $$1$$, i.e. $$\lambda_{a, b}^{(\mu)} = 1$$. Generally $$\lambda_{a, b}^{(\Lambda)} \neq 1$$ due to the contributions $$S^{(\mu\to\Lambda)}$$. To canonically normalize $$S^{(\Lambda)}$$, we will introduce the normalized field

$$\hat{\chi} := Z^{1/a} \chi$$

defining $$Z := 1 + \delta\lambda_{a, b}^{(\mu\to\Lambda)}$$. Then Equation \ref{eqn:actcounter} becomes

$$\hat{S}^{(\Lambda)}[\hat{\chi}] = \hat{S}^{(\mu)}[\hat{\chi}] + \hat{S}^{(\mu\to\Lambda)}[\hat{\chi}]$$

where we have defined the normalized actions

$$\hat{S}^{(\Lambda)}[\hat{\chi}] := S^{(\Lambda)}[Z^{-1/a} \hat{\chi}], \quad \hat{S}^{(\mu)}[\hat{\chi}] := S^{(\mu)}[Z^{-1/a} \hat{\chi}], \quad \hat{S}^{(\mu\to\Lambda)}[\hat{\chi}] := S^{(\mu\to\Lambda)}[Z^{-1/a} \hat{\chi}]$$

with couplings $$\{\hat{\lambda}^{(\Lambda)}_{m, n}\}_{m, n}$$, $$\{\hat{\lambda}^{(\mu)}_{m, n}\}_{m, n}$$, $$\{\delta\hat{\lambda}_{m, n}^{(\mu\to\Lambda)}\}_{m, n}$$ respectively. Importantly, we now have that $$\hat{S}^{(\Lambda)}$$ is canonically normalized, with $$\hat{\lambda}_{a, b}^{(\Lambda)} = 1$$. Explicitly we can write these new couplings in terms of the original couplings:

$$\begin{align*}
\hat{\lambda}^{(\Lambda)}_{m, n} &= Z^{-m/a} \lambda_{m, n}^{(\Lambda)}\\
&= \frac{\lambda_{m, n}^{(\mu)} + \delta\lambda_{m, n}^{(\mu\to\Lambda)}}{Z^{m/a}}
\end{align*}$$

One often calls $$\hat{S}^{(\Lambda)}$$ the *bare* theory, and $$S^{(\mu)}$$ the *renormalized* theory.

Counterterm renormalization involves designing the counterterms $$\delta\lambda_{m, n}^{(\mu\to\infty)}$$ such that the infinite energy theory $$\hat{S}^{(\infty)}$$ of couplings $$\hat{\lambda}^{(\infty)}_{m, n}$$ has finite correlators. As we outline in Section 9, one can compute correlators perturbatively. In our context, we can do so by 
taking $$\delta\lambda_{m, n}^{(\mu\to\infty)}$$ to be sufficiently small such that we can treat $$\hat{S}^{(\mu\to\infty)}$$ as a perturbation. The general procedure of counterterm renormalization includes:
1. Compute correlators perturbatively under $$\hat{S}^{(\Lambda)} = \hat{S}^{(\mu)} + \hat{S}^{(\mu\to\Lambda)}$$ by treating $$\hat{S}^{(\mu\to\Lambda)}$$ as a perturbation.
2. Choose $$\delta\lambda_{m, n}^{(\mu\to\Lambda)}$$ appropriately (by some *prescription*) to ensure that these correlators remain finite in the limit $$\Lambda \to \infty$$ and that the counterterms $$\delta\lambda_{m, n}^{(\mu\to\Lambda)}$$ also remain finite in this limit (such that the perturbative assumption is valid).
3. If this procedure can ensure that all $$n$$-pt correlators are finite for arbitrary $$n \in \mathbb{Z}_{+}$$, then we say that the theory $$S^{(\mu)}$$ is *asymptotically free*.

In step 2, the *prescription* of counterterm renormalization describes how one chooses $$\delta\lambda_{m, n}^{(\mu\to\Lambda)}$$, since generally various different choices can result in finite correlators (e.g. can add an arbitrary constant). Different prescription schemes include the minimal subtraction (MS) scheme, the modified minimal subtraction ($$\bar{\text{MS}}$$) scheme, and the mass-shell scheme.

**Raising the discretization scale.** In the following, we will more explicitly write $$S^{(N, \epsilon)} \equiv S^{(\Lambda)}$$ to denote the theory defined on configuration space $$\mathcal{C}^{(N, \epsilon)} \equiv \mathcal{C}^{(\Lambda)}$$, with associated field configuration $$\Psi^{(N, \epsilon)} \equiv \Psi^{(\Lambda)}$$. Through the lowering operator $$\mathfrak{L}^{(N \to M)} \equiv \mathfrak{L}^{(\Lambda\to\mu)}$$, we saw that we can transform a $$(N, \epsilon)$$-theory into a $$(M, \epsilon)$$-theory for $$M < N$$, which has the effect of lowering the energy scale from $$\Lambda \propto N\epsilon$$ to $$\mu \propto M\epsilon$$.

It turns out that we can also construct a mapping from a $$(N, \epsilon)$$-theory to a $$(N, \eta)$$-theory with a raised cutoff $$\eta > \epsilon$$. To see this, we will first introduce a new field $$\xi$$ to have Fourier components

$$\tilde{\xi}(k) := (\eta/\epsilon)^d \tilde{\Psi}(\epsilon k/\eta) \implies d^d \tilde{\Psi}(k) = d^d \tilde{\xi}(\eta k/\epsilon)$$

which allows us to write

$$\begin{align*}
\text{D}\Psi^{(N, \epsilon)} = \prod_{k \in \epsilon \mathbb{Z}_N^d} d^d \tilde{\Psi}(k) &= \prod_{k \in \epsilon \mathbb{Z}_N^d} d^d \tilde{\xi}(\eta k/\epsilon)\\
&= \prod_{k \in \eta \mathbb{Z}_N^d} d^d \tilde{\xi}(k)\\
&= \text{D}\xi^{(N, \eta)}
\end{align*}$$

One can also show that

$$\Psi^{(N, \epsilon)}(x) = (\epsilon/\eta)^d \xi^{(N, \eta)}(\epsilon x/\eta)$$

These two results let us write

$$\begin{align*}
Z^{(N, \epsilon)} &= \int_{\mathcal{C}^{(N, \epsilon)}} \text{D}\Psi \, e^{-S^{(N, \epsilon)}[\Psi]}\\
&= \int_{\mathcal{C}^{(N, \eta)}} \text{D}\xi \, e^{-S^{(N, \epsilon)}[(\epsilon/\eta)^d \xi(\epsilon \, \cdot/\eta)]}
\end{align*}$$

where see that

$$\begin{align*}
S^{(N, \epsilon)}[(\epsilon/\eta)^d \xi(\epsilon \, \cdot/\eta)] &= \sum_{m, n} \lambda_{m, n}^{(N, \epsilon)} \int_M d^d x \, L_{m, n}[(\epsilon/\eta)^d \xi(\epsilon \, \cdot/\eta); x]\\
&= \sum_{m, n} (\epsilon/\eta)^{dm + n} \lambda_{m, n}^{(N, \epsilon)} \int_M d^d x \, L_{m, n}[\xi; \epsilon x/\eta]\\
&= \sum_{m, n} (\epsilon/\eta)^{d(m-1) + n} \lambda_{m, n}^{(N, \epsilon)} \int_M d^d \tilde{x} \, L_{m, n}[\xi; \tilde{x}]\\
&= \sum_{m, n} \underbrace{\frac{(\epsilon/\eta)^{n-d+m(d-b)/a}}{(\lambda_{a, b}^{(N, \epsilon)})^{m/a}} \lambda_{m, n}^{(N, \epsilon)}}_{=: \, \tilde{\lambda}^{(N, \eta)}} \int_M d^d \tilde{x} \, L_{m, n}[\tilde{\xi}; \tilde{x}]\\
&= \sum_{m, n} \tilde{\lambda}^{(N, \eta)}_{m, n} \int_M d^d \tilde{x} \, L_{m, n}[\tilde{\xi}; \tilde{x}]\\
&=: \tilde{S}^{(N, \eta)}[\tilde{\xi}]
\end{align*}$$

defining the rescaled field

$$\tilde{\xi} := (\epsilon/\eta)^{d+(b-d)/a} (\lambda_{a, b}^{(N, \epsilon)})^{1/a} \xi$$

precisely such that $$\tilde{S}^{(N, \eta)}[\tilde{\xi}]$$ is canonically normalized, i.e. $$\tilde{\lambda}_{a, b}^{(N, \eta)} = 1$$. As a result, we can write

$$Z^{(N, \epsilon)} \propto \int_{\mathcal{C}^{(N, \eta)}} \text{D}\tilde{\xi} \, e^{-\tilde{S}^{(N, \eta)}[\tilde{\xi}]}$$

The above has therefore shown that there is a particularly natural raising operation for the discretization scale

$$\mathfrak{R}^{(\epsilon\to\eta)}: \mathcal{S}^{(N, \epsilon)} \to \mathcal{S}^{(N, \eta)}, \quad S^{(N, \epsilon)} \mapsto \tilde{S}^{(N, \eta)}$$

* This procedure applies identically to lowering the discretization scale (i.e. $$\eta < \epsilon$$), however for our purposes we will only care for raising. This is in contrast to lowering the cutoff parameter where there is no analogous means to raise the cutoff parameter.

*Coarse-graining.* Consider the map

$$\mathfrak{C}^{(N \to M)}: \mathcal{S}^{(N, \epsilon)} \to \mathcal{S}^{(M, N\epsilon/M)}$$

$$\mathfrak{C}^{(N \to M)} := \mathfrak{R}^{(\epsilon\to N\epsilon/M)} \circ \mathfrak{L}^{(N \to M)}$$

If a theory $$S^{(N, \epsilon)}$$ has couplings $$\{\lambda_{m, n}^{(N, \epsilon)}\}_{m, n}$$, then $$\mathfrak{C}^{(N \to M)}[S^{(N, \epsilon)}]$$ has couplings

$$\begin{equation}
\label{eqn:coarsecouplings}
\lambda^{(M, N\epsilon/M)}_{m, n} = (M/N)^{n-d+m(d-b)/a} \frac{\lambda_{m, n}^{(N, \epsilon)} + \delta\lambda_{m, n}^{(N \to M, \epsilon)}}{(\lambda_{a, b}^{(N, \epsilon)} +\delta\lambda_{a, b}^{(N\to M, \epsilon)})^{m/a}}
\end{equation}$$

This operation is particularly special in that it \textbf{preserves energy scales}, since $$N\epsilon = M(N\epsilon/M)$$. It also maps between canonically normalized theories.

As a result, we can view $$\mathfrak{C}^{(N\to M)}$$ as coarse-graining a theory by decreasing the cutoff and raising the discretization scale, while maintaining the same overall energy scale.

We can classify the possible contributions $$\{L_{m, n}\}_{m, n}$$ to a theory based on how their couplings $$\{\lambda_{m, n}^{(N, \epsilon)}\}_{m, n}$$ behave under coarse-graining. Namely, from Equation \ref{eqn:coarsecouplings}, we have that for each $$(m, n)$$:
1. $$n-d+m(d-b)/a > 0$$: Implies that $$\lambda^{(M, N\epsilon/M)}_{m, n} < \lambda^{(N, \epsilon)}_{m, n}$$, i.e. the $$(m, n)$$ contribution becomes weaker under coarse-graining. In this case, we say that the contribution is *irrelevant*.
2. $$n-d+m(d-b)/a = 0$$: Implies that $$\lambda^{(M, N\epsilon/M)}_{m, n} \approx \lambda^{(N, \epsilon)}_{m, n}$$, i.e. the $$(m, n)$$ contribution is invariant under coarse-graning. In this case, we say that the contribution is *marginal*.
3. $$n-d+m(d-b)/a < 0$$: Implies that $$\lambda^{(M, N\epsilon/M)}_{m, n} > \lambda^{(N, \epsilon)}_{m, n}$$, i.e. the $$(m, n)$$ contribution becomes stronger under coarse-graining. In this case, we say that the contribution is *relevant*.

Todo: usually one interprets irrelevant terms as terms that can be ignored at low energy. But under coarse-graining the energy scale is not changing, so how can we remedy this difference in viewpoint?

Todo: the general prescription for writing down low-energy theories: just write down all relevant and marginal terms that satisfy spacetime and gauge invariance. The Standard Model action can be derived by such a procedure (but also with some extra terms, like the theta term).

Todo: how is gauge invariance affected by cutoff regularization? (in comparison, Fujikawa regulation preserves gauge invariance)

Todo: The existence of Landau poles suggests that the Standard Model may not be asymptotically free, however this analysis is only perturbative and hence does not act as a proof.

### 9. Computing correlators perturbatively

As mentioned previously, physical quantities such as scattering probabilities can be written in terms of correlators: expectances of the general form

$$\mathbb{E}_{\Psi \sim S}[\Psi_{i_1}(x_1) \cdots \Psi_{i_n}(x_n)] := \frac{1}{Z} \int_{\mathcal{C}} \text{D}\Psi \, \Psi_{i_1}(x_1) \cdots \Psi_{i_n}(x_n) e^{-S[\Psi]}$$

for arbitrary $$n \in \mathbb{Z}_{+}$$, indices $$(i_1, \ldots, i_n) \in \{1, \ldots, N\}^n$$, and points $$(x_1, \ldots, x_n) \subset M$$.

But how can we actually compute correlators? In the

Generally we can write

$$\mathbb{E}_{\Psi \sim S}[\Psi_{i_1}(x_1) \cdots \Psi_{i_n}(x_n)] = (-1)^n \frac{\delta^n Z[J_1, \ldots, J_N]}{\delta J_{i_n}(x_n) \cdots \delta J_{i_1}(x_1)}\bigg|_{J_1 = \cdots = J_N = 0}$$

where we have introduced a *source* $$J_i \in C^{\infty}(M, V^{(i)})$$ for each field $$\Psi_i$$ and defined

$$Z[J_1, \ldots, J_N] := \int_{\mathcal{C}} \text{D}\Psi \, e^{-S[\Psi] - \sum_{i=1}^{N} \int_M d^d x \, J_i(x) \cdot \Psi_i(x)}$$

* The ordering of the functional derivatives is significant, since the Standard Model features Grassmann-valued fields (i.e. spinors) that anti-commute, with their sources also being Grassmann-valued. 

Decomposing $$S[\Psi] = S_0[\Psi] + \Delta S[\Psi]$$ into *free* contributions $$S_0$$ (i.e. kinetic and mass terms) and *perturbative* contributions $$\Delta S$$ (i.e. interactions with small couplings), we are able to compute $$Z[J_1, \ldots, J_N]$$ perturbatively via Taylor expansion of:

$$Z[J_1, \ldots, J_N] = \exp\left(-\Delta S\left[\frac{\delta}{\delta J_1}, \ldots, \frac{\delta}{\delta J_N}\right]\right) Z_0[J_1, \ldots, J_N]$$

with $$Z_0[J_1, \ldots, J_N]$$ defined

$$Z_0[J_1, \ldots, J_N] := \int_{\mathcal{C}} \text{D}\Psi \, e^{-S_0[\Psi] - \sum_{i=1}^{N} \int_M d^d x \, J_i(x) \cdot \Psi_i(x)}$$

Because $$S_0[\Psi]$$ consists of free contributions and is at most quadratic in $$\Psi$$, and since the source terms are linear in $$\Psi$$, one can usually complete the square within the exponent and determine a closed form for $$Z_0[J_1, \ldots, J_N]$$.
* For example, a real scalar field $$\phi$$ has free action
    
    $$S_0[\phi] = \frac{1}{2} \partial_{\mu} \phi \partial^{\mu} \phi - \frac{1}{2} m^2 \phi^2$$

    and by using integration by parts and completing the square, one can find that

    $$Z_0[J] = Z_0[0] \exp\left(-\frac{1}{2} \int d^d x \, d^d y \, J(x) D_F(x-y) J(y)\right)$$

    where $$D_F(x-y)$$ is the propagator for a real scalar field, defined by the Green's function property

    $$i(\square + m^2) D_F(x-y) = \delta(x-y)$$

**Feynman rules.** The correlator *Feynman rules* are a distilled set of rules for computing the correlator term-by-term *perturbatively* (via Taylor expansion), as described above.
* There are also a separate set of Feynman rules for computing scattering amplitudes directly.

Todo: Give explicit conversion between Feynman diagrams and relevant integrals.





















## Appendix A.1. Classifying semi-simple Lie groups and their representations

To understand the kinds of invariances that our theory can have -- specifically, the available choices of Lie groups for the gauge group -- it is useful to classify the available Lie groups. It happens that each Lie group has a (not necessarily unique) \textit{Lie algebra}, and it is much easier to classify the available Lie algebras, and so this will be the focus of A.1.1.

Furthermore, ultimately these Lie groups must act on our field content, which requires choosing a \textit{representation} of the Lie group (as outlined in the Introduction). In A.1.2, we will classify the available representations to better understand the choices we can make.

The unitary group $$U(n)$$ is not semi-simple, and so the classification of its representations must be approached differently. We will explore this in Appendix A.1.3.

### A.1.1. Classifying Lie algebras

#### A.1.1.1. Definitions

A Lie algebra $$(\mathfrak{g}, [\cdot, \cdot])$$ is a vector space $$\mathfrak{g}$$ (over some $$\mathbb{F}$$) paired with a bracket

$$[\cdot, \cdot]: \mathfrak{g} \times \mathfrak{g} \to \mathfrak{g}$$

with the bracket satisfying (i) anti-symmetry (ii) bilinearity (iii) the Jacobi identity:

$$[X, [Y, Z]] + [Y, [Z, X]] + [Z, [X, Y]] = 0$$

which is a convenient property for various proofs.
* Roughly, under certain assumptions, every Lie group has a (not necessarily unique) Lie algebra -- constructed using the tangent space $$T_e(G)$$ at the identity element of $$G$$ -- and we can map back from a Lie algebra to a Lie group via the exponential map $$\exp: \mathfrak{g} \to G$$.

For a vector space $$V$$ with some associative product $$*$$ (e.g. $$V$$ a matrix space with matrix multiplication $$*$$), then $$(V, [\cdot, \cdot])$$ is a Lie algebra if one defines $$[\cdot, \cdot]$$ by

$$[X, Y] := X * Y - Y * X$$

For two Lie algebras $$\mathfrak{g}, \mathfrak{h}$$, we say that $$f: \mathfrak{g} \to \mathfrak{h}$$ is a (Lie algebra) homomorphism iff (i) it is a vector space homomorphism (i.e. $$f$$ linear) and (ii) $$f$$ commutes with $$[\cdot, \cdot]$$ in the sense that:

$$f([X, Y]) = [f(X), f(Y)]$$

As usual, we say that $$f: \mathfrak{g} \to \mathfrak{h}$$ is an isomorphism iff $$f$$ is a bijective homomorphism whose inverse is also a homomorphism. If such an isomorphism exists, we write $$\mathfrak{g} \cong \mathfrak{h}$$.

For a Lie algebra $$\mathfrak{g}$$, a sub-algebra $$\mathfrak{h} \leq \mathfrak{g}$$ is said to be ideal iff

$$[\mathfrak{g}, \mathfrak{h}] \subseteq \mathfrak{h}$$

using the notation $$[\mathfrak{g}, \mathfrak{h}] \equiv \{[X, Y]: X \in \mathfrak{g}, Y \in \mathfrak{h}\}$$. Any Lie algebra $$\mathfrak{g}$$ always has the trivial ideals $$\mathfrak{h} = \mathfrak{g}$$ and $$\mathfrak{h} = \{0\}$$.

\textit{Semi-simplicity.} We say that $$\mathfrak{g}$$ is \textit{semi-simple} if its non-trivial ideal sub-algebras $$\mathfrak{h}$$ are non-abelian.

For sub-algebras $$\mathfrak{h}$$ that are instead abelian, we say that $$\mathfrak{h}$$ is a \textit{maximal abelian subalgebra} if it is not contained in any larger sub-algebra.

For a complex semi-simple Lie algebra $$\mathfrak{g}$$, we say that a sub-algebra $$\mathfrak{h} \leq \mathfrak{g}$$ is a \textit{Cartan subalgebra} (CSA) iff $$\mathfrak{h}$$ is abelian \& maximal, and $$\text{ad}_H: \mathfrak{g} \to \mathfrak{g}$$ is diagonalizable for all $$H \in \mathfrak{h}$$.
* Any complex semi-simple Lie algebra has a class of CSAs. Indeed, all CSAs are related by conjugation.
* The fact that $$\mathfrak{g}$$ is semi-simple implies that any Cartan sub-algebra $$\mathfrak{h}$$ is non-ideal.
* $$\mathfrak{g}$$ being semi-simple also ensures that a CSA exists.
* The reason why "complex" is important is that it gives us algebraic closure. In fact, we can replace $$\mathbb{C}$$ with any algebraically closed field (I think?).

Semi-simple Lie algebras are special in that there is a natural metric on the Lie algebra when we have semi-simplicity (indeed, $$\mathfrak{g}$$ is semi-simple iff its Killing form is non-degenerate, which allows us to define a metric).

A \textit{simple} Lie algebra is a non-abelian Lie algebra with no non-trivial ideals. We can always write a semi-simple Lie algebra as a direct sum of simple Lie algebras. Indeed, an alternative definition of a semi-simple Lie algebra is as any direct sum of simple Lie algebras.

#### A.1.1.2. Constructing a basis

In the following, we will \textbf{classify all complex semi-simple finite-dim Lie algebras} $$\mathfrak{g}$$, making use of the existence of a CSA $$\mathfrak{h} \leq \mathfrak{g}$$.

Given a CSA $$\mathfrak{h}$$ of our Lie algebra $$\mathfrak{g}$$, abelian-ness tells us that

$$\text{ad}_H(H') = 0 \; \forall \; H, H' \in \mathfrak{h}$$

$$\implies [\text{ad}_H, \text{ad}_{H'}] = \text{ad}_{[H, H']} \equiv 0$$

and further, since $$\text{ad}_H$$ is diagonalizable, the collection $$\{\text{ad}_H\}_{H \in \mathfrak{h}}$$ is simltaneously diagonalizable (since diagonalizable operators that commute with eachother are simultaneously diagonalizable). By spectral decomposition theorem, this means that $$\mathfrak{g}$$ is spanned by the simultaneous eigenvectors of $$\{\text{ad}_H\}_{H \in \mathfrak{h}}$$.

Overall we can write $$\mathfrak{g}$$ as a span of the Cartan-Weyl basis:

$$\mathfrak{g} = \text{span}_{\mathbb{C}} \mathcal{B}, \quad \mathcal{B} := \underbrace{\{H_i\}_{i=1}^{r}}_{\text{basis of} \; \mathfrak{h}} \cup \{E_{\alpha}\}_{\alpha \in \Phi}$$

where $$\{E_{\alpha}\}_{\alpha \in \Phi}$$ are simultaneous eigenvectors of $$\{\text{ad}_H\}_{H \in \mathfrak{h}}$$ satisfying

$$\text{ad}_H(E_{\alpha}) = \alpha(H) E_{\alpha} \quad \forall \; \; H \in \mathfrak{h}$$

for roots $$\Phi \ni \alpha: \mathfrak{h} \to \mathbb{C}$$, and with $$\text{ad}_H(H_i) = 0 \; \forall \; i$$ (since $$H_i \in \mathfrak{h}$$), and defining $$r := \dim\mathfrak{h}$$. Further, one can show that the roots $$\alpha \in \Phi$$ are linear, and hence $$\Phi \subset \mathfrak{h}^{*}$$. 
* Linearity of roots $$\alpha \in \Phi$$ follows simply from their definition and using linearity of $$\text{ad}: X \mapsto \text{ad}_X$$.
* One can show that the roots $$\alpha \in \Phi$$ are non-degenerate as eigenvalues.

Additionally, we will generally have $$\dim\mathfrak{h} \leq \dim\mathfrak{g}/2$$, which implies that $$|\Phi| \geq \dim\mathfrak{h}^{*}$$. As we will see later, one can show that the roots span $$\mathfrak{h}^{*}$$, i.e. $$\mathfrak{h}^{*} = \text{span}_{\mathbb{C}}\Phi$$. As a result, $$\Phi$$ can be viewed as an overcomplete basis for $$\mathfrak{h}^{*}$$.
* Since $$\mathfrak{h}$$ is a CSA, it is the maximal abelian sub-algebra, and hence no other elements outside of $$\mathfrak{h}$$ can map to $$0$$ under $$\text{ad}_H$$ (as if an element $$X \in \mathfrak{g}\backslash\mathfrak{h}$$ satisfied $$\text{ad}_H(X) = 0$$ for all $$H \in \mathfrak{h}$$, then $$\mathfrak{h} \cup \text{span}_{\mathbb{C}}\{X\}$$ would be an abelian subalgebra containing $$\mathfrak{h}$$, contradicting maximality of the CSA $$\mathfrak{h}$$).
* As a result, $$0 \notin \Phi$$.


#### A.1.1.3. Relations

By definition, this set of generators has relations

$$[H_i, H_j] = 0, \quad [H_i, E_{\alpha}] = \alpha(H_i) E_{\alpha}$$

and further, $$[E_{\alpha}, E_{\beta}]$$ can be determined via Jacobi's identity:

$$\text{ad}_{H}([E_{\alpha}, E_{\beta}]) = (\alpha(H) + \beta(H)) [E_{\alpha}, E_{\beta}]$$

$$\implies \begin{cases}
[E_{\alpha}, E_{\beta}]  \in \mathfrak{h}, & \alpha+\beta = 0\\
[E_{\alpha}, E_{\beta}]=N_{\alpha, \beta} E_{\alpha+\beta}, & \alpha+\beta \in \Phi\\
[E_{\alpha}, E_{\beta}]=0, & \text{otherwise}
\end{cases}$$

for proportionality factor $$N_{\alpha, \beta} \neq 0$$.

\textit{Natural metric for semi-simple Lie algebras.} For the following, introduce the Killing form $$\kappa$$: a symmetric $$(0, 2)$$ tensor defined by

$$\kappa(X, Y) := \text{tr}(\text{ad}_X \circ \text{ad}_Y)$$

We can find its components more explicitly. For some basis $$\{T_i\}_i \subset \mathfrak{g}$$ with structure constants $$[T_i, T_j] = f^k{}_{ij} T_k$$, expanding the trace in this basis gives us

$$\begin{align*}
\kappa_{ij} := \kappa(T_i, T_j) &= \text{tr}(\text{ad}_{T_i} \circ \text{ad}_{T_j})\\
&= [T_i, [T_j, T_k]]^k\\
&= f^k{}_{il} f^l{}_{jk}
\end{align*}$$

We have that $$\kappa$$ is non-degenerate (equivalent to the matrix $$\kappa_{ij}$$ being invertible in any basis) iff $$\mathfrak{g}$$ is semi-simple. 
* This gives us a way to show a Lie algebra $$\mathfrak{g}$$ is semi-simple: just check whether its Killing form is non-singular.

Therefore, in the case of $$\mathfrak{g}$$ being semi-simple, we can view $$\kappa$$ as analogous to a metric (i.e. a symmetric, non-degenerate $$(0, 2)$$ tensor). As we will see, we will lower indices using $$\kappa_{ij}$$ and raise indices using $$\kappa^{ij} \equiv (\kappa^{-1})^{ij}$$. In the following we will work with the inner product

$$(X, Y) := \kappa(X, Y) = \kappa_{ij} X^i Y^j = \kappa^{ij} X_i Y_j$$

for any $$X, Y \in \mathfrak{g}$$. This inner product also naturally extends to an inner product over $$\mathfrak{g}^{*}$$: for $$\omega, \eta \in \mathfrak{g}^{*}$$ we will write

$$(\omega, \eta) := (X_{\omega}, X_{\eta}) \equiv \kappa^{ij} \omega_i \eta_j$$

where (analogous to Riesz representation theorem) for $$\lambda \in \mathfrak{g}^{*}$$ we define $$X_{\lambda} \in \mathfrak{g}$$ as satisfying

$$\lambda(Y) =: \kappa(X_{\lambda}, Y) \quad \forall \; Y \in \mathfrak{g}$$

which implies that $$X_{\lambda} = \kappa^{ij} \lambda_j T_i$$ for basis $$\{T_i\}_i \subset \mathfrak{g}$$ (found by writing in components).

$$\kappa$$ is a special metric in that it satisfies a Jacobi identity-like property:

$$\kappa(X, [Y, Z]) = \kappa(Y, [Z, X]) = \kappa(Z, [X, Y])$$

* Proof: using the fact that $$\text{ad}$$ is a rep of $$\mathfrak{g}$$ (which can be proven via Jacobi's identity), we have

    $$\begin{align*}
    \kappa(X, [Y, Z]) &= \text{tr}(\text{ad}_X \circ \text{ad}_{[Y, Z]})\\
    &= \text{tr}(\text{ad}_X \circ [\text{ad}_Y, \text{ad}_Z])\\
    &= \text{tr}(\text{ad}_Y \circ [\text{ad}_Z, \text{ad}_X]) \equiv \kappa(Y, [Z, X])\\
    &= \text{tr}(\text{ad}_Z \circ [\text{ad}_X, \text{ad}_Y]) \equiv \kappa(Z, [X, Y])
    \end{align*}$$

    using the cyclic property of $$\text{tr}$$.


For $$\alpha \in \mathfrak{h}^{*}$$, we will use similar notation to above, with $$H_{\alpha} = \kappa^{ij} \alpha_j H_i \in \mathfrak{h}$$ (with $$\kappa_{ij} \equiv \kappa(H_i, H_j)$$) satisfying

$$\alpha(H) = \kappa(H_{\alpha}, H) \quad \forall \; H \in \mathfrak{h}$$

\textit{Relations.} Using the properties established above, we can write

$$[E_{\alpha}, E_{-\alpha}] = (E_{\alpha}, E_{-\alpha}) H_{\alpha}$$

* This follows from the invariance property of $$\kappa$$:

    $$\kappa(H, [E_{\alpha}, E_{-\alpha}]) = \kappa(E_{\alpha}, [E_{-\alpha}, H]) = \underbrace{\alpha(H)}_{\kappa(H_{\alpha}, H)} \kappa(E_{\alpha}, E_{-\alpha})$$

    $$\implies \kappa(H, [E_{\alpha}, E_{-\alpha}] - \kappa(E_{\alpha}, E_{-\alpha}) H_{\alpha}) = 0 \quad \forall \; H \in \mathfrak{h}$$

    $$\implies [E_{\alpha}, E_{-\alpha}] = \kappa(E_{\alpha}, E_{-\alpha}) H_{\alpha} \equiv (E_{\alpha}, E_{-\alpha}) H_{\alpha}$$

    by non-degeneracy of $$\kappa$$.

Instead of considering the generators $$\{H_i, E_{\alpha}\}_{i, \alpha \in \Phi}$$, we will instead consider $$\{H_{\alpha}, E_{\alpha}\}_{\alpha \in \Phi}$$ to be our generators, which (using $$H_{\alpha} = \kappa^{ij} \alpha_j H_i$$) have relations

$$[H_{\alpha}, H_{\beta}] = 0, \quad [H_{\alpha}, E_{\beta}] = (\alpha, \beta) E_{\beta},$$

$$[E_{\alpha}, E_{\beta}] = \begin{cases}
(E_{\alpha}, E_{-\alpha}) H_{\alpha} & \alpha+\beta=0\\
N_{\alpha, \beta} E_{\alpha+\beta} & \alpha+\beta \in \Phi\\
0 & \text{otherwise}
\end{cases}$$

Note that the sub-algebra generated by $$(H_{\alpha}, E_{\alpha}, E_{-\alpha})$$ has relations

$$[H_{\alpha}, E_{\pm\alpha}] = \pm(\alpha, \alpha)E_{\pm\alpha}, \quad [E_{\alpha}, E_{-\alpha}] = (E_{\alpha}, E_{-\alpha}) H_{\alpha}$$

We would like for these relations to precisely match the relations of $$\mathfrak{su}(2)_{\mathbb{C}}$$. To achieve this, we define rescaled elements

$$h_{\alpha} := \frac{2}{(\alpha, \alpha)} H_{\alpha}, \quad e_{\alpha} := \sqrt{\frac{2}{(\alpha, \alpha) (E_{\alpha}, E_{-\alpha})}} E_{\alpha}$$

with $$(h_{\alpha}, e_{\alpha}, e_{-\alpha})$$ precisely satisfying the $$\mathfrak{su}(2)_{\mathbb{C}}$$ algebra:

$$[h_{\alpha}, e_{\pm\alpha}] = \pm 2e_{\pm\alpha}, \quad [e_{\alpha}, e_{-\alpha}] = h_{\alpha}$$

That is, each root $$\alpha \in \Phi$$ is associated with a subalgebra corresponding to $$\mathfrak{su}(2)_{\mathbb{C}}$$.

These rescaled elements $$\{h_{\alpha}, e_{\alpha}, e_{-\alpha}\}_{\alpha}$$ have global relations

$$[h_{\alpha}, h_{\beta}] = 0, \quad [h_{\alpha}, e_{\beta}] = \frac{2(\alpha, \beta)}{(\alpha, \alpha)} e_{\beta}, \quad [e_{\alpha}, e_{\beta}] = \begin{cases}
h_{\alpha} & \alpha + \beta = 0\\
n_{\alpha, \beta} e_{\alpha+\beta} & \alpha+\beta \in \Phi\\
0 & \text{otherwise}
\end{cases}$$

This construction implies that we have

$$\frac{2(\alpha, \beta)}{(\alpha, \alpha)} \in \mathbb{Z} \quad \forall \; \alpha, \beta \in \Phi$$

* This follows because: we have that $$\mathfrak{g}^{\alpha} := \text{span}_{\mathbb{C}}\{h^{\alpha}, e^{\alpha}, e^{-\alpha}\}$$ is such that $$\mathfrak{g}^{\alpha} \cong \mathfrak{su}(2)_{\mathbb{C}}$$. And $$\mathfrak{g}^{\alpha}$$ has CSA $$\mathfrak{h}^{\alpha} := \text{span}_{\mathbb{C}}\{h^{\alpha}\}$$. Since $$\mathfrak{g}^{\alpha} \cong \mathfrak{su}(2)_{\mathbb{C}}$$, then any irrep of $$\mathfrak{g}^{\alpha}$$ is isomorphic to some irrep of $$\mathfrak{su}(2)_{\mathbb{C}}$$ (?). And note that isomorphic reps share the same weight set, and any rep of $$\mathfrak{su}(2)_{\mathbb{C}}$$ has a weight set of the form $$\{-\Lambda, -\Lambda+2, \ldots, \Lambda\}$$.
* Now we will construct a valid irrep of $$\mathfrak{g}^{\alpha}$$ on some space $$V$$ and apply the above to obtain the desired result. The minimal invariant subspace $$V$$ of $$\text{ad}$$ will act as a particularly natural irrep. See that

    $$\text{ad}_{h^{\alpha}}(e^{\beta}) = \frac{2(\alpha, \beta)}{(\alpha, \alpha)} e^{\beta}, \quad \text{ad}_{e^{\alpha}}(e^{\beta}) = \begin{cases}
    n_{\alpha, \beta} e^{\alpha+\beta}, & \alpha+\beta \in \Phi\\
    0, & \text{otherwise}
    \end{cases}$$

    assuming that $$\beta \in \Phi$$ is such that $$\beta \neq -\alpha$$ (as if $$\beta = -\alpha$$, then $$h^{\alpha}$$ would have to be included in our vector space, but we are looking for the \textit{minimal} invariant subspace). As a result, we clearly have that

    $$V_{\alpha, \beta} := \text{span}_{\mathbb{C}}\{e^{\beta+\rho\alpha} \, | \, \rho \in \mathbb{Z}: \beta+\rho\alpha \in \Phi\}$$

    is the minimal invariant subspace. As a result, $$\text{ad}^{\alpha, \beta}: \mathfrak{g}^{\alpha} \to \mathfrak{gl}(V_{\alpha, \beta}), X \mapsto [X, \cdot]$$ is an irrep, and by isomorphism $$\mathfrak{g}^{\alpha} \cong \mathfrak{su}(2)_{\mathbb{C}}$$, we have that

    $$S_{\text{ad}^{\alpha, \beta}} = \{-\Lambda, -\Lambda+2, \ldots, \Lambda\} \quad \text{for some} \; \Lambda \in \mathbb{Z}_{+}$$

    and note that, by definition, this weight set can be written

    $$\begin{align*}
    S_{\text{ad}^{\alpha, \beta}} &= \{\lambda \in \mathbb{C} \, | \, \exists \, v \in V_{\alpha, \beta}: \text{ad}_{h^{\alpha}}^{\alpha, \beta}(v) = \lambda v\}\\
    &= \left\{\frac{2(\alpha, \beta)}{(\alpha, \alpha)} + 2\rho \, | \, \rho  \in \mathbb{Z}: \beta+\rho\alpha \in \Phi\right\}
    \end{align*}$$

    and so we arrive at

    $$\frac{2(\alpha, \beta)}{(\alpha, \alpha)}  \in \mathbb{Z} \quad \forall \; \alpha, \beta \in \Phi$$

* For any $$\alpha, \beta \in \Phi$$, these results also imply that there must be some maximum $$\rho = n_{+} \in \mathbb{Z}_{\geq 0}$$ for which $$\beta+\rho \alpha \in \Phi$$, as well as some minimum $$\rho = n_{-} \in \mathbb{Z}_{\leq 0}$$ for which $$\beta+\rho\alpha \in \Phi$$ holds. Since these describe the endpoints of the weight set, corresponding to $$\Lambda$$ and $$-\Lambda$$ respectively, then we must have

    $$\frac{2(\alpha, \beta)}{(\alpha, \alpha)} = -n_{+} - n_{-}$$

    For a pair of roots $$\alpha, \beta \in \Phi$$, we will use the notation $$n_{+} = n_{+}(\alpha, \beta)$$ and $$n_{-} = n_{-}(\alpha, \beta)$$ for these integers.
* We can actually generalize this result to arbitrary Lie algebra representations $$d$$ other than the adjoint representation (i.e. representations over arbitrary vector spaces $$V$$ other than over $$\mathfrak{g}$$). We will provide this proof later during the classification of representations.

In the above, we have shown that each root string of length $$\ell_{\alpha, \beta}$$ is in correspondence with a rep of $$\mathfrak{su}(2)_{\mathbb{C}}$$ of dimension $$\ell_{\alpha, \beta}$$. Particularly, the constructed rep $$\text{ad}^{\alpha, \beta}$$ acts on

$$V_{\alpha, \beta} = \text{span}_{\mathbb{C}}\{e_{\gamma}: \gamma \in S_{\alpha, \beta}\}$$

which has dimension $$\dim V_{\alpha, \beta} = |S_{\alpha, \beta}| \equiv \ell_{\alpha, \beta}$$. And since $$\ell_{\alpha, \beta} = n_{+} - n_{-} + 1$$, we have that for simple roots $$\alpha, \beta \in \Phi_S$$ (such that $$n_- = 0$$), we can write

$$\ell_{\alpha, \beta} = 1 - \frac{2(\alpha, \beta)}{(\alpha, \alpha)}$$

The \textit{Cartan matrix} $$A$$ is defined

$$A_{ij} := \frac{2(\alpha_{(i)}, \alpha_{(j)})}{(\alpha_{(j)}, \alpha_{(j)})}$$

Since $$(\alpha, \alpha) > 0$$, this tells us that we must have $$(\alpha, \beta) \leq 0$$ for all $$\alpha, \beta \in \Phi_S$$ (as otherwise, $$\ell_{\alpha, \beta} < 1$$ which is invalid). As we will see, this tells us that the non-diagonal elements of the Cartan matrix are non-positive: $$A_{ij} \leq 0$$ for $$i \neq j$$.

Using the notation $$\ell_{i, j} := \ell_{\alpha_{(i)}, \alpha_{(j)}}$$, we therefore have

$$\ell_{i, j} = 1-A_{ji}$$

which we use later to derive Serre's relation.

#### A.1.1.4. Simple roots and geometry

We have that $$\mathfrak{h}^{*} = \text{span}_{\mathbb{C}} \Phi$$.
* Proof: if it doesnt span, then there exists some $$X \in \mathfrak{h}$$ such that $$\alpha(X) \equiv \alpha_i X^i = 0$$ for all $$\alpha \in \Phi$$. From here, we can use the existence of $$X$$ to construct a non-trivial ideal subalgebra $$\mathfrak{k}$$ (i.e. $$[\mathfrak{g}, \mathfrak{k}] \subseteq \mathfrak{k}$$) that is abelian, contradicting semi-simplicity of $$\mathfrak{g}$$. This is trivial to do: $$X \in \mathfrak{h}$$ and so clearly the span of $$X$$ will be an abelian subalgebra (since CSAs are abelian), and so now we must show that its ideal. See that

    $$[X, E_{\alpha}] = X^i [H_i, E_{\alpha}] = \underbrace{X^i \alpha_i}_{=\, 0} E^{\alpha} = 0$$

    (using commutation relations) and hence $$\text{span}_{\mathbb{C}} \{X\}$$ is an ideal abelian subalgebra of $$\mathfrak{g}$$, contradicting semi-simplicity.



Since $$\mid\Phi\mid \geq \dim\mathfrak{h}^{*}$$, the set of roots will generally be an overcomplete basis for $$\mathfrak{h}^{*}$$. This motivates constructing a minimal set of exactly $$r$$ roots that acts as a basis for $$\mathfrak{h}^{*}$$.

To do so, we perform the following reduction to obtain the \textit{simple roots}.

\textit{Constructing the simple roots.} As we have used above in writing $$E_{-\alpha}$$ as a generator, we have that $$\alpha \in \Phi \iff -\alpha \in \Phi$$.
* Proof: To show this, we must make use of two results:

    1. $$\kappa(H, E_{\alpha}) = 0$$ for all $$H \in \mathfrak{h}$$ and all $$\alpha \in \Phi$$.
        * Proof: Since $$\alpha \neq 0$$, there exists $$H' \in \mathfrak{h}$$ such that $$\alpha(H') \neq 0$$, and so

            $$\alpha(H') \kappa(H, E_{\alpha}) = \kappa(H, \alpha(H') E_{\alpha}) = \kappa(H, [H', E_{\alpha}]) = \kappa(E_{\alpha}, [H, H']) = 0$$

    2. For any $$\alpha, \beta \in \Phi$$ such that $$\beta \neq -\alpha$$, we have that $$\kappa(E_{\alpha}, E_{\beta}) = 0$$.
        * Proof: Consider $$(\alpha(H) + \beta(H)) \kappa(E_{\alpha}, E_{\beta})$$ and use linearity + Jacobi property of $$\kappa$$.

    These results tell us that, for any $$\alpha \in \Phi$$, $$\kappa(E_{\alpha}, H) = \kappa(E_{\alpha}, E_{\beta}) = 0$$ for any $$H \in \mathfrak{h}$$ and any $$\beta \in \Phi$$ such that $$\beta \neq -\alpha$$. But since $$\kappa$$ is non-degenerate by semi-simplicity, $$\kappa(E_{\alpha}, \cdot)$$ cannot map everything to zero. The only option is for $$-\alpha \in \Phi$$ with $$\kappa(E_{\alpha}, E_{-\alpha}) \neq 0$$.

And since $$0 \notin \Phi$$, we have that $$\mid\Phi\mid$$ is even. Let's separate $$\mathfrak{h}^{*} \cong \mathbb{C}^{r}$$ into two halves via a $$r-1$$ $$\mathbb{C}$$-dimensional hyperplane (that intersects with the origin). This hyperplane will split $$\Phi$$ into two equally sized sets of roots:

$$\Phi := \Phi_{+} \cup \Phi_{-}$$

Namely, if $$\alpha \in \Phi_{+}$$, then we will have $$-\alpha \in \Phi_{-}$$. Further, if $$\alpha, \beta \in \Phi_{+}$$, then $$\alpha + \beta \in \Phi_{+}$$ (and similarly for $$\Phi_{-}$$).

Now that we have halved the size of $$\Phi$$ to $$\Phi_{+}$$, there is one further reduction we perform. We say that a root $$\alpha \in \Phi$$ is simple iff it is a positive root ($$\alpha \in \Phi_{+}$$) and cannot be written as the sum of two positive roots. We denote the set of simple roots by

$$\Phi_S = \{\alpha_{(i)} : i = 1, \ldots, |\Phi_S|\}$$

We will now show that $$\Phi_S$$ is a basis for $$\mathfrak{h}^{*}$$ with $$\mid\Phi_S\mid = r$$.

Firstly, we have that

$$\Phi_{+} \subseteq \text{span}_{\mathbb{Z}_{\geq 0}} \Phi_S, \quad \Phi_{-} \subseteq \text{span}_{\mathbb{Z}_{\leq 0}} \Phi_S$$

telling us that any positive root can be written as a linear combination of simple roots with positive coefficients, and similarly for negative roots.
* Proof: We want to show that $$\alpha \in \Phi_{+} \implies \alpha \in \text{span}_{\mathbb{Z}_{\geq 0}} \Phi_S$$. For $$\alpha  \in \Phi_{+}$$, if $$\alpha \in \Phi_S$$ then we are done. Otherwise, since $$\alpha$$ is non-simple there must exist some $$\alpha_1, \alpha_2 \in \Phi_{+}$$ such that $$\alpha = \alpha_1 + \alpha_2$$. If $$\alpha_1$$ and $$\alpha_2$$ are both simple, then we are done. Otherwise, repeat the initial procedure for non-simple $$\alpha_1$$ or $$\alpha_2$$ (or both). Eventually this process must terminate since there are a finite number of roots. As a result, we must have $$\alpha \in \text{span}_{\mathbb{Z}_{\geq 0}} \Phi_S$$.
* This result extends analogously to $$\Phi_{-}$$ since $$\alpha \in \Phi_{+} \iff -\alpha \in \Phi_{-}$$.

This result implies that $$\Phi \subseteq \text{span}_{\mathbb{Z}} \Phi_S$$ and, since $$\Phi$$ $$\mathbb{C}$$-spans $$\mathfrak{h}^{*}$$, we therefore have that

$$\text{span}_{\mathbb{C}} \Phi_S = \mathfrak{h}^{*}$$

also.

Finally, we have that the simple roots are linearly independent, meaning that $$|\Phi_S| = r$$ and hence $$\Phi_S$$ is a basis for $$\mathfrak{h}^{*}$$.
* Proof: Note that we can write any $$\lambda \in \mathfrak{h}^{*}$$ as

    $$\lambda = \sum_i c_i \alpha_{(i)}, \qquad c_i \in \mathbb{C}$$

    To show linear independence of $$\Phi_S$$ it is sufficient to show that $$\lambda = 0 \implies c_i = 0 \; \forall \; i$$. See that if $$\lambda = 0$$, we can apply $$(\cdot, \alpha_{(j)})$$ to the above:

    $$0 = \sum_i c_i (\alpha_{(i)}, \alpha_{(j)}) \quad \forall \; \; j$$

    This condition can be written as a matrix equation:

    $$Ac = 0$$

    for $$c = (c_1, \ldots, c_s)^T \in \mathbb{C}^s$$ and the symmetric matrix $$A$$ defined by $$A_{ij} := (\alpha_{(i)}, \alpha_{(j)})$$. We have that $$A$$ is non-singular as it is positive definite:

    $$x^T A x = x^i x^j (\alpha_{(i)}, \alpha_{(j)}) = (\chi, \chi) > 0$$

    for any non-zero $$x \in \mathbb{C}^{s}$$, defining $$\chi := x^i \alpha_{(i)} \in \mathfrak{h}^{*}$$. Hence $$A$$ is non-singular, and so $$Ac = 0$$ has the unique solution $$c=0$$, giving us linear independence.

One useful result is that the difference of two simple roots is not a root.
* Proof: For any two simple roots $$\alpha_{(i)}, \alpha_{(j)} \in \Phi_S$$, define

    $$\alpha := \alpha_{(i)} - \alpha_{(j)}$$

    Now assume that $$\alpha \in \Phi$$, then WLOG we can assume $$\alpha \in \Phi_{+}$$ (as otherwise we can switch sign $$\alpha \mapsto -\alpha$$ in the definition of $$\alpha$$). Then $$\alpha + \alpha_{(j)} = \alpha_{(i)}$$ is the sum of two positive roots that equals a simple root, which contradicts the definition of a simple root. Therefore, we must have $$\alpha_{(i)} - \alpha_{(j)} \notin \Phi$$.



#### A.1.1.5. Classification

The simple roots

$$\Phi_S = \{\alpha_{(i)}: i = 1, \ldots, r\}$$

provide a canonical $$\mathbb{C}$$-basis for $$\mathfrak{h}^{*}$$. Writing $$h_i := h_{\alpha_{(i)}}$$ and $$e_{i} := e_{\alpha_{(i)}}$$ (and $$h_{-i} := h_{-\alpha_{(i)}}$$, $$e_{-i} := e_{-\alpha_{(i)}}$$), the triple $$(h_i, e_i, e_{-i})$$ generate a subalgebra corresponding to $$\mathfrak{su}(2)_{\mathbb{C}}$$, globally acting as

$$[h_i, h_j] = 0, \quad [h_i, e_{\pm j}] = \pm \underbrace{\frac{2(\alpha_{(i)}, \alpha_{(j)})}{(\alpha_{(i)}, \alpha_{(i)})}}_{=: A_{ji}} e_{\pm j}, \quad [e_i, e_{-j}] = \delta_{ij} h_i,$$

defining the Cartan matrix

$$A_{ij} := \frac{2(\alpha_{(i)}, \alpha_{(j)})}{(\alpha_{(j)}, \alpha_{(j)})}$$

Similarly to before, we can define the subalgebra $$\mathfrak{g}_i := \text{span}_{\mathbb{C}}\{h_i, e_i, e_{-i}\}$$, which has $$\mathfrak{g}_{i} \cong \mathfrak{su}(2)_{\mathbb{C}}$$ and associated CSA $$\mathfrak{h}_{i} := \text{span}_{\mathbb{C}}\{h_i\}$$. The generators $$\{h_i, e_i, e_{-i}\}_{i}$$ are called the Chevalley basis of $$\mathfrak{g}$$.

In addition to the above relations, we have

$$\text{ad}_{e_i}(e_j) \equiv [e_i, e_j] \propto \begin{cases}
e_{\alpha_{(i)}+\alpha_{(j)}}, & \alpha_{(i)} + \alpha_{(j)} \in \Phi\\
0, & \text{otherwise}
\end{cases}$$

meaning that

$$\text{ad}_{e_i}^{\rho}(e_j) \propto \begin{cases}
e_{\alpha_{(j)} + \rho\alpha_{(i)}}, & \rho \in \{n_{-}^{(i, j)}, \ldots, n_{+}^{(i, j)}\}\\
0, & \text{otherwise}
\end{cases}$$

defining $$n_{+}^{(i, j)} := n_{+}(\alpha_{(i)}, \alpha_{(j)})$$ and $$n_{-}^{(i, j)} := n_{-}(\alpha_{(i)}, \alpha_{(j)})$$, where $$n_{+}, n_{-}$$ are as defined in A.1.1.3.

Furthermore, as proven in the previous section, the difference between two simple roots is not a root (i.e. $$\alpha_{(i)} - \alpha_{(j)} \notin \Phi$$), which tells us that $$n_{-}^{(i, j)} = 0$$ for all $$i, j$$. And recall (in A.1.1.3) that we found

$$n_{+}^{(i, j)} = -\frac{2(\alpha_{(i)}, \alpha_{(j)})}{(\alpha_{(i)}, \alpha_{(i)})} \equiv -A_{ji}$$

As a result, we have

$$\text{ad}_{e_i}^{\rho}(e_j) \propto \begin{cases}
e_{\alpha_{(j)} + \rho\alpha_{(i)}}, & \rho \in \{0, 1, \ldots, -A_{ji}\}\\
0, & \text{otherwise}
\end{cases}$$

providing the \textit{Serre relation} for $$i\neq j$$:

$$\text{ad}_{e_i}^{-A_{ji}}(e_j) \neq 0, \qquad \text{ad}_{e_i}^{-A_{ji}+1}(e_j) = 0$$

* $$A_{ji} \in \mathbb{Z}_{\leq 0}$$ for $$i\neq j$$ since $$(\alpha_{(i)}, \alpha_{(j)}) \leq 0$$ for $$i \neq j$$. In contrast, $$A_{ii} = 2$$ (no sum).
* The Serre relation tells us that $$\text{ad}_{e_i}^{-A_{ji}}(e_j)$$ is the highest weight vector of the adjoint rep $$\text{ad}$$. (check?)



\textbf{Classifying algebras via Cartan matrices.} We saw that the entries of the Cartan matrix $$A \in \mathbb{Z}^{r \times r}$$ completely determined the relations between elements of the Chevalley basis $$\{h_i, e_i, e_{-i}\}_{i=1}^{r}$$ of $$\mathfrak{g}$$. Indeed, the Cartan matrix uniquely determines a semi-simple complex Lie algebra.

As a result, the problem of classifying all complex semi-simple Lie algebras reduces to classifying all possible Cartan matrices. In the following, we will derive some necessary conditions on $$A$$ to aid in this classification.

Todo: relevant proofs

As a result, we can classify all complex semi-simple finite-dim Lie algebras by determining the valid Cartan matrices $$A$$. The definition of $$A$$ gives some constraints:
1. $$A_{ii} = 2$$ for all $$i = 1, \ldots, r$$.
2. $$A_{ij} = 0 \iff A_{ji} = 0$$.
3. $$A_{ij} \in \mathbb{Z}_{\leq 0}$$ for all $$i \neq j$$.
4. $$\det A > 0$$.
5. $$A_{ij} A_{ji} \in \{0, 1, 2, 3\}$$ for all $$i \neq j$$.
6. $$A$$ is irreducible, i.e. $$P A P^{-1}$$ is not block upper triangular for any permutation $$P \in S_r$$.

This is sufficient to classify all complex semi-simple finite-dim Lie algebras, amounting to the infinite families $$A_r, B_r, C_r, D_r$$ as well as exceptional algebras $$E_6, E_7, E_8, F_4, G_2$$. See [here](https://en.wikipedia.org/wiki/Simple_Lie_algebra#:~:text=Lie%20algebra%20is-,isomorphic,-to%20either%20of) for more details.

### Appendix A.1.2. Classifying representations

We now wish to classify all available representations $$D: G \to \text{GL}(V)$$ of a given Lie group $$G$$. We can do the following: (i) classify all Lie algebra reps (ii) map to Lie group reps via the exponential map.

#### Definitions

A Lie algebra representation $$d: \mathfrak{g} \to \mathfrak{gl}(V)$$ is a (Lie algebra) homomorphism between $$\mathfrak{g}$$ and $$\mathfrak{gl}(V)$$ for some vector space $$V$$. One often calls $$V$$ the representation, or the representation space. 

For a representation $$d$$ of $$\mathfrak{g}$$ over $$V$$, we say that $$W \leq V$$ is an invariant subspace of $$d$$ iff

$$d_X(W) \subseteq W \quad \forall \; \; X \in \mathfrak{g}$$

Any representation $$d$$ always has the trivial invariant subspaces $$W = V$$ and $$W = \{0\}$$. If $$d$$ has no non-trivial invariant subspaces, then we say that $$d$$ is irreducible. Otherwise, we say that $$d$$ is reducible.

We can generalize the concept of irreducibility: if we can decompose

$$V = W_1 \oplus \cdots \oplus W_K$$

for a collection of invariant subspaces $$\{W_i\}_{i=1}^{K}$$ of $$d$$, then we say that $$d$$ is totally reducible. The case of $$K=1$$ corresponds to irreducibility.

In particular, if $$d$$ is totally reducible, then we may write

$$d = d^{(1)} \oplus \cdots \oplus d^{(K)}$$

for a collection of irreducible representations $$\{d^{(i)}: \mathfrak{g} \to \mathfrak{gl}(W_i)\}_{i=1}^{K}$$.

Maschke's theorem tells us that any finite-dimensional representation of a semi-simple Lie algebra $$\mathfrak{g}$$ is totally reducible (assuming a characteristic zero field $$\mathbb{F}$$).
* This can be seen as a justification for studying semi-simple algebras: it means we only need to classify the irreps.

#### Classification

Consider a rep $$d$$ of $$\mathfrak{g}$$ on $$V$$. We want to better understand the vectors spaces $$V$$ on which $$\mathfrak{g}$$ can act through reps $$d$$. First we will work with the initial Cartan-Weyl basis $$\{H_i, E_{\alpha}\}_{i, \alpha}$$ of $$\mathfrak{g}$$ paired with CSA $$\mathfrak{h}$$. Note that

$$[d_{H_i}, d_{H_j}] = d_{[H_i, H_j]} = 0$$

since $$[H_i, H_j] = 0$$. This means that the operators $$\{d_{H_i}\}_{i=1}^{r}$$ on $$V$$ are simultaneously diagonalizable. Then define the eigenspace of simultaneous eigenvectors associated with weight $$\lambda \in \mathfrak{h}^{*}$$ by

$$V_d^{\lambda} := \{v \in V: d_H(v) = \lambda(H)v \;\; \forall \; H \in \mathfrak{h}\}$$

Denote the total set of weights of rep $$d$$ by $$S_d \subset \mathfrak{h}^{*}$$, i.e.

$$S_d := \{\lambda \in \mathfrak{h}^{*}: V_{d}^{\lambda} \neq \{0\}\}$$

It is easy to show that

$$d_{E_{\alpha}}(V_d^{\lambda}) \subseteq \begin{cases}
V_d^{\lambda+\alpha}, & \lambda+\alpha \in S_d\\
\{0\}, & \text{otherwise}
\end{cases}$$

telling us that $$d_{E_{\alpha}}$$ raises weights $$\lambda \to \lambda+\alpha$$.

Every finite-dim rep $$d$$ has at least one \textit{highest weight} $$\Lambda \in S_d$$ defined by

$$d_{E_{\alpha}}(V_{d}^{\Lambda}) = \{0\} \;\; \forall \; \alpha \in \Phi_{+}$$

i.e. the highest weight $$\Lambda \in S_d$$ is such that, for any positive root $$\alpha \in \Phi_{+}$$, $$\Lambda + \alpha \notin S_d$$. 





Note that here we are working with the subalgebra $$\mathfrak{g}^{\alpha}$$, where $$\mathfrak{h}^{\alpha}$$ is 1-dimensional and hence can just treat $$\lambda \in \mathbb{C}$$. In this case, the relevant CSA from which weights are defined is $$\mathfrak{h}^{\alpha} = \text{span}_{\mathbb{C}}\{h^{\alpha}\}$$, and for a rep $$d$$ of $$\mathfrak{g}^{\alpha} \cong \mathfrak{su}(2)_{\mathbb{C}}$$ on $$V$$, the weights of $$d$$ corresponds to eigenvalues of $$d_{h^{\alpha}}: V \to V$$. But by isomorphism, the weight set of $$d$$ must match $$\{-\Lambda, \ldots, \Lambda\}$$, meaning that

$$\lambda \in S_d \implies d_{h_{\alpha}}(v) = \lambda(h_{\alpha}) v \; \; \text{for some} \; \; v \in V\backslash\{0\}$$

means that $$\lambda(h_{\alpha}) \in \mathbb{Z}$$. But we can equivalently write

$$\lambda(h_{\alpha}) = \frac{2}{(\alpha, \alpha)} \lambda(H_{\alpha}) = \frac{2(\lambda, \alpha)}{(\alpha, \alpha)}$$

meaning that

$$\frac{2(\lambda, \alpha)}{(\alpha, \alpha)} \in \mathbb{Z} \quad \forall \; \lambda \in S_d, \alpha \in \Phi$$

In the second equality we used the Reisz representation theorem-like definition of $$H_{\alpha}$$, which means

$$\lambda(H_{\alpha}) = \kappa^{ij} \alpha_j \lambda(H_i) = \kappa^{ij} \alpha_j \lambda_i \equiv (\lambda, \alpha)$$

The above generalizes the previously used result; when $$d = \text{ad}^{\alpha, \beta}$$, we get the root-specific condition $$2(\alpha, \beta)/(\alpha, \alpha)$$.

\textit{Dominant weights.} For the following, we will define the co-roots

$$\hat{\alpha}_{(i)} := \frac{2}{(\alpha_{(i)}, \alpha_{(i)})} \alpha_{(i)}$$

For $$\lambda \in \mathfrak{h}^{*}$$, we define its components $$\lambda^i := (\lambda, \hat{\alpha}_{(i)})$$. In particular, they are the components of $$\lambda$$ under the dual basis $$\{\omega_{(i)}\}_i$$ of $$\{\hat{\alpha}_{(i)}\}_i$$:

$$\lambda = \lambda^i \omega_{(i)}$$

with $$(\omega_{(i)}, \hat{\alpha}_{(j)}) = \delta_{ij}$$. Then we define the dominant weights as

$$\mathcal{D}_W := \{\lambda \in \mathfrak{h}: \lambda^i \in \mathbb{Z}_{\geq 0} \; \forall \; i\}$$

The main result: for every dominant weight $$\lambda \in \mathcal{D}_W$$, there exists a unique finite-dimensional irrep $$d^{(\lambda)}$$ of $$\mathfrak{g}$$ for which $$\lambda$$ is its highest weight. And further, this exhausts all finite-dim irreps. As a result, we can determine finite-dim irreps by iterating over all dominant weights.

Todo: relevant proofs

### Appendix A.1.3. Classifying the representations of the unitary group

Todo

## Appendix A.2: The LSZ formula

In the canonical quantization approach to QFT -- an alternative to the path integral approach outlined in the rest of this post -- it is particularly convenient to derive the scattering/decay probabilities associated with scattering/decay processes (e.g. a meson decaying into a nucleon and anti-nucleon). For example, for a free scalar QFT with field $$\phi$$, the scattering process $$\phi\phi \to \phi\phi$$ has scattering amplitude

$$\begin{align*}
\braket{f|S|i} &= \int \left[\prod_{i=1}^{4} d^4 x_i\right] \, e^{-ip_1\cdot x_1} (\square_1 + m^2) e^{-ip_2\cdot x_2} (\square_2 + m^2) e^{ip_3\cdot x_3} (\square_3+m^2) e^{ip_4\cdot x_4} (\square_4 + m^2)\\
&\qquad \qquad \qquad \qquad \braket{\phi(x_1) \phi(x_2) \phi(x_3) \phi(x_4)}
\end{align*}$$

with $$\square_i$$ the Laplace operator associated with $$x_i$$.

It is a general property of scattering amplitudes that they can be expressed as an integral over correlators. This tells us that correlators have a direct physical relevance, and hence it is natural to enforce that such correlators -- and expectances more generally (as given in Introduction) -- are invariant to the symmetry group of the theory.


## Appendix A.3: Coupling to gravity

Non-dynamical gravity involves modifying the action

$$\int d^n x \, \mathcal{L}(\Phi; x) \to \int d^n x \, \sqrt{-g} \mathcal{L}(\Phi; x)$$

To make the metric $$g$$ dynamical, we cam add a Ricci scalar term:

$$\int d^n x \, \mathcal{L}(\Phi; x) \to \int d^n x \, \sqrt{-g} \left(\frac{1}{16\pi G}R + \mathcal{L}(\Phi; x)\right)$$

In the context of gravity, the concept of diffeomorphism invariance of our theory becomes relevant. The $$\sqrt{-g}$$ factor as well as the Ricci scalar $$R$$ are manifestly diffeomorphism invariant.

The main problem why gravity is problematic is that the resulting Feynman diagrams/graviton loop corrections are non-renormalizable: one requires an infinite number of counter-terms to remedy divergences at all orders. As a result, after coupling to gravity, you are restricted to only considering tree level contributions.

Todo: demonstrate divergences under dynamical gravity.

## Appendix A.3: Justifying gauge invariance as a principle

In biology there is a redundancy analogous to gauge invariance: multiple genotypes map to 
the same phenotype. In particular, field configurations $$\Psi \in \mathcal{C}$$ are analogous to genotypes, and physical configurations $$\Phi \in \mathcal{P}$$ are analogous to phenotypes. The equivalence class $$[\Psi]$$ is analogous to a \textit{neutral network}. Note however that the discreteness of genotypes compared to the continuity of the configuration space $$\mathcal{C}$$ weaken this analogy.

In biology, the existence of neutral networks aid search by allowing for a more diverse search over genotypes and hence phenotypes. How can we interpret such benefits in the context of gauge invariance? Can we better understand why our theories must exhibit properties like gauge invariance?

Todo


## Appendix A.4: Effects of integrating over redundant configurations

What are the effects of integrating over physically equivalent configurations in $$\mathcal{C}$$? We can expect some form of overcounting. Namely, see that

$$\begin{align*}
\mathbb{E}_{\Psi \sim S}^{\mathcal{C}}[f(\Psi)] = \frac{1}{Z_{\mathcal{C}}} \int_{\mathcal{C}} \text{D}\Psi \, f(\Psi) e^{-S[\Psi]} &= \frac{1}{Z_{\mathcal{C}}} \int_{\mathcal{P}} \text{D}\Phi \, \int_{[\Phi]} \text{D}\Psi \, f(\Psi) e^{-S[\Psi]}\\
&= \frac{1}{Z_{\mathcal{C}}} \int_{\mathcal{P}} \text{D}\Phi \,  e^{-S[\Phi]} \left[\int_{[\Phi]} \text{D}\Psi \, f(\Psi)\right]\\
&= \frac{1}{Z_{\mathcal{P}}} \int_{\mathcal{P}} \text{D}\Phi \,  F(\Phi) e^{-S[\Phi]}\\
&= \mathbb{E}_{\Phi\sim S}^{\mathcal{P}}[F(\Phi)]
\end{align*}$$

defining

$$F(\Phi) := \frac{Z_{\mathcal{P}}}{Z_{\mathcal{C}}} \int_{[\Phi]} \text{D}\Psi \, f(\Psi)$$

We view integrating over $$\mathcal{P}$$ as integrating over representative states of each distinct equivalence class, only integrating over non-equivalent configurations.

Similarly, one can show that

$$Z_{\mathcal{C}} = Z_{\mathcal{P}} \mathbb{E}_{\Phi \sim S}^{\mathcal{P}}[\text{Vol}([\Phi])]$$

which lets us write

$$F(\Phi) = \frac{1}{\mathbb{E}_{\Phi' \sim S}^{\mathcal{P}}[\text{Vol}([\Phi'])]} \int_{[\Phi]} \text{D}\Psi \, f(\Psi)$$

Now say that each equivalence class has the same volume, i.e. $$\text{Vol}([\Phi]) = \text{Vol}([\Phi'])$$ for any $$\Phi, \Phi' \in \mathcal{P}$$. Further, say $$f$$ is constant over $$[\Phi]$$, which corresponds to invariance $$f \circ \rho_g = f$$. In this case, we have that $$F(\Phi) = f(\Phi)$$, i.e. taking expectances of $$f$$ over $$\mathcal{C}$$ is equivalent to taking expectances over $$\mathcal{P}$$. But generally, $$f$$ will not be constant over $$[\Phi]$$ (as is the case for correlators), and so generally these are distinct operations.

The overcounting associated with gauge fields can be detrimental to the validity of the theory due to divergences, which we explore in Section 7.
