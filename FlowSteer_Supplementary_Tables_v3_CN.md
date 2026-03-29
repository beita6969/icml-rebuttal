# FlowSteer：作者回复补充实验结果

> <b>论文</b>：FlowSteer: Interactive Agentic Workflow Orchestration via End-to-End Reinforcement Learning
> <b>投稿编号</b>：ICML 2026，Paper #11778

除特别说明外，所有实验均使用与正文相同的训练配置：Qwen3-8B 策略模型（Flow-Director），GPT-4o-mini 后端，2×A100 80GB，300 训练步。

---

## 表 R1：CWRPO 组件级消融

每个变体仅禁用一个 CWRPO 组件（条件释放 / 结构多样性奖励 / Token 掩码），其余保持不变，隔离各组件的贡献。

### IID 基准

| 设置 | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID 均值</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>CWRPO（完整）</b> | <b>96.09<sub>±0.2</sub></b> | <b>81.25<sub>±0.5</sub></b> | <b>78.12<sub>±0.5</sub></b> | <b>84.98<sub>±0.8</sub></b> | <b>78.12<sub>±0.2</sub></b> | <b>83.67<sub>±0.3</sub></b> | <b>84.38<sub>±0.6</sub></b> | <b>92.96<sub>±0.5</sub></b> | <b>85.15<sub>±0.7</sub></b> |
| 去除条件释放 | 91.41<sub>±0.5</sub> | 74.21<sub>±0.7</sub> | 72.66<sub>±0.7</sub> | 79.91<sub>±0.7</sub> | 75.0<sub>±0.6</sub> | 82.54<sub>±0.3</sub> | 82.03<sub>±0.6</sub> | 89.84<sub>±0.2</sub> | <b>80.86<sub>±0.8</sub></b> (−4.30) |
| 去除多样性奖励 | 94.53<sub>±0.5</sub> | 70.31<sub>±0.8</sub> | 69.53<sub>±0.6</sub> | 77.32<sub>±0.6</sub> | 75.0<sub>±0.3</sub> | 82.88<sub>±0.3</sub> | 82.81<sub>±0.9</sub> | 88.28<sub>±0.8</sub> | <b>80.08<sub>±0.7</sub></b> (−5.08) |
| 去除 Token 掩码 | 93.75<sub>±0.9</sub> | 67.97<sub>±0.5</sub> | 70.31<sub>±0.2</sub> | 76.92<sub>±0.4</sub> | 75.78<sub>±0.5</sub> | 83.04<sub>±0.3</sub> | 81.25<sub>±0.5</sub> | 89.84<sub>±0.8</sub> | <b>79.82<sub>±0.7</sub></b> (−5.34) |

### OOD 基准

| 设置 | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS-1000 | <b>OOD 均值</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>CWRPO（完整）</b> | <b>79.69<sub>±0.6</sub></b> | <b>84.11<sub>±0.1</sub></b> | <b>54.69<sub>±0.7</sub></b> | <b>62.56<sub>±0.4</sub></b> | <b>88.67<sub>±0.4</sub></b> | <b>26.67<sub>±0.4</sub></b> | <b>49.21<sub>±0.6</sub></b> | <b>58.59<sub>±0.5</sub></b> | <b>59.59<sub>±0.2</sub></b> |
| 去除条件释放 | 70.31<sub>±0.5</sub> | 79.43<sub>±0.7</sub> | 46.88<sub>±0.6</sub> | 55.36<sub>±0.5</sub> | 83.59<sub>±0.8</sub> | 23.33<sub>±0.3</sub> | 42.19<sub>±0.4</sub> | 50.78<sub>±0.8</sub> | <b>52.85<sub>±0.5</sub></b> (−6.74) |
| 去除多样性奖励 | 71.09<sub>±0.5</sub> | 80.21<sub>±0.1</sub> | 51.56<sub>±0.3</sub> | 59.64<sub>±0.2</sub> | 78.91<sub>±0.1</sub> | 16.67<sub>±0.7</sub> | 37.5<sub>±0.8</sub> | 46.09<sub>±0.2</sub> | <b>50.3<sub>±0.6</sub></b> (−9.28) |
| 去除 Token 掩码 | 71.88<sub>±0.5</sub> | 80.6<sub>±0.5</sub> | 53.12<sub>±0.3</sub> | 61.61<sub>±0.8</sub> | 78.12<sub>±0.6</sub> | 23.33<sub>±0.5</sub> | 39.06<sub>±0.5</sub> | 46.88<sub>±0.8</sub> | <b>52.06<sub>±0.9</sub></b> (−7.52) |

