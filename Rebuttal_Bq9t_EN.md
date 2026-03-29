# Reviewer Bq9t (Score: 3, Weak Reject)

We thank the reviewer for recognizing our operator library and diversity-constrained reward. We address each concern below (full data in [Supplementary](https://anonymous.4open.science/r/Supplementary)).

**W1: Design differences from existing methods**

We clarify via the following comparison:

| | Orchestration | Workflow | Prompt | Training |
|---|---|---|---|---|
| AFlow | MCTS search | Code-searched | In code | No RL |
| Orchestrator | Hierarchical | Fixed routing | Fixed | Multi-obj RL |
| AgentFlow | 4-module pipe | Fixed pipeline | Fixed | Flow-GRPO |
| RouterR1 | LLM routing | Fixed linear | Fixed | RL |
| **FlowSteer** | **Canvas interact.** | **Dynamic editing** | **Per-task** | **CWRPO** |

FlowSteer is the only method that *dynamically edits the workflow graph (Definition 1) with per-task prompt customization*. The policy submits atomic editing actions per turn (add/delete/modify/set_prompt/control structures, Eq. 6); the Canvas returns structured feedback (Eqs. 7–8) for iterative refinement (Section 4.2). All baselines either route to fixed tools, generate monolithic code, or use fixed pipelines with fixed prompts. Table 5 quantifies the contribution: removing multi-turn interaction causes IID −5.15; removing Canvas feedback causes the largest OOD drop.

**W2: Plug-and-play is not "pure engineering"**

The plug-and-play capability stems from a *factored action space* (Section 4.1, Proposition 1) that decouples action types from operators, yielding $`O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)`$ vs. naive $`O(\lvert\mathcal{A}_{\text{type}}\rvert \times \lvert\mathcal{O}\rvert)`$. This is a *design-level* contribution with theoretical grounding, not merely an engineering wrapper.

New transfer experiments (supplementary **Tables R2–R4**) validate this: *Removal*: IID 76.95 / OOD 50.30 with only 4 core operators — graceful degradation. *Substitution* (Programmer→Jupyter, Custom→Generate with Skills): avg. IID 85.42 (+0.3) / OOD 59.69 (+0.1). *Addition* (unseen Search, Calculator, Debugger): selective target improvement (+5.5–8.6 EM on QA, +2.4–3.9 on code) with zero non-target degradation.

**W3/Q1: CWRPO vs. GRPO — three objective-level differences**

CWRPO differs from GRPO in three ways *at the objective level*, not merely in the reward:

**(1) Token-level mask** (Eq. 10): A binary mask $`\text{mask}_t`$ inside the policy gradient computes loss only on policy-generated tokens. GRPO computes loss on all tokens including environment feedback. Proposition 6c proves unbiased estimates with strictly lower variance. Ablation: removing it drops MATH −13.28.

**(2) Conditional release with sign separation** (Eq. 14): $`\mathbb{I}\{R_{diversity}=1.0\}`$ creates sign-separated rewards — feasible $`R(\tau) \geq 0`$, non-feasible $`R(\tau) < 0`$. This enables provable two-stage optimization (Proposition 6b): structure first, then answer quality. GRPO has no such curriculum. Ablation: removing it drops TriviaQA EM −9.38.

**(3) Source-partitioned advantage normalization**: Advantages use within-source $`(\mu_{src}, \sigma_{src})`$ to prevent cross-task advantage collapse. GRPO uses global batch statistics.

Table 6 confirms: CWRPO outperforms GRPO on all 6 IID benchmarks (GSM8K 96.09 vs. 92.97, MATH 81.25 vs. 73.43).

**W4: Reward component ablation**

New **Table R1** isolates each component independently:

| Variant | IID Avg. | OOD Avg. |
|---|---|---|
| CWRPO (Full) | 85.15 | 59.59 |
| w/o Token Masking | 79.82 (−5.34) | 52.06 (−7.52) |
| w/o Diversity Reward | 80.08 (−5.08) | 50.30 (−9.28) |
| w/o Cond. Release | 80.86 (−4.30) | 52.85 (−6.74) |

The three components address orthogonal challenges: **Token Masking** — credit assignment (MATH −13.28); **Diversity Reward** — mode collapse (AIME −10.00, DS-1000 −12.50); **Conditional Release** — premature convergence (TriviaQA −9.38). These failures are non-compensating, confirming CWRPO is a minimal complete design. With Table 5 (framework) and Table 6 (algorithm), this forms three complementary ablation levels.
