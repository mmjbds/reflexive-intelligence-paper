# Reflexive Intelligence: Decision-Making in Observer-Participant Environments

> **Draft V3.0** | 2026-04-12 | Target: arXiv preprint
> Author: Mian Zhang¹
> ¹ Independent Researcher
> Contact: zhangmian061@arxiv.org
> Status: DRAFT

---

## Abstract

Modern AI systems achieve remarkable success where the environment ignores the agent. AlphaGo plays within rules that do not bend. GPT-4 generates text without reshaping the English language. AlphaFold predicts structures that remain politely unchanged by the act of prediction. We call these **observer-invariant environments** — and note that every major AI breakthrough inhabits one.

Yet many of the most consequential real-world systems refuse this courtesy. A fund's trades move prices. A policy announcement alters citizen behavior. A recommendation algorithm reshapes the content it curates. These are **observer-participant environments**: the agent's decisions causally alter the ground it stands on.

We introduce **Reflexive Intelligence** — the capacity to decide while accounting for one's own causal impact on the observed environment. We formalize the observer-participant feedback loop, propose a taxonomy spanning six domains, and present a neuroscience-motivated cognitive architecture for training reflexive reasoning in large language models via multi-reward Group Relative Policy Optimization (GRPO). We argue this represents the missing complement to LeCun's Joint-Embedding Predictive Architecture: where JEPA learns how worlds evolve, Reflexive Intelligence learns how the observer's participation changes that evolution.

We provide preliminary evidence from a financial world model, where a 3B-active-parameter Mixture-of-Experts model — trained at a cost accessible to individual researchers — exhibits emergent reflexive reasoning not spontaneously produced by generic models with 600× more parameters under zero-shot conditions. The bottleneck, we find, is not scale but **training methodology**: the specific curriculum of failures survived and lessons encoded.

**Keywords**: reflexive intelligence, observer-participant systems, world models, GRPO, cognitive architecture

---

## 1. Introduction

### 1.1 The Assumption Nobody States

There is an assumption so deeply embedded in AI research that it functions like gravity — invisible, ubiquitous, and only noticed when it breaks. We call it **observer-invariance**:

> **Definition 1 (Observer-Invariant Environment).** An environment $\mathcal{E}$ is observer-invariant if its transition dynamics are independent of the agent's identity, history, and the cumulative effects of its past actions on the state distribution.

Every system in the table below satisfies this definition. Every system has also been celebrated as a breakthrough:

| System | Environment | Why Observer-Invariant |
|:---|:---|:---|
| AlphaGo | Go board | The stones do not move themselves because AlphaGo is watching |
| GPT-4 | Text corpora | English grammar does not drift because GPT-4 generated a paragraph |
| AlphaFold | Proteins | Molecular physics is not taking notes on who is folding what |
| Self-driving (L2) | Road geometry | Lane markings do not rearrange themselves in protest |
| DeepSeek-R1 | Math problems | $2+2$ remains stubbornly equal to $4$ regardless of who computes it |

The pattern is clear: **AI excels where the world holds still while being examined.** But many of the most important systems for humanity are not so obliging.

### 1.2 When the World Watches Back

We define a second class of environments — ones that violate observer-invariance with varying degrees of hostility:

> **Definition 2 (Observer-Participant Environment).** An environment $\mathcal{E}$ is observer-participant if the agent's cumulative action history causally shifts the environment's state distribution. The world is not merely observed; it is reshaped by the observation.

The distinction is not that actions have consequences — all RL environments share this property. The distinction is that actions alter the **statistical regularities** the agent was trained to exploit. A fund that identifies and trades an inefficiency may eliminate that inefficiency. A recommendation algorithm that surfaces content reshapes what creators produce. The map, when used as navigation, redraws the territory.

George Soros (1987) called this **reflexivity**: participants' beliefs about market states alter those states, which alter beliefs. But reflexivity reaches well beyond markets:

