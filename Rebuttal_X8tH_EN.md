We thank the reviewer for the positive assessment. We address each concern with existing theory and new experiments (full tables in https://anonymous.4open.science/r/Supplementary).

**W1/Q1: Theoretical grounding and reward stability** (New: Tables R1, R5 | Paper: Prop. 3)
**Proposition 3 (Appendix B.2)** provides formal analysis with four sub-proofs: (a) **sign separation** (Eqs. 45–48); (b) **two-stage optimization** (Eqs. 49–56) — conditional release shifts mass toward feasibility; (c) **bounded updates** (Eqs. 57–60); (d) **token mask variance reduction** (Eqs. 61–66) — unbiased with $\\text{Var}(\\text{mask}) < \\text{Var}(\\text{no-mask})$.

Empirically, new **Table R1** ablation isolates each component:

| Variant | IID Avg. | OOD Avg. |
|:---:|:---:|:---:|
| CWRPO (Full) | 85.15 | 59.59 |
| w/o Token Masking | 79.82 (−5.34) | 52.06 (−7.52) |
| w/o Diversity Reward | 80.08 (−5.08) | 50.30 (−9.28) |
| w/o Cond. Release | 80.86 (−4.30) | 52.85 (−6.74) |

These drops validate theoretical predictions: token masking's variance reduction (Prop. 3d) is critical for math requiring clean gradients (MATH −13.28); conditional release (Prop. 3b) prevents premature convergence on long-chain QA (TriviaQA −9.38); diversity reward prevents mode collapse with the largest OOD impact (−9.28). The three failure modes are orthogonal, confirming CWRPO is a minimal complete design.

**The reward design is robust to hyperparameter variation.** New **Table R5** tests 5 configurations while keeping all mechanisms active: equal weighting (0.25×4) achieves IID 82.29 / OOD 55.58; checker-heavy (0.4/0.2/0.2/0.2) IID 82.94 / OOD 54.41; min_ops ∈ {3, 4, 7} yields IID 80.73–81.51 / OOD 52.29–53.92 — all show gradual degradation (IID −2.21 to −4.42, OOD −4.01 to −7.30) vs. default IID 85.15 / OOD 59.59, confirming robustness to reward shaping choices.

**W2/Q2: Ablation granularity — disentangling contributions** (Paper: Tables 5, 6 | New: Table R1)
We designed **three complementary ablation levels** to disentangle each factor's contribution:

(1) **Framework-level** (Table 5): w/o Multi-turn (IID −5.15), w/o Canvas (IID −4.32), w/o RL (IID −4.81), w/o Agent (IID −3.32). This isolates the interactive multi-turn formulation.

(2) **RL algorithm-level** (Table 6): CWRPO vs. GRPO vs. DAPO under identical multi-turn settings. CWRPO leads on all 6 IID benchmarks (e.g., MATH 81.25 vs. 73.43, SQuAD v2 EM 78.12 vs. 61.72). PPO-based baselines in Table 3 (AgentFlow, Orchestrator) further confirm CWRPO's advantage over standard policy optimization.

(3) **CWRPO component-level** (Table R1 above): Isolates diversity constraints, conditional release, and token masking individually.

Crossing levels (1) and (3) directly answers Q2: the multi-turn formulation contributes IID −5.15 (Table 5), while CWRPO-specific mechanisms contribute −4.30 to −5.34 each (Table R1). Both are substantial and complementary — multi-turn provides the iterative refinement capability, while CWRPO provides the learning signal to exploit it effectively.

**W3/Q3: Positioning relative to RL-based agentic methods** (Paper: Appendix E, Table 12)
**Appendix E, Table 12** provides a six-dimensional comparison; FlowSteer uniquely satisfies all six. Key distinctions: AgentFlow uses PPO but with a fixed 4-module pipeline and no per-task prompt customization; Router-R1 routes to fixed templates; Orchestrator selects operators but lacks two-step interaction (add + set_prompt) for fine-grained configuration.

FlowSteer uniquely *dynamically edits the workflow graph* (Definition 1) with per-task prompt customization. Proposition 2 (Appendix B.1) proves this possesses the monotonic improvement property — each turn cannot decrease expected accuracy.

**W4/Q4: Scalability and computational cost** (New: Table R6 | Paper: Appendix C, G)
**FlowSteer's multi-turn overhead is manageable.** Appendix C shows $O(NT)$ training and $O(T)$ inference complexity, mitigated by vectorized rollout (Appendix G): 32-way concurrent trajectories, batched API calls, cached states, and early-finish skipping.

FlowSteer trains once and deploys zero-shot to 6 or more backends (Figure 4) without per-backend tuning. New **Table R6** confirms task-proportional inference: GSM8K averages 8.3 turns / \$0.0012 vs. AIME 12.4 turns / \$0.0019 (Spearman ρ=0.65, p=0.021).

**Q5: Cross-backend generalization** (Paper: Figure 4 | New: Tables R2–R4)
**Figure 4 / Section 5.3 (RQ3)** evaluates exactly this across 6 architecturally diverse backends (DeepSeek-V3, Grok-4-Fast, GPT-5, Claude-Opus-4.5, Gemini-2.5-Flash, Qwen-Plus). FlowSteer yields consistent IID and OOD improvements on all backends. New **Tables R2–R4** further show operator-level transfer (removal/substitution/addition) with graceful degradation or selective improvement, confirming generalizability is semantic rather than hard-coded.