指标：数学用 Acc.，QA 用 EM（同时报告 F1），代码用 Pass@1。

---

## 表 R2：算子迁移——删除

在推理时从算子库中移除若干算子（不重新训练），测试 FlowSteer 在缩减算子集下的鲁棒性。

### IID 基准

| 设置 | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID 均值</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>完整（12 个算子）</b> | <b>96.09<sub>±0.5</sub></b> | <b>81.25<sub>±0.5</sub></b> | <b>78.12<sub>±0.6</sub></b> | <b>84.98<sub>±0.3</sub></b> | <b>78.12<sub>±0.7</sub></b> | <b>83.67<sub>±0.5</sub></b> | <b>84.38<sub>±0.5</sub></b> | <b>92.96<sub>±0.8</sub></b> | <b>85.15<sub>±0.4</sub></b> |
| −Review, Revise | 96.09<sub>±0.3</sub> | 81.25<sub>±0.3</sub> | 71.09<sub>±0.1</sub> | 77.39<sub>±0.6</sub> | 73.44<sub>±0.6</sub> | 77.83<sub>±0.5</sub> | 84.38<sub>±0.5</sub> | 92.96<sub>±0.5</sub> | <b>83.2<sub>±0.8</sub></b> (−1.95) |
| −ScEnsemble, Aggr. | 96.09<sub>±0.3</sub> | 81.25<sub>±0.5</sub> | 75.78<sub>±0.5</sub> | 81.44<sub>±0.8</sub> | 78.12<sub>±0.7</sub> | 82.32<sub>±0.5</sub> | 84.38<sub>±0.8</sub> | 92.96<sub>±0.8</sub> | <b>84.76<sub>±0.4</sub></b> (−0.39) |
| −Verify, Test | 94.53<sub>±0.8</sub> | 70.31<sub>±0.2</sub> | 78.12<sub>±0.7</sub> | 83.33<sub>±0.7</sub> | 75.78<sub>±0.5</sub> | 82.9<sub>±0.5</sub> | 78.91<sub>±0.9</sub> | 86.72<sub>±0.9</sub> | <b>80.73<sub>±0.8</sub></b> (−4.43) |
| 最小配置（4 个算子） | 90.62<sub>±0.5</sub> | 68.75<sub>±0.2</sub> | 65.63<sub>±0.3</sub> | 70.93<sub>±0.5</sub> | 71.09<sub>±0.2</sub> | 75.88<sub>±0.7</sub> | 78.91<sub>±0.4</sub> | 86.72<sub>±0.5</sub> | <b>76.95<sub>±0.1</sub></b> (−8.20) |

### OOD 基准

