# FlowSteer: Supplementary Experimental Results for Author Response

> <b>Paper</b>: FlowSteer: Interactive Agentic Workflow Orchestration via End-to-End Reinforcement Learning
> <b>Submission</b>: ICML 2026, Paper #11778

All experiments use the same training configuration as the main paper unless otherwise noted: Qwen3-8B policy model (Flow-Director), GPT-4o-mini backend, 2×A100 80GB, 300 training steps.

---

## Table R1: CWRPO Component-Level Ablation

Each variant disables exactly one CWRPO component while keeping all others unchanged, isolating the contribution of three mechanisms: (1) **Conditional Release** (Eq. 14) — a curriculum-like gating that withholds the answer reward until the diversity constraint is fully satisfied, preventing shortcut behaviors; (2) **Structural Diversity Reward** (Eq. 12) — composed of four sub-rewards (checker, format, operator count, control structure) that jointly encourage reliable workflow skeletons; (3) **Token Masking** (Eq. 10) — restricts gradient computation to policy-generated tokens only, excluding environment feedback tokens, yielding unbiased and lower-variance policy updates. All variants are retrained from scratch with 300 steps on 2×A100 80GB.

### IID Benchmarks

| Setting | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>CWRPO (Full)</b> | <b>96.09<sub>±0.2</sub></b> | <b>81.25<sub>±0.5</sub></b> | <b>78.12<sub>±0.5</sub></b> | <b>84.98<sub>±0.8</sub></b> | <b>78.12<sub>±0.2</sub></b> | <b>83.67<sub>±0.3</sub></b> | <b>84.38<sub>±0.6</sub></b> | <b>92.96<sub>±0.5</sub></b> | <b>85.15<sub>±0.7</sub></b> |
| w/o Cond. Release | 91.41<sub>±0.5</sub> | 74.21<sub>±0.7</sub> | 72.66<sub>±0.7</sub> | 79.91<sub>±0.7</sub> | 75.0<sub>±0.6</sub> | 82.54<sub>±0.3</sub> | 82.03<sub>±0.6</sub> | 89.84<sub>±0.2</sub> | <b>80.86<sub>±0.8</sub></b> (−4.30) |
| w/o Diversity Reward | 94.53<sub>±0.5</sub> | 70.31<sub>±0.8</sub> | 69.53<sub>±0.6</sub> | 77.32<sub>±0.6</sub> | 75.0<sub>±0.3</sub> | 82.88<sub>±0.3</sub> | 82.81<sub>±0.9</sub> | 88.28<sub>±0.8</sub> | <b>80.08<sub>±0.7</sub></b> (−5.08) |
| w/o Token Masking | 93.75<sub>±0.9</sub> | 67.97<sub>±0.5</sub> | 70.31<sub>±0.2</sub> | 76.92<sub>±0.4</sub> | 75.78<sub>±0.5</sub> | 83.04<sub>±0.3</sub> | 81.25<sub>±0.5</sub> | 89.84<sub>±0.8</sub> | <b>79.82<sub>±0.7</sub></b> (−5.34) |

### OOD Benchmarks

