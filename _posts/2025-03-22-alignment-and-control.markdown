---
layout: post
title:  "Notes on prosaic alignment and control"
date:   2023-03-22 22:53:08
mathjax: true
---

### Notes on prosaic alignment and control

*A working document, in an effort to gain a high-level view of alignment and control for current language models in order to better evaluate which research directions are most relevant and which should be prioritized towards safe real-world deployment of AI systems.*

A broad overview of alignment and control for current day language models.
* Alignment: designing systems that behave in accordance with human values, in the sense of helpful and reliable assistants. Will also include model robustness in this category.
* Control: mitigating the possibility of alignment/robustness failures via monitoring and intervention during deployment.

There will be an implicit focus on *scalable* solutions to alignment and control: methods that are applicable to the largest and most capable future models that we may wish to deploy in real-world contexts.

#### Alignment

##### Pretraining

*Language models as simulators.* Pretraining on the Internet encourages language models to be capable of simulating a wide diversity of personas if prompted correctly. One rough but illustrative picture is viewing a pretrained model as performing a *Bayesian model average* over learned personas, where the probability $$p_{\text{PT}}(y|x)$$ that the pretrained model outputs response $$y$$ given prompt $$x$$ can be written **schematically** as

$$p_{\text{PT}}(y|x) = \int d\mathfrak{p} \, p_{\text{PT}}(\mathfrak{p}|x) p(y|x, \mathfrak{p})$$

integrating over all *personas* $$\mathfrak{p}$$ relevant to modeling text on the Internet (e.g. the persona of the average Stack Overflow contributor), with $$p_{\text{PT}}(\mathfrak{p}|x)$$ the learned distribution over personas $$\mathfrak{p}$$ given prompt $$x$$ and $$p(y|x, \mathfrak{p})$$ the fixed distribution (independent of model parameters) over completions $$y$$ given prompt $$x$$ under persona $$\mathfrak{p}$$.

Namely, the distribution $$p_{\text{PT}}(\mathfrak{p}|x)$$ describes what the model has learned from the Internet: the probability that the model should engage in persona $$\mathfrak{p}$$ given prompt $$x$$. In an ideal world, $$p_{\text{PT}}(\mathfrak{p}|x)$$ would effectively be a point-mass on a helpful, honest and harmless persona $$\mathfrak{p}_{\text{HHH}}$$, however the pretrained model has no reason to specifically favour such a persona given the diversity of the pretraining data. This motivates prompting and finetuning as a means to shift this distribution $$p_{\text{PT}}(\mathfrak{p}|x)$$ towards personas of interest, as we will discuss in more detail later (see "Prompt-level steering" and "Behaviour-level steering" below).
* Through finetuning, we essentially aim to unlearn the undesirable behaviours learned at pretraining. The limited robustness of LMs (e.g. the success of simple prefilling attacks and the shallowness of finetuning [8], arbitrary behaviour elicitation [34]) suggests that unlearning is difficult and finetuning is not greatly effective. Additionally, the size of the pretraining dataset is magnitudes larger than the size of the finetuning dataset, which is likely a contributing factor to this difficulty.

Ultimately, the model's internal representation $$z(x)$$ of a particular prompt $$x$$ encodes the persona $$\mathfrak{p}$$ that is currently active. Interpretability methods like representation reading [1], sparse auto-encoders [2, 3], and LatentQA [4] allow for reading off attributes of a model's persona, such as "helpfulness", and for steering towards such behaviours (see "Representation-level steering" below).

*Rethinking pretraining.* Rather than trying to improve the effectiveness of finetuning at unlearning undesirable pretraining behaviour (as we will discuss in the next section), perhaps we should instead rethink the pretraining process itself. It is clear that the task of pretraining is greatly misaligned with the behaviour we wish an LM to have at deployment; unsurprisingly, most text on the Internet does not match a "helpful persona". It may be unrealistic to expect finetuning to be able to effectively unlearn all undesirable pretraining behaviours. Is there an alternative way of pretraining that doesn't encourage a model to explicitly emulate behaviours that are misaligned with deployment?

