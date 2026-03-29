# FlowSteer (ICML 2026 #11778) — 审稿人回复（中文版）

---

## Reviewer LqWU（评分：4，Borderline Accept，置信度：4）

衷心感谢审稿人对我们的问题定义、广泛实验评估以及在 QA、数学和代码任务上一致性改进的认可。我们针对每个问题以新实验作为回应。完整表格见[匿名补充材料](LINK_PLACEHOLDER)。

**W1/Q1：CWRPO 组件级消融**

我们同意 Table 6 对比的是算法层面，但未隔离 CWRPO 的内部机制。我们现已在相同训练设置下，对 12 个 benchmark 逐一移除各组件完成消融实验（**Table R1**：数学用 Acc.，QA 用 EM/F1，代码用 Pass@1）：

| 变体 | IID 平均 | OOD 平均 |
|---|---|---|
| CWRPO（完整） | 85.15 | 59.59 |
| 去除多样性奖励 | 79.69 (−5.47) | 46.54 (−13.05) |
| 去除 Token 掩码 | 79.82 (−5.34) | 45.85 (−13.74) |
| 去除条件释放 | 80.60 (−4.55) | 46.11 (−13.48) |

三个组件均不可缺少，影响相当。多样性奖励（ $R_\text{diversity}$ ，公式 12）IID 下降最大——它是条件释放的门控脚手架，去除后 MATH 下降 13.28（命题 3）。Token 掩码（公式 10）OOD 下降最大，DS-1000 从 58.59 暴跌至 2.34，与复杂任务上梯度方差失稳一致（命题 6c，公式 62–64）。条件释放（公式 14）对开放域 QA 有独特影响（TriviaQA EM −9.38，NQ EM −7.81），确认捷径抑制作用（命题 6b）。

**W2/Q2：算子库迁移**

感谢审稿人的建议。据我们所知，此前没有工作（AFlow：6 个固定算子；AgentFlow：4 个固定模块）评估过算子级迁移。我们在全部 12 个 benchmark 上完成了三层实验：

*删除*（**Table R2**）：仅保留 4 个核心算子（Plan, Programmer, Custom, Format），FlowSteer 仍达到 IID XX.XX / OOD XX.XX 的平均分，超过 Table 3 中的大多数基线。移除 Review/Revise 后，策略自适应转向 Verify 进行质量检查——由多轮 canvas 反馈驱动，命题 5（附录 B.2）证明其单调改进性。

*替换*（**Table R3**）：替换算子实现（Programmer→Jupyter, Custom→RAG, Review→LLM-Judge）达到 IID XX.XX (−X.X) / OOD XX.XX (−X.X)。Flow-Director 通过语义描述（Table 7）选择算子，而非硬编码索引，通过 canvas 反馈自动适应，无需重新训练。

*新增*（**Table R4**）：训练时未见过的新算子（WebSearch, Summarizer, Debugger）被自主发现和利用，达到 IID XX.XX (+X.X) / OOD XX.XX (+X.X)，得益于分解式动作空间设计（ $O(\lvert A_\text{type}\rvert+\lvert\mathcal{O}\rvert)$ ，第 4.1 节）。

**W3/Q3：结构先验敏感性**

感谢这个问题。我们希望澄清：结构约束并非启发式的，而是从理论最小值推导得出的。

(1) *最小算子数的理论依据*：命题 4（附录 B.1）定义了 7 个认知原语 $\mathcal{C}=\lbrace P,D,E,M,R,I,O\rbrace$ ，满射覆盖（公式 19–25）。一个完整的认知控制循环至少需要： $\mathbf{P}$ （规划，1）+ $\mathbf{E}$ （执行，1）+ $\mathbf{M}$ （监控，1，满足 $R_\text{checker}$ ）+ $\mathbf{R/I}$ （修正/集成，1，构成控制结构满足 $R_\text{control}$ ）+ $\mathbf{O}$ （输出，1，满足 $R_\text{format}$ ）= $\mathbf{5}$ 个节点。这是同时满足全部 4 个 diversity 约束的最小数量。附录 B.1(iii) 三类任务模板均收敛于此最小值。