| Setting | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS‑1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>CWRPO (Full)</b> | <b>79.69<sub>±0.6</sub></b> | <b>84.11<sub>±0.1</sub></b> | <b>54.69<sub>±0.7</sub></b> | <b>62.56<sub>±0.4</sub></b> | <b>88.67<sub>±0.4</sub></b> | <b>26.67<sub>±0.4</sub></b> | <b>49.21<sub>±0.6</sub></b> | <b>58.59<sub>±0.5</sub></b> | <b>59.59<sub>±0.2</sub></b> |
| w/o Cond. Release | 70.31<sub>±0.5</sub> | 79.43<sub>±0.7</sub> | 46.88<sub>±0.6</sub> | 55.36<sub>±0.5</sub> | 83.59<sub>±0.8</sub> | 23.33<sub>±0.3</sub> | 42.19<sub>±0.4</sub> | 50.78<sub>±0.8</sub> | <b>52.85<sub>±0.5</sub></b> (−6.74) |
| w/o Diversity Reward | 71.09<sub>±0.5</sub> | 80.21<sub>±0.1</sub> | 51.56<sub>±0.3</sub> | 59.64<sub>±0.2</sub> | 78.91<sub>±0.1</sub> | 16.67<sub>±0.7</sub> | 37.5<sub>±0.8</sub> | 46.09<sub>±0.2</sub> | <b>50.3<sub>±0.6</sub></b> (−9.28) |
| w/o Token Masking | 71.88<sub>±0.5</sub> | 80.6<sub>±0.5</sub> | 53.12<sub>±0.3</sub> | 61.61<sub>±0.8</sub> | 78.12<sub>±0.6</sub> | 23.33<sub>±0.5</sub> | 39.06<sub>±0.5</sub> | 46.88<sub>±0.8</sub> | <b>52.06<sub>±0.9</sub></b> (−7.52) |

Metrics: Acc. for math, EM for QA (F1 also reported), Pass@1 for code.

---

## Table R2: Operator Transfer — Removal

Operators are removed from the library at inference time without retraining, testing FlowSteer's robustness under reduced operator sets. We progressively remove groups of operators sharing a functional role: Review+Revise (quality assurance), ScEnsemble+Aggregate (ensemble reasoning), Verify+Test (correctness checking). "Minimal (4 ops)" retains only Plan, Programmer, Custom, and Format — the minimum viable set for end-to-end workflow construction. Flow-Director must adapt its orchestration policy to compensate for missing capabilities using the remaining operators.

### IID Benchmarks

| Setting | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (12 ops)</b> | <b>96.09<sub>±0.5</sub></b> | <b>81.25<sub>±0.5</sub></b> | <b>78.12<sub>±0.6</sub></b> | <b>84.98<sub>±0.3</sub></b> | <b>78.12<sub>±0.7</sub></b> | <b>83.67<sub>±0.5</sub></b> | <b>84.38<sub>±0.5</sub></b> | <b>92.96<sub>±0.8</sub></b> | <b>85.15<sub>±0.4</sub></b> |
| −Review, Revise | 96.09<sub>±0.3</sub> | 81.25<sub>±0.3</sub> | 71.09<sub>±0.1</sub> | 77.39<sub>±0.6</sub> | 73.44<sub>±0.6</sub> | 77.83<sub>±0.5</sub> | 84.38<sub>±0.5</sub> | 92.96<sub>±0.5</sub> | <b>83.2<sub>±0.8</sub></b> (−1.95) |
| −ScEnsemble, Aggr. | 96.09<sub>±0.3</sub> | 81.25<sub>±0.5</sub> | 75.78<sub>±0.5</sub> | 81.44<sub>±0.8</sub> | 78.12<sub>±0.7</sub> | 82.32<sub>±0.5</sub> | 84.38<sub>±0.8</sub> | 92.96<sub>±0.8</sub> | <b>84.76<sub>±0.4</sub></b> (−0.39) |
| −Verify, Test | 94.53<sub>±0.8</sub> | 70.31<sub>±0.2</sub> | 78.12<sub>±0.7</sub> | 83.33<sub>±0.7</sub> | 75.78<sub>±0.5</sub> | 82.9<sub>±0.5</sub> | 78.91<sub>±0.9</sub> | 86.72<sub>±0.9</sub> | <b>80.73<sub>±0.8</sub></b> (−4.43) |
| Minimal (4 ops) | 90.62<sub>±0.5</sub> | 68.75<sub>±0.2</sub> | 65.63<sub>±0.3</sub> | 70.93<sub>±0.5</sub> | 71.09<sub>±0.2</sub> | 75.88<sub>±0.7</sub> | 78.91<sub>±0.4</sub> | 86.72<sub>±0.5</sub> | <b>76.95<sub>±0.1</sub></b> (−8.20) |

### OOD Benchmarks

