---
layout: post
title: "Advice in Academic Experiment and Writing"
date: "2022-05-23 21:13:24"
categories: 
    - Research
tags: 
    - Experiment_writing
--- 
对于如何做好科研，已经有很多成名已久的大牛在网络上分享了自己的见解，本人自知资历尚浅，不足以谈论科研之“道”，只在科研之“术”进行一次总结。本文写于第一篇 A* 文章接受之后，趁热打铁，将结合自身投稿和审稿的经历介绍实验管理和论文写作中的技法和注意问题。但科研依然是创新至上，技法为辅，不可本末倒置。

## 实验管理

从 idea 产生到完整的实验结果，要经过很长时间的修正和迭代。规范的实验管理不仅能达到快速验证算法，也有利于扩展和分析实验结果。同时，详尽的实验记录也可以保证实验可复现性，而复现性是科学方法的主要原则之一。

### 尽早建立 Pipeline

可以借鉴软件工程中持续集成和持续部署的思路，尽快建立起从数据输入到模型结果的 Pipeline, 即使最开始使用最简单的模型。在整个实验周期内不断迭代更新，但必须保证每次迭代时这个 Pipeline 是可以跑通的。

1. 尽量减少 Pipeline 中每个模块之间的耦合，采用通用接口。因为在整个实验中每个模块都有可能随时扩展，比如增加对比算法，这些算法有不同的输入格式甚至是使用不同的编程语言实现的。
2. 对于一般实验来讲，可以使用 Linux Shell 脚本建立 Pipeline, 将数据处理、模型训练、算法对比、结果收集等模块串起来。这样每次跑实验丢到服务器上后只等着拿最终结果了。甚至可以写一个从实验结果生成 Latex Tabel 的脚本, 可以直接复制到论文的 tex 源码中。更大的项目可以使用 Github Action 或者 Jenkins 等集成工具。
3. Windows 下使用 WSL2 Linux Kernel 是个不错的选择。好处是不但拥有了两个操作系统，而且使用的 Linux 环境可以导出硬盘并在另一台支持 WSL2 的计算机上复制一份。这样也不用担心换计算机后实验环境要重新配置一遍了。

### 数据管理

为保证实验的复现，在数据处理时要注意以下内容：

