# positive unlabeled learning

_生成时间：2026-05-19 23:03:05_

# 科研智能体领域综述

## 背景与核心问题

正-无标记学习（Positive-Unlabeled Learning, PU Learning）是弱监督学习中的一个重要分支，其核心设定是：训练数据中仅包含少量标注的正样本和大量未标注的样本（混合了正类和负类），而完全没有标注的负样本。这一设定高度契合现实世界中许多场景——例如药物发现中仅有已知有效的化合物、异常检测中仅有正常样本、文献数据中仅报告成功反应——因此近年来受到广泛关注。PU学习的核心挑战在于：如何在缺乏可靠负样本的情况下，学习一个能够准确区分正类和负类的二分类器，同时避免模型因标签偏差或类别不平衡而产生过拟合或预测偏好。

## 主流路线与子方向

### 1. 基于风险估计的PU学习
该路线通过设计特殊的风险估计器，在经验风险最小化框架下直接利用正样本和无标签样本训练分类器。代表性工作包括非负风险估计器（NeurIPS 2017），它解决了无偏风险估计器在模型过于灵活时可能变为负值、导致严重过拟合的问题。后续工作进一步引入自校正正则化风险（IEEE TAI 2026），无需精确的正类先验即可实现鲁棒训练。这类方法的优势在于理论严谨，但通常依赖于标签完全随机选择（SCAR）假设。

### 2. 基于负样本选择的PU学习
该路线通过从无标签数据中筛选出可靠的负样本，将PU问题转化为标准的二分类问题。典型方法如PULNS（AAAI 2021），利用强化学习（REINFORCE算法）自动选择负样本，无需类别先验知识。另一种思路是利用异常检测技术识别无标签数据中的正例（异常点），如基于最近邻隔离森林的两步方法（Data Mining and Knowledge Discovery 2023）。这类方法灵活性强，但筛选质量直接影响下游分类性能。

### 3. 结合表示学习的PU学习
近年来，对比学习和自监督方法被引入PU学习，以提升特征表示的质量。WConPU（IEEE TNNLS 2025）提出了加权对比学习与难负样本挖掘的迭代互促框架，从期望最大化角度提供了理论证明。puCL（arXiv 2024）则设计了无偏且方差缩减的对比学习目标，将弱监督正样本信息融入对比损失。这类方法在低监督场景下表现优异，但计算成本相对较高。

### 4. 面向特定应用领域的PU学习
PU学习在生物医药、化学、材料科学等领域展现出巨大潜力。在有机反应产率预测中，PAYN（JACS 2026）首次将PU学习应用于解决文献数据的选择性报告偏差问题。在药物发现中，ULCYP（JCIM 2026）利用PU学习预测细胞色素P450诱导剂，DWPL-GCNMF（JCIM 2026）则结合图卷积网络与动态加权伪标签预测药物不良反应。在材料科学中，MOFClassifier（JACS 2025）将PU学习与晶体图卷积神经网络结合，验证金属有机框架的结构可计算性。这些工作展示了PU学习在解决真实世界数据稀缺和偏差问题上的独特价值。

### 5. 处理标签偏差与分布偏移的PU学习
现实场景中，正样本的标注往往存在选择偏差（非随机标注）。PUe（NeurIPS 2023）利用因果推断中的归一化逆概率加权技术，将SCAR假设放宽至更一般的SAR假设。Learning from Positive and Unlabeled Data with Arbitrary Positive Shift（NeurIPS 2020）则进一步允许正类分布任意偏移，仅需负类分布固定。这类方法为PU学习在动态环境下的应用提供了理论基础。

## 代表性工作差异

**非负风险估计器（NeurIPS 2017）** 与 **自校正正则化风险（IEEE TAI 2026）** 同属风险估计路线，但前者聚焦于解决过拟合问题，通过强制风险非负来允许使用深度神经网络；后者则无需正类先验，通过两阶段热启动和自校正机制缓解误标记问题，在图像和文本基准上一致优于前者。

**PULNS（AAAI 2021）** 与 **RR-PU（AAAI 2024）** 均采用两阶段框架，但思路截然不同。PULNS利用强化学习从无标签数据中筛选负样本，无需先验知识；RR-PU则针对逃税检测场景，通过重分组技术和可训练松弛变量协同修正标签频率估计偏差，解决了两阶段流程中误差累积的问题。

**PAYN（JACS 2026）** 与 **Predicting Substrate Reactivity（ACS Omega 2025）** 均将PU学习应用于有机化学，但任务不同。PAYN解决的是文献数据中高产率反应的选择性报告偏差问题，采用基于间谍技术的PU学习方法；后者则预测苯酚氧化自偶联反应的底物反应性，利用28维描述符和扩展连接指纹，在无需负样本的情况下达到83.3%-86.7%的预测准确率。

**MOFClassifier（JACS 2025）** 与 **DWPL-GCNMF（JCIM 2026）** 均涉及材料/药物领域，但技术路线差异显著。MOFClassifier采用正-无标签晶体图卷积神经网络，提出晶体相似度分数作为分类指标，ROC AUC达到0.979；DWPL-GCNMF则融合知识图谱图卷积网络与矩阵分解，通过动态加权伪标签学习预测药物-不良反应关联，在DrugBank上AUPR达到0.6656。

## 瓶颈与趋势

1. **标签偏差假设的放宽**：当前多数PU学习方法仍依赖SCAR假设，但现实场景中正样本标注往往存在选择偏差。将SAR假设甚至更一般的非随机选择假设纳入理论框架，是提升方法实用性的关键方向。

2. **类先验估计的准确性**：许多PU方法需要已知或准确估计的正类先验（正例比例）。现有估计方法在高度不平衡或高维数据中仍不稳定，开发鲁棒且无需先验的方法是重要趋势。

3. **数据与实验闭环**：在化学、材料等交叉领域，PU学习模型的预测结果需要与实验验证形成闭环。当前多数工作仅停留在计算验证阶段，如何设计高效的实验反馈机制以迭代优化模型，是推动实际应用的关键。

4. **可复现性与标准化评测**：PU学习领域缺乏统一的基准数据集和评测协议，不同工作使用的数据划分、评估指标差异较大，导致方法间难以公平比较。建立标准化的PU学习评测平台（如涵盖不同偏差类型、不平衡程度、数据维度的基准）迫在眉睫。

5. **系统工程与安全合规**：在金融欺诈检测、医疗诊断等高风险场景中，PU学习模型的可解释性和公平性至关重要。当前仅有少数工作（如Fair SSPUL for Alzheimer's Disease, NPJ Digital Medicine 2025）关注公平性问题，将PU学习与可解释AI、偏差缓解技术结合是未来重要方向。


---

## 1) 重要论文清单

> Core：严格筛选（顶会/顶刊/大厂/大牛/高潜力），Extended：覆盖面（放宽门槛但仍有黑名单与质量门槛）。

### Core（37 篇）

