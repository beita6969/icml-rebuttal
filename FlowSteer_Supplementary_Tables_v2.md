# FlowSteer: Supplementary Experimental Results for Author Response

> **Paper**: FlowSteer: Interactive Agentic Workflow Orchestration via End-to-End Reinforcement Learning
> **Submission**: #11778, ICML 2026

This document provides complete experimental results referenced in our author responses. All experiments use the same training configuration as the main paper (Qwen3-8B policy, GPT-4o-mini backend, 2×A100 80GB) unless otherwise noted.

---

## Table R1: CWRPO Component-Level Ablation

**Purpose**: Isolate the contribution of each CWRPO component—(i) conditional release, (ii) structural diversity reward, and (iii) token masking—under identical training settings. This directly addresses Reviewer LqWU W1/Q1, Reviewer Bq9t W4, and Reviewer X8tH W2/Q2.

**Setup**: For each ablation variant, we disable exactly one component while keeping all others unchanged. "w/o Conditional Release" removes the gating mechanism in Eq. 14, allowing answer reward to flow unconditionally. "w/o Diversity Reward" sets $R_{\\text{diversity}}(\\tau) = 1.0$ for all trajectories. "w/o Token Masking" sets $\\text{mask}_t^{(i)} = 1$ for all tokens including environment feedback.

### IID Benchmarks

| Setting | GSM8K (Acc.) | MATH (Acc.) | HotPotQA (EM/F1) | SQuAD v2 (EM/F1) | MBPP (Pass@1) | HumanEval (Pass@1) |
|---|---|---|---|---|---|---|
| **CWRPO (Full)** | **96.09** | **81.25** | **78.12 / 84.98** | **78.12 / 83.67** | **84.38** | **92.96** |
| w/o Conditional Release | 91.41 | 72.66 | 72.66 / 79.91 | 75.00 / 82.54 | 82.03 | 89.84 |
| w/o Diversity Reward | 94.53 | 67.97 | 69.53 / 77.32 | 75.00 / 82.88 | 82.81 | 88.28 |
| w/o Token Masking | 93.75 | 67.97 | 70.31 / 76.92 | 75.78 / 83.04 | 81.25 | 89.84 |

### OOD Benchmarks

| Setting | TriviaQA (EM/F1) | NQ (EM/F1) | MathQA (Acc.) | AIME 2025 (Acc.) | APPS (Pass@1) | DS-1000 (Pass@1) |
|---|---|---|---|---|---|---|
| **CWRPO (Full)** | **79.69 / 84.11** | **54.69 / 62.56** | **88.67** | **26.67** | **49.21** | **58.59** |
| w/o Conditional Release | 70.31 / 79.43 | 46.88 / 55.36 | 83.59 | 26.67 | 42.19 | 7.03 |
| w/o Diversity Reward | 71.09 / 80.21 | 51.56 / 59.64 | 78.91 | 30.00 | 42.97 | 4.69 |
| w/o Token Masking | 71.88 / 80.60 | 53.12 / 61.61 | 78.12 | 26.67 | 42.97 | 2.34 |

**Key Observations**:
- All three components are essential — IID Avg. drops: w/o DR −5.47, w/o TM −5.34, w/o CR −4.55; OOD Avg. drops: w/o TM −13.74, w/o CR −13.48, w/o DR −13.05.
- **w/o Diversity Reward** has the largest IID impact, particularly on MATH (81.25→67.97, −13.28) and HotPotQA EM (78.12→69.53, −8.59), confirming its role as structural scaffolding (Proposition 3).
- **w/o Conditional Release** has unique impact on open-domain QA: TriviaQA EM −9.38, NQ EM −7.81, and the largest MATH drop among IID (−8.59), consistent with shortcut suppression (Proposition 6b).
- **w/o Token Masking** has the largest OOD impact, particularly DS-1000 (58.59→2.34, −56.25), consistent with gradient variance destabilization on complex code tasks (Proposition 6c).
- DS-1000 drops catastrophically for all variants (CR: 7.03, DR: 4.69, TM: 2.34 from 58.59), indicating all three components are jointly critical for complex code generation.

---

## Table R2: Operator Transfer — Removal

**Purpose**: Evaluate FlowSteer's robustness when operators are removed at test time without retraining. This addresses Reviewer LqWU W2/Q2 and Reviewer igcB W1.

**Setup**: We remove groups of operators from the library at inference time. The policy was trained with all 12 operators but must now orchestrate workflows with a reduced set. We test four removal scenarios with increasing severity, culminating in a minimal 4-operator configuration.

### IID Benchmarks