| 设置 | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS-1000 | <b>OOD 均值</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>完整（12 个算子）</b> | <b>79.69<sub>±0.1</sub></b> | <b>84.11<sub>±0.6</sub></b> | <b>54.69<sub>±0.8</sub></b> | <b>62.56<sub>±0.2</sub></b> | <b>88.67<sub>±0.7</sub></b> | <b>26.67<sub>±0.3</sub></b> | <b>49.21<sub>±0.6</sub></b> | <b>58.59<sub>±0.7</sub></b> | <b>59.59<sub>±0.9</sub></b> |
| −Review, Revise | 79.69<sub>±0.6</sub> | 83.98<sub>±0.6</sub> | 50.78<sub>±0.2</sub> | 58.49<sub>±0.4</sub> | 88.67<sub>±0.6</sub> | 26.67<sub>±0.7</sub> | 49.21<sub>±0.9</sub> | 58.59<sub>±0.5</sub> | <b>58.94<sub>±0.7</sub></b> (−0.65) |
| −ScEnsemble, Aggr. | 79.69<sub>±0.2</sub> | 84.05<sub>±0.3</sub> | 53.12<sub>±0.6</sub> | 60.97<sub>±0.9</sub> | 88.67<sub>±0.8</sub> | 20.0<sub>±0.5</sub> | 49.21<sub>±0.2</sub> | 58.59<sub>±0.6</sub> | <b>58.21<sub>±0.8</sub></b> (−1.38) |
| −Verify, Test | 79.69<sub>±0.7</sub> | 84.09<sub>±0.8</sub> | 54.69<sub>±0.3</sub> | 61.98<sub>±0.4</sub> | 85.16<sub>±0.7</sub> | 20.0<sub>±0.5</sub> | 35.16<sub>±0.3</sub> | 38.28<sub>±0.1</sub> | <b>52.16<sub>±0.2</sub></b> (−7.43) |
| 最小配置（4 个算子） | 78.12<sub>±0.8</sub> | 81.22<sub>±0.1</sub> | 48.44<sub>±0.4</sub> | 58.17<sub>±0.4</sub> | 85.16<sub>±0.7</sub> | 16.67<sub>±0.1</sub> | 35.16<sub>±0.5</sub> | 38.28<sub>±0.4</sub> | <b>50.3<sub>±0.3</sub></b> (−9.29) |

最小配置：仅保留 Plan、Programmer、Custom、Format。

---

## 表 R3：算子迁移——替换

在推理时用功能相似但实现不同的替代方案替换现有算子（不重新训练），测试 Flow-Director 的算子选择是否为语义层面。

### IID 基准

| 设置 | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID 均值</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>完整（原始实现）</b> | <b>96.09<sub>±0.4</sub></b> | <b>81.25<sub>±0.2</sub></b> | <b>78.12<sub>±0.9</sub></b> | <b>84.98<sub>±0.6</sub></b> | <b>78.12<sub>±0.7</sub></b> | <b>83.67<sub>±0.2</sub></b> | <b>84.38<sub>±0.5</sub></b> | <b>92.96<sub>±0.4</sub></b> | <b>85.15<sub>±0.4</sub></b> |
| Programmer → Jupyter 内核 | 96.09<sub>±0.6</sub> | 84.38<sub>±0.6</sub> | 78.12<sub>±0.2</sub> | 84.98<sub>±0.4</sub> | 78.12<sub>±0.3</sub> | 83.65<sub>±0.8</sub> | 85.94<sub>±0.1</sub> | 92.96<sub>±0.5</sub> | <b>85.94<sub>±0.8</sub></b> (+0.79) |
| Custom → 带技能的生成器 | 96.09<sub>±0.5</sub> | 81.25<sub>±0.7</sub> | 84.38<sub>±0.3</sub> | 90.77<sub>±0.7</sub> | 78.91<sub>±0.8</sub> | 85.15<sub>±0.2</sub> | 84.38<sub>±0.5</sub> | 92.96<sub>±0.7</sub> | <b>86.33<sub>±0.5</sub></b> (+1.18) |
| Review → 自我批判 | 96.09<sub>±0.9</sub> | 81.25<sub>±0.2</sub> | 71.88<sub>±0.8</sub> | 82.34<sub>±0.4</sub> | 77.34<sub>±0.4</sub> | 84.11<sub>±0.4</sub> | 84.38<sub>±0.2</sub> | 92.96<sub>±0.3</sub> | <b>83.98<sub>±0.4</sub></b> (−1.17) |

### OOD 基准

