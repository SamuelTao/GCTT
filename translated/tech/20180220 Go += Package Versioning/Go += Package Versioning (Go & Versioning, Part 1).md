
# Go += Package Versioning (Go & Versioning, Part 1)

我们需要添加包版本管理。

更确切地说，我们需要将包版本的概念潜移默化到 ```GO``` 开发者和开发工具中，这样当他们互相讨论究竟应该构建、运行或分析哪个程序时，它们都是精确的。 ```go``` 命令需要能够告诉开发人员究竟哪个版本包在特定的构建中，反之亦然。
    
版本控制将允许我们能够可重复构建，所以如果我告诉您尝试我最新版本的程序，我知道您将不仅能够获取到我最新版本的代码，而且包括该版本代码依赖的所有包，因此，您和我将构建完全相同的二进制文件(译者注：包库)。

版本控制也将让我们确保程序的构建方式每天都一样。即使有新版本的依赖项，```go``` 命令也不能开始使用，直到被请求为止。

尽管我们必须添加版本控制但是我们也不能移除目前 ``go`` 命令最棒的组成部分：简单性、高速性和可理解性。
现在许多程序员大多不注重版本控制，当然，这也并不影响工作。
如果我们得到了模型和默认值，我们可以通过添加版本控制的方式，让程序员依然不需要关注版本控制，但是工作得更好，更容易理解。
目前的工作流应尽可能少地更改。新版本的发布也应该更加简单。一般来说，版本管理工作必须淡出后台，而不是日常的关注。

简言之，我们需要添加包版本控制，但是我们需要在不破坏```go get```的前提下完成它。
这篇文章勾画了一个做这个的建议，以及一个原型演示，你也可以开始尝试，希望这将是最终的 ```GO``` 命令集成的基础。
我打算把这个帖子作为一个富有成效的讨论开始。基于这个讨论，我会对提案和原型进行调整，然后我会提交一个官方的建议，以整合到 ```Go 1.11``` 作为一个选择功能。

这一建议保持关于 ```go get``` 最好的部分，增加了重复建设，采用语义翻译，消除了 ```vendoring``` ，不提倡在一个项目的工作流支持 ```gopath``` ，并提供从 ```dep```和其前辈的平滑迁移。
也就是说，这项建议仍处于初期阶段。如果细节还不对的话，我们会花时间在 ```main Go``` 发布前修复它。

### 背景

在我们看这个建议之前，让我们看看我们今天是怎么做到的。这也许有点长，但历史对现在有重要的教训，有助于理解为什么提案改变了它的做法。如果您不耐烦，请随时跳过提案，或阅读附例博客文章。

####  Makefiles, goinstall, and go get

在2009年11月，最初版本的 ```GO``` 是由编译器、链接器和一些库组成的。你不得不运行 ```6g``` 和 ```6l``` 来编译和链接程序，并纳入样本文件。
在大多数情况下，一个最小 ```wrapper gobuild``` 可能建立一个单一的包和写一个文件。当时并没有建立一种方式去与他人共享代码。
我们知道还有很多地方需要去完善，但我们发布了我们已经实现的功能，并计划与社区一起努力完成剩下的工作。

在2010年2月，我们提出了```goinstall```，它是一个新的，像 ```bitbucket``` 和 ```GitHub``` 一样 拥有 从源控制库下载软件包的零配置命令。
在今天```Goinstall```作为一种约定被```Go```开发者理所当然的在导入路径时所引用。
因为当时并没有关于这些公约的代码，```goinstall``` 起初只用导入没有超出标准库的包。但是开发人员很快从各自不同的命名方案中迁移到我们今天所知道的统一惯例，
并将一系列已发布的包变成了一个连贯的生态系统。

Goinstall also eliminated makefiles and, with them, the complexity of user-defined build variations. While it is occasionally inconvenient to package authors not to be able to generate code during each build, that simplification has been incredibly important to package users: a user never has to worry about first installing the same set of tools as the package author used before building a package. The simplification has also been crucial to tooling. A makefile is an imperative, step-by-step recipe for compiling a package; reverse-engineering how to apply a different tool, like go vet or code completion, to the same package, can be quite difficult. Even getting build dependencies right, so that packages are rebuilt when necessary and only when necessary, is much harder with arbitrary makefiles. Although some people objected at the time that flexibility was being taken away, it is clear in retrospect that the benefits far outweighed the inconvenience.





----------------

via: https://research.swtch.com/vgo-intro

作者：[Russ Cox](https://swtch.com/~rsc/)
译者：[SamuelTao](https://github.com/SamuelTao)
校对：[校对者ID](https://github.com/校对者ID)

本文由 [GCTT](https://github.com/studygolang/GCTT) 原创编译，[Go 中文网](https://studygolang.com/) 荣誉推出