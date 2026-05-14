# PINN Agent

基于 LangGraph 的 PINN 训练助手——人机协同的调参系统。Agent 负责训练监控、诊断和调参建议，用户负责确认和物理判断。

## Language

**Training Round**:
一轮完整的"训练→诊断→建议→确认"循环。每个 Training Round 包含一次 PINN 训练和后续的 Agent 分析。
_Avoid_: iteration, cycle, step

**Training Configuration**:
定义一轮训练的全部超参数集合，包括损失权重、优化器设置、采样策略和参数初始化。
_Avoid_: config, hyperparameter set, training setup

**Training History**:
训练过程中每步的损失值、参数估计值和预测误差的时间序列，是 Diagnosis 的输入。
_Avoid_: log, results, metrics, output

**Diagnosis**:
Agent 分析 Training History，识别训练问题并给出自然语言诊断结果的过程。输出为诊断描述和 Tuning Suggestion。
_Avoid_: analysis, review, assessment

**Tuning Suggestion**:
Agent 基于 Diagnosis 提出的具体参数调整方案（如"将 λ_pde 从 1 调至 10"），需经用户 Confirmation 后生效。
_Avoid_: recommendation, adjustment plan, advice

**Confirmation**:
用户审核 Agent 的 Tuning Suggestion 并决定是否执行的动作。Agent 不自动修改训练配置。
_Avoid_: approval, validation, sign-off

**Fixed-Config Baseline**:
评估对照组——使用固定 Training Configuration 跑完整个训练，不做任何调整。
_Avoid_: vanilla, default, standard

**Agent-Assisted**:
评估实验组——由 Agent 在每个 Training Round 后进行 Diagnosis 并提出 Tuning Suggestion，经用户 Confirmation 后调整配置。
_Avoid_: agent-guided, automated, adaptive

**Observation Data**:
逆问题中用于参数反演的观测数据，由解析解或数值解生成，可叠加高斯噪声。
_Avoid_: training data, ground truth, measurements

**Collocation Points**:
时空域内随机采样的点，用于计算 PDE 残差损失。
_Avoid_: sample points, grid points, mesh

**Loss Weights**:
Training Configuration 中各损失项的权重系数（λ_data, λ_pde, λ_ic, λ_bc），控制不同物理约束的相对重要性。
_Avoid_: loss coefficients, loss scaling

**Gradient Pathology**:
PINN 训练中不同损失项的梯度量级差异悬殊，导致优化偏向容易项而忽略重要项的失败模式。Burgers 和 Allen-Cahn 方程均易出现此问题。
_Avoid_: gradient imbalance, gradient conflict, stiffness

**RAG Knowledge**:
存入向量库的 PINN 领域知识（论文要点、调参指南、方程特性），在 Diagnosis 时检索提供给 LLM。
_Avoid_: knowledge base, documents, references

**Diagnostic Tool**:
LangGraph 中 Agent 可调用的计算工具（如计算梯度范数比、生成损失曲线图），为 LLM 提供它自身无法完成的数值能力。
_Avoid_: function, skill, plugin

## Relationships

- 一个 **Training Round** 消耗一个 **Training Configuration**，产出一组 **Training History**
- **Diagnosis** 读取 **Training History** 和 **RAG Knowledge**，调用 **Diagnostic Tool**，产出一个 **Tuning Suggestion**
- **Tuning Suggestion** 经 **Confirmation** 后生成新的 **Training Configuration**，进入下一个 **Training Round**
- **Fixed-Config Baseline** 执行单次完整训练，不经过 Diagnosis→Confirmation 循环
- **Agent-Assisted** 执行多个 Training Round，每轮经过完整的 Diagnosis→Tuning Suggestion→Confirmation 流程
- **Observation Data** 仅在逆问题中使用；正问题不需要
- **Loss Weights** 是 **Training Configuration** 的子集，是 **Gradient Pathology** 的主要调节手段

## Example dialogue

> **Dev:** "Diagnosis 发现 Gradient Pathology，Tuning Suggestion 是调 Loss Weights。用户 Confirmation 后，新的 Training Round 会自动跑吗？"
> **Domain expert:** "对，Confirmation 之后进入下一个 Training Round，用更新后的 Training Configuration 训练。但用户也可以拒绝 Tuning Suggestion，那本轮训练就结束了。"
> **Dev:** "那 Fixed-Config Baseline 怎么保证公平对比？"
> **Domain expert:** "同样的 Observation Data、同样的随机种子、同样的初始 Training Configuration。唯一区别是 Agent-Assisted 会在训练过程中调参。"

## Flagged ambiguities

- "训练数据"既可以指 Observation Data（逆问题的观测点），也可以指 Collocation Points（PDE 残差的配点）——已分别定义，代码和文档中禁止用"训练数据"泛指。
- "诊断"在通用语境下指医疗诊断，本项目特指 Agent 对 Training History 的分析——上下文足以消歧，无需额外限定。
