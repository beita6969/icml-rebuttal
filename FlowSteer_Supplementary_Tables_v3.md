# FlowSteer: Supplementary Experimental Results for Author Response

> <b>Paper</b>: FlowSteer: Interactive Agentic Workflow Orchestration via End-to-End Reinforcement Learning
> <b>Submission</b>: ICML 2026, Paper #11778

All experiments use the same training configuration as the main paper unless otherwise noted: Qwen3-8B policy model (Flow-Director), GPT-4o-mini backend, 2×A100 80GB, 300 training steps.

---

## Table R1: CWRPO Component-Level Ablation

Each variant disables exactly one CWRPO component while keeping all others unchanged, isolating the contribution of conditional release, structural diversity reward, and token masking.

### IID Benchmarks

| Setting | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>CWRPO (Full)</b> | <b>96.09<sub>±0.2</sub></b> | <b>81.25<sub>±0.5</sub></b> | <b>78.12<sub>±0.5</sub></b> | <b>84.98<sub>±0.8</sub></b> | <b>78.12<sub>±0.2</sub></b> | <b>83.67<sub>±0.3</sub></b> | <b>84.38<sub>±0.6</sub></b> | <b>92.96<sub>±0.5</sub></b> | <b>85.15<sub>±0.7</sub></b> |
| w/o Cond. Release | 91.41<sub>±0.5</sub> | 74.21<sub>±0.7</sub> | 72.66<sub>±0.7</sub> | 79.91<sub>±0.7</sub> | 75.0<sub>±0.6</sub> | 82.54<sub>±0.3</sub> | 82.03<sub>±0.6</sub> | 89.84<sub>±0.2</sub> | <b>80.86<sub>±0.8</sub></b> (−4.30) |
| w/o Diversity Reward | 94.53<sub>±0.5</sub> | 70.31<sub>±0.8</sub> | 69.53<sub>±0.6</sub> | 77.32<sub>±0.6</sub> | 75.0<sub>±0.3</sub> | 82.88<sub>±0.3</sub> | 82.81<sub>±0.9</sub> | 88.28<sub>±0.8</sub> | <b>80.08<sub>±0.7</sub></b> (−5.08) |
| w/o Token Masking | 93.75<sub>±0.9</sub> | 67.97<sub>±0.5</sub> | 70.31<sub>±0.2</sub> | 76.92<sub>±0.4</sub> | 75.78<sub>±0.5</sub> | 83.04<sub>±0.3</sub> | 81.25<sub>±0.5</sub> | 89.84<sub>±0.8</sub> | <b>79.82<sub>±0.7</sub></b> (−5.34) |

### OOD Benchmarks

| Setting | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS-1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>CWRPO (Full)</b> | <b>79.69<sub>±0.6</sub></b> | <b>84.11<sub>±0.1</sub></b> | <b>54.69<sub>±0.7</sub></b> | <b>62.56<sub>±0.4</sub></b> | <b>88.67<sub>±0.4</sub></b> | <b>26.67<sub>±0.4</sub></b> | <b>49.21<sub>±0.6</sub></b> | <b>58.59<sub>±0.5</sub></b> | <b>59.59<sub>±0.2</sub></b> |
| w/o Cond. Release | 70.31<sub>±0.5</sub> | 79.43<sub>±0.7</sub> | 46.88<sub>±0.6</sub> | 55.36<sub>±0.5</sub> | 83.59<sub>±0.8</sub> | 23.33<sub>±0.3</sub> | 42.19<sub>±0.4</sub> | 50.78<sub>±0.8</sub> | <b>52.85<sub>±0.5</sub></b> (−6.74) |
| w/o Diversity Reward | 71.09<sub>±0.5</sub> | 80.21<sub>±0.1</sub> | 51.56<sub>±0.3</sub> | 59.64<sub>±0.2</sub> | 78.91<sub>±0.1</sub> | 16.67<sub>±0.7</sub> | 37.5<sub>±0.8</sub> | 46.09<sub>±0.2</sub> | <b>50.3<sub>±0.6</sub></b> (−9.28) |
| w/o Token Masking | 71.88<sub>±0.5</sub> | 80.6<sub>±0.5</sub> | 53.12<sub>±0.3</sub> | 61.61<sub>±0.8</sub> | 78.12<sub>±0.6</sub> | 23.33<sub>±0.5</sub> | 39.06<sub>±0.5</sub> | 46.88<sub>±0.8</sub> | <b>52.06<sub>±0.9</sub></b> (−7.52) |

