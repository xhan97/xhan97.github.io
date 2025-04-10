---
layout: post
title: "Academic Experimentation and Writing: Practical Advices"
date: "2022-05-23 21:13:24"
categories:
    - Research
tags:
    - Experiment_writing
---
在如何做好科研方面，许多知名学者已经在网络上分享了他们的见解。本人虽资历尚浅，难以谈论科研之“道”，但在科研之“术”上，希望能总结一些经验。本文写于我的第一篇 A* 文章接受之后，借此机会结合个人的投稿和审稿经历，介绍实验管理和论文写作中的技巧及注意事项。然而，科研仍然以创新为核心，技法仅为辅助手段，切忌本末倒置。<!-- more -->

## 实验管理

从构思 idea 到获得完整的实验结果，这个过程需要经过多次修正和迭代。规范的实验管理不仅能加速算法验证，还能促进实验结果的扩展和分析。此外，详尽的实验记录能够保障实验的可复现性，而复现性是科学方法的基本原则之一。

### 尽早建立 Pipeline

可以借鉴软件工程中的持续集成和持续部署概念，尽快建立从数据输入到模型结果的 Pipeline，即使最初使用的是最简单的模型。在整个实验周期中不断迭代更新，但需确保每次迭代时这个 Pipeline 是可以正常运行的。

1. 尽量减少 Pipeline 中各模块之间的耦合，采用通用接口。因为每个模块都有可能随时扩展，如增加对比算法，这些算法可能使用不同的输入格式甚至不同的编程语言实现。
2. 常规实验可使用 Linux Shell 脚本建立 Pipeline，把数据处理、模型训练、算法对比、结果收集等模块串联起来。如此一来，实验只需在服务器上运行，等待最终结果。甚至可以编写脚本，将实验结果生成 Latex 表格，直接复制到论文的 tex 源码中。对于更大的项目，可以使用 Github Action 或 Jenkins 等集成工具。
3. 在 Windows 上使用 WSL2 Linux 内核是一个不错的选择。既拥有了两个操作系统，又可以将 Linux 环境导出到硬盘，并在另一台支持 WSL2 的计算机上复制使用，避免更换计算机后重新配置实验环境。

### 数据管理

为保证实验的可复现性，数据处理时需注意以下事项：