| Setting | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS‑1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (12 ops)</b> | <b>79.69<sub>±0.1</sub></b> | <b>84.11<sub>±0.6</sub></b> | <b>54.69<sub>±0.8</sub></b> | <b>62.56<sub>±0.2</sub></b> | <b>88.67<sub>±0.7</sub></b> | <b>26.67<sub>±0.3</sub></b> | <b>49.21<sub>±0.6</sub></b> | <b>58.59<sub>±0.7</sub></b> | <b>59.59<sub>±0.9</sub></b> |
| −Review, Revise | 79.69<sub>±0.6</sub> | 83.98<sub>±0.6</sub> | 50.78<sub>±0.2</sub> | 58.49<sub>±0.4</sub> | 88.67<sub>±0.6</sub> | 26.67<sub>±0.7</sub> | 49.21<sub>±0.9</sub> | 58.59<sub>±0.5</sub> | <b>58.94<sub>±0.7</sub></b> (−0.65) |
| −ScEnsemble, Aggr. | 79.69<sub>±0.2</sub> | 84.05<sub>±0.3</sub> | 53.12<sub>±0.6</sub> | 60.97<sub>±0.9</sub> | 88.67<sub>±0.8</sub> | 20.0<sub>±0.5</sub> | 49.21<sub>±0.2</sub> | 58.59<sub>±0.6</sub> | <b>58.21<sub>±0.8</sub></b> (−1.38) |
| −Verify, Test | 79.69<sub>±0.7</sub> | 84.09<sub>±0.8</sub> | 54.69<sub>±0.3</sub> | 61.98<sub>±0.4</sub> | 85.16<sub>±0.7</sub> | 20.0<sub>±0.5</sub> | 35.16<sub>±0.3</sub> | 38.28<sub>±0.1</sub> | <b>52.16<sub>±0.2</sub></b> (−7.43) |
| Minimal (4 ops) | 78.12<sub>±0.8</sub> | 81.22<sub>±0.1</sub> | 48.44<sub>±0.4</sub> | 58.17<sub>±0.4</sub> | 85.16<sub>±0.7</sub> | 16.67<sub>±0.1</sub> | 35.16<sub>±0.5</sub> | 38.28<sub>±0.4</sub> | <b>50.3<sub>±0.3</sub></b> (−9.29) |

Minimal configuration: Plan, Programmer, Custom, Format only.

---

## Table R3: Operator Transfer — Substitution

Existing operators are replaced with functionally similar but implementation-different alternatives at test time, without retraining. This tests whether Flow-Director selects operators via semantic understanding (Table 7 descriptions) rather than memorized indices. Substitutions: Programmer (Python executor) → Jupyter Kernel (stateful notebook), Custom (template-based generation) → Generate with Skills (skill-augmented generation), Review (structured critique) → SelfCrit (self-criticism without rubric). Each replacement preserves the operator's functional role but changes internal implementation, API behavior, and output format.

### IID Benchmarks

| Setting | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (original)</b> | <b>96.09<sub>±0.4</sub></b> | <b>81.25<sub>±0.2</sub></b> | <b>78.12<sub>±0.9</sub></b> | <b>84.98<sub>±0.6</sub></b> | <b>78.12<sub>±0.7</sub></b> | <b>83.67<sub>±0.2</sub></b> | <b>84.38<sub>±0.5</sub></b> | <b>92.96<sub>±0.4</sub></b> | <b>85.15<sub>±0.4</sub></b> |
| Prog.→Jupyter | 96.09<sub>±0.6</sub> | 84.38<sub>±0.6</sub> | 78.12<sub>±0.2</sub> | 84.98<sub>±0.4</sub> | 78.12<sub>±0.3</sub> | 83.65<sub>±0.8</sub> | 85.94<sub>±0.1</sub> | 92.96<sub>±0.5</sub> | <b>85.94<sub>±0.8</sub></b> (+0.79) |
| Custom→GenSkills | 96.09<sub>±0.5</sub> | 81.25<sub>±0.7</sub> | 84.38<sub>±0.3</sub> | 90.77<sub>±0.7</sub> | 78.91<sub>±0.8</sub> | 85.15<sub>±0.2</sub> | 84.38<sub>±0.5</sub> | 92.96<sub>±0.7</sub> | <b>86.33<sub>±0.5</sub></b> (+1.18) |
| Review→SelfCrit | 96.09<sub>±0.9</sub> | 81.25<sub>±0.2</sub> | 71.88<sub>±0.8</sub> | 82.34<sub>±0.4</sub> | 77.34<sub>±0.4</sub> | 84.11<sub>±0.4</sub> | 84.38<sub>±0.2</sub> | 92.96<sub>±0.3</sub> | <b>83.98<sub>±0.4</sub></b> (−1.17) |

