# Reviewer LqWU (Score: 4, Weak Accept)

We thank the reviewer for recognizing our problem framing and broad evaluation. We address each concern with new experiments (full tables in [Anonymous Supplementary](LINK)).

**W1/Q1: Component-level ablation of CWRPO**

We have completed this ablation across all 12 benchmarks, removing each component independently under identical training (**Table R1**):

| Variant | IID Avg. | Δ | OOD Avg. | Δ |
|---|---|---|---|---|
| CWRPO (Full) | 85.15 | — | 59.59 | — |
| w/o Token Masking | 79.82 | −5.34 | 52.06 | −7.52 |
| w/o Diversity Reward | 80.08 | −5.08 | 50.30 | −9.28 |
| w/o Cond. Release | 80.86 | −4.30 | 52.85 | −6.74 |

Each component exhibits a distinct failure mode: **(1) Token Masking** causes the largest IID drop, concentrated on math — MATH 81.25→67.97 (−13.28), MathQA 88.67→78.12 (−10.55), consistent with the variance reduction proof in Proposition 6c (Eqs. 62–64). **(2) Diversity Reward** causes the largest OOD drop (−9.28), with AIME 26.67→16.67 (−10.00), DS-1000 58.59→46.09 (−12.50), APPS 49.21→37.50 (−11.71), confirming structural scaffolding prevents shortcut convergence on unseen distributions (Proposition 3). **(3) Conditional Release** uniquely impacts open-domain QA — TriviaQA EM −9.38, NQ EM −7.81, validating the two-stage curriculum that suppresses premature termination (Proposition 6b, Eqs. 49–54). With Table 5 (framework) and Table 6 (algorithm), this forms a three-layer ablation hierarchy.

**W2/Q2: Operator library transfer**

We conduct three tiers of transfer experiments on IID and OOD benchmarks (**Tables R2–R4**), the first systematic operator-level transfer evaluation in this area.

*Removal* (**Table R2**): We remove operator groups at test time without retraining. Each group's removal causes task-specific degradation: −Verify/Test impacts code most severely (APPS 49.21→35.16, DS-1000 58.59→38.28), −Review/Revise impacts QA (HotPotQA EM −7.03, SQuAD v2 EM −4.68), and −ScEnsemble/Aggregate significantly impacts hard problems (AIME 26.67→20.00). This confirms surjective coverage over the 7 cognitive primitives in Proposition 4 (Appendix B.1, Eqs. 19–25) — each group maps to a distinct functional role, and removal creates an observable gap. Notably, even the minimal 4-operator configuration (below the theoretical minimum of 5 in Proposition 4) still achieves IID 76.95 / OOD 50.30 — graceful degradation rather than catastrophic failure, demonstrating the robustness of our learned orchestration policy.

*Substitution & Addition* (**Tables R3–R4**): Replacing operators with unseen alternatives (Programmer→Jupyter Kernel, Custom→Generate with Skills) yields near-lossless avg. IID 85.42 (+0.3) / OOD 59.69 (+0.1). Adding entirely new operators unseen during training shows selective improvement on target tasks — +Search: TriviaQA/NQ +5.47/+8.59 EM; +Debugger: APPS/DS-1000 +2.35/+3.91 — with zero degradation elsewhere. Both capabilities stem from the factored action space $O(\lvert\mathcal{A}_\text{type}\rvert + \lvert\mathcal{O}\rvert)$ (Proposition 1): Flow-Director selects operators via semantic descriptions (Table 7), not indices, enabling zero-shot transfer to unseen implementations.

**W3/Q3: Structural prior sensitivity**

We first clarify that the structural constraints are not heuristic but theoretically grounded: Proposition 4 (Appendix B.1) defines 7 cognitive primitives and proves that a complete control loop requires at minimum 5 operators via surjective coverage (Eqs. 19–25).

To verify sensitivity, we retrain from scratch with varied hyperparameters (**Table R5**): relaxing to min_ops=4 yields IID 80.60 (−4.55) / OOD 47.54; switching to equal weighting (0.25×4) yields IID 79.56 (−5.59) / OOD 48.72. Both show gradual degradation rather than catastrophic failure, confirming the framework is robust to specific hyperparameter choices while the default configuration (min_ops=5, 0.2/0.2/0.2/0.4) remains near-optimal.

Regarding the concern about bias toward longer workflows: **Table R6** and **Appendix H** demonstrate the opposite — FlowSteer generates task-proportional workflows. For easy tasks, GSM8K uses a simple 3-operator chain in ~8.3 turns / 21.9K tokens; for hard tasks, AIME 2025 constructs a 4-stage pipeline Plan→Programmer→Verify→Format over 12.4 turns / 47.2K tokens (detailed in Appendix H case study). Spearman ρ = 0.65 (p = 0.021) between dataset difficulty and workflow complexity confirms statistically significant adaptation. Moreover, Figure 5(a-b) shows CWRPO (Full) uses *fewer* tokens and turns than all ablation variants — the structural prior teaches the policy *when to stop*, not to build uniformly complex workflows.

**Limitations**: We expand in revision — public benchmarks only (no PII), sandboxed execution (Appendix A.2); 12 operators cover math/QA/code, broader domains need extension; local models reduce cost (Figure 4c).