(2) *自适应复杂度*（**Table R6**，报告每个数据集的平均 turns、tokens、算子数）：逐数据集统计表明 FlowSteer 生成与任务难度成比例的工作流：GSM8K（小学数学）平均 X.X 轮 / X.XK tokens，而 AIME 2025（竞赛数学）X.X 轮 / X.XK tokens；SQuAD v2（抽取式 QA）X.X 轮，而 HotPotQA（多跳推理）X.X 轮。数据集难度与工作流复杂度的 Spearman ρ=X.XX（p<0.05），确认统计显著的自适应性。Figure 5(a-b) 进一步显示 FlowSteer (Full) 比所有消融变体（Table 5）消耗更低。

(3) *敏感性分析*（**Table R5**）：我们改变最小算子门槛（5→4→3→7）和奖励权重分配（equal 0.25×4 vs 默认 0.2/0.2/0.2/0.4），保持 $R_\text{diversity}$ 总和 = 1.0 使条件释放在所有变体中正常运作（区别于 Table R1 移除整个机制）。结果显示渐进退化（平均 −X.X 到 −X.X）而非灾难性崩溃；equal 权重与默认表现相当，min_ops 增加到 7 收益递减——与 5 为理论最小值一致。

**局限性**：我们将扩展隐私讨论（所有数据来自公开 benchmark；代码沙箱执行，30 秒超时）和滥用防范（算子约束 + canvas 验证；生产部署需额外安全措施）。

---

## Reviewer Bq9t（评分：3，Reject，置信度：3）

感谢审稿人对我们算子库设计和多样性约束奖励的认可。我们逐一回应每个问题。完整表格见[匿名补充材料](LINK_PLACEHOLDER)。

**W1：与现有方法的设计差异**

FlowSteer 引入了根本不同的范式（Figure 2d）：

| | 编排方式 | 工作流 | 提示词 | 训练 | 算子 |
|---|---|---|---|---|---|
| AFlow | MCTS 搜索 | 代码搜索 | 写在代码中 | 无 RL | 6 个固定 |
| Orchestrator | 层级式 | 固定路由 | 固定 | 多目标 RL | 可插拔 |
| AgentFlow | 4 模块管道 | 固定管道 | 固定 | Flow-GRPO | 4 个固定 |
| RouterR1 | LLM 路由 | 固定线性 | 固定 | RL | LLM 路由 |
| **FlowSteer** | **Canvas 交互** | **动态 DAG** | **逐任务定制** | **CWRPO** | **12 个可插拔** |

FlowSteer 是唯一*动态构建工作流 DAG 并逐任务定制提示词*的方法：策略每轮提交原子编辑动作（add/delete/modify/set\_prompt/控制结构，公式 6），Canvas 返回结构化反馈（公式 7–8）实现“诊断-编辑-验证”优化（Section 4.2）。其他方法要么路由到固定工具，要么生成整体代码，要么使用固定管道和固定提示词。Table 5 验证：移除多轮导致 IID −5.15，移除 Canvas 导致最大 OOD 下降。

**W2：即插即用并非“纯工程”**

即插即用由*分解式动作空间*（Section 4.1）实现，将动作类型与算子解耦（命题 1）：

$$O(|\mathcal{A}_{\text{type}}| + |\mathcal{O}|) \quad\text{vs.}\quad O(|\mathcal{A}_{\text{type}}| \times |\mathcal{O}|)$$

Flow-Director 通过语义描述（Table 7）选择算子而非索引，实现零样本迁移。

12 个 benchmark 上的新实验：*删除*（**Table R2**，仅 4 个核心算子）：IID XX.XX / OOD XX.XX，超过大多数基线。*替换*（**Table R3**）：IID XX.XX (−X.X) / OOD XX.XX (−X.X)。*新增*（**Table R4**，未见过的 WebSearch, Summarizer, Debugger）：IID XX.XX (+X.X) / OOD XX.XX (+X.X)。此前无工作展示过算子级迁移。

**W3/Q1：CWRPO vs. GRPO——三个目标函数层面的区别**

我们感谢这个机会来澄清区别。CWRPO 在*目标函数层面*与 GRPO 有三处不同，而非仅在奖励：