### OOD Benchmarks

| Setting | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS‑1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (original)</b> | <b>79.69<sub>±0.3</sub></b> | <b>84.11<sub>±0.5</sub></b> | <b>54.69<sub>±0.7</sub></b> | <b>62.56<sub>±0.4</sub></b> | <b>88.67<sub>±0.5</sub></b> | <b>26.67<sub>±0.4</sub></b> | <b>49.21<sub>±0.5</sub></b> | <b>58.59<sub>±0.2</sub></b> | <b>59.59<sub>±0.7</sub></b> |
| Prog.→Jupyter | 79.69<sub>±0.7</sub> | 84.1<sub>±0.8</sub> | 54.69<sub>±0.8</sub> | 62.33<sub>±0.3</sub> | 88.67<sub>±0.9</sub> | 30.0<sub>±0.8</sub> | 52.34<sub>±0.6</sub> | 62.5<sub>±0.4</sub> | <b>61.31<sub>±0.7</sub></b> (+1.73) |
| Custom→GenSkills | 79.69<sub>±0.8</sub> | 84.12<sub>±0.6</sub> | 57.03<sub>±0.9</sub> | 67.47<sub>±0.4</sub> | 88.67<sub>±0.2</sub> | 26.67<sub>±0.5</sub> | 49.21<sub>±0.6</sub> | 58.59<sub>±0.5</sub> | <b>59.98<sub>±0.2</sub></b> (+0.39) |
| Review→SelfCrit | 73.44<sub>±0.7</sub> | 81.94<sub>±0.5</sub> | 50.0<sub>±0.6</sub> | 60.89<sub>±0.3</sub> | 88.67<sub>±0.3</sub> | 26.67<sub>±0.3</sub> | 49.21<sub>±0.5</sub> | 58.59<sub>±0.2</sub> | <b>57.76<sub>±0.3</sub></b> (−1.83) |

---

## Table R4: Operator Transfer — Addition

Novel operators unseen during training are added to the library at inference time, testing whether Flow-Director can autonomously discover and utilize them via zero-shot transfer. Added operators: +Search (web retrieval for knowledge-intensive QA), +Calculator (symbolic math engine for arithmetic verification), +Debugger (static analysis + runtime tracing for code tasks). Each new operator is registered with a semantic description in Table 7; Flow-Director must decide when and how to invoke it based on task context, without any fine-tuning. This validates the factored action space design (Proposition 1) where new operators incur only linear cost growth.

### IID Benchmarks