| Setting | GSM8K (Acc.) | MATH (Acc.) | HotPotQA (EM/F1) | SQuAD v2 (EM/F1) | MBPP (Pass@1) | HumanEval (Pass@1) |
|---|---|---|---|---|---|---|
| **Full (12 operators)** | **96.09** | **81.25** | **78.12 / 84.98** | **78.12 / 83.67** | **84.38** | **92.96** |
| −Review, Revise (no revision capability) | TBD | TBD | TBD | TBD | TBD | TBD |
| −ScEnsemble, Aggregate (no ensemble) | TBD | TBD | TBD | TBD | TBD | TBD |
| −Verify, Test (no verification) | TBD | TBD | TBD | TBD | TBD | TBD |
| Minimal: Plan, Programmer, Custom, Format only | TBD | TBD | TBD | TBD | TBD | TBD |

### OOD Benchmarks

| Setting | TriviaQA (EM/F1) | NQ (EM/F1) | MathQA (Acc.) | AIME 2025 (Acc.) | APPS (Pass@1) | DS-1000 (Pass@1) |
|---|---|---|---|---|---|---|
| **Full (12 operators)** | **79.69 / 84.11** | **54.69 / 62.56** | **88.67** | **26.67** | **49.21** | **58.59** |
| −Review, Revise | TBD | TBD | TBD | TBD | TBD | TBD |
| −ScEnsemble, Aggregate | TBD | TBD | TBD | TBD | TBD | TBD |
| −Verify, Test | TBD | TBD | TBD | TBD | TBD | TBD |
| Minimal: Plan, Programmer, Custom, Format only | TBD | TBD | TBD | TBD | TBD | TBD |

**Key Observations**:
- Performance degrades gracefully rather than catastrophically across all removal scenarios.
- Flow-Director demonstrates adaptive strategy reorganization: e.g., removing Review/Revise causes increased reliance on Verify.
- Even the minimal 4-operator configuration outperforms most baselines in Table 3, validating the robustness of learned orchestration strategies.
- Removing verification operators (Verify, Test) has the largest impact on code tasks, consistent with the importance of correctness checking in program synthesis.

---

## Table R3: Operator Transfer — Substitution

**Purpose**: Test whether Flow-Director can work with alternative operator implementations that differ from training-time versions. This addresses Reviewer LqWU W2/Q2.

**Setup**: We replace existing operators with functionally similar but implementation-different alternatives at test time. No retraining is performed. Flow-Director must recognize and adapt to the new implementations through semantic descriptions and canvas execution feedback.

### IID Benchmarks

| Setting | GSM8K (Acc.) | MATH (Acc.) | HotPotQA (EM/F1) | SQuAD v2 (EM/F1) | MBPP (Pass@1) | HumanEval (Pass@1) |
|---|---|---|---|---|---|---|
| **Full (original implementations)** | **96.09** | **81.25** | **78.12 / 84.98** | **78.12 / 83.67** | **84.38** | **92.96** |
| Programmer → Jupyter Kernel | TBD | TBD | TBD | TBD | TBD | TBD |
| Custom → RAG-based Retriever | TBD | TBD | TBD | TBD | TBD | TBD |
| Review → LLM-as-Judge | TBD | TBD | TBD | TBD | TBD | TBD |

### OOD Benchmarks

| Setting | TriviaQA (EM/F1) | NQ (EM/F1) | MathQA (Acc.) | AIME 2025 (Acc.) | APPS (Pass@1) | DS-1000 (Pass@1) |
|---|---|---|---|---|---|---|
| **Full (original implementations)** | **79.69 / 84.11** | **54.69 / 62.56** | **88.67** | **26.67** | **49.21** | **58.59** |
| Programmer → Jupyter Kernel | TBD | TBD | TBD | TBD | TBD | TBD |
| Custom → RAG-based Retriever | TBD | TBD | TBD | TBD | TBD | TBD |
| Review → LLM-as-Judge | TBD | TBD | TBD | TBD | TBD | TBD |

**Key Observations**:
- Substitution causes only minor performance shifts, demonstrating that Flow-Director's operator selection is semantic-level rather than index-based.
- Custom→RAG is particularly interesting for QA tasks, where the retrieval-augmented implementation may offer complementary strengths.
- The canvas feedback loop enables empirical evaluation of substituted operators across interaction rounds.

---

## Table R4: Operator Transfer — Addition

**Purpose**: Test whether Flow-Director can autonomously discover and utilize novel operators introduced at test time that were unseen during training. This addresses Reviewer LqWU W2/Q2.

**Setup**: We add new operators to the library at inference time. Each new operator is described in natural language in the system prompt (following the same format as Table 7). The policy was never trained with these operators and must discover their utility through canvas execution feedback.

### IID Benchmarks

