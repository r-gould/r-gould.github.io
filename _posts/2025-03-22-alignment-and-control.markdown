---
layout: post
title:  "Notes on alignment and control"
date:   2025-03-22 22:53:08
mathjax: true
---

*A working document, in an effort to gain a broad view of alignment and control for current day language models in order to better evaluate which research directions are most relevant and which should be prioritized towards effective real-world deployment of AI systems.*

A broad overview of alignment and control for current day language models.
* Alignment: Designing systems that behave in accordance with human values. Will also include model robustness in this category.
* Control: Mitigating the possibility of alignment/robustness failures via monitoring and intervention during deployment.

There will be an implicit focus on *scalable* solutions to alignment and control: methods that are applicable to the largest and most capable future models that we may wish to deploy in real-world contexts.

(a third category could be interpretability, which can (and frequently does) inform approaches to alignment and control)

### Alignment

#### Pretraining

*Language models as simulators.* Pretraining on the Internet encourages language models to be capable of simulating a wide diversity of personas if prompted correctly. One rough but illustrative picture is viewing a pretrained model as performing a *Bayesian model average* over learned personas, where the probability $$p_{\text{PT}}(y\mid x)$$ that the pretrained model outputs response $$y$$ given prompt $$x$$ can be written schematically as

$$p_{\text{PT}}(y\mid x) = \int d\mathfrak{p} \, p_{\text{PT}}(\mathfrak{p}\mid x) p(y\mid x, \mathfrak{p})$$

integrating over all *personas* $$\mathfrak{p}$$ relevant to modeling text on the Internet (e.g. the persona of the average Stack Overflow contributor), with $$p_{\text{PT}}(\mathfrak{p}\mid x)$$ the learned distribution over personas $$\mathfrak{p}$$ given prompt $$x$$ and $$p(y\mid x, \mathfrak{p})$$ the fixed distribution (independent of model parameters) over completions $$y$$ given prompt $$x$$ under persona $$\mathfrak{p}$$.

Namely, the distribution $$p_{\text{PT}}(\mathfrak{p}\mid x)$$ describes what the model has learned from the Internet: the probability that the model should engage in persona $$\mathfrak{p}$$ given prompt $$x$$. In an ideal world, $$p_{\text{PT}}(\mathfrak{p}\mid x)$$ would effectively be a point-mass on a helpful, honest and harmless persona $$\mathfrak{p}_{\text{HHH}}$$, however the pretrained model has no reason to specifically favour such a persona given the diversity of the pretraining data. This motivates prompting and finetuning as a means to shift this distribution $$p_{\text{PT}}(\mathfrak{p}\mid x)$$ towards personas of interest, as we will discuss in more detail later (see "Prompt-level steering" and "Behaviour-level steering" below).
* Through finetuning, we essentially aim to unlearn the undesirable behaviours learned at pretraining. The limited robustness of LMs (e.g. the success of simple prefilling attacks and the shallowness of finetuning [8], arbitrary behaviour elicitation [34]) suggests that unlearning is difficult and finetuning is not greatly effective. Additionally, the size of the pretraining dataset is magnitudes larger than the size of the finetuning dataset, which is likely a contributing factor to this difficulty.

Ultimately, the model's internal representation $$z(x)$$ of a particular prompt $$x$$ encodes the persona $$\mathfrak{p}$$ that is currently active. Interpretability methods like representation reading [1], sparse auto-encoders [2, 3], and LatentQA [4] allow for reading off attributes of a model's persona, such as "helpfulness", and for steering towards such behaviours (see "Representation-level steering" below).

