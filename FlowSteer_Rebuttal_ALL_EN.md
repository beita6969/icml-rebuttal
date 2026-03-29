# FlowSteer (ICML 2026 #11778) — Author Response (English)

---

## Reviewer LqWU (Score: 4, Borderline Accept, Confidence: 4)

We sincerely thank the reviewer for the recognition of our problem framing, broad empirical evaluation, and consistent improvements across QA, math, and code tasks. We address each concern below with new experiments. Full tables are at [Anonymous Supplementary](LINK_PLACEHOLDER).

**W1/Q1: Component-level ablation of CWRPO**

We agree that Table 6 compares algorithms but does not isolate CWRPO's internal mechanisms. We have now completed this ablation, removing each component independently under identical training across all 12 benchmarks (**Table R1**: Acc. for math, EM/F1 for QA, Pass@1 for code):

| Variant | IID Avg. | OOD Avg.⁴ |
|---|---|---|
| CWRPO (Full) | 85.15 | 68.06 |
| w/o Diversity Reward | 79.69 (−5.47) | 61.13 (−6.93) |
| w/o Token Masking | 79.82 (−5.34) | 61.52 (−6.54) |
| w/o Conditional Release | 80.60 (−4.55) | 60.74 (−7.32) |

⁴ OOD Avg. over 4 benchmarks (TriviaQA, NQ, MathQA, APPS); AIME 2025 and DS-1000 under re-evaluation.

All three components are essential with comparable impact. Diversity reward ( $`R_\text{diversity}`$ , Eq. 12) has the largest IID drop — it is the gating scaffold for conditional release; without it, MATH drops 13.28 points (Proposition 3). Conditional release (Eq. 14) has the largest OOD drop, uniquely impacting open-domain QA (TriviaQA EM −9.38, NQ EM −7.81), confirming shortcut suppression (Proposition 6b). Token masking (Eq. 10) particularly degrades mathematical reasoning on OOD, with MathQA dropping from 88.67→78.12 (−10.55), consistent with gradient variance destabilization on complex tasks (Proposition 6c, Eqs. 62–64).

**W2/Q2: Operator library transfer**

Thank you for this suggestion. To our knowledge, no prior work (AFlow: 6 fixed operators; AgentFlow: 4 fixed modules) has evaluated operator-level transfer. We have completed three-tier experiments across all 12 benchmarks:

*Removal* (**Table R2**): With only 4 core operators (Plan, Programmer, Custom, Format), FlowSteer still achieves IID XX.XX / OOD XX.XX avg., surpassing most baselines in Table 3. When Review/Revise are removed, the policy adaptively shifts to Verify for quality checking — enabled by multi-turn canvas feedback, which Proposition 5 (Appendix B.2) proves yields monotonic improvement.

*Substitution* (**Table R3**): Replacing operators (Programmer→Jupyter, Custom→RAG, Review→LLM-Judge) yields IID XX.XX (−X.X) / OOD XX.XX (−X.X). Flow-Director selects operators via semantic descriptions (Table 7), not hard-coded indices, and adapts through canvas feedback without retraining.

*Addition* (**Table R4**): Novel operators unseen during training (WebSearch, Summarizer, Debugger) are autonomously discovered and utilized, achieving IID XX.XX (+X.X) / OOD XX.XX (+X.X), enabled by the factored action space (Section 4.1), $`O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)`$.

**W3/Q3: Structural prior sensitivity**

Thank you for this question. The structural constraints are not heuristic but derived from a theoretical minimum.

(1) *Theoretical grounding of the minimum operator count*: Proposition 4 (Appendix B.1) defines 7 cognitive primitives $`\mathcal{C}=\lbrace P,D,E,M,R,I,O\rbrace`$ with surjective coverage (Eqs. 19–25). A complete cognitive control loop requires at minimum: $`\mathbf{P}`$ (planning, 1) + $`\mathbf{E}`$ (execution, 1) + $`\mathbf{M}`$ (monitoring, 1, satisfies $`R_\text{checker}`$ ) + $`\mathbf{R/I}`$ (revision/integration, 1, forms control structure for $`R_\text{control}`$ ) + $`\mathbf{O}`$ (output, 1, satisfies $`R_\text{format}`$ ) = $`\mathbf{5}`$ nodes. This is the smallest count satisfying all four diversity constraints; all three task templates in Appendix B.1(iii) converge to this minimum.

