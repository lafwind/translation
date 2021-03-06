source : <https://realworldocaml.org/>

# 序言

## 为什么要选OCaml？
你使用的编程语言会影响你写的代码。它们会影响你软件的可靠性、安全性、性能、可读性及重构和扩展等。你所熟知的语言也会影响你思考编程和设计软件时的思维方式。

编程语言不都是平等的。40年来，一些语言特性浮现出来，一起成为语言设计的“甜点”。这些特性包括：
- 用于自动内存管理的垃圾收集，现在几乎所有现代高级语言都有。
- 像在JavaScript和C#中一样，函数作为一等公民可以像普通的值一样传递。
- 静态类型检查，可以提升性能并减少一些运行时错误，就像在Java和C#那样。
- 参数多态，允许构建可以在不同数据类型上工作的抽象，类似Java和C#中的泛型还有C++中的模板。
- 更好地支持不可变编程，如，编程时不对数据结构做破坏性更新。这在像Scheme那样的传统函数式编程语言中已经存在，且在Hadoop这样的大数据框架中也可以见到。
- 自动类型推导，可以不用辛苦地为程序中的每一个单独变量都定义类型，而是基于一个值的使用推导出其类型。在C#中有隐式类型的局部变量还有C++11中的`auto`关键字都是这个特性的受限形式。
- 代数数据类型和模式匹配，用以定义和操作复杂数据类型。Scala和F#支持。

> 代数数据类型（Algebraic data types），是一种组合类型，如元组和记录等。

你们中一些人了解并喜欢所有这些特性，而对其它人而言它们可能是全新的，但大多数人都是在他们使用的语言中或多或少见过一些。贯穿本书，我们都会展示，要使这些特性在同一种语言中并存且能相互作用需要一些变形。尽管如此重要，这些概念只是有限地进入了主流语言中，并且即使是在主流语言中，如C#中的高阶函数或Java中的参数多态，通常都是受限和笨拙的形式。完整包含这些概念的只有静态函数式编程语言，像OCaml、F#、Haskell、Scala和Standard ML。

在这组优秀的语言中，OCaml脱颖而出，因为它在提供强大特性的同时保持了高度的实用性。其编译器有一种直接的编译策略，不需要过度优化和或复杂的动态JIT编译就能产生高性能代码。这一点，加上严格的计算模型，使得运行时行为容易预测。垃圾收集器是增量的，这意味着可以避免GC引起大的暂停；也是精确的，就是说会收集所有没有引用的数据（不像引用计数收集器）；运行时简单且高度可移植。

所有这些都使得OCaml成为对编程语言有追求的程序员的绝佳选择，同时也可以完成实际的工作。
	
### 简史
OCaml是法国INRIA的Xavier Leroy，Jérôme Vouillon，Damien Doligez和Didier Rémy在1996年写的。灵感来自1960年代以来对ML的大量研究，并且持续和学院社区保持紧密联系。

ML最开始是Robin Milner（当时在斯坦福，后来到了剑桥）1972年发布的LCF证明辅助系统的元语言。ML后来变成了一个编译器以使得在不同机器上使用LCF更容易，并在1980年代逐渐转变成一个完备的独立系统。

第一个Caml实现出现在1987年，最开始由Ascander Saurez创建，后来由Pierre Weis和Michel Mauny继续。1990年，Xavier Leroy和Damien Doligez创建一个新的实现叫Caml Light，Caml Light基于一个拥有快速垃圾收集器的字节码解释器。之后的几年出现了一些有用的库，如Michel Mauny的语法结构处理工具等，这些都促进了Caml在教育和研究领域的使用。

Xavier Leroy继续扩展Caml Light的新特性，这就有了1995年发布的Caml Special Light。加入了一个快速的原生机器码编译器来大幅度提高程序性能，从而使Caml的性能可以和C++这样的主流语言相抗衡。从Standard ML借鉴来的模块系统给Caml提供了强大的抽象工具，也使得构建大型程序更容易。

随着idier Rémy和Jérôme Vouillon实现了一个强大、优雅的类型系统，现代OCaml于1996年出现。这个类型系统的亮点是可以以静态类型安全的方式支持许多通用的OO惯用法，而在其它语言（如C++和Java）中则需要运行时检查。2000年，Jacques Garrigue向OCaml添加了一些新特性，如多态方法、变体（variant）、标签参数和可选参数。

