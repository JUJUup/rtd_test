---
CJKmainfont: PingFang SC
---
# 关于远程服务器的coding

## Changelog

NOTE： pdf version 已经在repo中给出(See README.pdf)。请按需使用~

221116_update: 更新了一些可能会有用的command. See useful_cmd.md in repo.

230111_update: 发现一个可能比较有用的网站：[The Missing Semester of Your CS Education](https://missing-semester-cn.github.io)

230704_update: e-science中心的姚哥在it侠沙龙上分享了学校高性能计算的架构，感觉可能会有些帮助。bilibili录播: [ITalk 技术沙龙 录播230415【南京大学IT侠互助协会】_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1mT411H7DQ/?spm_id_from=333.1007.top_right_bar_window_default_collection.content.click&vd_source=54c4d3b08c304762d66fe212fa812829)

## History of Unix/Open Source/Linux

什么是Unix？什么是Linux？如果你的奶奶问起这个问题，这里有一个最简短的回答：Unix是一种类型的计算机操作系统的集合，而Linux是Unix这类系统下重要的一个分支。

在具体讨论我们该如何在学校服务器上的Linux操作系统中进行工作前，我觉得一点历史介绍是非常有益的。限于篇幅，下面仅会介绍笔者觉得重要的Unix发展节点。如果你想知道更多，一本非常有趣的书可以提供参考：《**Harley Hann‘s Guide to Unix and Linux**》。简中版为《**Unix&Linux大学教程**》（奇怪的中文译名……我更愿意称它为《Harley Hann's炉边漫谈Unix&Linux》）。同时，网络上还有相当多的优质Unix发展历史评述，感兴趣的可以善用搜索引擎，但我觉得Harley Hann的书已经提供了足够的内容。

你可以在下面的超链接中找到英文版的pdf。中文版可以去Zlibrary/Libgen等平台自行搜索。

[Harley Hann‘s Guide to Unix and Linux](https://box.nju.edu.cn/f/9f60d1d731924da69c68/)

我们开始吧。

### Unix：它的诞生

在1969年，第一个Unix系统诞生在AT&T公司的贝尔实验室，开发者是一名名为**Ken Thompson**的程序员。该程序员开发Unix系统的目的是为了能够流畅运行Space Travel游戏。贝尔实验室的人们在70年代早期又对Unix重新进行了编写和增强，并在1974年7月由Thompson and Dennis Ritchie撰写并在ACM上发表了一篇划时代的Paper："The UNIX Time-Sharing System"。这是Unix第一次公开走向世界。

除了贝尔实验室外，加州大学伯克利分校也是Unix开发的重要核心阵地。在1974年代表了Unix初登场的论文发表的同年，UC Berkeley的一名教授Bob Fabry获得了一份AT&T公司的UNIX副本，Berkeley的师生们开始对这一系统进行研究和改进。1975年，Thompson前往Berkeley进行一年的交流访问，充当了Berkeley进行Unix开发的催化剂。

70年代的Unix发展史中，不得不提到的人是Berkeley的一名研究生**Bill Joy**。尽管已经过去了接近五十年，Joy所编写的程序依然在忠实地为我们服务。他有两个最重要的作品：vi编辑器（1976年），cshell（1978年），这两个软件几乎存在于现在所有的Linux发行版中。尽管vi作为一个以命令行和键盘输入为导向的非常原始的文本编辑器，在现在已经有了诸多更好的替代品（我会在下文进行介绍），但其至今依然有活跃的用户社区（用vi/vim的都是大神），并且其作为完全内生于各类Linux发行版的编辑器，在不熟悉的开发环境进行轻度的文本编辑时依然能够完美胜任；cshell则是一个被广泛使用的shell语言（如果不了解shell是什么的话，可以简单的把shell看作是一个个命令行command的集合，用于对操作系统的内核下达指令/接收信息），lili group所使用的driver file（用于控制model/DA system等）几乎全部由cshell编写。

221126补充：cshell似乎是被许多coder讨厌的，具体可以看看下面：[cshell中的换行符问题](https://unix.stackexchange.com/questions/682831/tcsh-scripting-keep-newlines-of-grep-results) and [Csh Programming Considered Harmful](https://www-uxsup.csx.cam.ac.uk/misc/csh.html)。当然，科研编程也可以不考虑这么多，能跑就行()

1977年，Joy发行了Berkeley大学的第一版的Unix，这也是Unix一个重要发行版本**BSD**（Berkeley Software Distribution）的初号机。到此为止，所有的Unix都是免费的，大家为Unix的进步慷慨而无私地共享自己的知识。

1979年，AT&T公司认识到了UNIX的商业潜能，宣布他们准备将UNIX作为一个产品来进行销售。所有Unix，以及BSD的用户都被要求购买AT&T公司的许可证，并且不再允许除AT&T公司外的人查看UNIX的源代码，哪怕是出于教学目的并购买了许可证。UCB的师生们，乃至所有Unix程序员们对此都非常不满，而解决方法就是重新编写BSD中基于AT&T公司UNIX的那一部分代码，重新构建属于所有人的Unix。这是一个崇高而艰巨的目标。在艰难的重构UNIX过程中，一个在网络世界有巨大影响力的运动开始形成，现在大家耳熟能详的Github就建立在这场运动所达成的共识上：**开放源代码**。

### 自由软件运动：GNU宣言

让软件能够免费且自由地被复制，能够让世界变得更好吗？**Richard Stallman**，一个（曾经）工作于MIT人工智能实验室的程序员，以及一名well-educated的社会批评家，想必是对此持积极态度的。在1981年人工智能实验室的软件及操作系统不再对他们开放查看及修改的权力后，Stallman离开了MIT。他认为，这样的系统会在程序员和软件公司之间创建不健康的权力斗争。而这种问题的解决方法就是从头开始创建属于所有人的，自由发行的开放源代码的软件。

1985年，Stallman牵头创立了一个名为**自由软件基金会（Free Software Fundation, FSF）**的组织。他们试图开发一个能够兼容Unix的类Unix系统：GNU，并发表了一篇阐释FSF建立的初衷与思想理论基础的文章，**GNU宣言**。许多大家耳熟能详的东西（比如说Bash，以及gfortran），实际上都是FSF的作品。

关于GNU宣言，这篇文章的一些想法在笔者看来与共产主义宣言异曲同工。下面摘录部分1985年GNU宣言的中文翻译版。如果对完整的GNU宣言感兴趣，请点击这里查看：[GNU宣言中文版链接](https://www.gnu.org/gnu/manifesto.zh-cn.html)

!!! info
    我认可的黄金法则是如果我喜欢一个程序，我就必须把它分享给喜欢它的人。软件销售商通过让每个用户保证不和其他人分享来分化用户并控制他们。我拒绝以这种方式打破和其他用户组成的统一体。我的良知让我无法签署这样的保密协议或软件许可证协议。几年来，我在人工智能实验室都在反抗这种趋势以及其他冷漠，但是最终他们还是走得太远了：我无法再呆在一个为我做违背我意愿之事的机构。

    为了能够继续不失颜面地使用计算机，我决定把一些必要的自由软件集合在一起，这样我就能够继续下去而不需要任何非自由软件。我从人工智能实验室辞了职，这样就可以在我发布GNU时避免和MIT产生法律纠葛
    
    ……
    
    许多程序员对系统软件的商业化并不高兴。这可能使他们赚到更多的钱，不过这一般要求他们和其他程序员之间是对立关系，而不是伙伴关系。程序员之间的友谊的基本方式是分享程序；而现在典型的市场活动基本上是禁止程序员互相成为朋友。软件买家必须在友谊和守法之间抉择。自然地，许多人认为友谊更重要。但是许多守法的人通常会感到选哪个都不自在。他们变得愤世嫉俗并且认为编程只是一个挣钱的手段。
    
    ……
    
    长远来看，让软件自由是通往富足世界的一小步；在富足世界里，人们不必辛苦工作来谋生。人们在每周10小时的法律活动、家庭咨询、机器人维修和流星观察等规定任务之外，能够自由投入到象编程这样的有趣活动中。那时，就没有必要再以编程为谋生手段了。
    
    我们已经把整个社会要维持生产力的工作大大减少了，但是只有很少一部分转化为劳动者的闲暇，因为生产活动需要夹杂很多的非生产活动。其主要原因是官僚主义和对竞争的抗拒。自由软件会大大减少在软件生产领域的生产力流失。我们必须做这件事，为了使技术进步带来的生产力提高能够转化为人们工作的减少。
    
    ……

### 作为开源操作系统诞生的Linux

在Unix的发展历史中，开放源代码运动的影响有深远的历史意义。如果没有这项运动，以及其对编程文化进行的改革，我们或许永远也不会听说Linux。

整个1980年代的Unix发展史类似三国混战，Berkeley以及其他大学的师生们一直在致力于创建一个完全独立的BSD版Unix，Stallman他们也在做着类似的努力。但Berkely以及FSF都没能解决Unix的内核问题，而AT&T公司则在一个又一个地更新UNIX的版本，并且收费越来越贵……直到1992年，我们的友校赫尔辛基大学的一名本科学生Linus Torvalds横空出世，带着Linux结束了Unix的混战局面。

Linus在1991年开始进行Linux项目（当时他还是一位大二的学生），而在同年9月，第一版Linux内核就已经发布。Linus本人的热情和编程天赋毋庸置疑，FSF的开源运动所创造的团结而和谐的程序员社区，以及初生的Internet在Linux诞生的过程中也产生了巨大的作用。正是因为Linus将软件自由的发行给大量的爱好者，使得成千上万的程序员通过Internet志愿参与进了Linux的调试及改进，最终让Linux飞速发展和完善，比历史上任何一个主要的软件项目都要快。

严格来说，Linus他们创造的是能够替代AT&T公司的UNIX的Linux内核（Kernel），而一个Kernel还需要Shell以及其他必要Utilities的才能称为一个操作系统。因此，基于Linux内核的众多操作系统发行版就出现了（比如说Debian，Red Hat，Ubuntn等），学校的超算中心所使用的是Red Hat，组里的服务器则使用CentOS。不过不管它叫什么，剥开外壳里面都是Linux，使用起来也几乎没有差别。

## 在Linux服务器上的操作

!!! warning
    在不确定自己的命令会造成什么后果之前，请不要轻举妄动，你在与所有人分享同一个服务器。尤其是不要随便修改\~/.bashrc（你可以把它当成是开机自启动的一堆命令，一般用于设置环境变量）。如果你需要定制自己的bashrc，你可以在别处建立相关file，然后在\~/.bashrc里设定自己的alias以source它。

### 适应命令行

不同于大家常用的Windows/MacOS，服务器上的Linux通常不会给你图形界面来对文件进行操作，你需要学会使用命令行。相比于用鼠标操作图形界面，命令行是更有效率，更加直观，更有拓展性的操作方式。在熟练之后，你可以非常方便地使用通配符，正则表达式，tunnel（Unix的特色）[有兴趣的话可以看看这个](https://prithu.dev/posts/unix-pipeline/)，以及shell语言（如果想的话python/matlab也可以）等来对文件进行批量化，自动化的操作。组里常用的对于模式后处理/nc文件的工具NCO（开源工具，NASA资助；EC那边也有相似的命令行工具CDO）也是以命令行的形式进行调用。简而言之，适应命令行是迈入使用服务器的第一步。补充一个nature图一乐板块的文章：[Five reasons why researchers should learn to love the command line](https://www.nature.com/articles/d41586-021-00263-0)

!!! info
    “It’s so powerful, and I feel like such a **cool nerd** when I’m using it”.

如果你还不熟悉Linux系统的基本操作，笔者非常建议首先通篇浏览[Harley Hann‘s Guide to Unix and Linux](https://box.nju.edu.cn/f/9f60d1d731924da69c68/) ，并善用Linux自带的man命令随时打开相关手册进行查阅。

### 服务器上的Python

众所周知，与Linux一样，python是**开源的编程语言**（也就是说不用担心像北航等军工校的Matlab一样被制裁而不能使用），无数的聪明才智自愿地为python的发展添砖加瓦，同时也使python具有可以说最为活跃的社区环境。尤其是在英语世界，几乎所有的与python相关的问题丢到google里都能找到答案。开源、活跃的社区，这也是笔者选择python的主要理由。

如果读者已经有了interpreter，编程环境管理等概念，或者说，只要知道python不是像matlab一样刚上来就可以即开即用的集成度非常高的编程工具，那么在服务器上使用python与在本地PC上是几乎无差别的，只是如果你使用vi等文本编辑工具的话（笔者非常不推荐这样做），需要适应在服务器上的文本编辑方式。如果你使用VSCode等比较时髦一点的工具（后面将会介绍这一工具），连这一点差别都可以被填平，你将make full use of VSCode，获得与在本地PC上码代码一致的畅快体验。

#### 如何在服务器上搭建自己的Python环境

我的建议是使用Conda进行Python环境管理。

关于如何使用conda，请自行参阅[Anaconda Documentation — Anaconda documentation](https://docs.anaconda.com/) 。这里仅给出一点使用建议：

1. 建议创建单独的conda虚拟环境(eg. conda create haoxing)，然后在虚拟环境里进行python库管理，不要一股脑都在默认的base环境中管理python。在需要用哪个python环境进行编译时，再使用conda activate * 启动该虚拟环境即可。
2. 建议给conda的默认下载渠道添加南大源。学校服务器与外界断网，不通过南大镜像无法常规下载相关的库，这一点或许在服务器上刚使用conda时会带来一些困扰。关于南大镜像的使用，请参照：[关于本站 — NJU Mirror Help 文档 (njuer.org)](https://nju-mirror-help.njuer.org/about.html)

……

#### 一些python科研代码的建议

python的基础教程在Internet上多如牛毛，并且各有其优势。在这方面，笔者唯一的建议是不要在基础的练习上浪费过多时间。为你的科研需要进行coding并对基础内容查漏补缺是最好的练习。

如果读者已经比较熟悉python的一些基础，可以看看[Python for Atmosphere and Ocean Scientists (carpentries-lab.github.io)](https://carpentries-lab.github.io/python-aos-lesson/) 这个小教程用比较简短的篇幅告诉你大气科学python编程的一些routine。

下面给出一些笔者自己对python科研代码一些建议：

1. 尽量使用xarray，而不是netcdf库来进行nc文件的操作。尽管netcdf库更加直观也更加底层，xarray也是基于netcdf开发，但越底层的package并不一定越高级越好用。xarray简化了许多netcdf的操作逻辑，更容易实现一些fancy的操作，并且也是许多其他大气科学常用库的依赖项，比如说NCAR的wrf-python（据说几乎实现了NCL的所有功能，不过笔者并不经常使用它），如果能理解xarray，也就能更好地理解应用这些库时的编程思路。当然最好两个都要熟悉。
2. 尝试与已有的工具进行合作。python是所谓的“胶水语言”，对于上古遗留下来的fortran文件，或者NCO等命令行工具都有比较方便的接口。
3. 整理归纳自己的代码。许多基础性的代码并不需要每次都重新码。如果有兴趣，试试根据自己的科研需要创建自己的python库。笔者正在进行这方面的尝试。
4. 学会翻墙。中文互联网关于python科研编程的内容只能说还方兴未艾。如果你遇到的问题baidu无法给出答案，请试试google。如果不会翻墙或者没有找到合适稳定的梯子，可以联系我。如果大家有用着觉得非常顺手的梯子，或许我们可以创一个组里共用的账号？
5. 尝试使用github。简单来说，github就是一个给大家保存并分享自己的开源项目的地方，许多的python库都会在github上管理自己的源代码，还有一些科研人员会在github上管理自己的项目代码，以及lili group也试图通过github共享大家的知识，参见[lili group userguide](https://github.com/lilisgroup/UserGuide)。这些资源都能为你编写自己的python code提供有价值的参考。此外，github还有诸多其他的用处，比如说交友以及distribute静态个人网站……在此就不多赘述了。

### 服务器上的Matlab/NCL/Others

笔者对此并不熟悉……欢迎大家提issue进行补充。但下面会给出一个笔者认为这些编程语言都有帮助的文档编辑工具。

### 服务器上的文档编辑

Linux服务器上的操作可以大致分成三类：文件操作；文档编辑；进程管理。文件操作、进程管理的内容请参阅Harley's Guide。由于Harley Guide成书时间比较早，而服务器上的文档编辑在近些年有了突飞猛进的发展，因此在这里笔者想对此进行一些补充说明。

#### Vi/Emacs：完全基于键盘的编辑方式

相信使用过服务器的大家对于vi/vim命令绝对不陌生。作为几乎所有Linux发行版自带的文档编辑工具，vi几乎成了所有初次接触Linux系统时必须学习的基础工具，笔者知道甚至有同学认为Linux服务器上的文档只能通过vi进行编辑。

尽管vi在Joy先生（在Unix历史简介部分中提到过他）编写出它的时代可谓是最先进最方便的文档编辑工具，并且直到现在仍然在进行更新迭代，但需要毫不客气地指出，vi，以及emacs（由Stallman编写），这些早期的文档编辑工具基本的操作逻辑中是不包含鼠标这一交互方式的，他们认为所有的交互靠键盘就能解决。这在当时很好理解，因为键盘的出现和发展远远早于鼠标。但硬软件技术发展至今，对于依然坚持只靠键盘是最好的人机交互方式这一观点，笔者持保留意见。或许这看起来很Cool很Geek，但对于提高编辑效率毫无帮助。笔者曾经尝试过在服务器上使用vim进行coding，最终因为vim陡峭的学习曲线而放弃。

不得不承认的是，作为几乎所有Linux系统自带的编辑工具，在进行快速的文档查看以及非常轻度的文档编辑时，vi是最优选择。但当你需要进行繁重的coding时，最好选择别的工具。

#### Jupyter/Pycharm/Matlab：现代的文档编辑方法

有过Python使用经验的读者大概都知道Jupyter Notebook或者Pycharm，前者是基于web的交互式编程工具（并且开源），后者则是PC上使用度极高的python IDE（集成开发环境）。他们都具有比vi/Emacs更为现代的交互逻辑，包括但远不止于能够用鼠标进行交互，高亮显示以及语法检查。同时，Jupyter也能够在服务器上提供与本地编辑一致的coding体验，具体方案可以参考anaconda的官方文档：[Running Jupyter Notebook on a remote server — Anaconda documentation](https://docs.anaconda.com/anaconda/user-guide/tasks/remote-jupyter-notebook/)或者[这篇公众号推文](https://mp.weixin.qq.com/s/1nC6U0nx3b1Npjfmpg7XEg)。Pycharm专业版同样提供了连接服务器的功能，但需要付费（学校没买……免费的社区版并不具备这项功能）。在很长一段时间里，Jupyter notebook是笔者在服务器上进行python coding的主要工具。

如果你是matlab党，并且能够忍受服务器上matlab图形界面令人难受的锯齿分辨率和延迟，那么当然可以完全不考虑这些，直接matlab *.m，同样也能像本地matlab一样进行编辑……但笔者觉得上述的缺点已经使得服务器上用matlab自带的GUI编程非常的令人难受。这也是我在本科尝试在服务器上使用matlab后迅速转向python的原因之一。如果matlab党有其他的coding方案，非常欢迎提issue告知。

尽管Jupyter开源，有丰富的社区资源和extension，还支持python/R等多种语言，但就读者所知，jupyter并不能支持fortran，cshell以及matlab，尤其是不能支持cshell（以及其他的shell语言），这使得jupyter并不能成为一站式的服务器文档编辑方案。使用Pycharm需要money，并且显然只支持python；Matlab自带的GUI则连自己的本职工作都难以做好。是否有一个工具能够一站式地解决所有服务器上的文档编辑呢？

#### VSCode：一站式解决方案

VSCode作为微软开发的开源、轻量的文档编辑器，或许有不少人早已听闻其大名（请注意，不要将VSCode与Visual Studio混为一谈），但它很长时间都只是一个仅能在本地使用的编辑器。直到2019年5月（半年后，Corona就在武汉爆发），VSCode推出了微软官方开发的VSCode remote extension，这个extension使得VSCode能够连接远程服务器，在远程服务器上使用几乎所有VSCode在本地的功能，比如高亮选择、自动缩进、代码补全等等，并且能够通过extension支持几乎所有的语言，包括fortran，matlab，shell，甚至jupyter notebook文件。All you need is在VSCode terminal中输入code *file，即可进行文档编辑。或许VSCode remote也同Corona一起促进了远程办公的发展。

关于如何在远程服务器上使用VSCode……仲睿补充了一些细致操作，详见[仲睿的maching-learning intro]([lilisgroup/Intro-to-machine-learning (github.com)](https://github.com/lilisgroup/Intro-to-machine-learning#使用visual-studio-code在远程服务器上工作))

总之，无论读者是python党，matlab党，甚至是fortran/NCL党，如果已经受够了vi这样老旧的文档编辑工具，都可以尝试使用VSCode进行文档编辑。希望以上信息能够提升大家在服务器上的操作体验。不过无论什么工具，其终点都是为人服务，如果现在使用的工具已经让自己感到舒服，那其实并没有非要换掉它的理由。

最后，Happy coding on Linux~~~





