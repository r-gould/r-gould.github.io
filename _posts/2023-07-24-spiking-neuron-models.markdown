---
layout: post
title:  "Brief notes on spiking neuron models"
date:   2023-07-24 09:00:00
mathjax: true
---

### Spiking Neuron Model

A primary asset of machine learning is the artificial neuron; a very idealized, non-spiking model of a neuron in the brain. What does a more realistic model of 
neurons and their spiking behaviour look like?

The spiking network is modelled as a directed graph $$G = (V, E)$$, where nodes $$V$$ represent **neurons** and edges $$E$$ represent **synapses** between neurons. 
For $$n$$ neurons, $$V := \{1, \ldots, n\}$$.
* Define the input function $$\mathcal{I}: V \to \mathcal{P}(V)$$ by $$\mathcal{I}(i) := \{j : (j, i) \in E\}$$, mapping a neuron to its set of incoming neurons, and analogously the output function $$\mathcal{O}: V \to \mathcal{P}(V)$$ mapping a neuron to its set of outgoing neurons.

The most common spiking neural models are **Integrate-and-Fire** (IF) and **Leaky-Integrate-and-Fire** (LIF).

For a neuron $$i \in V$$, let $$u_i = u_i(t)$$ model the state variable, which represents the **neural membrane potential** of neuron $$i$$. Then under the LIF model it evolves as

$$C \frac{du_i}{dt}(t) = -\frac{1}{R}(u_i(t) - u_{\text{rest}}) + (I_0(t) + \sum_{j \in \mathcal{I}(i)} w_{ji} I_j(t))$$

where $$\{w_{ji}\}_{j \in \mathcal{I}(i)}$$ are **learnable** synaptic weights, $$I_0 = I_0(t)$$ is the external current driving the neural state, 
$$I_j = I_j(t)$$ is the input current from neuron $$j \in \mathcal{I}(i)$$, $$u_{\text{rest}}$$ is the rest potential, $$C$$ is the membrane capacitance, and $$R$$ is the input resistance.

A brief derivation of this model from electromagnetic principles can be found in the appendix.
* The IF model can be obtained by letting $$R \to \infty$$.

Whenever the membrane potential $$u_i$$ for neuron $$i \in V$$ reaches the **firing threshold** $$\upsilon$$, which occurs at say times $$T_i \subset \mathbb{R}$$, the neuron spikes, 
which is modelled by its corresponding **spike train** $$S_i(t) := \sum_{t' \in T_i} \delta(t - t')$$. Immediately after the spike, the neural state is reset to the **rest potential** 
$$u_{\text{rest}} < \upsilon$$ and held at that level for the time interval representing the neural **absolute refractory period**.

How are the incoming currents $$\{I_j\}_{j \in \mathcal{I}(i)}$$ modelled? Each incoming neuron has their own spike trains $$\{S_j\}_{j \in \mathcal{I}(i)}$$ with corresponding spike times $$\{T_j \subset \mathbb{R}\}_{j \in \mathcal{I}(i)}$$. Then for each $$j \in \mathcal{I}(i)$$, 