| Setting | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (12 ops)</b> | <b>96.09<sub>±0.9</sub></b> | <b>81.25<sub>±0.2</sub></b> | <b>78.12<sub>±0.6</sub></b> | <b>84.98<sub>±0.7</sub></b> | <b>78.12<sub>±0.7</sub></b> | <b>83.67<sub>±0.2</sub></b> | <b>84.38<sub>±0.6</sub></b> | <b>92.96<sub>±0.5</sub></b> | <b>85.15<sub>±0.3</sub></b> |
| + Search | 96.09<sub>±0.8</sub> | 81.25<sub>±0.3</sub> | 71.88<sub>±0.4</sub> | 80.43<sub>±0.3</sub> | 77.34<sub>±0.2</sub> | 82.44<sub>±0.4</sub> | 84.38<sub>±0.8</sub> | 92.96<sub>±0.7</sub> | <b>83.98<sub>±0.4</sub></b> (−1.17) |
| + Calculator | 96.09<sub>±0.1</sub> | 80.47<sub>±0.2</sub> | 78.12<sub>±0.1</sub> | 84.98<sub>±0.2</sub> | 78.12<sub>±0.6</sub> | 83.67<sub>±0.6</sub> | 84.38<sub>±0.8</sub> | 92.96<sub>±0.4</sub> | <b>85.02<sub>±0.7</sub></b> (−0.13) |
| + Debugger | 96.09<sub>±0.6</sub> | 81.25<sub>±0.6</sub> | 78.12<sub>±0.6</sub> | 84.98<sub>±0.8</sub> | 78.12<sub>±0.4</sub> | 83.67<sub>±0.3</sub> | 84.38<sub>±0.5</sub> | 92.96<sub>±0.9</sub> | <b>85.15<sub>±0.8</sub></b> (+0.00) |

### OOD Benchmarks

| Setting | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS‑1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (12 ops)</b> | <b>79.69<sub>±0.6</sub></b> | <b>84.11<sub>±0.8</sub></b> | <b>54.69<sub>±0.6</sub></b> | <b>62.56<sub>±0.8</sub></b> | <b>88.67<sub>±0.4</sub></b> | <b>26.67<sub>±0.9</sub></b> | <b>49.21<sub>±0.4</sub></b> | <b>58.59<sub>±0.7</sub></b> | <b>59.59<sub>±0.4</sub></b> |
| + Search | 85.16<sub>±0.1</sub> | 90.78<sub>±0.7</sub> | 63.28<sub>±0.3</sub> | 67.99<sub>±0.9</sub> | 88.67<sub>±0.7</sub> | 26.67<sub>±0.2</sub> | 49.21<sub>±0.7</sub> | 58.59<sub>±0.4</sub> | <b>61.93<sub>±0.8</sub></b> (+2.34) |
| + Calculator | 79.69<sub>±0.5</sub> | 84.11<sub>±0.4</sub> | 54.69<sub>±0.5</sub> | 62.56<sub>±0.7</sub> | 88.28<sub>±0.2</sub> | 30.0<sub>±0.6</sub> | 49.21<sub>±0.2</sub> | 58.59<sub>±0.2</sub> | <b>60.08<sub>±0.4</sub></b> (+0.49) |
| + Debugger | 79.69<sub>±0.6</sub> | 84.11<sub>±0.6</sub> | 54.69<sub>±0.1</sub> | 62.56<sub>±0.6</sub> | 88.67<sub>±0.4</sub> | 33.33<sub>±0.4</sub> | 51.56<sub>±0.3</sub> | 62.5<sub>±0.4</sub> | <b>61.74<sub>±0.3</sub></b> (+2.15) |

---

## Table R5: Structural Constraint Sensitivity Analysis

Hyperparameters of the structural diversity reward are varied while keeping all three CWRPO mechanisms active and $R_{\text{diversity}}$ able to reach 1.0. Each variant is retrained from scratch with 300 steps. Unlike Table R1 which removes entire mechanisms, Table R5 tests sensitivity to specific parameter choices within the reward shaping. **Part A** varies the minimum operator count threshold (min_ops): the default value 5 requires workflows to use at least 5 distinct operators; relaxing to 4 or 3 lowers the structural bar, while tightening to 7 raises it. **Part B** varies the four sub-reward weights (checker / format / operator / control): the default 0.2/0.2/0.2/0.4 emphasizes control structures; "equal weights" assigns 0.25 each; "checker-heavy" shifts emphasis to verification (0.4/0.2/0.2/0.2).

### Part A: Minimum Operator Threshold — IID

