# Reviewer X8tH (Score: 4, Weak Accept)

We thank the reviewer for the positive assessment. We address each concern below, highlighting existing theoretical analysis and supplementing with new experiments (full tables in [Supplementary](LINK)).

**W1/Q1: Theoretical grounding and reward stability**

We would like to highlight that **Proposition 3 (Appendix B.2)** already provides formal analysis with four sub-proofs: (a) **sign separation** (Eqs. 45–48) — non-feasible trajectories receive $`R(\tau) \in [-1, 0)`$, feasible ones $`R(\tau) \geq 0`$; (b) **two-stage optimization** (Eqs. 49–56) — conditional release provably shifts probability mass toward feasibility, with $`p_\theta`$ monotonically increasing (Eq. 55); (c) **bounded updates** (Eqs. 57–60) — clipping + KL jointly bound per-step policy change; (d) **token mask variance reduction** (Eqs. 61–66) — unbiased estimates with $`\text{Var}(\text{mask}) < \text{Var}(\text{no-mask})`$.

Empirically, our new **Table R1** ablation isolates each component:

| Variant | IID Avg. | OOD Avg. |
|---|---|---|
| CWRPO (Full) | 85.15 | 59.59 |
| w/o Token Masking | 79.82 (−5.34) | 52.06 (−7.52) |
| w/o Diversity Reward | 80.08 (−5.08) | 50.30 (−9.28) |
| w/o Cond. Release | 80.86 (−4.30) | 52.85 (−6.74) |

Regarding reward sensitivity, **Table R5** varies hyperparameters across 5 configurations while keeping all mechanisms active: equal weighting (0.25×4) achieves IID 82.29 / OOD 55.58; checker-heavy (0.4/0.2/0.2/0.2) IID 82.94 / OOD 54.41; min_ops ∈ {3, 4, 7} yields IID 80.73–81.51 / OOD 52.29–53.92 — all show gradual degradation (IID −2.21 to −4.42, OOD −4.01 to −7.30) vs. default IID 85.15 / OOD 59.59, confirming robustness to reward shaping choices.

**W2/Q2: Ablation granularity — disentangling contributions**

Our paper provides **three complementary ablation levels** that jointly disentangle the contributions the reviewer asks about:

(1) **Framework-level** (Table 5): w/o Multi-turn (IID −5.15), w/o Canvas (IID −4.32), w/o RL (IID −4.81), w/o Agent (IID −3.32). This isolates the interactive multi-turn formulation.

(2) **RL algorithm-level** (Table 6): CWRPO vs. GRPO vs. DAPO under identical multi-turn settings. CWRPO outperforms on all 6 IID benchmarks (e.g., MATH 81.25 vs. 73.43, SQuAD v2 EM 78.12 vs. 61.72). Additionally, PPO-based baselines in Table 3 (AgentFlow, Orchestrator) further confirm CWRPO's advantage over standard policy optimization — these use PPO with the same backend but achieve lower performance across all benchmarks.

(3) **CWRPO component-level** (Table R1 above): Isolates diversity constraints, conditional release, and token masking individually.

Crossing levels (1) and (3) directly answers Q2: the multi-turn formulation contributes IID −5.15 (Table 5), while the CWRPO-specific mechanisms contribute −4.30 to −5.34 each (Table R1). Both are substantial and complementary — multi-turn provides iterative refinement capability, while CWRPO provides the learning signal to use it effectively.

**W3/Q3: Positioning relative to RL-based agentic methods**

**Appendix E, Table 12** provides a six-dimensional architectural comparison; FlowSteer is the only method satisfying all six dimensions. The key distinctions: AgentFlow uses PPO but with a fixed 4-module pipeline and no per-task prompt customization; Router-R1 routes to fixed templates with limited feedback adaptation; Orchestrator selects operators via PPO but lacks two-step interaction (add + set_prompt) for fine-grained configuration.

FlowSteer uniquely *dynamically edits the workflow graph* (Definition 1) with per-task prompt customization. Proposition 2 (Appendix B.1) proves this multi-turn interaction possesses the monotonic improvement property — each turn cannot decrease expected accuracy, which iterative refinement without structured feedback cannot guarantee.

**W4/Q4: Scalability and computational cost**

**Appendix C** analyzes complexity: $`O(NT)`$ training, $`O(T)`$ inference. The multi-turn cost is mitigated by vectorized rollout (**Appendix G**): 32-way concurrent trajectory interaction, batched API calls, cached workflow states, and early-finish skipping.

Crucially, FlowSteer trains once and deploys zero-shot to 6 backends (Figure 4), amortizing training cost across backends. **Table R6** confirms task-proportional inference cost: GSM8K averages 8.3 turns $`/`$ \$0.0012 vs. AIME 12.4 turns $`/`$ \$0.0019, with Spearman ρ=0.65 (p=0.021) between difficulty and complexity.

**Q5: Cross-backend generalization**

**Figure 4 / Section 5.3 (RQ3)** evaluates exactly this: 6 architecturally diverse backends (DeepSeek-V3, Grok-4-Fast, GPT-5, Claude-Opus-4.5, Gemini-2.5-Flash, Qwen-Plus). FlowSteer yields consistent improvements across all backends on both IID and OOD benchmarks. Additionally, **Tables R2–R4** show operator-level transfer (removal/substitution/addition) with graceful degradation or selective improvement, further confirming generalizability is semantic rather than hard-coded.
