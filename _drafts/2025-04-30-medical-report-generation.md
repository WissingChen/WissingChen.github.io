---
title: 'Medical Report Generation'
date: 2025-04-30
permalink: /posts/2025/04/mrg/
tags:
  - medical image analysis
  - multi-modal
---

医疗行业正经历数字化转型，其中医学报告生成是一个关键环节。每天，全球医院产生数百万份影像数据，将这些影像和结构化数据转化为清晰、准确的文字报告是医疗工作的重要部分。

医学报告生成的自动化面临几个关键痛点：首先是效率问题——放射科医生需要花费大量时间撰写报告，自动化可以显著提高工作效率；其次是质量一致性——人工报告可能因经验、疲劳等因素导致质量波动；最后是医疗资源分配不均——许多偏远地区缺乏专业医师，自动报告生成可以部分弥补这一差距。

与一般的自然语言生成任务相比，医学报告生成有其特殊性：对准确性的要求极高（错误可能导致严重后果）、需要符合专业规范（使用标准医学术语）、必须具备可解释性（支持医生的决策过程）。这些特点使其成为AI与医学交叉领域的重要研究方向。尤其是在大模型时代，结合多模态信息以自然语言的方式进行解释的医学大模型逐渐受到了广泛的关注。




3. 主流技术路线回顾
3.1 模板/规则方法（2000年代至今）
基于模板的方法是最早应用的技术：

优势：

高度可控，几乎不会产生幻觉（hallucination）内容
输出符合规范，结构一致
劣势：

覆盖面有限，难以处理长尾罕见情况
缺乏灵活性，表达方式单一
这类方法通常依靠预定义的模板库和决策树，根据检测到的异常填充相应描述。尽管看似简单，在某些标准化程度高的检查中仍有广泛应用。

3.2 检索后编辑（Retrieve-then-Edit）
这是结合信息检索(IR)和自然语言生成(NLG)的混合方法：

使用密集检索技术（如BM25算法或Dense Passage Retriever）找出相似病例
通过槽位填充(Slot-filling)或序列到序列(Seq2Seq)模型微调检索到的报告模板
代表性工作包括M²Trans和R2Gen-KD，前者利用记忆机制存储和检索相似报告，后者通过知识蒸馏增强模型的检索能力。

这种方法平衡了可控性和灵活性，特别适合中等复杂度的医学报告生成任务。

3.3 编码器-解码器架构
基于深度学习的端到端模型是近年研究热点：

早期工作采用Show-Attend-Tell、CNN-RNN with attention等架构
R2Gen模型结合ResNet、Transformer和报告记忆机制，成为该领域的基准
这类方法面临的主要挑战是长文本生成和事实一致性保证
编码器负责提取影像特征，解码器将这些特征转化为连贯文本。虽然灵活性高，但可能产生不准确描述或遗漏关键发现。

3.4 强化学习与后编辑
为解决监督学习的局限性，研究者引入强化学习：

先通过监督学习建立基线模型
再使用临床驱动的奖励函数（如CIDEr分数与CheXbert标签准确性的组合）进行强化学习
HRGR-Agent和PPG-RL是代表性工作，前者结合层次结构改进报告生成，后者使用策略梯度方法优化模型
后编辑策略可进一步提升准确性，模型生成初稿后经过专门的编辑模型修正错误或补充关键信息。

3.5 知识注入
医学知识的显式引入是提高准确性的重要手段：

通过实体图谱（如RadGraph）编码解剖和病理知识，结合图神经网络(GNN)或知识感知注意力机制
使用医学词汇约束解码，确保生成的报告使用标准术语
通过UMLS（统一医学语言系统）链接，增强模型对医学概念的理解
知识注入帮助模型从纯统计学习迈向有医学理解的生成，大幅减少专业性错误。

4. 最新进展（2023-2024 Q2）
4.1 视觉-语言大模型(VLM)
多模态大型语言模型带来医学报告生成的革命性变化：

通用VLM：GPT-4o、Gemini 1.5 Pro等支持32k+上下文窗口的多模态大模型展现出强大的零样本和少样本医学理解能力
医学专用VLM：Med-PaLM M、LLaVA-Med、BioGPT-v2等经过医学指令微调的模型进一步提升了适用性
端到端方案："ViT-Adapter + LLaMA2-Chat"等组合能实现从细分病灶定位到报告草稿的完整流程
这些模型展现出前所未有的语义理解能力，能够捕捉微妙的影像特征并生成连贯、专业的描述。

4.2 检索增强生成(RAG)在放射学中的应用
RAG技术显著提升了医学报告的准确性：

利用向量数据库（如FAISS、Milvus）存储和检索相似病例
放射学专用LLM结合精心设计的提示词计划（prompt-planning）
动态插入相似病例与解剖知识，实验表明可提升事实准确性3-5%
RAG框架使模型能够参考类似病例、查询最新指南，减少知识盲点导致的错误。

4.3 结构化到自由文本的混合范式
先抽取后生成的两阶段方法受到关注：

"解释后生成"（Explain-then-Generate）模式：先识别关键发现，再生成详细解释
IBM Medical Summarizer和RadGraph-2-Text是代表性工作，前者通过多步骤推理增强可靠性，后者利用图结构表示临床发现
这种方法将复杂任务分解为相对简单的子任务，每个子任务可以更容易地达到高准确性。

4.4 对齐与安全
随着临床应用的推进，安全性成为焦点：

基于放射科医生反馈的人类反馈强化学习(RLHF)
自一致性检查与验证链(Chain-of-Verification, CoVe)提示词技术
事实性水印和不确定性估计，明确标记模型确信度较低的部分
这些技术确保AI系统与医疗实践对齐，减少误导性内容，提高临床可靠性。

======

医学报告生成技术正从"模型能写"阶段迈向"写得安全、可落地"的新阶段。真正具有差异化竞争力的解决方案需要同时解决数据合规问题、落实专科知识、整合端到端工作流。

随着技术不断成熟，我们可以期待AI辅助医学报告生成为医疗工作带来效率提升和质量提高，尤其是在资源紧张的地区发挥更大价值。这一领域欢迎更多研究者和工程师加入，共同探索、开源分享，推动技术发展与临床落地。


Acknowledge
======


Reference
------