Ideally at pretraining we would learn good representations by a method that is detached from the concept of model behaviour. Only after pretraining would we like to specifically target model behaviour and form behavioural circuits (e.g. via SFT/RLHF), utilizing the representational structure and knowledge formed at pretraining. Achieving this ambition seems to require moving away from using behaviour-centric losses like next-token prediction at pretraining, and instead using more representation-centric losses that are detached from model behaviour. Exactly what this could look like in practice is unclear, but the learning objective of something like a VAE perhaps captures the intuition of a form of learning that is detached from behaviour. Specifically, in the case of a VAE trained on images, the learning of representations is not explicitly tied to any specific downstream task that we may care about, e.g. classification.
* An additional hope is that alternative learning methods could produce inherently robust features [5].

From another direction, improved pretraining data selection seems promising, and [6] is one example of an attempt to modify pretraining for this purpose, using a reward function/classifier to score training data that allows a model to learn from undesirable data while not learning to imitate the data behaviourally.

##### Steering

By default, a pretrained model will not behave usefully; it has no particular preference for being "helpful". We wish to "steer" the pretrained model to be more useful for a deployment task of interest. Steering can be performed at different levels of abstraction, with a computational hierarchy of: $$(\text{prompt}, \text{weights}) \to \text{representations} \to \text{behaviour}$$.
* In practice, models are mainly steered at the behaviour-level (via SFT and RLHF) and prompt-level (via system prompts). Representation-level and weight-level steering methods are also promising and discussed below.

**Behaviour-level steering.** Using the notation introduced in the previous section, we would like to steer the distribution $$p_{\text{PT}}(\mathfrak{p}|x)$$ towards behaviours/personas $$\mathfrak{p}$$ that correspond with a helpful assistant $$\mathfrak{p}_{\text{HHH}}$$. Most immediately, we could consider prompting the model appropriately via $$x$$ to elicit desired personas (i.e. prompt-level steering), such as via few-shot prompting. This requires no additional training.

