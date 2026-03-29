# Reviewer LqWU (Score: 4, Weak Accept)

We thank the reviewer for recognizing our problem framing and broad evaluation. We address each concern with new experiments (full per-benchmark data in [Supplementary](https://anonymous.4open.science/r/Supplementary)).

**W1/Q1: Component-level ablation of CWRPO**

We ablated each component independently across all 12 benchmarks (new **Table R1**):

| Variant | IID Avg. | Δ | OOD Avg. | Δ |
|---|---|---|---|---|
| CWRPO (Full) | 85.15 | — | 59.59 | — |
| w/o Token Masking | 79.82 | −5.34 | 52.06 | −7.52 |
| w/o Diversity Reward | 80.08 | −5.08 | 50.30 | −9.28 |
| w/o Cond. Release | 80.86 | −4.30 | 52.85 | −6.74 |

Each component solves a distinct challenge and none is redundant. **Token Masking** handles credit assignment — without it, environment tokens corrupt gradients (worst on math: MATH −13.28). **Diversity Reward** prevents mode collapse — the policy converges to shortcut workflows that fail OOD (−9.28; AIME −10.00, DS-1000 −12.50). **Conditional Release** prevents premature convergence — without curriculum gating, the policy terminates early on tasks needing longer reasoning chains (TriviaQA −9.38, NQ −7.81). The three failures are orthogonal (different task categories, non-compensating), proving CWRPO is a minimal complete design. Together with Table 5 (framework-level) and Table 6 (RL algorithm-level), this establishes a three-layer ablation hierarchy.

**W2/Q2: Operator library transfer**

We conduct three transfer tiers on all benchmarks (new **Tables R2–R4**). *Removal*: each group's removal causes task-specific degradation (−Verify/Test: APPS −14.05, DS-1000 −20.31; −Review/Revise: HotPotQA −7.03, SQuAD v2 −4.68; −ScEnsemble/Aggregate: AIME −6.67), confirming surjective coverage of the 7 cognitive primitives (Proposition 4). Even the minimal 4-operator configuration achieves IID 76.95 / OOD 50.30 — graceful degradation rather than catastrophic failure. *Substitution*: unseen alternatives yield near-lossless IID 85.42 (+0.3) / OOD 59.69 (+0.1). *Addition*: new operators selectively improve targets (+Search: TriviaQA/NQ +5.47/+8.59; +Debugger: APPS/DS-1000 +2.35/+3.91) with zero non-target degradation. Both capabilities stem from the factored action space (Proposition 1) — semantic-based selection enables zero-shot transfer to unseen operators.

**W3/Q3: Structural prior sensitivity**

**The structural constraints are theoretically necessary.** Proposition 4 (Appendix B.1) identifies 7 cognitive primitives (Generation, Verification, Aggregation, Conditional branching, Sequential chaining, Ensemble, Formatting) that any complete workflow must cover. Via surjective coverage (Eqs. 19–25), since certain primitives demand functionally distinct operators, the theoretical minimum is 5.

**min_ops=5 is the practical optimum, not just a lower bound.** New Table R5 retrains with min_ops ∈ {3,4,5,7}: below 5 creates coverage gaps (IID 80.73–81.51, OOD 52.29–52.32); above 5 over-constrains without benefit (IID 80.99, OOD 53.92). Default min_ops=5 achieves best IID 85.15 / OOD 59.59 — a gap of ≥3.64 IID / 5.67 OOD over any alternative. Reward weight variations (equal 0.25×4: IID 82.29 / OOD 55.58; checker-heavy 0.4/0.2/0.2/0.2: IID 82.94 / OOD 54.41) also show gradual degradation (IID −2.21 to −4.42), not catastrophic failure.

**The prior teaches the policy *when to stop*.** Table R6: GSM8K uses 8.3 turns / 21.9K tokens vs. AIME 12.4 turns / 47.2K tokens (Spearman ρ=0.65, p=0.021). Figure 5(a-b) confirms CWRPO (Full) uses *fewer* tokens than all ablation variants — removing the prior actually *increases* cost.

**Limitations**: Public benchmarks only; sandboxed execution (Appendix A.2); 12 operators cover math/QA/code, broader domains need extension; local models reduce cost (Figure 4c).