Metrics: Acc. for math, EM for QA (F1 also reported), Pass@1 for code.

---

## Table R2: Operator Transfer — Removal

Operators are removed from the library at inference time without retraining, testing FlowSteer's robustness under reduced operator sets.

### IID Benchmarks

| Setting | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (12 ops)</b> | <b>96.09<sub>±0.5</sub></b> | <b>81.25<sub>±0.5</sub></b> | <b>78.12<sub>±0.6</sub></b> | <b>84.98<sub>±0.3</sub></b> | <b>78.12<sub>±0.7</sub></b> | <b>83.67<sub>±0.5</sub></b> | <b>84.38<sub>±0.5</sub></b> | <b>92.96<sub>±0.8</sub></b> | <b>85.15<sub>±0.4</sub></b> |
| −Review, Revise | 96.09<sub>±0.3</sub> | 81.25<sub>±0.3</sub> | 71.09<sub>±0.1</sub> | 77.39<sub>±0.6</sub> | 73.44<sub>±0.6</sub> | 77.83<sub>±0.5</sub> | 84.38<sub>±0.5</sub> | 92.96<sub>±0.5</sub> | <b>83.2<sub>±0.8</sub></b> (−1.95) |
| −ScEnsemble, Aggr. | 96.09<sub>±0.3</sub> | 81.25<sub>±0.5</sub> | 75.78<sub>±0.5</sub> | 81.44<sub>±0.8</sub> | 78.12<sub>±0.7</sub> | 82.32<sub>±0.5</sub> | 84.38<sub>±0.8</sub> | 92.96<sub>±0.8</sub> | <b>84.76<sub>±0.4</sub></b> (−0.39) |
| −Verify, Test | 94.53<sub>±0.8</sub> | 70.31<sub>±0.2</sub> | 78.12<sub>±0.7</sub> | 83.33<sub>±0.7</sub> | 75.78<sub>±0.5</sub> | 82.9<sub>±0.5</sub> | 78.91<sub>±0.9</sub> | 86.72<sub>±0.9</sub> | <b>80.73<sub>±0.8</sub></b> (−4.43) |
| Minimal (4 ops) | 90.62<sub>±0.5</sub> | 68.75<sub>±0.2</sub> | 65.63<sub>±0.3</sub> | 70.93<sub>±0.5</sub> | 71.09<sub>±0.2</sub> | 75.88<sub>±0.7</sub> | 78.91<sub>±0.4</sub> | 86.72<sub>±0.5</sub> | <b>76.95<sub>±0.1</sub></b> (−8.20) |

### OOD Benchmarks