$$I_j(t) := \int_{0}^{\infty} S_j(t-s) \exp(-s/\tau) ds = \sum_{t' \in T_j, t' \leq t} \exp(-(t-t')/\tau)$$

where $$\tau$$ is the **synaptic time constant**. This can be thought of as accumulating all spikes before the time $$t$$, weighing more recent spikes strongly compared to older spikes.

Importantly, the received current $$I_j$$ **only depends on the spikes** of this $$j$$th presynaptic neuron, and does not depend on other properties of the potential. The postsynaptic neuron only knows about spikes of 
its input neurons, and not anything more specific about their potential changes. **Incoming potentials below the firing threshold are ignored**.

We could update discretely using $$u_i(t + \Delta t) \approx u_i(t) + \Delta t \frac{du_i}{dt}(t)$$, and after updating in this way we would check for neurons that have increased over the threshold, set them to the rest potential and put them in refractory mode until the refractory period 
is over. 

### Encoding data

Say we have data $$x \in \mathbb{R}^n$$ with $$x_i \in [0, 1]$$. How do we convert this information into binary spikes across time analogously to how our sensory receptors do?

One method is rate encoding, where we encode each $$x_i$$ as a sequence of spikes at some frequency, where this frequency is a function of the 
intensity $$x_i$$, such that this frequency encodes the data.

A specific example of rate encoding is Poisson rate encoding:
* Let $$\lambda_i \propto \frac{1}{x_i + \epsilon}$$ for some appropriate proportionality constant (which will define the range of spike frequencies).
* Sample $$\{\delta_{ij} \sim \text{Po}(\lambda_i)\}_j$$, representing time intervals between spikes.
* Then spike times for $$x_i$$ are $$T_i = \{\sum_{j=1}^k \delta_{ij} : k = 1, 2, \ldots\}$$.

There are other methods of encoding, such as temporal coding schemes that encode information in precise timing of spikes as opposed to frequencies.

Within the network there would be a set of neurons that have no incoming synapses, and these would act as input neurons, where we would provide these neurons directly with the raw data encoded as spikes. Then, other neurons 
would receive the spike information of this raw data via incoming currents.

### Learning by STDP

STDP is based on the observed phenomena of synapses strengthening if the pre-synaptic neuron spikes before the post-synaptic does too, and the synapse weakening if the post-synaptic neuron fires before the pre-synaptic neuron. The smaller the time difference between the two spikes, 
the stronger the synaptic update.

A common STDP rule says that when a neuron $$i$$ spikes at say time $$t$$, we should:
* For each $$j \in \mathcal{I}(i)$$, let $$t_{post} = t$$ and $$t_{pre} = T_j[-1]$$, i.e. the latest spiking time of $$j$$ (so $$i$$ is the post-synaptic neuron, $$j$$ is the pre). Then, 
the change in synaptic weight $$w_{ji}$$ is $$\delta w = A_{+} \exp(-(t_{post} - t_{pre})/\tau_+)$$. This is an example of the strengthening of synapses.
* For each $$j \in \mathcal{O}(i)$$, let $$t_{pre} = t$$ and $$t_{post} = T_j[-1]$$ (so $$i$$ is pre, $$j$$ is post), and $$w_{ij}$$ changes by 
$$\delta w = -A_{-} \exp(-(t_{pre} - t_{post})/\tau_{-})$$. This is an example of the weakening of synapses.

For better stability and noise reduction, the updates can be stored until, say, $$w_{ij}$$ has 5 stored updates, where the updates are summed and 
then updated. It can also be done in a fixed time window, such as updating every 5ms, as opposed to performing the updates immediately.

### Appendix

#### Derivation of LIF model

The key idea is to model the intracellular and extracellular regions of the neuron as **conductors**, with the neuron membrane acting as the space between them. 
* A conductor is an object in which charges can move freely, which implies that the electric field $$\mathbf{E} = \mathbf{0}$$ (as if this is not the case, the free charges would move in response to the electric force until the force was zero).
* Hence the electrostatic potentials $$\Phi$$ are constant in each region, since $$\mathbf{E} =: - \mathbf{\nabla} \Phi$$. 
* So the potential difference $$V$$ between the two regions is well-defined, and matches the conventional definition of the potential of a neuron.

In addition, the charges of the regions are opposite in sign, and approximately equal in charge, say $$\pm Q$$. 
Hence together they form a **capacitor**.

The capacitance of two conductors carrying charges $$\pm Q$$ with a voltage $$V$$ between them is $$C := \frac{Q}{V}$$. Differentiating gives

$$C \frac{dV}{dt} = \frac{dQ}{dt}$$

For a volume $$\mathcal{V}$$, the charge is defined as

$$Q := \int_{\mathcal{V}} \rho \; d\mathcal{V}$$

where $$\rho = \rho(\mathbf{x}, t)$$ is the electric charge density.

The current describes the flow of charge. Specifically, the total current flowing into the volume is defined by

$$I := -\int_{\partial \mathcal{V}} \mathbf{J} . d\mathbf{S}$$

where $$\mathbf{J} = \mathbf{J}(\mathbf{x}, t)$$ is the flux of electric charge per unit area.
* $$\rho$$ and $$\mathbf{J}$$ are **sources**, and are irreducible. The sources define a problem in electromagnetism. They can be thought of as hyperparameters of the system that we must provide.

	The Maxwell equations then describe how the fields $\mathbf{E}$ and $\mathbf{B}$ evolve due to these sources. The Lorentz force law describes how the charges, described by the sources, move due to the fields.

Charge conservation means that the charge in a volume can only change due to charge flowing into/out of the volume. This means that

$$\frac{dQ}{dt} = I$$

hence the capacitance equation becomes

$$C \frac{dV}{dt} = I$$

where $$I$$ is the current flowing into the neuron.

The above represents the IF model, but in reality, there is a **leak** of ions through the neuron membrane, causing a current to flow out of the neuron. Adding in the term corresponding to this 
effect leads to the LIF model:

$$C \frac{dV}{dt}(t) = -\frac{1}{R}(V(t) - V_{\text{rest}}) + I(t)$$

and we can further expand $$I(t)$$ into the currents incoming via synapses to get the desired expression.