过去10年，OCaml吸引了大量用户，语言发展趋于稳健以支持日益增长的商业和学术代码库。作为一等公民的模块、广义代数数据类型（Generalized Algebraic Data Types，GADT）以及动态链接提高了语言的灵活性，另外快速的本地代码支持x86_64、ARM、PowerPC和Sparc，这使得在同时关注资源使用、可预测性和性能的系统中，OCaml成为一个很好的选择。

### Core标准库
光有语言还不够。你需要丰富的库作为应用程序的基础。学习OCaml时一种常见的挫败感就是编译器自带的标准库很有限，比你所期望的通用目的标准库设施要少得多。这是因为标准库不是通用目的工具，它被开发出来自举编译器的，并刻意保持短小。

庆幸的是，开源世界里从来就没有什么能阻止写出其它库来扩充编译器提供的标准库。这就是“Core”发布版。

Jane Street，一家已经使用OCaml超过10年的公司，开发Core是自用的，但是设计开始就具有前瞻性，要成为通用目的标准库。和OCaml自身一样，Core设计也充分考虑了正确性、可靠性和性能。

Core以语法扩展的形式发布，为OCaml提供了新功能，也提供了额外的库，如异步网络通信库，使Core更适合构建复杂分布式系统。所有这些库都是以自由的Apache 2许可发布的，无论出于兴趣，还是学术，甚至是在商业环境中，都允许使用。 

### OCaml平台
Core是一个全面高效的标准库，但除此之外还有许多OCaml软件。自1996年OCaml首次发布以来，已经有一个庞大的程序员社区在Ocaml，并且开发了大量有用的库和工具。在本书课程的例子中，我们会介绍其中一些库。


