# AI 伦理与安全

---

## 一、模型偏见与公平性

### 1.1 偏见来源

- **数据偏见**：训练数据中的系统性偏差（性别、种族、文化等）
- **标注偏见**：标注者自身的偏见
- **选择偏见**：数据收集过程中的选择性
- **表征偏见**：某些群体在数据中被过度/不足代表
- **聚合偏见**：用总体模型处理具有不同分布的子群体

### 1.2 表现

- 性别偏见：如"医生"更多关联男性，"护士"更多关联女性
- 种族偏见：对某些族群的描述更负面
- 文化偏见：以英语/西方文化为中心
- 社会经济偏见：模型对不同经济水平群体的表现差异

### 1.3 缓解方法

- **数据层面**：重新采样、数据增强、去偏数据集构建
- **模型层面**：对抗训练（Adversarial Debiasing）、公平性约束、正则化
- **后处理**：对输出进行校准、阈值调整
- **评估与审计**：在不同子群体上分别评估模型性能

### 1.4 公平性指标

- **人口统计平等（Demographic Parity）**：各群体被预测为正类的比例相同
- **机会均等（Equal Opportunity）**：各群体的真正率（TPR）相同
- **均等化几率（Equalized Odds）**：各群体的 TPR 和 FPR 都相同
- **预测校准（Calibration）**：预测概率在各群体中同样准确
- **个体公平性（Individual Fairness）**：相似个体应获得相似预测

**公平性的不可能定理**：除非完美预测或基础比率相同，否则无法同时满足所有公平性标准。

---

## 二、对齐问题（Alignment）

### 2.1 核心问题

如何确保 AI 系统的行为与人类意图、价值观一致。

### 2.2 对齐的三个层次

- **指令遵循**：模型能正确理解和执行人类指令
- **价值对齐**：模型的输出符合人类价值观和社会规范
- **目标对齐**：模型的长期目标与人类利益一致

### 2.3 当前方法

- **RLHF / DPO**：通过人类反馈调整模型行为
- **Constitutional AI（CAI）**：用一组原则（Constitution）指导模型自我改进
  - 两阶段：AI 先根据原则批评并修改自己的回答（SL-CAI），再用 AI 反馈替代人类反馈进行 RL（RL-CAI）
- **Red Teaming**：系统性地寻找模型的安全漏洞
- **Scalable Oversight**：通过 AI 辅助人类进行更有效的评估

### 2.4 挑战

- **外部对齐 vs 内部对齐**：模型表面遵从但内部目标不一致（Deceptive Alignment）
- **价值观的多元性和文化差异**
- **能力泛化 vs 对齐泛化（Alignment Tax）**
- **Goodhart's Law**：当度量指标成为优化目标时，它不再是好的度量——奖励模型可能被利用（Reward Hacking）

---

## 三、幻觉问题（Hallucination）

### 3.1 定义

模型生成的内容看似合理但实际上是错误的或虚构的。

### 3.2 类型

- **事实性幻觉（Factual Hallucination）**：生成与现实不符的事实
- **忠实性幻觉（Faithfulness Hallucination）**：生成与给定上下文/源文档不符的内容
- **编造引用**：生成不存在的论文、数据、URL

### 3.3 原因

- 训练数据中的错误或过时信息
- 模型倾向于生成流畅文本而非准确文本（语言模型的本质是建模概率分布）
- 长尾知识覆盖不足
- 训练时缺乏有效的事实性约束
- **解码过程中的错误累积**：自回归生成中，早期的小偏差可能被放大

### 3.4 缓解方法

- **RAG（检索增强生成）**：让模型基于真实文档回答
- **引用生成（Citation Generation）**：要求模型标注信息来源
- **自我一致性检查（Self-Consistency）**：多次采样对比
- **不确定性表达**：训练模型在不确定时明确表示
- **事实核查模块**：独立的验证系统检查生成内容的准确性
- **知识蒸馏**：将结构化知识注入模型

---

## 四、AI 安全

### 4.1 对抗攻击（Adversarial Attack）

**对抗样本（Adversarial Examples）**：对输入添加人眼不可见的微小扰动，导致模型做出错误预测。

**攻击方法**：
- **FGSM（Fast Gradient Sign Method）**：x' = x + ε * sign(∇_x L(x, y))。单步攻击，快速但攻击强度有限
- **PGD（Projected Gradient Descent）**：迭代版 FGSM，多步小扰动，更强但更慢
- **C&W Attack**：基于优化的攻击，生成更小扰动的对抗样本
- **黑盒攻击**：不需要模型梯度信息，利用迁移性或查询

