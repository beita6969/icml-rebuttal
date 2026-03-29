We thank the reviewer for recognizing our operator library and diversity-constrained reward. We address each concern below (full tables in https://anonymous.4open.science/r/Supplementary).

> **W1: Design differences from existing methods** (Paper: Table 5, Def. 1)
The following comparison clarifies the key architectural distinctions:

| | Orchestration | Workflow | Prompt | Training |
|:---:|:---:|:---:|:---:|:---:|
| AFlow | MCTS search | Code-searched | In code | No RL |
| Orchestrator | Hierarchical | Fixed routing | Fixed | Multi-obj RL |
| AgentFlow | 4-module pipe | Fixed pipeline | Fixed | Flow-GRPO |
| RouterR1 | LLM routing | Fixed linear | Fixed | RL |
| **FlowSteer** | **Canvas interact.** | **Dynamic editing** | **Per-task** | **CWRPO** |

FlowSteer is the only method that *dynamically edits the workflow graph (Definition 1) with per-task prompt customization*. The policy submits atomic editing actions per turn (add/delete/modify/set_prompt/control structures, Eq. 6); the Canvas returns structured feedback (Eqs. 7–8) for iterative refinement (Section 4.2). All baselines either route to fixed tools, generate monolithic code, or use fixed pipelines. Table 5 quantifies: removing multi-turn causes IID −5.15; removing Canvas causes the largest OOD drop.

> **W2: Plug-and-play is not "pure engineering"** (New: Tables R2–R4 | Paper: Prop. 1, Table 7)
The plug-and-play capability is enabled by a *factored action space* (Section 4.1) that decouples action types from operators (Proposition 1), yielding $O(\\lvert\\mathcal{A}\_{\\text{type}}\\rvert + \\lvert\\mathcal{O}\\rvert)$ vs. the naive $O(\\lvert\\mathcal{A}\_{\\text{type}}\\rvert \\times \\lvert\\mathcal{O}\\rvert)$ complexity. This is a *design-level* contribution with theoretical grounding, not merely an engineering wrapper. Flow-Director selects operators via semantic descriptions (Table 7), not hard-coded indices.

We validate this with new transfer experiments (supplementary **Tables R2–R4**): *Removal* (4 core operators only): IID 76.95 / OOD 50.30, graceful degradation. *Substitution* (Programmer→Jupyter): avg. IID 85.42 (+0.3) / OOD 59.69 (+0.1), near-lossless. *Addition* (unseen Search, Debugger): selective improvement (+5.5–8.6 EM on QA, +2.4–3.9 on code) with zero non-target degradation.

> **W3/Q1: CWRPO vs. GRPO — three objective-level differences** (Paper: Table 6, Prop. 6)
CWRPO differs from GRPO in three ways *at the objective level*, not merely in the reward function:

** (1) Token-level mask in the objective** (Eq. 10): A binary mask $\\text{mask}\_t \\in \\{0,1\\}$ applied *inside the policy gradient* computes loss only on policy-generated tokens, while GRPO includes environment feedback. Proposition 6c proves unbiased estimates with strictly lower variance. Ablation: removing it drops MATH by −13.28 and MathQA by −10.55.

** (2) Conditional release with sign separation** (Eq. 14): The indicator $\\mathbb{I}\\{R\_{diversity}=1.0\\}$ creates sign-separated rewards — feasible $R(\\tau) \\geq 0$, non-feasible $R(\\tau) < 0$, enabling provable two-stage optimization (Proposition 6b): structure first, then quality. GRPO has no curriculum. Ablation: TriviaQA EM −9.38, NQ EM −7.81.

** (3) Source-partitioned advantage normalization**: Advantages use within-source statistics $(\\mu\_{src}, \\sigma\_{src})$, preventing cross-task advantage collapse. GRPO uses global batch statistics.

Table 6 confirms the combined effect: CWRPO outperforms GRPO on all 6 IID benchmarks (GSM8K 96.09 vs. 92.97, MATH 81.25 vs. 73.43, HotPotQA 78.12 vs. 72.66).

> **W4: Reward component ablation** (New: Table R1 | Paper: Tables 5, 6)
We have completed this ablation (new **Table R1**), removing each component independently:

| Variant | IID Avg. | Δ | OOD Avg. | Δ |
|:---:|:---:|:---:|:---:|:---:|
| CWRPO (Full) | 85.15 | — | 59.59 | — |
| w/o Token Masking | 79.82 | −5.34 | 52.06 | −7.52 |
| w/o Diversity Reward | 80.08 | −5.08 | 50.30 | −9.28 |
| w/o Cond. Release | 80.86 | −4.30 | 52.85 | −6.74 |

The three components address orthogonal challenges and none is redundant: **Token Masking** solves multi-turn credit assignment — without it, environment feedback tokens corrupt gradients, destabilizing math reasoning most (MATH −13.28); **Diversity Reward** prevents structural mode collapse — without it, the policy converges to shortcut workflows that fail on unseen distributions (AIME −10.00, DS-1000 −12.50); **Conditional Release** prevents premature convergence — without the curriculum gating, the policy terminates early, uniquely degrading open-domain QA where longer chains are needed (TriviaQA EM −9.38). These failures are orthogonal (different task categories, non-compensating), demonstrating CWRPO is a minimal complete design. With Table 5 (framework) and Table 6 (algorithm), this forms three complementary ablation levels.