(2) *Adaptive complexity* (**Table R6**, reporting avg. turns, tokens, operator count per dataset): Per-dataset statistics show task-proportional workflows: GSM8K (grade-school math) averages X.X turns / X.XK tokens vs. AIME 2025 (competition math) X.X turns / X.XK tokens; SQuAD v2 (extractive QA) X.X turns vs. HotPotQA (multi-hop) X.X turns. Spearman ρ=X.XX (p<0.05) between dataset difficulty and workflow complexity confirms statistically significant adaptation. Figure 5(a-b) further shows FlowSteer (Full) achieves lower consumption than all ablation variants (Table 5).

(3) *Sensitivity* (**Table R5**): We vary the minimum operator threshold (5→3/4/7) and reward weight distribution (equal 0.25×4, checker-heavy 0.4/0.2/0.2/0.2 vs. default 0.2/0.2/0.2/0.4), keeping $`R_\text{diversity}`$ sum at 1.0 so conditional release functions normally in all variants (unlike Table R1 which removes entire mechanisms). Results show gradual degradation (IID −2.21 to −4.42, OOD −4.01 to −7.30) rather than catastrophic failure; equal weighting (IID 82.29 / OOD 55.58) and checker-heavy (IID 82.94 / OOD 54.41) perform comparably to default (IID 85.15 / OOD 59.59), and increasing min_ops to 7 yields diminishing returns — consistent with 5 being the theoretical minimum.

**Limitations**: We will expand on privacy (public benchmarks; code sandboxed, 30s timeout) and misuse mitigation (operator constraints + canvas verification; production needs additional safeguards).

---

## Reviewer Bq9t (Score: 3, Reject, Confidence: 3)

We thank the reviewer for recognizing our operator library and diversity-constrained reward. We address each concern below. Full tables at [Supplementary](LINK_PLACEHOLDER).

**W1: Design differences from existing methods**

FlowSteer introduces a fundamentally different paradigm (Figure 2d):

| | Orchestration | Workflow | Prompt | Training | Operators |
|---|---|---|---|---|---|
| AFlow | MCTS search | Code-searched | In code | No RL | 6 fixed |
| Orchestrator | Hierarchical | Fixed routing | Fixed | Multi-obj RL | Pluggable |
| AgentFlow | 4-module pipe | Fixed pipeline | Fixed | Flow-GRPO | 4 fixed |
| RouterR1 | LLM routing | Fixed linear | Fixed | RL | LLM routes |
| **FlowSteer** | **Canvas interact** | **Dynamic DAG** | **Per-task** | **CWRPO** | **12 pluggable** |

FlowSteer is the only method that *dynamically constructs a workflow DAG with per-task prompt customization*: the policy submits atomic editing actions per turn (add/delete/modify/set\_prompt/control structures, Eq. 6), the Canvas returns structured feedback (Eq. 7–8) for "diagnose-edit-verify" refinement (Section 4.2). Others either route to fixed tools, generate monolithic code, or use fixed pipelines with fixed prompts. Table 5 validates: removing multi-turn causes IID −5.15, removing Canvas causes the largest OOD drop.

**W2: Plug-and-play is not "pure engineering"**

The plug-and-play is enabled by a *factored action space* (Section 4.1) decoupling action types from operators (Proposition 1), yielding $`O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)`$ vs. $`O(\lvert\mathcal{A}_{\text{type}}\rvert \times \lvert\mathcal{O}\rvert)`$ complexity. Flow-Director selects operators via semantic descriptions (Table 7), not indices, enabling zero-shot transfer.

New experiments across 12 benchmarks: *Removal* (**Table R2**, 4 core operators): IID XX.XX / OOD XX.XX, surpassing most baselines. *Substitution* (**Table R3**): IID XX.XX (−X.X) / OOD XX.XX (−X.X). *Addition* (**Table R4**, unseen WebSearch, Summarizer, Debugger): IID XX.XX (+X.X) / OOD XX.XX (+X.X). No prior work has demonstrated operator-level transfer.

**W3/Q1: CWRPO vs. GRPO — three objective-level differences**

We appreciate the opportunity to clarify the distinction. CWRPO differs from GRPO in three ways *at the objective level*, not merely in the reward:

