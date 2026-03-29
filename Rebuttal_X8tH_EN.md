# Reviewer X8tH (Score: 4, Weak Accept)

We thank the reviewer for the positive assessment. We address each concern below, highlighting existing theoretical analysis and supplementing with new experiments (full data in [Supplementary](https://anonymous.4open.science/r/Supplementary)).

**W1/Q1: Theoretical grounding and reward stability**

**Proposition 3 (Appendix B.2)** already provides formal convergence analysis: (a) **sign separation** — non-feasible trajectories $`R(\tau)<0`$, feasible ones $`R(\tau) \geq 0`$; (b) **two-stage optimization** — conditional release provably shifts probability mass toward feasibility with $`p_\theta`$ monotonically increasing; (c) **bounded updates** — clipping + KL jointly bound per-step policy change; (d) **token mask variance reduction** — unbiased estimates with $`\text{Var}(\text{mask}) < \text{Var}(\text{no-mask})`$.

New **Table R1** empirically validates each component:

| Variant | IID Avg. | OOD Avg. |
|---|---|---|
| CWRPO (Full) | 85.15 | 59.59 |
| w/o Token Masking | 79.82 (−5.34) | 52.06 (−7.52) |
| w/o Diversity Reward | 80.08 (−5.08) | 50.30 (−9.28) |
| w/o Cond. Release | 80.86 (−4.30) | 52.85 (−6.74) |

These drops validate the theory: token masking's variance reduction (Prop. 3d) is critical for math requiring clean gradients (MATH −13.28); conditional release's two-stage optimization (Prop. 3b) prevents premature convergence on long-chain QA (TriviaQA −9.38); diversity reward prevents mode collapse with the largest OOD impact (−9.28). The three failure modes are orthogonal, confirming CWRPO is a minimal complete design.

**The reward is robust to hyperparameter variation.** New Table R5 tests 5 configurations: equal weighting (0.25×4) achieves IID 82.29 / OOD 55.58; checker-heavy (0.4/0.2/0.2/0.2) IID 82.94 / OOD 54.41; min_ops ∈ {3, 4, 7} yields IID 80.73–81.51 / OOD 52.29–53.92 — all gradual degradation (IID −2.21 to −4.42, OOD −4.01 to −7.30) vs. default IID 85.15 / OOD 59.59.

**W2/Q2: Ablation granularity — disentangling contributions**

We designed three complementary ablation levels to disentangle each factor:

(1) **Framework-level** (Table 5): w/o Multi-turn (IID −5.15), w/o Canvas (IID −4.32), w/o RL (IID −4.81), w/o Agent (IID −3.32) — isolating the interactive multi-turn formulation.

(2) **RL algorithm-level** (Table 6): CWRPO vs. GRPO vs. DAPO under identical settings. CWRPO leads on all 6 IID benchmarks (MATH 81.25 vs. 73.43, SQuAD v2 78.12 vs. 61.72). PPO-based baselines in Table 3 (AgentFlow, Orchestrator) further confirm CWRPO's advantage.

(3) **CWRPO component-level** (Table R1 above): Isolates diversity reward, conditional release, and token masking individually.

Crossing levels (1) and (3) directly answers Q2: multi-turn contributes IID −5.15 (Table 5), CWRPO mechanisms contribute −4.30 to −5.34 each (Table R1). Both are substantial and complementary — multi-turn provides iterative refinement, while CWRPO provides the learning signal to exploit it.

**W3/Q3: Positioning relative to RL-based agentic methods**

**Appendix E Table 12** compares architectures across six dimensions; FlowSteer is the only method satisfying all six. Core distinctions: AgentFlow uses PPO but with a fixed 4-module pipeline and no per-task prompts; Router-R1 routes to fixed templates with limited feedback; Orchestrator selects operators via PPO but lacks two-step interaction (add + set_prompt) for fine-grained configuration.

FlowSteer uniquely *dynamically edits the workflow graph* (Definition 1) with per-task prompt customization. Proposition 2 (Appendix B.1) proves this interaction has the monotonic improvement property — each turn cannot decrease expected accuracy, a guarantee absent from iterative refinement without structured feedback.

**W4/Q4: Scalability and computational cost**

**FlowSteer's multi-turn overhead is manageable.** Appendix C: $`O(NT)`$ training, $`O(T)`$ inference. Vectorized rollout (Appendix G) mitigates multi-turn cost: 32-way concurrency, batched API calls, cached states, early-finish skipping. FlowSteer trains once (~8 GPU-hrs, 2×A100) and deploys zero-shot to 6 backends (Figure 4), amortizing to ~1.3 GPU-hrs/backend. New **Table R6** confirms task-proportional cost: GSM8K 8.3 turns / \$0.0012 vs. AIME 12.4 turns / \$0.0019 (Spearman ρ=0.65, p=0.021).

**Q5: Cross-backend generalization**

**Figure 4 / Section 5.3** evaluates 6 diverse backends (DeepSeek-V3, Grok-4-Fast, GPT-5, Claude-Opus-4.5, Gemini-2.5-Flash, Qwen-Plus) with consistent improvements on all IID and OOD benchmarks. New **Tables R2–R4** further show operator-level transfer: removal causes task-specific degradation, substitution is near-lossless, and addition selectively improves targets — confirming generalizability is semantic, not hard-coded.
