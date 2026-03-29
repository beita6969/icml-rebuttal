# Reviewer igcB (Score: 3, Weak Reject)

We thank the reviewer for recognizing the conditional release mechanism, comprehensive evaluation, and plug-and-play design. We address each concern below (full tables in https://anonymous.4open.science/r/Supplementary).

**W1: Operator library scalability with overlapping tools** (New: Tables R2–R4 | Paper: Prop. 1, 4)

We appreciate this question and would like to clarify a key distinction in FlowSteer's design. FlowSteer operates at a *meta-level*: rather than searching over tools to call, it constructs a workflow graph (Definition 1) through multi-turn editing actions (add/delete/modify/set_prompt/control structures). The 12 operators serve as composable building blocks for workflow construction, not as a tool inventory to search from. This fundamentally differs from tool-search systems where scaling to hundreds of APIs introduces search-space and disambiguation challenges.

Concretely, the factored action space (Section 4.1, Proposition 1) decouples action types from operators — adding new operators grows cost linearly: $O(\\lvert\\mathcal{A}\_{\\text{type}}\\rvert + \\lvert\\mathcal{O}\\rvert)$. Flow-Director selects operators through semantic descriptions (Table 7), not indices, so overlapping functionality is resolved by the policy's understanding of operator semantics rather than exhaustive search.

Our transfer experiments (new **Tables R2–R4**) empirically validate this: removing operators causes task-specific degradation (−Verify/Test: APPS −14.05, DS-1000 −20.31; −ScEnsemble/Aggregate: AIME −6.67), confirming each group's distinct role; substitution with unseen implementations yields near-lossless performance (avg. IID +0.3 / OOD +0.1); and adding entirely new operators shows selective improvement without non-target degradation (+Search: TriviaQA/NQ +5.47/+8.59 EM; +Debugger: APPS/DS-1000 +2.35/+3.91).

We acknowledge that extending to significantly larger operator libraries is an important future direction, and a natural extension is hierarchical operator selection (category → operator), which our factored action space naturally supports. That said, our current 12 operators already provide broad task coverage: Proposition 4 (Appendix B.2) proves that they realize all 7 cognitive primitives (Generation, Verification, Aggregation, Conditional branching, Sequential chaining, Ensemble, Formatting) via a surjective mapping, with a theoretical minimum of 5 operators — our library exceeds this bound while the transfer experiments above confirm no redundancy.

**W2: Computational cost and training-inference trade-off** (New: Table R6 | Paper: Appendix G, Figures 4–5)

We provide a concrete cost analysis. FlowSteer trains in ~8 GPU-hours on 2×A100 80GB (~300 steps). The apparent cost of "36 trajectories × 20 rounds" is mitigated by vectorized rollout (Appendix G): 32-way concurrent trajectory interaction, batched API calls, cached workflow states, and early-finish skipping.

Crucially, FlowSteer trains once and deploys zero-shot to 6 architecturally diverse backends (Figure 4), requiring no per-backend tuning.

At inference, FlowSteer is *more* efficient: Figure 5(a-b) shows CWRPO (Full) uses fewer tokens and turns than all ablation variants — RL teaches the policy when to stop. New Table R6 confirms task-proportional cost: GSM8K averages 8.3 turns / \$0.0012 vs. AIME 12.4 turns / \$0.0019 per problem.

**W3: Weight inconsistency between Appendix C.1 and Table 11** (New: Table R5 | Paper: Table 11)

We thank the reviewer for catching this. The actual training uses weights 0.2/0.2/0.2/0.4 (as in Table 11), giving higher weight to $R\_{control}$ to reflect the importance of control structures. Appendix C.1's equal-weight statement was from an earlier draft and will be corrected.

Importantly, our sensitivity analysis (new **Table R5**) shows this distinction has limited practical impact: equal weighting (0.25×4) achieves IID 82.29 / OOD 55.58; checker-heavy (0.4/0.2/0.2/0.2) achieves IID 82.94 / OOD 54.41 — vs. default IID 85.15 / OOD 59.59. All 5 configurations show gradual degradation (IID −2.21 to −4.42) without catastrophic failure. All weight configurations with sum=1.0 preserve the conditional release mechanism (Eq. 14).