| Domain | Feedback Loop | What Breaks |
|:---|:---|:---|
| **Financial Markets** | Trading → price impact | The price you were predicting |
| **Policy-Making** | Policy → behavioral adaptation | The behavior you were regulating |
| **Social Recommendation** | Curation → content creation | The ecosystem you were curating |
| **Autonomous Driving** | Route optimization → traffic redistribution | The traffic model you were optimizing against |
| **Epidemiology** | Intervention → compliance drift | The transmission dynamics you were modeling |
| **Hiring AI** | Screening criteria → applicant gaming | The signal quality you were screening for |

Each row describes a system where deploying an AI agent makes the AI agent's training data partially obsolete. This is not an edge case — it is the default condition in social systems.

### 1.3 The Missing Half of World Models

LeCun (2022) has argued persuasively that the next frontier is **world models** — systems that learn abstract, predictive representations of how environments evolve. The Joint-Embedding Predictive Architecture (JEPA) proposes learning future abstract states from current ones, enabling planning without pixel-level simulation.

We agree with the vision and identify a gap. JEPA models how worlds evolve — but not how the observer's participation changes that evolution. In observer-invariant environments, this omission is harmless; the observer's participation changes nothing. In observer-participant environments, it is fatal.

We propose **Reflexive Intelligence** as the complement:

> **Definition 3 (Reflexive Intelligence).** The capacity to make decisions while maintaining an internal model of one's own causal impact on the environment — accounting for how one's actions alter the distribution of future observations.

Put simply:

```
World Model (JEPA):       "How does the world evolve?"
Reflexive Intelligence:   "How does the world evolve given that I am part of it?"
```

This is, in Soros's language, the difference between the cognitive function (understanding the world) and the participative function (changing it by acting on that understanding). Modern AI has mastered the first while entirely neglecting the second.

### 1.4 Relationship to Performative Prediction

Perdomo et al. (2020) formalized a closely related concept: **Performative Prediction**, where a model's predictions influence the outcomes it aims to predict. Their work establishes theoretical foundations — convergence conditions for iterative retraining, the concept of performative stability — and has rightly generated significant follow-up research.

Our work differs in three dimensions:

1. **Decision vs. prediction.** Performative Prediction and its extension to strategic classification (Hardt et al., 2016) study models whose outputs influence future data; we study agents that must *choose actions* — with associated costs, risks, and irreversibility — in environments where the impact is not merely distributional but structural.
2. **Cognitive architecture vs. learning theory.** Perdomo et al. provide convergence guarantees for retraining procedures; we propose a neuroscience-motivated architecture for training reflexive capabilities directly into model weights via multi-reward reinforcement learning.
3. **Emergent reasoning vs. equilibrium analysis.** We focus on *emergent reflexive reasoning* — the model spontaneously generating text that reasons about its own environmental impact — rather than characterizing fixed points of iterative retraining.

We view these as complementary: Performative Prediction diagnoses the disease; we propose a treatment through cognitive training.

### 1.5 Contributions

1. **Formalization** of observer-invariance and observer-participation, with practical implications for AI deployment (§2).
2. **Taxonomy** of observer-participant environments spanning six domains, identifying four shared structural properties (§3).
3. **Reflexive Intelligence Framework**: formal definition, relationship to JEPA, and an argument for why scaling laws have bounded applicability in reflexive domains (§4).
4. **Neuroscience-Motivated Cognitive Architecture** mapping cognitive functions to GRPO reward functions: a practical recipe for training reflexive capabilities in LLMs (§5). We claim analogy, not homology.
5. **Craft Knowledge Documentation**: the first systematic account of multi-reward GRPO interaction effects, stabilization patterns, and failure modes — tacit knowledge absent from the algorithmic literature (§5.5). We commit to releasing complete training logs and checkpoint histories to enable community study.
6. **Preliminary Evidence** that a 3B-active model trained with this framework exhibits reflexive reasoning not spontaneously produced by generic models with vastly more parameters, suggesting training methodology matters more than scale in observer-participant domains (§6).

---

## 2. Background

### 2.1 World Models

