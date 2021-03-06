![Maven 题图](https://s2.ax1x.com/2019/12/17/QIXaxP.png)

# Maven

Maven 是功能强大的构建工具能够帮我们自动化构建过程，从清理、编译、测试到生成报告，再到打包和部署。我们只需要输入简单的命令(如 `mvn clean install`)，Maven 就会帮我们处理繁琐的任务；它最大化的消除了构建的重复，抽象了构建生命周期，并且为绝大部分的构建任务提供了已实现的插件。比如说测试，我们只需要遵循 Maven 的约定编写好测试用例，当我们运行构建的时候，这些测试便会自动运行。除此之外，Maven 能帮助我们标准化构建过程。在 Maven 之前，十个项目可能有十种构建方式，但通过 Maven，所有项目的构建命令都是简单一致的。有利于促进项目团队的标准化。

# 构建工具对比

在 Linux C 开发中我们常常使用 Make 进行构建，不过 Make 将自己和操作系统绑定在一起了；也就是说，使用 Make，就不能实现(至少很难)跨平台的构建，这对于 Java 来说是非常不友好的。此外，Makefile 的语法也成问题，很多人抱怨 Make 构建失败的原因往往是一个难以发现的空格或 Tab 使用错误。而在 Java 发展过程中常见的自动化构建工具以 Ant、Maven、Gradle 为代表，整个自动化流程往往包含以下步骤：编译源代码、运行单元测试和集成测试、执行静态代码分析、生成分析报告、创建发布版本、部署到目标环境、部署传递过程以及执行冒烟测试和自动功能测试。

和 Make 一样，Ant 也都是过程式的，开发者显式地指定每一个目标，以及完成该目标所需要执行的任务。针对每一个项目，开发者都需要重新编写这一过程，这里其实隐含着很大的重复。Maven 是声明式的，项目构建过程和过程各个阶段所需的工作都由插件实现，并且大部分插件都是现成的，开发者只需要声明项目的基本元素，Maven 就执行内置的、完整的构建过程。这在很大程度上消除了重复。

此外，Ant 是没有依赖管理的，所以很长一段时间 Ant 用户都不得不手工管理依赖，这是一个令人头疼的问题。幸运的是，Ant 用户现在可以借助 Ivy 管理依赖。而对于 Maven 用户来说，依赖管理是理所当然的，Maven 不仅内置了依赖管理，更有一个可能拥有全世界最多 Java 开源软件包的中央仓库，Maven 用户无须进行任何配置就可以直接享用。

而 Gradle 抛弃了 Maven 的基于 XML 的繁琐配置；众所周知 XML 的阅读体验比较差，对于机器来说虽然容易识别，但毕竟是由人去维护的。取而代之的是 Gradle 采用了领域特定语言 Groovy 的配置，大大简化了构建代码的行数。Maven 的设计核心 Convention Over Configuration 被 Gradle 更加发扬光大，而 Gradle 的配置即代码又超越了 Maven。在 Gradle 中任何配置都可以作为代码被执行的，我们也可以随时使用已有的 Ant 脚本(Ant task 是 Gradle 中的一等公民)、Java 类库、Groovy 类库来辅助完成构建任务的编写。在[现代 Java 开发基础](https://parg.co/bgk)系列文章中也有专门的章节讲解 Gradle，笔者在 Android 与 Spring 项目构建中也会优先选择 Gradle。

# 生命周期

Maven 对构建(build)的过程进行了抽象和定义，这个过程被称为构建的生命周期(lifecycle)。生命周期(lifecycle)由多个阶段(phase)组成,每个阶段(phase)会挂接一到多个 goal。goal 是 maven 里定义任务的最小单元，相当于 ant 里的 target。

Maven 的生命周期就是对所有的构建过程进行抽象和统一。包含了项目的清理、初始化、编译、测试、打包、集成测试、验证、部署和站点生成等几乎所有的构建步骤。Maven 的生命周期是抽象的，即生命周期不做任何实际的工作，实际任务由插件完成，类似于设计模式中的模板方法。

## 核心概念

- lifecycle：生命周期，这是 maven 最高级别的的控制单元，它是一系列的 phase 组成，也就是说，一个生命周期，就是一个大任务的总称，不管它里面分成多少个子任务，反正就是运行一个 lifecycle，就是交待了一个任务，运行完后，就得到了一个结果，中间的过程，是 phase 完成的，自己可以定义自己的 lifecycle，包含自己想要的 phase。

- phase：可以理解为任务单元，lifecycle 是总任务，phase 就是总任务分出来的一个个子任务，但是这些子任务是被规格化的，它可以同时被多个 lifecycle 所包含，一个 lifecycle 可以包含任意个 phase，phase 的执行是按顺序的，一个 phase 可以绑定很多个 goal，至少为一个，没有 goal 的 phase 是没有意义的。

- goal: 这是执行任务的最小单元，它可以绑定到任意个 phase 中，一个 phase 有一个或多个 goal，goal 也是按顺序执行的，一个 phase 被执行时，绑定到 phase 里的 goal 会按绑定的时间被顺序执行，不管 phase 己经绑定了多少个 goal，你自己定义的 goal 都可以继续绑到 phase 中。

- mojo: lifecycle 与 phase 与 goal 都是概念上的东西，mojo 才是做具体事情的，可以简单理解 mojo 为 goal 的实现类，它继承于 AbstractMojo，有一个 execute 方法，goal 等的定义都是通过在 mojo 里定义一些注释的 anotation 来实现的，maven 会在打包时，自动根据这些 anotation 生成一些 xml 文件，放在 plugin 的 jar 包里。

抛开 mojo 不讲，lifecycle 与 phase 与 goal 就是级别的大小问题，引用必须是从高级引用下级（goal 绑定到 phase，也可理间为 phase 引用 goal，只是在具体绑定时，不会 phase 定义引用哪些 goal，但是执行是，却是 phase 调用绑定到它那的 goal），也不能跨级引用，如 lifecycle 可以引用任意的 phase，不同 lifecycle 可以同时引用相同的 phase，lifecycle 不能跨级引用 goal。goal 会绑定到任意的 phase 中，也就是说不同的 phase 可以同时引用相同的 goal，所以 goal 可以在一个 lifecycle 里被重复执行哦，goal 自然也不能说绑定到 lifecycle 中，它们三者的关系可以用公司里的 总领导，组领导，与职员的关系来解释。

lifecycle 的 phase 执行会指向之前所有的 phase，然后执行当前指定的 phase，一个 phase 会引用至少一个 goal。plugin 中的 goal 只是单单执行当前指定的 goal。执行 install:install 只会执行 installplugin 中的 goal:install，但是项目创建后还没有进行之前必要的步骤，比如 complie。这样直接执行 install:install 是肯定会出错的。

## 三套生命周期

Maven 有三套相互独立的生命周期，分别是 clean、default 和 site。每个生命周期包含一些阶段（phase），阶段是有顺序的，后面的阶段依赖于前面的阶段。

- clean 生命周期：清理项目，包含三个 phase：

1）pre-clean：执行清理前需要完成的工作
2）clean：清理上一次构建生成的文件
3）post-clean：执行清理后需要完成的工作

- default 生命周期：构建项目，重要的 phase 如下：

1）validate：验证工程是否正确，所有需要的资源是否可用。
2）compile：编译项目的源代码。
3）test：使用合适的单元测试框架来测试已编译的源代码。这些测试不需要已打包和布署。
4）package：把已编译的代码打包成可发布的格式，比如 jar。
5）integration-test：如有需要，将包处理和发布到一个能够进行集成测试的环境。
6）verify：运行所有检查，验证包是否有效且达到质量标准。
7）install：把包安装到 maven 本地仓库，可以被其他工程作为依赖来使用。
8）deploy：在集成或者发布环境下执行，将最终版本的包拷贝到远程的 repository，使得其他的开发者或者工程可以共享。

- site 生命周期：建立和发布项目站点，phase 如下：

1）pre-site：生成项目站点之前需要完成的工作
2）site：生成项目站点文档
3）post-site：生成项目站点之后需要完成的工作
4）site-deploy：将项目站点发布到服务器

各个生命周期相互独立，一个生命周期的阶段前后依赖。举例如下：

- mvn clean：调用 clean 生命周期的 clean 阶段，实际执行 pre-clean 和 clean 阶段

- mvn test：调用 default 生命周期的 test 阶段，实际执行 test 以及之前所有阶段

- mvn clean install：调用 clean 生命周期的 clean 阶段和 default 的 install 阶段，实际执行 pre-clean 和 clean，install 以及之前所有阶段

## Mojo

### 以 phase 为目标构建

以 phase 为目标进行构建是最常见的，如我们平时经常执行的 mvn compile,mvn test,mvn package... 等等,compile,test,package 都是 maven 生命周期(lifecycle)里的 phase,通过 mvn 命令，你可以指定一次构建执行到那一个阶段，在执行过程中，所有经历的执行阶段(phase)上绑定的 goal 都将得到执行。例如，对于一个 jar 包应用，当执行 mvn package 命令时，maven 从 validate 阶段一个阶段一个阶段的执行，在执行到 compile 阶段时，compiler 插件的 compile goal 会被执行，因为这个 goal 是绑定在 compile 阶段(phase)上的。这一点可从其对应的 mojo 类上得知：

![CompilerMojo](https://s2.ax1x.com/2019/12/17/QouCDS.png)

再比如经常使用的打包插件 shade，它的 goal 是绑定到 package 阶段的，这样，使用 mvn package 进行打包时都会执行 shade 的。

![ShareMojo](https://s2.ax1x.com/2019/12/17/QoulE4.png)

### 以 goal 为目标构建

虽然以 phase 为目标的构建最常见，但是有时候我们会发现，一些插件的 goal 并不适合绑定到任何阶段(phase)上，或者是，这些 goal 往往是单独执行，不需要同某个阶段(phase)绑定在一起，比如 hibernate 插件的导入\导出 goal 多数情况下是根据需要要手动执行的(当然，也可以绑定到某个阶段上，比如进行单元测试时，可考虑将其绑定到 test 阶段上)。再比如 jetty(6.1.26)插件，它的 goal 都是将打包或未打包的工程部署到 jetty 里然后启动 jetty 容器的，多数情况下，人们都是独立运行这些 goal 的，比如：人们希望当键入 mvn jetty:run 后，工程就能完成编译后启动 jetty,而 jetty 插件也确实是这样做的，它的 run goal 的 mojo 是这样声明的：

![Jetty6RunMojo](https://s2.ax1x.com/2019/12/17/QouJ81.png)

其中@execute phase="test-compile"指明 jetty:run 这一 goal 会促使 maven 先 build 到 test-compile 阶段，再执行这个 goal.同样，对于 jetty:run-war 这个 goal 则要求先 build 到 package 阶段再执行该 goal。

![Jetty6RunWar](https://s2.ax1x.com/2019/12/17/QouD5d.png)

而另外一个例子是 exec 插件的 exec:java：

![ExecJavaMojo](https://s2.ax1x.com/2019/12/17/Qou62t.png)

这个 goal 也声明了 execute 的 phase,但却是 validate，这样，如果代码没有编译，执行这个 goal 就会出错，所以多数情况下，人们总是使用下面的方式执行的：`mvn clean compile exec:java`。
