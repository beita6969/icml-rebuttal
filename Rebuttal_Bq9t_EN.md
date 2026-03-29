# Reviewer Bq9t (Score: 3, Weak Reject)

We thank the reviewer for recognizing our operator library and diversity-constrained reward. We address each concern below (full tables in [Supplementary](LINK)).

**W1: Design differences from existing methods**

We clarify the key architectural distinctions via the following comparison:

| | Orchestration | Workflow | Prompt | Training |
|---|---|---|---|---|
| AFlow | MCTS search | Code-searched | In code | No RL |
| Orchestrator | Hierarchical | Fixed routing | Fixed | Multi-obj RL |
| AgentFlow | 4-module pipe | Fixed pipeline | Fixed | Flow-GRPO |
| RouterR1 | LLM routing | Fixed linear | Fixed | RL |
| **FlowSteer** | **Canvas interact.** | **Dynamic editing** | **Per-task** | **CWRPO** |

FlowSteer is the only method that *dynamically edits the workflow graph (Definition 1) with per-task prompt customization through multi-turn interaction*. The policy submits atomic editing actions per turn (add/delete/modify/set_prompt/control structures, Eq. 6); the Canvas executes and returns structured feedback (Eqs. 7–8) for iterative "diagnose-edit-verify" refinement (Section 4.2). All baselines either route to fixed tools, generate monolithic code, or use fixed pipelines with fixed prompts. Table 5 quantifies the contribution: removing multi-turn interaction causes IID −5.15; removing Canvas feedback causes the largest OOD drop.

**W2: Plug-and-play is not "pure engineering"**

The plug-and-play capability is enabled by a *factored action space* (Section 4.1) that decouples action types from operators (Proposition 1):

$$O(|\mathcal{A}_{\text{type}}| + |\mathcal{O}|) \quad\text{vs. the naive}\quad O(|\mathcal{A}_{\text{type}}| \times |\mathcal{O}|)$$

This is a *design-level* contribution with theoretical grounding, not merely an engineering wrapper. Flow-Director selects operators via semantic descriptions (Table 7), not hard-coded indices.

We validate this with new transfer experiments (**Tables R2–R4**): *Removal* (4 core operators only): IID 76.95 / OOD 50.30, graceful degradation. *Substitution* (Programmer→Jupyter, Custom→Generate with Skills): avg. IID 85.42 (+0.3) / OOD 59.69 (+0.1), near-lossless. *Addition* (unseen Search, Calculator, Debugger): selective target-task improvement (+5.5–8.6 EM on QA, +2.4–3.9 on code) with zero non-target degradation. No prior work has demonstrated this level of operator-level transfer.

**W3/Q1: CWRPO vs. GRPO — three objective-level differences**

We respectfully clarify that CWRPO differs from GRPO in three ways *at the objective level*, not merely in the reward function:

**(1) Token-level mask in the objective** (Eq. 10): A binary mask $\text{mask}_t \in \{0,1\}$ is applied *inside the policy gradient*, computing loss only on policy-generated tokens. GRPO computes loss on all tokens including environment feedback. This changes *which tokens receive gradient signal*. Proposition 6c proves this yields unbiased estimates with strictly lower variance (Eqs. 62–64). Ablation confirms: removing it drops MATH by −13.28 and MathQA by −10.55.

**(2) Conditional release with sign separation** (Eq. 14): The indicator $\mathbb{I}\{R_{diversity}=1.0\}$ creates sign-separated rewards — feasible trajectories get $R(\tau) \geq 0$ (Eq. 48), non-feasible get $R(\tau) < 0$ (Eq. 46). This enables provable two-stage optimization (Proposition 6b): structure first, then answer quality. GRPO has no such curriculum mechanism. Ablation: removing it drops TriviaQA EM by −9.38 and NQ EM by −7.81.

**(3) Source-partitioned advantage normalization**: Advantages use within-source statistics $(\mu_{src}, \sigma_{src})$ partitioned by data source, preventing cross-task advantage collapse in multi-task training. Standard GRPO uses global batch statistics.

Table 6 confirms: CWRPO outperforms GRPO on all 6 IID benchmarks (GSM8K 96.09 vs. 92.97, MATH 81.25 vs. 73.43, HotPotQA 78.12 vs. 72.66).

**W4: Reward component ablation**

We have completed this ablation (**Table R1**), removing each component independently:

| Variant | IID Avg. | Δ | OOD Avg. | Δ |
|---|---|---|---|---|
| CWRPO (Full) | 85.15 | — | 59.59 | — |
| w/o Token Masking | 79.82 | −5.34 | 52.06 | −7.52 |
| w/o Diversity Reward | 80.08 | −5.08 | 50.30 | −9.28 |
| w/o Cond. Release | 80.86 | −4.30 | 52.85 | −6.74 |

Each component is essential with a distinct role: token masking stabilizes math (MATH −13.28); diversity reward ensures OOD generalization (AIME −10.00, DS-1000 −12.50); conditional release suppresses QA shortcuts (TriviaQA EM −9.38). With Table 5 (framework) and Table 6 (algorithm), this forms three complementary ablation levels.