World models encompass simulation-based approaches (Ha & Schmidhuber, 2018; Hafner et al., 2020), abstract prediction architectures (LeCun, 2022; Assran et al., 2023), and language-based world models (Li et al., 2024; Xiang et al., 2024). All share the observer-invariance assumption: environment dynamics are treated as independent of the agent.

### 2.2 Reflexivity in Social Science

The concept has deep roots. Soros (1987) formalized reflexivity in markets. Goodhart (1975) warned that targeted measures cease to be good measures. Lucas (1976) demonstrated that policy models break when policy changes. Campbell (1979) predicted the corruption of social indicators used for decision-making. These insights have never been systematically integrated into AI system design.

### 2.3 Performative Prediction and Strategic Classification

Perdomo et al. (2020) introduced the formal framework of Performative Prediction at ICML, establishing convergence conditions for models whose predictions shift the data distribution. Mendler-Dünner et al. (2020) extended this to stochastic optimization. Hardt et al. (2016) studied Strategic Classification, where agents being classified adapt their features to game the classifier — a specific form of adversarial observer-participation. We build on these theoretical foundations while pursuing a complementary direction: not analyzing convergence properties or equilibrium existence, but training models that can *reason in natural language* about their own reflexive impact.

### 2.4 GRPO and Emergent Reasoning

DeepSeek-R1 (Guo et al., 2025) demonstrated that Group Relative Policy Optimization can elicit emergent chain-of-thought reasoning in LLMs for mathematical tasks. DAPO (Yu et al., 2025) refined this with dynamic sampling and clip-higher mechanisms. Both operate in observer-invariant domains with verifiable ground truth. We extend GRPO to observer-participant environments where ground truth is endogenous.

---

## 3. Taxonomy of Observer-Participant Environments

We identify four structural properties shared across observer-participant domains:

**P1: Endogenous State Transition.** The agent's actions enter the environment's transition function not merely as control inputs but as *distributional shifters*. The statistical regularities change.

**P2: Self-Defeating Predictions.** Accurate predictions, when acted upon, become self-defeating. The very act of exploiting a pattern erodes it. A DJ who knows the crowd's favorite track also knows that playing it at the wrong moment — too early, too predictable, right after a similar peak — can clear the floor faster than silence. The crowd's response is not a fixed function of the track; it is a function of the entire set preceding it. Market alpha is no different: the edge is not in the signal, it is in the timing, the sizing, and the footprint left on prices by the act of capture.

**P3: Regime Non-Stationarity.** Observer-participant environments exhibit qualitative phase transitions — not mere distributional drift, but sudden structural reorganizations. A dance floor does not gradually thin; it empties in a moment, when enough people sense the energy has gone. Financial markets do not slowly de-risk; they panic in minutes. The transitions are triggered not by any single action but by the accumulated weight of all participants' actions reaching a tipping point. What shifts is not the data distribution but the entire game being played.

**P4: Adversarial Adaptation.** Other participants adapt to the agent's presence. In markets: front-running, alpha decay, and crowding. In recommendation: gaming the ranking algorithm. In hiring: resume engineering toward the detected screening criteria. The environment is not merely changing — it is *strategically responding* to being played.

### Domain Severity Matrix

| Domain | P1 | P2 | P3 | P4 | Severity |
|:---|:---:|:---:|:---:|:---:|:---:|
| Financial Markets | ✅ Strong | ✅ Strong | ✅ Strong | ✅ Strong | **Critical** |
| Policy-Making | ✅ Strong | ✅ Mod | ✅ Strong | ✅ Mod | **High** |
| Social Recommendation | ✅ Strong | ✅ Strong | ✅ Mod | ✅ Strong | **High** |
| Autonomous Driving | ✅ Mod | ✅ Mod | ✅ Weak | ✅ Weak | **Moderate** |
| Epidemiology | ✅ Strong | ✅ Strong | ✅ Strong | ✅ Weak | **High** |
| Hiring/Admissions | ✅ Mod | ✅ Strong | ✅ Weak | ✅ Strong | **Moderate** |