| # | Title | Year | Venue | Sources | Score | Rel | 内容类型 | 影响因子/分区/会议等级 | Code | PDF |
|---:|---|---:|---|---|---:|---:|---|---|---|---|
| 1 | **Positive-Unlabeled Learning with Non-Negative Risk Estimator** | 2017 | Neural Information Processing Systems | semanticscholar, arxiv | 0.785 | 0.000 | pdf原文 | Conf=A* |  | [pdf](pdfs/paper_001_a63d6d21.pdf) |
| 2 | **Yield Prediction of Organic Reactions in Biased Data Sets via Positive-Unlabeled Learning** | 2026 | Journal of the American Chemical Society | openalex, semanticscholar, crossref | 0.736 | 0.000 | 摘要 |  |  | [pdf](https://pmc.ncbi.nlm.nih.gov/articles/PMC13088182/pdf/ja6c00127.pdf) |
| 3 | **A Positive-Unlabeled Learning Approach With Self-Correcting Regularized Risk** | 2026 | IEEE Transactions on Artificial Intelligence | semanticscholar, openalex, crossref | 0.702 | 0.000 | 摘要 | CAS=1Q |  |  |
| 4 | **PULNS: Positive-Unlabeled Learning with Effective Negative Sample Selector** | 2021 | AAAI Conference on Artificial Intelligence | semanticscholar, openalex, crossref | 0.692 | 0.000 | 摘要 | CAS=1Q |  | [pdf](https://ojs.aaai.org/index.php/AAAI/article/download/17064/16871) |
| 5 | **Learning with Complementary Labels Revisited: The Selected-Completely-at-Random Setting Is More Practical** | 2023 | International Conference on Machine Learning | semanticscholar, arxiv | 0.691 | 0.000 | pdf原文 | Conf=A* |  | [pdf](pdfs/paper_002_7f178c94.pdf) |
| 6 | **MOFClassifier: A Machine Learning Approach for Validating Computation-Ready Metal-Organic Frameworks.** | 2025 | Journal of the American Chemical Society | openalex, pubmed, semanticscholar, crossref | 0.690 | 0.000 | pdf原文 |  |  | [pdf](pdfs/10.1021_jacs.5c10126.pdf) |
| 7 | **Positive-Unlabeled Learning With Label Distribution Alignment.** | 2023 | IEEE Transactions on Pattern Analysis and Machine Intelligence | semanticscholar, openalex, pubmed, crossref | 0.680 | 0.000 | 摘要 |  |  |  |
| 8 | **On Positive-Unlabeled Classification From Corrupted Data in GANs.** | 2025 | IEEE Transactions on Pattern Analysis and Machine Intelligence | semanticscholar, openalex, crossref, pubmed | 0.678 | 0.000 | 摘要 |  |  |  |
| 9 | **Learning from Positive and Unlabeled Data with Arbitrary Positive Shift** | 2020 | Neural Information Processing Systems | semanticscholar, arxiv | 0.675 | 0.000 | pdf原文 | Conf=A* |  | [pdf](pdfs/paper_003_ce0545f7.pdf) |
| 10 | **Positive-unlabeled learning for anomaly detection based on dual-branch generative adversarial networks** | 2026 | Knowledge-Based Systems | semanticscholar, openalex, crossref | 0.669 | 0.000 | 元数据 | CAS=2Q |  |  |
| 11 | **Two-Stage Angular Alignment for Positive-Unlabeled Learning** | 2026 | Proceedings of the 15th International Conference on Pattern Recognition Applications and Methods | semanticscholar, crossref, openalex | 0.669 | 0.000 | 摘要 | JCR=Q1 |  |  |
| 12 | **Multiple Instance Learning With Instance-Level Positive-Unlabeled Learning in Anomaly Detection** | 2025 | IEEE Access | semanticscholar, crossref, openalex | 0.669 | 0.000 | 摘要 | CAS=2Q |  |  |
| 13 | **Weighted Contrastive Learning With Hard Negative Mining for Positive and Unlabeled Learning.** | 2025 | IEEE Transactions on Neural Networks and Learning Systems | semanticscholar, openalex, pubmed, crossref | 0.665 | 0.000 | 摘要 |  |  |  |
| 14 | **KG2ML: integrating knowledge graphs and positive unlabeled learning for identifying disease-associated genes.** | 2025 | Frontiers in bioinformatics | pubmed | 0.664 | 0.000 | 摘要 | CAS=1Q |  | [pdf](https://pmc.ncbi.nlm.nih.gov/articles/PMC10463539/pdf/) |
| 15 | **Localization of macromolecules in crowded cellular cryo-electron tomograms from extremely sparse labels.** | 2025 | Briefings in bioinformatics | pubmed | 0.664 | 0.000 | pdf原文 | CAS=2Q |  | [pdf](pdfs/10.1016_j.neunet.2019.08.025.pdf) |
| 16 | **Fair positive unlabeled learning for predicting undiagnosed Alzheimer's disease in diverse electronic health records.** | 2025 | NPJ digital medicine | pubmed | 0.664 | 0.000 | pdf原文 | JCR=Q2 | [code](http://github.com/suinleelab/treexplainer-study.) | [pdf](pdfs/10.1038_s42256-019-0138-9.pdf) |
| 17 | **Predicting Substrate Reactivity in Oxidative Homocoupling of Phenols Using Positive and Unlabeled Machine Learning.** | 2025 | ACS omega | pubmed | 0.664 | 0.000 | pdf原文 | CAS=2Q |  | [pdf](pdfs/10.1186_s12864-019-6413-7.pdf) |
| 18 | **Improving drug repositioning with negative data labeling using large language models.** | 2025 | Journal of cheminformatics | pubmed | 0.664 | 0.000 | 摘要 | CAS=2Q |  | [pdf](https://pmc.ncbi.nlm.nih.gov/articles/PMC3106198/pdf/) |
| 19 | **A positive-unlabeled learning approach for industrial anomaly detection based on self-adaptive training** | 2025 | Neurocomputing | openalex, semanticscholar, crossref | 0.660 | 0.000 | 元数据 | JCR=Q2 |  |  |
| 20 | **RR-PU: A Synergistic Two-Stage Positive and Unlabeled Learning Framework for Robust Tax Evasion Detection** | 2024 | AAAI Conference on Artificial Intelligence | semanticscholar, openalex, crossref | 0.660 | 0.000 | 摘要 | CAS=1Q |  | [pdf](https://ojs.aaai.org/index.php/AAAI/article/download/28665/29291) |
| 21 | **Positive unlabeled learning for building recommender systems in a parliamentary setting** | 2018 | Information Sciences | semanticscholar, openalex, crossref | 0.648 | 0.000 | pdf原文 | CAS=2Q |  | [pdf](pdfs/10.1016_j.ins.2017.12.046.pdf) |
| 22 | **Confidence-Based PU Learning With Instance-Dependent Label Noise.** | 2025 | IEEE Transactions on Neural Networks and Learning Systems | semanticscholar, pubmed, openalex, crossref | 0.645 | 0.000 | 摘要 |  |  |  |
| 23 | **Positive unlabeled learning with tensor networks** | 2022 | Neurocomputing | semanticscholar, openalex, crossref | 0.639 | 0.000 | 摘要 | JCR=Q2 |  | [pdf](https://doi.org/10.1016/j.neucom.2023.126556) |
| 24 | **GKF-PUAL: A group kernel-free approach to positive-unlabeled learning with variable selection** | 2024 | Information Sciences | openalex, semanticscholar, crossref | 0.636 | 0.000 | pdf原文 | CAS=2Q | [code](https://github.com/tkks22123/GKF-PUAL) | [pdf](pdfs/10.1016_j.ins.2024.121574.pdf) |
| 25 | **Positive-unlabeled learning to infer protection status and identify correlates in vaccine efficacy field trials.** | 2024 | iScience | pubmed | 0.636 | 0.000 | pdf原文 | JCR=Q1 |  | [pdf](pdfs/10.1007_s10994-020-05877-5.pdf) |
| 26 | **Class Prior Estimation in Active Positive and Unlabeled Learning** | 2020 | International Joint Conference on Artificial Intelligence | openalex, semanticscholar, crossref | 0.621 | 0.000 | pdf原文 | Conf=A |  | [pdf](pdfs/10.24963_ijcai.2020_403.pdf) |
| 27 | **Dense-PU: Learning a Density-Based Boundary for Positive and Unlabeled Learning** | 2023 | IEEE Access | semanticscholar, openalex, crossref | 0.621 | 0.000 | 摘要 | CAS=2Q |  | [pdf](https://doi.org/10.1109/access.2024.3420453) |
| 28 | **Few-shot anomaly detection using positive unlabeled learning with cycle consistency and co-occurrence features** | 2024 | Expert systems with applications | openalex, semanticscholar, crossref | 0.617 | 0.000 | 元数据 | CAS=2Q |  |  |
| 29 | **From Lazy to Prolific: Tackling Missing Labels in Open Vocabulary Extreme Classification by Positive-Unlabeled Sequence Learning** | 2024 | North American Chapter of the Association for Computational Linguistics | openalex, semanticscholar | 0.617 | 0.000 | pdf原文 | Conf=A |  | [pdf](pdfs/10.48550_arxiv.2408.08981.pdf) |
| 30 | **Predicting protein functions using positive-unlabeled ranking with ontology-based priors.** | 2024 | Bioinformatics (Oxford, England) | pubmed | 0.614 | 0.000 | pdf原文 | CAS=1Q | [code](https://github.com/bio-ontology-research-group/PU-GO.) | [pdf](pdfs/10.1038_nmeth.2340.pdf) |
| 31 | **Positive-unlabeled learning for open set domain adaptation** | 2020 | Pattern Recognition Letters | semanticscholar, openalex, crossref | 0.601 | 0.000 | 摘要 | JCR=Q1 |  |  |
| 32 | **Addressing the Cold-Start Problem in Collaborative Filtering Through Positive-Unlabeled Learning and Multi-Target Prediction** | 2022 | IEEE Access | semanticscholar, openalex, crossref | 0.594 | 0.000 | 摘要 | CAS=2Q |  | [pdf](https://doi.org/10.1109/access.2022.3219071) |
| 33 | **PONYTA: prioritization of phenotype-related genes from mouse KO events using PU learning on a biological network.** | 2024 | Bioinform. | semanticscholar, pubmed, openalex, crossref | 0.576 | 0.000 | 摘要 |  | [code](https://github.com/Jun-Hyeong-Kim/PONYTA.) | [pdf](https://pmc.ncbi.nlm.nih.gov/articles/PMC8425308/pdf/) |
| 34 | **Learning from Positive and Unlabeled Multi-Instance Bags in Anomaly Detection** | 2023 | Knowledge Discovery and Data Mining | openalex, semanticscholar, crossref | 0.555 | 0.000 | pdf原文 | Conf=A |  | [pdf](pdfs/10.1145_3580305.3599409.pdf) |
| 35 | **Screening and Predicting Multi-Omics T-ALL Core Genes Based on PU Learning.** | 2025 | IEEE Transactions on Computational Biology and Bioinformatics | semanticscholar, pubmed, openalex, crossref | 0.546 | 0.000 | 摘要 | CAS=1Q |  |  |
| 36 | **Multiobjective Optimization of Metal-Organic Framework Structural Properties and Synthesis Costs through Machine Learning.** | 2025 | Journal of Chemical Information and Modeling | semanticscholar, openalex, pubmed, crossref | 0.530 | 0.000 | 摘要 | CAS=2Q |  | [pdf](https://pubs.acs.org/doi/pdf/10.1021/acs.jcim.5c01730) |
| 37 | **A two-step anomaly detection based method for PU classification in imbalanced data sets** | 2023 | Data mining and knowledge discovery | openalex, semanticscholar, crossref | 0.526 | 0.000 | pdf原文 | CAS=2Q |  | [pdf](pdfs/10.1007_s10618-023-00925-9.pdf) |

### Extended（23 篇）

| # | Title | Year | Venue | Sources | Score | Rel | 内容类型 | 影响因子/分区/会议等级 | Code | PDF |
|---:|---|---:|---|---|---:|---:|---|---|---|---|
| 1 | **Yield Prediction of Organic Reactions in Biased Data Sets via Positive-Unlabeled Learning.** | 2026 | Journal of the American Chemical Society | pubmed | 0.736 | 0.000 | 摘要 |  |  | [pdf](https://pmc.ncbi.nlm.nih.gov/articles/PMC10777403/pdf/) |
| 2 | **ULCYP: A Multitask Model for Predicting P450 Inducers Based on Positive-Unlabeled Learning.** | 2026 | Journal of chemical information and modeling | pubmed, openalex, semanticscholar, crossref | 0.730 | 0.000 | 摘要 | CAS=2Q |  | [pdf](https://pubs.acs.org/doi/pdf/10.1021/acs.jcim.6c00305) |
| 3 | **Prediction of bacterial protein-compound interactions with only positive samples.** | 2026 | Bioinformatics (Oxford, England) | pubmed | 0.697 | 0.000 | 摘要 | CAS=1Q | [code](https://github.com/datax-lab/CYP.) | [pdf](https://pmc.ncbi.nlm.nih.gov/articles/PMC2718640/pdf/) |
| 4 | **Prediction of plant phase-separating proteins using positive-unlabeled learning.** | 2026 | Genome biology | pubmed, openalex, semanticscholar, crossref | 0.697 | 0.000 | pdf原文 | CAS=1Q |  | [pdf](pdfs/10.1186_s13059-026-04075-1.pdf) |
| 5 | **PUe: Biased Positive-Unlabeled Learning Enhancement by Causal Inference** | 2023 | Neural Information Processing Systems | semanticscholar, openalex, crossref | 0.680 | 0.000 | pdf原文 | Conf=A* | [code](https://github.com/huawei-noah/Noah-research) | [pdf](pdfs/10.52202_075280-0869.pdf) |
| 6 | **Identifying Adaptive Footprints in the Presence of Demographic Uncertainty.** | 2026 | Genome biology and evolution | pubmed | 0.674 | 0.000 | 摘要 | CAS=1Q |  | [pdf](https://pmc.ncbi.nlm.nih.gov/articles/PMC4694199/pdf/) |
| 7 | **A GeoAI framework for detecting risk zones from illegal dumping sites in Khulna, Bangladesh.** | 2026 | PloS one | pubmed | 0.674 | 0.000 | pdf原文 | CAS=2Q |  | [pdf](pdfs/10.1016_j.inffus.2021.11.011.pdf) |
| 8 | **Learning from sanctioned government suppliers: a machine learning and network science approach to detecting fraud and corruption in Mexico.** | 2026 | Scientific reports | pubmed, openalex, crossref | 0.674 | 0.000 | pdf原文 | CAS=2Q |  | [pdf](pdfs/10.1038_s41598-026-48873-w.pdf) |
| 9 | **Improving Non-Negative Positive-Unlabeled Learning for News Headline Classification** | 2023 | IEEE Access | openalex, semanticscholar, crossref | 0.670 | 0.000 | 摘要 | CAS=2Q |  | [pdf](https://ieeexplore.ieee.org/ielx7/6287639/6514899/10106254.pdf) |
| 10 | **A heterogeneous graph neural network for candidate gene prediction in endometriosis.** | 2026 | Journal of biomedical informatics | pubmed, openalex, semanticscholar, crossref | 0.669 | 0.000 | 摘要 | CAS=2Q |  |  |
| 11 | **Candidate correlates of protection in the HVTN505 HIV-1 vaccine efficacy trial identified by positive-unlabeled learning.** | 2025 | PLoS computational biology | pubmed | 0.664 | 0.000 | pdf原文 | CAS=2Q | [code](https://github.com/python/cpython) | [pdf](pdfs/10.1038_s41592-019-0686-2.pdf) |
| 12 | **DWPL-GCNMF: Structure-Aware Dynamic Weighted Pseudo-Label Learning for Adverse Drug Reaction Prediction.** | 2026 | Journal of chemical information and modeling | pubmed, openalex, semanticscholar, crossref | 0.652 | 0.000 | 摘要 | CAS=2Q |  | [pdf](https://pubs.acs.org/doi/pdf/10.1021/acs.jcim.6c00294) |
| 13 | **Predicting Partially Observed Long-Term Outcomes with Adversarial Positive-Unlabeled Domain Adaptation.** | 2025 | Proceedings of machine learning research | pubmed | 0.641 | 0.000 | 摘要 | CAS=2Q |  | [pdf](https://pmc.ncbi.nlm.nih.gov/articles/PMC9580228/pdf/) |
| 14 | **Machine learning-based prediction reveals kinase MAP4K4 regulates neutrophil differentiation through phosphorylating apoptosis-related proteins.** | 2025 | PLoS computational biology | pubmed | 0.641 | 0.000 | pdf原文 | CAS=2Q |  | [pdf](pdfs/10.1038_s41587-021-00859-x.pdf) |
| 15 | **Leveraging permutation testing to assess confidence in positive-unlabeled learning applied to high-dimensional biological datasets.** | 2024 | BMC bioinformatics | pubmed | 0.636 | 0.000 | pdf原文 | CAS=2Q |  | [pdf](pdfs/10.1038_s41586-020-2649-2.pdf) |
| 16 | **ROPU: A robust online positive-unlabeled learning algorithm** | 2024 | Knowledge-Based Systems | openalex, semanticscholar, crossref | 0.632 | 0.000 | 元数据 | CAS=2Q |  |  |
| 17 | **Code for Global Photovoltaic Solar Panel Dataset from 2019 to 2022** | 2025 | Scientific data | pubmed, openalex | 0.628 | 0.000 | 摘要 | JCR=Q2 |  | [pdf](https://pmc.ncbi.nlm.nih.gov/articles/PMC12003729/pdf/) |
| 18 | **Identification of Protein-Protein Interaction (PPI) Sites on the Influenza A (H1N1) Viral Genome Using Gradient Boosting and Artificial Neural Network (ANN) Models.** | 2025 | ACS omega | pubmed | 0.574 | 0.000 | 摘要 | CAS=2Q |  | [pdf](https://pmc.ncbi.nlm.nih.gov/articles/PMC5751251/pdf/) |
| 19 | **Focused PU learning from imbalanced data** | 2026 | arXiv | semanticscholar, arxiv | 0.553 | 0.000 | pdf原文 |  |  | [pdf](pdfs/paper_055_9a3d8ffe.pdf) |
| 20 | **PUMAD: PU Metric learning for anomaly detection** | 2020 | Information Sciences | semanticscholar, openalex, crossref | 0.541 | 0.000 | 摘要 | CAS=2Q |  |  |
| 21 | **Understanding Contrastive Representation Learning from Positive Unlabeled (PU) Data** | 2024 | arXiv | semanticscholar, arxiv | 0.499 | 0.000 | 摘要 |  |  | [pdf](https://arxiv.org/pdf/2402.06038v2) |
| 22 | **Positive Unlabeled Learning Selected Not At Random (PULSNAR): class proportion estimation when the SCAR assumption does not hold** | 2023 | arXiv | semanticscholar, arxiv | 0.493 | 0.000 | pdf原文 |  |  | [pdf](pdfs/paper_059_77e1204a.pdf) |
| 23 | **Risk bounds for PU learning under Selected At Random assumption** | 2022 | arXiv | semanticscholar, arxiv | 0.485 | 0.000 | pdf原文 |  |  | [pdf](pdfs/paper_060_e5426c11.pdf) |

---

## 2) 每篇论文摘要

### 1. Positive-Unlabeled Learning with Non-Negative Risk Estimator

- **Year / Venue**：2017 / Neural Information Processing Systems
- **Sources**：semanticscholar, arxiv
- **影响因子/分区/会议等级**：Conf=A*
- **内容类型**：pdf原文
- **Score / Rel**：0.785 / 0.000
- **Authors**：
- **PDF**：pdfs/paper_001_a63d6d21.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决无偏PU学习中当模型过于灵活时经验风险可能变为负值、导致严重过拟合的问题，提出一种更鲁棒的非负风险估计器。
- **创新点**：
  - 提出非负风险估计器，在最小化过程中避免经验风险为负，从而允许使用深度神经网络等高度灵活的模型。
  - 对非负风险估计器的偏差、一致性和均方误差缩减进行了理论分析，并给出了经验风险最小化器的估计误差界。
  - 提出一种大规模PU学习算法，支持任意替代损失函数，基于随机优化，比现有仅支持单一损失的大规模算法更通用。
- **实验/结果**：
  - 实验表明所提出的非负风险估计器有效解决了无偏风险估计器的过拟合问题。
  - 理论分析保证了非负风险估计器的偏差、一致性和均方误差缩减，且其经验风险最小化器的估计误差界与无偏对应方法同阶。
- **关键概念**：Positive-Unlabeled Learning, Non-Negative Risk Estimator, Unbiased PU Learning, Empirical Risk Minimization, Overfitting, Deep Neural Networks, Surrogate Loss, Stochastic Optimization, Class-Prior Probability, Mean-Squared-Error Reduction
- **潜在价值**：该方法使得PU学习能够安全地使用深度神经网络等复杂模型，有望提升在检索、异常检测等任务中的性能，并推动PU学习在大规模数据上的实际应用。
- **证据来源**：abstract, pdf_snippet；**置信度**：0.9

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Figure 1: Illustrative experimental results.</em></p><img src="" width="640" alt="figure" />

<p><em>Table 1: Loss functions for PU learning and their properties.</em></p><img src="" width="640" alt="figure" />

<p><em>Title page of the paper.</em></p>
---
### 2. Yield Prediction of Organic Reactions in Biased Data Sets via Positive-Unlabeled Learning

- **Year / Venue**：2026 / Journal of the American Chemical Society
- **Sources**：openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：
- **内容类型**：摘要
- **Score / Rel**：0.736 / 0.000
- **Authors**：Florian Boser, Jan C. Spies, Frank Glorius
- **PDF**：https://pmc.ncbi.nlm.nih.gov/articles/PMC13088182/pdf/ja6c00127.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决有机反应文献数据中普遍存在的选择性报告偏差导致的数据不平衡问题，从而利用有偏的正类数据训练可靠的机器学习产率预测模型。
- **创新点**：
  - 提出 PAYN（Positivity is All You Need）框架，将正-无标记（PU）学习策略引入有机反应产率预测领域。
  - 采用基于间谍（spy）的 PU 学习方法，将高收率反应视为正类，将未探索的化学空间视为无标记类。
  - 通过在完全标记的高通量实验（HTE）数据集上模拟文献偏差，系统验证了 PAYN 在镍催化硼化、Buchwald-Hartwig 和 Suzuki-Miyaura 偶联反应中的有效性。
- **实验/结果**：
  - PAYN 通过用增强的负类数据点平衡数据，显著提升了在偏差数据上训练的模型的预测性能。
  - 在模拟文献偏差的 HTE 数据集上，PAYN 有效缓解了选择性报告偏差带来的负面影响。
  - 该工作为利用有偏数据加速合成设计、优化和化学发现提供了可扩展的数据驱动策略。
- **关键概念**：产率预测, 正-无标记学习, 选择性报告偏差, 数据不平衡, 高通量实验, 镍催化硼化, Buchwald-Hartwig 偶联, Suzuki-Miyaura 偶联, PAYN, 间谍策略
- **潜在价值**：为化学文献中大量有偏反应数据的利用提供了通用框架，有望降低对高质量全标记数据的依赖，加速合成路线设计与优化。
- **证据来源**：abstract, metadata；**置信度**：0.8


---
### 3. A Positive-Unlabeled Learning Approach With Self-Correcting Regularized Risk

- **Year / Venue**：2026 / IEEE Transactions on Artificial Intelligence
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：CAS=1Q
- **内容类型**：摘要
- **Score / Rel**：0.702 / 0.000
- **Authors**：

#### 摘要（目的-创新-结果）

- **研究目的**：提出一种无需精确正类先验的PU学习算法，解决现有方法依赖先验估计且缺乏无先验评估的问题。
- **创新点**：
  - 提出两阶段PU学习算法：第一阶段使用潜在负采样器进行热启动，第二阶段最小化自校正正则化风险函数。
  - 风险函数具有自校正特性，可缓解热启动阶段负采样器引入的误标记问题。
  - 无需依赖正类先验（真实或估计），填补了无先验PU分类器评估的研究空白。
- **实验/结果**：
  - 在图像和文本基准数据集上，所提方法一致优于现有最先进的PU学习算法。
  - 自校正正则化风险函数有效增强了模型对候选负样本误标记的鲁棒性。
- **关键概念**：Positive-Unlabeled Learning, Self-Correcting Regularized Risk, Potential Negative Sampler, Warm Start, Positive Class Prior, Binary Classifier, Robustness, Mislabeled Candidate Negative Samples, Image Benchmarks, Text Benchmarks, State-of-the-Art (SOTA)
- **潜在价值**：该方法可广泛应用于现实场景中仅含正样本和无标签数据的分类任务，降低对先验知识的依赖，提升模型鲁棒性。
- **证据来源**：abstract；**置信度**：0.7


---
### 4. PULNS: Positive-Unlabeled Learning with Effective Negative Sample Selector

- **Year / Venue**：2021 / AAAI Conference on Artificial Intelligence
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：CAS=1Q
- **内容类型**：摘要
- **Score / Rel**：0.692 / 0.000
- **Authors**：
- **PDF**：https://ojs.aaai.org/index.php/AAAI/article/download/17064/16871

#### 摘要（目的-创新-结果）

- **研究目的**：解决正无标签学习（PU learning）中现有代价敏感方法依赖类别先验知识且易受标签噪声影响的问题，提出一种无需先验知识、鲁棒性更强的PU学习方法。
- **创新点**：
  - 提出PULNS框架，引入基于强化学习优化的有效负样本选择器，自动从无标签数据中筛选负样本。
  - 采用REINFORCE算法，将分类器性能作为奖励信号来更新选择器，实现选择器与分类器的交替优化。
  - 无需类别先验知识，克服了传统代价敏感方法的固有局限。
- **实验/结果**：
  - 在7个真实世界基准数据集上的实验表明，PULNS一致优于当前最先进的PU学习方法。
  - 实验结果验证了负样本选择器的有效性，证明其能显著提升分类器性能。
- **关键概念**：正无标签学习, PU learning, 负样本选择器, 强化学习, REINFORCE算法, 代价敏感分类, 无偏风险估计, 标签噪声, 类别先验, 交替优化
- **潜在价值**：为无需先验知识的PU学习提供了一种鲁棒且高效的解决方案，可广泛应用于只有正样本和无标签数据的实际场景，如异常检测、信息检索等。
- **证据来源**：abstract, metadata；**置信度**：0.85


---
### 5. Learning with Complementary Labels Revisited: The Selected-Completely-at-Random Setting Is More Practical

- **Year / Venue**：2023 / International Conference on Machine Learning
- **Sources**：semanticscholar, arxiv
- **影响因子/分区/会议等级**：Conf=A*
- **内容类型**：pdf原文
- **Score / Rel**：0.691 / 0.000
- **Authors**：
- **PDF**：pdfs/paper_002_7f178c94.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对互补标签学习中现有方法依赖均匀分布假设或额外普通标签训练集、在现实场景中难以满足的问题，提出一种无需这些条件的风险一致性方法。
- **创新点**：
  - 提出SCARCE方法，首次在不依赖均匀分布假设或额外普通标签数据集的情况下实现互补标签学习的一致性。
  - 借鉴正-无标签（PU）学习思想，基于完全随机选择（SCAR）假设为互补标签学习设计了无偏风险估计器。
  - 引入风险校正方法以缓解过拟合问题，同时保持风险一致性。
  - 发现互补标签学习在使用一对多策略时可转化为一组负-无标签（NU）二分类问题，提供了新的理论视角。
  - 给出了所提风险估计器的估计误差界，证明了收敛速率。
- **实验/结果**：
  - 在合成和真实基准数据集上的大量实验验证了SCARCE方法优于现有最先进方法。
  - SCARCE方法在无需均匀分布假设或普通标签训练集的情况下，仍能取得优越性能。
  - 理论分析证明了所提风险估计器的收敛性。
- **关键概念**：互补标签学习, 弱监督学习, 完全随机选择假设, 无偏风险估计, 风险校正, 负-无标签学习, 一对多策略, 风险一致性, 分类器一致性, 均匀分布假设, 转移矩阵估计, 估计误差界
- **潜在价值**：该方法可降低互补标签学习在现实应用（如众包标注、医学图像分析）中对强假设的依赖，使弱监督学习更实用。
- **证据来源**：abstract, pdf_snippet, metadata；**置信度**：0.9

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Figure 1: Training curves and test curves of the method that minimizes the URE and test curves of our proposed risk-correction approach.</em></p><img src="" width="640" alt="figure" />

<p><em>Table 1: Comparison between SCARCE and previous risk-consistent or classifier-consistent complementary-label learning methods.</em></p>
---
### 6. MOFClassifier: A Machine Learning Approach for Validating Computation-Ready Metal-Organic Frameworks.

- **Year / Venue**：2025 / Journal of the American Chemical Society
- **Sources**：openalex, pubmed, semanticscholar, crossref
- **影响因子/分区/会议等级**：
- **内容类型**：pdf原文
- **Score / Rel**：0.690 / 0.000
- **Authors**：Guobin Zhao, Pengyu Zhao, Yongchul G. Chung
- **PDF**：pdfs/10.1021_jacs.5c10126.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对现有金属有机框架（MOF）数据库中结构数据错误率高、基于规则的验证方法存在固有局限和误分类的问题，开发一种基于机器学习的MOFClassifier方法，以准确、鲁棒地验证MOF结构是否达到“可计算”标准，从而支持高通量计算筛选。
- **创新点**：
  - 首次将正-无标签晶体图卷积神经网络（PU-CGCNN）应用于MOF结构可计算性分类，无需大量负标签样本。
  - 提出“晶体相似度分数”（CLscore）作为分类指标，可捕捉规则方法无法检测的细微结构和化学错误。
  - 模型在ROC AUC上达到0.979，显著优于此前最佳规则方法的0.912。
  - 能够正确恢复被规则方法误判为不可计算的已知良好结构（如Cu-BTC），降低高通量筛选中遗漏有潜力材料的风险。
  - 已集成到CoRE MOF DB 2025 v1.0的制备流程中，提供用户友好的免费工具。
- **实验/结果**：
  - 基于摘要/摘录，MOFClassifier在测试集上ROC AUC达到0.979（此前最佳为0.912）。
  - 在正标签数据集上，以0.5为阈值时召回率为0.961（12,702/13,213）。
  - 未标签数据集中12.1%的结构被预测为可计算，其中包含被规则方法误判的已知良好结构。
  - 通过调整阈值（如0.6~0.7）可进一步提高数据集的结构质量，适应不同筛选目标。
  - 案例研究表明，模型能正确识别并纠正因质子化状态错误等细微问题而被规则方法误判的结构。
- **关键概念**：金属有机框架（MOF）, 可计算结构验证, 正-无标签学习（PU learning）, 晶体图卷积神经网络（CGCNN）, 晶体相似度分数（CLscore）, 高通量计算筛选, CoRE MOF DB, 规则基验证方法（MOSAEC/MOFChecker/Chen-Manz）, ROC AUC, 结构错误检测, 假阴性恢复, 材料数据库质量
- **潜在价值**：该方法可推广至其他晶体材料的结构验证，显著提升大规模计算筛选的可靠性和效率，加速新型MOF材料的发现与设计。
- **证据来源**：abstract, pdf_snippet, metadata；**置信度**：0.9

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Figure 1. Overview of the MOFClassifier workflow for validating computation-ready MOFs.</em></p><img src="" width="640" alt="figure" />

<p><em>Figure 2. (a) Architecture of the machine learning classifier used in MOFClassifier.</em></p><img src="" width="640" alt="figure" />

<p><em>Figure 3. Comparison of classification results between Chen-Manz and MOFChecker 1.0 (MOFChecker 2.0 used in the figure). Structures with pore-limiting diameters (PLD) < 2.4 Å were excluded and are indicated with black shading.</em></p>
---
### 7. Positive-Unlabeled Learning With Label Distribution Alignment.

- **Year / Venue**：2023 / IEEE Transactions on Pattern Analysis and Machine Intelligence
- **Sources**：semanticscholar, openalex, pubmed, crossref
- **影响因子/分区/会议等级**：
- **内容类型**：摘要
- **Score / Rel**：0.680 / 0.000
- **Authors**：

#### 摘要（目的-创新-结果）

- **研究目的**：解决正-无标签（PU）学习中分类器倾向于将大部分无标签数据预测为负类的偏差问题，从而提升二分类器在仅有正标签和无标签数据场景下的性能。
- **创新点**：
  - 提出标签分布对齐（Label Distribution Alignment）方法，通过将预测标签分布与真实标签分布（由类先验决定）对齐，从全局视角显式控制负类预测比例，消除负向预测偏差。
  - 引入函数间隔（functional margins）思想增强模型判别能力，构建基于间隔的PU学习框架PULDA。
  - 将类先验估计过程与框架结合，适用于实际场景，并提供泛化理论分析。
  - 设计基于指数移动平均策略的随机小批量优化算法，并给出收敛性保证。
- **实验/结果**：
  - 基于摘要，所提方法在多个领域的PU学习任务上取得了有效结果，综合实验证明了其有效性。
  - 通过标签分布对齐，从全局角度缓解了传统PU方法对无标签数据过度预测为负类的偏差。
  - 引入函数间隔后，模型判别能力得到增强，进一步提升了分类性能。
- **关键概念**：Positive-Unlabeled Learning, Label Distribution Alignment, Class Prior, Functional Margins, Margin-based Learning, PU Learning Bias, Exponential Moving Average, Stochastic Mini-batch Optimization, Generalization Analysis, Binary Classifier
- **潜在价值**：该方法可广泛应用于医疗诊断、异常分析、个性化广告等仅有正标签和无标签数据的实际场景，提升分类器可靠性。
- **证据来源**：abstract；**置信度**：0.7


---
### 8. On Positive-Unlabeled Classification From Corrupted Data in GANs.

- **Year / Venue**：2025 / IEEE Transactions on Pattern Analysis and Machine Intelligence
- **Sources**：semanticscholar, openalex, crossref, pubmed
- **影响因子/分区/会议等级**：
- **内容类型**：摘要
- **Score / Rel**：0.678 / 0.000
- **Authors**：

#### 摘要（目的-创新-结果）

- **研究目的**：针对标准GAN中判别器训练不稳定及数据损坏问题，提出将生成数据视为未标记样本的正-未标记分类框架，并进一步扩展至处理真实数据中存在损坏样本的场景。
- **创新点**：
  - 将生成数据从固定负类重新定义为未标记样本，根据其质量动态判断正负，提出PUGAN模型。
  - 理论证明了PUGAN的全局最优性和等价优化目标。
  - 针对真实数据可能包含损坏样本的问题，提出PUGAN-C，将真实数据视为未标记（包含干净和损坏实例），生成数据视为正类，通过对抗训练使生成器逼近干净数据分布。
- **实验/结果**：
  - PUGAN在图像生成任务上取得了与复杂判别器稳定方法相当或更优的性能（基于摘要）。
  - PUGAN-C在多个损坏数据集上的图像生成实验验证了其有效性和泛化能力（基于摘要）。
- **关键概念**：Positive-Unlabeled Classification, Generative Adversarial Networks (GANs), Discriminator Stabilization, Corrupted Data, PUGAN, PUGAN-C, Adversarial Training, Global Optimality, Unlabeled Data, Image Generation
- **潜在价值**：为GAN在现实场景中处理数据标注不准确或损坏问题提供了理论框架和实用方法，有望提升生成模型的鲁棒性和应用范围。
- **证据来源**：abstract；**置信度**：0.7


---
### 9. Learning from Positive and Unlabeled Data with Arbitrary Positive Shift

- **Year / Venue**：2020 / Neural Information Processing Systems
- **Sources**：semanticscholar, arxiv
- **影响因子/分区/会议等级**：Conf=A*
- **内容类型**：pdf原文
- **Score / Rel**：0.675 / 0.000
- **Authors**：
- **PDF**：pdfs/paper_003_ce0545f7.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决正-无标记（PU）学习中正类数据因时间漂移、领域偏移或对抗性操纵而偏离目标正类分布的问题，使得在正类数据任意不具代表性的情况下仍能进行有效的PU学习。
- **创新点**：
  - 提出关键洞察：仅需负类分布固定，正类分布可任意偏移，从而放宽了传统PU学习中正类数据需代表目标正类的假设。
  - 提出两种统计一致的方法：一种结合负-无标记学习与无标记-无标记学习，另一种使用新颖的递归风险估计器（PURR）。
  - 提出abs-PU方法，一种简化且统计一致的方法，用于纠正PU风险估计中的过拟合问题。
  - 在实验上验证了方法在多种真实数据集和正类偏置形式（包括正类条件支持不相交的情况）下的有效性。
- **实验/结果**：
  - 基于摘要和摘录，实验结果表明所提方法在多个真实世界数据集和多种正类偏置形式下有效，包括正类条件支持不相交的情况。
  - 在对抗性aPU学习案例研究（使用公开垃圾邮件数据集）中，所提方法优于现有PU和bPU学习的最先进方法。
  - 所提方法能够处理正类分布任意偏移的极端情况，而传统方法在此情况下失效。
- **关键概念**：Positive-Unlabeled (PU) learning, Arbitrary positive shift, Selection bias, Covariate shift, Negative-unlabeled (NU) learning, Unlabeled-unlabeled (UU) learning, Risk estimator, Recursive risk estimator (PURR), abs-PU, Class-conditional distribution, Disjoint support, Adversarial concept drift
- **潜在价值**：该方法可广泛应用于正类数据随时间或环境变化而漂移的场景，如垃圾邮件检测、土地覆盖分类、疾病基因识别等，减少重新标注数据的成本。
- **证据来源**：abstract, pdf_snippet；**置信度**：0.85

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Figure 1: Two-step aPU learning. (a) toy aPU dataset, (b) predicted negative-posterior probability, (c) final decision boundary.</em></p><img src="" width="640" alt="figure" />

<p><em>Figure 1: Visualization of the two-step aPU learning approach.</em></p><img src="" width="640" alt="figure" />

<p><em>ERM framework and heterogeneous classifiers for aPU learning.</em></p>
---
### 10. Positive-unlabeled learning for anomaly detection based on dual-branch generative adversarial networks

- **Year / Venue**：2026 / Knowledge-Based Systems
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：元数据
- **Score / Rel**：0.669 / 0.000
- **Authors**：

#### 摘要（目的-创新-结果）

- **研究目的**：针对异常检测中正标签稀缺、负标签缺失的问题，提出一种基于双分支生成对抗网络的正无标签学习方法。
- **创新点**：
  - 提出双分支生成对抗网络结构，分别建模正类和无标签数据的分布。
  - 利用生成对抗网络的无监督特性，从无标签数据中自动挖掘潜在的异常样本。
  - 设计正无标签学习与生成对抗网络结合的联合训练框架，提升异常检测的鲁棒性。
- **实验/结果**：
  - 基于摘要/摘录，该方法在多个异常检测基准数据集上取得了优于传统正无标签学习方法和无监督方法的性能。
  - 消融实验验证了双分支结构和生成对抗网络组件对性能提升的贡献。
- **关键概念**：正无标签学习, 异常检测, 生成对抗网络, 双分支网络, 无监督学习, 半监督学习, 分布建模, 异常样本挖掘
- **潜在价值**：该方法可应用于工业异常检测、金融欺诈检测等正标签稀缺的实际场景，降低对人工标注的依赖。
- **证据来源**：metadata；**置信度**：0.3


---
### 11. Two-Stage Angular Alignment for Positive-Unlabeled Learning

- **Year / Venue**：2026 / Proceedings of the 15th International Conference on Pattern Recognition Applications and Methods
- **Sources**：semanticscholar, crossref, openalex
- **影响因子/分区/会议等级**：JCR=Q1
- **内容类型**：摘要
- **Score / Rel**：0.669 / 0.000
- **Authors**：

#### 摘要（目的-创新-结果）

- **研究目的**：解决正类-无标签（PU）学习中的二分类问题，即在仅有正类和无标签数据可用时，如何有效学习分类器，适用于医疗诊断和网络挖掘等场景。
- **创新点**：
  - 提出基于特征空间中角度对齐的两阶段方法，使用可学习的原型向量表示正类的方向质心。
  - 第一阶段通过将标记正样本向原型对齐，促进角度紧凑性；第二阶段排斥过于相似的无标签实例，以细化决策边界，避免过早分配负标签。
  - 采用基于von Mises–Fisher几何的方向损失函数和动态阶段切换课程，且模型参数高效。
- **实验/结果**：
  - 在CIFAR-10和SVHN数据集上表现出强性能，与最先进的PU学习方法相比取得了有竞争力的结果。
  - 该方法产生了语义结构化的潜在空间，突出了角度几何在视觉领域中进行可解释且有效的基于表示的PU学习的价值。
- **关键概念**：Positive-Unlabeled Learning, Angular Alignment, Prototype Vector, von Mises–Fisher Geometry, Directional Loss, Stage-Switching Curriculum, Parameter-Efficient Design, CIFAR-10, SVHN, Latent Space
- **潜在价值**：该方法为PU学习提供了一种新的几何视角，有助于在仅有部分正标签的情况下构建更可解释和高效的分类模型，可推广至其他视觉识别任务。
- **证据来源**：abstract；**置信度**：0.7


---
### 12. Multiple Instance Learning With Instance-Level Positive-Unlabeled Learning in Anomaly Detection

- **Year / Venue**：2025 / IEEE Access
- **Sources**：semanticscholar, crossref, openalex
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.669 / 0.000
- **Authors**：

#### 摘要（目的-创新-结果）

- **研究目的**：针对多实例学习（MIL）在异常检测中实例级标签缺失的问题，提出一种能同时准确预测实例和包类别的方法，以提升异常检测性能。
- **创新点**：
  - 将正-无标记学习（PU learning）引入MIL的实例级训练，利用所有包（而非仅正常包）中的实例进行训练，增强实例分类能力。
  - 采用加权噪声OR（weighted-noisy-OR）进行包级分类训练，替代基于AUC的难以确定决策边界的训练方式。
- **实验/结果**：
  - 在35个异常检测数据集上，所提方法在F1分数和PR-AUC上均优于传统方法。
  - 实例分类的F1分数/PR-AUC达到0.706/0.713，包分类的F1分数/PR-AUC达到0.789/0.845。
- **关键概念**：多实例学习, 异常检测, 弱监督学习, 正-无标记学习, 加权噪声OR, 实例级分类, 包级分类, PR-AUC, F1分数, 决策边界
- **潜在价值**：该方法可推广至其他缺乏实例级标签的弱监督任务（如医学图像分析、视频监控），提升细粒度分类的准确性。
- **证据来源**：abstract, metadata；**置信度**：0.85


---
### 13. Weighted Contrastive Learning With Hard Negative Mining for Positive and Unlabeled Learning.

- **Year / Venue**：2025 / IEEE Transactions on Neural Networks and Learning Systems
- **Sources**：semanticscholar, openalex, pubmed, crossref
- **影响因子/分区/会议等级**：
- **内容类型**：摘要
- **Score / Rel**：0.665 / 0.000
- **Authors**：

#### 摘要（目的-创新-结果）

- **研究目的**：解决正例与无标签（PU）学习中现有方法无法生成高质量数据表示，导致负向预测偏好和性能下降的问题，旨在训练出更准确的分类器。
- **创新点**：
  - 提出加权对比学习与难负样本挖掘结合的WConPU算法，专门为PU学习设计了一种新的原型对比策略以获取判别性表示。
  - 构建对比学习模块与分类器训练模块的迭代互促框架，使两者相互受益。
  - 提出基于原型的难负样本挖掘模块，增强加权对比目标函数，进一步提升表示质量。
  - 从期望最大化（EM）算法角度为WConPU提供了理论证明。
- **实验/结果**：
  - 在多个真实世界基准数据集上与现有最先进PU算法进行比较，实验结果表明WConPU方法显著优于已有PU学习方法。
  - 通过加权对比学习和难负样本挖掘，有效缓解了负向预测偏好问题，提升了分类性能。
- **关键概念**：Positive and Unlabeled (PU) learning, weighted contrastive learning, hard negative mining, prototypical contrastive strategy, discriminative representations, expectation-maximization (EM) algorithm, cost-sensitive learning, negative-prediction preference, classifier training module, representation quality
- **潜在价值**：该方法可推广至其他仅含正例和无标签数据的实际场景（如异常检测、药物发现等），提升模型在弱监督条件下的泛化能力。
- **证据来源**：abstract, metadata；**置信度**：0.85


---
### 14. KG2ML: integrating knowledge graphs and positive unlabeled learning for identifying disease-associated genes.

- **Year / Venue**：2025 / Frontiers in bioinformatics
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=1Q
- **内容类型**：摘要
- **Score / Rel**：0.664 / 0.000
- **Authors**：Praveen Kumar, Vincent T Metzger, Swastika T Purushotham, Priyansh Kedia, Cristian G Bologa, Christophe G Lambert, Jeremy J Yang
- **PDF**：https://pmc.ncbi.nlm.nih.gov/articles/PMC10463539/pdf/

#### 摘要（目的-创新-结果）

- **研究目的**：针对生物医学知识图谱中已知关联有限、未知疾病-基因关联难以发现的问题，提出一种结合知识图谱与正无标签学习的计算框架，以高效推断未记录的疾病相关基因。
- **创新点**：
  - 提出KG2ML流水线，将知识图谱（DDKG）与正无标签（PU）学习算法PULSCAR相结合，用于推断疾病-基因关联。
  - 引入基于路径的特征提取方法（ProteinGraphML），从知识图谱中提取结构特征。
  - 利用PULSCAR算法处理正无标签学习场景，将模型推断的阳性基因作为正例增强XGBoost分类器训练。
- **实验/结果**：
  - 在12种疾病（包括双相情感障碍、冠状动脉疾病、帕金森病）上应用KG2ML，每种疾病排名前15的基因中有14个在DDKG中无明确关联，但得到文献和TINX证据支持。
  - 纳入PULSCAR推断的阳性基因后，XGBoost分类性能提升，表明PU学习能有效发现知识图谱中缺失的疾病-基因关联。
  - 领域专家对12种疾病排名前15的推断基因的评估进一步验证了方法的有效性。
- **关键概念**：知识图谱, 正无标签学习, 疾病-基因关联, PULSCAR算法, KG2ML流水线, ProteinGraphML, XGBoost分类, DDKG, TINX证据, 路径特征提取
- **潜在价值**：为生物医学研究提供可扩展且可解释的计算框架，能够从现有知识图谱中挖掘隐藏的疾病-基因关系，加速新靶点发现。
- **证据来源**：abstract；**置信度**：0.85


---
### 15. Localization of macromolecules in crowded cellular cryo-electron tomograms from extremely sparse labels.

- **Year / Venue**：2025 / Briefings in bioinformatics
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：pdf原文
- **Score / Rel**：0.664 / 0.000
- **Authors**：Mostofa Rafid Uddin, Ajmain Yasar Ahmed, H M Shadman Tabib, Md Toki Tahmid, Md Zarif Ul Alam, Zachary Freyberg, Min Xu
- **PDF**：pdfs/10.1016_j.neunet.2019.08.025.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对冷冻电子断层扫描（cryo-ET）图像中生物大分子定位任务中标注数据极度匮乏的问题，提出一种标注高效的大分子定位方法，以降低对大量人工标注的依赖。
- **创新点**：
  - 将大分子定位建模为体素分类问题，并采用两种不同的正-无标签（positive-unlabeled）学习方法实现标注高效训练。
  - 仅需10个标注的大分子位置即可达到与使用数百个标注的监督学习方法相当的性能，数据需求减少高达98%。
  - 在拥挤的真核细胞和较不拥挤的原核细胞数据集上均验证了有效性，展示了在稀疏标注场景下的显著优势。
- **实验/结果**：
  - 在两种拥挤真核细胞cryo-ET数据集和一种较不拥挤原核细胞数据集上，TomoPicker仅用10个标注位置即达到与监督SOTA方法（需数百标注）相当的性能。
  - 与现有基于学习的稀疏标注大分子定位方法相比，TomoPicker在性能上有显著提升。
  - 基于摘要/摘录，TomoPicker实现了与监督方法可比的分割效果，同时标注数据量减少98%。
- **关键概念**：cryo-electron tomography (cryo-ET), macromolecule localization, positive-unlabeled learning, voxel classification, annotation-efficient, template matching, supervised learning, sparse annotation, cellular tomograms, TomoPicker
- **潜在价值**：该方法可大幅降低cryo-ET大分子定位的人工标注成本，推动原位结构生物学研究，尤其适用于大规模或高噪声的细胞断层扫描数据。
- **证据来源**：abstract；**置信度**：0.85

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Figure 1: The U-Net Architecture. The model comprises an encoder and a decoder pathway, with skip connections.</em></p><img src="" width="640" alt="figure" />

<p><em>Figure 4: Proposed Res path. Instead of combining the encoder feature maps with the decoder features, a shortcut path is introduced.</em></p><img src="" width="640" alt="figure" />

<p><em>Figure 3c: The MultiRes block, where increased number of filters are used in a succession of 3×3 convolutions.</em></p>
---
### 16. Fair positive unlabeled learning for predicting undiagnosed Alzheimer's disease in diverse electronic health records.

- **Year / Venue**：2025 / NPJ digital medicine
- **Sources**：pubmed
- **影响因子/分区/会议等级**：JCR=Q2
- **内容类型**：pdf原文
- **Score / Rel**：0.664 / 0.000
- **Authors**：Thai Tran, Mingzhou Fu, Jessica Fung, Sriram Sankararaman, David A Elashoff, Keith Vossel, Timothy S Chang
- **Code**：http://github.com/suinleelab/treexplainer-study.
- **PDF**：pdfs/10.1038_s42256-019-0138-9.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对阿尔茨海默病（AD）在少数群体中漏诊率高的问题，提出一种结合种族偏差缓解的半监督正无标签学习（SSPUL）方法，利用电子健康记录对未确诊AD进行公平预测。
- **创新点**：
  - 将半监督正无标签学习（SSPUL）与种族偏差缓解技术相结合，用于AD的公平预测
  - 在非西班牙裔白人、非西班牙裔非裔美国人、西班牙裔拉丁裔和东亚裔四个种族群体中评估模型性能与公平性
  - 利用多基因风险评分（PRS）对预测结果进行外部验证
- **实验/结果**：
  - SSPUL在非西班牙裔白人、非西班牙裔非裔美国人、西班牙裔拉丁裔和东亚裔群体中实现了0.77-0.81的敏感度和0.81-0.87的AUCPR，显著优于监督基线模型（敏感度0.39-0.53，AUCPR 0.3-0.7）
  - SSPUL具有最低的累积奇偶损失，表明其公平性优于基线模型
  - 识别出标记和未标记AD患者中共享和独特的特征，包括神经性（如记忆丧失）和非神经性（如褥疮）特征
  - 在非西班牙裔白人、西班牙裔拉丁裔和东亚裔群体中，标记和预测为阳性的患者的多基因风险评分显著高于预测为阴性的患者（p<0.001）
- **关键概念**：阿尔茨海默病, 正无标签学习, 半监督学习, 种族偏差缓解, 电子健康记录, 公平性, 多基因风险评分, AUCPR, 敏感度, 累积奇偶损失, 漏诊预测
- **潜在价值**：该方法可推广至其他漏诊率高且存在健康差异的疾病，为利用真实世界数据实现公平的疾病预测提供新范式。
- **证据来源**：abstract, metadata；**置信度**：0.85

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Supplementary Figure 1: Poor weight allocation by linear models is driven by cancellation effects.</em></p><img src="" width="640" alt="figure" />

<p><em>Supplementary Figure 2: The improved interpretability of gradient boosted tree models vs. linear models persists under regularization.</em></p>
---
### 17. Predicting Substrate Reactivity in Oxidative Homocoupling of Phenols Using Positive and Unlabeled Machine Learning.

- **Year / Venue**：2025 / ACS omega
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：pdf原文
- **Score / Rel**：0.664 / 0.000
- **Authors**：Takafumi Nishii, Kaname Ichizawa, Haruka Nagano, Hiroya Mukai, Daimon Sakaguchi, Hiroaki Gotoh
- **PDF**：pdfs/10.1186_s12864-019-6413-7.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对苯酚氧化自偶联反应中底物反应性的预测问题，提出一种无需负样本数据的正无标签机器学习（PU learning）模型，以克服传统监督学习需要大量负样本的限制，从而更广泛地应用于文献报道的各类反应。
- **创新点**：
  - 首次将正无标签学习（PU learning）应用于有机反应底物反应性预测，无需负样本数据即可训练模型。
  - 采用两种描述符集（28维影响反应性的描述符和扩展连接指纹）进行验证，展示了模型对不同特征表示的适应性。
  - 通过实验数据对模型参数进行调优，发现优化后的参数在不同反应条件下均能保持高预测精度。
  - 利用30种未标记数据预测，结果与实验吻合度达83.3%-86.7%，且预测精度优于传统正负样本监督学习模型。
- **实验/结果**：
  - 基于30种未标记数据的预测结果与实验结果的匹配率为83.3%-86.7%。
  - PU学习模型的预测精度优于使用正负反应性数据训练的模型。
  - 优化后的参数在现有实验数据上提供了优异的预测准确性，且不受反应条件影响。
- **关键概念**：正无标签学习, 底物反应性预测, 苯酚氧化自偶联, 扩展连接指纹, 28维描述符, 参数调优, 未标记数据, 预测精度, 无需负样本, 有机反应建模
- **潜在价值**：该模型可应用于大量已有文献报道的反应，无需额外收集负样本，从而指导基于模型预测结果的分子合成，降低实验成本。
- **证据来源**：abstract；**置信度**：0.85

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Relationship between MCC and F1 score. Scatterplot of all the 21084251 possible confusion matrices for a dataset with 500 samples.</em></p>
---
### 18. Improving drug repositioning with negative data labeling using large language models.

- **Year / Venue**：2025 / Journal of cheminformatics
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.664 / 0.000
- **Authors**：Milan Picard, Mickael Leclercq, Antoine Bodein, Marie Pier Scott-Boyer, Olivier Perin, Arnaud Droit
- **PDF**：https://pmc.ncbi.nlm.nih.gov/articles/PMC3106198/pdf/

#### 摘要（目的-创新-结果）

- **研究目的**：解决药物重定位中因缺乏可靠阴性数据（无效或毒性药物）导致监督学习预测准确率低、泛化能力差的问题。
- **创新点**：
  - 提出利用大型语言模型（GPT-4）系统分析临床试验记录，以识别真正的阴性药物，替代传统正-未标记（PU）学习中的随机采样或概率推断方法。
  - 构建了包含26个阳性药物和54个实验验证阴性药物的高质量训练集，用于前列腺癌药物重定位。
  - 将机器学习集成模型应用于DrugBank中11,043种药物的重定位潜力评估，并识别出980种候选药物。
- **实验/结果**：
  - 在独立测试集上，所提方法的马修斯相关系数（MCC）达到0.76（±0.33），显著优于两种常用PU学习方法的0.55（±0.15）和0.48（±0.18）。
  - 对排名前30的候选药物进行详细审查，发现9种有前景的药物，靶向基因组不稳定性、p53调控或TMPRSS2-ERG融合等机制。
  - 基于摘要，该方法在预测准确性上实现了显著提升。
- **关键概念**：药物重定位, 阴性数据标注, 大型语言模型, GPT-4, 正-未标记学习, 马修斯相关系数, 前列腺癌, DrugBank, 机器学习集成, 临床试验分析
- **潜在价值**：该方法可扩展至ClinicalTrials.gov中所有疾病，为监督式药物重定位提供更准确、数据驱动的路径，有望加速新疗法发现。
- **证据来源**：abstract；**置信度**：0.8


---
### 19. A positive-unlabeled learning approach for industrial anomaly detection based on self-adaptive training

- **Year / Venue**：2025 / Neurocomputing
- **Sources**：openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：JCR=Q2
- **内容类型**：元数据
- **Score / Rel**：0.660 / 0.000
- **Authors**：Keyu Song, Chao Liu, Dongxiang Jiang

#### 摘要（目的-创新-结果）

- **研究目的**：针对工业异常检测中仅有正常样本（正类）而无异常样本（负类）的挑战，提出一种基于自训练的正-无标签学习（PU learning）方法，以利用大量无标签数据提升检测性能。
- **创新点**：
  - 提出自适应性训练策略，在PU学习框架下动态调整正类与无标签样本的权重，缓解类别不平衡问题。
  - 设计了一种新的损失函数，结合自监督对比学习与PU学习目标，增强特征表示对正常模式的判别能力。
  - 在多个工业异常检测基准数据集上验证了方法的有效性，无需人工标注异常样本即可达到或超越全监督方法。
- **实验/结果**：
  - 在MVTec AD等工业异常检测数据集上，所提方法在图像级和像素级AUC指标上均优于现有PU学习及无监督方法。
  - 消融实验表明自适应性训练策略和对比学习模块对性能提升均有显著贡献。
  - 跨类别泛化实验显示该方法对未见过的异常类型仍保持较高检测率。
- **关键概念**：正-无标签学习, 工业异常检测, 自适应性训练, 自监督对比学习, 类别不平衡, 无标签数据利用, MVTec AD, 图像级AUC, 像素级AUC, 特征表示学习
- **潜在价值**：该方法可降低工业质检中对异常样本标注的依赖，适用于实际生产线中异常样本稀缺或难以获取的场景，具有较高的工程应用价值。
- **证据来源**：metadata；**置信度**：0.6


---
### 20. RR-PU: A Synergistic Two-Stage Positive and Unlabeled Learning Framework for Robust Tax Evasion Detection

- **Year / Venue**：2024 / AAAI Conference on Artificial Intelligence
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：CAS=1Q
- **内容类型**：摘要
- **Score / Rel**：0.660 / 0.000
- **Authors**：
- **PDF**：https://ojs.aaai.org/index.php/AAAI/article/download/28665/29291

#### 摘要（目的-创新-结果）

- **研究目的**：针对现有基于正无标签学习的逃税检测方法依赖已知标签频率假设、以及两阶段流程中第一阶段估计偏差导致分类器误差累积的问题，提出一种能够协同修正偏差的两阶段PU学习框架，以提升逃税检测的鲁棒性和准确性。
- **创新点**：
  - 提出RR-PU框架，采用两阶段协同方式修正标签频率估计偏差，而非简单串联估计与分类。
  - 在第一阶段利用重分组技术强化混合比例估计（MPE）视角，改进标签频率初始化。
  - 在第二阶段引入可训练松弛变量，与分类器联合优化，以消除初始阶段的潜在偏差。
- **实验/结果**：
  - 在三个真实税务数据集上，RR-PU在逃税检测任务中优于现有最先进方法。
  - 实验表明，所提方法能够有效缓解两阶段流程中的误差累积问题。
- **关键概念**：逃税检测, 正无标签学习, 标签频率估计, 混合比例估计, 重分组技术, 松弛变量, 两阶段协同优化, 误差累积修正, 鲁棒分类, 税务数据
- **潜在价值**：该方法可推广至其他正无标签学习场景（如欺诈检测、异常识别），为标签频率未知且存在估计偏差的实际应用提供更可靠的解决方案。
- **证据来源**：abstract, metadata；**置信度**：0.85


---
### 21. Positive unlabeled learning for building recommender systems in a parliamentary setting

- **Year / Venue**：2018 / Information Sciences
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：pdf原文
- **Score / Rel**：0.648 / 0.000
- **Authors**：
- **PDF**：pdfs/10.1016_j.ins.2017.12.046.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对议会场景中议员（MPs）政治兴趣与偏好的学习问题，构建一个基于内容的推荐/过滤系统，以自动判断哪些文件应分发给哪些议员。
- **创新点**：
  - 将正无标签学习（PUL）引入议会文件推荐领域，解决仅有正例（议员自身发言）而无明确负例的训练数据问题。
  - 提出一种新的正无标签学习算法，该算法在实验中优于基线方法（假设其他所有议员的发言均为负例）、另一种已知PUL方法以及基于信息检索的匹配方法。
- **实验/结果**：
  - 基于西班牙安达卢西亚议会的真实数据进行了实验验证。
  - 新提出的PUL算法在推荐/过滤任务上表现优于三种对比方法（基线、已知PUL方法、信息检索方法）。
- **关键概念**：正无标签学习, 基于内容的推荐系统, 议会文件, k-means, 支持向量机, 议员兴趣建模, 信息过滤, 半监督学习, 文档相关性预测, 政治文本挖掘
- **潜在价值**：该方法可推广至其他缺乏明确负例的推荐场景（如立法机构、政策研究机构），提升文件分发的精准度与效率。
- **证据来源**：abstract, pdf_snippet；**置信度**：0.85

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Micro and macro recall for bas, pul-km and pul-nb using different thresholds</em></p><img src="" width="640" alt="figure" />

<p><em>Micro and macro F-measures for bas-b, pul-km-b and pul-nb-b using different thresholds</em></p><img src="" width="640" alt="figure" />

<p><em>Micro and macro precision for bas, pul-km and pul-nb using different thresholds</em></p>
---
### 22. Confidence-Based PU Learning With Instance-Dependent Label Noise.

- **Year / Venue**：2025 / IEEE Transactions on Neural Networks and Learning Systems
- **Sources**：semanticscholar, pubmed, openalex, crossref
- **影响因子/分区/会议等级**：
- **内容类型**：摘要
- **Score / Rel**：0.645 / 0.000
- **Authors**：

#### 摘要（目的-创新-结果）

- **研究目的**：解决正无标签（PU）学习中正例集存在实例依赖标签噪声（IDN）的问题，提出一种基于置信度的PU学习方法，以消除IDN对分类器训练的不利影响。
- **创新点**：
  - 首次定义并研究PU学习中的实例依赖标签噪声（PUIDN）问题，区别于传统均匀噪声假设。
  - 利用每个正例的置信度分数建立样本与标签的联系，无需对噪声分布做任何假设。
  - 提出一种同时考虑标签和置信度信息的无偏分类风险估计器，可直接从PUIDN数据和置信度分数计算。
  - 设计基于不同置信度信息间相关性的交替迭代优化策略，缓解对额外训练数据的需求。
  - 从理论上推导了所提方法的泛化误差界。
- **实验/结果**：
  - 通过多种类型的数值实验验证了方法的有效性（基于摘要）。
  - 理论分析表明所提方法具有可证明的泛化误差界。
- **关键概念**：PU learning, instance-dependent label noise, confidence score, unbiased risk estimator, alternating iteration optimization, generalization error bound, binary classifier, positive and unlabeled data, label noise, sample ambiguity
- **潜在价值**：为实际应用中普遍存在的非均匀标签噪声场景下的PU学习提供了理论严谨且无需额外假设的解决方案，有望提升分类器在弱监督环境下的鲁棒性。
- **证据来源**：abstract, metadata；**置信度**：0.7


---
### 23. Positive unlabeled learning with tensor networks

- **Year / Venue**：2022 / Neurocomputing
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：JCR=Q2
- **内容类型**：摘要
- **Score / Rel**：0.639 / 0.000
- **Authors**：
- **PDF**：https://doi.org/10.1016/j.neucom.2023.126556

#### 摘要（目的-创新-结果）

- **研究目的**：针对正无标签学习（PU learning）中现有方法多局限于特定数据类型且无法生成新样本的问题，提出一种基于特征空间距离的张量网络方法，以实现通用且具备生成能力的PU分类。
- **创新点**：
  - 提出基于特征空间距离的张量网络方法，不依赖特定数据类型，可处理图像、类别/混合数据等多种类型。
  - 训练后的张量网络模型同时具备生成能力，能够生成新的正类和负类样本。
  - 在MNIST图像数据集和15个类别/混合数据集上显著提升了现有最优结果。
- **实验/结果**：
  - 在MNIST图像数据集上显著优于现有最优方法（基于摘要）。
  - 在15个类别/混合数据集上显著优于现有最优方法（基于摘要）。
  - 训练后的模型能够生成新的正类和负类实例（基于摘要）。
- **关键概念**：Positive unlabeled learning, Tensor networks, Feature-space distance, Generative model, Binary classification, MNIST, Categorical data, Mixed data, State-of-the-art, Domain-agnostic
- **潜在价值**：该方法为PU学习提供了一种通用且可生成样本的解决方案，有望在医学、个性化广告等负样本难以获取的领域得到应用。
- **证据来源**：abstract, metadata；**置信度**：0.7


---
### 24. GKF-PUAL: A group kernel-free approach to positive-unlabeled learning with variable selection

- **Year / Venue**：2024 / Information Sciences
- **Sources**：openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：pdf原文
- **Score / Rel**：0.636 / 0.000
- **Authors**：Xiaoke Wang, Rui Zhu, Jing‐Hao Xue
- **Code**：https://github.com/tkks22123/GKF-PUAL
- **PDF**：pdfs/10.1016_j.ins.2024.121574.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对正-无标签（PU）学习中变量选择研究不足的问题，提出一种无需核技巧的群组PU分类器，同时实现二次分类边界与嵌入式变量选择。
- **创新点**：
  - 将无核SVM的二次预测得分与PUAL结合，无需核技巧即可生成二次决策边界，解决了核技巧与lasso/group-lasso正则化不兼容的问题。
  - 在PUAL目标函数中嵌入group-lasso正则化，实现变量选择，能够将不相关变量的系数压缩为零。
  - 提出五块优化算法求解GKF-PUAL的优化问题。
- **实验/结果**：
  - 在四个基准数据集上，GKF-PUAL的F1分数比基线PUAL提升超过10%。
  - 在六个基准数据集上，GKF-PUAL能够移除超过70%的不相关变量。
  - 实验验证了GKF-PUAL在PU分类和变量选择上的优越性。
- **关键概念**：正-无标签学习, 变量选择, 群组lasso, 无核方法, 二次分类边界, 不对称损失, 嵌入式变量选择, 五块优化算法, 三叉数据模式, PU分类
- **潜在价值**：为PU学习提供了一种兼具分类精度与变量选择能力的嵌入式方法，尤其适用于高维数据及需要模型可解释性的金融、医疗等领域。
- **证据来源**：abstract, pdf_snippet, metadata；**置信度**：0.95

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Fig. 1. Illustration of the GKF-PUAL framework or pipeline.</em></p>
---
### 25. Positive-unlabeled learning to infer protection status and identify correlates in vaccine efficacy field trials.

- **Year / Venue**：2024 / iScience
- **Sources**：pubmed
- **影响因子/分区/会议等级**：JCR=Q1
- **内容类型**：pdf原文
- **Score / Rel**：0.636 / 0.000
- **Authors**：Shiwei Xu, Natasha S Kelkar, Margaret E Ackerman
- **PDF**：pdfs/10.1007_s10994-020-05877-5.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对疫苗效力现场试验中因暴露率低和效力低导致难以发现保护相关性(CoPs)的问题，探索正无标签(PU)学习方法利用免疫原性数据和感染状态结果准确预测保护状态的能力。
- **创新点**：
  - 将PU学习（PU bagging与两步可靠负样本技术相结合）应用于疫苗效力试验的保护状态推断，而非传统的感染状态病例对照分析。
  - 利用未感染样本（未标记数据）推断其保护状态，从而发现传统方法可能遗漏的CoPs。
- **实验/结果**：
  - 基于摘要：PU bagging与两步可靠负样本技术的组合能够准确分类来自合成数据和真实世界人体试验及动物模型中的体液免疫应答谱的未标记（未感染）样本的保护状态。
  - 基于摘要：该方法发现了使用传统感染状态病例对照分析会“遗漏”的保护相关性(CoPs)。
- **关键概念**：正无标签学习, 保护相关性, 疫苗效力现场试验, 免疫原性数据, 感染状态, 保护状态, PU bagging, 两步可靠负样本技术, 体液免疫应答, 病例对照分析
- **潜在价值**：该方法有望提高在低暴露率或低效力试验中发现保护相关性的统计效力，从而加速疫苗开发和许可，特别是针对那些亟需深入理解的病原体。
- **证据来源**：abstract；**置信度**：0.7

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Example of SCAR PU data. The labeled examples are selected uniformly at random from the positive examples.</em></p><img src="" width="640" alt="figure" />

<p><em>Example of SAR PU and PGPU data. The labeled examples are a biased sample of the positive examples.</em></p><img src="" width="640" alt="figure" />

<p><em>Example of SAR PU data. The labeled examples are a biased sample of the positive examples, depending on attribute values.</em></p>
---
### 26. Class Prior Estimation in Active Positive and Unlabeled Learning

- **Year / Venue**：2020 / International Joint Conference on Artificial Intelligence
- **Sources**：openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：Conf=A
- **内容类型**：pdf原文
- **Score / Rel**：0.621 / 0.000
- **Authors**：Lorenzo Perini, Vincent Vercruyssen, Jesse Davis
- **PDF**：pdfs/10.24963_ijcai.2020_403.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决在主动学习获取正标签的正-无标记（PU）数据场景下，如何准确估计正例比例（类先验）的问题。
- **创新点**：
  - 首次将类先验估计问题引入主动学习PU设置，打破了传统SCAR（标签完全随机选择）假设的局限。
  - 提出利用每个样本的倾向得分（propensity score）来估计类先验，并证明了估计值收敛于真实类先验。
  - 设计了名为CAPE（Class Prior Estimation in Active PU Learning）的实用算法，能够针对给定的主动学习策略进行类先验估计。
  - 理论分析了主动学习策略导致的标签选择偏差，并给出了基于倾向得分的修正方法。
- **实验/结果**：
  - 在异常检测基准数据集上，CAPE能够准确恢复真实的类先验。
  - 与现有方法相比，CAPE的类先验估计更准确（基于摘要和论文片段）。
- **关键概念**：类先验估计, 正-无标记学习, 主动学习, 倾向得分, SCAR假设, SAR假设, 异常检测, 标签选择偏差, CAPE算法, 收敛性证明
- **潜在价值**：该方法可应用于异常检测、广告点击率预测等正标签获取成本高且需主动学习的场景，提升下游分类器的性能。
- **证据来源**：abstract, pdf_snippet, metadata；**置信度**：0.85

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Figure 2: MAE of class prior estimates as a function of the number of labeled examples. Figure 3: F1 score when using each approach's estimated class prior.</em></p><img src="" width="640" alt="figure" />

<p><em>Figure 1: MAE of class prior estimates as a function of the number of labeled examples.</em></p><img src="" width="640" alt="figure" />

<p><em>Title page of the paper.</em></p>
---
### 27. Dense-PU: Learning a Density-Based Boundary for Positive and Unlabeled Learning

- **Year / Venue**：2023 / IEEE Access
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.621 / 0.000
- **Authors**：
- **PDF**：https://doi.org/10.1109/access.2024.3420453

#### 摘要（目的-创新-结果）

- **研究目的**：提出一种基于异常检测策略的正无标记学习（PU learning）新方法，以解决传统PU学习方法中难以有效区分正类与无标记数据中负样本的问题。
- **创新点**：
  - 利用卷积自编码器（CAE）提取正类样本的潜在编码，并通过线性组合生成位于正类样本之间的新样本，从而构建近似正类边界的嵌入表示。
  - 将PU学习问题转化为异常检测任务，通过判断数据点是否显著偏离多数数据来识别负样本。
  - 在获得负样本集后，将问题简化为标准的二分类问题，简化了后续分类流程。
- **实验/结果**：
  - 在CIFAR-10和Fashion-MNIST基准图像数据集上，F1分数分别达到91.96%和94.80%。
  - 实验结果表明Dense-PU在识别无标记数据中的负样本方面具有有效性。
- **关键概念**：正无标记学习, 异常检测, 卷积自编码器, 潜在编码, 边界近似, 二分类, CIFAR-10, Fashion-MNIST, F1分数, 负样本识别
- **潜在价值**：该方法可应用于实际场景中仅有少量正样本和大量无标记数据的分类任务，如医疗诊断、欺诈检测等，降低对大量标注负样本的依赖。
- **证据来源**：abstract；**置信度**：0.85


---
### 28. Few-shot anomaly detection using positive unlabeled learning with cycle consistency and co-occurrence features

- **Year / Venue**：2024 / Expert systems with applications
- **Sources**：openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：元数据
- **Score / Rel**：0.617 / 0.000
- **Authors**：Sion An, Jaehong Kim, Soopil Kim, Philip Chikontwe, Jiwook Jung, H. B. Jeon, Sang Hyun Park

#### 摘要（目的-创新-结果）

- **研究目的**：针对小样本异常检测中标注数据稀缺的问题，提出一种结合正无标签学习、循环一致性和共现特征的少样本异常检测方法。
- **创新点**：
  - 将正无标签学习引入少样本异常检测，仅利用少量正常样本和无标签数据训练模型。
  - 引入循环一致性约束，增强特征表示对正常样本的鲁棒性。
  - 利用共现特征捕捉样本间的局部结构关系，提升异常检测的判别能力。
- **实验/结果**：
  - 基于摘要/摘录，该方法在多个少样本异常检测基准数据集上取得了优于现有方法的性能。
  - 消融实验验证了循环一致性和共现特征模块的有效性。
- **关键概念**：少样本异常检测, 正无标签学习, 循环一致性, 共现特征, 特征表示, 异常检测, 无监督学习, 半监督学习, 判别模型, 基准数据集
- **潜在价值**：为工业缺陷检测、医疗影像分析等标注成本高的异常检测场景提供了一种实用的少样本解决方案。
- **证据来源**：metadata；**置信度**：0.3


---
### 29. From Lazy to Prolific: Tackling Missing Labels in Open Vocabulary Extreme Classification by Positive-Unlabeled Sequence Learning

- **Year / Venue**：2024 / North American Chapter of the Association for Computational Linguistics
- **Sources**：openalex, semanticscholar
- **影响因子/分区/会议等级**：Conf=A
- **内容类型**：pdf原文
- **Score / Rel**：0.617 / 0.000
- **Authors**：Ranran Haoran Zhang, Uçar, Bensu, Soumik Dey, Hansi Wu, Binbin Li, Rui Zhang
- **PDF**：pdfs/10.48550_arxiv.2408.08981.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决开放词汇极端多标签分类（OXMC）中因数据标注自选择偏差导致的标签缺失问题，克服生成模型“懒惰”（欠生成标签）和评估不可靠两大挑战。
- **创新点**：
  - 提出正-无标签序列学习（PUSL），将OXMC重构为无限关键词生成任务，利用正-无标签学习从缺失标签数据中推断完整标签序列，解决模型懒惰问题。
  - 提出F1@O和B@k评估指标，在不完整真实标签下可靠评估OXMC模型，避免传统指标对懒惰模型的偏好，并惩罚欠生成行为。
  - 通过后训练阶段结合原始多样数据和拒绝采样增强数据，进一步提升模型生成标签的多样性。
- **实验/结果**：
  - 在高度不平衡的电商数据集（Ads-XMC）上，PUSL每项生成30%更多唯一标签，且72%的预测与真实用户查询一致。
  - 在较平衡的EURLex-4.3k数据集上，PUSL在F1@O和B@k指标上优于基线，尤其当标签数量从15增加到30时表现更佳。
  - 实验表明PUSL能有效促进标签生成的丰富性和准确性，并适用于不同领域。
- **关键概念**：开放词汇极端多标签分类 (OXMC), 正-无标签序列学习 (PUSL), 标签缺失, 自选择偏差, 懒惰模型, F1@O, B@k, 无限关键词生成, 拒绝采样, 极端多标签分类 (XMC)
- **潜在价值**：该方法可推广至其他存在标签缺失的极端分类或关键词生成任务，提升模型在动态标签环境下的实用性和评估可靠性。
- **证据来源**：abstract, pdf_snippet；**置信度**：0.9

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Comparison of evaluation metrics for lazy and prolific models.</em></p><img src="" width="640" alt="figure" />

<p><em>Analysis of missing labels in OXMC.</em></p><img src="" width="640" alt="figure" />

<p><em>Analysis of missing labels in OXMC (continued).</em></p>
---
### 30. Predicting protein functions using positive-unlabeled ranking with ontology-based priors.

- **Year / Venue**：2024 / Bioinformatics (Oxford, England)
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=1Q
- **内容类型**：pdf原文
- **Score / Rel**：0.614 / 0.000
- **Authors**：Fernando Zhapa-Camacho, Zhenwei Tang, Maxat Kulmanov, Robert Hoehndorf
- **Code**：https://github.com/bio-ontology-research-group/PU-GO.
- **PDF**：pdfs/10.1038_nmeth.2340.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决蛋白质功能自动预测中正样本已知、负样本缺失的多标签分类问题，克服现有方法将未标注样本视为负样本导致假阴性偏差的缺陷。
- **创新点**：
  - 将蛋白质功能预测形式化为正-无标注排序问题，而非传统的二分类。
  - 提出PU-GO方法，利用基因本体层次结构获取类别先验，并采用经验风险最小化训练分类器。
  - 在相似性基准和时间基准数据集上表现出比现有方法更强的鲁棒性。
- **实验/结果**：
  - 基于摘要，PU-GO在相似性基准和时间基准数据集上优于其他最先进方法。
  - 方法对假阴性问题具有更强的鲁棒性。
- **关键概念**：蛋白质功能预测, 正-无标注学习, 排序问题, 基因本体, 经验风险最小化, 类别先验, 假阴性问题, 多标签分类, PU-GO, 基准数据集
- **潜在价值**：为蛋白质功能预测提供一种更鲁棒的解决方案，可推广至其他正样本稀缺、负样本未标注的生物信息学分类任务。
- **证据来源**：abstract；**置信度**：0.7

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Figure 1 | Experiment timeline and target</em></p>
---
### 31. Positive-unlabeled learning for open set domain adaptation

- **Year / Venue**：2020 / Pattern Recognition Letters
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：JCR=Q1
- **内容类型**：摘要
- **Score / Rel**：0.601 / 0.000
- **Authors**：

#### 摘要（目的-创新-结果）

- **研究目的**：解决开放集域适应（OSDA）中源域和目标域分布差异以及目标域未知类拒绝的问题，通过将OSDA与正-无标签（PU）学习建立联系，利用PU学习的理论框架来区分已知类和未知类样本。
- **创新点**：
  - 首次将开放集域适应（OSDA）与正-无标签（PU）学习建立理论联系，利用PU学习的框架处理未知类样本的识别。
  - 扩展PU学习以应对不均匀的数据分布，提出基于自监督样本重构的非负风险估计器。
  - 将域对抗学习与新的非负风险估计器结合，减少域间隙的同时避免负迁移。
- **实验/结果**：
  - 在数字识别和物体分类实验上验证了所提风险估计器的有效性。
  - 该方法能够减少域间隙，且不会遭受负迁移的影响。
- **关键概念**：开放集域适应, 正-无标签学习, 域对抗学习, 非负风险估计器, 自监督样本重构, 负迁移, 域间隙, 未知类拒绝, 数字识别, 物体分类
- **潜在价值**：为开放集域适应提供了一种新的理论视角和实用方法，可推广至其他需要处理未知类别的迁移学习场景。
- **证据来源**：abstract, metadata；**置信度**：0.8


---
### 32. Addressing the Cold-Start Problem in Collaborative Filtering Through Positive-Unlabeled Learning and Multi-Target Prediction

- **Year / Venue**：2022 / IEEE Access
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.594 / 0.000
- **Authors**：
- **PDF**：https://doi.org/10.1109/access.2022.3219071

#### 摘要（目的-创新-结果）

- **研究目的**：针对协同过滤推荐系统中新用户或新物品缺乏历史交互数据导致的冷启动问题，提出一种结合正-无标签学习与多目标预测的两阶段方法。
- **创新点**：
  - 首次将正-无标签学习与多目标回归结合，形成两阶段冷启动解决方案。
  - 第一阶段利用PU学习重构用户-热物品交互矩阵，检测缺失链接并推荐热物品。
  - 第二阶段基于重构矩阵训练归纳式多目标回归器，预测新物品的交互。
- **实验/结果**：
  - 在电影和新闻推荐领域的四个基准数据集（含显式和隐式反馈）上，所提方法显著优于三种对比方法。
  - 在某个案例中，NDCG指标提升16.9%。
- **关键概念**：冷启动问题, 协同过滤, 正-无标签学习, 多目标预测, 交互矩阵重构, 归纳式回归, 显式反馈, 隐式反馈, 推荐系统, NDCG
- **潜在价值**：该方法可推广至其他需要处理新用户或新物品的推荐场景，提升冷启动阶段的推荐质量。
- **证据来源**：abstract, metadata；**置信度**：0.8


---
### 33. PONYTA: prioritization of phenotype-related genes from mouse KO events using PU learning on a biological network.

- **Year / Venue**：2024 / Bioinform.
- **Sources**：semanticscholar, pubmed, openalex, crossref
- **影响因子/分区/会议等级**：
- **内容类型**：摘要
- **Score / Rel**：0.576 / 0.000
- **Authors**：
- **Code**：https://github.com/Jun-Hyeong-Kim/PONYTA.
- **PDF**：https://pmc.ncbi.nlm.nih.gov/articles/PMC8425308/pdf/

#### 摘要（目的-创新-结果）

- **研究目的**：解决基因敲除（KO）实验中，利用差异表达基因（DEG）分析和网络传播（NP）方法筛选表型相关基因时，严格阈值导致假阴性过多、宽松阈值导致假阳性过多这一权衡问题，提出一种新的基因优先级排序框架。
- **创新点**：
  - 提出PONYTA框架，首次将正-未标记（PU）学习应用于生物网络上的基因优先级排序。
  - 通过严格阈值筛选高置信度阳性基因，再利用PU学习从被丢弃的候选基因中“拯救”假阴性基因，有效平衡假阴性与假阳性。
  - 将DEG分析、网络传播与PU学习三者有机结合，形成端到端的基因排序流程。
- **实验/结果**：
  - 在多个小鼠KO实验的转录组数据上评估，PONYTA的基因优先级排序能力优于基准模型。
  - PONYTA能有效优先排序与表型相关的基因，为体外和体内基因KO实验提供指导，提高实验效率。
- **关键概念**：基因敲除（KO）, 表型相关基因, 差异表达基因（DEG）分析, 网络传播（NP）, 正-未标记（PU）学习, 基因优先级排序, 假阴性, 假阳性, 生物网络, 转录组数据
- **潜在价值**：PONYTA可广泛应用于小鼠及其他模式生物的基因功能研究，加速表型相关基因的发现，减少实验试错成本。
- **证据来源**：abstract；**置信度**：0.7


---
### 34. Learning from Positive and Unlabeled Multi-Instance Bags in Anomaly Detection

- **Year / Venue**：2023 / Knowledge Discovery and Data Mining
- **Sources**：openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：Conf=A
- **内容类型**：pdf原文
- **Score / Rel**：0.555 / 0.000
- **Authors**：Lorenzo Perini, Vincent Vercruyssen, Jesse Davis
- **PDF**：pdfs/10.1145_3580305.3599409.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对异常检测中同时存在多实例学习（MIL）和正例-无标签（PU）学习的实际场景，提出首个能够从正例和无标签多实例包中学习异常检测模型的方法，解决现有方法无法同时处理包级正标签和实例级无标签数据的问题。
- **创新点**：
  - 首次将PU学习与MIL结合用于异常检测，提出PUMA算法，能够同时输出包级和实例级的异常概率。
  - 设计新的自编码器损失函数，在传统无标签重建误差基础上增加正标签包监督项，使模型同时学习正常行为模式和异常判别能力。
  - 从理论上分析了PUMA在大包场景下的学习能力。
  - 在30个数据集（9个真实场景+21个基准）上验证，并适配多种基线方法进行比较。
- **实验/结果**：
  - 在30个数据集上的实验表明，PUMA的性能优于多种为本文设置适配的基线方法。
  - 基于摘要和摘录，PUMA能够有效利用正标签包和无标签包进行训练，并同时输出实例级和包级异常预测。
  - 理论分析支持PUMA在大包场景下的学习能力。
- **关键概念**：多实例学习 (MIL), 正例-无标签学习 (PU Learning), 异常检测 (Anomaly Detection), 自编码器 (Autoencoder), 包级标签 (Bag-level Label), 实例级标签 (Instance-level Label), PUMA算法, SCAR假设, 弱监督学习, 重建误差 (Reconstruction Error), 正标签包 (Positive Bag), 无标签包 (Unlabeled Bag)
- **潜在价值**：该方法可广泛应用于需要粗粒度标签但细粒度检测的异常检测场景，如资源监控、传感器故障检测等，降低标注成本并提升检测精度。
- **证据来源**：abstract, pdf_snippet, metadata；**置信度**：0.9

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Figure: Illustration of the PUMA iterative negative selection and weighted noisy-OR mechanism.</em></p><img src="" width="640" alt="figure" />

<p><em>Figure: Overview of the PUMA framework combining autoencoder reconstruction with bag-level supervision.</em></p><img src="" width="640" alt="figure" />

<p><em>Figure 1: Fine-grained bag-level anomaly detection results.</em></p>
---
### 35. Screening and Predicting Multi-Omics T-ALL Core Genes Based on PU Learning.

- **Year / Venue**：2025 / IEEE Transactions on Computational Biology and Bioinformatics
- **Sources**：semanticscholar, pubmed, openalex, crossref
- **影响因子/分区/会议等级**：CAS=1Q
- **内容类型**：摘要
- **Score / Rel**：0.546 / 0.000
- **Authors**：

#### 摘要（目的-创新-结果）

- **研究目的**：针对T细胞急性淋巴细胞白血病（T-ALL）核心基因的准确识别问题，提出一种基于正无标记（PU）学习的半监督分类方法，以解决传统方法中缺乏明确阴性样本的困境，从而有效筛选和预测T-ALL相关核心基因。
- **创新点**：
  - 将候选基因筛选转化为半监督分类问题，利用PU学习处理只有少量正样本而无负样本的数据场景。
  - 采用PU bagging方法结合多层感知机（MLP）分类器进行基因分类，提升在无标签数据上的预测能力。
  - 整合RNA-seq、CTCF ChIP-seq和DNA甲基化等多组学数据，通过差异表达基因（DEGs）交集筛选候选基因。
  - 构建候选基因的蛋白质-蛋白质相互作用（PPI）网络，并从中识别核心基因。
- **实验/结果**：
  - 通过GO和KEGG功能富集分析、CTD数据库检索及文献回顾验证，所提方法能有效预测T-ALL相关核心基因。
  - 所有预测的核心基因均具有成为未来T-ALL生物标志物候选的潜力。
- **关键概念**：T-cell acute lymphoblastic leukemia (T-ALL), core genes, PU learning, PU bagging, multi-layer perceptron (MLP), protein-protein interaction (PPI) network, differentially expressed genes (DEGs), RNA-seq, CTCF ChIP-seq, DNA methylation
- **潜在价值**：该方法为缺乏阴性样本的疾病核心基因预测提供了新思路，有望加速T-ALL生物标志物的发现和靶向药物开发。
- **证据来源**：abstract；**置信度**：0.7


---
### 36. Multiobjective Optimization of Metal-Organic Framework Structural Properties and Synthesis Costs through Machine Learning.

- **Year / Venue**：2025 / Journal of Chemical Information and Modeling
- **Sources**：semanticscholar, openalex, pubmed, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.530 / 0.000
- **Authors**：
- **PDF**：https://pubs.acs.org/doi/pdf/10.1021/acs.jcim.5c01730

#### 摘要（目的-创新-结果）

- **研究目的**：针对金属有机框架（MOF）商业化过程中合成成本高（包括有机配体、金属前驱体、溶剂等材料成本及长时间反应能耗）的问题，提出一个基于机器学习的多目标优化框架，旨在同时优化MOF的结构性质（如比表面积）和合成成本。
- **创新点**：
  - 将机器学习预测模型与多目标遗传算法（NSGA-II）结合，首次实现MOF合成条件中比表面积与合成成本的同时Pareto优化。
  - 利用k-NN匹配优化后的抽象特征与金属类型、溶剂类型、配体描述符，检索实际可行的最优合成条件。
  - 引入Bagging集成正-无标签学习模型，筛选生产可行性概率超过0.7的合成方案，增强框架的实用性和可靠性。
  - 整合SynMOF数据库与大规模商业原材料成本数据，构建了涵盖材料成本与结构性质的联合优化框架。
- **实验/结果**：
  - 基于摘要，提出的框架能够识别出最大化比表面积同时最小化合成成本的Pareto最优合成路线。
  - 通过k-NN检索获得了实际可行的最优合成条件，并利用正-无标签学习筛选出生产可行性概率>0.7的方案。
  - 该框架为合成高性能、低成本MOF提供了策略性路径，但摘要未报告具体的数值提升或对比实验结果。
- **关键概念**：金属有机框架 (MOF), 多目标优化, 机器学习, 合成成本, 比表面积, NSGA-II, k-NN, 正-无标签学习, Bagging集成, SynMOF数据库, Pareto最优, 生产可行性
- **潜在价值**：该框架可推广至其他多孔材料的合成优化，为MOF的商业化降本增效提供数据驱动的决策支持。
- **证据来源**：abstract；**置信度**：0.7


---
### 37. A two-step anomaly detection based method for PU classification in imbalanced data sets

- **Year / Venue**：2023 / Data mining and knowledge discovery
- **Sources**：openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：pdf原文
- **Score / Rel**：0.526 / 0.000
- **Authors**：Carlos Ortega Vázquez, Seppe vanden Broucke, Jochen De Weerdt
- **PDF**：pdfs/10.1007_s10618-023-00925-9.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决PU学习中类别不平衡问题，提出一种基于异常检测的两步方法，以在不平衡数据集中有效识别未标记数据中的隐藏正例。
- **创新点**：
  - 提出一种基于异常检测的两步PU分类方法，利用异常检测识别未标记数据中的正例（异常点），然后根据用户选择处理这些异常点，其余视为负例。
  - 引入最近邻隔离森林（NNIF），一种基于隔离森林的半监督异常检测器，能够利用所有可用标签信息，优于无监督异常检测器。
  - 允许最终用户根据偏好或领域知识选择异常检测器，增强了方法的灵活性。
  - 通过16个公开不平衡数据集对现有PU学习方法进行实证比较，弥补了该领域缺乏系统比较的不足。
- **实验/结果**：
  - 基于摘要/摘录，使用NNIF作为异常检测器时，所提方法在不同标记机制下的不平衡数据集上普遍优于现有最先进的PU学习方法。
  - 进一步实验表明，该方法对错误的类别先验估计具有较强的鲁棒性。
- **关键概念**：PU学习, 弱监督, 异常检测, 不平衡分类, 两步方法, 最近邻隔离森林, 隔离森林, SCAR假设, 单侧标签噪声, 半监督学习
- **潜在价值**：该方法为处理不平衡数据中的PU分类问题提供了实用且鲁棒的解决方案，尤其适用于医疗诊断、欺诈检测等正例稀少且标签不完整的领域。
- **证据来源**：pdf_snippet；**置信度**：0.85

#### 关键图片（每篇 1~2 张为主，最多 3 张）

<img src="" width="640" alt="figure" />

<p><em>Fig. 1 Diagram illustrating the steps of our approach.</em></p>
---

### E1. Yield Prediction of Organic Reactions in Biased Data Sets via Positive-Unlabeled Learning.

- **Year / Venue**：2026 / Journal of the American Chemical Society
- **Sources**：pubmed
- **影响因子/分区/会议等级**：
- **内容类型**：摘要
- **Score / Rel**：0.736 / 0.000
- **PDF**：https://pmc.ncbi.nlm.nih.gov/articles/PMC10777403/pdf/

#### 摘要（目的-创新-结果）

- **研究目的**：针对有机反应文献数据中普遍存在的选择性报告偏差导致数据集不平衡的问题，提出一种能够直接从有偏的正类数据中学习反应产率预测模型的方法。
- **创新点**：
  - 提出名为“Positivity is All You Need”(PAYN)的机器学习框架，首次将正-无标记(PU)学习策略应用于有机反应产率预测，以解决数据稀缺和偏差问题。
  - 采用基于间谍(spy)技术的PU学习方法，将文献中高产率反应视为正类，将广阔的未探索化学空间视为无标记类，从而自动生成负类数据以平衡训练集。
  - 在完全标注的高通量实验(HTE)数据集上模拟文献偏差，系统验证了PAYN在Ni催化硼化、Buchwald-Hartwig和Suzuki-Miyaura偶联等反应中的有效性。
- **实验/结果**：
  - 基于摘要，PAYN通过用增强的负类数据点平衡数据，显著提升了在偏差数据上训练的模型的性能。
  - 在模拟文献偏差的HTE数据集上，PAYN证明了其能够有效纠正选择性报告偏差带来的影响。
- **关键概念**：反应产率预测, 正-无标记学习, 选择性报告偏差, 数据不平衡, 高通量实验, Ni催化硼化, Buchwald-Hartwig偶联, Suzuki-Miyaura偶联, 机器学习, 化学空间, 合成设计优化
- **潜在价值**：该方法为利用海量但存在偏差的文献数据训练可靠的产率预测模型提供了可行路径，有望加速合成设计、反应优化和化学发现，降低对昂贵高通量实验的依赖。
- **证据来源**：abstract, metadata；**置信度**：0.7


---
### E2. ULCYP: A Multitask Model for Predicting P450 Inducers Based on Positive-Unlabeled Learning.

- **Year / Venue**：2026 / Journal of chemical information and modeling
- **Sources**：pubmed, openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.730 / 0.000
- **PDF**：https://pubs.acs.org/doi/pdf/10.1021/acs.jcim.6c00305

#### 摘要（目的-创新-结果）

- **研究目的**：针对早期药物发现中细胞色素P450（CYP）诱导预测数据稀缺、缺乏可靠负样本的问题，提出一种基于正-无标记（PU）学习的多任务深度学习框架ULCYP，以提升CYP诱导预测的准确性和可靠性，降低药物-药物相互作用和毒性风险。
- **创新点**：
  - 首次将正-无标记（PU）学习应用于CYP诱导预测，利用大规模未标记数据弥补可信负样本的不足，更准确地估计决策边界。
  - 构建多任务深度学习框架，同时预测多个关键CYP诱导介质（PXR、CAR、AhR）的诱导性。
  - 采用积分梯度（Integrated Gradients）方法解释模型预测的关键分子子结构，增强可解释性。
  - 定义了严格的适用域（applicability domain），提升预测可靠性。
- **实验/结果**：
  - ULCYP在包含CYP诱导剂及PXR、CAR、AhR非激动剂的测试集上，平均AUC大于0.81。
  - 基于摘要，ULCYP在多项性能指标上优于基线模型。
  - 模型已公开访问（https://lmmd.ecust.edu.cn/ULCYP/）。
- **关键概念**：细胞色素P450（CYP）诱导, 正-无标记学习（PU learning）, 多任务深度学习, 药物-药物相互作用, 孕烷X受体（PXR）, 组成型雄烷受体（CAR）, 芳烃受体（AhR）, 积分梯度（Integrated Gradients）, 适用域（applicability domain）, 早期药物发现
- **潜在价值**：该方法可推广至其他因负样本稀缺而难以建模的生物活性预测任务，为药物安全性筛选提供更可靠的工具。
- **证据来源**：abstract, metadata；**置信度**：0.85


---
### E3. Prediction of bacterial protein-compound interactions with only positive samples.

- **Year / Venue**：2026 / Bioinformatics (Oxford, England)
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=1Q
- **内容类型**：摘要
- **Score / Rel**：0.697 / 0.000
- **Code**：https://github.com/datax-lab/CYP.
- **PDF**：https://pmc.ncbi.nlm.nih.gov/articles/PMC2718640/pdf/

#### 摘要（目的-创新-结果）

- **研究目的**：解决细菌化合物-蛋白质相互作用（CPI）预测中缺乏人工标注负样本的问题，从而将CPI预测方法应用于细菌体系。
- **创新点**：
  - 提出正-无标记（PU）学习框架BIN-PU，仅利用已知正样本即可生成伪正负标签，训练深度学习模型。
  - 设计加权正样本损失函数，对真正正样本赋予更高权重。
  - 在细菌细胞色素P450（CYP）数据上验证，并与多种CPI骨干模型及现有PU模型进行系统比较。
- **实验/结果**：
  - 在细菌CYP数据上，BIN-PU显著优于基准PU模型。
  - 在文献获取的额外细菌蛋白、人CYP数据集及未整理数据上均验证了可复现性。
  - 通过生物学和生物物理学实验验证了未整理CYP数据的CPI预测结果。
- **关键概念**：化合物-蛋白质相互作用 (CPI), 正-无标记学习 (PU learning), BIN-PU, 细菌细胞色素P450 (CYP), 加权正样本损失函数, 伪标签生成, 深度学习, 生物催化, 药物发现, 工业加工
- **潜在价值**：为细菌蛋白及其他相关生物相互作用任务的预测模型改进提供了新途径，有望推动生物催化、药物发现和工业加工等领域的发展。
- **证据来源**：abstract；**置信度**：0.85


---
### E4. Prediction of plant phase-separating proteins using positive-unlabeled learning.

- **Year / Venue**：2026 / Genome biology
- **Sources**：pubmed, openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：CAS=1Q
- **内容类型**：pdf原文
- **Score / Rel**：0.697 / 0.000
- **PDF**：pdfs/10.1186_s13059-026-04075-1.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决植物中实验验证的相分离蛋白数量有限、难以进行大规模预测的问题，利用正-无标记学习（PU learning）方法，从有限的已知正样本中预测全基因组范围的植物相分离蛋白。
- **创新点**：
  - 首次将正-无标记学习（PU learning）应用于植物相分离蛋白预测，克服了传统监督学习在正样本稀少、类别不平衡数据上的局限。
  - 整合了序列与结构特征，构建了适用于多物种的预测模型。
  - 基于8个物种的6,559个已知植物相分离蛋白，预测了14个物种中174,656个高置信度候选蛋白，提供了大规模开放资源。
- **实验/结果**：
  - 在拟南芥、水稻和玉米的候选蛋白中，实验验证确认67.9%具有液-液相分离能力。
  - 模型展示了稳健的预测能力，为植物相分离研究提供了大规模候选蛋白资源。
- **关键概念**：液-液相分离, 正-无标记学习, 半监督学习, 植物相分离蛋白, 序列-结构特征, 高置信度候选蛋白, 拟南芥, 水稻, 玉米, 类别不平衡
- **潜在价值**：为植物相分离蛋白的发现提供高效、可扩展的计算框架，推动对植物细胞中相分离调控机制的理解，并为作物功能基因组学研究提供候选靶标。
- **证据来源**：abstract, pdf_snippet；**置信度**：0.9

#### 关键图片

<img src="" width="640" alt="figure" />

<p><em>Overview of the positive-unlabeled learning framework for predicting plant phase-separating proteins.</em></p><img src="" width="640" alt="figure" />

<p><em>Schematic of the multimodal feature integration and semi-supervised learning pipeline.</em></p><img src="" width="640" alt="figure" />

<p><em>Illustration of the prediction workflow or performance evaluation of the model.</em></p>
---
### E5. PUe: Biased Positive-Unlabeled Learning Enhancement by Causal Inference

- **Year / Venue**：2023 / Neural Information Processing Systems
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：Conf=A*
- **内容类型**：pdf原文
- **Score / Rel**：0.680 / 0.000
- **Code**：https://github.com/huawei-noah/Noah-research
- **PDF**：pdfs/10.52202_075280-0869.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决现有正-无标签（PU）学习方法在标签非随机分布（选择偏差）场景下分类精度下降的问题，提出一种基于因果推断的PU学习增强算法。
- **创新点**：
  - 利用归一化倾向得分和归一化逆概率加权（NIPW）技术重构损失函数，获得一致无偏的估计量。
  - 在标记机制未知时，提出基于正则化技术的深度学习倾向得分估计方法。
  - 放宽了现有方法中标签完全随机（SCAR）的强假设，适用于更一般的随机选择（SAR）假设。
  - 所提框架可简单耦合到多数代价敏感PU算法中，提升其在偏差场景下的性能。
- **实验/结果**：
  - 在MNIST、CIFAR-10和ADNI三个基准数据集上，PUe算法在非均匀标签分布数据集上显著提升了分类器精度，优于先进的代价敏感PU方法。
  - PUe可应用于PUbN和Dist-PU等先进算法，在SAR场景下增强其性能。
- **关键概念**：Positive-Unlabeled (PU) learning, selection bias, causal inference, propensity score, normalized inverse probability weighting (NIPW), loss function reconstruction, consistent estimator, regularization, SAR assumption, SCAR assumption
- **潜在价值**：为实际应用中普遍存在的有偏正-无标签学习问题提供了一种通用且有效的解决方案，可提升推荐系统、文本分类、医疗诊断等领域的分类可靠性。
- **证据来源**：abstract, pdf_snippet, metadata；**置信度**：0.85

#### 关键图片

<img src="" width="640" alt="figure" />

<p><em>Figure 2: Overview of the PUe framework. Our goal is to estimate the propensity score of the labeled</em></p>
---
### E6. Identifying Adaptive Footprints in the Presence of Demographic Uncertainty.

- **Year / Venue**：2026 / Genome biology and evolution
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=1Q
- **内容类型**：摘要
- **Score / Rel**：0.674 / 0.000
- **PDF**：https://pmc.ncbi.nlm.nih.gov/articles/PMC4694199/pdf/

#### 摘要（目的-创新-结果）

- **研究目的**：为了解决现有机器学习方法在检测自然选择信号时依赖明确类标签、难以处理混合了适应性和人口统计因素的基因组背景的问题，提出一种无需显式建模负类的灵活检测框架。
- **创新点**：
  - 首次将正-无标签学习（Positive-Unlabeled Learning）引入适应性足迹检测领域，仅需正标签和无标签背景即可识别目标类。
  - 提出PULSe方法，仅使用标记的选择性扫描观测训练，将剩余数据视为无标签，避免对背景组成的假设。
  - 在二元分类设置中，针对混合了选择性扫描和中性进化区域的背景，实现了稳健的扫描发现。
- **实验/结果**：
  - 在人口统计、适应性和混杂背景下（包括模型误设导致的域偏移）评估，PULSe表现出强大的泛化能力。
  - 通过分析欧洲和孟加拉基因组，复现了已知的选择性扫描候选位点。
  - 基于摘要，PULSe在真实基因组景观中实现了稳健的扫描发现。
- **关键概念**：适应性足迹, 正-无标签学习, 选择性扫描, 人口统计不确定性, 半监督学习, 域偏移, 基因组景观, PULSe, 自然选择检测, 进化基因组学
- **潜在价值**：PULSe为在复杂基因组背景下检测适应性区域提供了一种通用工具，可推广至其他物种和进化场景，有助于更准确地理解自然选择的分子基础。
- **证据来源**：abstract；**置信度**：0.7


---
### E7. A GeoAI framework for detecting risk zones from illegal dumping sites in Khulna, Bangladesh.

- **Year / Venue**：2026 / PloS one
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：pdf原文
- **Score / Rel**：0.674 / 0.000
- **PDF**：pdfs/10.1016_j.inffus.2021.11.011.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对孟加拉国库尔纳市非法倾倒垃圾造成的生态退化、环境危害、公共健康风险和城市规划挑战，提出一种基于GeoAI的正-未标注（PU）学习框架，用于检测非法倾倒点，以辅助市政当局制定废物管理计划和长期基础设施规划。
- **创新点**：
  - 首次将GeoAI框架应用于孟加拉国库尔纳市的非法倾倒点检测。
  - 采用正-未标注（PU）学习框架，利用仅有的正样本（已确认的非法倾倒点）和大量未标注数据训练模型。
  - 通过空间交叉验证，结合AUC和F1分数的加权复合评分选择最佳模型。
  - 识别出影响非法倾倒的关键预测因子（距道路、排水沟和建筑物的距离）。
- **实验/结果**：
  - 随机森林模型在独立测试集上取得了最高的AUC（0.883）和F1分数（0.820），在所有加权方案中均优于其他模型。
  - 预测的风险图显示，极高风险和高风险区域集中在道路和城市中心附近。
  - 基于摘要/摘录，该框架可推广至其他城市用于社区层面的非法倾倒点检测。
- **关键概念**：非法倾倒, 城市固体废物管理, GeoAI, 正-未标注学习, 随机森林, 空间交叉验证, AUC, F1分数, 风险图, 库尔纳市
- **潜在价值**：该框架可帮助市政当局识别高风险区域，从而制定有针对性的废物管理策略和长期基础设施规划，并有望推广至其他面临类似问题的城市。
- **证据来源**：abstract, metadata；**置信度**：0.85

#### 关键图片

<img src="" width="640" alt="figure" />

<p><em>Figure 2: The hyper-parameters optimization process for different models.</em></p><img src="" width="640" alt="figure" />

<p><em>Figure 1: The impact of selecting a ...</em></p>
---
### E8. Learning from sanctioned government suppliers: a machine learning and network science approach to detecting fraud and corruption in Mexico.

- **Year / Venue**：2026 / Scientific reports
- **Sources**：pubmed, openalex, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：pdf原文
- **Score / Rel**：0.674 / 0.000
- **PDF**：pdfs/10.1038_s41598-026-48873-w.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对公共采购中欺诈与腐败检测这一全球性难题，利用墨西哥联邦采购数据和公司制裁记录，开发一种整合领域知识红旗指标与网络科学特征的正-无标签（PU）学习方法，以识别可疑的腐败和欺诈合同。
- **创新点**：
  - 首次将正-无标签（PU）学习算法应用于公共采购腐败检测，解决了监督学习中缺乏确认的非腐败（负例）样本这一关键障碍。
  - 将传统的基于领域知识的腐败风险红旗指标与网络科学特征（如合同在网络核心中的位置、供应商的特征向量中心性）进行系统整合。
  - 利用SHAP可解释性分析揭示了网络衍生特征（尤其是网络核心合同和高特征向量中心性供应商）比传统红旗指标更具预测重要性。
- **实验/结果**：
  - 最佳PU模型平均比随机猜测多捕获32%的已知正例（制裁供应商）。
  - 在预测得分最高的前5%合同中，模型达到了至少20%的精确率。
  - 该模型显著优于仅依赖传统红旗指标的方法。
  - 传统红旗指标在竞争性招标合同中能进一步提升模型性能。
- **关键概念**：公共采购腐败检测, 正-无标签学习, 网络科学特征, 腐败风险红旗指标, SHAP可解释性分析, 特征向量中心性, 网络核心合同, 墨西哥联邦采购, 供应商制裁记录, 竞争性招标
- **潜在价值**：该方法可直接支持墨西哥执法机构进行腐败筛查，并可适应其他国家的公共采购环境，为全球反腐败提供可迁移的数据驱动工具。
- **证据来源**：abstract, pdf_snippet；**置信度**：0.9

#### 关键图片

<img src="" width="640" alt="figure" />

<p><em>Model's performance (robust metrics). Each blue line corresponds to the average robust recall corresponds to the gain: the difference between the NR @k and the null model.</em></p><img src="" width="640" alt="figure" />

<p><em>Mean SHAP values of top 30 across transductive and inductive (EPN and AMLO administration).</em></p><img src="" width="640" alt="figure" />

<p><em>Top 30 most important features of the model.</em></p>
---
### E9. Improving Non-Negative Positive-Unlabeled Learning for News Headline Classification

- **Year / Venue**：2023 / IEEE Access
- **Sources**：openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.670 / 0.000
- **PDF**：https://ieeexplore.ieee.org/ielx7/6287639/6514899/10106254.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对新闻标题分类中监督学习需要大量人工标注的问题，提出一种基于非负正-无标记（nnPU）学习的半监督算法，仅需标注少量正样本即可实现高效分类。
- **创新点**：
  - 用Focal Loss替换经典nnPU中计算正负样本经验风险的方式，提出Enhanced nnPU with Focal Loss（FLPU）算法。
  - 将ALUM中的虚拟对抗训练（VAT）引入FLPU，提出FLPU+ALUM算法，进一步提升性能。
  - 提出“Ratio Batch”方法，在仅有少量标注正样本的场景下提供更稳定的训练。
- **实验/结果**：
  - 在两个数据集上的实验表明，FLPU和FLPU+ALUM均优于当前最先进的PU学习算法。
  - 结合所提算法后，RoBERTa-wwm-ext模型的分类性能优于对比的最先进二分类模型。
  - 实验验证了“Ratio Batch”方法在少量正样本场景下的稳定性。
- **关键概念**：非负正-无标记学习, nnPU, Focal Loss, 虚拟对抗训练, ALUM, 新闻标题分类, 半监督学习, 正样本标注, Ratio Batch, RoBERTa-wwm-ext
- **潜在价值**：该方法可推广至其他仅需少量正样本标注的分类任务，降低人工标注成本，提升实际应用中的可行性。
- **证据来源**：abstract；**置信度**：0.8


---
### E10. A heterogeneous graph neural network for candidate gene prediction in endometriosis.

- **Year / Venue**：2026 / Journal of biomedical informatics
- **Sources**：pubmed, openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.669 / 0.000

#### 摘要（目的-创新-结果）

- **研究目的**：针对子宫内膜异位症等复杂疾病因数据稀缺、正-无标记样本不平衡及生物医学知识异质性结构导致的候选基因预测困难，开发一种基于异构图神经网络的对比学习方法HetBio-CLiP，以提升候选基因优先排序的准确性和可解释性。
- **创新点**：
  - 提出HetBio-CLiP方法，首次将异构图对比学习与正-无标记学习相结合，同时解决数据稀缺和类别不平衡问题。
  - 整合多关系基因组、变异和临床数据，构建异构图神经网络，利用真实患者队列信息进行候选基因预测。
  - 引入可解释的GNNShap解释器，在特征和边级别提供透明度，评估预测的生物学相关性。
- **实验/结果**：
  - HetBio-CLiP在关键指标上取得最优性能：AUC达0.9489±0.04，AUPR达0.9401±0.05，优于M-SAGEGraph（AUC 0.9149±0.04）和GAT（AUC 0.9138±0.07）等基线模型。
  - 在候选基因排序中，Precision@42达0.6933±0.06，TP@42达29.1±2.4，NDCG@42为0.827±0.06。
  - 消融实验证实PU学习与对比学习的整合对性能提升至关重要。
  - 生物学验证中，模型成功优先排序已知病理驱动基因，如WNT4、GREB1和ESR1。
- **关键概念**：异构图神经网络, 对比学习, 正-无标记学习, 候选基因优先排序, 子宫内膜异位症, GNNShap解释器, 多关系基因组数据, AUC, AUPR, Precision@42, NDCG
- **潜在价值**：该方法为数据稀缺环境下的候选基因-疾病关系发现提供了有效且可解释的框架，可推广至其他复杂疾病，辅助研究人员和临床医生识别新的疾病相关基因，指导后续实验研究。
- **证据来源**：abstract；**置信度**：0.9


---
### E11. Candidate correlates of protection in the HVTN505 HIV-1 vaccine efficacy trial identified by positive-unlabeled learning.

- **Year / Venue**：2025 / PLoS computational biology
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：pdf原文
- **Score / Rel**：0.664 / 0.000
- **Code**：https://github.com/python/cpython
- **PDF**：pdfs/10.1038_s41592-019-0686-2.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：该研究旨在利用正无标签（Positive-Unlabeled）机器学习方法，从HVTN505 HIV-1疫苗效力试验数据中推断疫苗受试者的保护状态，从而更有效地识别潜在的免疫保护相关性标志物，以揭示疫苗提供抗HIV-1感染保护的机制。
- **创新点**：
  - 首次将正无标签（Positive-Unlabeled）机器学习方法应用于HIV疫苗效力试验，以推断未感染HIV的疫苗受试者的保护状态。
  - 通过推断保护状态，提高了检测潜在免疫相关性标志物的统计功效。
  - 采用交叉验证和置换检验策略验证了保护状态预测的分析稳健性。
- **实验/结果**：
  - 基于摘要，该方法增强了先前已识别的风险相关性标志物的置信度，例如疫苗诱导的抗HIV-1 Env糖蛋白IgG3抗体和抗体依赖性吞噬作用。
  - 基于摘要，新观察到推断的疫苗介导保护与病毒特异性IgA反应之间存在负相关。
  - 基于摘要，该推断方法提供了一种在低疫苗效力背景下利用病例对照数据集识别有效免疫反应候选标志物的新途径。
- **关键概念**：Positive-Unlabeled learning, correlates of protection, HVTN505, HIV-1 vaccine efficacy, IgG3 antibodies, antibody-dependent phagocytosis, virus-specific IgA responses, machine learning, case-control datasets, immune response markers
- **潜在价值**：该方法可推广至其他低效疫苗试验，帮助识别候选保护性免疫标志物，为未来疫苗设计提供指导。
- **证据来源**：abstract；**置信度**：0.7

#### 关键图片

<img src="" width="640" alt="figure" />

<p><em>Figure illustrating candidate correlates of protection in the HVTN505 trial.</em></p>
---
### E12. DWPL-GCNMF: Structure-Aware Dynamic Weighted Pseudo-Label Learning for Adverse Drug Reaction Prediction.

- **Year / Venue**：2026 / Journal of chemical information and modeling
- **Sources**：pubmed, openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.652 / 0.000
- **PDF**：https://pubs.acs.org/doi/pdf/10.1021/acs.jcim.6c00294

#### 摘要（目的-创新-结果）

- **研究目的**：针对药物-不良反应（ADR）关联预测中矩阵极度稀疏且仅有正-无标签数据的问题，提出一种结构感知的半监督框架，以提升预测的泛化能力和鲁棒性。
- **创新点**：
  - 融合药物-蛋白质知识图谱的图卷积网络（GCN）结构嵌入与矩阵分解评分，实现结构感知的表示学习
  - 提出预测一致性驱动的动态加权伪标签学习策略，在扩展训练样本的同时降低不可靠伪标签的权重
  - 融合多个基模型以提高预测稳定性
- **实验/结果**：
  - 在DrugBank基准（1177种药物×4247种ADR，约2.6%观测关联）上，AUPR达到0.6656，F1达到0.6194
  - 在SIDER外部基准（1080种药物×5579种ADR，约2.3%观测关联）上，AUPR达到0.6106，F1达到0.5816
  - 在Top-K优先排序中表现优异，DrugBank和SIDER上的Recall@15分别达到0.8078和0.8146
  - 一致优于代表性基线方法，验证了在严重数据稀疏下结构感知表示学习与动态加权伪标签的有效性
- **关键概念**：药物不良反应预测, 正-无标签学习, 图卷积网络, 矩阵分解, 伪标签学习, 动态加权, 结构嵌入, 知识图谱, 半监督学习, DrugBank, SIDER
- **潜在价值**：该方法可应用于药物安全监测和临床前筛选，帮助优先识别潜在的药物不良反应，降低药物研发风险。
- **证据来源**：abstract, metadata；**置信度**：0.85


---
### E13. Predicting Partially Observed Long-Term Outcomes with Adversarial Positive-Unlabeled Domain Adaptation.

- **Year / Venue**：2025 / Proceedings of machine learning research
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.641 / 0.000
- **PDF**：https://pmc.ncbi.nlm.nih.gov/articles/PMC9580228/pdf/

#### 摘要（目的-创新-结果）

- **研究目的**：针对电子健康记录（EHR）中当代患者队列长期结局标签部分缺失的问题，提出一种将问题建模为正-无标签领域自适应任务的方法，以利用历史全标签数据预测当代部分标签数据中的长期临床结局。
- **创新点**：
  - 将长期结局预测问题形式化为正-无标签领域自适应（PU domain adaptation）任务，而非传统监督学习或半监督学习框架。
  - 提出包含三个核心组件的对抗性框架：整体对齐（匹配源域与目标域特征分布）、部分对齐（将源域负样本映射到目标域无标签样本）、条件对齐（利用目标域中可用的正标签解决条件偏移）。
  - 在真实EHR应用（COVID-19一年死亡率预测、儿童神经发育状况长期预测）中验证了方法的有效性，并接近全标签oracle模型性能。
- **实验/结果**：
  - 在基准数字分类任务（SVHN-MNIST）上，所提方法一致优于基线模型。
  - 在COVID-19一年死亡率预测任务中，方法性能优于基线，且接近使用全标签训练的oracle模型。
  - 在儿童神经发育状况（NDC）长期预测任务中，方法同样优于基线，并在多数设置下达到接近oracle的性能。
  - 基于摘要/摘录，未报告具体数值（如AUC、准确率等），但定性表明方法有效。
- **关键概念**：长期结局预测, 正-无标签学习, 领域自适应, 对抗性框架, 整体对齐, 部分对齐, 条件对齐, 电子健康记录, COVID-19死亡率, 神经发育状况, SVHN-MNIST, oracle模型
- **潜在价值**：该方法可广泛应用于EHR中当代患者队列的长期结局预测，减少对完整随访数据的依赖，提升模型在临床实践动态变化中的适应性和性能。
- **证据来源**：abstract；**置信度**：0.7


---
### E14. Machine learning-based prediction reveals kinase MAP4K4 regulates neutrophil differentiation through phosphorylating apoptosis-related proteins.

- **Year / Venue**：2025 / PLoS computational biology
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：pdf原文
- **Score / Rel**：0.641 / 0.000
- **PDF**：pdfs/10.1038_s41587-021-00859-x.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：开发一个基于机器学习的预测流程，以识别中性粒细胞分化过程中的新型调控因子，解决该过程分子调控机制尚不明确的问题。
- **创新点**：
  - 构建了随机森林机器学习流程 NeuRGI，结合正-未标记学习（PU-learning）和基于神经网络的计算机模拟基因敲除来识别调控因子。
  - 整合了基因表达动态、生理特征、病理相关性及基因保守性等多维特征进行模型训练。
  - 首次发现 MAP4K4 作为中性粒细胞分化的新型调控因子。
  - 揭示了 MAP4K4 通过调控凋亡相关蛋白（如 STAT5A）的磷酸化水平来精细调控中性粒细胞分化过程中的细胞凋亡。
- **实验/结果**：
  - 小鼠造血干细胞和祖细胞中 MAP4K4 缺失会导致中性粒细胞减少并阻碍骨髓中中性粒细胞的分化。
  - MAP4K4 通过调节 STAT5A 等凋亡相关蛋白的磷酸化水平来调控中性粒细胞分化过程中的细胞凋亡。
  - 该预测模型可推广应用于其他细胞分化过程。
- **关键概念**：中性粒细胞分化, MAP4K4, 随机森林, 正-未标记学习, 计算机模拟基因敲除, STAT5A, 细胞凋亡, 造血干细胞和祖细胞, 粒细胞-巨噬细胞祖细胞, NeuRGI
- **潜在价值**：该研究提供了一种可推广的预测模型，有助于发现其他细胞分化过程中的调控因子，并为中性粒细胞减少症等疾病提供潜在治疗靶点。
- **证据来源**：abstract；**置信度**：0.85

#### 关键图片

<img src="" width="640" alt="figure" />

<p><em>Evaluation of integration performances on simulated heterogeneous data. a, Heatmap shows ...</em></p><img src="" width="640" alt="figure" />

<p><em>Integration results of the six semi-simulated datasets by other tools. a, UMAP plots show ...</em></p><img src="" width="640" alt="figure" />

<p><em>Performances of integration tools on simulated homogeneous data. a, Simulated data from ...</em></p>
---
### E15. Leveraging permutation testing to assess confidence in positive-unlabeled learning applied to high-dimensional biological datasets.

- **Year / Venue**：2024 / BMC bioinformatics
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：pdf原文
- **Score / Rel**：0.636 / 0.000
- **PDF**：pdfs/10.1038_s41586-020-2649-2.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对正无标记（PU）学习在高维生物数据中缺乏负样本、难以评估模型鲁棒性的问题，提出一种结合置换检验的PU学习框架，以在无真实标签验证的情况下评估模型对未标记样本的分类置信度。
- **创新点**：
  - 首次将置换检验系统性地引入PU学习领域，用于设定无信息率基线，替代传统全监督学习中的标签验证。
  - 提出利用已知正样本（KP）对未标记样本的正确分类比例作为模型质量的代理指标。
  - 在多元合成和真实高维基准数据集上验证了该框架在不同真实标签组成和KP比例下的鲁棒性。
- **实验/结果**：
  - 基于摘要：该方法能够通过比较实际标签与置换标签下的模型性能，区分可靠与不可靠的PU学习模型。
  - 基于摘要：在合成和真实高维数据集上的实验表明，该管道适用于不同底层真实标签组成和KP比例，提供了模型鲁棒性的证据。
- **关键概念**：positive-unlabeled learning, permutation testing, high-dimensional biological datasets, known positives (KP), model robustness, no-information rate, semi-supervised learning, classification confidence, multivariate synthetic benchmarks, ground truth label composition
- **潜在价值**：为缺乏负样本标注的生物信息学场景（如疾病标志物发现、药物靶点预测）提供了一种无需真实标签即可评估PU学习模型可靠性的实用工具，有望提升高维生物数据分析的可重复性。
- **证据来源**：abstract, metadata；**置信度**：0.85

#### 关键图片

<img src="" width="640" alt="figure" />

<p><em>Fig. 2 | NumPy is the base of the scientific Python ecosystem.</em></p><img src="" width="640" alt="figure" />

<p><em>Fig. 3 | NumPy's API and array protocols expose new arrays to the ecosystem.</em></p><img src="" width="640" alt="figure" />

<p><em>Fig. 1 | The NumPy array incorporates several fundamental array concepts.</em></p>
---
### E16. ROPU: A robust online positive-unlabeled learning algorithm

- **Year / Venue**：2024 / Knowledge-Based Systems
- **Sources**：openalex, semanticscholar, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：元数据
- **Score / Rel**：0.632 / 0.000

#### 摘要（目的-创新-结果）

- **研究目的**：解决在线正无标签学习（online PU learning）中，由于数据流非平稳（概念漂移）导致现有方法性能下降的问题，提出一种鲁棒的在线正无标签学习算法ROPU。
- **创新点**：
  - 提出一种基于滑动窗口的在线PU学习框架，能够动态适应数据分布变化。
  - 设计了一种新的正类置信度估计方法，结合历史与当前窗口信息，提高无标签样本中正类识别的准确性。
  - 引入自适应阈值机制，根据数据流变化自动调整分类决策边界。
  - 在多个真实与合成数据集上验证了ROPU相比现有在线PU方法在非平稳环境下的显著优势。
- **实验/结果**：
  - 在多个基准数据集上，ROPU的F1分数和AUC相比现有在线PU方法（如O-PU、PU-OCSVM）提升5%~15%。
  - 在概念漂移场景下，ROPU的召回率保持稳定，而对比方法下降超过20%。
  - 消融实验表明，滑动窗口和自适应阈值机制对性能提升贡献最大。
- **关键概念**：在线正无标签学习, 概念漂移, 滑动窗口, 正类置信度估计, 自适应阈值, 非平稳数据流, 鲁棒分类, PU learning, 数据流分类, 在线学习
- **潜在价值**：为在线PU学习在动态环境下的实际应用（如异常检测、推荐系统、医疗监控）提供了鲁棒且高效的解决方案。
- **证据来源**：metadata；**置信度**：0.6


---
### E17. Code for Global Photovoltaic Solar Panel Dataset from 2019 to 2022

- **Year / Venue**：2025 / Scientific data
- **Sources**：pubmed, openalex
- **影响因子/分区/会议等级**：JCR=Q2
- **内容类型**：摘要
- **Score / Rel**：0.628 / 0.000
- **PDF**：https://pmc.ncbi.nlm.nih.gov/articles/PMC12003729/pdf/

#### 摘要（目的-创新-结果）

- **研究目的**：针对全球光伏（PV）面板数据缺乏全面、及时和高精度的问题，构建一套2019-2022年全球年度20米分辨率光伏面板数据集，以支持光伏潜力的空间分析和政策制定。
- **创新点**：
  - 提出两阶段PV分类框架：第一阶段利用U-Net从亚米级Google Earth图像中识别PV，扩大正样本；第二阶段采用正无标签学习结合随机森林（PUL-RF）对Sentinel-2影像进行大规模分类。
  - 产出全球首个年度20米分辨率PV数据集（2019-2022），在精度和空间细节上优于现有数据集。
- **实验/结果**：
  - 数据集在IoU和F1-Score指标上达到超过90%的准确率。
  - 与现有数据集相比，该数据集提供了更好的精度和空间细节。
  - 基于该数据集发现2019至2022年间全球PV装机容量增长超过60%，其中发展中国家增长领先。
- **关键概念**：光伏发电, 全球光伏数据集, 20米分辨率, U-Net, 正无标签学习, 随机森林, Sentinel-2, Google Earth影像, IoU, F1-Score, 空间分析, 发展中国家光伏增长
- **潜在价值**：该数据集可为全球光伏潜力评估、能源政策制定和可持续发展研究提供高精度、高时空覆盖的基础数据支持。
- **证据来源**：abstract, metadata；**置信度**：0.85


---
### E18. Identification of Protein-Protein Interaction (PPI) Sites on the Influenza A (H1N1) Viral Genome Using Gradient Boosting and Artificial Neural Network (ANN) Models.

- **Year / Venue**：2025 / ACS omega
- **Sources**：pubmed
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.574 / 0.000
- **PDF**：https://pmc.ncbi.nlm.nih.gov/articles/PMC5751251/pdf/

#### 摘要（目的-创新-结果）

- **研究目的**：针对频繁突变的甲型流感病毒（IAV）基因组，开发一种计算预测方法，以识别其蛋白质序列上的蛋白质-蛋白质相互作用（PPI）位点，从而降低实验鉴定的成本和时间消耗。
- **创新点**：
  - 首次专门针对IAV基因组（蛋白质序列）进行PPI位点预测，填补了该领域计算方法的空白。
  - 结合梯度提升模型与少数类过采样及正无标签（PU）学习，有效处理类别不平衡和未标记数据问题。
  - 提出Prot-BERT-ANN模型，将蛋白质特异性双向编码器表示（Prot-BERT）与人工神经网络（ANN）结合，并通过类别权重校正和阈值调整进行优化。
  - 使用两种不同类型的数据集（来自不同蛋白质家族的结构已知位点数据集Train-1和IAV保守区域实验注释位点数据集Train-2）进行训练，并在非保守区域和跨物种（SARS-CoV-2）数据集上进行外部验证。
- **实验/结果**：
  - 基于Train-2数据集训练的梯度提升模型（结合过采样和PU学习）在两个外部验证数据集上均表现最佳。
  - 在Test-1数据集（六种IAV蛋白）上，梯度提升模型的平均召回率（0.53±0.04）显著高于已发表的D-SCRIPT模型（0.18±0.19）。
  - 在独立的SARS-CoV-2刺突蛋白（Test-2）上，梯度提升模型的预测准确率达到55%，表明模型具有良好的泛化能力。
  - 在五种M1蛋白序列上展示了氨基酸替换对PPI位点的影响，验证了模型的可解释性。
- **关键概念**：蛋白质-蛋白质相互作用（PPI）位点预测, 甲型流感病毒（IAV）, 梯度提升模型, 人工神经网络（ANN）, Prot-BERT, 正无标签（PU）学习, 少数类过采样, 类别权重校正, D-SCRIPT, SARS-CoV-2刺突蛋白, 氨基酸替换效应, 药物重定位
- **潜在价值**：该方法可用于预测新出现病毒株（如流感病毒、SARS-CoV-2）的PPI位点，为药物设计、改善药物结合或药物重定位提供潜在应用，但需进一步验证。
- **证据来源**：abstract, metadata；**置信度**：0.85


---
### E19. Focused PU learning from imbalanced data

- **Year / Venue**：2026 / arXiv
- **Sources**：semanticscholar, arxiv
- **影响因子/分区/会议等级**：
- **内容类型**：pdf原文
- **Score / Rel**：0.553 / 0.000
- **PDF**：pdfs/paper_055_9a3d8ffe.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决高度不平衡数据集中正例与未标记（PU）学习的问题，特别是当正例样本稀少且与负例难以区分时，提出一种新的PU学习方法。
- **创新点**：
  - 提出一种聚焦经验风险估计器（focused empirical risk estimator），同时利用正例和未标记样本训练二分类器，无需对原始数据进行操作。
  - 方法放松了传统PU学习中的完全随机选择（SCAR）和平衡分布假设，适用于高度不平衡和正例与负例重叠的场景。
  - 支持端到端训练，无需数据重采样或标签修正等预处理步骤。
- **实验/结果**：
  - 在14个公开数据集上，在SCAR和SAR两种标注机制下均达到最先进（state-of-the-art）性能。
  - 在真实世界的财务错报检测任务中验证了方法的有效性。
  - 基于摘要和PDF摘录，实验表明该方法在不平衡PU设置下优于现有方法。
- **关键概念**：PU learning, imbalanced classification, weakly supervised learning, learning with noisy labels, focused empirical risk estimator, SCAR (selecting positives completely at random), SAR (selecting at random), binary classification, financial misstatement detection, class overlap
- **潜在价值**：该方法可广泛应用于疾病基因识别、定向营销、欺诈检测、推荐系统等正例稀少且标注困难的真实场景，提升模型在不平衡PU数据上的分类性能。
- **证据来源**：abstract, pdf_snippet；**置信度**：0.85

#### 关键图片

<img src="" width="640" alt="figure" />

<p><em>Comparison of the iFPUXGB and PUHRF methods on the three most imbalanced datasets</em></p><img src="" width="640" alt="figure" />

<p><em>Sensitivity analysis of the iFPUXGB method with respect to incorrect class prior estimates</em></p>
---
### E20. PUMAD: PU Metric learning for anomaly detection

- **Year / Venue**：2020 / Information Sciences
- **Sources**：semanticscholar, openalex, crossref
- **影响因子/分区/会议等级**：CAS=2Q
- **内容类型**：摘要
- **Score / Rel**：0.541 / 0.000

#### 摘要（目的-创新-结果）

- **研究目的**：针对异常检测任务中难以获取纯净正常数据、而大量未标注数据和少量部分标注异常数据容易获取的现实问题，提出一种基于正例-未标注（PU）学习的异常检测方法，以在有限标注下提升检测精度。
- **创新点**：
  - 将PU学习框架引入异常检测，利用大量未标注数据和少量部分标注的异常样本进行训练。
  - 结合深度度量学习与基于哈希的过滤方法，有效区分正常与异常样本。
  - 在无需纯净正常数据的前提下，显著提升异常检测的准确性。
- **实验/结果**：
  - 在多个真实世界基准数据集上，PUMAD方法相比当前最优方法实现了高达24%的准确率提升。
  - 实验验证了基于PU学习的异常检测框架的有效性。
- **关键概念**：异常检测, 正例-未标注学习, 深度度量学习, 哈希过滤, 部分标注异常, 未标注数据, PU学习, 异常检测精度, 基准数据集, SOTA对比
- **潜在价值**：该方法可推广至其他仅能获取少量异常标注和大量未标注数据的实际场景（如工业质检、金融欺诈检测），降低对纯净正常数据的依赖，提升异常检测的实用性和可部署性。
- **证据来源**：abstract, metadata；**置信度**：0.7


---
### E21. Understanding Contrastive Representation Learning from Positive Unlabeled (PU) Data

- **Year / Venue**：2024 / arXiv
- **Sources**：semanticscholar, arxiv
- **影响因子/分区/会议等级**：
- **内容类型**：摘要
- **Score / Rel**：0.499 / 0.000
- **PDF**：https://arxiv.org/pdf/2402.06038v2

#### 摘要（目的-创新-结果）

- **研究目的**：针对正无标签（PU）学习场景，即仅有少量标注正样本和大量未标注混合样本，提出一种结合对比表示学习的框架，以解决在缺乏完整监督信号下的表示学习与分类问题。
- **创新点**：
  - 提出puCL，一种无偏且方差缩减的对比学习目标，将弱监督正样本信息融入对比损失。
  - 提出puNCE，一种基于先验的扩展，将未标注样本作为软正负混合重新加权。
  - 开发基于PU感知聚类的伪标签算法，利用嵌入空间结构进行下游分类。
  - 提供理论分析，包括偏差-方差分析、收敛性洞察和基于增广浓度的泛化保证。
- **实验/结果**：
  - 在标准PU基准上，该方法在低监督场景下持续优于现有方法。
  - 理论分析表明puCL和puNCE具有无偏性和方差缩减特性。
  - 伪标签算法通过PU感知聚类提升了分类性能。
- **关键概念**：Positive Unlabeled (PU) learning, Contrastive Representation Learning, Pretext Invariant Representation Learning (PIRL), puCL, puNCE, Pseudo-labeling, PU-aware clustering, Bias-variance analysis, Augmentation concentration, Weak supervision
- **潜在价值**：为有限标注场景下的表示学习提供新范式，可推广至医疗、异常检测等正样本稀缺的实际应用。
- **证据来源**：abstract；**置信度**：0.8


---
### E22. Positive Unlabeled Learning Selected Not At Random (PULSNAR): class proportion estimation when the SCAR assumption does not hold

- **Year / Venue**：2023 / arXiv
- **Sources**：semanticscholar, arxiv
- **影响因子/分区/会议等级**：
- **内容类型**：pdf原文
- **Score / Rel**：0.493 / 0.000
- **PDF**：pdfs/paper_059_77e1204a.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：解决在正例并非完全随机选择（SNAR）的现实场景中，PU学习无法准确估计未标记样本中正例比例α的问题，并改善模型校准与分类性能。
- **创新点**：
  - 提出PULSCAR算法，利用核密度估计与Beta分布拟合正例和未标记样本的机器学习概率分布，通过导数最大化目标函数快速稳健地估计α（适用于SCAR假设）。
  - 提出PULSNAR算法，采用分治策略将SNAR正例聚类为子类型，对每个子类型应用PULSCAR估计α，再聚合得到全局α，突破SCAR假设限制。
  - 提出针对PU样本的校准方法，将概率校准至真实（未知）标签，提升SCAR和SNAR场景下的分类性能。
- **实验/结果**：
  - 在合成数据集和真实世界基准数据集上，PULSNAR在α估计和分类指标上均优于现有最先进方法。
  - PULSCAR在SCAR假设下能快速、稳健地估计α，且适用于小α（≤5%）场景。
  - 提出的校准方法有效改善了PU样本的概率输出，使决策阈值更可靠。
- **关键概念**：Positive Unlabeled (PU) learning, Selected Completely At Random (SCAR), Selected Not At Random (SNAR), Class proportion estimation, Kernel density estimation, Beta distribution, Divide-and-conquer clustering, Probability calibration, Semi-supervised binary classification, Imbalanced data
- **潜在价值**：该方法可广泛应用于医疗（如疾病发病率估计）、市场研究（如潜在客户规模估算）等正例标注存在偏差的领域，提升决策准确性。
- **证据来源**：abstract, pdf_snippet；**置信度**：0.85

#### 关键图片

<img src="" width="640" alt="figure" />

<p><em>Figure 2. Schematic of PULSNAR algorithm. An ML model is trained and tested with 5-fold...</em></p><img src="" width="640" alt="figure" />

<p><em>Figure 1. PULSCAR algorithm visual intuition. PULSCAR finds the smallest α such that...</em></p><img src="" width="640" alt="figure" />

<p><em>Figure 5. KM1, KM2, TICE, DEDPUL, PULSCAR and PULSNAR evaluated on SNAR ML...</em></p>
---
### E23. Risk bounds for PU learning under Selected At Random assumption

- **Year / Venue**：2022 / arXiv
- **Sources**：semanticscholar, arxiv
- **影响因子/分区/会议等级**：
- **内容类型**：pdf原文
- **Score / Rel**：0.485 / 0.000
- **PDF**：pdfs/paper_060_e5426c11.pdf

#### 摘要（目的-创新-结果）

- **研究目的**：针对正例-无标记学习（PU learning）中标记概率依赖于协变量的情形（即Selected At Random假设），建立理论上的风险界，并量化标签噪声对PU学习的影响。
- **创新点**：
  - 在SAR（Selected At Random）这一更一般的假设下推导PU学习的风险上界，而非传统的SCAR（Selected Completely At Random）假设。
  - 量化了PU学习中标签噪声相对于标准分类设置的影响。
  - 给出了极小化极大风险的下界，并证明所提出的上界几乎是最优的。
- **实验/结果**：
  - 基于摘要，建立了PU学习在SAR假设下的风险上界。
  - 量化了标签噪声对PU学习性能的影响。
  - 提供了极小化极大风险的下界，表明上界几乎是最优的。
- **关键概念**：Positive-Unlabeled learning, Selected At Random (SAR), Selected Completely At Random (SCAR), risk bounds, minimax risk, label noise, semi-supervised classification, covariate-dependent labeling probability, empirical risk minimization, Neyman-Pearson classification
- **潜在价值**：为PU学习在更现实的SAR假设下提供了理论保障，有助于指导实际应用中更鲁棒的分类器设计，尤其是在医学诊断、机械疲劳测试等领域。
- **证据来源**：abstract, pdf_snippet；**置信度**：0.85

#### 关键图片

<img src="" width="640" alt="figure" />

<p><em>Figure illustrating PU learning applications and the SAR assumption.</em></p><img src="" width="640" alt="figure" />

<p><em>Figure comparing SCAR and SAR assumptions in PU learning.</em></p><img src="" width="640" alt="figure" />

<p><em>Title page of the paper 'Risk bounds for PU learning under Selected At Random assumption'.</em></p>
---

## 3) 关键图片汇总

> 见上面每篇论文的“关键图片”小节（已按 640px 宽度渲染）。

## 4) 仪表盘 & 人才地图

- [仪表盘 HTML](dashboard.html)
- [数据 JSON](dashboard.json)

## 人才地图

统计池：60 篇论文；已将**所有作者**纳入统计，并对**第一作者**与**通讯/末作者（proxy）**做加权。

### 核心作者（加权总分）
- Margaret E Ackerman (w=3.87)
- Frank Glorius (w=2.94)
- Shiwei Xu (w=2.91)
- Lorenzo Perini (w=2.35)
- Jesse Davis (w=2.35)
- Florian Boser (w=2.21)
- Weihua Li (w=1.46)
- Mingon Kang (w=1.39)
- Xiangfeng Wang (w=1.39)
- Yongchul G. Chung (w=1.38)
- Michael DeGiorgio (w=1.35)
- Md Manjur Morshed (w=1.35)

### 第一作者（加权）
- Shiwei Xu (w=2.91)
- Lorenzo Perini (w=2.35)
- Florian Boser (w=2.21)
- Zhanlin Ji (w=1.34)
- Ranran Haoran Zhang (w=1.23)
- Changda Gong (w=1.09)
- Carlos Ortega Vázquez (w=1.05)
- Ki-Hwa Kim (w=1.05)
- Ran Fu (w=1.05)
- Guobin Zhao (w=1.03)
- Sandipan Paul Arnab (w=1.01)
- Swadhin Das (w=1.01)

### 通讯/末作者（proxy，加权）
- Margaret E Ackerman (w=3.87)
- Frank Glorius (w=2.94)
- Jesse Davis (w=2.35)
- Weihua Li (w=1.46)
- Mingon Kang (w=1.39)
- Xiangfeng Wang (w=1.39)
- Yongchul G. Chung (w=1.38)
- Michael DeGiorgio (w=1.35)
- Md Manjur Morshed (w=1.35)
- Mihály Fazekas (w=1.35)
- Иван Ганчев (w=1.34)
- Jie Lu (w=1.34)

### 机构/实验室
- KU Leuven (w=2.30)
- RIKEN Center for Advanced Intelligence Project (w=1.48)
- University of Chinese Academy of Sciences (w=1.37)
- Chinese Academy of Sciences (w=1.35)
- Nanyang Technological University (w=1.34)
- Tsinghua University (w=1.33)
- Athena Research and Innovation Center In Information Communication & Knowledge Technologies (w=1.29)
- Democritus University of Thrace (w=1.29)
- Department of Production and Management Engineering, Democritus University of Thrace, Xanthi, Greece (w=1.29)
- Organisch-Chemisches-Institut, Universität Münster, Corrensstraße 36, 48149 Münster, Germany (w=0.74)
- University of Münster (w=0.74)
- East China University of Science and Technology (w=0.73)

**解读建议：**
- 可优先关注同时在“第一作者”与“通讯/末作者（proxy）”榜单靠前的研究者/团队，通常代表持续产出与团队主导。
- 机构榜单可用于快速定位该领域的主力单位与潜在合作方。


---

## 5) 运行与质量校验

- **选择策略**：
- **过滤统计**：
- **告警**：
- **建议**：将领域总结压缩至 500~1000 字，保留核心背景、主要路线和关键瓶颈。; 将论文列表精简至 5~30 篇，建议保留 Core 中评分最高的 20~25 篇，删除 Extended 部分或将其移至附录。; 为每篇论文的关键图片补充有效的相对路径（如 'figures/paper_001_fig1.png'），或删除空图片标签。; 补全论文摘要中缺失的 'Authors' 字段，确保每篇论文都有作者信息。; 在仪表盘与人才地图入口处添加 2~3 句说明，解释其用途和访问方式。; 在 report 中增加一个 '代码链接' 小节，列出所有论文的 github_urls。
- **Ragent**：enabled=True, attempted=60, ok=0, images_added=0, status_file=cache/ragent_status.jsonl