**对 LLM 的攻击**：
- **Prompt Injection（提示注入）**：通过输入数据中嵌入的恶意指令操纵模型行为
- **Jailbreak（越狱）**：通过精心构造的 prompt 绕过安全限制
- **对抗后缀**：在输入末尾添加特定 token 序列触发不安全输出

**对抗训练（Adversarial Training）**：将对抗样本加入训练数据，提高模型鲁棒性。

### 4.2 隐私与安全

**数据隐私**：训练数据可能包含个人信息，模型可能记忆并泄露。

- **Membership Inference Attack**：判断某个样本是否在训练集中
- **Training Data Extraction**：从模型中提取训练数据（如 GPT-2 可以生成训练集中的电话号码）

**差分隐私（Differential Privacy）**：
- 训练时在梯度上添加噪声（DP-SGD），保证单个样本对模型影响有限
- 形式化保证：对于任意两个只差一个样本的数据集 D 和 D'，模型输出的分布差异有界
- ε-differential privacy：P[M(D) ∈ S] ≤ e^ε · P[M(D') ∈ S]

**联邦学习（Federated Learning）**：
- 数据不出本地，各设备在本地训练后只上传模型参数/梯度
- 服务器聚合参数（如 FedAvg）更新全局模型
- 挑战：Non-IID 数据分布、通信开销、梯度泄露攻击

**模型窃取（Model Stealing）**：通过大量 API 查询复制模型功能。

### 4.3 可解释性（Interpretability / Explainability）

**为什么需要可解释性**：
- 建立用户信任
- 调试和改进模型
- 法律合规（如 GDPR 的"解释权"）
- 发现潜在偏见和安全问题

**方法**：
- **Attention 可视化**：查看模型关注哪些输入部分（但 attention ≠ explanation）
- **SHAP（SHapley Additive exPlanations）**：基于 Shapley 值的特征重要性
- **LIME（Local Interpretable Model-agnostic Explanations）**：用可解释的局部线性模型近似
- **Grad-CAM**：CNN 中基于梯度的类激活图，可视化模型关注的图像区域
- **Mechanistic Interpretability**：理解模型内部的计算机制（如 Superposition、Feature Circuits）
- **Probing**：训练探针分类器分析模型中间表示中编码的信息

### 4.4 AI 治理

**负责任的 AI 开发**：
- 安全评估和能力评估
- 红队测试（Red Teaming）
- 分级发布和访问控制
- 模型卡（Model Cards）和数据表（Datasheets）

**开源 vs 闭源的安全权衡**：
- 开源：促进研究和审查，但可能被滥用
- 闭源：更可控，但缺乏外部审查

**AI 监管框架**：
- **欧盟 AI Act**：基于风险分级的监管框架
- 各国的 AI 治理指南和伦理准则
- 行业自律标准和最佳实践

**长期风险**：
- 超级智能（Superintelligence）：远超人类能力的 AI 系统
- 控制问题（Control Problem）：如何确保超级 AI 系统可控
- 存在性风险（Existential Risk）：AI 对人类文明的潜在威胁

---

## 五、常见面试/笔试高频问题

### A. 概念辨析

1. **生成式模型 vs 判别式模型**：生成式建模 P(X, Y)（如朴素贝叶斯、VAE、GAN），判别式建模 P(Y|X)（如 Logistic 回归、SVM、神经网络）
2. **参数模型 vs 非参数模型**：参数模型有固定数量的参数（如线性回归），非参数模型参数数量随数据增长（如 KNN、决策树）
3. **Encoder-only vs Decoder-only vs Encoder-Decoder**：BERT / GPT / T5 的架构区别与适用场景
4. **Batch Normalization vs Layer Normalization**：BN 在 batch 维度归一化（适合 CNN），LN 在特征维度归一化（适合 RNN/Transformer）

### B. 推导与计算

1. Logistic 回归的梯度推导
2. SVM 对偶问题的推导（拉格朗日乘子法、KKT 条件）
3. 反向传播的链式法则推导
4. Self-Attention 的计算复杂度分析
5. Transformer 参数量估算：12Ld²（L 层，维度 d）

### C. 算法对比

1. GBDT vs XGBoost vs LightGBM 的核心区别
2. RNN vs LSTM vs Transformer 在序列建模上的对比
3. RLHF vs DPO 的原理对比
4. Various attention mechanisms（Self / Cross / Causal / Multi-Head）

### D. 开放性问题

1. 为什么 Transformer 能 Scale（Scaling Law：L(C) ∝ C^{-α}）
2. LLM 的 Emergent Abilities（涌现能力）有哪些？为什么出现？
3. 如何评估一个 LLM 的能力？（Benchmark：MMLU、GSM8K、HumanEval 等）
4. AI Agent 面临的核心挑战是什么？
5. 如何解决 LLM 的幻觉问题？