Financial markets score maximum on every axis, making them the ideal — and most demanding — testbed for Reflexive Intelligence.

---

## 4. The Reflexive Intelligence Framework

### 4.1 Formal Definition

A **Reflexive Decision Process (RDP)** extends the standard MDP with a reflexive impact function: $(\mathcal{S}, \mathcal{A}, T, R, \Phi, \gamma)$, where $\Phi: \Pi \times \mathcal{H} \rightarrow \Delta(\mathcal{S})$ maps the agent's policy and action history to a perturbation of the state distribution.

The critical equation:

$$s_{t+1} \sim T(s_t, a_t, \Phi(\pi_\theta, H_{1:t}))$$

In a standard MDP, $\Phi = \emptyset$. In an RDP, **the agent is part of the territory it maps.**

### 4.2 Why Scaling Fails in Observer-Participant Environments

The argument proceeds in three steps:

1. **Pretraining encodes observer-invariant knowledge.** "The capital of France is Paris" does not change based on who asks. All pretraining knowledge shares this property.

2. **Reflexive reasoning requires experiential learning.** Understanding your own impact on the environment cannot be absorbed from text. It requires training *in environments that respond to your actions* — which standard pretraining does not provide.

3. **Therefore, larger pretrained models do not spontaneously acquire reflexive reasoning.** A 1.8T-parameter model has more observer-invariant knowledge than a 3B model, but neither has reflexive knowledge unless trained for it. The marginal return of scale diminishes sharply in reflexive domains.

This predicts a specific empirical signature: on reflexive tasks, the gap between a large generic model and a small reflexive model should favor the small model. We provide preliminary evidence for this in §6.

---

## 5. Neuroscience-Motivated Cognitive Architecture

Human traders, diplomats, and military strategists routinely operate in observer-participant environments. Cognitive neuroscience has identified neural mechanisms supporting this capacity. We propose a computational architecture *motivated by* — though not claiming to replicate — these mechanisms. The mapping between brain structures and training components is analogical: we claim functional inspiration, not biological homology.

### 5.1 Architecture Overview

| Brain Structure | Cognitive Function | Training Mechanism |
|:---|:---|:---|
| **Prefrontal Cortex** | Executive decision under uncertainty | Format reward with multi-format recognition |
| **Hippocampus** | Episodic memory of past decisions and outcomes | Decision history injection in context |
| **Anterior Cingulate** | Conflict monitoring, error detection | Reasoning-decision consistency reward |
| **Insula / Somatic Markers** | Gut-feeling risk calibration | Kelly alignment + regime sensitivity |
| **Mirror Neurons** | Theory of mind, crowd modeling | Reflexivity awareness reward |
| **Basal Ganglia** | Action selection, habit formation | Multi-reward GRPO optimization |
| **Cerebellum** | Timing, precision calibration | Causal chain depth reward |

Each row corresponds to a concrete reward function in our GRPO training pipeline. We emphasize that these are functional analogies — the reward functions were designed to encode specific cognitive priors, not to simulate neural circuits. The neuroscience connection provides design motivation and a vocabulary for discussing the architecture, not a claim of biological fidelity.

### 5.2 Multi-Reward GRPO

We use GRPO (Shao et al., 2024) with domain-specific reward functions serving as cognitive modules:

$$r_{total} = \sum_{i=1}^{K} w_i \cdot r_i(\text{completion}, \text{context})$$

Unlike standard GRPO (DeepSeek-R1, DAPO) using a single correctness reward, our multi-reward setup creates a **cognitive landscape** shaped by:
- **Structural rewards**: format, consistency — the grammar of decisions
- **Epistemic rewards**: causal depth, cross-domain coverage — reasoning quality
- **Reflexive rewards**: crowd awareness, regime sensitivity, memory utilization — observer-participant cognition

### 5.3 Progressive Reward Shaping

A key empirical finding: **the order of reward activation matters critically.** Activating all rewards simultaneously leads to gradient fragmentation. We discovered this through failure: our fourth training round collapsed within 20 steps when all ten rewards competed for gradient bandwidth.

