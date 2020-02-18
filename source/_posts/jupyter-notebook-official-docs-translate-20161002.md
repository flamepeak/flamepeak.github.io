---
title: Jupyter Notebook官方文档 翻译
date: 2016-10-02 12:09:25
tags: [Python, Jupyter]
categories: [Python]
---
官方文档：http://jupyter-notebook.readthedocs.io/en/latest/notebook.html
笔者注:
官方文档共分为以下几个大的部分，将尽力翻译，各部分将以红色横线分割。
+ 用户文档（**翻译完成**）
+ 配置(**翻译中**)
+ 贡献者(Contributor)文档
+ 社区(Comunity)文档
+ 关于Jupyter Notebook
+ 问题与建议



# 用户文档(USER DOCUMENTATION)
本部分包含5个内容：
+ Jupyter Notebook
+ 安装
+ 运行Notebook
+ 从IPython中迁移
+ 用户界面构成

## Jupyter Notebook
### 介绍
notebook在一个全新的方向上拓展了基于控制台的方法到交互式计算，提供了一种基于web的应用来记录整个计算过程：开发、编辑、执行代码，同时交互式显示结果。Jupyter notebook 结合了两个部分：
**web应用程序**：一个基于浏览器的交互式文档创作工具，这种交互式文档集说明性文字、数学、计算和富媒体于一体。
**Notebook 文档**: web应用程序中的所有可见内容的表现形式，包括计算的输入输出、说明性文字、数学、图像以及富媒体对象的表示。

