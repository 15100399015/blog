### 什么是Monorepo

Monorepo 最早的出处是软件开发策略的一个分支，”mono” 表示单一 “repo” 是”repository”的缩写，意为多个项目共用一个代码库来管理依赖关系，同一套配置文件，统一构建部署流程等等。

Monorepo是集中式管理的前端代码管理方式，将所有的项目在集中一个代码仓库进行管理，严格的统一个收归，有利于统一的升级和管理方式，作为新型的管理方式，monorepo有效降低了运行以及协作的成本，但一个代码仓库的管理模式带来了项目体积上的上升，获取时间延长，同时安全性也有所下降

![file](https://liangx-gallery.oss-cn-beijing.aliyuncs.com/202303191940235.png)

上图为Multirepo和Monorepo的对比图，

- Multirepo是由多个仓库组成的项目管理方式，每个仓库有着独立的工作流，组件与配置
- Monorepo则将不同的仓库整合为一个仓库，并共享工作流，组件与配置

两种管理方式各有千秋，不能简单的定义那种方式更好，但monorepo的共享机制，统一管理和协作成本低等优势，显然更符合复杂项目的需求，选择Monorepo也是团队探索效率提升的必然道路

##### 不仅仅是代码托管

如果项目之间没有明确定义的依赖关系，仅仅是同用一个git仓库的话，并不能称之为 Monorepo 并且这样明显弊大于利。如果有明确的依赖关系，但是关系冗余，并没有复用抽象，其实也不算真正意义上的 Monorepo。

![img](https://liangx-gallery.oss-cn-beijing.aliyuncs.com/202303192024300.webp)

### 为什么需要 Monorepo

> multirepo是一种分散式的前端代码管理方式，按照功能或其他维度，将项目拆分成不同模块并单独维护于各自仓库中，作为传统的管理方式，multirepo具有灵活度高，安全控制等特点，但同时也带来了管理成本和协作成本，依赖升级等问题

我们把现在最普遍的 Multirepo 拿出来比较一下，Multirepo 是当前开发应用程序的标准方式每个团队的应用程序或项目都有一个单独的 repo。每个 repo 都有一个构建脚本配置和部署流程。

前端代码管理一直是困扰不少前端开发团队的难题，从开发到发布的整体工作流程，除了常规的技术问题外，往往还伴随着沟通成本，维护成本及协作效率等问题，这些问题在团队规模较小的时候可能不太明显，但是当团队规模变大的时候矛盾就越发明显

![img](https://liangx-gallery.oss-cn-beijing.aliyuncs.com/202303192026556.webp)

Multirepo 流行的一个重要原因是有很好的天然隔离机制，项目自身不依赖其他任何团队的项目，开发团队可以自己决定使用那些框架，依赖库的版本，代码风格的配置，构建的自定义配置。

但过多的自由性和项目隔离也带来了很多协作和管理的问题。

##### 繁琐的代码共享

要跨代码库共享代码的话，可能要为共享代码创建一个代码库。然后设置工具和 CI 环境，并设置包发布，以便其他存储库可以依赖它。与此同时，还要有代码更新版本，同时要维护其他项目依赖更新的心智负担。

##### 代码重复

在维护多个项目的时候，有一些逻辑很有可能会被多次用到，比如一些基础的组件、工具函数，或者一些配置，这些代码如果单单复制过来，之后这些代码出现 bug、或者需要做一些调整的时候，就得修改多份，维护成本越来越高。

##### 不统一的代码风格和配置文件

每个项目都使用自己的一组命令来运行测试、构建、服务、linting、部署等。不一致会产生需要记忆在不同项目之间使用哪些命令的心理开销。

代码风格的不一致，也会大大的降低可读性，以及不同开发人员在格式化时造成没必要的修改。

##### 黑洞级别的 node_modules

多个代码库会导致重复依赖的安装文件分布在每个代码库的 node_modules 中。

### Monorepo如何帮助解决所有这些问题？

##### 更好的代码复用

所有项目代码集中于单一仓库，易于抽离出共用的业务组件或工具。由于所有的项目放在一个仓库当中，复用起来非常方便，如果有依赖的代码变动，那么用到这个依赖的项目当中会立马感知到。并且所有的项目都是使用最新的代码，不会产生其它项目版本更新不及时的情况。

##### 工作流的一致性

复用已有的方式来构建和测试使用不同工具和技术编写的应用程序。只需要很少的人来维护所有项目的基建，维护成本也大大减低。不同开发人员可以自信地为其他团队的应用程序提交代码。

##### 依赖关系清晰

对于内部依赖有很清晰的引用路径，改动即时更新，不会有代码不同步的情况。改一处，所有依赖同步更新。

### Monorepo 配合工具还可以做到什么？

Monorepos 有很多优势，但要让它们发挥作用，需要拥有正确的工具。随着工作空间的扩大，这些工具必须帮助您保持快速、易于理解和易于管理。

##### 增量构建

如果我们的项目过大，构建多个子包会造成时间和性能的浪费，基于turborepo 的 Monorepo的缓存机制 可以帮助我们记住构建内容 并且跳过已经计算过的内容，优化打包效率。

##### 代码生成

大部分 Monorepo 工具都提供模板代码的自动生成，降低新增workspace的心智负担，并统一配置，不用担心不一致的问题。

##### 项目结构更加清晰可见

![img](https://liangx-gallery.oss-cn-beijing.aliyuncs.com/202303192041445.jpeg)

### Monorepo 不同解决方案的比较

![img](https://liangx-gallery.oss-cn-beijing.aliyuncs.com/202303192042136.webp)

### 合适的Monorepo方案

市面上关于Monorepo的解决方案和相关工具有很多，虽然rush、nx 之类的工具能够在特定的领域提供较好的解决方案，但却并不符合我们的实际需求。

> 市面上主流的包管理工具npm。yarn，pnpm都基于workspace的方式实现了对Monorepo架构的基础支持

#### lerna

#### turborepo

#### nx

#### rush

### Monorepo的一些落地实践

Monorepo解决了之前使用multirepo时存在的问题，帮助我们更好的管理代码

#### 统一配置

Multirepo存在的一个显著问题是配置的不统一导致的难以维护，所以我们需要对格式化、代码检测、打包等相关流程的配置进行规范化和统一，同时针对不同产品线的细微差别，也需要支持其灵活的扩展。因此我们在Monorepo仓库的根目录提供了统一的基础配置，同时如需要进行调整，不同产品线可以继承该配置并进行必要的修改。

#### 逻辑复用

Multirepo存在的另一个显著问题就是逻辑难以复用，迁移之前的逻辑复用主要是靠抽象到私有包并发布，或者直接复制粘贴，整体效率低，流程长且难以维护。迁移之后我们对各种配置等进行了统一的同时，也将公用的业务逻辑和组件整合到了仓库根目录的packages目录下，同时通过pnpm的 workspace protocal 链接到各个产品线中以复用。这样不仅解决了逻辑复用的相关问题，同时私有源也不用进行维护，Multirepo下的私有源维护成本问题得以解决。

#### 权限校验

当基础配置和公共逻辑被暴露出来之后，就面临着这些内容可以被随意修改的问题，而这往往会影响所有的产品线，稍有不慎会有造成巨大损失，因此我们需要给这些重要的内容施以限制和保护。

我们基于git hooks做了一些工作，在pre-commit和pre-push阶段分别对权限和分支名等内容进行了校验，并定义了Maintainer、Owner、Deverloper 三个角色，对应的权限分别为：

- Maintainer: 拥有全部权限，可以修改包括基础配置文件等的所有内容。
- Owner: 各产品线或者公共组件主要负责人，拥有对应范围内的所有权限。
- Developer: 该产品线或者公共组件的辅助开发人员，只拥有包括开发新功能等的部分产品线权限。

角色权限进行明确的划分之后，我们可以将基础配置和公共逻辑等内容的修改交给更有经验的工程师。同时权限分配配置维护在本地，这样可以更清晰的了解不同产品线对应的人员，方便沟通