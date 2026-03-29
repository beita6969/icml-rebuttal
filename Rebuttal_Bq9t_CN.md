# 审稿人 Bq9t（评分：3，弱拒绝）

感谢审稿人对我们算子库和多样性约束奖励的认可。以下逐一回应各项关切（完整数据见 [补充材料](https://anonymous.4open.science/r/Supplementary)）。

**W1：与现有方法的设计差异**

以下对比表阐明关键架构区别：

| | 编排方式 | 工作流 | 提示词 | 训练 |
|---|---|---|---|---|
| AFlow | MCTS 搜索 | 代码搜索 | 写在代码中 | 无 RL |
| Orchestrator | 分层路由 | 固定路由 | 固定 | 多目标 RL |
| AgentFlow | 4 模块管线 | 固定管线 | 固定 | Flow-GRPO |
| RouterR1 | LLM 路由 | 固定线性 | 固定 | RL |
| **FlowSteer** | **Canvas 交互** | **动态编辑** | **逐任务定制** | **CWRPO** |

FlowSteer 是唯一能*通过多轮交互动态编辑 workflow graph（Definition 1）并逐任务定制提示词*的方法。策略每轮提交原子编辑动作（add/delete/modify/set_prompt/控制结构，公式 6）；Canvas 返回结构化反馈（公式 7–8），形成迭代式"诊断-编辑-验证"循环（Section 4.2）。所有 baseline 要么路由到固定工具、生成整块代码，要么使用固定管线和固定提示词。Table 5 量化贡献：移除多轮交互导致 IID −5.15，移除 Canvas 反馈导致最大 OOD 下降。

**W2：Plug-and-play 并非"纯工程"**

Plug-and-play 能力源于*分解式动作空间*（Section 4.1，Proposition 1），将动作类型与算子解耦，复杂度 $`O(\lvert\mathcal{A}_{\text{type}}\rvert + \lvert\mathcal{O}\rvert)`$ vs. 朴素的 $`O(\lvert\mathcal{A}_{\text{type}}\rvert \times \lvert\mathcal{O}\rvert)`$。这是有理论基础的*设计层*贡献，而非工程封装。

新增迁移实验（补充材料 **表 R2–R4**）验证：*删除*（仅 4 核心算子）：IID 76.95 / OOD 50.30，渐进退化。*替换*（Programmer→Jupyter，Custom→Generate with Skills）：均值 IID 85.42（+0.3）/ OOD 59.69（+0.1）。*新增*（未见过的 Search、Calculator、Debugger）：目标任务选择性提升（QA +5.5–8.6 EM，代码 +2.4–3.9），非目标零退化。

**W3/Q1：CWRPO vs. GRPO——三个目标函数层面的差异**

CWRPO 与 GRPO 在*目标函数层面*存在三个差异，而非仅仅是 reward 不同：

**(1) Token 级掩码**（公式 10）：二值掩码 $`\text{mask}_t`$ 作用于策略梯度内部，仅对策略生成的 token 计算损失。GRPO 对包括环境反馈在内的所有 token 计算损失。Proposition 6c 证明此方式产生无偏且方差更低的估计。消融验证：移除后 MATH −13.28。

**(2) 带符号分离的条件释放**（公式 14）：$`\mathbb{I}\{R_{diversity}=1.0\}`$ 创建符号分离奖励——可行轨迹 $`R(\tau) \geq 0`$，不可行 $`R(\tau) < 0`$。实现可证明的两阶段优化（Proposition 6b）：先学结构，再优化答案。GRPO 无此课程机制。消融：移除后 TriviaQA EM −9.38。

**(3) 按数据源分区的优势归一化**：优势值使用按数据源分区的统计量 $`(\mu_{src}, \sigma_{src})`$，防止多任务训练中的跨任务优势坍缩。GRPO 使用全局批统计量。

Table 6 证实：CWRPO 在全部 6 个 IID 基准上优于 GRPO（GSM8K 96.09 vs. 92.97，MATH 81.25 vs. 73.43）。

**W4：Reward 组件消融**

新增 **表 R1** 逐一隔离各组件：

| 变体 | IID 均值 | OOD 均值 |
|---|---|---|
| CWRPO（完整） | 85.15 | 59.59 |
| 去除 Token Masking | 79.82（−5.34） | 52.06（−7.52） |
| 去除 Diversity Reward | 80.08（−5.08） | 50.30（−9.28） |
| 去除 Cond. Release | 80.86（−4.30） | 52.85（−6.74） |

三个组件解决正交的挑战：**Token Masking**——信用分配（MATH −13.28）；**Diversity Reward**——模式坍缩（AIME −10.00，DS-1000 −12.50）；**Conditional Release**——过早收敛（TriviaQA −9.38）。三类失效不可互补，证明 CWRPO 是最小完备设计。结合 Table 5（框架）和 Table 6（算法），构成三层互补消融体系。