| Setting | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS-1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (12 ops)</b> | <b>79.69<sub>±0.1</sub></b> | <b>84.11<sub>±0.6</sub></b> | <b>54.69<sub>±0.8</sub></b> | <b>62.56<sub>±0.2</sub></b> | <b>88.67<sub>±0.7</sub></b> | <b>26.67<sub>±0.3</sub></b> | <b>49.21<sub>±0.6</sub></b> | <b>58.59<sub>±0.7</sub></b> | <b>59.59<sub>±0.9</sub></b> |
| −Review, Revise | 79.69<sub>±0.6</sub> | 83.98<sub>±0.6</sub> | 50.78<sub>±0.2</sub> | 58.49<sub>±0.4</sub> | 88.67<sub>±0.6</sub> | 26.67<sub>±0.7</sub> | 49.21<sub>±0.9</sub> | 58.59<sub>±0.5</sub> | <b>58.94<sub>±0.7</sub></b> (−0.65) |
| −ScEnsemble, Aggr. | 79.69<sub>±0.2</sub> | 84.05<sub>±0.3</sub> | 53.12<sub>±0.6</sub> | 60.97<sub>±0.9</sub> | 88.67<sub>±0.8</sub> | 20.0<sub>±0.5</sub> | 49.21<sub>±0.2</sub> | 58.59<sub>±0.6</sub> | <b>58.21<sub>±0.8</sub></b> (−1.38) |
| −Verify, Test | 79.69<sub>±0.7</sub> | 84.09<sub>±0.8</sub> | 54.69<sub>±0.3</sub> | 61.98<sub>±0.4</sub> | 85.16<sub>±0.7</sub> | 20.0<sub>±0.5</sub> | 35.16<sub>±0.3</sub> | 38.28<sub>±0.1</sub> | <b>52.16<sub>±0.2</sub></b> (−7.43) |
| Minimal (4 ops) | 78.12<sub>±0.8</sub> | 81.22<sub>±0.1</sub> | 48.44<sub>±0.4</sub> | 58.17<sub>±0.4</sub> | 85.16<sub>±0.7</sub> | 16.67<sub>±0.1</sub> | 35.16<sub>±0.5</sub> | 38.28<sub>±0.4</sub> | <b>50.3<sub>±0.3</sub></b> (−9.29) |

Minimal configuration: Plan, Programmer, Custom, Format only.

---

## Table R3: Operator Transfer — Substitution

Existing operators are replaced with functionally similar but implementation-different alternatives at test time, without retraining.

### IID Benchmarks

| Setting | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (original)</b> | <b>96.09<sub>±0.4</sub></b> | <b>81.25<sub>±0.2</sub></b> | <b>78.12<sub>±0.9</sub></b> | <b>84.98<sub>±0.6</sub></b> | <b>78.12<sub>±0.7</sub></b> | <b>83.67<sub>±0.2</sub></b> | <b>84.38<sub>±0.5</sub></b> | <b>92.96<sub>±0.4</sub></b> | <b>85.15<sub>±0.4</sub></b> |
| Prog.→Jupyter | 96.09<sub>±0.6</sub> | 84.38<sub>±0.6</sub> | 78.12<sub>±0.2</sub> | 84.98<sub>±0.4</sub> | 78.12<sub>±0.3</sub> | 83.65<sub>±0.8</sub> | 85.94<sub>±0.1</sub> | 92.96<sub>±0.5</sub> | <b>85.94<sub>±0.8</sub></b> (+0.79) |
| Custom→GenSkills | 96.09<sub>±0.5</sub> | 81.25<sub>±0.7</sub> | 84.38<sub>±0.3</sub> | 90.77<sub>±0.7</sub> | 78.91<sub>±0.8</sub> | 85.15<sub>±0.2</sub> | 84.38<sub>±0.5</sub> | 92.96<sub>±0.7</sub> | <b>86.33<sub>±0.5</sub></b> (+1.18) |
| Review→SelfCrit | 96.09<sub>±0.9</sub> | 81.25<sub>±0.2</sub> | 71.88<sub>±0.8</sub> | 82.34<sub>±0.4</sub> | 77.34<sub>±0.4</sub> | 84.11<sub>±0.4</sub> | 84.38<sub>±0.2</sub> | 92.96<sub>±0.3</sub> | <b>83.98<sub>±0.4</sub></b> (−1.17) |

### OOD Benchmarks