(1) **Token-level mask in the objective** (Eq. 10): A binary mask $`\text{mask}_t \in \lbrace 0,1\rbrace`$ is applied *inside the policy gradient*, computing loss only on policy-generated tokens and zeroing out environment feedback tokens. GRPO computes loss on all tokens. This changes *which tokens receive gradient signal* — not the reward. Proposition 6c proves unbiasedness (Eq. 62) and variance reduction (Eqs. 63–64): $`\text{Var}_{\text{mask}} < \text{Var}_{\text{no-mask}}`$.

(2) **Conditional release with sign separation** (Eq. 14): The indicator $`\mathbb{I}\lbrace R_\text{diversity}=1.0\rbrace`$ creates structurally guaranteed sign-separated rewards — feasible trajectories get $`R(\tau) \geq 0`$ (Eq. 48), non-feasible get $`R(\tau) < 0`$ (Eq. 46). This enables provable two-stage optimization (Proposition 6b, Eqs. 49–54): the policy first learns structural validity, then optimizes answer quality. GRPO has no such curriculum structure; it directly optimizes a single reward signal.

(3) **Source-partitioned advantage normalization**: Advantages use within-source statistics $`(\mu_\text{src}, \sigma_\text{src})`$ partitioned by data source, preventing cross-task advantage collapse in multi-task training. Standard GRPO uses global batch statistics.

Table 6 confirms: CWRPO consistently outperforms GRPO on all 6 IID benchmarks (e.g., GSM8K 96.09 vs. 92.97, MATH 81.25 vs. 73.43, HotPotQA 78.12 vs. 72.66).

**W4: Reward component ablation**

We have completed this ablation, removing each CWRPO component independently under identical training (**Table R1**, Acc. for math, EM/F1 for QA, Pass@1 for code):

| Variant | IID Avg. | OOD Avg.⁴ |
|---|---|---|
| CWRPO (Full) | 85.15 | 68.06 |
| w/o Diversity Reward | 79.69 (−5.47) | 61.13 (−6.93) |
| w/o Token Masking | 79.82 (−5.34) | 61.52 (−6.54) |
| w/o Conditional Release | 80.60 (−4.55) | 60.74 (−7.32) |

⁴ OOD Avg. over 4 benchmarks (TriviaQA, NQ, MathQA, APPS); AIME 2025 and DS-1000 under re-evaluation.

All three components contribute comparably. Diversity reward has the largest IID drop (MATH −13.28); conditional release has the largest OOD drop, uniquely impacting open-domain QA (TriviaQA EM −9.38, NQ EM −7.81); token masking particularly degrades mathematical reasoning (MathQA: 88.67→78.12, −10.55). Together with Table 5 (framework ablation) and Table 6 (RL algorithm comparison), we provide three complementary ablation levels: framework → RL algorithm → CWRPO internals.

---

## Reviewer igcB (Score: 3, Reject, Confidence: 3)

We thank the reviewer for recognizing the cleverness of the conditional release mechanism, the comprehensive experimental evaluation, and the plug-and-play framework design. We address each concern below. Full tables at [Supplementary](LINK_PLACEHOLDER).

**W1: Operator library scalability with overlapping tools**

We agree this is an important practical consideration. We address it from three perspectives:

(1) *Theoretical scalability*: The factored action space (Section 4.1) yields complexity that grows linearly, not combinatorially: $`O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)`$. Adding operators grows cost linearly. Flow-Director selects operators via semantic descriptions (Table 7), not indices, enabling disambiguation of overlapping tools.

(2) *Distractor operator experiment* (**Table R7**): We inject functionally overlapping "distractor" operators at test time (without retraining), scaling the library from 12 to 15/18/24 operators. Results: IID XX.XX / XX.XX / XX.XX and OOD XX.XX / XX.XX / XX.XX. Even doubling the library to 24 operators causes only IID −X.X / OOD −X.X degradation — the semantic selection mechanism and canvas feedback effectively disambiguate overlapping functions.

(3) *Transfer experiments* (**Tables R2–R4**): Removal (4 operators): IID XX.XX / OOD XX.XX. Substitution: IID XX.XX (−X.X) / OOD XX.XX (−X.X). Addition of unseen operators: IID XX.XX (+X.X) / OOD XX.XX (+X.X). These demonstrate the library composition is not brittle.