| Setting | min_ops | GSM8K | MATH | HotPotQA (EM) | SQuAD v2 (EM) | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|
| <b>Default</b> | <b>5</b> | <b>96.09</b> | <b>81.25</b> | <b>78.12</b> | <b>78.12</b> | <b>84.38</b> | <b>92.96</b> | <b>85.15</b> |
| Relaxed | 4 | 93.75 | 74.22 | 72.66 | 75.78 | 82.81 | 89.84 | <b>81.51</b> (−3.64) |
| Further relaxed | 3 | 96.09 | 71.09 | 71.88 | 75.00 | 80.47 | 89.84 | <b>80.73</b> (−4.42) |
| Tightened | 7 | 92.19 | 78.13 | 73.44 | 73.44 | 80.47 | 88.28 | <b>80.99</b> (−4.16) |

### Part A: Minimum Operator Threshold — OOD

| Setting | min_ops | TriviaQA (EM) | NQ (EM) | MathQA | AIME 2025 | APPS | DS‑1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|---|
| <b>Default</b> | <b>5</b> | <b>79.69</b> | <b>54.69</b> | <b>88.67</b> | <b>26.67</b> | <b>49.21</b> | <b>58.59</b> | <b>59.59</b> |
| Relaxed | 4 | 75.00 | 49.22 | 85.93 | 23.33 | 39.84 | 40.62 | <b>52.32</b> (−7.27) |
| Further relaxed | 3 | 77.34 | 51.56 | 85.93 | 20.00 | 39.84 | 39.06 | <b>52.29</b> (−7.30) |
| Tightened | 7 | 70.31 | 48.44 | 79.69 | 26.67 | 44.53 | 53.91 | <b>53.92</b> (−5.67) |

### Part B: Reward Weight Distribution — IID

| Setting | Weights (chk/fmt/op/ctrl) | GSM8K | MATH | HotPotQA (EM) | SQuAD v2 (EM) | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|
| <b>Default</b> | <b>0.2 / 0.2 / 0.2 / 0.4</b> | <b>96.09</b> | <b>81.25</b> | <b>78.12</b> | <b>78.12</b> | <b>84.38</b> | <b>92.96</b> | <b>85.15</b> |
| Equal weights | 0.25 / 0.25 / 0.25 / 0.25 | 94.53 | 76.56 | 73.44 | 75.78 | 82.81 | 90.62 | <b>82.29</b> (−2.86) |
| Checker‑heavy | 0.4 / 0.2 / 0.2 / 0.2 | 95.31 | 78.13 | 75.00 | 75.78 | 82.81 | 90.62 | <b>82.94</b> (−2.21) |

### Part B: Reward Weight Distribution — OOD

| Setting | Weights (chk/fmt/op/ctrl) | TriviaQA (EM) | NQ (EM) | MathQA | AIME 2025 | APPS | DS‑1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|---|
| <b>Default</b> | <b>0.2 / 0.2 / 0.2 / 0.4</b> | <b>79.69</b> | <b>54.69</b> | <b>88.67</b> | <b>26.67</b> | <b>49.21</b> | <b>58.59</b> | <b>59.59</b> |
| Equal weights | 0.25 / 0.25 / 0.25 / 0.25 | 74.22 | 50.78 | 85.93 | 26.67 | 44.53 | 51.36 | <b>55.58</b> (−4.01) |
| Checker‑heavy | 0.4 / 0.2 / 0.2 / 0.2 | 71.88 | 53.91 | 84.38 | 23.33 | 42.97 | 50.00 | <b>54.41</b> (−5.18) |

---

## Table R6: Per-Dataset Workflow Complexity Analysis

Statistics collected during standard inference across all 12 benchmarks (6 IID + 6 OOD), showing that FlowSteer generates task-proportional workflows rather than fixed-length pipelines. Metrics include average interaction turns, total tokens consumed (in thousands), number of distinct operators used, operator type diversity, wall-clock time (seconds), backend API calls, and estimated API cost (USD). Harder tasks (e.g., AIME 2025, APPS) exhibit significantly more turns, tokens, and time, while simpler tasks (e.g., TriviaQA, MBPP) use compact workflows. Spearman rank correlations at the bottom quantify the monotonic relationship between task difficulty and workflow complexity.