The solution mirrors cognitive development:

```
Phase 1 (Steps 0-N):   Structural rewards only
                        → The model learns the language of decisions

Phase 2 (Steps N-2N):   + Epistemic rewards
                        → The model learns to reason before deciding

Phase 3 (Steps 2N+):    + Reflexive rewards
                        → The model learns to account for its own impact
```

Children learn language before logic, logic before empathy. The training curriculum follows the same progression — not by design, but because we discovered through repeated failure that any other order collapses.

### 5.4 The Cross-Disciplinary Signal Vocabulary

A distinctive feature of our architecture is an analytical vocabulary drawn from disciplines that most financial AI researchers would consider decorative. We consider them structural:

**Bach's Counterpoint — Cross-Asset Harmony Detection.** In counterpoint, voices must move independently; when all voices fall into parallel motion, the composition loses depth and tension. We apply this literally: multiple asset classes are modeled as simultaneous voices. When equities, bonds, gold, and crypto all begin moving in parallel — what counterpoint calls a "forbidden parallel fifth" — the system flags a potential liquidity crisis. Not because of the direction, but because diversity of motion has collapsed. The implementation (`renaissance_layer.py: CounterpointAnalyzer`) computes cross-asset harmony tension continuously, triggering portfolio warnings when parallelism exceeds a learned threshold. This is not a metaphor appended to satisfy reviewers; it is a detection mechanism with measurable precision.

**Chekhov's Gun — Structural Imbalance Monitoring.** In Chekhov's dramaturgical principle, a gun introduced in Act 1 must fire by Act 3. The principle is about inevitable consequence: structural elements, once introduced, demand resolution. Our system maintains a registry of seven known structural imbalances — US debt-to-GDP, Japanese carry trade scale, commercial real estate vacancy rates, private equity unrealized NAV, stablecoin reserve opacity, China's deflation regime, and AI capex concentration — each with documented trigger signals and causal transmission chains. When trigger proximity increases, the model's context window receives an injection: *this gun is loaded and pointed.* The system does not predict when it fires; it ensures the reasoning agent never forgets that it will.

**Negative Space — Expectation Violation Signals.** Sculptors speak of negative space as the shape defined by the material's absence. In markets, what *does not* happen is frequently more diagnostic than what does. An asset that refuses to drop on genuinely bad news — when the model predicts a 1.5% decline and the price moves +0.3% instead — reveals underlying accumulation no positive data point can match. Our `NegativeSpaceDetector` computes expectation residuals against calibrated event response templates, flagging reversals when actual behavior consistently contradicts the predicted reaction.

These three frameworks share a common epistemology: they extract signal from *structure, absence, and relationship* rather than from the data itself. They were not chosen for theoretical elegance. They were chosen because they worked — discovered iteratively across trading sessions and training runs where more conventional indicators failed to capture what was happening.

### 5.5 Craft Knowledge: The Variable Nobody Publishes

We now document something no paper on GRPO has published — because it cannot be derived from equations, reproduced from hyperparameter tables, or extracted from ablation studies without first having lived through the failures.

This work is governed by what we call **Iron Rules** (铁律): a set of invariant training constraints that emerged from documented catastrophes across seven iterations. Iron Rule G001 requires logging every reward component's mean and variance at every step. G007 requires saving checkpoints at every step, not every epoch. These rules were not designed in advance. Each exists because we violated its spirit once, and the cost was a collapsed training run.

Across seven rounds, we documented the following interaction effects that standard GRPO literature does not anticipate:

**Finding 1: The Overlong Penalty Paradox.** A reward penalizing long outputs ($r_{overlong}$) and a reward encouraging structured decisions ($r_{format}$) create a negative feedback loop when weighted equally. The model discovers that the safest policy is to output short, substance-free text — minimizing the overlong penalty while accepting the format penalty, as the combined loss is lower than either extreme. Resolution: condition the overlong penalty on decision presence. A long output *containing* a `<DECISION>` block is not pathological. A long output of pure analysis with no decision *is* the failure mode — analytical paralysis, which we treat as distinct from verbosity.

