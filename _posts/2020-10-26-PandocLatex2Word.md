---
layout: post
title: 使用 Pandoc 将 Latex 转化为 Word 简易教程
categories: 
  - Latex
tags:
  - Latex2Word
---
## 0. 简介
使用 Latex 编写的文档在一些情况下需要转化成 *.docx* 格式，现存的 Latex2Word 软件大多需要收取一定费用才能实现转化。
而 Pandoc 是一款免费开源的文档格式转化工具，支持众多的支持类型之间的转化，再加上其优秀的转化效果，使得其受到广泛的应用。本文将简单介绍使用 Pandoc 将 *.tex* 转化成 *.docx* 文件的流程及注意事项。因为本人只在 Windows 操作系统下进行过测试，
因此以下教程不一定适用其他操作系统。<!-- more -->
## 1. Pandoc 安装
Pandoc 安装比较简单，可以在 [Pandoc 官网](https://pandoc.org/installing.html) 下载和安装最新版本，
或者在 [Github Releases](https://github.com/jgm/pandoc/releases/) 下载需要的版本。本文中使用 pandoc-2.11.0.4 版本。

## 2. 基本使用
在Windows 下打开 DOS 命令窗口并进入到要转化的 *.tex* 文件的目录。之后使用如下命令：

``pandoc input.tex -o output.docx``

将 input.tex 转化成 output.docx。
input 是输入的文件名，*.tex* 文件； 
output 是输出的文件名，必须是 *.docx* 后缀。可以注意到要转化的 *.docx* 文件不需要和 *.tex* 文件同名。

## 3. 交叉引用
Pandoc 不能直接生成表格，公式或者图片在正文的交叉引用编号。可以使用 pandoc-crossref 这个工具达到这个目的。
对于 Windows 系统，
需要在 [GitHub Repo](https://github.com/lierdakil/pandoc-crossref/releases) 发布页面上下载预构建好的 *.exe* 文件。然后将这个可执行文件放在 Pandoc 的安装目录中。**注意： pandoc-crossref 的版本必须与 pandoc 的版本匹配**
并使用如下命令：

``--filter pandoc-crossref ``

## 4. 参考文献
参考文献是论文必不可少的部分，自动从 *.tex* 生成参考文献可节约大量格式转化的时间。
可以通过添加如下命令生成参考文献。

``--bibliography=reference.bib``

### 4.1. 文献格式
生成参考文献时，一些情况下需要根据要求生成指定格式的参考文献，例如需要指定为 IEEE 或 Springer 的格式。
在 Pandoc 中，参考文献的格式可以通过特定的 *.csl* 文件指定，
在 [Zotero Style Repository](https://www.zotero.org/styles) 下载到所需的 *.csl* 文件，如springer-basic-note.csl，并放置到与 *.tex* 文件同级目录下。使用如下命令指定格式：

``--csl springer-basic-note.csl ``

此外，生成指定的参考文献格式需要执行器，网络上很多教程推荐使用
``--filter pandoc-citeproc``
这个命令。**但新版本的 Pandoc 已经弃用了这个命令**，而改为直接使用：

``--citeproc`` 

因此完整版指定格式的参考文献生成命令为：

``--citeproc --csl springer-basic-note.csl``

### 4.2. 章节名称
上面命名只能生成参考文献列表，但不能生成参考文献的章节名。可用如下命令自定义参考文献的章节名：

``-M reference-section-title=Reference``

其中 *Reference* 可替换成任何自定义的章节名。
## 5. 完整命令
总结上述内容，得到最常用 *.tex* 文件转 *.docx* 命令为:

``pandoc input.tex  --filter pandoc-crossref --citeproc --csl springer-basic-note.csl  --bibliography=reference.bib -M reference-section-title=Reference -o output.docx``

## 6. 踩过的坑

### 6.1. 版本不符合预期
   
明明安装了最新的 Pandoc 版本如 Pandoc-2.11.0.4, 但发现 Pandoc 版本不正确，例如 Pandoc-1.19.2.1。从而导致使用预期匹配版本的 pandoc-crossref 报错。可以使用 ``pandoc -v`` 查看版本号。

解决办法：
  1. Python 包管理器 Anaconda 中提供了 Pandoc 的运行脚本，如果安装了 Anaconda 并设置了环境变量，系统很大可能会调用Anaconda 中的 Pandoc 可执行程序，从而导致运行版本与预期不符。**这时可以暂时把 ~\ProgramData\Anaconda3\Scripts 下的 pandoc.exe 移除**，转化完之后再重新放回。
  2.  检查是否安装过旧版本的 *Pandoc* ，如果是，将新旧版本全部卸载后重新安装新版。
   
### 6.2. 参考文献无法生成

在确保命令使用正确下从以下方面进行检查：

   1. *reference.bib* 是否和要编译的 *.tex* 文件在同一目录下。
   2. ``--filter pandoc-crossref`` 需紧跟在 ``pandoc input.tex`` 后面。 

###  6.3. *docx* 文件不更新
*.tex* 文件更新，但生成的*.docx* 文件并没有更新。
   解决方法：
   1. *tex* 文件更新后，需要正确编译后才能使用 Pandoc 成功转化为 *.docx*.