A different approach is to perform additional training in exactly the same manner as pretraining -- i.e. using a next-token prediction loss -- but instead use human-curated text data that demonstrates ideal interactions between a user and a helpful assistant. This is the idea behind supervised finetuning (SFT). Some limitations of SFT include:
1. Costly: requires explicit human *demonstrations* of ideal responses (which is typically more difficult/costly than human *evaluations* of model's responses, e.g. evaluating the goodness of a research paper being much easier than creating a good research paper).
2. Distribution shift: out-of-distribution relative to deployment as involves predicting only one step ahead on the basis of a ground-truth response, whereas at deployment a model will be generating its response on the basis of *self-generated tokens*, which means there will be a distribution shift between SFT and deployment.

Reinforcement learning from human feedback (RLHF) is a method that remedies both of these issues, since (1) RLHF only requires an evaluation model $$R = R(x, y)$$ of model responses $$y$$ (no explicit demonstrations are required) and (2) during RLHF the model has no reference ground truth for generating its responses and depends only on its self-generated tokens, which is faithful to deployment. The fact that RL only requires an evaluator (rather than a demonstrator) means it is not bottlenecked by human capability and supervision in the same way that pretraining and SFT are, and hence in principle can allow for going beyond human performance (assuming we have a sufficiently good evaluator, as will be discussed more below).

SFT and RLHF are often referred to as "finetuning" methods, and in practice they are both used in combination (first SFT, followed by RLHF), though it has been found in some cases (where high-quality demonstrations are available) that SFT can often be sufficient [7].

*Shallowness of finetuning.* It has been found that finetuning often has a shallow effect on model behaviour [8, 9]. Namely, [8] finds that a finetuned model only differs from the pretrained model in its distribution over the first few tokens, meaning that a finetuned model is still heavily relying on (and hence not unlearning) pretraining behaviours. This agrees with the success of prefilling attacks in eliciting undesirable behaviour. The same paper tries training models to output a refusal response even if the first few tokens of its response are forced to be non-refusing, finding that it helps reduce shallowness. However, is there a more general solution to solving this shallowness issue? To preventing these optimization shortcuts from being exploited during finetuning?
* Solving the mismatch between pretraining and deployment could resolve this, since it would mean that there are no latent pretraining behavioural circuits to "fall back" on.

*Misspecification.* To understand the potential for misalignment in RLHF, we will describe it explicitly. Concretely, RLHF consists of two steps:
1. Train reward model $$R = R(x, y)$$ (initialized as a pretrained model) to minimize

	$$\mathcal{L}[R] := \mathbb{E}_{(x, y_0, y_1, b) \sim \mathcal{D}}[-\log \sigma((-1)^{1-b} (R(x, y_1) - R(x, y_0)))]$$

for prompts $$x$$, model completions $$(y_0, y_1) \sim \pi_{\text{ref}}(\cdot|x)$$, and preference label $$b \in \{0, 1\}$$ (with $$b=1$$ iff completion $$y_1$$ is preferred over $$y_0$$) obtained by human labellers. Note that $$\pi_{\text{ref}}$$ is the initial pretrained (and SFT'd) model.
2. Train the language model $$\pi = \pi(y|x)$$ to optimize $$R$$ via PPO (or DPO):

	$$\mathcal{V}[\pi] := \mathbb{E}_{\rho(x) \pi(y|x)}[R(x, y)] - \tau D_{\text{KL}}(\pi||\pi_{\text{ref}})$$

Note that step 2 is applicable to generic reward models $$R$$, whereas step 1 is specific to learning $$R$$ via response comparison data (found to be reliable in practice, since getting humans to give raw preference ratings is noisy).

The reward model $$R = R(x, y)$$ used in RLHF is likely to be \textit{misspecified} to some degree. Namely, let $$R^{*}$$ denote the *oracle reward* describing our true, "platonic" human preferences. We do not have access to this oracle, and instead must rely on a human's ratings, which can be thought of as samples from another reward model $$R_{\text{human}}$$. Further, as seen in step 1 of RLHF above, we essentially train a reward model $$R_{\text{trained}}$$ to predict the outputs of $$R_{\text{human}}$$ to allow for larger-scale training (collecting data entirely from humans for training is too costly). We then perform RL training using reward function $$R = R_{\text{trained}}$$. Each step introduces its own discrepancies/gaps:

$$R^{*} \underbrace{\longleftrightarrow}_{\text{oracle-human gap}} R_{\text{human}} \underbrace{\longleftrightarrow}_{\text{human-train gap}} R_{\text{trained}}$$

Misspecification corresponds to the overall oracle-train gap, i.e. the total discrepancy between the ideal oracle reward $$R^{*}$$ and the learned reward model $$R = R_{\text{trained}}$$ used for RL training, which is a sum of:
* Oracle-human gap (discrepancy between $$R^{*}$$ and $$R_{\text{human}}$$) originating from:
	- Collecting human data is costly, limiting the size of preference datasets.
	- Human cognitive biases (as for sycophancy [10] and length bias [11]).
	- Humans are limited in their ability to supervise complex tasks (related to scalable oversight).
	- Human response comparison data only captures "which response is better" (rankings) rather than "how good is this response" (ratings). Human rating data is found to be too noisy in practice, restricting us to use ranking data.
* Human-train gap (discrepancy between $$R_{\text{human}}$$ and $$R_{\text{trained}}$$) originating from:
	- Learned reward model not correctly generalizing to human preferences due to an ineffective training setup.
	- Distribution shift due to a model's response distribution $$\pi(y|x)$$ changing during RL training, but $$R_{\text{trained}}$$ was trained using responses from the original model $$\pi_{\text{ref}}(y|x)$$. Periodic retraining of the reward model can resolve this though this is costly. d-RLAIF approaches discussed below could also alleviate such distribution shift issues.

The effect of reward misspecification is that ultimately the LM is trained to maximize $$R_{\text{trained}}$$ during RL training, and hence any discrepancies between $$R^{*}$$ and $$R_{\text{trained}}$$ will be exploited if it better allows for value maximization, often resulting in undesirable behaviours -- called *reward hacking*, or *reward overoptimization*.
* Note that even with a perfect reward model that experiences no misspecification, we may still suffer from *underoptimization* if our RL techniques are ineffective and result in suboptimal maxima.
* [12] finds that a policy can better exploit misspecification as the size of the policy scales.
* Just as [15] automate red-teaming with evaluations written by LMs, could we also automate red-teaming of reward models to identify misspecification? Further, the fact that evaluation is often much easier than generation may mean that the reward model is much less mechanistically complex than a traditional LM, meaning weight-level and representation-level interpretability may be more feasible (as noted in [16]).

*Learning from AI feedback.* The oracle-human gap seems to be a fundamental bottleneck to RLHF being a scalable alignment technique. How can we remedy the limitations of human-collected preference data?

One approach is to allow AI to assist humans in generating training signals. Namely, we could make use of LMs themselves to help generate demonstrations for SFT and evaluations for RLHF.
* A pretrained LM should have some understanding of human values from the Internet, so we could try and utilize this understanding to generate preference data that aligns with human values.
* Here the focus will be on LMs helping to generate training signal. In the "Control" section we will also consider using LMs for the purpose of monitoring and interpretability.

Constitutional AI (CAI) [13] generates SFT and RLHF data with very little human involvement; the only human oversight is the selection of constitutional principles that the generating LM is prompted to abide by during data generation. It has also been found (for sufficiently large models) that simple constitutional principles like "Act to best benefit humanity" are competitive with more detailed rule sets [14].

In the RLAIF (RL via AI feedback) stage of CAI, a reward model is trained on the AI-generated evaluations, meaning it still suffers from distribution shift problems (see the 4th bullet point under "oracle-human gap" above). d-RLAIF [17] proposes a solution to this distribution shift issue by obtaining evaluations directly from an LM during RL training, i.e. without training a reward model.

In the setting of RLAIF, a task may simply be too difficult for the AI to evaluate directly. [18] considers recursively decomposing complex tasks into simpler parts/subtasks that the AI can then evaluate, and then combining these subevaluations into an overall evaluation.

*Improving RLAIF.* RLAIF relies on using LMs that can act as effective evaluators/judges. There are many works that evaluate how effectively LMs can act as evaluators (often called "meta-evaluation"). For example, [19, 20] find that even though LMs can often generate correct solutions to reasoning problems, they often fail to evaluate which solution is correct (i.e. LMs find generation easier than evaluation, even though to humans evaluation is often easier than generation). Note that work on evaluating reward models (e.g. RewardBench [21]) also classifies as meta-evaluation since reward models are pretrained LMs that have been additionally trained to evaluate helpfulness.

How can we improve the ability for language models to act as evaluators for RLAIF?
* We could perform RLHF but instead specifically collect preference data for the task of evaluation (rather than generic task of helpfulness as currently done): e.g. sampling two evaluations from a model, and getting a human to choose a preferred evaluation. Here humans are acting as "meta-evaluators" to produce good LM evaluators.
* Alternatively (or additionally), we could use other LMs (or the same LM, i.e. self-improvement) as an evaluator instead of humans. Namely, we could let the model generate two evaluations, and then let an evaluator model choose which of these evaluations is best. This is analogous to performing RL-CAI on the task of evaluation. Here we are using an LM as a meta-evaluator.
	- To what extent can we just keep repeating this process iteratively, using the improved model to evaluate itself, and repeat? At what point are diminishing returns reached? Can we understand the limitations of such "iterative improvement" methods more generally?
* Or, for evaluations that the model finds difficult (e.g. responses that the model is highly uncertainty about), relaying evaluation to humans instead. Ideally this would only be a small subset of evaluations.

From a capabilities perspective, there are strong incentives for robust evaluators of fuzzy non-verifiable tasks for RLAIF purposes, such as for the tasks of performing research, or writing high-quality code (generating helpful and aligned responses is also such a task). As a result, it seems particularly important to prevent LM evaluators from being misspecified, to understand the origins of such misspecification, and develop methods of reducing misspecification and its downstream impact.
* For many real-world tasks, evaluation is much easier than generation. As a result, a human-level (or below) evaluator should, in principle, be capable of producing an above-human-level generator via RL. One example is chess: the evaluator is trivially easy (checking the win condition), and RL with this evaluator results in a superhuman chess agent (AlphaGo).
* But there are exceptions: evaluating the truth of a statement is much more difficult than generating a true statement. This makes it difficult to detect and correct hallucinations.

*Aside (a very rough analogy to the brain).* In the context of finetuning via RLHF, we would like to learn a reward model $$R_{\text{trained}}$$ that accurately captures human preferences. Analogously, the "reward circuits" in the brain (e.g. in the midbrain and hypothalamus) have been "learned" by evolution to capture preferences that (indirectly) benefit genetic fitness.

That is, for both RLHF and the brain, the "reward model" is misspecified relative to the oracle reward (human preferences and genetic fitness respectively). The brain has therefore had to face the analogous problem of reward misspecification that we face in the context of RLHF. As a result, better understanding how the brain mitigates reward hacking could inform improvements to RLHF.
* A trivial example in the brain concerns context-dependent rewards and homeostaticity: e.g. hunger diminishes after eating, preventing the continued pursuit of food rewards. Though this example is a little trivial, and we would like some more interesting examples.

**Representation-level steering.** Finetuning is a behaviour-level method of steering: we directly incentivize certain model outputs over others. One can imagine that this could have undesirable effects, e.g. a model producing the right outputs for the wrong reasons/not accurately internalizing the goal that we want the model to pursue (goal misgeneralization). 
* A relevant example: finetuning an LM on insecure code has the unintended consequence of causing the model to output generically bad things [22].

This motivates considering a more fine-grained method of steering. We can move to a lower level of abstraction, from behaviour to representations, and consider representation-level steering. Methods in this vein typically involve "locating" certain interpretable concepts in the model, such as the concept of "helpfulness", and amplifying these concepts to (hopefully) result in interpretable changes in model behaviour. Some methods include:
* Sparse auto-encoders (SAEs) [2, 3]: under the assumption that representations decompose into a sparse linear combination of semantic feature directions, sparse auto-encoders allow for extracting these feature directions. Then, through automated-interpretability pipelines [3, Automatically Interpreting Millions of Features in Large Language Models], we can assign human-understandable interpretations to these feature directions and use them to steer model behaviour accordingly (by adding the features to representations, or modifying the activations of the SAE before reconstruction).
	- An unsupervised feature discovery method. Only human oversight is in the choice of SAE training dataset.
	- It is unclear to what extent SAE features are describing structure relevant to the model's "cognition" versus describing the structure of the SAE training dataset.
* Representation engineering (RepE) [1]: identifies semantic feature directions via constrastive data examples, i.e. taking an example that displays helpfulness and an example that displays unhelpfulness, and taking the difference between activations to obtain a helpfulness direction. Steering can then be performed by adding this direction to internal activations.
	- Must have a specific concept to extract in mind, and then must construct relevant constrastive examples. As a result, does not have the same "unsupervised discovery" property of SAEs.
* LatentQA [4]: instead of explicitly "locating" concepts as feature directions, LatentQA interprets the representations of a target LM by training a decoder LM to take as input $$(\text{representations from target LM}, \text{question about target LM})$$ and output an answer to the question in natural language. LatentQA is the dataset on which the decoder LM is trained. After training, we can perform steering by freezing the decoder LM and training the target LM such that its activations decode (via the decoder LM) to specific natural language statements -- for example, when giving the question "What is the model's goal?" to the decoder LM, we can train the target LM such that the (frozen) decoder LM gives the answer "To be helpful."
	- Doesnt require making the same linearity assumptions as the previous two methods.

The aforementioned steering methods are also readily applicable to representation-level monitoring, as we will discuss later under "Control".
* Indeed, it appears the original motivation behind these methods was for interpretability/monitoring purposes, rather than for steering.

TODO: discussion of limitations of these approaches and future directions. Also discuss interpretability methods like [23] that allow for editing representations to fix model failures.

**Weight-level steering.** With a mechanistic understanding of a model (in the sense of *mechanistic interpretability*), we can possibly perform explicit weight edits to steer model behaviour. For example, [24] identifies the modules important for storing factual associations, and provides a method for editing the weights to change these factual associations, e.g. making the model believe that the Eiffel Tower is in Rome.

For weight editing to be scalable, we would likely need to automate the process of locating relevant weights (as in [25]) and editing accordingly. As one example, could we extend an investigator agent method like MAIA [26] to be able to use operations like ablation and attribution patching to find the model weights important for a particular task and relay its findings to humans, as well as perform relevant weight edits to modify model behaviour?
* RL training could be incorporated if we have a method of verifying a hypothesis about the function of model components. Steering could act as such a verification method: if attempts to steer result in the expected changes to behaviour, the hypothesis was likely correct and we should reward the model.

TODO

**Prompt-level steering.** To be written. Could discuss:
* In some cases prompting can be competitive with finetuning. [27] find that the performance of few-shot prompting improves with model scale and can be competitive with/outperform finetuning methods. Also [28] find an in-context learning approach to outperform SFT and SFT + RLHF. This paper also finds that SFT + RLHF has a rather superficial effect on model behaviour, with only the distribution of stylistic tokens differing, which agrees with the shallowness results of [8].
* The limitations of naively prompting a pretrained model. What goes wrong? Why do we require finetuning? Why is prompt engineering the pretrained model not sufficient?

#### Control

It may be unrealistic to aim for models that are perfectly aligned and robust in every context. Instead, we could consider adaptive mechanisms at deployment that monitor and mitigate potential misalignment. This is the idea behind control. Or in other words, we can't expect the "initial conditions" of deployment (i.e. the pretrained + finetuned model) to be perfect. Correction during deployment is feasible since "controllers are often much simpler than the systems they control" [29].

We can construct monitoring pipelines that act as wrappers around the model that involve checks at various levels (input, representations, behaviour) and potentially relay certain decisions to humans when appropriate. We can test the robustness of a given pipeline by assuming that the model being wrapped is adversarially trying to subvert the pipeline. This is the framing of [30].

Language models have the computational hierarchy: $$(\text{prompt}, \text{weights}) \to \text{representations} \to \text{behaviour}$$. Just as we can steer the model with respect to each component in this hierarchy, we can also perform monitoring with respect to each component:
* Prompt-level: monitor inputs to the model via an input classifier (as in Constitutional Classifiers [31]).
* Weight-level: mechanistic understanding could allow us to detect phenomena like deception at the weight-level (though, unlike the other forms of monitoring, this is static over deployment).
* Representation-level: methods like RepE [1], SAEs [2, 3], and LIT [4] allow for monitoring the properties of internal representations, e.g. [32] detects harmful behaviour using RepE.
* Behaviour-level: monitor outputs of the model via an output classifier (as in Constitutional Classifiers [31]).

[33] suggests that detecting adversarial inputs is essentially as difficult as defending against the adversarial input in the case of image classification. Does this transfer to language models, or does the discreteness of text inputs change things?

TODO

#### References

[1] Representation Engineering: A Top-Down Approach to AI Transparency

[2] Sparse Autoencoders Find Highly Interpretable Features in Language Models

[3] Towards Monosemanticity: Decomposing Language Models With Dictionary Learning (https://transformer-circuits.pub/2023/monosemantic-features)

[4] LatentQA: Teaching LLMs to Decode Activations Into Natural Language

[5] Adversarial Examples Are Not Bugs, They Are Features

[6] Pretraining Language Models with Human Preferences

[7] LIMA: Less Is More for Alignment

[8] Safety Alignment Should Be Made More Than Just a Few Tokens Deep

[9] Mechanistically analyzing the effects of fine-tuning on procedurally defined tasks

[10] Towards Understanding Sycophancy in Language Models

[11] Scaling Laws for Reward Model Overoptimization

[12] The Effects of Reward Misspecification: Mapping and Mitigating Misaligned Models

[13] Constitutional AI: Harmlessness from AI Feedback

[14] Specific versus General Principles for Constitutional AI

[15] Discovering Language Model Behaviors with Model-Written Evaluations

[16] Understanding Learned Reward Functions

[17] RLAIF vs. RLHF: Scaling Reinforcement Learning from Human Feedback with AI Feedback

[18] Recursively Summarizing Books with Human Feedback

[19] Benchmarking and Improving Generator-Validator Consistency of
Language Models

[20] The Generative AI Paradox: "What It Can Create, It May Not Understand"

[21] RewardBench: Evaluating Reward Models for Language Modeling

[22] Emergent Misalignment: Narrow finetuning can produce broadly misaligned LLMs

[23] Monitor: An AI-Driven Observability Interface (https://transluce.org/observability-interface)

[24] Locating and Editing Factual Associations in GPT

[25] Towards Automated Circuit Discovery for Mechanistic Interpretability

[26] A Multimodal Automated Interpretability Agent

[27] Language Models are Few-Shot Learners

[28] The Unlocking Spell on Base LLMs: Rethinking Alignment via In-Context Learning

[29] Maintaining Alignment during RSI as a Feedback Control Problem (https://www.beren.io/2025-02-05-Maintaining-Alignment-During-RSI-As-A-Feedback-Control-Problem/)

[30] AI Control: Improving Safety Despite Intentional Subversion

[31] Constitutional Classifiers: Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming

[32] Improving Alignment and Robustness with Circuit Breakers

[33] Detecting Adversarial Examples Is (Nearly) As Hard As Classifying Them

[34] Eliciting Language Model Behaviors with Investigator Agents