**Finding 2: Statistical Diversity Threshold.** Group size $G=4$ produces zero-gradient training steps when 10 reward functions are active, because 4 completions cannot generate sufficient advantage diversity across 10 dimensions. The model finds a local safe zone where all 4 completions cluster, and the relative advantage signal collapses to noise. $G=8$ resolves this at the cost of doubled generation memory. The lesson: each additional reward function effectively reduces the useful variance of the group — a dynamic absent from theoretical GRPO analysis.

**Finding 3: Temperature Annealing Endpoints.** Cosine annealing from $T=1.2$ to $T=0.8$ causes late-stage mode collapse in multi-reward settings because the advantage distribution becomes excessively concentrated before the model has stabilized its multi-objective policy. An endpoint of $T=1.0$ preserves sufficient exploration throughout. The difference between 0.8 and 1.0 sounds trivial; across 666 training steps, it determines whether the model maintains behavioral diversity or collapses to three high-frequency templates.

**Finding 4: Emergent Reasoning Tags.** At round 7, step 10, the model spontaneously generated `<thinking>` tags before `<DECISION>` blocks. This structure appeared in zero training examples. The behavior was initially suppressed because the overlong penalty penalized the additional tokens. Once identified and protected — by conditioning the penalty on decision presence — the behavior stabilized. We now treat this as a diagnostic marker: when `<thinking>` tags emerge, the model is beginning to introspect rather than merely pattern-match.

We term this accumulated expertise **craft knowledge** — the AI training equivalent of what Polanyi (1966) called tacit knowledge in craft work: *we know more than we can tell.* In the military context, this is doctrine that exists because someone paid for it in failure. Every Iron Rule in our system has a timestamp, a training iteration, and a specific catastrophe that motivated it. A paper can describe the algorithm; it cannot transmit the accumulated discipline of seven rounds of training where a wrong hyperparameter choice at 11 PM means a dead run discovered at 7 AM. The Ouroboros system is named for the snake that eats its own tail — the image of a loop that has learned from previous versions of itself. The craft knowledge *is* the loop.

---

## 6. Preliminary Evidence: Financial World Model

### 6.1 Setup

- **Model**: Qwen3.5-35B-A3B (MoE, 3B active parameters per token)
- **Training**: GRPO with 10 reward functions, progressive activation across 3 phases
- **Hardware**: 2× NVIDIA A800 80GB GPUs
- **Total cost**: Approximately $200 in cloud GPU rental across 7 iterative training rounds — deliberately highlighting that reflexive training does not require large-scale compute
- **Data**: 1,332 multi-regime financial scenarios with episodic decision memory
- **Training flywheel**: Each round incorporates lessons from the previous round's failures, with checkpoint selection, reward function tuning, and curriculum adjustment
- **Reproducibility commitment**: Complete training logs, reward trajectories, Iron Rule documentation, and checkpoint histories will be released as supplementary material with Paper 2

### 6.2 Key Observations

**Observation 1: Reflexive reasoning emerges without explicit supervision.** After seven rounds of iterative GRPO training, the model spontaneously generates text referencing reflexive dynamics, crowding effects, and regime-dependent position adjustment. None of these behaviors were explicitly present in training targets.

**Important caveat on Observation 1.** Our reflexivity-related reward functions detect the *presence* of reflexive reasoning patterns in model outputs. A legitimate concern is whether the model has learned genuine reflexive cognition or merely learned to produce text containing rewarded keywords. We acknowledge this as an open question that requires independent evaluation: human expert assessment with blinded conditions, which we plan for Paper 2. The preliminary evidence reported here is suggestive, not conclusive.