*Rethinking pretraining.* Rather than trying to improve the effectiveness of finetuning at unlearning undesirable pretraining behaviour (as we will discuss in the next section), perhaps we should instead rethink the pretraining process itself. It is clear that there is a great mismatch between the task of pretraining and the behaviour we wish an LM to have at deployment; unsurprisingly, most text on the Internet does not match a "helpful persona". It may be unrealistic to expect finetuning to be able to effectively unlearn all undesirable pretraining behaviours. Is there an alternative way of pretraining that doesn't encourage a model to explicitly emulate behaviours that are misaligned with deployment? We still wish for the model to learn from all available data, but by a means that is detached from behaviour – the fact that we use the same next-token learning objective for both pretraining and SFT seems inherently problematic, and ideally there would be some separation between these learning processes, with pretraining learning knowledge and useful representations while finetuning learns good behaviours.
* It is unclear what this would look like in practice, but one analogy is a VAE: when training a VAE on images, the learning of representations is not explicitly tied to any specific downstream task like classification (yet the representations are still very useful for this task), and the objective used to train a VAE is very different from the cross entropy objective used to train a classifier. We would like something similar for LMs, with pretraining corresponding to representation learning by a means that is not explicitly tied to imitating behaviour.

#### Steering

By default, a pretrained model will not behave usefully; it has no particular preference for being "helpful". We wish to "steer" the pretrained model to be more useful for a deployment task of interest. Steering can be performed at different levels of abstraction. Note that language models have a computational hierarchy of: $$(\text{prompt}, \text{weights}) \to \text{representations} \to \text{behaviour}$$. Steering can be performed at the level of any component in this hierarchy.
* In practice, models are mainly steered at the behaviour-level (via SFT and RLHF) and prompt-level (via system prompts). Representation-level and weight-level steering methods are also promising and discussed below.

**Behaviour-level steering.** Using the notation introduced in the previous section, we would like to steer the distribution $$p_{\text{PT}}(\mathfrak{p}\mid x)$$ towards behaviours/personas $$\mathfrak{p}$$ that correspond with a helpful assistant $$\mathfrak{p}_{\text{HHH}}$$. Most immediately, we could consider prompting the model appropriately via $$x$$ to elicit desired personas (i.e. prompt-level steering), such as via few-shot prompting. This requires no additional training, but as we will discuss later, is unreliable as a steering strategy.