OPAM包管理器大大简化了第三方库的安装和管理。本书中我们会更详细介绍[OPAM](http://opam.ocaml.org/)，它构成了平台的基础，包括一组工具和库，配合OCaml编译器，让你可以又快又好地构建实用的应用程序。

我们也使用OPAM来安装 **utop**命令行界面。这是一个现代解释器，支持历史命令、宏展开，模块补全和其它细节，使得使用OCaml更舒服。贯穿本书，我们都会使用**utop**，这样你就可以交互式单步运行例子。

## 关于本书
Real World OCaml的目标读者是有一些传统语言经验的程序员，但对于静态类型函数式语言经验没有要求。根据你的背景，我们涵盖的一些概念对你来说也许的全新的，包括诸如高阶函数和不可变数据类型等传统的函数式编程技术，还有OCaml强大的类型和模块系统。

如果你已经会了OCaml，本书也会让你有意外之喜。Core重定义了许多标准命名空间来使OCaml的模块系统更好用，并且还默认地暴露了一些强大的、可重用的数据结构。旧的OCaml代码仍可以和Core交互，但你需要修改一下才能获得最大的好处。我们写的新代码都使用Core，我们相信Core是值得学习的，它已经在一个巨大的几百万行的代码库中成功使用，扫除了OCaml构建复杂应用的一个巨大障碍。

只使用传统编译器标准库的代码总是存在的，网络上有一些其它资源可以用来学习它们。Real World OCaml聚焦于作者们在构建可扩展的、健壮的软件的个人经验中使用的技术。

### 内容简介
Real World OCaml被分为3个部分：

- 第一部分涵盖了语言本身，开始以一个快速浏览来展现语言的骨架。不要试图去理解浏览中的一切，它只是为了让你尝试一下这种语言中不一样的地方，后面的章节会深入解释。

    语言核心之后，第一部分转向更高级的特性，像模块、算子和对象，这些可能要消化一段时间，但理解这些概念很重要。在OCaml之外，转向其它现代语言（许多都受ML启发）时，这些概念也可以给你带来好处。

- 第二部分建立在使用有用的工具和技术来应对普通实际应用的工作之上，应用包含从命令行解析到异步网络编译的方方面面。通过这种方式，你可以看到第一部分中的概念如何组合在一个真实的库和工具中，这组合了语言的不同特性来达到更好的效果。

- 第三部分讨论了OCaml的运行时系统和编译工具链。和其它语言（如Java或.NET的CLR）相比这非常简单。阅读这部分可以使你创建出非常高性能的系统，或者如何与C库配合。这一部分也介绍使用GNU gdb等工具进行性能分析和调试的技术。

#### 安装指导
Real World OCaml使用了一些在本书写作过程中开发的工具。其中的一些促进了编译器的提升，也就是说你要使用最新的开发环境（使用4.01版编译器）。我们通过OPAM使安装过程高度自动化。如何安装以及需要哪些库请参考[这里](https://github.com/realworldocaml/book/wiki/Installation-Instructions)。

发稿时，Core还不支持Windows系统，所有只有Mac OS X、Linux、FreeBSD和OpenBSD可以工作。请查看在线安装指导以获得最新的Windows支持情况，或安装一个Linux虚拟机来学习本书。

本书不是参考手册。我们的目标是教你这门语言、库、工具和技术来使你成为更高效的OCaml程序员。但是不能替代API文档或OCaml手册和man页。书中提及的库和工具的文档你都可以[在线](https://realworldocaml.org/doc)获得。

### Code Examples
本书所有的示例代码都可以免费在线获得，许可近似于完全公开，我们欢迎你拷贝合适的代码片到你的代码中，没有任何归属声明或其它限制。

在线代码库地址是：<https://github.com/realworldocaml/examples>。书中每个代码片都有一个头来说明源代码、shell脚本或源数据文件的在代码库中的文件名， 

## 关于作者
**Yaron Minsky**

Yaron Minsky 领导Jane Street的技术团队，Jane Street是一家私人交易公司，是OCaml最大的商业用户。他负责将OCaml引入公司并将公司的核心基础设施过渡到OCaml。现在，这些系统每天都要处理数十亿美元的证券交易。

Yaron在康乃尔大学获得计算机科学博士学位，主攻分布式系统。 Yaron通过演讲、博客和文章宣传OCaml已多年，一些文件发表在“ACM通信”和“函数式编程”期刊上。他是“Commercial Users of Functional Programming”指导委员会的主席，并且是“International Conference on Functional Programming”指导委员会的成员。

**Anil Madhavapeddy**

Anil Madhavapeddy是剑桥大学系统研究组的高级研究员。他是Xen虚拟机管理程序初始团队的一员，并完全使用OCaml帮助开发了一个业界领先的云管理工具箱。这个名为XenServer的产品已经部署到数百万台物理宿主机上，驱动着许多财富500强企业的重要基础设施。

在2006年从剑桥大学获得博士学位之前，Anil在工业界有很广泛的背景，包括NetApp、NASA和Internet Vision。他是OpenBSD开源社区的活跃成员，是“ACM Commercial Uses of Functional Programming”指导委员会的成员，还服务于一些广泛使用OCaml的初创公司的董事会。他还开发了Mirage [unikernel](http://anil.recoil.org/papers/2013-asplos-mirage.pdf)系统，在驱动层以上全部使用了OCaml。

**Jason Hickey**

Jason Hickey是加州山景城Google公司的一名软件工程师。他所在的团队设计开发全球计算基础设施，来支持Google的服务，包括管理调度大规模分布式计算资源的软件系统。

在加盟Google之前，Jason是加州理工学院的计算机科学助教，在那里他研究可靠、容错的计算系统，包括编程语言设计、形式化方法、编译器和新的分布式计算模型。他在康乃尔大学获得博士学位，主攻编程语言。他是MetaPRL系统的作者，这是一个设计和分析大型软件的逻辑框架;也是OMake的作者，一个大型软件工程的高级构建系统。他也是“An Introduction to Objective Caml（未出版）”的作者。

### 贡献者
我们要特别感谢下面的人，他们帮助改善了Real World OCaml：

- Leo White为[第11章，对象](https://github.com/zforget/translation/blob/master/real_world_ocaml/1_11_objects.md)和[12章，类](https://github.com/zforget/translation/blob/master/real_world_ocaml/1_12_classes.md)贡献了大量内容和示例。
- Jeremy Yallop是[19章，外部功能接口](https://github.com/zforget/translation/blob/master/real_world_ocaml/3_19_foreign_function_interface.md)描述的Ctypes库和文档的作者
- Stephen Weeks负责Core背后的模块化架构，他丰富的笔记是[20章，值的内存表示](https://github.com/zforget/translation/blob/master/real_world_ocaml/3_20_memory_representation_of_values.md)和[21章，理解垃圾收集器](https://github.com/zforget/translation/blob/master/real_world_ocaml/3_21_understanding_the_garbage_collector.md)的基础。
- Jeremie Diminio是 **utop**的作者，本书的代码片都在使用。