**Observation 2: Generic large models do not spontaneously exhibit reflexive reasoning.** GPT-4o (est. 1.8T parameters), when prompted with identical scenarios under zero-shot conditions, produces competent financial commentary but does not reason about its own market impact, reference crowd dynamics, or adjust for regime-dependent risk. We note that this comparison is between a fine-tuned model and a zero-shot baseline — it demonstrates that reflexive training *adds* capabilities that pretraining alone does not provide, not that scale is irrelevant. A fair comparison against GPT-4o with equivalent few-shot prompting or fine-tuning remains future work.

**Observation 3: Training methodology matters.** Seven iterative rounds, each incorporating 3-5 specific lessons from the previous round's documented failures, produced capabilities not present in the base model. This is consistent with our theoretical argument (§4.2): reflexive capabilities require experiential curriculum.

**Observation 4: Multi-reward interactions produce phase transitions.** We documented 12 specific interaction effects across 7 rounds, including gradient dilution from competing objectives, mode collapse from temperature boundaries, and spontaneous emergence of reasoning structures.

> Full quantitative results, ablation studies (including keyword-only baselines to address the Observation 1 caveat), and benchmark evaluations will appear in a companion paper establishing **ReflexBench** — a benchmark designed for observer-participant capabilities [forthcoming].

---

## 7. Discussion

### 7.1 Implications for Scaling

Our preliminary finding challenges the scaling-first paradigm in a specific, bounded way. We do **not** claim scaling is unhelpful generally — it demonstrably advances observer-invariant capabilities. We claim that scaling laws have a **domain of applicability** that excludes observer-participant environments, where the bottleneck shifts from model capacity to training methodology.

If confirmed at scale, this has significant implications for research democratization: reflexive capabilities may be achievable with commodity hardware, given the right training curriculum. The $200 total cost of our seven-round training program is not a limitation we overcame but a feature we highlight.

### 7.2 Broader Impact and Risks

A reflexive AI that accurately models its market impact could theoretically manipulate markets. We advocate three safeguards:

1. **Human-in-the-loop deployment** until formal safety guarantees exist.
2. **Impact caps**: regulatory limits on single-agent participation scale.
3. **Reflexive auditing**: periodic evaluation of whether deployed systems create the distributional shifts they predict.

### 7.3 Limitations

This paper presents a theoretical framework with preliminary evidence. We identify the following specific limitations:

1. **Single domain.** All empirical evidence is from financial markets. Transfer to other observer-participant domains (policy, recommendation, epidemiology) is hypothesized but untested.
2. **Single architecture.** We report results from one model (Qwen3.5-35B-A3B). Generalization to other base models is unknown.
3. **Evaluation methodology.** Our current evidence relies on reward-function-based evaluation, which risks circularity (§6.2, Observation 1 caveat). Independent human expert evaluation is needed.
4. **Unfair baseline.** The comparison against GPT-4o is zero-shot vs. fine-tuned, which conflates the effect of reflexive training with the effect of any domain-specific training.
5. **Neuroscience claims.** Our brain-structure-to-reward-function mapping is motivational analogy, not biological modeling. We explicitly disclaim homology.
6. **Reproducibility.** The craft knowledge we document is inherently difficult to reproduce from paper description alone. We mitigate this by committing to release complete training artifacts.

These limitations are partially by design — we are establishing theoretical priority and practical feasibility, not claiming a completed research program. Paper 2 will address limitations 3, 4, and 6 directly.

---

## 8. Conclusion

We have argued that a fundamental assumption underlies modern AI's major successes: that the environment is independent of the observer. This **observer-invariance assumption** holds for language modeling, game-playing, and mathematical reasoning, but fails systematically in financial markets, policy-making, social recommendation, and autonomous driving.

We introduced **Reflexive Intelligence** — the capacity to decide while modeling one's own causal impact — as the missing complement to world models. Where JEPA learns how static worlds evolve, Reflexive Intelligence learns how the observer's participation changes that evolution.

Our neuroscience-inspired cognitive architecture and preliminary evidence suggest that reflexive reasoning can emerge from a well-designed training curriculum in a model with 3B active parameters, at a cost accessible to individual researchers. The bottleneck is not compute. It is not parameters. It is the accumulated craft knowledge required to navigate the interaction space of multi-reward reinforcement learning — and the willingness to document seven rounds of failure before finding what works.