(1) **目标函数中的 Token 级掩码**（公式 10）：二值掩码 $\text{mask}_t \in \lbrace 0,1\rbrace$ 应用于*策略梯度内部*，仅对策略生成的 token 计算损失，环境反馈 token 梯度归零。GRPO 对所有 token 计算损失。这改变了*哪些 token 接收梯度信号*——而非奖励。命题 6c 证明了无偏性（公式 62）和方差缩减（公式 63–64）：

$$\text{Var}_{\text{mask}} < \text{Var}_{\text{no-mask}}$$

(2) **条件释放与符号分离**（公式 14）：指示函数 $\mathbb{I}\lbrace R_\text{diversity}=1.0\rbrace$ 创建结构保证的符号分离奖励——可行轨迹 $R(\tau) \geq 0$ （公式 48），不可行轨迹 $R(\tau) < 0$ （公式 46）。这实现了可证明的两阶段优化（命题 6b，公式 49–54）：策略先学习结构有效性，再优化答案质量。GRPO 无此课程结构，直接优化单一奖励信号。

(3) **按数据源分区的优势归一化**：优势值使用按数据源分区的统计量 $(\mu_\text{src}, \sigma_\text{src})$ ，防止多任务训练中的跨任务优势坍缩。标准 GRPO 使用全局批统计。

Table 6 证实：CWRPO 在全部 6 个 IID benchmark 上一致优于 GRPO（如 GSM8K 96.09 vs. 92.97，MATH 81.25 vs. 73.43，HotPotQA 78.12 vs. 72.66）。

**W4：奖励组件消融**

我们已完成此消融，在相同训练下逐一移除 CWRPO 各组件（**Table R1**，数学用 Acc.，QA 用 EM/F1，代码用 Pass@1）：

| 变体 | IID 平均 | OOD 平均 |
|---|---|---|
| CWRPO（完整） | 85.15 | 59.59 |
| 去除多样性奖励 | 79.69 (−5.47) | 46.54 (−13.05) |
| 去除 Token 掩码 | 79.82 (−5.34) | 45.85 (−13.74) |
| 去除条件释放 | 80.60 (−4.55) | 46.11 (−13.48) |

三个组件影响相当。多样性奖励 IID 下降最大（MATH −13.28）；Token 掩码 OOD 下降最大（DS-1000: 58.59→2.34）；条件释放对开放域 QA 有独特影响（TriviaQA EM −9.38，NQ EM −7.81）。结合 Table 5（框架消融）和 Table 6（RL 算法对比），我们提供三个互补消融层次：框架 → RL 算法 → CWRPO 内部机制。

---

## Reviewer igcB（评分：3，Reject，置信度：3）

感谢审稿人对条件释放机制的巧妙设计、全面实验评估以及即插即用框架设计的认可。我们逐一回应每个问题。完整表格见[补充材料](LINK_PLACEHOLDER)。

**W1：算子库在重叠工具场景下的可扩展性**

我们同意这是一个重要的实际问题。从三个角度回应：

(1) *理论可扩展性*：分解式动作空间（Section 4.1）复杂度为 $O(\lvert\mathcal{A}_\text{type}\rvert + \lvert\mathcal{O}\rvert)$ ——新增算子线性增长而非组合爆炸。Flow-Director 通过语义描述（Table 7）选择算子而非索引，能够区分功能重叠的工具。

(2) *干扰算子实验*（**Table R7**）：在测试时（不重新训练）注入功能重叠的“干扰”算子，将库从 12 扩展到 15/18/24 个。结果：IID XX.XX / XX.XX / XX.XX，OOD XX.XX / XX.XX / XX.XX。即使库翻倍到 24 个算子，仅造成 IID −X.X / OOD −X.X 的退化——语义选择机制和 canvas 反馈能有效区分重叠功能。

(3) *迁移实验*（**Tables R2–R4**）：删除（仅 4 算子）：IID XX.XX / OOD XX.XX。替换：IID XX.XX (−X.X) / OOD XX.XX (−X.X)。新增未见算子：IID XX.XX (+X.X) / OOD XX.XX (+X.X)。证明库组成并不脆弱。

我们承认扩展到数百个 API 对所有当前工作流编排方法仍是开放挑战。一个有前景的方向是层级式算子选择（类别→算子），我们的分解式动作空间天然支持通过类别级路由实现。