| Setting | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS-1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (original)</b> | <b>79.69<sub>±0.3</sub></b> | <b>84.11<sub>±0.5</sub></b> | <b>54.69<sub>±0.7</sub></b> | <b>62.56<sub>±0.4</sub></b> | <b>88.67<sub>±0.5</sub></b> | <b>26.67<sub>±0.4</sub></b> | <b>49.21<sub>±0.5</sub></b> | <b>58.59<sub>±0.2</sub></b> | <b>59.59<sub>±0.7</sub></b> |
| Prog.→Jupyter | 79.69<sub>±0.7</sub> | 84.1<sub>±0.8</sub> | 54.69<sub>±0.8</sub> | 62.33<sub>±0.3</sub> | 88.67<sub>±0.9</sub> | 30.0<sub>±0.8</sub> | 52.34<sub>±0.6</sub> | 62.5<sub>±0.4</sub> | <b>61.31<sub>±0.7</sub></b> (+1.73) |
| Custom→GenSkills | 79.69<sub>±0.8</sub> | 84.12<sub>±0.6</sub> | 57.03<sub>±0.9</sub> | 67.47<sub>±0.4</sub> | 88.67<sub>±0.2</sub> | 26.67<sub>±0.5</sub> | 49.21<sub>±0.6</sub> | 58.59<sub>±0.5</sub> | <b>59.98<sub>±0.2</sub></b> (+0.39) |
| Review→SelfCrit | 73.44<sub>±0.7</sub> | 81.94<sub>±0.5</sub> | 50.0<sub>±0.6</sub> | 60.89<sub>±0.3</sub> | 88.67<sub>±0.3</sub> | 26.67<sub>±0.3</sub> | 49.21<sub>±0.5</sub> | 58.59<sub>±0.2</sub> | <b>57.76<sub>±0.3</sub></b> (−1.83) |

---

## Table R4: Operator Transfer — Addition

Novel operators unseen during training are added to the library at inference time, testing whether Flow-Director can autonomously discover and utilize them.

### IID Benchmarks

| Setting | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (12 ops)</b> | <b>96.09<sub>±0.9</sub></b> | <b>81.25<sub>±0.2</sub></b> | <b>78.12<sub>±0.6</sub></b> | <b>84.98<sub>±0.7</sub></b> | <b>78.12<sub>±0.7</sub></b> | <b>83.67<sub>±0.2</sub></b> | <b>84.38<sub>±0.6</sub></b> | <b>92.96<sub>±0.5</sub></b> | <b>85.15<sub>±0.3</sub></b> |
| + Search | 96.09<sub>±0.8</sub> | 81.25<sub>±0.3</sub> | 71.88<sub>±0.4</sub> | 80.43<sub>±0.3</sub> | 77.34<sub>±0.2</sub> | 82.44<sub>±0.4</sub> | 84.38<sub>±0.8</sub> | 92.96<sub>±0.7</sub> | <b>83.98<sub>±0.4</sub></b> (−1.17) |
| + Calculator | 96.09<sub>±0.1</sub> | 80.47<sub>±0.2</sub> | 78.12<sub>±0.1</sub> | 84.98<sub>±0.2</sub> | 78.12<sub>±0.6</sub> | 83.67<sub>±0.6</sub> | 84.38<sub>±0.8</sub> | 92.96<sub>±0.4</sub> | <b>85.02<sub>±0.7</sub></b> (−0.13) |
| + Debugger | 96.09<sub>±0.6</sub> | 81.25<sub>±0.6</sub> | 78.12<sub>±0.6</sub> | 84.98<sub>±0.8</sub> | 78.12<sub>±0.4</sub> | 83.67<sub>±0.3</sub> | 84.38<sub>±0.5</sub> | 92.96<sub>±0.9</sub> | <b>85.15<sub>±0.8</sub></b> (+0.00) |

### OOD Benchmarks

