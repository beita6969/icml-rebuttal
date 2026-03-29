# Reviewer LqWU (Score: 4, Weak Accept)

We thank the reviewer for recognizing our problem framing and broad evaluation. We address each concern with new experiments (full tables in [Supplementary](https://anonymous.4open.science/r/Supplementary)).

**W1/Q1: Component-level ablation of CWRPO** (New: Table R1 | Paper: Tables 5, 6)

We have completed this ablation across all 12 benchmarks, removing each component independently under identical training (new **Table R1**):

| Variant | IID Avg. | Δ | OOD Avg. | Δ |
|---|---|---|---|---|
| CWRPO (Full) | 85.15 | — | 59.59 | — |
| w/o Token Masking | 79.82 | −5.34 | 52.06 | −7.52 |
| w/o Diversity Reward | 80.08 | −5.08 | 50.30 | −9.28 |
| w/o Cond. Release | 80.86 | −4.30 | 52.85 | −6.74 |

Each component addresses a distinct challenge: **(1) Token Masking** solves multi-turn credit assignment — environment feedback tokens corrupt gradients, destabilizing math most (MATH −13.28, MathQA −10.55). **(2) Diversity Reward** prevents structural mode collapse — the policy converges to shortcut workflows that fail on unseen distributions (largest OOD drop −9.28; AIME −10.00, DS-1000 −12.50, APPS −11.71). **(3) Conditional Release** prevents premature convergence — without curriculum gating, the policy terminates early, degrading open-domain QA (TriviaQA EM −9.38, NQ EM −7.81). The three failure modes are orthogonal (different task categories, non-compensating), confirming CWRPO is a minimal complete design. Together with Table 5 (framework-level) and Table 6 (RL algorithm-level), this establishes a three-layer ablation hierarchy.

**W2/Q2: Operator library transfer** (New: Tables R2–R4 | Paper: Table 7, Prop. 1)

We conduct three tiers of transfer experiments (new **Tables R2–R4**), the first systematic operator-level transfer evaluation in this area.

*Removal* (new **Table R2**): Removing operator groups at test time without retraining causes task-specific degradation: −Verify/Test impacts code most (APPS 49.21→35.16, DS-1000 58.59→38.28), −Review/Revise impacts QA (HotPotQA EM −7.03), −ScEnsemble/Aggregate impacts hard problems (AIME 26.67→20.00). This confirms surjective coverage over the 7 cognitive primitives (Proposition 4). Even a minimal 4-operator configuration achieves IID 76.95 / OOD 50.30 — graceful degradation, demonstrating robustness of the learned orchestration policy.

*Substitution & Addition* (new **Tables R3–R4**): Replacing operators with unseen alternatives (Programmer→Jupyter Kernel, Custom→Generate with Skills) yields near-lossless avg. IID 85.42 (+0.3) / OOD 59.69 (+0.1). Adding entirely new operators unseen during training shows selective improvement — +Search: TriviaQA/NQ +5.47/+8.59 EM; +Debugger: APPS/DS-1000 +2.35/+3.91 — with zero non-target degradation. Both stem from the factored action space (Proposition 1): $`O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)`$ complexity. Flow-Director selects operators via semantic descriptions (Table 7), enabling zero-shot transfer to unseen implementations.

**W3/Q3: Structural prior sensitivity** (New: Tables R5, R6 | Paper: Prop. 4, Figure 5)

**The structural constraints are theoretically necessary, not heuristic.** Proposition 4 (Appendix B.1) identifies 7 cognitive primitives any complete workflow must cover: Generation, Verification, Aggregation, Conditional branching, Sequential chaining, Ensemble, and Formatting. We prove via surjective coverage (Eqs. 19–25) that each primitive requires at least one operator, and since certain primitives (e.g., Verification vs. Generation) need functionally distinct implementations, the theoretical minimum is 5 operators.

**Experiments confirm min_ops=5 is the practical optimum.** New **Table R5** (Part A) retrains with min_ops ∈ {3, 4, 5, 7}: below the minimum (3–4) creates coverage gaps (IID 80.73–81.51, OOD 52.29–52.32); above it (7) over-constrains without benefit (IID 80.99, OOD 53.92). Default min_ops=5 achieves best IID 85.15 / OOD 59.59 — at least +3.64 / +5.67 over alternatives. Part B tests reward weights: equal (0.25×4) yields IID 82.29 / OOD 55.58; checker-heavy (0.4/0.2/0.2/0.2) IID 82.94 / OOD 54.41. All degrade gradually (IID −2.21 to −4.42), confirming robustness.

**The structural prior teaches the policy *when to stop*, not to build longer workflows.** New **Table R6** shows task-proportional complexity: GSM8K averages 8.3 turns / 21.9K tokens vs. AIME 12.4 turns / 47.2K tokens (Spearman ρ=0.65, p=0.021). Figure 5(a-b) shows CWRPO (Full) uses *fewer* tokens than all ablation variants — removing the prior actually *increases* cost due to over-exploration.

**Limitations**: Public benchmarks only; sandboxed execution (Appendix A.2); 12 operators cover math/QA/code — broader domains need extension; local models reduce cost (Figure 4c).
