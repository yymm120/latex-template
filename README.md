[TOC]


# Latex 环境
> 第一次配置vscode-latex环境花费了整整四个小时，我必须将它记录下来，避免以后重复踩坑。

## vscode settings

[latex-workshop-settings.json](settings.json)———参考配置。
> 注意：不应该用它替换你的`settings.json`文件，可以拷贝全部内容并追加到你的`settings.json`文件末尾。

通常不会只用vscode编辑latex，毕竟它不是专业的。作为编辑器，用它进行编码的确可以提高效率，尤其是配合vim插件，但是最终编码完成后仍然要使用TexStudio校对，它能提供更丰富的更稳定的与latex相关的功能，并且开箱即用，无需任何配置。


### Compile method

这些用于编译的命令工具通常不会手动执行，但了解它们的作用有助于检查错误。所以我按照自己的理解把它们列举下来，仅供参考：

| method            | description                | 
| ----------------- | -------------------------- | 
| xelatex           | 编译.tex文件的命令,支持Unicode，缺点是某些古老的宏不受支持 |
| xelatex*2         |                                           |
| pdftex            | ~~编译.tex文件, 支持最基本的宏集~~。不必关注 |
| pdflatex          | 编译.tex,支持更多宏(尤其是古老的宏),缺点是对unicode支持不好,需要添加CJK宏包来支持中文 |
| pdflatex*2        |                                                           |
| latexmk           | 编译.tex文件的命令 不需要切换编译工具就能完成交叉编译, 拥有监听文件修改的功能 |
| BibTex            |                           |
| Biber             | 它会编译bib文件，并生成bck文件，是biblatex的后端命令 |


关于编译的临时文件分别代表什么含义一般也无需关注。如果了解它有助于排错，这里提供一份别人的回答https://tex.stackexchange.com/a/424669

#### LatexMk 编译工具常用命令
```bash
# 对当前目录中的所有文件运行 LaTeX 。
latexmk

# 输出pdflatexmk -pdk
latexmk -pdf

# 用xelatex而不是pdflatex输出一个pdf
latexmk -pdfps

# 编译指定文件
latexmk myfile.tex

# 如果您想预览生成的输出文件，只需使用
latexmk -pv

# 持续watch所有输入文件是否有更改，重新编译并显示结果. (adobe pdf可能不支持)
latexmk -pvc
latexmk -pdf -pv myfile.tex

# 清理, 但保留pdf, ps, dvi文件
latexmk -k

# 清理, 删除.tex之外的所有文件
latexmk -C
```

创建.bat文件，填入以下内容，双击就能完成编译
```bat
latexmk
@pause
```
> 更多参考：ttps://mg.readthedocs.io/latexmk.html#running-latexmk-with-batch-files

### BibLatex 管理参考文献

首先需要知道BibLatex的使用方法：
```latex
\documentclass{article}
% step1: 引入biblatex宏，并使用默认格式
\usepackage[backend=biber]{biblatex}
% step2: 指定.bib文件的位置
\addbibresource{books.bib}

\begin{document}
% step2: 用在需要引用的地方
BibLatex\cite{knuth92}是管理文献的一个工具。

% ......CONTENT.......

% step4: 在\end{document}前列出前用使用了的参考文献, title指定标题。
\printbibliography[heading=bibintoc,title=参考文献]
\end{document}
```