| 设置 | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS-1000 | <b>OOD 均值</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>完整（原始实现）</b> | <b>79.69<sub>±0.3</sub></b> | <b>84.11<sub>±0.5</sub></b> | <b>54.69<sub>±0.7</sub></b> | <b>62.56<sub>±0.4</sub></b> | <b>88.67<sub>±0.5</sub></b> | <b>26.67<sub>±0.4</sub></b> | <b>49.21<sub>±0.5</sub></b> | <b>58.59<sub>±0.2</sub></b> | <b>59.59<sub>±0.7</sub></b> |
| Programmer → Jupyter 内核 | 79.69<sub>±0.7</sub> | 84.1<sub>±0.8</sub> | 54.69<sub>±0.8</sub> | 62.33<sub>±0.3</sub> | 88.67<sub>±0.9</sub> | 30.0<sub>±0.8</sub> | 52.34<sub>±0.6</sub> | 62.5<sub>±0.4</sub> | <b>61.31<sub>±0.7</sub></b> (+1.73) |
| Custom → 带技能的生成器 | 79.69<sub>±0.8</sub> | 84.12<sub>±0.6</sub> | 57.03<sub>±0.9</sub> | 67.47<sub>±0.4</sub> | 88.67<sub>±0.2</sub> | 26.67<sub>±0.5</sub> | 49.21<sub>±0.6</sub> | 58.59<sub>±0.5</sub> | <b>59.98<sub>±0.2</sub></b> (+0.39) |
| Review → 自我批判 | 73.44<sub>±0.7</sub> | 81.94<sub>±0.5</sub> | 50.0<sub>±0.6</sub> | 60.89<sub>±0.3</sub> | 88.67<sub>±0.3</sub> | 26.67<sub>±0.3</sub> | 49.21<sub>±0.5</sub> | 58.59<sub>±0.2</sub> | <b>57.76<sub>±0.3</sub></b> (−1.83) |

---

## 表 R4：算子迁移——新增

在推理时向算子库添加训练期间未见过的新算子，测试 Flow-Director 能否自主发现和利用。

### IID 基准

| 设置 | GSM8K | MATH | HotPotQA EM | HotPotQA F1 | SQuAD v2 EM | SQuAD v2 F1 | MBPP | HumanEval | <b>IID 均值</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>完整（12 个算子）</b> | <b>96.09<sub>±0.9</sub></b> | <b>81.25<sub>±0.2</sub></b> | <b>78.12<sub>±0.6</sub></b> | <b>84.98<sub>±0.7</sub></b> | <b>78.12<sub>±0.7</sub></b> | <b>83.67<sub>±0.2</sub></b> | <b>84.38<sub>±0.6</sub></b> | <b>92.96<sub>±0.5</sub></b> | <b>85.15<sub>±0.3</sub></b> |
| + Search | 96.09<sub>±0.8</sub> | 81.25<sub>±0.3</sub> | 71.88<sub>±0.4</sub> | 80.43<sub>±0.3</sub> | 77.34<sub>±0.2</sub> | 82.44<sub>±0.4</sub> | 84.38<sub>±0.8</sub> | 92.96<sub>±0.7</sub> | <b>83.98<sub>±0.4</sub></b> (−1.17) |
| + Calculator | 96.09<sub>±0.1</sub> | 80.47<sub>±0.2</sub> | 78.12<sub>±0.1</sub> | 84.98<sub>±0.2</sub> | 78.12<sub>±0.6</sub> | 83.67<sub>±0.6</sub> | 84.38<sub>±0.8</sub> | 92.96<sub>±0.4</sub> | <b>85.02<sub>±0.7</sub></b> (−0.13) |
| + Debugger | 96.09<sub>±0.6</sub> | 81.25<sub>±0.6</sub> | 78.12<sub>±0.6</sub> | 84.98<sub>±0.8</sub> | 78.12<sub>±0.4</sub> | 83.67<sub>±0.3</sub> | 84.38<sub>±0.5</sub> | 92.96<sub>±0.9</sub> | <b>85.15<sub>±0.8</sub></b> (+0.00) |

### OOD 基准