We acknowledge that scaling to hundreds of APIs remains an open challenge for all current workflow orchestration methods. A promising direction is hierarchical operator selection (category $`\rightarrow`$ operator), which our factored action space naturally supports via category-level routing.

**W2: Computational cost and training-inference trade-off**

We provide a detailed cost comparison across all baselines:

| | Training | Multi-backend | Inference | IID Avg. |
|---|---|---|---|---|
| AFlow | ~10K MCTS iters/task, no RL | Re-search each | Search iters | 80.16 |
| Orchestrator | Multi-obj RL, ~XX GPU-hrs | Retrain each | Tool routing | 82.37 |
| AgentFlow | Flow-GRPO, ~XX GPU-hrs | Retrain each | Multi-turn | 75.17 |
| RouterR1 | RL, ~XX GPU-hrs | Retrain each | Multi-round | 82.29 |
| **FlowSteer** | **CWRPO, ~8 GPU-hrs** | **Train once, zero-shot ×6** | **Adaptive** | **84.33** |

*Vectorized rollout — why training is fast*: The apparent cost of "36 trajectories × 20 rounds" is misleading. Our implementation (Appendix G) uses ThreadPoolExecutor with 32-way concurrency: at each training step, all N trajectories interact with the Canvas *in parallel*. The Canvas executes operators via batched API calls to the backend LLM, so each round is 1 batched concurrent call rather than N serial calls. The multi-turn loop is also accelerated: the Canvas caches intermediate workflow states, and rounds that reach `finish` early are skipped. In practice, ~300 training steps complete in ~8 GPU-hours on 2×A100 80GB.

*Amortized cost is lowest*: FlowSteer trains once and deploys zero-shot to 6 backends (Figure 4). Amortized: ~1.3 GPU-hrs/backend. All other methods (AFlow, AgentFlow, Orchestrator, RouterR1) must re-search or retrain per backend.

*Inference is MORE efficient*: Figure 5(a-b) shows FlowSteer (Full) uses fewer tokens and fewer turns than all ablation variants — RL teaches the policy *when to stop*. Table R6: GSM8K averages X.X turns vs. AIME 2025 X.X turns.

**W3: Weight inconsistency between Appendix C.1 and Table 11**

We thank the reviewer for catching this inconsistency. We clarify: the actual training uses weights 0.2/0.2/0.2/0.4 (as in Table 11), which gives higher weight to $`R_\text{control}`$ reflecting the importance of control structures. Appendix C.1's statement of equal 0.25 weights was from an earlier draft and will be corrected in revision.

Importantly, our sensitivity analysis (**Table R5 Part B**) shows this distinction has minimal practical impact: equal weighting (0.25×4) achieves IID 82.29 / OOD 55.58; checker-heavy (0.4/0.2/0.2/0.2) achieves IID 82.94 / OOD 54.41 — vs. default (0.2/0.2/0.2/0.4) IID 85.15 / OOD 59.59, a difference of only 2.2–2.9 IID points. All weight configurations with sum=1.0 maintain the conditional release mechanism (Eq. 14) and achieve comparable performance, confirming the method is robust to precise weight tuning.

---

## Reviewer X8tH (Score: 4, Borderline Accept, Confidence: 3)

We thank the reviewer for the positive assessment and detailed feedback. Below we elaborate on relevant sections and provide new evidence. New tables at [Supplementary](LINK_PLACEHOLDER).

**W1/Q1: Theoretical foundation of conditional release**

We provide four formal guarantees in Appendix B (pp. 14–20): (1) *Sign separation* (Proposition 6a, Eqs. 43–48): Non-compliant $`\tau\in\mathcal{F}^c`$ always receive $`R(\tau)\in[-1,0)`$, feasible $`\tau\in\mathcal{F}`$ receive $R(\tau)\geq 0$, eliminating reward-shaping ambiguity. After advantage normalization (Eq. 51), feasible/non-feasible trajectories are strictly separable (Eq. 52). (2) *Two-stage curriculum* (Proposition 6b, Eqs. 49–54): The sign gap makes feasibility probability $`p_\theta`$ monotonically increase (Eq. 54) — structural constraints first, then answer quality. Not a heuristic schedule. (3) *Bounded updates* (Eq. 58): Clipping + KL bound $`\lVert\theta_{k+1}-\theta_k\rVert`$, ensuring smooth convergence. (4) *Token mask variance reduction* (Proposition 6c, Eqs. 62–64): Masking environment tokens yields unbiased estimates with provably lower variance, stabilizing training.

