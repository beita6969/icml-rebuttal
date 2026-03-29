感谢审稿人的积极评价。以下结合已有理论分析和新实验数据逐一回应各项关切（完整数据见 https://anonymous.4open.science/r/Supplementary）。

**W1/Q1：理论基础与奖励稳定性**（新增：Tables R1, R5 | 论文：Prop. 3）

**Proposition 3（附录 B.2）**已提供形式化分析，包含四个子证明：(a) **符号分离**（公式 45–48）——不可行 $R(\\tau) \\in [-1, 0)$，可行 $R(\\tau) \\geq 0$；(b) **两阶段优化**（公式 49–56）——条件释放将概率质量转移至可行轨迹，$p\_\\theta$ 单调递增；(c) **有界更新**（公式 57–60）——裁剪 + KL 约束每步策略变化；(d) **Token 掩码方差缩减**（公式 61–66）——无偏估计且 $\\text{Var}(\\text{mask}) < \\text{Var}(\\text{no-mask})$。

实证上，新**表 R1** 消融隔离了每个组件：

| 变体 | IID 均值 | OOD 均值 |
|---|---|---|
| CWRPO (Full) | 85.15 | 59.59 |
| w/o Token Masking | 79.82 (−5.34) | 52.06 (−7.52) |
| w/o Diversity Reward | 80.08 (−5.08) | 50.30 (−9.28) |
| w/o Cond. Release | 80.86 (−4.30) | 52.85 (−6.74) |

这些下降验证了理论预测：Token 掩码的方差缩减（Prop. 3d）对需要干净梯度的数学推理至关重要（MATH −13.28）；条件释放（Prop. 3b）防止策略在长链 QA 上过早收敛（TriviaQA −9.38）；多样性奖励防止模式坍缩，OOD 影响最大（−9.28）。三种失效模式正交，证实 CWRPO 是最小完备设计。

**奖励设计对超参数选择具有鲁棒性。** 新**表 R5** 在保持所有机制激活的条件下测试 5 种配置：等权重（0.25×4）达 IID 82.29 / OOD 55.58；Checker‑heavy（0.4/0.2/0.2/0.2）达 IID 82.94 / OOD 54.41；min_ops ∈ {3, 4, 7} 达 IID 80.73–81.51 / OOD 52.29–53.92——所有变体均为渐进退化（IID −2.21 至 −4.42，OOD −4.01 至 −7.30）vs. 默认 IID 85.15 / OOD 59.59，证实对奖励塑形选择的鲁棒性。

**W2/Q2：消融粒度——解耦各贡献**（论文：Tables 5, 6 | 新增：Table R1）

我们设计了**三个互补的消融层次**来分离各因素的贡献：

(1) **框架级**（Table 5）：w/o Multi-turn（IID −5.15）、w/o Canvas（IID −4.32）、w/o RL（IID −4.81）、w/o Agent（IID −3.32），隔离了交互式多轮 formulation。

(2) **RL 算法级**（Table 6）：相同多轮设置下 CWRPO vs. GRPO vs. DAPO，CWRPO 在全部 6 个 IID 基准上领先（如 MATH 81.25 vs. 73.43，SQuAD v2 EM 78.12 vs. 61.72）。此外，Table 3 中基于 PPO 的 baseline（AgentFlow、Orchestrator）使用相同后端但性能均低于 FlowSteer，进一步验证 CWRPO 相比标准策略优化的优势。

(3) **CWRPO 组件级**（上述表 R1）：独立隔离多样性约束、条件释放和 token 掩码。

交叉层次 (1) 和 (3) 直接回答 Q2：多轮 formulation 贡献 IID −5.15（Table 5），而 CWRPO 各机制贡献 −4.30 至 −5.34（表 R1）。两者实质性且互补——多轮提供迭代细化能力，CWRPO 提供有效利用该能力的学习信号。

**W3/Q3：与 RL agent 框架的定位**（论文：Appendix E, Table 12）

**附录 E Table 12** 提供六维对比，FlowSteer 是唯一满足全部六个维度的方法。核心区别：AgentFlow 使用 PPO 但采用固定 4 模块管线且无逐任务 prompt 定制；Router-R1 路由至固定模板；Orchestrator 通过 PPO 选择算子但缺少两步交互（add + set_prompt）实现细粒度配置。

FlowSteer 独特地*动态编辑 workflow graph*（Definition 1）并定制逐任务 prompt。Proposition 2（附录 B.1）证明此交互具有单调改进性质——每轮不会降低期望准确率。

**W4/Q4：可扩展性与计算成本**（新增：Table R6 | 论文：Appendix C, G）

**FlowSteer 的多轮开销是可控的。** 附录 C 显示训练 $O(NT)$、推理 $O(T)$，通过向量化 rollout（附录 G）缓解：32 路并发轨迹、批量 API 调用、缓存状态、提前结束跳过。

FlowSteer 训练一次即可零样本部署到 6 个甚至更多后端（Figure 4），无需逐后端调优。新**表 R6** 确认任务自适应推理成本：GSM8K 平均 8.3 轮 / \$0.0012，AIME 12.4 轮 / \$0.0019（Spearman ρ=0.65，p=0.021）。

**Q5：跨后端泛化**（论文：Figure 4 | 新增：Tables R2–R4）

**Figure 4 / Section 5.3（RQ3）**正是评估此问题：6 个架构差异显著的后端（DeepSeek-V3、Grok-4-Fast、GPT-5、Claude-Opus-4.5、Gemini-2.5-Flash、Qwen-Plus）。FlowSteer 在所有后端均实现一致 IID 和 OOD 提升。新**表 R2–R4** 进一步验证泛化性是语义层面的而非硬编码的。