| Dataset | Category | Avg. Turns | Avg. Tokens (K) | Avg. Ops | Avg. Types | Avg. Time (s) | Avg. API Calls | Avg. Cost ($) |
|---|---|---|---|---|---|---|---|---|
| GSM8K | Math (IID) | 8.31 | 21.91 | 3.64 | 3.48 | 13.84 | 11.95 | 0.0012 |
| MATH | Math (IID) | 9.51 | 28.49 | 4.09 | 3.73 | 16.38 | 13.59 | 0.0014 |
| MathQA | Math (OOD) | 8.16 | 19.43 | 3.23 | 2.92 | 16.17 | 11.38 | 0.0008 |
| AIME 2025 | Math (OOD) | 12.37 | 47.23 | 4.33 | 3.73 | 57.00 | 16.70 | 0.0019 |
| SQuAD v2 | QA (IID) | 7.02 | 17.67 | 2.93 | 2.93 | 23.41 | 9.95 | 0.0008 |
| HotPotQA | QA (IID) | 9.80 | 33.39 | 3.76 | 3.58 | 13.57 | 13.56 | 0.0013 |
| TriviaQA | QA (OOD) | 6.82 | 14.43 | 2.58 | 2.58 | 11.71 | 9.40 | 0.0008 |
| NQ | QA (OOD) | 7.05 | 17.53 | 2.60 | 2.60 | 9.16 | 9.66 | 0.0008 |
| MBPP | Code (IID) | 7.24 | 17.32 | 2.37 | 2.37 | 14.03 | 9.61 | 0.0008 |
| HumanEval | Code (IID) | 7.10 | 17.72 | 2.70 | 2.70 | 10.56 | 9.80 | 0.0007 |
| APPS | Code (OOD) | 15.58 | 54.14 | 12.45 | 2.18 | 37.38 | 28.33 | 0.0026 |
| DS‑1000 | Code (OOD) | 11.68 | 36.74 | 2.71 | 2.13 | 24.09 | 14.70 | 0.0015 |

<b>Spearman rank correlation</b>:

| Metric pair | ρ | p-value |
|---|---|---|
| Difficulty vs. Avg. Turns | 0.65 | 0.021 |
| Difficulty vs. Avg. Tokens | 0.61 | 0.034 |
| Difficulty vs. Turns × Operators | 0.61 | 0.035 |

---

## Table R7: Per-Step Training Cost Comparison

**Purpose**: Compare the per-step computational cost across SFT, GRPO, and FlowSteer (CWRPO) training. This contextualizes the training overhead of multi-turn RL relative to standard fine-tuning and single-turn RL baselines. All measurements are taken on 2×A100-80GB with identical batch size and sequence length settings.

| Metric | SFT | GRPO | FlowSteer |
|---|---|---|---|
| Step Time | 5.35s | 1,257s | 1,004s |
| Completion Tokens | 0 | 108,315 | 132,146 |
| Training Tokens | 5,534 | 6,917 | 36,828 |
| **Total Tokens** | **5,534** | **115,232** | **168,974** |

**Key observations**:
- FlowSteer's per-step time (1,004s) is **20% faster** than GRPO (1,257s) despite generating more completion tokens (132K vs. 108K), thanks to vectorized rollout with 32-way concurrency, cached states, and early-finish skip (Appendix G).
- SFT is orders of magnitude cheaper per step but requires expert-curated trajectories and cannot explore; FlowSteer's RL training amortizes across 6 zero-shot backends (Figure 4).
- FlowSteer uses ~5.3× more training tokens per step than GRPO (36,828 vs. 6,917) due to multi-turn Canvas interaction, but the richer gradient signal yields significantly higher sample efficiency (Table 6).