There is a Chinese aphorism attributed to Laozi: *治大国如烹小鲜* — "Governing a great nation is like cooking a small fish." The principle is restraint: a small fish falls apart if you handle it too much. Complex systems, whether economies, ecosystems, or multi-reward training processes, resist aggressive intervention. The temptation is always to add another reward function, increase another weight, fix another failure with another patch. The discipline is knowing when to hold still.

The Ouroboros is the ancient symbol of a serpent consuming its own tail — a closed loop, a system that feeds on and regenerates from its previous iterations. We chose the name deliberately. Each training round ingests the failures of the previous one. The craft knowledge accumulates. The model becomes, gradually, something that understands its own participation in the world it is trying to model.

This is what Reflexive Intelligence is, ultimately: not a technique but a stance. The observer is part of what is observed. It is time our AI systems learned this — not as a theorem, but as a trained instinct.

> *"The observer is part of what is observed."* — Werner Heisenberg
>
> It is time our AI systems learned this too.

---

## Acknowledgments

The author used large language models for editing assistance, literature search, and prose organization during the preparation of this manuscript. All theoretical contributions, experimental design, system architecture, training execution, and craft knowledge documentation reflect the author's original work.

---

## References

Assran, M. et al. (2023). Self-Supervised Learning from Images with a Joint-Embedding Predictive Architecture. *CVPR*.

Campbell, D. T. (1979). Assessing the Impact of Planned Social Change. *Evaluation and Program Planning*, 2(1).

Damasio, A. (1994). *Descartes' Error: Emotion, Reason, and the Human Brain*. Putnam.

Goodhart, C. (1975). Problems of Monetary Management: The UK Experience. *Papers in Monetary Economics*, Reserve Bank of Australia.

Hardt, M. et al. (2016). Strategic Classification. *ITCS*.

Guo, D. et al. (2025). DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning. arXiv:2501.12948.

Ha, D. & Schmidhuber, J. (2018). World Models. arXiv:1803.10122.

Hafner, D. et al. (2020). Dream to Control: Learning Behaviors by Latent Imagination. *ICLR*.

Hoffmann, J. et al. (2022). Training Compute-Optimal Large Language Models. *NeurIPS*.

Kaplan, J. et al. (2020). Scaling Laws for Neural Language Models. arXiv:2001.08361.

LeCun, Y. (2022). A Path Towards Autonomous Machine Intelligence. *OpenReview*.

Li, B. et al. (2024). Large World Model. arXiv.

Lucas, R. (1976). Econometric Policy Evaluation: A Critique. *Carnegie-Rochester Conference Series on Public Policy*.

Mendler-Dünner, C. et al. (2020). Stochastic Optimization for Performative Prediction. *NeurIPS*.

Perdomo, J. C. et al. (2020). Performative Prediction. *ICML*.

Polanyi, M. (1966). *The Tacit Dimension*. University of Chicago Press.

Shao, Z. et al. (2024). DeepSeekMath: Pushing the Limits of Mathematical Reasoning in Open Language Models. arXiv:2402.03300.

Soros, G. (1987). *The Alchemy of Finance*. Simon & Schuster.

Wu, S. et al. (2023). BloombergGPT: A Large Language Model for Finance. arXiv:2303.17564.

Xiang, J. et al. (2024). Language Models Meet World Models. *NeurIPS*.

Yang, H. et al. (2023). FinGPT: Open-Source Financial Large Language Models. arXiv:2306.06031.

Yu, Q. et al. (2025). DAPO: An Open-Source LLM Reinforcement Learning System. arXiv.

---

> **Note**: This is Paper 1 of 3 in the Reflexive Intelligence series.
> - **Paper 1** (this paper): Theoretical framework — arXiv
> - **Paper 2**: ReflexBench — benchmark and empirical results — ICAIF 2026
> - **Paper 3**: Ouroboros-Lite — simplified community implementation — NeurIPS Workshop 2026
