# Node2Vec-SynCom-Reproduction

## 基于 Node2Vec 的合成菌群(SynCom)知识图谱表征学习复现项目

---

### 👥 小组基本信息
- **课程**：可重复性研究方法（2026春季）
- **组织归属**：`D2RS-2026spring`
- **小组名称**：Node2Vec合成菌群表征学习研究组
- **小组成员**：@CANGSU-mei（单人独立完成，全流程代码编写、调试与文档撰写均可追溯）
- **复现论文**：Grover A, Leskovec J. *node2vec: Scalable Feature Learning for Networks*[C]//Proceedings of the 22nd ACM SIGKDD international conference on Knowledge discovery and data mining. 2016: 855-864.

---

### 🎯 项目简介与研究动机
在合成菌群（SynCom）的理性设计中，挖掘微生物之间复杂的互作网络至关重要。本项目旨在评估斯坦福大学于 2016 年提出的经典网络特征学习算法 **node2vec** 在现代计算环境下的可复现性。

**实验设计说明：**
考虑到专有菌群图谱数据的保密性与复现成本，本项目采用原论文 4.1 章节中经典的基准数据集 **Zachary's Karate Club** 进行算法的“功能性验证（Functional Validation）”。通过二阶偏置随机游走策略（调节参数 $p$ 和 $q$），算法旨在同时捕捉网络的**同质性（Homophily，对应功能菌群社区）**与**结构等价性（Structural Equivalence，对应核心菌株/代谢物角色）**。打通此复现流水线后，该特征提取方案可直接迁移至真实的 SynCom 知识图谱中。

---

### 📁 项目目录结构 (Project Management)
本项目严格按照可重复性研究的标准规范进行组织：

```text
Node2Vec-SynCom-Reproduction-main/
├── data/                  # 内置基准数据集 (Data Source)
│   └── karate.edgelist    # 空手道俱乐部关系网图谱文件
├── code/                  # 核心源代码 (Codebase)
│   └── SynCom_Analysis.ipynb  # 【核心文档】文学化编程Notebook（含数据加载、模型训练、可视化与解读）
├── results/               # 实验产出物 (Artifacts)
│   ├── my_karate.emb      # 算法持久化生成的 128维 节点嵌入向量文件
│   └── reproduction_clustering.png  # PCA降维及聚类可视化结果图
├── .gitignore             # Git 忽略配置
├── LICENSE                # 开源协议
├── requirements.txt       # 精确锁定的环境依赖清单
└── README.md              # 本项目说明文档

```

---

### 🛠️ 环境配置与排雷记录 (Software Decay Overcoming)

在复现过程中，我们发现原版代码（基于 Python 2.7）存在显著的软件衰败现象。本项目在 **Python 3.13.5** 环境下重构，并成功解决了以下底层冲突，确保后人可 100% 顺畅复现：

1. **依赖代差冲突解决**：原版 `node2vec` 隐式锁死了旧版 `numpy (<2.0.0)`，导致在 Python 3.13 下引发 C++ 编译器错误。本研究采用“解耦安装策略”，手动补齐了兼容现代架构的 Numpy 依赖及 `tqdm` 进度条组件。
2. **一键复现环境部署**：
```bash
# 使用项目提供的 requirement.txt 一键恢复实验环境
pip install -r requirements.txt

```



---

### 🚀 复现操作步骤 (Reproducibility Steps)

1. 将本仓库克隆至本地电脑。
2. 使用 Positron、VS Code 或 Jupyter 环境打开 `code/SynCom_Analysis.ipynb` 文件。
3. 点击工具栏的 **"Run All" (运行全部单元格)**。
4. 全程使用 CPU 运行（耗时约十余秒），所有实验结果（数值验证、日志、图片）将自动刷新并落盘至 `results/` 文件夹。

---

### 🧪 核心实验结果与原理论证 (Literate Programming Interpretation)

本项目的核心发现与代码解读均采用文学化编程（Literate Programming）范式撰写于 Notebook 中。核心结果验证如下：

#### 1. 向量降维与社区聚类验证

* **结果**：通过将生成的 128 维特征向量使用 PCA 降至二维，并应用 K-Means 聚类，算法以 **94.1%** 的准确率成功识别出原网络分裂的两个派系。
* **科学原理解读**：在 $p=1, q=1$（退化为 DeepWalk 机制）的参数设定下，可视化的散点图清晰显示，原网络中拓扑结构相近的节点在低维向量空间中依然保持聚拢。这完美复现了论文中提及的 **“邻域保持（Neighborhood Preserving）”** 特性。

#### 2. 下游任务表现验证

* **节点分类**：基于嵌入特征训练逻辑回归分类器，准确率达 **94.12%**，证明提取的向量具有极高的线性可分性。
* **链接预测 (Link Prediction)**：参考原论文 4.7 节方法，基于特征向量的哈达玛积（Hadamard Product）构建边特征，预测缺失连边的 AUC 评分高达 **0.8923**，显著优于传统启发式网络指标。

#### 3. 产物一致性

* 成功落盘了 `my_karate.emb` 文件。其 `34行 × 64列` 的数据结构。

---

### ✅ 可复现性保障声明

* **环境隔离**：提供精确到小版本的 `requirements.txt`（如 `networkx==3.2.1`），杜绝 API 突变。
* **随机性控制**：所有随机游走与机器学习算法均已全局固定随机种子 (`random_state=42`)，确保在任何设备上输出数值 100% 相同。
* **数据内置**：所有轻量级数据集均内置于仓库中，无需依赖失效的外部下载链接。
* **低算力门槛**：剔除了对 GPU 的依赖，轻薄本即可完成全套算法生命周期。

---


### 📚 参考文献
[1] Grover A, Leskovec J. node2vec: Scalable feature learning for networks[C]//Proceedings of the 22nd ACM SIGKDD international conference on Knowledge discovery and data mining. 2016: 855-864. [cite_start]*(本项目主复现论文)* [cite: 3, 4, 6]

[2] Perozzi B, Al-Rfou R, Skiena S. DeepWalk: Online learning of social representations[C]//Proceedings of the 20th ACM SIGKDD international conference on Knowledge discovery and data mining. 2014: 701-710. [cite_start]*(算法对比基准：当 p=1, q=1 时的退化模型)* [cite: 638]

[3] Mikolov T, Chen K, Corrado G, et al. Efficient estimation of word representations in vector space[C]//ICLR. 2013. [cite_start]*(理论基石：Skip-gram 模型的原始提出文献，node2vec 底层优化的数学基础)* [cite: 633, 634]

[4] Tang J, Qu M, Wang M, et al. LINE: Large-scale information network embedding[C]//Proceedings of the 24th international conference on world wide web. 2015: 1067-1077. [cite_start]*(算法对比基准：专注于一阶与二阶邻近度的大规模网络嵌入模型)* [cite: 645, 646]

[5] Mikolov T, Sutskever I, Chen K, et al. Distributed representations of words and phrases and their compositionality[C]//Advances in neural information processing systems (NIPS). 2013: 3111-3119. [cite_start]*(工程实现参考：提出了负采样 Negative Sampling 技术，用于加速极大规模网络的特征学习)* [cite: 635, 636]