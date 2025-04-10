---
layout: post
title: 使用 Pandoc 将 LaTeX 转换为 Word
categories:

- LaTeX
tags:
- LaTeX2Word

---
## 1. 简介

在使用 LaTeX 编写的文档需要转换为 *.docx* 格式时，现有的 LaTeX2Word 软件大多收费。Pandoc 是一款免费且开源的文档格式转换工具，支持多种文本类型之间的转换，效果相当出色。本文将简要介绍使用 Pandoc 将 *.tex* 转换为 *.docx* 文件的流程以及常见问题的解决方法。本人仅在 Windows 操作系统下进行过测试，因此以下教程在其他操作系统上可能不适用。<!-- more -->

## 2. Pandoc 安装

Pandoc 的安装相对简单，可以在 [Pandoc 官网](https://pandoc.org/installing.html) 下载并安装最新版本，或者从 [GitHub Releases](https://github.com/jgm/pandoc/releases/) 下载所需版本。本文使用的版本为 pandoc-2.17.1.1。

## 3. 基本使用

在 Windows 下，打开命令提示符并导航至要转换的 *.tex* 文件目录。然后使用以下命令：

```sh
pandoc input.tex -o output.docx
```

即可将 input.tex 转换为 output.docx。`input` 是输入文件名（*.tex* 文件）；`output` 是输出文件名，注意必须是 *.docx* 后缀。转换后的 *.docx* 文件不必与 *.tex* 文件同名。

## 4. 进阶使用

基本命令的转换效果可能无法满足一些复杂情况，例如需要将用 LaTeX 写的学术论文转换为 Word。在进阶使用中，将介绍如何利用一些工具和命令配置以满足更多转换需求。

### 4.1 交叉引用

Pandoc 无法直接生成表格、公式或图片在正文中的交叉引用编号。`pandoc-crossref` 工具可以帮助 Pandoc 实现这一功能。对于 Windows 系统，需要从 [GitHub Repo](https://github.com/lierdakil/pandoc-crossref/releases) 下载 *pandoc-crossref-Windows.7z*，解压后将 *pandoc-crossref.exe* 粘贴到 Pandoc 的安装目录中。**注意：pandoc-crossref 的版本必须与 Pandoc 的版本匹配**。

使用以下配置命令启用交叉引用：

```sh
--filter pandoc-crossref
```

#### 4.1.1 公式编号

通过添加以下配置命令自动生成公式编号并对齐：

```sh
-M autoEqnLabels
-M tableEqns
```

#### 4.1.2 标注的编号

LaTeX 中表格和图片的 `\caption` 编号可通过以下配置命令自动转换：

```sh
-t docx+native_numbering
```

#### 4.1.3 章节编号

通过添加以下配置命令生成各章节编号：

```sh
--number-sections
```

### 4.2 参考文献

参考文献是论文写作中不可或缺的部分，自动从 *.tex* 生成参考文献可节省大量格式转换时间。通过添加配置命令生成参考文献：

```sh
--bibliography=reference.bib
```

#### 4.2.1 指定参考文献格式

有时需要生成特定格式的参考文献，例如 IEEE 或 Springer 格式。在 Pandoc 中，参考文献格式由 *.csl* 文件指定。可在 [Zotero Style Repository](https://www.zotero.org/styles) 下载所需 *.csl* 文件，如 springer-basic-note.csl。将下载的 *.csl* 文件放在 *.tex* 文件的同级目录下。使用命令指定格式：

```sh
--csl springer-basic-note.csl
```

此外，Pandoc 生成指定格式的参考文献需要使用执行器，许多教程推荐使用

```sh
--filter pandoc-citeproc
```

**但新版本的 Pandoc 已弃用此命令**，改为直接使用：

```sh
--citeproc
```

完整的生成指定格式的参考文献命令为：

```sh
--citeproc --csl springer-basic-note.csl
```

#### 4.2.2 自定义章节名称

上述命令仅能生成参考文献列表，无法生成参考文献的章节名。使用以下命令可自定义参考文献的章节名：

```sh
-M reference-section-title=Reference
```

其中 *Reference* 为自定义章节名。

## 5. 完整命令

学术论文中最常用的 *.tex* 文件到 *.docx* 的转换命令为：

```sh
pandoc input.tex --filter pandoc-crossref --citeproc --csl springer-basic-note.csl --bibliography=reference.bib -M reference-section-title=Reference -M autoEqnLabels -M tableEqns -t docx+native_numbering --number-sections -o output.docx
```

配置的详细解释请参阅第 4 节。使用上述命令转换 `Springer Lecture Notes in Computer Science (LNCS)` 的示例可以在 [Latex2wordExample](https://github.com/xhan97/Latex2WordExample) 中查看。

## 6. 踩过的坑

### 6.1 Pandoc 版本不符

虽然安装了与 Pandoc 版本匹配的 pandoc-crossref，但使用 pandoc-crossref 时出现版本不匹配的问题，错误如下：

```sh
WARNING: pandoc-crossref was compiled with pandoc 2.11.0.4 but is being run through 1.19.2.1. This is not supported. Strange things may (and likely will) happen silently.
pandoc-crossref: Error in $: Incompatible API versions: encoded with [1,17,0,4] but attempted to decode with [1,22].
```

使用

```sh
pandoc -v
```

发现使用的 Pandoc 版本与安装的版本不符。例如，安装的版本为 Pandoc-2.11.0.4，但实际使用的是 Pandoc-1.19.2.1。

解决方法：

1. Python 包管理器 Anaconda 提供了 Pandoc 的运行脚本，如果安装了 Anaconda 并设置了环境变量，系统可能会调用 Anaconda 中的 Pandoc 可执行文件，导致版本不符。**可以暂时将 ~\ProgramData\Anaconda3\Scripts 下的 pandoc.exe 移除**，转换完成后再放回。
2. 检查是否安装过旧版本的 Pandoc，如果是，将新旧版本全部卸载后重新安装新版本。

### 6.2 参考文献无法生成

在确保命令使用正确的情况下，从以下方面检查：

1. *reference.bib* 是否与要转换的 *.tex* 文件在同一目录下。
2. ``--filter pandoc-crossref`` 是否紧跟在 ``pandoc input.tex`` 后面。

### 6.3 *docx* 文件不更新

*.tex* 文件更新，但生成的 *.docx* 文件没有更新。

解决方法：

1. 更新 *tex* 文件后，需正确编译后才能使用 Pandoc 成功转换为 *.docx*。

## 7. 总结

本文介绍了使用开源软件 Pandoc 将 *.tex* 文件转换为 *.docx* 文件的方法。首先介绍了 Pandoc 的安装及基本使用，然后介绍了针对特定转换需求的配置和使用方法，最后给出了论文转换常用命令。最后总结了在使用和配置过程中遇到的问题及解决方法。