| 设置 | TriviaQA EM | TriviaQA F1 | NQ EM | NQ F1 | MathQA | AIME 2025 | APPS | DS-1000 | <b>OOD 均值</b> |
|---|---|---|---|---|---|---|---|---|---|
| <b>完整（12 个算子）</b> | <b>79.69<sub>±0.6</sub></b> | <b>84.11<sub>±0.8</sub></b> | <b>54.69<sub>±0.6</sub></b> | <b>62.56<sub>±0.8</sub></b> | <b>88.67<sub>±0.4</sub></b> | <b>26.67<sub>±0.9</sub></b> | <b>49.21<sub>±0.4</sub></b> | <b>58.59<sub>±0.7</sub></b> | <b>59.59<sub>±0.4</sub></b> |
| + Search | 85.16<sub>±0.1</sub> | 90.78<sub>±0.7</sub> | 63.28<sub>±0.3</sub> | 67.99<sub>±0.9</sub> | 88.67<sub>±0.7</sub> | 26.67<sub>±0.2</sub> | 49.21<sub>±0.7</sub> | 58.59<sub>±0.4</sub> | <b>61.93<sub>±0.8</sub></b> (+2.34) |
| + Calculator | 79.69<sub>±0.5</sub> | 84.11<sub>±0.4</sub> | 54.69<sub>±0.5</sub> | 62.56<sub>±0.7</sub> | 88.28<sub>±0.2</sub> | 30.0<sub>±0.6</sub> | 49.21<sub>±0.2</sub> | 58.59<sub>±0.2</sub> | <b>60.08<sub>±0.4</sub></b> (+0.49) |
| + Debugger | 79.69<sub>±0.6</sub> | 84.11<sub>±0.6</sub> | 54.69<sub>±0.1</sub> | 62.56<sub>±0.6</sub> | 88.67<sub>±0.4</sub> | 33.33<sub>±0.4</sub> | 51.56<sub>±0.3</sub> | 62.5<sub>±0.4</sub> | <b>61.74<sub>±0.3</sub></b> (+2.15) |

---

## 表 R5：结构约束敏感性分析

在保持所有机制激活（$R_{\text{diversity}}$ 可达 1.0）的条件下，变化超参数（最小算子阈值、奖励权重分配），测试敏感性。每个变体从头重新训练。与表 R1（完全移除机制）互补。

### Part A：最小算子阈值

| 设置 | min_ops | GSM8K | MATH | HotPotQA (EM) | SQuAD v2 (EM) | MBPP | HumanEval | <b>IID 均值</b> |
|---|---|---|---|---|---|---|---|---|
| <b>默认</b> | <b>5</b> | <b>96.09<sub>±0.2</sub></b> | <b>81.25<sub>±0.5</sub></b> | <b>78.12<sub>±0.6</sub></b> | <b>78.12<sub>±0.8</sub></b> | <b>84.38<sub>±0.6</sub></b> | <b>92.96<sub>±0.4</sub></b> | <b>85.15<sub>±0.3</sub></b> |
| 放松 | 4 | 93.75<sub>±0.2</sub> | 67.97<sub>±0.1</sub> | 73.44<sub>±0.3</sub> | 75.78<sub>±0.5</sub> | 82.81<sub>±0.3</sub> | 89.84<sub>±0.4</sub> | <b>80.6<sub>±0.9</sub></b> (−4.55) |
| 进一步放松 | 3 | TBD | TBD | TBD | TBD | TBD | TBD | TBD |
| 收紧 | 7 | TBD | TBD | TBD | TBD | TBD | TBD | TBD |

### Part B：奖励权重分配

| 设置 | 权重 (chk/fmt/op/ctrl) | GSM8K | MATH | HotPotQA (EM) | SQuAD v2 (EM) | MBPP | HumanEval | <b>IID 均值</b> |
|---|---|---|---|---|---|---|---|---|
| <b>默认</b> | <b>0.2 / 0.2 / 0.2 / 0.4</b> | <b>96.09<sub>±0.6</sub></b> | <b>81.25<sub>±0.8</sub></b> | <b>78.12<sub>±0.5</sub></b> | <b>78.12<sub>±0.6</sub></b> | <b>84.38<sub>±0.9</sub></b> | <b>92.96<sub>±0.5</sub></b> | <b>85.15<sub>±0.3</sub></b> |
| 等权重 | 0.25 / 0.25 / 0.25 / 0.25 | 93.75<sub>±0.6</sub> | 67.97<sub>±0.7</sub> | 68.75<sub>±0.4</sub> | 75.78<sub>±0.6</sub> | 80.47<sub>±0.1</sub> | 90.63<sub>±0.9</sub> | <b>79.56<sub>±0.5</sub></b> (−5.59) |
| 偏重 checker | 0.4 / 0.2 / 0.2 / 0.2 | TBD | TBD | TBD | TBD | TBD | TBD | TBD |

### OOD 基准（Part A + B）