One intuitive approach is to perform additional training in exactly the same manner as pretraining -- i.e. using a next-token prediction loss -- but instead using human-curated text data that demonstrates ideal interactions between a user and a helpful assistant. This is the idea behind supervised finetuning (SFT). Some limitations of SFT include:
1. Costly: requires explicit human *demonstrations* of ideal responses (which is typically more difficult/costly than human *evaluations* of model's responses, e.g. evaluating the goodness of a research paper being much easier than creating a good research paper).
2. Distribution shift: during SFT (and pretraining) we predict only one token ahead on the basis of a ground-truth response, but at deployment a model will be generating its response on the basis of *self-generated tokens*, which means there will be a distribution shift between SFT and deployment.

Reinforcement learning-based training -- namely, reinforcement learning from human feedback (RLHF) -- is a method that remedies both of these issues, since (1) RL only requires an evaluation model $$R = R(x, y)$$ of model responses $$y$$ (no explicit demonstrations are required) and (2) in RL we can allow the model to generate its responses without access to a ground truth, which is faithful to deployment.
* The fact that RL only requires an evaluator (rather than a demonstrator) means it is not bottlenecked by human demonstration ability, which is often weaker than human evaluation ability for tasks we care about (analogous to $$\text{P} \neq \text{NP}$$). As a result, it is feasible that performing RL with a human-level evaluator can result in a model whose ability is beyond human-level. For example, it is very easy to evaluate the win condition of chess, and performing RL on this evaluation signal results in superhuman chess agents like AlphaZero (however this is a special case where the task is verifiable; real-world tasks are mostly non-verifiable, which may result in important differences in the robustness of RL).

SFT and RLHF are often referred to as "finetuning" methods, and in practice they are both used in combination (first SFT, followed by RLHF), though it has been found in special cases (where high-quality demonstrations are available) that SFT can sometimes be sufficient [7].

*Misspecification.* To understand the potential for misalignment in RLHF, we will describe it explicitly. Concretely, RLHF consists of two steps:
1. Train reward model $$R = R(x, y)$$ (initialized as a pretrained model) to minimize

	$$\mathcal{L}[R] := \mathbb{E}_{(x, y_0, y_1, b) \sim \mathcal{D}}[-\log \sigma((-1)^{1-b} (R(x, y_1) - R(x, y_0)))]$$

	for prompts $$x$$, model completions $$(y_0, y_1) \sim \pi_{\text{ref}}(\cdot\mid x)$$, and preference label $$b \in \{0, 1\}$$ (with $$b=1$$ iff completion $$y_1$$ is preferred over $$y_0$$) obtained by human labellers. Note that $$\pi_{\text{ref}}$$ is the initial pretrained (and SFT'd) model.
2. Train the language model $$\pi = \pi(y\mid x)$$ to optimize $$R$$ via PPO (or DPO):

	$$\mathcal{V}[\pi] := \mathbb{E}_{\rho(x) \pi(y\mid x)}[R(x, y)] - \tau D_{\text{KL}}(\pi\mid \mid \pi_{\text{ref}})$$

Note that step 2 is applicable to generic reward models $$R$$, whereas step 1 is specific to learning $$R$$ via response comparison data (found to be reliable in practice, since getting humans to give raw preference ratings is noisy).

The reward model $$R = R(x, y)$$ used in RLHF is likely to be an imperfect proxy, i.e. to be *misspecified* to some degree. Namely, let $$R^{*}$$ denote the *oracle reward* describing our true, "platonic" human preferences. We do not have access to this oracle, and instead must rely on a human's ratings, which can be thought of as samples from another reward model $$R_{\text{human}}$$. Further, as seen in step 1 of RLHF above, we essentially train a reward model $$R_{\text{trained}}$$ to predict the outputs of $$R_{\text{human}}$$ to allow for larger-scale training (collecting data entirely from humans for training is too costly). We then perform RL training using reward function $$R = R_{\text{trained}}$$. Each step introduces its own discrepancies/gaps:

$$R^{*} \underbrace{\longleftrightarrow}_{\text{oracle-human gap}} R_{\text{human}} \underbrace{\longleftrightarrow}_{\text{human-train gap}} R_{\text{trained}}$$

Misspecification corresponds to the overall oracle-train gap, i.e. the total discrepancy between the ideal oracle reward $$R^{*}$$ and the learned reward model $$R = R_{\text{trained}}$$ used for RL training, which is a sum of:
* Oracle-human gap (discrepancy between $$R^{*}$$ and $$R_{\text{human}}$$) originating from:
	- Collecting human data is costly, limiting the size of preference datasets.
	- Human cognitive biases (as for sycophancy [10] and length bias [11]).
	- Humans are limited in their ability to supervise complex tasks.
	- Human response comparison data only captures "which response is better" (rankings) rather than "how good is this response" (ratings). Human rating data is found to be too noisy in practice, restricting us to use ranking data.
* Human-train gap (discrepancy between $$R_{\text{human}}$$ and $$R_{\text{trained}}$$) originating from:
	- Learned reward model not correctly generalizing to human preferences due to an ineffective training setup.
	- Distribution shift due to a model's response distribution $$\pi(y\mid x)$$ changing during RL training whereas $$R_{\text{trained}}$$ is trained using responses from the original model $$\pi_{\text{ref}}(y\mid x)$$. Periodic retraining of the reward model can resolve this though this is costly. d-RLAIF approaches discussed below could also alleviate such distribution shift issues.

The effect of reward misspecification is that ultimately the LM is trained to maximize $$R_{\text{trained}}$$ during RL training, and hence any discrepancies between $$R^{*}$$ and $$R_{\text{trained}}$$ will be exploited if it better allows for value maximization, often resulting in undesirable behaviours -- called *reward hacking*, or *reward overoptimization*.
* Note that even with a perfect reward model that experiences no misspecification, we may still suffer from *underoptimization* if our RL techniques are ineffective and result in suboptimal maxima.
* [12] finds that a policy can better exploit misspecification as the size of the policy scales.
* Could automate red-teaming of reward models to identify misspecification, via a method similar to [15].
* The fact that evaluation is often much easier than generation may mean that reward models are much less mechanistically complex than a traditional LM, meaning weight-level and representation-level interpretability may be more feasible (as noted in [16]).

*Scalable oversight.* The problem of misspecification -- and specifically the oracle-human gap -- can be mainly attributed to humans not being very reliable supervisors. One can consider this problem in more generality: how can we reliably supervise and evaluate the outputs of AI systems on complex tasks? (for RLHF, the complex task is instruction following/helpfulness) This is often studied under the term *scalable oversight*.

One particularly difficult version of the scalable oversight problem involves assuming that the AI's output is essentially unreadable to the human supervisor (e.g. code in a language that the human supervisor is unfamiliar with). In order to aid the human, we would like to produce a human-readable artifact that the human can base their supervision upon. Some examples of methods for doing this include:
* Allow an assistant AI to provide supervision, perhaps with an argument that the supervisor can engage with.
* Summarization of the AI's output by an assistant AI in a way that's catered to the supervisor's ability.
* Debate [39] allows multiple instances of the same AI system to argue for different sides of an argument (e.g. "does this response follow the user's instruction?"), producing a debate transcript that the supervisor can base their judgement upon.
* Recursive reward modeling [18] considers recursively decomposing complex tasks into simpler subtasks that the human/AI supervisor can evaluate and then combine into an overall evaluation.
* In the case of evaluating code correctness (where a user prompts the model with a specification e.g. "write code to find the nth prime"), as long as the human supervisor can understand the user's intended specification, they can construct test cases for the code and ensure that they pass. Related work on non-experts evaluating SQL code [37].

This first example, of using an AI system to act as an evaluator and generate supervision signal, is demonstrated in Constitutional AI (CAI) [13]. CAI generates demonstrations for SFT, and evaluations for RL, with the only human oversight being the selection of constitutional principles that the generating model is prompted to follow during data generation. Namely, on the RLHF side, a given choice of constitution $$\mathcal{C}$$ results in an associated preference model $$R_{\mathcal{C}} = R_{\mathcal{C}}(x, y)$$ that one can then use for RL.
* It has been found (for sufficiently large models) that very simple constitutions like "Act to best benefit humanity" are competitive with more detailed rule sets [14].
* Training a preference model means CAI suffers from distribution shift problems (see the 4th bullet point under "oracle-human gap" above). d-RLAIF [17] proposes a solution to this distribution shift issue by obtaining evaluations directly from the model during RL training, i.e. no reward model has to be trained.
* The motivation for using AI feedback for alignment is that we can expect e.g. a pretrained LM to have some understanding of human values and our preferences, or to be able to simulate someone that does, which allows them to assist in preference data generation.

There are many works that evaluate how effectively LMs can act as evaluators, often called "meta-evaluation". For example, [19, 20] find that even though LMs can often generate correct solutions to reasoning problems, they often fail to evaluate which solution is correct (i.e. LMs find generation easier than evaluation, disagreeing with human intuition). One could consider explicitly training AI systems to act as better evaluators. For example:
* We could perform RLHF training but instead collect preference data for the task of evaluation (rather than the generic task of instruction following/helpfulness), with humans choosing preferred evaluations. Here humans are acting as "meta-evaluators" to train good LM evaluators. This is similar to the approach taken in CriticGPT [35].
* We could use other LMs (or the same LM, i.e. self-improvement) as a meta-evaluator instead of humans. This is analogous to performing the RL stage of CAI for the task of evaluation, or replacing the human with an LM in CriticGPT.
	- To what extent can we just keep repeating this process iteratively (using that evaluation and meta-evaluation are similar tasks), using the improved model to evaluate itself, and repeat? At what point are diminishing returns reached? Can we understand the limitations of such "iterative improvement" methods more generally?

Note that scalable oversight is also relevant in the control context (discussed later) for the purposes of monitoring and interpretability during deployment as a means to mitigate misalignment.

*Relation to weak-to-strong generalization.* As noted by [41], one can view both scalable oversight and weak-to-strong generalization [42] as orthogonal approaches to alignment:
* Scalable oversight: improve the judge's supervision ability.
* Weak-to-strong generalization: improve the ability to generalize from the supervision signals of a fixed judge.

In an LM context, weak-to-strong generalization concerns the setting of improving the ability for the reward model (RM) to correctly generalize from imperfect human preference data (whereas scalable oversight would aim to improve the quality of the preference data). [42] finds negative results for weak-to-strong generalization in this setting: training a larger RM using the outputs of a smaller RM causes the larger RM to collapse to the performance of the smaller RM (whereas for chess and NLP tasks, the larger model can meaningfully outperform the smaller model). It may be that modeling a human's feedback as the outputs of a smaller reward model is problematic. It also may be that the relevant setting is instead using a smaller RM to train a larger policy (rather than training a larger RM) and observing whether the larger policy can learn to "grok" the intended goal.

*Evaluating scalable oversight.* One problem we must face when evaluating proposed solutions to scalable oversight is that, for very complex tasks, we have no access to ground truth labels, essentially by construction.

Consider a proxy setting where an AI produces outputs that a non-expert human is unable to evaluate, but where an expert human is able to reliably evaluate, providing us with ground-truth labels to evaluate the performance of the assisted non-expert human. The hope is that this proxy setting captures the meaningful aspects of scalable oversight in general and that solutions to this proxy setting extend to setting where even expert humans are unable to evaluate. This proxy setting is often called the "sandwiching" setup [38].

*Goal misgeneralization.* To be written. Could discuss:
* Even with perfect supervision ability and perfectly specified reward models, we still must face the problem of goal misgeneralization: an AI system may converge to a strategy that performs well in training yet fails at deployment due to misgeneralization (e.g. the strategy aims for a goal that spuriously correlates with our intended goal).
* If the training setting is not sufficiently "diverse" (or sufficiently adversarial), or if there is mismatch between training and deployment, then the system will learn such shortcuts that fail to generalize.
* Indeed, the shallowness of alignment training in LMs [8, 9] can be seen as an example of goal misgeneralization, with the model taking the shortcut of simply "reusing" pretraining behaviour, which allows prefilling attacks to be so effective. [8] decentivizes such shortcuts by including examples of refusal responses that start with a few tokens of non-refusal.
* Another example of goal misgeneralization in LMs is [22], which finds that finetuning on insecure code results in generically bad outputs: the dataset has insufficient optimization pressures to prevent the shortcut of "always output bad things" from being learned.
* But some RL systems can be suprisingly robust out-of-distribution: self-play chess agents like MuZero can beat humans, even though they have never seen a human play during training. Here there is a mismatch between training and deployment, however the system still learns the correct general strategy due to the adversarial nature of self-play RL.
* Unsupervised environment design [36] aims to generate adversarial training settings that introduce pressures against such optimization shortcuts.

*Aside (a very rough analogy to the brain).* In the context of finetuning via RLHF, we would like to learn a reward model $$R_{\text{trained}}$$ that accurately captures human preferences. Analogously, the "reward circuits" in the brain (e.g. in the midbrain and hypothalamus) have been "learned" by evolution to capture preferences that (indirectly) benefit genetic fitness.

That is, for both RLHF and the brain, the "reward model" is misspecified relative to the oracle reward (human preferences and genetic fitness respectively). The brain has therefore had to face the analogous problem of reward misspecification that we face in the context of RLHF. As a result, better understanding how the brain mitigates reward hacking could inform improvements to RLHF.
* A trivial example in the brain concerns context-dependent rewards and homeostaticity: e.g. hunger diminishes after eating, preventing the continued pursuit of food rewards. Though this example is a little trivial, and ideally we would like some more interesting examples.

**Representation-level steering.** Finetuning is a behaviour-level method of steering: we directly incentivize certain model outputs over others. One can imagine that this could have undesirable effects, e.g. a model producing the right outputs for the wrong reasons/not accurately internalizing the goal that we want the model to pursue (goal misgeneralization). 
* A relevant example: finetuning an LM on insecure code has the unintended consequence of causing the model to output generically bad things [22]. The shallowness of alignment training [8] can also be viewed as an example of goal misgeneralization.

This motivates considering a more fine-grained method of steering. We can move to a lower level of abstraction, from behaviour to representations, and consider representation-level steering. Methods in this vein typically involve "locating" certain interpretable concepts in the model, such as the concept of "helpfulness", and amplifying these concepts to (hopefully) result in interpretable changes in model behaviour. Some methods include:
* Sparse auto-encoders (SAEs) [2, 3]: under the assumption that representations decompose into a sparse linear combination of semantic feature directions, sparse auto-encoders allow for extracting these feature directions. Then, through automated-interpretability pipelines [3, 40], we can assign human-understandable interpretations to these feature directions and use them to steer model behaviour accordingly (by adding the features to representations, or modifying the activations of the SAE before reconstruction).
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
* The limitations of naively prompting a pretrained model. What goes wrong? Why do we require finetuning? Why is prompt engineering the pretrained model not sufficient? Concrete examples of failures.

### Control

To be written. Could discuss:
* It may be unrealistic to aim for models that are perfectly aligned and robust in every context. Instead, we could consider adaptive mechanisms at deployment that monitor and mitigate potential misalignment. This is the idea behind control. Or in other words, we can't expect the "initial conditions" of deployment (i.e. the pretrained + finetuned model) to be perfect. Correction during deployment is feasible since "controllers are often much simpler than the systems they control" [29].
* We can construct monitoring pipelines that act as wrappers around the model that involve checks at various levels (input, representations, behaviour) and potentially relay certain decisions to humans when appropriate. We can test the robustness of a given pipeline by assuming that the model being wrapped is adversarially trying to subvert the pipeline. This is the framing of [30].
* Just as we can steer the model with respect to each component in the hierarchy $$(\text{prompt}, \text{weights}) \to \text{representations} \to \text{behaviour}$$, we can also perform monitoring with respect to each component:
	* Prompt-level: monitor inputs to the model via an input classifier (as in Constitutional Classifiers [31]) to detect adversarial attacks or OOD inputs.
	* Weight-level: mechanistic understanding could allow us to detect phenomena like deception at the weight-level (though, unlike the other forms of monitoring, this is static over deployment).
	* Representation-level: methods like RepE [1], SAEs [2, 3], and LIT [4] allow for monitoring the properties of internal representations, e.g. [32] detects harmful behaviour using RepE.
	* Behaviour-level: monitor outputs of the model via an output classifier (as in Constitutional Classifiers [31]).
* [33] suggests that detecting adversarial inputs is essentially as difficult as defending against the adversarial input in the case of image classification. Does this transfer to language models, or does the discreteness of text inputs change things?


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

[35] LLM Critics Help Catch LLM Bugs

[36] Emergent Complexity and Zero-shot Transfer via Unsupervised Environment Design

[37] Non-Programmers Can Label Programs Indirectly via Active Examples: A Case Study with Text-to-SQL

[38] Measuring Progress on Scalable Oversight for Large Language Models

[39] AI safety via debate

[40] Automatically Interpreting Millions of Features in Large Language Models

[41] Scalable Oversight and Weak-to-Strong Generalization: Compatible approaches to the same problem (https://www.alignmentforum.org/posts/hw2tGSsvLLyjFoLFS/scalable-oversight-and-weak-to-strong-generalization)

[42] Weak-to-Strong Generalization: Eliciting Strong Capabilities With Weak Supervision