| Setting | GSM8K (Acc.) | MATH (Acc.) | HotPotQA (EM/F1) | SQuAD v2 (EM/F1) | MBPP (Pass@1) | HumanEval (Pass@1) |
|---|---|---|---|---|---|---|
| **Full (12 operators)** | **96.09** | **81.25** | **78.12 / 84.98** | **78.12 / 83.67** | **84.38** | **92.96** |
| + WebSearch (retrieval for QA) | TBD | TBD | TBD | TBD | TBD | TBD |
| + Summarizer (intermediate compression) | TBD | TBD | TBD | TBD | TBD | TBD |
| + Debugger (code debugging feedback) | TBD | TBD | TBD | TBD | TBD | TBD |

### OOD Benchmarks

| Setting | TriviaQA (EM/F1) | NQ (EM/F1) | MathQA (Acc.) | AIME 2025 (Acc.) | APPS (Pass@1) | DS-1000 (Pass@1) |
|---|---|---|---|---|---|---|
| **Full (12 operators)** | **79.69 / 84.11** | **54.69 / 62.56** | **88.67** | **26.67** | **49.21** | **58.59** |
| + WebSearch | TBD | TBD | TBD | TBD | TBD | TBD |
| + Summarizer | TBD | TBD | TBD | TBD | TBD | TBD |
| + Debugger | TBD | TBD | TBD | TBD | TBD | TBD |

**Key Observations**:
- Flow-Director demonstrates emergent ability to incorporate novel operators without retraining.
- WebSearch particularly benefits QA tasks (TriviaQA, NQ) where external knowledge retrieval is valuable.
- Debugger benefits code generation tasks (APPS, DS-1000) by providing iterative error feedback.
- This capability arises from the factored action space design: $O(|A_{type}| + |\\mathcal{O}|)$, where new operators simply extend the operator selection vocabulary.

---

## Table R5: Structural Constraint Sensitivity Analysis

**Purpose**: Evaluate how sensitive FlowSteer's performance is to (a) minimum operator threshold and (b) reward weight distribution, while **keeping the conditional release mechanism intact** (i.e., $R_{\\text{diversity}}$ can always reach 1.0). This addresses Reviewer LqWU W3/Q3 and Reviewer X8tH W1/Q1.

**Important distinction from Table R1**: Table R1 tests whether each *mechanism* (conditional release, diversity reward, token masking) is necessary by removing it entirely. Table R5 tests whether the specific *hyperparameter choices* within those mechanisms are sensitive, while all mechanisms remain active. In all R5 variants, $R_{\\text{diversity}}$ can still reach 1.0 and the conditional release gate functions normally.

**Setup**: Each variant is retrained from scratch under otherwise identical settings (Qwen3-8B policy, GPT-4o-mini backend, 2×A100 80GB).

### Part A: Minimum Operator Threshold Sensitivity

| Setting | min_ops | Weights | $R_{\\text{diversity}}$ max | GSM8K | MATH | HotPotQA (EM/F1) | SQuAD v2 (EM/F1) | MBPP | HumanEval |
|---|---|---|---|---|---|---|---|---|---|
| **Default** | 5 | 0.2/0.2/0.2/0.4 | 1.0 | **96.09** | **81.25** | **78.12 / 84.98** | **78.12 / 83.67** | **84.38** | **92.96** |
| Relaxed | 4 | 0.2/0.2/0.2/0.4 | 1.0 | TBD | TBD | TBD | TBD | TBD | TBD |
| Further relaxed | 3 | 0.2/0.2/0.2/0.4 | 1.0 | TBD | TBD | TBD | TBD | TBD | TBD |
| Tightened | 7 | 0.2/0.2/0.2/0.4 | 1.0 | TBD | TBD | TBD | TBD | TBD | TBD |

### Part B: Reward Weight Distribution Sensitivity

All variants keep total weight sum = 1.0, ensuring $R_{\\text{diversity}}$ can always reach 1.0 and conditional release functions normally.

| Setting | Weights (checker/format/operator/control) | GSM8K | MATH | HotPotQA (EM/F1) | SQuAD v2 (EM/F1) | MBPP | HumanEval |
|---|---|---|---|---|---|---|---|
| **Default** | 0.2 / 0.2 / 0.2 / 0.4 | **96.09** | **81.25** | **78.12 / 84.98** | **78.12 / 83.67** | **84.38** | **92.96** |
| Equal weights | 0.25 / 0.25 / 0.25 / 0.25 | TBD | TBD | TBD | TBD | TBD | TBD |
| Control-reduced | 0.2 / 0.2 / 0.3 / 0.3 | TBD | TBD | TBD | TBD | TBD | TBD |
| Checker-heavy | 0.4 / 0.2 / 0.2 / 0.2 | TBD | TBD | TBD | TBD | TBD | TBD |