**W2：计算成本与训练-推理权衡**

我们提供所有基线的详细成本对比：

| | 训练 | 多后端部署 | 推理 | IID Avg. |
|---|---|---|---|---|
| AFlow | ~10K MCTS 迭代/任务，无 RL | 每后端重搜 | 搜索迭代 | 80.16 |
| Orchestrator | 多目标 RL，~XX GPU-hrs | 每后端重训 | 工具路由 | 82.37 |
| AgentFlow | Flow-GRPO，~XX GPU-hrs | 每后端重训 | 多轮 | 75.17 |
| RouterR1 | RL，~XX GPU-hrs | 每后端重训 | 多轮路由 | 82.29 |
| **FlowSteer** | **CWRPO，~8 GPU-hrs** | **训练一次，零样本 ×6** | **自适应** | **84.33** |

*向量化 rollout——训练为何快*：“36 条轨迹 × 20 轮”的表面成本具有误导性。我们的实现（Appendix G）使用 ThreadPoolExecutor 32 路并发：每个训练步中所有 N 条轨迹与 Canvas *并行交互*。Canvas 通过批量 API 调用执行算子，因此每轮是 1 次批并发调用而非 N 次串行调用。多轮循环同样被加速：Canvas 缓存中间工作流状态，提前到达 `finish` 的轮次直接跳过。实际约 300 步训练在 2×A100 80GB 上仅需约 8 GPU 小时。

*摊销成本最低*：FlowSteer 训练一次即零样本部署 6 个后端（Figure 4）。摊销：约 1.3 GPU-hrs/后端。其他方法（AFlow, AgentFlow, Orchestrator, RouterR1）均需每后端重搜或重训。

*推理反而更高效*：Figure 5(a-b) 显示 FlowSteer (Full) 比所有消融变体 token 和轮数更少——RL 教会策略*何时停止*。Table R6：GSM8K 平均 X.X 轮 vs. AIME 2025 X.X 轮。

**W3：Appendix C.1 与 Table 11 的权重不一致**

感谢审稿人发现此不一致。澄清：实际训练使用权重 0.2/0.2/0.2/0.4（如 Table 11 所示），给予 $R_\text{control}$ 更高权重以反映控制结构的重要性。Appendix C.1 中 equal 0.25 的描述来自早期草稿，将在修订中纠正。

重要的是，我们的敏感性分析（**Table R5 Part B**）表明此差异对实际影响很小：equal 权重（0.25×4）达到 IID XX.XX / OOD XX.XX，而默认（0.2/0.2/0.2/0.4）达到 IID XX.XX / OOD XX.XX——仅差 X.X 个百分点。所有总和=1.0 的权重配置都保持条件释放机制（公式 14）正常运作并取得相当的性能，确认方法对精确权重调节具有鲁棒性。

---

## Reviewer X8tH（评分：4，Borderline Accept，置信度：3）

感谢审稿人的积极评价和详细反馈。下面我们对相关章节进行阐述并提供新证据。新表格见[补充材料](LINK_PLACEHOLDER)。

**W1/Q1：条件释放的理论基础（收敛与稳定性）**

我们在 Appendix B（第 14–20 页）中提供了四项形式化保证：(1) *符号分离*（命题 6a，公式 43–48）：不合规 $\tau\in\mathcal{F}^c$ 始终获得 $R(\tau)\in[-1,0)$，可行 $\tau\in\mathcal{F}$ 获得 $R(\tau)\geq 0$，消除奖励塑形歧义。优势归一化后（公式 51），可行/不可行轨迹严格可分（公式 52）。(2) *两阶段课程*（命题 6b，公式 49–54）：符号间隔使可行性概率 $p_\theta$ 单调递增（公式 54）——先学结构约束，再优化答案质量，非启发式调度。(3) *有界更新*（公式 58）：裁剪 + KL 约束 $\lVert\theta_{k+1}-\theta_k\rVert$，确保平滑收敛。(4) *Token 掩码方差缩减*（命题 6c，公式 62–64）：掩蔽环境 token 产生无偏估计且方差可证明更低，稳定训练。

*新增*：**Table R5** 变化奖励权重（0.25×4 vs. 0.2/0.2/0.2/0.4）和最小算子阈值（3→7），所有变体仅渐进变化（−X.X 到 −X.X）而非不稳定，确认鲁棒性。

