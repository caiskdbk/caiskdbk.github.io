---
layout: post
title: 来篇干货：使用VSCode当作stata编辑器
---

嗯，research厌烦症发作的我决定写一篇干货来躲避我需要做research这个事实。 所以今天来讲讲如何在VSCode里面集成stata。

首先来说一下啥是stata。 简单来说就是一种economics常用的统计软件，代码文件后缀是.do

然后来说说啥是VSCode。 VSCode全称是Visual Studio Code。从名字就可以看出来是微软的一个code editing 软件。 同类的软件还有比如notepad++ ，sublime text之类的。 支持的语言有很多，但是economics常用的stata，python， R， fortran都支持。 各种功能介绍请见 https://code.visualstudio.com/docs

Part 1： 为啥要用VSCode

Stata自带的编辑器基本功能都有，所以第一个问题就是为啥要用VSCode，我简单的把原因总结一下 （除非特别提到，一般这些功能是作用于所有语言的，不仅仅是stata）：

1. 自动补全 括号/引号

这个是个很实用的功能，比如你打”， VSCode会自动把剩下的那个”补全，而且会把光标停在两个引号之中。 所以这样首先不用怕哪次忘了补全括号或者引号，而且取决于你的输入习惯，可能还能省一次挪光标的操作（比如我的习惯是如果没有自动补全，我会先打两个引号，然后再把光标挪到两个引号中间).

2. syntax 补全/高亮

见图， syntax高亮这个很好解释， syntax补全就是比如你要打reg， 其实打r就会出现一个下来菜单可以选所有r开头的命令。

![_config.yml]({{ site.baseurl }}/images/image000006.png)

3. linter (for other programming languages)

如果你用VSCode编辑其他语言 （VSCode对语言的判定是根据文件后缀来的，所以如果你存一个.py的文件就会被判断为Python， 存一个.R的文件就会被判断成R这样），linter默认是打开的（默认支持python的linter，其他语言可能可以通过extension来实现）。 这个linter是干啥的呢？其实就是在不运行代码的前提下检查程序有没有出现简单的错误。比如说你新建一个python file，然后输入print (a)，这个时候VSCode会在下方output那里报错，告诉你undefined variable ‘a’ (见下图）

![_config.yml]({{ site.baseurl }}/images/image000007.png)

4. multiple selections

这个功能我用官网的两个gif来解释

![_config.yml]({{ site.baseurl }}/images/multicursor.gif)

![_config.yml]({{ site.baseurl }}/images/multicursor-word.gif)

实际上的用处差不多就是比如某个variable name你用了多次然后不知道为啥突然想改了，或者比如说你准备画很多张图，然后这些图都有一个common的参数，然后你现在想把这个common参数改了这个功能就能方便的帮你一次性改全部

5. 逼格高

嗯 黑色背景配上花花绿绿的代码，看上去逼格特别高。另外，其他编辑器（包括stata自带的）对于高分屏支持的不是特别好 （就是比如你的屏幕分辨率特别高 （e.g 2K/4K)，这时候这些编辑器的按钮，菜单，基本上所有东西都显示的特别小，或者看上去感觉怪怪的）。 VSCode没这毛病。

Part 2： 如何在VSCode里面集成Stata

首先，VSCode默认是不支持Stata这个语言的，所以比如syntax highlighting， syntax auto completion啥的是没有的。同时，VSCode作为一个code editor，本身也是不能直接run do file的. 所以我们来一步步解决。

首先安装完VSCode之后 运行VScode， 先点击file，在下拉菜单里面勾选auto save，这样每次运行程序之前VSCode会自动保存代码。 然后去extension （最左边第四个按钮）那里搜stata enhanced， 安装

装好了之后就有有syntax highlighting， syntax auto completion，另外装了之后如果你打`会自动补全’ （嗯，是的，我上了博一之后发现，stata里面的前后引号不是同一个） 具体的feature见下图

![_config.yml]({{ site.baseurl }}/images/image000008.png)

 

然后来搞定最重要的一个，如何用VSCode跑Stata

首先按照这个instruction做 https://huebler.blogspot.ca/2008/04/stata.html

这个人基本上写了个程序可以让任何第三方编辑器调用stata，他的第三步用notepad++做例子，所以前两步按照他说的做，第三步看下面，这里我假设你按照他的建议，把两个文件放到了C:\ado\personal\下面

第三步，首先打开VSCode，在extension里面搜索code runner。 这个extension可以让你在VSCode里面快速运行各种语言，但是默认的语言支持里面并没有stata。 所以我们需要改一下preference。 首先按下control+comma调出user settings。 嗯VSCode这点不是特别user friendly，他的settings全部都是存在json里面的，但无所谓 别被吓到，跟着我做就行。在右侧的窗口（左侧是default settings，右侧是user settings）里面添加这么几行 （见图）

![_config.yml]({{ site.baseurl }}/images/image000009.png)
“code-runner.executorMapByFileExtension”: {
“.do”: “C:\\ado\\personal\\rundo.exe”
},
“code-runner.customCommand”: “C:\\ado\\personal\\rundolines.exe”,
如果第一个”code-runner.executorMapByFileExtension” 不是蓝色的，那就在他的上一行最后补一个逗号. 如果你下载下来的文件存在了别的地方把C:\\ado\\personal\\改成你存的路径就行，不过注意要用 \\ 而不是\
“code-runner.executorMapByFileExtension”: {
“.do”: “C:\\ado\\personal\\rundo.exe”
},

这个是说增加一个基于文件后缀的executor： 对于所有文件后缀是.do的文件，用C:\\ado\\personal\\rundo.exe来跑。

“code-runner.customCommand”: “C:\\ado\\personal\\rundolines.exe”

这个是说增加一个自定义命令，运行C:\\ado\\personal\\rundolines.exe

然后保存你的settings。这个时候你还可以去file->preference->keyboard shortcuts里面去把run code和run custom command的快捷键改了 （默认是ctrl+alt+N 和ctrl+alt+K， 我下面按照默认快捷键来说）为了确保所有东西没问题，建议这个时候关了VSCode，再重开。

 

用法：

新建一个空白文件，保存, 确认文件后缀是.do （ 你可以去保存文件那个下拉菜单里面选stata enhanced，也可以直接写比如test.do）或者打开一个你已经写好的.do文件

这个时候正常写你的code，你应该发现什么syntax highlighting 啥的都已经有效果了，然后如果你要跑整个do file，就按ctrl+alt+N （或者屏幕右上角的那个播放按钮），如果你只想跑某几行，用鼠标选中，然后按ctrl+alt+K

另外，rundo.exe有个bug （不是VSCode的锅，因为貌似notepad++下面也有这个毛病）就是会忽略文件的最后一行。所以记得在文件最后留至少一行空的就行。

另外，还有一种用法，就是只在user settings 里面添加”code-runner.customCommand”: “C:\\ado\\personal\\rundolines.exe”， 然后你每次想运行整个do file的时候，先ctrl+A全选所有code，然后ctrl+alt+K运行。两个文件的作者推荐这种用法，但是我个人更喜欢把rundolines 和rundo分开用，因为这样跑整个do file的时候省一次快捷键。