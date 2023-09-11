---
layout: post
title:  "Understanding HTM"
date:   2023-08-03 09:00:00
mathjax: true
---

### Introduction

(repo https://github.com/r-gould/htm)

A description and implementation of the Hierarchical Temporal Memory (HTM) system, a self-supervised, non-probabilistic, non-gradient-based prediction 
algorithm inspired by the neocortex. The implementation is focused on next token prediction.
* The [BaMI document](https://www.numenta.com/assets/pdf/biological-and-machine-intelligence/BaMI-Complete.pdf) describes the HTM system in high-detail. This post aims to condense the key ideas and algorithms.

The approach rests on some observed properties of the neocortex:
* Uniformity: across different points in the neocortex, the structure and connections are highly similar.
* Adaptability: different regions of the neocortex can substitute other regions
	
	(Feeding visual input to the auditory cortex in a ferret, the auditory cortex learns to 'see', see [here](https://www.nytimes.com/2000/04/25/science/rewired-ferrets-overturn-theories-of-brain-growth.html).
	Also, cases of people missing large parts of their brain, but still functioning, as if the remaining parts of the brain are able to rewire and compensate.)

An extension of these ideas is that the neocortex is built out of functionally identical [cortical columns](https://en.wikipedia.org/wiki/Cortical_column), with, for example, cortical columns in the visual cortex 
performing the same **general algorithm** as those in the auditory cortex. The only reason that the cortical columns in these regions perform different roles is because they receive 
different data, but ultimately, the same general algorithm is being applied.
* See [here](https://journals.physiology.org/doi/abs/10.1152/jn.1957.20.4.408) for the first discovery of this columnar structure.
* This uniformity is seen to work well in machine learning; a single component, such as an attention head or convolutional layer, and composing many (functional) copies of such components together.

Cortical columns consist of 6 layers, and the HTM system is a model of one of these (input) layers.

### Encoders

#### Overview

Components in the brain communicate via. **sparse distributed representations** (SDRs), representing groups of neuron firings. An SDR is modelled as a 
binary vector $$x \in \{0, 1\}^n$$ (for some $$n \in \mathbb{N}$$) with a sparse number of 'on' bits, meaning $$\sum_i x_i \ll n$$. Define 
$$S(n, k) := \{x \in \{0, 1\}^n : \sum_i x_i = k\}$$, i.e. the set of binary vectors with exactly $$k$$ 'on' bits.
* In the brain, singular neuron firings have been observed to represent semantic concepts.
* Two SDRs that represent a similar thing semantically should have an overlap in their 'on' bits to allow for better generalisation.
* SDRs have some nice benefits for communication, discussed [here](https://arxiv.org/abs/1503.07469).
* Current machine learning methods do not explicitly use sparse representations, but there is some evidence in LLMs that the 
'dense' representations are actually built up from sparse, semantic features, i.e. the model possibly learns to use SDRs.

Encoders are models of sensory receptors, which convert real world data into SDRs to be used by components in the brain.
* e.g. an encoder $$e$$ turning RGB image data into SDRs has type $$e: \mathbb{R}^{h \times w \times 3} \to S(n, k)$$ for some $$n$$, and $$k \ll n$$.

An SDR sparsity of $$2\%$$ is often used, meaning that $$\left[\frac{k}{n}\right] = 0.02$$.

####

(for understanding the HTM system generally, can skip this)

From the above, in the context of NLP, we want SDRs of, say, tokens across a vocabulary, to have overlapping 'on' bits when semantic 
information is shared.

One way of producing such semantic SDRs is through the use of **self-organizing maps**.

In the context of NLP, this involves:

Picking a tokenizer $$T: \text{str} \to [\text{int}]$$ with vocabulary size denoted by $$V$$.
For a dataset of text, split the text into **snippets** of paragraphs (or variable length). For each snippet, tokenize 
it, and create a binary vector of length $$V$$, with $$1$$ at a token index if that token is present in the snippet, and $$0$$ otherwise.
Pass these binarized snippets into the **BSOM algorithm**.
* [This](https://www.researchgate.net/publication/333462681_Adapting_Self-Organizing_Map_Algorithm_to_Sparse_Data) describes the BSOM algorithm in a general context (see Algorithm 2 in the linked).
* The BSOM algorithm in the context of NLP is described [here](https://jem-mosig.com/2021/03/semantic-map-embeddings/) (and also credit to the author Johannes E. M. Mosig for answering some of my questions regarding the process).

Implemented the BSOM algorithm [here](https://github.com/r-gould/htm/blob/fa3e1c87b53d61a65db148569c2992042deda065/bsom/codebook.py#L30C9-L30C10), with training script [here](https://github.com/r-gould/htm/blob/main/bsom/main.py).

### HTM System

#### Definitions

Now I describe the HTM system; a model of an input layer in a cortical column.
* This system accounts for only a portion of what a cortical column does, and does not account for interactions between layers and cortical columns.
* The spatial pooling and temporal memory algorithms describe how a HTM layer learns from a stream of input SDRs over time.

The layer is made up of a number, say $$C$$, of **minicolumns**. When the layer receives an input SDR $$x^{(t)} \in S(n, k)$$ (at time $$t$$), each minicolumn 
receives a fixed subset of the total input bits $$\{1, \ldots, n\}$$. This is described through the notion of **proximal synapses** $$S(c) \subset \{1, \ldots, n\}$$, with minicolumn $$c \in \{1, \ldots, C\}$$ possessing connections 
to the input indices in $$S(c)$$. Each proximal synapse $$s \in S(c)$$ has a time-dependent **permanence** $$p_{c, s}^{(t-1)} \in [0, 1]$$. Then the **receptive synapses** $$R^{(t-1)}(c) := \{s \in S(c) : p_{c, s}^{(t-1)} \geq p_{\text{thresh}}\} \subset S(c)$$ are 
synapses with a large enough permanence to actually **receive** their input (synapses $$s \in S(c) \backslash R^{(t-1)}(c)$$ are connected but do not actually receive the input as their permanence is not high enough, but during learning, 
the permanence can increase such that they become receptive).

* The spatial pooling algorithm updates these permanences and returns a set of **active columns** $$A^{(t)} \subset \{1, \ldots, C\}$$.
* Note that the set of minicolumns and the set of proximal synapses is fixed, and does not depend on the timestep $$t$$. Permanences change via. learning, which causes the set of receptive synapses to change.

Each minicolumn has a number, say $$N$$, of **neurons**. Each neuron has a set of **distal segments**. The total distal segments on a minicolumn (totalled across all neurons in the minicolumn) is given by $$\mathcal{D}^{(t-1)}(c)$$, and for $$d \in \mathcal{D}^{(t-1)}(c)$$, the **origin neuron** from which the segment stems from is given by $$\mathcal{N}(d) \in \{1, \ldots, NC\}$$. 
The **distal synapses** on a distal segment $$d$$ are given by $$\mathcal{S}^{(t-1)}(d)$$, and similarly to proximal synapses, each distal synapse $$s \in \mathcal{S}^{(t-1)}(d)$$ has a permanence $$q_{d, s}^{(t-1)} \in [0, 1]$$ with receptive synapses $$\mathcal{R}^{(t-1)}(d) := \{s \in \mathcal{S}^{(t-1)}(d) : q_{d, s}^{(t-1)} \geq q_{\text{thresh}}\} \subset \mathcal{S}^{(t-1)}(d)$$. Instead of each synapse connecting to a bit in the input, 
the synapses instead connect to other neurons across HTM layer. Specifically, each $$s \in \mathcal{S}^{(t-1)}(d)$$ has an associated **pre-synaptic neuron** $$\mathcal{P}(s) \in \{1, \ldots, NC\}$$ that it is connected to.

e.g. a neuron with 2 distal segments, each segment with 3 synapses (without including pre-synaptic neurons):

<img src="/assets/understanding-htm/distal_segment.png" width="350"/>

#### Overview

The HTM layer receives an input SDR $$x^{(t)}$$, and results in a set of neurons either being in **predictive state** or not. If a neuron is in predictive state, then it is predicting 
that the minicolumn that it is in will activate when given the next input $$x^{(t+1)}$$. The specifics of which neurons are firing within the minicolumn encodes the **context** based on previous inputs $$\{x^{(1)}, \ldots, x^{(t)}\}$$.

The spatial pooling algorithm is applied first, and then the temporal memory algorithm, in order to obtain these predictions. The details are described later, and here, brief overviews are given as to what these algorithms do.

#### Spatial pooling

The spatial pooling takes an input SDR $$x^{(t)}$$ and computes which minicolumns are active, based on how many receptive proximal synapses are active (the input index from $$x^{(t)}$$ that they connect to is 'on', equal to 1). If this number of active synapses meets an 
**overlap threshold** $$o_{\text{thresh}}$$, then the minicolumn is considered as active.

Then, global inhibition is performed, deactivating minicolumns that are not in the top $$M$$ overlap scores across minicolumns. Then, one can pick $$M$$ such that, say, at most $$2\%$$ of minicolumns are active, giving sparsity.
* Local inhibition can also be used, where minicolumns inhibit other minicolumns in their neighbourhood. See BaMI.

This process returns a set of active minicolumns $$A^{(t)} \subset \{1, \ldots, C\}$$.

For each active minicolumn in $$A^{(t)}$$, a Hebbian learning process is performed on the permanences of the proximal synapses, to obtain new permanences $$\{p_{c, s}^{(t)}\}_{c, s}$$.

#### Temporal memory

The end of timestep $$t-1$$ placed certain neurons in **predictive state**, meaning the minicolumns that contain such neurons are predicted to activate. 
We compare these predicted minicolumn activations with $$A^{(t)}$$, and when there is an overlap, the synapses on distal segments that were correctly active are strengthened, and those 
that were not active are weakened (Hebbian process). 

If a minicolumn in $$A^{(t)}$$ was not predicted, then the minicolumn **bursts**, with all neurons in this minicolumn becoming active, and a Hebbian process is applied identically to above for a chosen **learning segment** (see details below).

For non-active minicolumns not in $$A^{(t)}$$, synapses that **matched** (see details below) are punished.

Then, neurons are placed into predictive state for use in the next timestep $$t+1$$.

(quite a few details have been skimmed over, see below for a full treatment)

### Spatial Pooler

(for implementation see [here](https://github.com/r-gould/htm/blob/fa3e1c87b53d61a65db148569c2992042deda065/components/layer.py#L65C5-L65C7))

The set of active minicolumns $$A^{(t)}$$ is computed by the following procedure:
1. Initialize $$A^{(t)} := \{\}$$.
2. For each minicolumn $$c \in \{1, \ldots, C\}$$, define the **overlap** $$o^{(t)}(c) := \sum_{s \in \mathcal{R}^{(t-1)}(c)} x^{(t)}_s$$, describing the 
number of overlapping bits between the receptive field for minicolumn $$c$$ and the input $$x^{(t)}$$.
* If **boosting** is active, then set $$o^{(t)}(c) := b_c o^{(t)}(c)$$ where $$b_c$$ is the boosting parameter for minicolumn $$c$$. Boosting parameters are then modified during the learning stage if boosting is active.
3. Let $$\hat{o}$$ be the $$M$$th highest value from the set $$\{o^{(t)}(c)\}_{c=1}^{C}$$.
4. Now, for each minicolumn $$c \in \{1, \ldots, C\}$$, if $$o^{(t)}(c) \geq \max(o_{\text{thresh}}, \hat{o})$$, then add $$c$$ to the active columns, i.e. $$A^{(t)} := A^{(t)} \cup \{c\}$$.

We then perform a **Hebbian learning process** on the active columns **only**. Specifically,
1. For each active column $$c \in A^{(t)}$$, and for each proximal synapse $$s \in S(c)$$, we compute $$p^{(t)}_{c, s}$$. Specifically, 
if $$x^{(t)}_s = 1$$, then let $$p^{(t)}_{c, s} := \min(1, p^{(t-1)}_{c, s} + \Delta_+)$$. Otherwise, if $$x^{(t)}_s = 0$$, then 
let $$p^{(t)}_{c, s} := \max(0, p^{(t-1)}_{c, s} - \Delta_-)$$.

If boosting is active, we also:
1. Update the boosting paramters $\{b_c\}_{c=1}^{C}$. This involves TODO

The goal of boosting is to maintain homeostasis of neuronal activity, encouraging a variety of minicolumns to be activating across inputs 
rather than a small set of minicolumns dominating activity with most minicolumns remaining unactive for long periods.

### Temporal Memory

(for implementation see [here](https://github.com/r-gould/htm/blob/fa3e1c87b53d61a65db148569c2992042deda065/components/layer.py#L91C2-L91C9))

With the set of active columns $$A^{(t)}$$ obtained by spatial pooling, we can now perform the temporal memory algorithm.

Information from the previous timestep $$t-1$$:
* activated neurons $$\mathcal{N}^{(t-1)}_A$$: neurons that have activated, which happens as a result of a neuron correctly being in predictive state, or in the absence of a prediction when there should be, causing the minicolumn to burst.
* winner neurons $$\mathcal{N}^{(t-1)}_W$$: neurons that were either correctly predictive, or, in the case of a minicolumn bursting, either the neuron with the maximally matching segment, or, if there are no matching segments in the minicolumn, the neuron with the fewest segments.
* activated distal segments $$\mathcal{D}^{(t-1)}_A$$: set of all segments (across all minicolumns) that activated past threshold $$A_{\text{thresh}}$$, only considering activations from receptive synapses $$\mathcal{R}^{(t-1)}(d)$$.
* matching distal segments $$\mathcal{D}^{(t-1)}_M$$: set of all segments (across all minicolumns) that activated past threshold $$M_{\text{thresh}}$$, considering activations from all synapses $$\mathcal{S}^{(t-1)}(d)$$ (both receptive and non-receptive).
* $$n^{(t-1)}(d)$$: the number of active synapses on segment $$d$$ out of $$\mathcal{S}^{(t-1)}(d)$$ (including non-receptive synapses).

The temporal memory algorithm then finds the information for the next timestep, 
i.e. $$\mathcal{N}^{(t)}_A$$, $$\mathcal{N}^{(t)}_W$$, $$\mathcal{D}^{(t)}_A$$, $$\mathcal{D}^{(t)}_M$$, all initially set to $$\{\}$$, while learning from the input $$x^{(t)}$$. Whether a neuron is in predictive state is determined by 
whether one of its segments is active (in $$\mathcal{D}^{(t-1)}_A$$).

This process can be split into three processes, involving: active columns, inactive columns and updating of segments. These processes are explained below, 
in 8.1.1, 8.1.2, 8.1.3 respectively.

### Algorithm

#### For each active column $$c \in A^{(t)}$$

<u>If $$|\mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_A| > 0$$:</u> 

(if a segment of minicolumn $$c$$ is active, meaning the minicolumn was correctly predicted to be active)

In this case, we activate, and set to be a winner, any neurons that have any such active segments:

$$\mathcal{N}^{(t)}_A := \mathcal{N}^{(t)}_A \cup \{\mathcal{N}(d) : d \in \mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_A\}$$

$$\mathcal{N}^{(t)}_W := \mathcal{N}^{(t)}_W \cup \{\mathcal{N}(d) : d \in \mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_A\}$$

This corresponds to a neuron correctly being in predictive state at step $$t-1$$, and hence becoming active at step $$t$$.

Now, if in learing mode, a Hebbian process is applied to active distal segments. Additionally, we strengthen the pattern matching capability 
of the active segments by growing up to $$S_{\text{sample}}$$ synapses to the previous winning neurons from $$\mathcal{N}^{(t-1)}_W$$ (whose activations can be utilized to make next step predictions).

* For each active segment $$d \in \mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_A$$, then for each receptive synapse $$s \in \mathcal{R}^{(t-1)}(d)$$:
* if $$\mathcal{P}(s) \in \mathcal{N}^{(t-1)}_A$$ (if pre-synaptic neuron is active, meaning synapse $$s$$ is active), then strengthen permanence: 

$$q^{(t)}_{d, s} := \min(1, q^{(t-1)}_{d, s} + \delta_+)$$

* otherwise (synapse did not activate), weaken permanence:

$$q^{(t)}_{d, s} := \max(0, q^{(t-1)}_{d, s} - \delta_-)$$


and synapse growth:
* For each active segment $$d \in \mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_A$$, create a new set of synapses $$\mathcal{S}^{(t)}(d) \supset \mathcal{S}^{(t-1)}(d)$$ by extending $$\mathcal{S}^{(t-1)}(d)$$ through growing up to $$S - n^{(t-1)}(d)$$ new synapses to previous winning neurons $$\mathcal{N}^{(t-1)}_W$$ (each chosen randomly).
* Permanences of new synapses are initialized to $$q_{\text{init}}$$.

In this case there are no new segments formed, therefore $$\mathcal{D}^{(t)}(c) := \mathcal{D}^{(t-1)}(c)$$.

<u>If, instead, $$|\mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_A| = 0$$:</u>

(no segments of minicolumn $$c$$ is active, meaning no neuron was in predictive state, when it should have been)

In this case the column **bursts**. This means that **all** neurons 
within this minicolumn are activated (as opposed to only activating neurons with activated segments, as above): 

$$\mathcal{N}^{(t)}_A := \mathcal{N}^{(t)}_A \cup \{\mathcal{N}(d) : d \in \mathcal{D}^{(t-1)}(c)\}$$

After bursting, we wish to correct this for the future, such that the column will not burst again and instead will predict correctly. To do this, 
we pick the maximially matching segment, or, if there are no matching segments, a new segment grown on the neuron with the fewest segments, and apply a Hebbian learning rule on 
this new segment.

If $$|\mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_M| > 0$$ (if a segment in minicolumn $$c$$ is matching), then:
* Choose the **learning segment**:

$$\ell := \text{argmax}_{d \in \mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_M} \;n^{(t-1)}(d)$$

which is the maximally matching segment in minicolumn $$c$$.
* Choose the **winner neuron** as the origin neuron of the matching segment:

$$w := \mathcal{N}(\ell)$$



Otherwise, if $$|\mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_M| = 0$$ (no matching segments in minicolumn $$c$$), then:
* Choose the winner neuron to be the neuron in minicolumn $$c$$ with the fewest segments,

$$w := \text{argmin}_{n \in \{1, \ldots, NC\}} \;|\{d \in \mathcal{D}^{(t-1)}(c) : \mathcal{N}(d) = n\}|$$

breaking any ties randomly.

* If in learning mode, create a new segment $$d_{\text{new}}$$ originating from the winner neuron $$w$$, and set this new segment to be the learning segment:

$$\ell := d_{\text{new}}$$


This last step gives updated segments $$\mathcal{D}^{(t)}(c) := \mathcal{D}^{(t-1)}(c) \cup \{d_{\text{new}}\}$$.

Now add $$w$$ to the winner neurons:

$$\mathcal{N}^{(t)}_W := \mathcal{N}^{(t)}_W \cup \{w\}$$

If in learning mode, then a Hebbian learning rule and synapse growth is applied to the learning segment, identically to as the other case (though here, **just on the learning segment**, not across many segments).

#### For each inactive column $$c \notin A^{(t)}$$

If not in learning mode, nothing is done in this case. If in learning mode:

The idea is to punish segments that did activate, as they made an incorrect prediction since the minicolumn $$c$$ did not activate. So only performing the negative update 
in the Hebbian learning rule.

If there are matching segments, meaning $$|\mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_M| > 0$$, then for each matching segment $$d \in \mathcal{D}^{(t-1)}(c) \cap \mathcal{D}^{(t-1)}_M$$:
* Iterate over each synapse $$s \in \mathcal{S}^{(t-1)}(d)$$, and
* if $$\mathcal{P}(s) \in \mathcal{N}^{(t-1)}_A$$ (synapse activated), then weaken this synapse:

$$q^{(t)}_{d, s} := q^{(t-1)}_{d, s} - \delta_-$$


No new segments are grown, so $$\mathcal{D}^{(t)}(c) := \mathcal{D}^{(t-1)}(c)$$, and no new synapses are grown, so $$\mathcal{S}^{(t)}(d) := \mathcal{S}^{(t-1)}(d) \; \forall \; d \in \mathcal{D}^{(t)}(c)$$.

#### Updating segments

The above has determined new neuron information $$\mathcal{N}^{(t)}_A$$ and $$\mathcal{N}^{(t)}_W$$ alongside, for each $$d \in \mathcal{D}^{(t)}(c)$$, a set of new synapses $$\mathcal{S}^{(t)}(d)$$ and receptive synapses $$\mathcal{R}^{(t)}(d)$$ based on new permanences $$\{q^{(t)}_{d, s}\}_{s \in \mathcal{S}^{(t)}(d)}$$. 

We can now determine which segments are active and matching: $$\mathcal{D}^{(t)}_A$$ and $$\mathcal{D}^{(t)}_M$$. This involves a 
simple check over segments.

For each segment $$d \in \mathcal{D}(c)$$:
* Set $$n^{(t)}(d) := |\{\mathcal{P}(s) : s \in \mathcal{S}^{(t)}(d)\} \cap \mathcal{N}^{(t)}_A|$$
* If $$n^{(t)}(d) \equiv |\{\mathcal{P}(s) : s \in \mathcal{S}^{(t)}(d)\} \cap \mathcal{N}^{(t)}_A| \geq M_{\text{thresh}}$$ (sufficiently many active synapses, **including non-receptive** synapses), then update

$$\mathcal{D}^{(t)}_M := \mathcal{D}^{(t)}_M \cup \{d\}$$

* If $$|\{\mathcal{P}(s) : s \in \mathcal{R}^{(t)}(d)\} \cap \mathcal{N}^{(t)}_A| \geq A_{\text{thresh}}$$ (sufficiently many active **receptive** synapses), then update

$$\mathcal{D}^{(t)}_A := \mathcal{D}^{(t)}_A \cup \{d\}$$


with $$M_{\text{thresh}}, A_{\text{thresh}} \in \mathbb{N}$$ thresholds for matching and activating a segment respectively.