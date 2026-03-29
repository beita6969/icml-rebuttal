感谢审稿人对我们算子库和多样性约束奖励的认可。以下逐一回应各项关切（完整数据见 https://anonymous.4open.science/r/Supplementary）。

**W1：与现有方法的设计差异（论文：Table 5, Def. 1）**

以下对比表阐明关键架构区别：

| | 编排方式 | 工作流 | 提示词 | 训练 |
|:---:|:---:|:---:|:---:|:---:|
| AFlow | MCTS 搜索 | 代码搜索 | 写在代码中 | 无 RL |
| Orchestrator | 分层路由 | 固定路由 | 固定 | 多目标 RL |
| AgentFlow | 4 模块管线 | 固定管线 | 固定 | Flow-GRPO |
| RouterR1 | LLM 路由 | 固定线性 | 固定 | RL |
| **FlowSteer** | **Canvas 交互** | **动态编辑** | **逐任务定制** | **CWRPO** |

FlowSteer 是唯一能*通过多轮交互动态编辑 workflow graph（Definition 1）并逐任务定制提示词*的方法。策略每轮提交原子编辑动作（add/delete/modify/set_prompt/控制结构，公式 6）；Canvas 返回结构化反馈（公式 7–8）支持迭代式细化（Section 4.2）。所有 baseline 要么路由到固定工具、生成整块代码，要么使用固定管线。Table 5 量化：移除多轮交互导致 IID −5.15；移除 Canvas 导致最大 OOD 下降。

**W2：Plug-and-play 并非"纯工程"（新增：Tables R2–R4 | 论文：Prop. 1, Table 7）**

Plug-and-play 能力由*分解式动作空间*（Section 4.1）支撑，将动作类型与算子解耦（Proposition 1），复杂度为 $O(\\lvert\\mathcal{A}\_{\\text{type}}\\rvert + \\lvert\\mathcal{O}\\rvert)$ vs. 朴素的 $O(\\lvert\\mathcal{A}\_{\\text{type}}\\rvert \\times \\lvert\\mathcal{O}\\rvert)$。这是有理论基础的*设计层*贡献，而非简单的工程封装。Flow-Director 通过语义描述（Table 7）而非硬编码索引选择算子。

新迁移实验验证（补充材料**表 R2–R4**）：*删除*（仅 4 核心算子）：IID 76.95 / OOD 50.30，渐进退化。*替换*（Programmer→Jupyter，Custom→Generate with Skills）：均值 IID 85.42（+0.3）/ OOD 59.69（+0.1），几乎无损。*新增*（未见过的 Search、Calculator、Debugger）：目标任务选择性提升（QA +5.5–8.6 EM，代码 +2.4–3.9），非目标任务零退化。

**W3/Q1：CWRPO vs. GRPO——三个目标函数层面的差异（论文：Table 6, Prop. 6）**

CWRPO 与 GRPO 在*目标函数层面*存在三个差异，而非仅仅是 reward 不同：

**(1) 目标函数中的 Token 级掩码**（公式 10）：二值掩码 $\\text{mask}\_t \\in \\{0,1\\}$ 作用于*策略梯度内部*，仅对策略生成的 token 计算损失，而 GRPO 包含环境反馈。Proposition 6c 证明此掩码产生无偏估计且方差严格更低。消融：移除后 MATH −13.28，MathQA −10.55。

**(2) 带符号分离的条件释放**（公式 14）：指示函数 $\\mathbb{I}\\{R\_{diversity}=1.0\\}$ 创建符号分离奖励——可行 $R(\\tau) \\geq 0$，不可行 $R(\\tau) < 0$，实现可证明的两阶段优化（Proposition 6b）：先学结构，再优化答案。GRPO 无此课程机制。消融：TriviaQA EM −9.38，NQ EM −7.81。

**(3) 按数据源分区的优势归一化**：优势值使用按数据源分区的统计量 $(\\mu\_{src}, \\sigma\_{src})$，防止多任务训练中的跨任务优势坍缺。标准 GRPO 使用全局批统计量。

Table 6 证实这三项差异的综合效果：CWRPO 在全部 6 个 IID 基准上优于 GRPO（GSM8K 96.09 vs. 92.97，MATH 81.25 vs. 73.43，HotPotQA 78.12 vs. 72.66）。

**W4：Reward 组件消融（新增：Table R1 | 论文：Tables 5, 6）**

我们已完成该消融实验（新**表 R1**），逐一移除各组件：

| 变体 | IID 均值 | Δ | OOD 均值 | Δ |
|:---:|:---:|:---:|:---:|:---:|
| CWRPO（完整） | 85.15 | — | 59.59 | — |
| 去除 Token Masking | 79.82 | −5.34 | 52.06 | −7.52 |
| 去除 Diversity Reward | 80.08 | −5.08 | 50.30 | −9.28 |
| 去除 Cond. Release | 80.86 | −4.30 | 52.85 | −6.74 |

三个组件解决正交的挑战且缺一不可：**Token Masking** 解决多轮信用分配问题——缺失后环境 token 污染梯度，数学推理退化最严重（MATH −13.28）；**Diversity Reward** 防止结构模式坍缩——缺失后策略收敛到捷径 workflow，在未见分布上失败（AIME −10.00，DS-1000 −12.50）；**Conditional Release** 防止过早收敛——缺失课程门控后策略提前终止，对需要长推理链的开放域 QA 影响最大（TriviaQA EM −9.38）。三类失效影响不同任务类别且无法互补，证明 CWRPO 是最小完备设计。结合 Table 5（框架）和 Table 6（算法），构成三层互补消融体系。
