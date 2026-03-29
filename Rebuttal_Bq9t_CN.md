# 审稿人 Bq9t（评分：3，弱拒绝）

感谢审稿人对我们算子库和多样性约束奖励的认可。以下逐一回应各项关切（完整数据见 [补充材料](https://anonymous.4open.science/r/Supplementary)）。

**W1：与现有方法的设计差异**

我们通过以下对比表阐明关键架构区别：

| | 编排方式 | 工作流 | 提示词 | 训练 |
|---|---|---|---|---|
| AFlow | MCTS 搜索 | 代码搜索 | 写在代码中 | 无 RL |
| Orchestrator | 分层路由 | 固定路由 | 固定 | 多目标 RL |
| AgentFlow | 4 模块管线 | 固定管线 | 固定 | Flow-GRPO |
| RouterR1 | LLM 路由 | 固定线性 | 固定 | RL |
| **FlowSteer** | **Canvas 交互** | **动态编辑** | **逐任务定制** | **CWRPO** |

FlowSteer 是唯一能*通过多轮交互动态编辑 workflow graph（Definition 1）并逐任务定制提示词*的方法。策略每轮提交原子编辑动作（add/delete/modify/set_prompt/控制结构，公式 6）；Canvas 执行后返回结构化反馈（公式 7–8），形成迭代式"诊断-编辑-验证"循环（Section 4.2）。所有 baseline 要么路由到固定工具、生成整块代码，要么使用固定管线和固定提示词。Table 5 量化了各组件贡献：移除多轮交互导致 IID −5.15；移除 Canvas 反馈导致最大 OOD 下降。

**W2：Plug-and-play 并非"纯工程"**

Plug-and-play 能力由*分解式动作空间*（Section 4.1）支撑，将动作类型与算子解耦（Proposition 1），复杂度为 $`O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)`$ vs. 朴素的 $`O(\lvert\mathcal{A}_{\text{type}}\rvert \times \lvert\mathcal{O}\rvert)`$。这是有理论基础的*设计层*贡献，而非简单的工程封装。Flow-Director 通过语义描述（Table 7）而非硬编码索引选择算子。

新迁移实验验证（**表 R2–R4**）：*删除*（仅 4 核心算子）：IID 76.95 / OOD 50.30，渐进退化。*替换*（Programmer→Jupyter，Custom→Generate with Skills）：均值 IID 85.42（+0.3）/ OOD 59.69（+0.1），几乎无损。*新增*（未见过的 Search、Calculator、Debugger）：目标任务选择性提升（QA +5.5–8.6 EM，代码 +2.4–3.9），非目标任务零退化。据我们所知，此前无工作展示过这一层级的算子迁移能力。

**W3/Q1：CWRPO vs. GRPO——三个目标函数层面的差异**

我们恳请澄清：CWRPO 与 GRPO 在*目标函数层面*存在三个差异，而非仅仅是 reward 不同：

**(1) 目标函数中的 Token 级掩码**（公式 10）：二值掩码 $`\text{mask}_t \in \{0,1\}`$ 作用于*策略梯度内部*，仅对策略生成的 token 计算损失。GRPO 对包括环境反馈在内的所有 token 计算损失。这改变了*哪些 token 接收梯度信号*。Proposition 6c 证明此掩码产生无偏估计且方差严格更低（公式 62–64）。消融验证：移除后 MATH −13.28，MathQA −10.55。

**(2) 带符号分离的条件释放**（公式 14）：指示函数 $`\mathbb{I}\{R_{diversity}=1.0\}`$ 创建符号分离的奖励——可行轨迹 $`R(\tau) \geq 0`$（公式 48），不可行轨迹 $`R(\tau) < 0`$（公式 46）。这实现了可证明的两阶段优化（Proposition 6b）：先学结构，再优化答案。GRPO 无此课程机制。消融：移除后 TriviaQA EM −9.38，NQ EM −7.81。

**(3) 按数据源分区的优势归一化**：优势值使用按数据源分区的统计量 $`(\mu_{src}, \sigma_{src})`$，防止多任务训练中的跨任务优势坍缺。标准 GRPO 使用全局批统计量。

Table 6 证实：CWRPO 在全部 6 个 IID 基准上优于 GRPO（GSM8K 96.09 vs. 92.97，MATH 81.25 vs. 73.43，HotPotQA 78.12 vs. 72.66）。

**W4：Reward 组件消融**

我们已完成该消融实验（**表 R1**），逐一移除各组件：

| 变体 | IID 均值 | Δ | OOD 均值 | Δ |
|---|---|---|---|---|
| CWRPO（完整） | 85.15 | — | 59.59 | — |
| 去除 Token Masking | 79.82 | −5.34 | 52.06 | −7.52 |
| 去除 Diversity Reward | 80.08 | −5.08 | 50.30 | −9.28 |
| 去除 Cond. Release | 80.86 | −4.30 | 52.85 | −6.74 |

每个组件均不可或缺且作用各异：Token Masking 稳定数学推理（MATH −13.28）；Diversity Reward 保障 OOD 泛化（AIME −10.00，DS-1000 −12.50）；Conditional Release 抑制 QA 捷径（TriviaQA EM −9.38）。结合 Table 5（框架）和 Table 6（算法），构成三层互补消融体系。