#### web应用程序的主要特点
+ 浏览器内部编辑代码，包括自动语法高亮、缩进、tab补全和内省。
+ 从浏览器执行代码的能力，计算的结果附加在生成他们的代码后面。
+ 使用富媒体表示方法显示计算结果，比如HTML，LaTex，PNG，SVG等。例如，[matplotlib](http://matplotlib.org/)出版级质量的图片可以包含在行内。
+ 浏览器内部编辑富文本使用markdown标记语言，可以给代码添加注释，不仅仅是纯文本。
+ 很容易的将使用LaTex的数学符号包含在markdown cell内，或则本地使用[MathJax](http://www.mathjax.org/)生成。

#### Notebook 文档
Notebook 文档包含一个交互式绘画的输入和输出，以及代码的不可执行的附加文本。这样，notebook文件可以作为一个会话完整计算的记录，将说明性文字、数学公式、计算结果的富媒体表示与可执行代码交织在一起。这些文件实质是[JSON](http://en.wikipedia.org/wiki/JSON)文件并以`.ipynb`扩展名保存。由于JSON是一种纯文本格式，因此他们可以受版本控制并与同事分享。

Notebook可以导出为一系列静态格式(static formats)，包括HTML(例如，用于博客文章）、 reStructuredText、LaTeX、PDF和silde shows, 通过[nbconvert](http://nbconvert.readthedocs.org/en/latest/)命令实现。

此外，任何公开网址可用的`.ipynb`文件可以通过Jupyter Notebook Viewer([nbviewer](http://nbviewer.jupyter.org/))共享。nbviewer从URL加载notebook文件，将其呈现为一个静态的web页面。我们可以把这个web页面与同事分享或者作为公共博客的文章，而其它用户不需要安装Jupyter notebook。实际上，[nbviewer](http://nbviewer.jupyter.org/)只是[nbconvert](http://nbconvert.readthedocs.org/en/latest/)的网络服务版本，因此你可以自己使用nbconvert进行静态转换，而不是依赖nbviewer。

### 启动notebook服务器
你可以在命令行中使用以下命令运行notebook服务器：

    jupyter notebook
运行后，控制台将会显示一些关于notebook服务器的信息，同时打开浏览器访问web应用程序的URL(默认地址：`http://127.0.0.1:8888`).

Jupyter notebook web应用程序的登录页，即主面板(dashboard)，显示notebook目录(默认情况下，notebook目录是notebook服务器启动的目录)中当前可用的notebook文件。

你可以使用主面板中`New Notebook`按钮创建新notebook文件，或者单击打开已有的文件。你还可以拖动`.ipynb`文件和Python源码文件`.py`到notebook文件区域。

当你从命令后打开notebook服务器时，你同样可以直接打开特定的notebook文件，而不是通过主面板，使用命令：`jupyter notebook my_notebook.ipynb` .如果没有指定文件扩展名，则会假定扩展名为`.ipynb`.

当你在一个打开的notebook文件里时，使用*File/Open...*菜单选项将会在新选项卡中打开主面板。这样你可以从notebook目录中打开另一notebook文件，或者创建一个新notebook文件。

> 注意：
你可以同时打开多个notebook服务器，如果你想在不同的目录操作notebook文件。默认情况下，第一个服务器使用8888端口，之后启动的服务器自动寻找距离8888最近的端口。你同样可以使用`--port`选项手动指定端口。

#### 创建新的notebook文档
可以在任何时候创建新的notebook文件，从主面板，或者在一个打开的notebook文件中使用*File/New*菜单选项。新notebook文件被放在同一个目录，并在新的浏览器选项卡中打开。在主面板notebook文件列表中可以看到新建的文件。


#### 打开notebooks
一个打开的notebook仅有一个交互式会话连接到[IPython内核](http://ipython.org/ipython-doc/dev/overview.html#ipythonzmq),此内核将会执行用户代码并返回结果。如果web浏览器窗口关闭，内核依然保持活动状态；当你从主面板重新打开同一个notebook时，web应用程序将会再次连接到同一个内核。在主面板，有活动内核的notebook文件旁边有一个`Shutdown`按钮，而没有活动内核的notebook文件旁边是`Delete`按钮。

其它客户端可能连接到相同的底层(underlying)IPython内核。notebook 服务器总是在终端显示如何连接到每一个内核的全部细节，显示如下：

    [NotebookApp] Kernel started: 87f7d2c0-13e3-43df-8bb8-1bd37aaf3373

这个长字符串就是内核的ID，此ID是连接到内核所需要的信息。你同样可以通过运行魔术函数`%connect_info`来获取连接信息。`%connect_info`将会输出相同的ID以及JSON数据结构的内容。
![connect_info_magic.jpg](/sourcepictures/20161002/connect_info_magic.jpg)

然后，你可以手动启动Qt控制台连接到同一个内核，通过传入ID的一部分：

    $ ipython qtconsole --existing 87f7d2c0

如果不输入ID，`--existing`将会连接到最近启动的内核。这也可以通过在notebook中运行魔术函数`%qtconsole`来实现。

### Notebook用户界面
当你创建一个新的notebook文件时，你将会看到notebook名称，菜单栏，工具栏和一个空的代码单元(code cell).
**notebook名称**：notebook文件名称显示在页面的最上方，紧邻`IP[y]: Notebook`标志。这个名字反映`.ipynb`notebook文件的名字。在notebook名称上单击会弹出对话框，你可以对其进行重命名操作。
例如，重命名文件`Untitled0.ipynb`为`My first notebook.ipynb`.
![new-notebook.gif](/sourcepictures/20161002/new-notebook.gif)

**菜单栏**：菜单栏提供了多个选项，可以用来修改notebook的功能。

**工具栏**：工具栏提供了一种快速使用notebook常用操作的方式，单击工具栏图标即可。

**代码单元**：单元(cell)的默认类型，read on for an explanation of cells

> 在notebook 4.1版本，用户界面允许同时选择多个单元。当多个单元被选择，菜单栏中`quick celltype selector`将会显示一个横线`-`，指示选区中的单元的类型可能不一致。`quick celltype selector`可以被用来改变选区的类型，将会改变所有选中单元的类型。

### notebook文件的结构
notebook由一系列的单元组成。单元是一个多行的文本输入区域，它的内容可以使用`Shift+Enter`来执行，或者单击工具栏`Play`按钮，或使用菜单栏*Cell/Run*运行。单元如何执行是由单元的类型决定的。共有四种类型的单元：**代码单元(code cells)**,**markdown单元(markdown cells)**,**原始单元(raw cells)**,**标题单元(heading cells)**.每一个单元开始都是代码单元，但它的类型可以通过工具栏下拉框(初始值为`Code`)进行修改，也可以使用[快捷键](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html#keyboard-shortcuts)进行修改。

更多notebook可以实现的功能，请参考[例子](http://nbviewer.jupyter.org/github/jupyter/notebook/tree/master/docs/source/examples/Notebook/).

#### 代码单元(Code cells)
你可以在代码单元里写入或编辑代码，代码单元有语法高亮和自动补全功能。默认情况下，代码单元默认的语言是Python，但是其它语言，例如`Julia`和`R`，可以使用[单元魔术命令](http://ipython.org/ipython-doc/dev/interactive/tutorial.html#magics-explained)来处理。

当一个代码单元被执行时，代码被发送到和notebook连接的内核。计算返回的结果显示在notebook中作为单元的输出(output).输出不仅限于文字，其它很多可能格式都是可行的，包括`matplotlib`图像和HTML表格(例如`pandas`数据分析包使用的表格)。这就是著名的IPython富媒体显示能力。

> 参见：
notebook：[富媒体输出(Rich Output)](https://nbviewer.jupyter.org/urls/raw.github.com/ipython/ipython/3.x/examples/IPython%20Kernel/Rich%20Output.ipynb)举例

#### Markdown单元(Markdown cells)
你可以采用方便阅读的方式记录计算过程，交替使用描述性文字(使用富文本)和代码. 在IPython中，这通过Markdown语言标记文本来说完成。相应的单元被称为Markdown单元。Markdown语言提供了一种简单的方式实现文本标记，即指定哪一部分文字应当被强调(斜体)、加粗、构成列表等。

当Markdown单元被执行，Markdown代码被转换成相应的富文本(rich text)。Markdown允许使用任意的HTML代码编辑格式。

在Markdown单元内部，你可以直接包含数学符号：行内数学符号使用标准的LateX标记:`$...$`,行间数学符号使用`$$...$$`标记。当Markdown单元被执行时，LaTeX部分自动生成高质量排版的方程式。[MathJax](http://www.mathjax.org/)使这成为可能，它支持LaTeX功能中的很大一部分。

LaTeX和AMS-LaTeX定义的标准公式环境同样可以使用，比如`\begin{equation}...\end{equation}`和`\begin{align}...\end{align}`。新的LaTeX宏可以使用标准的方法来定义，比如`\newcommand`，把它们放置在Markdown单元里数学分隔符之间的任何位置。这些定义在此后的整个IPython会话中可用。

> 参见：
notebook：[Markdown单元](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html#markdown-cells)举例

#### 原始单元(Raw cells)
原始单元提供了一个可以直接写输出(output)的地方。notebook不对原始单元进行计算或求值。当通过nbconvert转换为目的格式时，原始单元没有被修改。例如，你可以在原始单元键入LaTeX数据，这些数据只有在被nbconvert转换后才可以被LaTeX生成。

#### 标题单元(Heading cells)
如果你想对文件进行结构化处理，你可以使用markdown标题。Markdown标题由1到6个`#`号和跟在后面的一个空格、段落标题组成。markdown标题将会被转换成notebook中一个可以点击的链接。它同样被用来作为一种提示，当我们转换成其他文件格式时，例如PDF。我们建议在一个单元中只使用一个markdown标题，同时限制单元的内容与标题相关。为了灵活的转换格式，我们建议把其余的文本放置在下一个notebook单元。

### 基本工作流程(Basic workflow)
notebook正常的工作流程和标准IPython会话的工作流程很相似，不同的是，在这里你可以多次编辑单元直到获得想要的结果，而不是使用魔术命令`%run`重新运行单独的脚本。

通常情况下，将一个计算问题分成多个部分，组织相关的内容到单元中，当前一单元正确执行后继续处理后续单元。处理交互式研究比把计算过程分割成多个脚本一起执行更方便，因为之前结果是必须的，特别如果是某些部分需要很长时间运行。

有时候，可能需要中止一个需要很长时间才能完成的计算。这可以通过菜单栏*Kernel/Interrupt*选项，或者`Ctrl-m i`快捷键。同样的，你可能需要或者想重启整个计算过程，可以通过*Kernel/Restart*，或者快捷键`Ctrl-m`.

notebook文件可以通过菜单栏*File/Download as*下载为`.ipynb`或`.py`格式。当选择`.py`格式下载为Python脚本时，notebook中富文本输出被删除，同时markdown单元的内容作为注释插入到脚本中。

> 参见：
notebook：[在Jupyter Notebook中运行代码](https://nbviewer.jupyter.org/urls/raw.github.com/ipython/ipython/3.x/examples/Notebook/Running%20Code.ipynb)举例
notebook：[Notebook基础](https://nbviewer.jupyter.org/urls/raw.github.com/ipython/ipython/3.x/examples/Notebook/Notebook%20Basics.ipynb)

#### 快捷键(Keyboard shortcuts)
notebook所有操作都可以同时鼠标完成，但是对于常用操作也可以使用快捷键。基本的快捷键如下：
+ `Shift-Enter`：运行单元(run cell)
执行当前的单元，显示输出(如果有的话)，然后跳到下一个单元。如果`Shift-Enter`在最后一个单元被使用，一个新的代码单元将被创建。注意，在notebook里使用`Enter`键永远不会执行，而只是在当前单元插入一个新行。`Shift-Enter`等同于在菜单栏使用*Cell/Run*.

+ `Ctrl-Enter`：运行单元，并保持位置(run cell in-place)
执行当前单元就像在“终端模式”一样，并显示结果，但是光标仍然在当前单元中。执行完毕后整个单元的内容都被选中，因此你可以开始输入而且只有新输入的数据保留在单元中。这个功能对于做快速试验、或者查询文件系统内容很方便，而不用创建额外的不想保存在notebook中的单元

+ `Alt-Enter`：运行单元，并在下方插入新单元(run cell, insert below)
执行当前单元，显示输出，并在下方插入一个新的单元。这相当于运行两个快捷键`Shift-Enter`和 `Ctrl-m a`。(`Ctrl-m a` 在当前单元上方增加新单元)

+ `Esc`和`Enter`：命令模式和编辑模式(Command mode and edit mode)
在命令模式，你可以使用快捷键浏览整个notebook。在编辑模式，你可以编辑单元中的文本。

查看所有可用的快捷键，你可以通过单击菜单栏*Help/Keyboard Shortcuts*。

### 绘图(Plotting)
Jupyter notebook一个主要特色是能够显示代码单元输出的图表。为了提供这个功能，IPython内核被设计成与matplotlib绘图库无缝结合。内核集成专门的绘图库是一大特色。

### 安装内核(Installing kernels)
如何安装Python内核，请参考[IPython安装页面](http://ipython.org/install.html).
其它编程语言的内核可以在[IPython wiki](https://github.com/ipython/ipython/wiki/IPython%20kernels%20for%20other%20languages)中找到。在这里你可以找到如何在notebook中安装使用内核。

### Notebooks 签名(Signing Notebooks)
为了用户的安全，我们给notebook增加了签名，防止不被信任代码的在notebook打开时执行，签名信息存储在元数据(metadata)中。notebook服务器在notebook文件打开时校验签名信息。如果存储在元数据中的签名信息不一致，在文件载入时javascript和HTML输出将不会被显示，你必须再次执行代码单元来重新生成签名信息。

任何你已经完全执行过的notebook将被认为是可信的，在文件载入时javascript和HTML输出将会显示出来。

如果你想查看HTML或Javascript输出，而不重新执行代码单元，你可以明确指定信任此notebook(例如别人分享给你的notebook、或者你自己使用IPython2.0以前版本生成的notebook),通过使用命令行：

    $ jupyter trust mynotebook.ipynb [other notebooks.ipynb]

这将会生成一个新的签名存储在notebook中。
你可以生成一个新的签名密钥(signing key),通过：

    $ jupyter trust --reset

### 浏览器兼容性
Jupyter Notebook官方支持以下浏览器的最近稳定版本：
+ Chrome
+ Safari
+ Firefox

主要原因是notebook使用WebSockets和弹性盒子模型(flexible box model).
下列浏览器是不支持的：
+ Safari < 5
+ Firefox < 6
+ Chrome < 13
+ Opera (所有): CSS问题, 但是执行可能成功
+ Internet Explorer < 10
+ Internet Explorer ≥ 10 (same as Opera)

使用Safari的HTTPS和不信任的证书是不能正常使用的(websockets 会失败).

---



## 安装(Installation)
### 必备：Python
尽管Jupyter可以运行很多的编程语言代码，**Python**是安装Jupyter Noterbook的必备条件（Python3.3以上，或Python2.7）。

### 使用Anaconda和conda安装Jupyter
对于新用户，我们强烈建议安装[Anaconda](https://www.continuum.io/downloads). Anaconda可以很方便的安装Python, Jupyter Notebook以及其它科学计算与数据科学常用的包。
使用下面的安装步骤：
1. 下载Anaconda。我们建议下载Anaconda的最新Python 3的版本。
2. 安装你下载的Anaconda。
3. 恭喜你，你已经安装好了Jupyter Notebook。运行jupyter notebook:
`jupyter notebook`

### 针对有经验的Python用户：使用pip安装Jupyter
>重要提示：Jupyter安装需要Python3.3以上，或者Python2.7. IPython 1.x是支持Python3.2和2.6的最后版本。

作为一个Python用户，你可能想使用Python包管理器pip安装Jupyter,而不是Anaconda。
首先，确保你已经安装了最新版本的pip，旧版本的可能会遇到问题。
`pip3 install --upgrade pip`
然后，安装Jupyter Notebook：
`pip3 install jupyter`
(Use `pip` if using legacy Python 2.) 注：legacy不知怎么翻，“已停产的”？

### 可选的：安装Kernels
接下来，介绍在更高的层次在不同的编程语言中使用Jupyter Notebook。
### 是否有语言是预装的：
是的。安装Jupyter Notebook将会安装[IPython](https://ipython.readthedocs.io/en/latest/) [kernel](http://jupyter.readthedocs.io/en/latest/glossary.html#term-kernel)。这使得我们在notebooks 上使用python编程语言。
### 如何同时安装Python 2 和Python 3 ?
如果要安装其它版本的Python，比如同时使用Python 2和Python 3，请参考IPython文档[安装kernel](https://ipython.readthedocs.io/en/latest/install/kernel_install.html)
### 如何安装其它编程语言，比如R或Julia ?
如要在除Python外的其它语言中使用notebooks, 比如R或者Julia，你必须安装额外的内核。更多信息请参考[可用内核](https://github.com/ipython/ipython/wiki/IPython-kernels-for-other-languages)

---



## 运行Notebook(Running the Notebook)
**内容**
+ 基本步骤
+ 启动Notebook Server
+ 介绍Notebook Server 的命令后选项
  + 如何使用自定义的IP和端口启动Notebook？
  + 如何在不打开浏览器情况下使用Notebook server？
  + 如何获取Notebook server 选项的帮助信息？

### 基本步骤
1. 从命令行启动notebook服务器：
`jupyter notebook`
2. 你应当可以看到notebook开启了你的浏览器。

### 启动Notebook Server
在你安装Jupyter Notebook之后，你已经做好运行notebook服务器的准备。你可以从命令后启动notebook服务器:
`jupyter notebook`
运行命令之后，命令行将会显示一些关于notebook服务器的信息，包括网络应用程序的`URL`地址(默认值：`http://localhost:8888`):
```Python
$ jupyter notebook
[I 08:58:24.417 NotebookApp] Serving notebooks from local directory: /Users/catherine
[I 08:58:24.417 NotebookApp] 0 active kernels
[I 08:58:24.417 NotebookApp] The Jupyter Notebook is running at: http://localhost:8888/
[I 08:58:24.417 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
```
然后使用默认的浏览器打开`URL`地址：

浏览器打开notebook之后，你可以看到Notebook面板，Notebook面板将会显示notebook服务器启动目录中的notebook、文件、子目录列表。
**Notebook 面板**
![Notebook Dashboard](/sourcepictures/20160912/notebook_dashboard.png)

### 介绍Notebook Server 的命令后选项
#### 如何使用自定义的IP和端口启动Notebook？
默认情况下，notebook服务器使用`8888`端口。如果`8888`端口不可用或者被占用，notebook服务器将会寻找下一个可用端口。同样，你可以手动指定一个端口。在下面的例子里，我们设定服务器端口为`9999`：
`jupyter notebook --port 9999`
#### 如何在不打开浏览器情况下使用Notebook server？
启动notebook服务器而不打开浏览器，使用以下命令:
`jupyter notebook --no-browser`
#### 如何获取Notebook server 选项的帮助信息？
notebook服务器提供命令行参数的帮助信息，使用`--help`:
`jupyter notebook --help`

---




## 从IPython中迁移(Migrating from IPython)
**内容**
+ 概述
+ 了解迁移过程
  + 自动迁移文件
  + 我的配置文件去哪了？
+ 发现重要文件的位置
  + 配置文件(Configuration files)
  + 数据文件：kernelspecs和notebook扩展
+ 由于Jupyter没有配置文件(profiles)，我应当如何进行自定义设置？
  + 更改Jupyter notebook配置目录
  + 更改Jupyter notebook配置文件
  + 更改kernelspecs
+ 了解安装的变化
  + Notebook 扩展
  + 内核
+ 了解imports的变化

### 概述
[大分割计划(The Big Split)](https://blog.jupyter.org/2015/04/15/the-big-split/)将IPython中各种编程语言无关( language-agnostic)的组件移动到Jupyter之下。将来，Jupyter将会包含与编程语言无关的项目,这些项目支持(serve)多种编程语言。IPython将继续专注于Python和它在Jupyter上的使用。
这篇文章介绍当你从IPython 3迁移到Jupyter时，有哪些变化，以及在需要的时候如何修改代码或配置。

### 了解迁移过程
#### 自动迁移文件
当你第一次运行任何`jupyter`命令时，Jupyter将会执行一次文件自动迁移。自动迁移进程复制文件，而不是移动文件，确保原始文件不变，复制的文件到Jupyter文件目录中。如果需要的话，你可以使用命令`jupyter migrate`再次运行迁移进程.你的自定义配置将会自动迁移，在Jupyter中无需编辑即可使用。将来当你升级或者修改配置文件时，请留意配置文件的位置可能发生了变化。
#### 我的配置文件去哪了？
也被称为“为什么我的配置不起任何作用了？”
Jupyter 从IPython中分离出来，意味着一些文件的位置发生了变化，Jupyter 项目没有把以前IPython的功能全部继承下来。
当你第一次启动Jupyter程序时，相关的配置文件自动拷贝到它们在Jupyter中的新位置。在IPython中的原始配置文件不影响Jupyter的执行。如果你意外编辑了原始的IPython配置文件，那么在Jupyter中你将不会看到期望的效果。你应当检查你正在编辑Jupyter的配置文件，同时你应当重启Jupyter服务器才能看到你期望的效果。

### 发现重要文件的位置
#### 配置文件(Configuration files)
配置文件根据用户的爱好自定义Jupyter。被迁移的文件应当已经被自动拷贝到它们在Jupyter的新位置。下面是文件位置的变化：

|IPython location|Jupyter location|
|------|------|
|`~/.ipython/profile_default/static/custom`|`~/.jupyter/custom`|
|`~/.ipython/profile_default/ipython_notebook_config.py`|`~/.jupyter/jupyter_notebook_config.py`|
|`~/.ipython/profile_default/ipython_nbconvert_config.py`|`~/.jupyter/jupyter_nbconvert_config.py`|
|`~/.ipython/profile_default/ipython_qtconsole_config.py`|`~/.jupyter/jupyter_qtconsole_config.py`|
|`~/.ipython/profile_default/ipython_console_config.py`|`~/.jupyter/jupyter_console_config.py`|

如果想使用一个目录位置而不是默认的`~/.jupyter`，设置`JUPYTER_CONFIG_DIR`环境变量。在设置环境变量之后，你可能需要运行`jupyter migrate`来拷贝文件到目的目录中。

#### 数据文件：kernelspecs和notebook扩展名
数据文件(Data Files)包括用户安装的文件，除了配置文件。例如包括kernelspecs和notebook扩展。和配置文件一样，数据文件同样自动迁移到新的Jupyter目录中。
在IPython 3，数据文件在`~/.ipython`中
在Jupyter，数据文件在各平台适当的位置：
  + OS X: `~/Library/Jupyter`
  + Windows: 位置在环境变量`%APPDATA%`中指定
  + 其它，通常是`$XDG_DATA_HOME`,默认路径`~/.local/share/jupyter`
在以上所有平台，环境变量`JUPYTER_DATA_DIR`可以用来设置明确的位置。
系统层面（system-wide）安装的数据文件没有变化(e.g.`/usr/local/share/jupyter`)。每个用户(Per-user)安装的数据文件位置从`.ipython`变换到各平台适当的位置。

### 由于Jupyter没有配置文件(profiles)，我应当如何进行自定义设置？
尽管IPython有配置文件(profiles)的概念，但是Jupyter没有配置文件(profiles)。
在IPython，配置文件是配置和运行文件的集合。在IPython目录(`~/.ipython`)中，有很多像`profile_default`和`profile_demo`名字的目录。在这些目录里是配置文件(configuration file)(`ipython_config.py, ipython_notebook_config.py`)和运行文件(`history.sqlite, security/kernel-*.json`).配置文件(profiles)可以用来改变IPython的配置。
之前，我们可以使用命令`ipython notebook --profile demo`同时对notebook server和IPython kernel进行配置。但是，在Jupyter中不能同时对其进行配置，就像在IPython 3中不能对其他内核的同时配置。

#### 更改Jupyter notebook配置目录
如果你想改变Jupyter notebook的配置，你可以设置`JUPYTER_CONFIG_DIR`:
```Python
JUPYTER_CONFIG_DIR=./jupyter_config
jupyter notebook
```

#### 更改Jupyter notebook配置文件
如果你只是想改变配置文件，你可以：
```Python
jupyter notebook --config=/path/to/myconfig.py
```

#### 更改kernelspecs
如果你确实想改变IPython的内核文件，你不能通过在服务器命令行实现了。内核参数改变必须通过修改kernelspec来实现。你不用重启服务器就可以做到这一点。Kernelspec的改变在你开启一个新kenel时生效。然而，没有修改Kernelspec的好办法。一个办法是使用`jupyter kernelspec list`找到`kernel.json`文件文件，然后修改它，例如手动修改`kenels/python3/kernel.json`. 另外，[a2km](https://github.com/minrk/a2km)是一个努力使修改内核描述变得更容易的实验性项目。

### 了解安装的变化
#### Notebook 扩展
所有IPython notebook 扩展都被自动迁移到Jupyter中。
IPython Notebook 安装扩展使用：
```Python
ipython install-nbextension [--user] EXTENSION
```
现在，在Jupyter中，安装扩展使用：
```Python
jupyter nbextension install [--user] EXTENSION
```
notebook 扩展将会被安装到系统级的位置(例如：`/usr/local/share/jupyter/nbextensions`)。如果你使用`--user`安装，notebook扩展将会安装到`JUPYTER_DATA_DIR`。手动安装不能通过猜测文件应该安装到哪里来完成。(Installation SHOULD NOT be done manually by guessing where the files should go.)
#### 内核
内核的安装采用和notebook相同的方式。他们将会被自动迁移。
内核描述过去通过以下命令安装：
```Python
ipython kernelspec install [--user] KERNEL
```
现在，通过以下命令安装：
```Python
jupyter kernelspec install [--user] KERNEL
```
默认情况下，内核描述将会安装到一个系统级位置(例如:`/usr/local/jupyter/kernels`)。如果你使用`--user`安装，notebook扩展将会安装到`JUPYTER_DATA_DIR`。手动安装不能通过猜测文件应该安装到哪里来完成。(Installation SHOULD NOT be done manually by guessing where the files should go.)

### 了解imports的变化
IPython 4.0 包含shim来管理依赖(dependency)，所有在IPython 3中可用的导入（import）在IPython 4中继续可用。
一些变化包括：

|**IPython 3**|**Jupyter and IPython 4.0**|
|------|------|
|`IPython.html`|`notebook`|
|`IPython.html.widgets`|`ipywidgets`|
|`IPython.kernel`|`jupyter_client, ipykernel`|
|`IPython.parallel`|`ipyparallel`|
|`IPython.qt.console`|`qtconsole`|
|`IPython.utils.traitlets`|`traitlets`|
|`IPython.config`|`traitlets.config`|

重要变化:
IPython.kernel 变成两个包：
+ jupyter_client: Jupyter 客户端API
+ ipykernel: Jupyter的IPython内核

---

## 用户界面构成(UI Components)
**目录：**
+ Notebook 主面板(Notebook Dashboard)
+ Notebook 编辑器(Notebook Editor)
+ 交互式用户界面介绍(Interactive User Interface Tour of the Notebook)
  + 编辑模式和Notebook编辑器(Edit Mode and Notebook Editor)
+ 文件编辑器(File Editor)

当你打开问题报告(bug reports)或者给Jupyter邮件列表发送邮件时，了解用户界面各部分的名字是很有帮助的，可以使开发者和用户更快速的判断你的问题。这部分内容将帮你熟悉用户界面各部分的名字和Notebook不同的模式。

### Notebook 主面板(Notebook Dashboard)
当你运行`jupyter notebook`后，你看到的第一个页面就是Notebook主面板。
![jupyter-notebook-dashboard.png](/sourcepictures/20161002/jupyter-notebook-dashboard.png)

### Notebook 编辑器(Notebook Editor)
当你选中一个Notebook开始编辑，这个Notebook将会在Notebook编辑器中打开。
![jupyter-notebook-default.png](/sourcepictures/20161002/jupyter-notebook-default.png)

### 交互式用户界面介绍(Interactive User Interface Tour of the Notebook)
如果你想了解Notebook编辑器中某些部件，你可以使用菜单栏*Help/User Interface Tour*选项来浏览。

#### 编辑模式和Notebook编辑器(Edit Mode and Notebook Editor)
当一个单元处于编辑模式(edit mode)时，单元模式指示器(Cell Mode Indictor)将会发生改变来反映单元的状态。单元的状态通过界面右上方的小铅笔图标来指示。当单元处于命令模式(command mode)时，那个位置是没有图标的。
![jupyter-notebook-edit.png](/sourcepictures/20161002/jupyter-notebook-edit.png)

### 文件编辑器(File Editor)
假设现在你在Notebook主面板选择打开Markdown文件，而不是Notebook文件；这样的话，这个文件将会在文件编辑器中打开。
![jupyter-file-editor.png](/sourcepictures/20161002/jupyter-file-editor.png)



<HR style="border:5 double #987cb9" width="100%" color=red SIZE=3>

# 配置(CONFIGURATION)
目录
+ 配置概述
+ 配置文件和命令行选项
+ 运行notebook服务器
+ Jupyter notebook 安全
+ 配置notebook前端(frontend)
+ Jupyter 扩展作为Python包发布(Distributing Jupyter Extensions as Python Packages)
+ 扩展Jupyter Notebook(Extending the Notebook)


## 配置概述
除了默认的配置设置，你可以配置很多的选项来满足你的工作流程。下面是使用Jupyter Notebook常用的配置：
+ Jupyter 通用配置系统
+ Notebook服务器
+ Notebook 前端客户端(Notebook front-end client)
+ Notebook 扩展(Notebook extensions)

### Jupyter 通用配置系统
Jupyter 应用程序，从Notebook到Jupyter Hub，以及nbgrader，使用同一个通用配置系统。所有的Jupyter应用程序创建配置文件和改变设置的过程都是相似的。
+ [Jupyter通用配置方法](https://jupyter.readthedocs.io/en/latest/projects/config.html)
+ [常用目录和文件位置](https://jupyter.readthedocs.io/en/latest/projects/jupyter-directories.html)
+ [编程语言内核(Language kernels)](https://jupyter.readthedocs.io/en/latest/projects/kernels.html)
+ [traitlets](https://traitlets.readthedocs.org/en/latest/config.html#module-traitlets.config):为配置提供一个低层次(low-level)的架构

### Notebook服务器
Notebook服务器运行编程语言内核，同时与前端Notebook客户端进行通信。
+ 配置Notebook服务器
使用下面的命令在`.jupyter`目录创建`jupyter_notebook_config.py`文件，所有默认配置都被注释掉了：
`jupyter notebook --generate-config`

+ [运行Notebook服务器](http://jupyter-notebook.readthedocs.io/en/latest/public_server.html#working-remotely)

+ 相关：[配置编程语言内核](http://jupyter.readthedocs.org/en/latest/install.html#installing-kernels)使你的Notebook服务器可以运行其它编程语言，例如R或Julia。