1. 永远不要编辑原始数据，尤其不要手动编辑。不要覆盖原始数据，也不要保存多个版本。将数据（及其格式）视为不可更改。
2. 为数据的每个阶段构建处理流程，可以在中间阶段保存处理结果，但确保每个中间结果可以从原始数据生成。
3. 仔细确认数据处理，避免测试集数据泄漏。
4. 在实验周期中，记录模型版本、参数、评价结果、运行时间等数据，为后续分析和优化模型做准备。除手动记录外，还可以使用 MLFlow 的 [Model Traking](https://mlflow.org/docs/latest/tracking.html)。

### 代码管理

现在各大会议都鼓励投稿者声明代码的可复现性，甚至投稿时要求提交源码。我们研究组也鼓励所有被录用的工作开源代码。开源工作更容易被同行使用或对比，是增加自己工作影响力的有效方式。

关于代码管理的总结如下：

1. Git is All You Need. 版本控制极其重要，保持清晰的版本记录有利于回溯。每次 commit 时，信息应尽可能完整，说明增加了哪些功能或修复了哪些 Bugs。此外，善用 Git 的分支在 main 分支上留下清晰的版本记录。
2. 及时重构，减少代码墒增。虽然许多人在数据分析或训练模型时喜欢用 Jupyter notebook，但 notebook 不利于复用和自动化。目前本人主要在初期探索或展示模型效果时使用 notebook，验证无误后会尽快重构为 Python 脚步文件。减少代码的墒增，避免后续实验过多，难以维护。
3. 恰当的代码注释。避免以后连自己都看不懂写的代码。
4. 平时注意收集常用的画图代码块，精调配色和显示效果（字体和大小）。
5. 源码之下，没有秘密。不论是自己的还是阅读他人代码时，算法细节在源码下展露无遗，应确保发布的代码与论文一致。

代码的组织结构可以参考 [Cookiecutter Data Science](https://github.com/drivendata/cookiecutter-data-science)  的设置。这个框架提供了清晰的代码组织，使用此框架发布的代码也可以直接被其他人安装使用。可根据其组织结构建立适合自己的框架。

    ├── LICENSE
    ├── Makefile           <- Makefile with commands like `make data` or `make train`
    ├── README.md          <- The top-level README for developers using this project.
    ├── data
    │   ├── external       <- Data from third party sources.
    │   ├── interim        <- Intermediate data that has been transformed.
    │   ├── processed      <- The final, canonical data sets for modeling.
    │   └── raw            <- The original, immutable data dump.
    │
    ├── models             <- Trained and serialized models, model predictions, or model summaries
    │
    ├── notebooks          <- Jupyter notebooks. Naming convention is a number (for ordering),
    │                         the creator's initials, and a short `-` delimited description, e.g.
    │                         `1.0-jqp-initial-data-exploration`.
    │
    ├── references         <- Data dictionaries, manuals, and all other explanatory materials.
    │
    ├── reports            <- Generated analysis as HTML, PDF, LaTeX, etc.
    │   └── figures        <- Generated graphics and figures to be used in reporting
    │
    ├── requirements.txt   <- The requirements file for reproducing the analysis environment, e.g.
    │                         generated with `pip freeze > requirements.txt`
    │
    ├── setup.py           <- makes project pip installable (pip install -e .) so src can be imported
    ├── src                <- Source code for use in this project.
    │   ├── __init__.py    <- Makes src a Python module
    │   │
    │   ├── data           <- Scripts to download or generate data
    │   │   └── make_dataset.py
    │   │
    │   ├── features       <- Scripts to turn raw data into features for modeling
    │   │   └── build_features.py
    │   │
    │   ├── models         <- Scripts to train models and then use trained models to make
    │   │   │                 predictions
    │   │   ├── predict_model.py
    │   │   └── train_model.py
    │   │
    │   └── visualization  <- Scripts to create exploratory and results oriented visualizations
            └── visualize.py

## 论文写作

在 AI 领域，竞争异常激烈，每年顶会的投稿量达到上千甚至上万篇。审稿人根本不够用，所以指望每位审稿人都能仔细阅读稿件并提供公正的评论是不现实的。根据经验，审稿人在拿到稿件的5到10分钟内就大致决定了稿件的命运。如果可以接受，就根据摘要和贡献写评语，指出一些小问题。如果决定拒稿，则会细看稿件，找出一些理由拒绝。因此，除了要有优秀的创意和扎实的实验外，写作显得尤为重要。

基本上遵循一个原则：**以读者为核心阐述工作**。

1. 信息的呈现符合读者的认知惯性。引人入胜，让读者能快速找到想要的信息。
2. 降低读者的理解难度。综合使用信息元素：图 > 曲线 > 表 > 正文> 公式
3. 提高读者阅读愉悦感。论证充分、组织合理、逻辑严密、文笔优美、排版美观。

### 摘要（Abstract）

摘要写作时不要力图将所有细节说清楚，用语简单，让“外行”能看的懂。基本结构逐句说明：

1. 研究内容是什么
2. 现在的方法有什么问题
3. 我们解决思路是什么
4. 实现这个思路会遇到哪些难点
5. 我们怎么解决的
6. 我们解决的还不错

### 引言 (Introduction)

引言部分基本上是摘要的扩展，通过几段话清晰阐述工作。重点是充分论证所做工作的必要性和重要性。基本结构逐段实现以下目的：

1. 任务是什么
2. 现有方法有哪些
3. 现有最好的方法有什么问题
4. 我们怎么解决的、解决思路是什么
5. 我们的核心贡献是什么

段落的写法：

1. 每个段落有个诊断性的中心句
2. 其余部分都是支持句，围绕中心句展开论证
   a. 使用前人工作
   b. 使用具体数据
3. 支持句之间可分类组织
4. 断尾可加上衔接句保持上下文的连贯。

注：现在非常流行的在第一页右上部分加上一个图表说明本文的 Motivation。使得读者不需要读整段话就知道做了什么。

### 相关工作 (Related Work)

Related Work 需要向读者展示如下内容：

1. 向审稿人展示对研究领域具有全面深刻的把握；
2. 通过与前人工作对比凸显工作的创新性；
3. 为读者梳理领域发展脉络，获得全局认识。

切忌出现如下问题：

1. 没有引用重要论文（可作为直接拒稿的理由）；
2. 简单的罗列和堆砌，缺乏深刻的讨论。

### 方法 (Methodology)

1. 方法部分需要形式化描述，解释数学符号的意义
2. 利用 Running Example， 通俗的语言描述想法
   1. 英语不好说清楚，用例子
   2. 围绕着running example, 展开自己的工作
   3. 看完running example, 审稿人便能知道核心思想
3. 每个公式都有其语言学意义，源于你的直觉和想法，应直接告诉审稿人，避免让他们去揣摩。

### 实验 (Experiments)

1. 使用公认的标准数据集和 state-of-the-art 系统
2. 如果你的算法有很多的 parameters/magic numbers,
   1. 对所有的参数，选择以下其一
      1. 展示如何设置这些值（通过理论或者实验）
      2. 展示你的算法对确切的值不敏感（做参数敏感性检验）
   2. 解释每一个选择
      1. 如果你的选择是任意的，使用类似于我们换了其他的选择，但是实验表明并没有太大区别，为了简单起见，选择了文中使用的选择；
      2. 如果选择不是任意的，那就证明它是正确的。
3. 必须要有显著性检验
4. 不辞辛苦，做到极致
   1. 对于在正文声明的所有论点，都需要通过实验结果体现
   2. 使用消融实验验证算法的每个创新点。

### 其他细节

1. 行文占满所有可用空间。有些审稿人看到大量留白会质疑：实验是否充分、相关工作的讨论是否足够、参考文献是否充足；
2. 文中图尽可能使用矢量图。
3. 图表中 Caption 应包含充分的信息。最好能直接看图，不用再去看正文。
4. 文中每个数学符号都应该找到定义；
5. 避免数学符号冲突，如果空间足够，使用符号列表；
6. 排版美观和专业。

## 做好不被承认的准备

在 AI 领域竞争如此激烈的背景下，论文投稿量急剧增长。期望逢投必中是美好的，但现实往往很骨感。论文被拒甚至多次被拒，这种否定容易让人陷入自我怀疑和否定的泥潭。以我自身为例，最近发表的 A* 文章曾被拒三次，整整一年仿佛在泥潭中挣扎，无法自拔。这个经历让我学到：

1. 尽快调整心态，其实投顶会或者顶刊，接受率只有 15%-25%，被拒才是常态；
2. 分析审稿意见，把没有阐述清楚、引起误会或错误的地方修改掉；
3. 实验不充分的尽快加实验，比如常见的 baseline 不够新或者太少；
4. 时间不等人，写到极致，尽快改完投出去，等下一个结果；
5. 实在坚持不下去了，降低一级投稿。
6. 当自我怀疑和否定时，能熬就自己熬过去，不行就找朋友聊聊；相信只要论文写出来，总有地方会认可和接受。

## 参考文献

- [1] Eamonn Keogh. How to do goode research, get it published in SIGKDD and get it cited.
- [2] 刘洋. 机器翻译学术论文写作方法和技巧。