**W2/Q2：消融——隔离多轮交互 vs. 奖励设计 vs. 策略优化**

论文包含两个互补的消融层次，我们现在新增第三层：

(a) *框架消融*（Table 5）：w/o Multi-turn → IID −5.15；w/o Canvas → 最大 OOD 下降；w/o RL → IID −3.03。分离交互与 RL。

(b) *RL 算法对比*（Table 6 + Figure 5c-e）：相同 Canvas 交互下，CWRPO 在所有 IID 超过 GRPO（GSM8K 96.09 vs. 92.97，MATH 81.25 vs. 73.43）。隔离 CWRPO 与交互范式。**Table R8**（新）扩展到 6 个 OOD 基准。

(c) *CWRPO 组件消融*（**Table R1**，新）：三个组件均不可缺少——多样性奖励：IID −5.47；Token 掩码：OOD −13.74；条件释放：独特 QA 影响（NQ EM −7.81）。分解 CWRPO 三个机制。

合在一起：(a) 多轮必要，(b) 交互不变下 CWRPO 有额外价值，(c) 隔离奖励设计与优化。

**W3/Q3：与相关 RL 智能体系统的定位**

我们在 Figure 2 和 Section 2 中讨论了四种范式，进一步澄清区别：

| | 编排方式 | 工作流 | 提示词 | 训练 |
|---|---|---|---|---|
| AFlow | MCTS 搜索 | 代码搜索 | 写在代码中 | 无 RL |
| Orchestrator | 层级式 | 固定路由 | 固定 | 多目标 RL |
| AgentFlow | 4 模块管道 | 固定管道 | 固定 | Flow-GRPO |
| RouterR1 | LLM 路由 | 固定线性 | 固定 | RL |
| **FlowSteer** | **Canvas 交互** | **动态 DAG** | **逐任务定制** | **CWRPO** |

核心新颖性：FlowSteer 通过多轮 Canvas 交互*动态构建工作流 DAG 并逐任务定制提示词*（Section 4.2）。策略提交原子编辑（Table 1）；Canvas 返回结构化反馈（公式 7–8），形成其他基线不具备的“诊断-编辑-验证”闭环。Tables 3–4：FlowSteer IID 84.33 / OOD 73.34 avg. vs. 最佳基线 Orchestrator 82.37 / 69.32。

**W4/Q4：可扩展性与计算成本**

Appendix C 分析了复杂度：$O(NT+T)$ 训练，$O(T)$ 推理。值得注意的是，Figure 5(a-b) 显示 FlowSteer (Full) 使用*更少* token/轮次——RL 教会策略何时停止。

| | 训练 | 多后端部署 | IID Avg. |
|---|---|---|---|
| AFlow | ~10K MCTS 迭代/任务 | 每后端重搜 | 80.16 |
| Orchestrator | 多目标 RL | 每后端重训 | 82.37 |
| AgentFlow | Flow-GRPO | 每后端重训 | 75.17 |
| RouterR1 | RL | 每后端重训 | 82.29 |
| **FlowSteer** | **~8 GPU-hrs, 2×A100** | **训练一次，×6 零样本** | **84.33** |

向量化 rollout（Appendix G）：32 路并发、批量 API 调用、缓存状态、提前完成跳过。约 300 步，摊销约 1.3 GPU-hrs/后端。**Table R7**（新）：12→24 算子仅造成 −X.X IID / −X.X OOD 退化。

**Q5：跨后端泛化**

我们在 Section 5.4 (RQ3) 中对此进行了回应：Figure 4(a) 6 后端雷达图（DeepSeek-V3.2、Grok-4.1-Fast、GPT-5.2、Claude-Opus-4.5、Gemini-3-Flash、Qwen-Plus-Latest）；Figure 4(b) 按任务类别汇总增益；Figure 4(c) GPT-4o-mini 和 OSS-120B 训练动态均收敛。泛化源于在*工作流结构层面*编排——通过语义算子描述（Table 7）选择，而非后端特定 token。分解式动作空间 $O(\lvert A_\text{type}\rvert+\lvert\mathcal{O}\rvert)$ 无需任何后端适配。