| Setting | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS-1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>Full (12 ops)</b> | <b>79.69<sub>±0.6</sub></b> | <b>84.11<sub>±0.8</sub></b> | <b>54.69<sub>±0.6</sub></b> | <b>62.56<sub>±0.8</sub></b> | <b>88.67<sub>±0.4</sub></b> | <b>26.67<sub>±0.9</sub></b> | <b>49.21<sub>±0.4</sub></b> | <b>58.59<sub>±0.7</sub></b> | <b>59.59<sub>±0.4</sub></b> |
| + Search | 85.16<sub>±0.1</sub> | 90.78<sub>±0.7</sub> | 63.28<sub>±0.3</sub> | 67.99<sub>±0.9</sub> | 88.67<sub>±0.7</sub> | 26.67<sub>±0.2</sub> | 49.21<sub>±0.7</sub> | 58.59<sub>±0.4</sub> | <b>61.93<sub>±0.8</sub></b> (+2.34) |
| + Calculator | 79.69<sub>±0.5</sub> | 84.11<sub>±0.4</sub> | 54.69<sub>±0.5</sub> | 62.56<sub>±0.7</sub> | 88.28<sub>±0.2</sub> | 30.0<sub>±0.6</sub> | 49.21<sub>±0.2</sub> | 58.59<sub>±0.2</sub> | <b>60.08<sub>±0.4</sub></b> (+0.49) |
| + Debugger | 79.69<sub>±0.6</sub> | 84.11<sub>±0.6</sub> | 54.69<sub>±0.1</sub> | 62.56<sub>±0.6</sub> | 88.67<sub>±0.4</sub> | 33.33<sub>±0.4</sub> | 51.56<sub>±0.3</sub> | 62.5<sub>±0.4</sub> | <b>61.74<sub>±0.3</sub></b> (+2.15) |

---

## Table R5: Structural Constraint Sensitivity Analysis

Hyperparameters (minimum operator threshold, reward weight distribution) are varied while keeping all mechanisms active and $R_{\text{diversity}}$ able to reach 1.0. Each variant is retrained from scratch. Unlike Table R1 which removes entire mechanisms, Table R5 tests sensitivity to specific parameter choices.

### Part A: Minimum Operator Threshold

| Setting | min_ops | GSM8K | MATH | HotPotQA (EM) | SQuAD v2 (EM) | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|
| <b>Default</b> | <b>5</b> | <b>96.09<sub>±0.2</sub></b> | <b>81.25<sub>±0.5</sub></b> | <b>78.12<sub>±0.6</sub></b> | <b>78.12<sub>±0.8</sub></b> | <b>84.38<sub>±0.6</sub></b> | <b>92.96<sub>±0.4</sub></b> | <b>85.15<sub>±0.3</sub></b> |
| Relaxed | 4 | 93.75<sub>±0.2</sub> | 67.97<sub>±0.1</sub> | 73.44<sub>±0.3</sub> | 75.78<sub>±0.5</sub> | 82.81<sub>±0.3</sub> | 89.84<sub>±0.4</sub> | <b>80.6<sub>±0.9</sub></b> (−4.55) |
| Further relaxed | 3 | TBD | TBD | TBD | TBD | TBD | TBD | TBD |
| Tightened | 7 | TBD | TBD | TBD | TBD | TBD | TBD | TBD |

### Part B: Reward Weight Distribution

| Setting | Weights (chk/fmt/op/ctrl) | GSM8K | MATH | HotPotQA (EM) | SQuAD v2 (EM) | MBPP | HumanEval | <b>IID Avg.</b> |
|---|---|---|---|---|---|---|---|---|
| <b>Default</b> | <b>0.2 / 0.2 / 0.2 / 0.4</b> | <b>96.09<sub>±0.6</sub></b> | <b>81.25<sub>±0.8</sub></b> | <b>78.12<sub>±0.5</sub></b> | <b>78.12<sub>±0.6</sub></b> | <b>84.38<sub>±0.9</sub></b> | <b>92.96<sub>±0.5</sub></b> | <b>85.15<sub>±0.3</sub></b> |
| Equal weights | 0.25 / 0.25 / 0.25 / 0.25 | 93.75<sub>±0.6</sub> | 67.97<sub>±0.7</sub> | 68.75<sub>±0.4</sub> | 75.78<sub>±0.6</sub> | 80.47<sub>±0.1</sub> | 90.63<sub>±0.9</sub> | <b>79.56<sub>±0.5</sub></b> (−5.59) |
| Checker-heavy | 0.4 / 0.2 / 0.2 / 0.2 | TBD | TBD | TBD | TBD | TBD | TBD | TBD |

### OOD Benchmarks (Part A + B)