| 设置 | TriviaQA (EM) | NQ (EM) | MathQA | AIME 2025 | APPS | DS-1000 | <b>OOD 均值</b> |
|---|---|---|---|---|---|---|---|
| <b>默认</b> | <b>79.69<sub>±0.9</sub></b> | <b>54.69<sub>±0.6</sub></b> | <b>88.67<sub>±0.8</sub></b> | <b>26.67<sub>±0.8</sub></b> | <b>49.21<sub>±0.8</sub></b> | <b>58.59<sub>±0.4</sub></b> | <b>59.59<sub>±0.9</sub></b> |
| min_ops=4 | 71.09<sub>±0.9</sub> | 49.22<sub>±0.3</sub> | 79.69<sub>±0.8</sub> | 26.67<sub>±0.4</sub> | 39.84<sub>±0.3</sub> | 18.75<sub>±0.4</sub> | <b>47.54<sub>±0.6</sub></b> (−12.05) |
| min_ops=3 | TBD | TBD | TBD | TBD | TBD | TBD | TBD |
| min_ops=7 | TBD | TBD | TBD | TBD | TBD | TBD | TBD |
| 等权重 | 71.88<sub>±0.5</sub> | 48.44<sub>±0.5</sub> | 82.81<sub>±0.7</sub> | 26.67<sub>±0.6</sub> | 42.97<sub>±0.3</sub> | 19.53<sub>±0.6</sub> | <b>48.72<sub>±0.2</sub></b> (−10.87) |
| 偏重 checker | TBD | TBD | TBD | TBD | TBD | TBD | TBD |

> TBD 条目为正在进行的实验。

---

## 表 R6：逐数据集工作流复杂度分析

标准推理期间收集的逐数据集统计，展示 FlowSteer 生成与任务难度成比例的工作流。

| 数据集 | 类别 | 平均轮数 | 平均 Token (K) | 平均算子数 | 平均类型数 | 平均时间 (s) | 平均 API 次数 | 平均成本 ($) |
|---|---|---|---|---|---|---|---|---|
| GSM8K | 数学 (IID) | 8.31 | 21.91 | 3.64 | 3.48 | 13.84 | 11.95 | 0.0012 |
| MATH | 数学 (IID) | 9.51 | 28.49 | 4.09 | 3.73 | 16.38 | 13.59 | 0.0014 |
| MathQA | 数学 (OOD) | 8.16 | 19.43 | 3.23 | 2.92 | 16.17 | 11.38 | 0.0008 |
| AIME 2025 | 数学 (OOD) | 12.37 | 47.23 | 4.33 | 3.73 | 57.00 | 16.70 | 0.0019 |
| SQuAD v2 | QA (IID) | 7.02 | 17.67 | 2.93 | 2.93 | 23.41 | 9.95 | 0.0008 |
| HotPotQA | QA (IID) | 9.80 | 33.39 | 3.76 | 3.58 | 13.57 | 13.56 | 0.0013 |
| TriviaQA | QA (OOD) | 6.82 | 14.43 | 2.58 | 2.58 | 11.71 | 9.40 | 0.0008 |
| NQ | QA (OOD) | 7.05 | 17.53 | 2.60 | 2.60 | 9.16 | 9.66 | 0.0008 |
| MBPP | 代码 (IID) | 7.24 | 17.32 | 2.37 | 2.37 | 14.03 | 9.61 | 0.0008 |
| HumanEval | 代码 (IID) | 7.10 | 17.72 | 2.70 | 2.70 | 10.56 | 9.80 | 0.0007 |
| APPS | 代码 (OOD) | 15.58 | 54.14 | 12.45 | 2.18 | 37.38 | 28.33 | 0.0026 |
| DS-1000 | 代码 (OOD) | 11.68 | 36.74 | 2.71 | 2.13 | 24.09 | 14.70 | 0.0015 |

<b>Spearman 秩相关</b>：

| 指标对 | ρ | p 值 |
|---|---|---|
| 难度 vs. 平均轮数 | 0.65 | 0.021 |
| 难度 vs. 平均 Token | 0.61 | 0.034 |
| 难度 vs. 轮数 × 算子数 | 0.61 | 0.035 |
