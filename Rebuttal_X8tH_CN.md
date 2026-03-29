# 审稿人 X8tH（评分：4，弱接受）

感谢审稿人的积极评价。以下逐一回应各项关切，重点标注论文中已有的理论分析并补充新实验数据（完整数据见 [补充材料](LINK)）。

**W1/Q1：理论基础与奖励稳定性**

我们想强调，**Proposition 3（附录 B.2）**已提供条件释放机制的形式化分析，包含四个子证明：(a) **符号分离**（公式 45–48）——不满足结构约束的轨迹 $R(\tau) \in [-1, 0)$，满足的 $R(\tau) \geq 0$；(b) **两阶段优化**（公式 49–56）——条件释放可证明地将概率质量从不可行轨迹转移至可行轨迹，$p_\theta$ 单调递增（公式 55）；(c) **有界更新**（公式 57–60）——裁剪 + KL 联合约束每步策略变化；(d) **Token 掩码方差缩减**（公式 61–66）——无偏估计且 Var(mask) < Var(no-mask)。

实证上，新增 **表 R1** 消融隔离了每个组件：

| 变体 | IID 均值 | OOD 均值 |
|---|---|---|
| CWRPO (Full) | 85.15 | 59.59 |
| w/o Token Masking | 79.82 (−5.34) | 52.06 (−7.52) |
| w/o Diversity Reward | 80.08 (−5.08) | 50.30 (−9.28) |
| w/o Cond. Release | 80.86 (−4.30) | 52.85 (−6.74) |

关于奖励敏感性，**表 R5** 在保持所有机制激活的条件下变化超参：等权重（0.25×4）达 IID 79.56 / OOD 48.72，min_ops=4 达 IID 80.60 / OOD 47.54——渐进退化而非灾难性崩溃，证实对奖励塑形选择的鲁棒性。

**W2/Q2：消融粒度——解耦各贡献**

论文提供了**三个互补的消融层次**，共同解耦审稿人关注的各因素：

(1) **框架级**（Table 5）：w/o Multi-turn（IID −5.15）、w/o Canvas（IID −4.32）、w/o RL（IID −4.81）、w/o Agent（IID −3.32），隔离了交互式多轮 formulation。

(2) **RL 算法级**（Table 6）：相同多轮设置下 CWRPO vs. GRPO vs. DAPO，CWRPO 在全部 6 个 IID 基准上领先（如 MATH 81.25 vs. 73.43，SQuAD v2 EM 78.12 vs. 61.72）。此外，Table 3 中基于 PPO 的 baseline（AgentFlow、Orchestrator）使用相同后端但性能均低于 FlowSteer，进一步验证 CWRPO 相比标准策略优化的优势。

(3) **CWRPO 组件级**（上述表 R1）：独立隔离多样性约束、条件释放和 token 掩码。

交叉层次 (1) 和 (3) 直接回答 Q2：多轮 formulation 贡献 IID −5.15（Table 5），CWRPO 各机制贡献 −4.30 至 −5.34（表 R1）。两者均为实质性贡献且互补——多轮提供迭代细化能力，CWRPO 提供有效利用该能力的学习信号。

**W3/Q3：与 RL agent 框架的定位**

**附录 E Table 12** 提供六维架构对比，FlowSteer 是唯一满足全部六个维度的方法。核心区别：AgentFlow 使用 PPO 但采用固定 4 模块管线且无逐任务 prompt 定制；Router-R1 路由至固定模板，对中间反馈的适应有限；Orchestrator 通过 PPO 选择算子但缺少两步交互（add + set_prompt）实现细粒度配置。

FlowSteer 独特地通过多轮 Canvas 交互*动态编辑 workflow graph*（Definition 1）并定制逐任务 prompt。Proposition 2（附录 B.1）证明该多轮交互具有单调改进性质——每轮交互不会降低期望准确率，而无结构化反馈的迭代细化无法保证这一点。

**W4/Q4：可扩展性与计算成本**

**附录 C** 分析复杂度：$O(NT)$ 训练，$O(T)$ 推理。多轮成本通过向量化 rollout（**附录 G**）缓解：32 路并发轨迹交互、批量 API 调用、缓存工作流状态、提前结束跳过。

关键是，FlowSteer 训练一次即可零样本部署到 6 个后端（Figure 4），将训练成本摊销至各后端。**表 R6** 确认任务自适应推理成本：GSM8K 平均 8.3 轮 / \$0.0012，AIME 12.4 轮 / \$0.0019，Spearman ρ=0.65（p=0.021）。

**Q5：跨后端泛化**

**Figure 4 / Section 5.3（RQ3）**正是评估此问题：6 个架构差异显著的后端（DeepSeek-V3、Grok-4-Fast、GPT-5、Claude-Opus-4.5、Gemini-2.5-Flash、Qwen-Plus）。FlowSteer 在所有后端的 IID 和 OOD 基准上均实现一致提升。**表 R2–R4** 的算子迁移实验进一步验证泛化性是语义层面的而非硬编码的。