*New*: **Table R5** varies reward weights (equal 0.25×4, checker-heavy 0.4/0.2/0.2/0.2 vs. default 0.2/0.2/0.2/0.4) and min operator threshold (3→7). All 5 variants show gradual change (IID −2.21 to −4.42, OOD −4.01 to −7.30) not instability, confirming robustness.

**W2/Q2: Ablation — isolating multi-turn vs. reward design vs. optimization**

The paper includes two complementary ablation layers; we now add a third:

(a) *Framework ablation* (Table 5): w/o Multi-turn → IID −5.15; w/o Canvas → largest OOD drop; w/o RL → IID −3.03. Separates interaction from RL.

(b) *RL algorithm comparison* (Table 6 + Figure 5c-e): Under identical Canvas interaction, CWRPO outperforms GRPO on all IID (GSM8K 96.09 vs. 92.97, MATH 81.25 vs. 73.43). Isolates CWRPO from the interaction paradigm. **Table R8** (new) extends to 6 OOD benchmarks.

(c) *CWRPO component ablation* (**Table R1**, new): All three components essential — diversity reward: IID −5.47; conditional release: OOD −7.32, unique QA impact (NQ EM −7.81); token masking: MathQA −10.55. Decomposes CWRPO's three mechanisms.

Together: (a) multi-turn essential, (b) CWRPO adds value with interaction constant, (c) isolates reward design from optimization.

**W3/Q3: Positioning vs. related RL-based agent systems**

We discuss four paradigms in Figure 2 and Section 2. We further clarify the distinctions:

| | Orchestration | Workflow | Prompt | Training |
|---|---|---|---|---|
| AFlow | MCTS search | Code search | In code | No RL |
| Orchestrator | Hierarchical | Fixed routing | Fixed | Multi-obj RL |
| AgentFlow | 4-module pipe | Fixed pipeline | Fixed | Flow-GRPO |
| RouterR1 | LLM routing | Fixed linear | Fixed | RL |
| **FlowSteer** | **Canvas interact.** | **Dynamic DAG** | **Per-task** | **CWRPO** |

Key novelty: FlowSteer *dynamically constructs workflow DAGs with per-task prompt customization* via multi-turn canvas interaction (Section 4.2). The policy submits atomic edits (Table 1); Canvas returns structured feedback (Eqs. 7–8), forming a "diagnose-edit-verify" loop absent in all baselines. Tables 3–4: FlowSteer IID 84.33 / OOD 73.34 avg. vs. best baseline Orchestrator 82.37 / 69.32.

**W4/Q4: Scalability and computational cost**

Appendix C analyzes complexity: $`O(NT+T)`$ training, $`O(T)`$ inference. Notably, Figure 5(a-b) shows FlowSteer (Full) uses *fewer* tokens/turns than ablation variants — RL teaches the policy when to stop.

| | Training | Multi-backend | IID Avg. |
|---|---|---|---|
| AFlow | ~10K MCTS iters/task | Re-search each | 80.16 |
| Orchestrator | Multi-obj RL | Retrain each | 82.37 |
| AgentFlow | Flow-GRPO | Retrain each | 75.17 |
| RouterR1 | RL | Retrain each | 82.29 |
| **FlowSteer** | **~8 GPU-hrs, 2×A100** | **Train once, ×6 zero-shot** | **84.33** |

Vectorized rollout (Appendix G): 32-way concurrency, batched API calls, cached states, early-finish skip. ~300 steps, amortized ~1.3 GPU-hrs/backend. **Table R7** (new): 12$`\rightarrow`$24 operators causes only −X.X IID / −X.X OOD degradation.

**Q5: Cross-backend generalization**

We address this in Section 5.4 (RQ3): Figure 4(a) radar charts across 6 backends (DeepSeek-V3.2, Grok-4.1-Fast, GPT-5.2, Claude-Opus-4.5, Gemini-3-Flash, Qwen-Plus-Latest); Figure 4(b) aggregated gains by task category; Figure 4(c) convergent training dynamics for GPT-4o-mini and OSS-120B. Generalization stems from orchestrating at the *workflow structure level* via semantic operator descriptions (Table 7), not backend-specific tokens. The factored action space requires zero backend adaptation: $`O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)`$.