1. 永远不要编辑原始数据，不要手动编辑，尤其不要在 Excel 中。不要覆盖你的原始数据。不要保存原始数据的多个版本。将数据（及其格式）视为不可变的。
2. 为数据的每个阶段构建处理流程，可以在中间阶段保存处理结果，但一定确保每个中间结果可以从原始数据生成。
3. 仔细确认数据处理，避免出现测试集的数据泄漏。
4. 在整个实验周期中，需要记录设计的模型版本、模型参数、评价结果、运行时间等数据，为之后分析和调优模型做准备。这里除了手动加上日志外可以使用 MLFlow 的 [Model Traking](https://mlflow.org/docs/latest/tracking.html)。

### 代码管理

现在各大会议上都鼓励投稿者声明代码的复现性，投稿时需要提交源码。我们研究组也鼓励所有被录用的工作将代码开源。开源的工作更容易被其他同行使用或对比，是增加自己工作的影响力的有效途径。

对于代码管理有如下总结：

1. Git is All You Need. 版本控制是极其重要的，保持清晰的版本记录有利于回溯。每次 commit 时，信息尽可能完整，表明增加了那些功能或者修改了那些 Bugs, 此外利用好 Git 的分支在 main 分支上留下清晰的版本记录。
2. 及时重构，减少代码墒增。现在做数据分析或者训练模型，大多喜欢用 Jupyer notebook. 但其实 notebook 不利于复用和自动化的。目前本人使用 notebook 主要作为初期探索或者模型效果展示，验证无误后会尽快重构为 Python 可执行文件。减少代码的墒增，避免后续实验太多，难以维护；
3. 恰当的代码注释。避免以后连自己都不认识写的代码了。
4. 平时注意收集常用的画图代码块，精调配色和显示效果（字体和大小）；
5. 源码之下，没有秘密。不论是自己的还是阅读其他人的代码时，算法细节在源码下展露无疑，要确保发布的代码与论文的一致。

代码的组织结构可以参考 [Cookiecutter Data Science](https://github.com/drivendata/cookiecutter-data-science) 的设置。这个框架提供了清晰的代码组织，使用这个框架发布的代码也可以直接被其他人安装使用。可以根据这个组织结构建立适合自己的框架。

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

如今 AI 领域的竞争非常激烈，每年顶会的投稿量达到上千甚至上万篇。审稿人根本不够用，所以期望遇到的每个审稿人都能极富责任的仔细阅读到手的稿件，并给出中肯的评论是不现实的。其实根据以往的经验，审稿人在拿到稿件的5到10分钟内就大致确定了稿件能否被录取。如果能被录取，就根据稿件的摘要和贡献写一写评语，指出些次要小毛病。如果决定拒，就再细看稿件，从中找一些问题就给拒掉了。因此除了有很好的 idea 和 扎实的实验外，写作是重中之重。

基本上遵循一个原则：**以读者为核心阐述工作**。

1. 信息的呈现符合读者的认知惯性。引人入胜，让读者能快速找到想要的信息。
2. 降低读者的理解难度。综合使用信息元素：图 > 曲线 > 表 > 正文> 公式
3. 提高读者阅读愉悦感。论证充分、组织合理、逻辑严密、文笔优美、排版美观。

### 摘要写作

摘要写作时不要力图将所有细节说清楚，用语简单，让“外行”能看的懂。基本结构逐句说明：

1. 研究内容是什么
2. 现在的方法有什么问题
3. 我们解决思路是什么
4. 实现这个思路会遇到哪些难点
5. 我们怎么解决的
6. 我们解决的还不错

### Introduction

Introdution 部分基本上是摘要的扩展，用几段话说清工作。要点是充分论证所做工作的必要性和重要性。基本结构是在逐段达到以下目的：

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

### Related Work

Related Work 需要向读者展示如下内容：

1. 向审稿人展示对研究领域具有全面深刻的把握；
2. 通过与前人工作对比凸显工作的创新性；
3. 为读者梳理领域发展脉络，获得全局认识。

切忌出现如下问题：

1. 没有引用重要论文（可作为直接拒稿的理由）；
2. 简单的罗列和堆砌，缺乏深刻的讨论。

### 方法描述

1. 方法部分需要形式化描述，解释数学符号的意义
2. 利用 Running Example， 通俗的语言描述想法
   1. 英语不好说清楚，用例子
   2. 围绕着running example, 展开自己的工作
   3. 看完running example, 审稿人便能知道核心思想
3. 每个公式都有语言学意义，都来自你的直觉和想法，直接告诉审稿人，不要让审稿人去揣摩。

### 实验设计

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

1. 行文占满所有可用空间。有些审稿人看到留有大量空白会考虑：是不是实验不够充分、相关工作的讨论不够、参考文献是不是不足；
2. 文中图尽可能使用矢量图。
3. 图表中 Caption 应包含充分的信息。最好能直接看图，不用再去看正文。
4. 文中每个数学符号都应该找到定义；
5. 避免数学符号冲突，如果空间足够，使用符号列表；
6. 排版美观和专业。

## 做好不被承认的准备

在 AI 领域竞争如此激励下，论文投稿量极速增长。逢投必中的期望是美好的，但现实往往很骨感。论文被拒甚至多次被拒后，这种否定很容易让人陷入到自我怀疑和自我否定的泥潭中去。现身说法，最近中的A*文章被拒稿过三次，整整一年时间仿佛在泥潭中怎么挣扎，都无法爬出来。这个经历我学到的是：

1. 尽快调整心态，其实投顶会或者顶刊，接受率只有 15%-25%，被拒才是常态；
2. 分析审稿意见，把没有阐述清楚、引起误会或错误的地方修改掉；
3. 实验不充分的尽快加实验，比如常见的 baseline 不够新或者太少；
4. 时间不等人，写到极致，尽快改完投出去，等下一个结果；
5. 实在坚持不下去了，降低一级投稿。
6. 自我怀疑和否定时，能熬就自己熬过去，不行就找朋友说说话、聊聊天；相信只要论文写出来了，总有地方认可和接受的。

## Reference

- [1] Eamonn Keogh. How to do goode research, get it published in SIGKDD and get it cited.
- [2] 刘洋. 机器翻译学术论文写作方法和技巧。