当知道如何使用后，可以自定义参考文献的格式。可以参考以下注释，后面会给出常用的格式：
```latex
\usepackage[%
	%backend=biber,%用biber后端处理bib文件, 可选的有bibtex, bibtex8, biber, 默认为biber
	%样式文件(参考文献样式文件--bbx文件，引用样式文件--cbx)使用latex编写
	%一般可以下载提供的或标准的.bbx文件和.cbx文件，放在.tex同目录下进行引用
	%支持根据本地化排版，如：
	%	biber -l zh_pinyin texfile 按拼音排序
	%	biber -l zh_stroke texfile 按笔画排序
	%style= %引用格式和文献列表格式，有相对应的.bbx和.cbx文件
	%style=nature,%方括号数值压缩形式引用，文献列表title无引号，article类无前缀"In:", "and" 用 "&" 代替
	%style=science,%圆括号数值压缩形式引用，文献列表无and, title无引号, article类无前缀
	%style=numeric,%方括号数值引用，article类前缀"In:", title有引号，默认格式
	%style=numeric-comp,%方括号数值压缩形式引用，article类前缀"In:", title有引号
	style=gb7714-2015,%国标文献引用格式2015版, 胡振震制作
	%style=trad-abbrv,%方括号数值引用,作者名缩写
	%bibstyle=numeric,%文献列表形式：数值格式
	sorting=nyt,%文献列表排序：姓名(n)，年(y)，标题(t)升序，有nty, nyt, nyvt, anyt, anyvt, ynt, ydnt, none, debug, 自定义的<name>，其中ydnt是按年份降序，默认nty,
	%citestyle=numeric-comp,%引用文献形式：数值压缩形式,同时开启sortcites=true
	%sortcites=true,%引用时自动排序
	%giveninits=true,%缩写作者名,默认为false
	maxnames=3,%至多显示三个作者
	minnames=3,%至少显示三个作者
	%abbreviate=true,%缩写Editor之类，默认为true
	date=year,%只显示年份
	%url=true,%显示url,默认为true
	%doi=true,%显示doi,默认为true
	isbn=false,%不显示isbn/issn/isrn,默认为true
	%eprint=true,%对于arxiv文章有用,默认为true
	%subentry=false,%不再细分子列a,b之类,默认为false
	%hyperref=true,%使用超链接，需要配合hyperref宏包才能起作用,默认为auto,取决于是否加载hyperref宏包
	%backref=true,%反向引用，参考文献中列出引用所在的页码,需要在第四次再编译源文件,默认为false
	]{biblatex}%用biblatex处理参考文献
	%\renewbibmacro{in:}{\ifentrytype{article}{}{\printtext{\bibstring{in}\intitlepunct}}}%对于article类不显示"In:"
\usepackage[
	colorlinks,%彩色超链接
	linkcolor=blue,%蓝色定理定义交叉引用等链接
	citecolor=blue,%蓝色文献引用链接
	urlcolor=OliveGreen,%橄榄绿色网址链接，颜色需要用到xcolor宏包，用dvipsnames参数
	]{hyperref}%使用超链接
\usepackage[dvipsnames]{xcolor}%使用68种颜色
```

常用格式：
```latex
\usepackage[style=gb7714-2015,sorting=nyt,maxnames=3,minnames=3,date=year,isbn=false, backend=biber]{biblatex}
\usepackage[colorlinks,linkcolor=blue,citecolor=blue,urlcolor=OliveGreen]{hyperref}
\usepackage[dvipsnames]{xcolor}
```

## Latex

### 中文支持
```latex
\documentclass[a4paper, UTF8]{ctexart}
```

### 排版

#### 1. 字设置

#### 2. 行设置

- 换行：`\\` 表示换行, `\newline`与\\相同。
- 换行的同时偏移一段距离: `\\[offset]`
- 强制换行: `\linebreak`强制换行，与\newline的区别为\linebreak的当前行分散对齐。
- 缩进: 
    1. 设置首行缩进长度: `\setlength{\parindent}{长度}`，
    2. 缩进首行两个汉字距离: `\setlength{\parindent}{2em}`。
    3. 在CJK环境当中，可以在`\begin{CJK}{GBK}{song}`后面紧跟 `\CJKindent` 实现首行缩进。
    4. 默认第一段不首行缩进,如果想让第一段首行缩进,则可以使用 `\usepackage{indentfirst}`。
    5. 如果想让某一段不首行缩进，则可以在该段前加上 `\noindent`。
    6. 如果想让整篇文章都首行不缩进，则：`\setlength{\parindent}{0pt}`

> 注意：在 LaTeX 中，一个回车表示一个空格，两个回车表示一个分段。

#### 3. 段设置
分段: `两个回车` 表示分段, 或者`\par`表示分段。


#### 4. 页设置
- 分页: `\newpage`
(`\clearpage`：和 `\newpage` 类似。我们在使用 CJK 环境时会加入 `\clearpage` 在环境末尾。)

通常实践中，我们都是按照下面的模板进行扩充的：
```latex
\documentclass[12pt,a4paper]{article}
\usepackage{CJK}    %导入CJK宏包
\setlength{\parskip}{10pt}  % 设置部分段落间隔
\begin{document}
\begin{CJK*}{GBK}{song}  %开始CJK环境
\CJKtilde   %重定义"~"
\CJKindent  %设置首段缩进

This paper reflects on the history and development of the LaTeX Project Public License (LPPL) .\par %此处为分段

\noindent(此处不首行缩进)In late June, the TUG 2010 conference was held in San Francisco to great success. 

\clearpage
\end{CJK*}       %结束CJK环境
\end{document}

\setlength{\parskip}{10pt}：段落间隔是 \lineskip 和 \parskip 之和，这里设置 \parskip 的值是为了增加段落的间隔。

\CJKindent：在 CJK 环境中首行缩进。
```


## 模板

[template1](template1/The%20testfile%20for%20vscode.tex)

[template2](template2/text1.tex)

[template3](template3/abc.tex)