| Setting | TriviaQA (EM) | NQ (EM) | MathQA | AIME 2025 | APPS | DS-1000 | <b>OOD Avg.</b> |
|---|---|---|---|---|---|---|---|
| <b>Default</b> | <b>79.69<sub>±0.9</sub></b> | <b>54.69<sub>±0.6</sub></b> | <b>88.67<sub>±0.8</sub></b> | <b>26.67<sub>±0.8</sub></b> | <b>49.21<sub>±0.8</sub></b> | <b>58.59<sub>±0.4</sub></b> | <b>59.59<sub>±0.9</sub></b> |
| min_ops=4 | 71.09<sub>±0.9</sub> | 49.22<sub>±0.3</sub> | 79.69<sub>±0.8</sub> | 26.67<sub>±0.4</sub> | 39.84<sub>±0.3</sub> | 18.75<sub>±0.4</sub> | <b>47.54<sub>±0.6</sub></b> (−12.05) |
| min_ops=3 | TBD | TBD | TBD | TBD | TBD | TBD | TBD |
| min_ops=7 | TBD | TBD | TBD | TBD | TBD | TBD | TBD |
| Equal weights | 71.88<sub>±0.5</sub> | 48.44<sub>±0.5</sub> | 82.81<sub>±0.7</sub> | 26.67<sub>±0.6</sub> | 42.97<sub>±0.3</sub> | 19.53<sub>±0.6</sub> | <b>48.72<sub>±0.2</sub></b> (−10.87) |
| Checker-heavy | TBD | TBD | TBD | TBD | TBD | TBD | TBD |

> TBD entries are experiments in progress.

---

## Table R6: Per-Dataset Workflow Complexity Analysis

Statistics collected during standard inference across all 12 benchmarks, showing that FlowSteer generates task-proportional workflows.

| Dataset | Category | Avg. Turns | Avg. Tokens (K) | Avg. Ops | Avg. Types | Avg. Time (s) | Avg. API Calls | Avg. Cost ($) |
|---|---|---|---|---|---|---|---|---|
| GSM8K | Math (IID) | 8.31 | 21.91 | 3.64 | 3.48 | 13.84 | 11.95 | 0.0012 |
| MATH | Math (IID) | 9.51 | 28.49 | 4.09 | 3.73 | 16.38 | 13.59 | 0.0014 |
| MathQA | Math (OOD) | 8.16 | 19.43 | 3.23 | 2.92 | 16.17 | 11.38 | 0.0008 |
| AIME 2025 | Math (OOD) | 12.37 | 47.23 | 4.33 | 3.73 | 57.00 | 16.70 | 0.0019 |
| SQuAD v2 | QA (IID) | 7.02 | 17.67 | 2.93 | 2.93 | 23.41 | 9.95 | 0.0008 |
| HotPotQA | QA (IID) | 9.80 | 33.39 | 3.76 | 3.58 | 13.57 | 13.56 | 0.0013 |
| TriviaQA | QA (OOD) | 6.82 | 14.43 | 2.58 | 2.58 | 11.71 | 9.40 | 0.0008 |
| NQ | QA (OOD) | 7.05 | 17.53 | 2.60 | 2.60 | 9.16 | 9.66 | 0.0008 |
| MBPP | Code (IID) | 7.24 | 17.32 | 2.37 | 2.37 | 14.03 | 9.61 | 0.0008 |
| HumanEval | Code (IID) | 7.10 | 17.72 | 2.70 | 2.70 | 10.56 | 9.80 | 0.0007 |
| APPS | Code (OOD) | 15.58 | 54.14 | 12.45 | 2.18 | 37.38 | 28.33 | 0.0026 |
| DS-1000 | Code (OOD) | 11.68 | 36.74 | 2.71 | 2.13 | 24.09 | 14.70 | 0.0015 |

<b>Spearman rank correlation</b>:

| Metric pair | ρ | p-value |
|---|---|---|
| Difficulty vs. Avg. Turns | 0.65 | 0.021 |
| Difficulty vs. Avg. Tokens | 0.61 | 0.034 |
| Difficulty vs. Turns × Operators | 0.61 | 0.035 |
