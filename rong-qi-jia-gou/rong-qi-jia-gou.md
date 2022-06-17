---
description: Still Unchecked.
---

# 容器架构

## 容器的沿革发展

[https://www.infoq.cn/article/ss6sitklgolexqp4umr5](https://www.infoq.cn/article/ss6sitklgolexqp4umr5)

源起：属于 Jails 们的时代 虚拟化容器技术（virtualized container）的历史，其实可以一直追溯上世纪 70 年代末。时间回到 1979 年，贝尔实验室（ Bell Laboratories 正在为 Unix V7 （Version 7 Unix）操作系统的发布进行最后的开发和测试工作。

在那个时候，Unix 操作系统还是贝尔实验室的内部项目，而运行 Unix 的机器则是长得像音响一样的、名叫 PDP 系列的巨型盒子。在那个“软件危机（The Software Crisis）”横行的末期，开发和维护 Unix 这样一套操作系统项目，即使对贝尔实验室来说也绝非易事。更何况，那里的程序员们还得一边开发 Unix ，一边开发 C 语言本身呢。 而在 Unix V7 的开发过程中，系统级别软件构建（Build）和测试（Test）的效率其实是其中一个最为棘手的难题。这里的原因也容易理解：当一个系统软件编译和安装完成后，整个测试环境其实就被“污染”了。如果要进行下一次构建、安装和测试，就必须重新搭建和配置整改测试环境。在有云计算能力的今天，我们或许可以通过虚拟机等方法来完整的复现一个集群。但在那个一块 64K 的内存条要卖 419 美元的年代，“快速销毁和重建基础设施”的想法还是有点“科幻”了。 所以，贝尔实验室的聪明脑袋们开始构思一种在现有操作系统环境下“隔离”出一个可供软件进行构建和测试的环境。更确切的说，就是我能否简单的执行一些指令，就改变一个程序的“视图”，让它把当前目录当做自己的根目录呢？这样，我每次只要在当前目录里放置一个完整操作系统文件系统部分，该软件运行所需的所有依赖就完备了。

更为重要的是，有了这个能力，开发者实际上就间接拥有了应用基础设施“快速销毁和重现”的能力，而不需要在环境搭好之后进入到环境里去进行应用所需的依赖安装和配置。这当然是因为，现在我的整个软件运行的依赖，是以一个操作系统文件目录的形式事先准备好的，而开发者只需要构建和测试应用的时候，切换应用“眼中”的根目录到这个文件目录即可。 于是，一个叫做 chroot（Change Root）的系统调用就此诞生了。

顾名思义，chroot 的作用是“重定向进程及其子进程的根目录到一个文件系统上的新位置”，使得该进程再也看不到也没法接触到这个位置上层的“世界”。所以这个被隔离出来的新环境就有一个非常形象的名字，叫做 Chroot Jail。 值得一提的是，这款孕育了 chroot 的 Unix V7 操作系统，成为了贝尔实验室 Unix 内部发行版的绝唱。在这一年末尾， Unix 操作系统正式被 AT\&T 公司商业化，并被允许授权给外部使用，自此开启了一代经典操作系统的传奇之旅。 而 Chroot 的诞生，也第一次为世人打开了“进程隔离”的大门。 伴随着这种机被更广泛的用户接触到， chroot 也逐渐成为了开发测试环境配置和应用依赖管理的一个重要工具。而 “Jail” 这个用来描述进程被隔离环境的概念，也开始激发出这个技术领域更多的灵感。 在 2000 年，同属 Unix 家族的 FreeBSD 操作系统发布了"jail"命令， 宣布了 FreeBSD Jails 隔离环境的正式发布。相比于 Chroot Jail，FreeBSD Jails 把”隔离“这个概念扩展到了进程的完整视图，隔离出了独立进程环境和用户体系，并为 Jails 环境分配了独立的 IP 地址。所以确切的说，尽管 chroot 开创了进程环境隔离的思想，但 FreeBSD Jails，其实才真正实现了进程的沙箱化。而这里的关键在于，这种沙箱的实现，依靠的是操作系统级别的隔离与限制能力而非硬件虚拟化技术。

不过，无论是 FreeBSD Jails（Unix 平台），还是紧接着出现的 Oracle Solaris Containers（Solaris 平台），都没有能在更广泛的软件开发和交付场景中扮演到更重要的角色。在这段属于 Jails 们的时代，进程沙箱技术因为“云”的概念尚未普及，始终被局限在了小众而有限的世界里。 发展：云，与应用容器 事实上，在 Jails 大行其道的这几年间，同样在迅速发展的 Linux 阵营上也陆续出现多个类似的沙箱技术比如 Linux VServer 和 Open VZ（未进入内核主干）。但跟 Jails 的发展路径比较类似，这些沙箱技术最后也都沦为了小众功能。我们再次看到了，进程沙箱技术的发展过程中“云”的角色缺失所带来的影响，其实还是非常巨大的。而既然说到“云”，我们就不得不提到基础设施领域的翘楚：Google。 Google 在云计算背后最核心的基础设施领域的强大影响力，是被业界公认的一个事实，无论是当初震惊世界的三大论文，还是像 Borg/Omega 等领先业界多年的内部基础设施项目，都在这个领域扮演者重要的启迪者角色。

然而，放眼当今的云计算市场， 仅仅比 Google 云计算发布早一年多的 AWS 却是“云”这个产业毫无争议的领导者。而每每谈到这里的原因，大家都会提到一个充满了争议的项目：GAE。 GAE 对于中国的某几代技术人员来说，可以说是一个挥之不去的记忆。然而，即使是这批 GAE 的忠实用户，恐怕也很难理解这个服务竟然就是 Google 当年决定用来对抗 AWS 的核心云产品了。事实上，GAE 本身与其说是 PaaS，倒不如说是简化版的 Serverless。在 2008 年，在绝大多数人还完全不知道云计算为何物的情况下，这样的产品要想取得成功拿下企业用户，确实有些困难。 不过，在这里我们想要讨论的并不是 Google 的云战略，而是为什么从技术的角度上，Google 也认为 GAE 这样的应用托管服务就是云计算呢？ 这里的一个重要原因可能很多人都了解过，那就是 Google 的基础设施技术栈其实是一个标准容器技术栈，而不是一个虚拟机技术栈。更为重要的是，在 Google 的这套体系下，得益于 Borg 项目提供的独有的应用编排与管理能力，容器不再是一个简单的隔离进程的沙箱，而成为了对应用本身的一种封装方式。依托于这种轻量级的应用封装，Google 的基础设施可以说是一个天然以应用为中心的托管架构和编程框架，这也是很多前 Googler 调侃“离开了 Borg 都不知道怎么写代码”的真实含义。这样一种架构和形态，映射到外部的云服务成为 GAE 这样的一个 PaaS/Serverless 产品，也就容易理解了。

而 Google 这套容器化基础设施的规模化应用与成熟，可以追溯到 2004\~2007 年之间，而这其中一个最为关键的节点，正是一种名叫 Process Container 技术的发布。 Process Container 的目的非常直白，它希望能够像虚拟化技术那样给进程提供操作系统级别的资源限制、优先级控制、资源审计能力和进程控制能力，这与前面提到的沙箱技术的目标其实是一致的。这种能力，是前面提到的 Google 内部基础设施得以实现的基本诉求和基础依赖，同时也成为了 Google 眼中“容器”技术的雏形。带着这样的设思路，Process Container 在 2006 年由 Google 的工程师正式推出后，第二年就进入了 Linux 内核主干。 不过，由于 Container 这个术语在 Linux 内核中另有它用，Process Container 在 Linux 中被正式改名叫作：Cgroups。Cgroups 技术的出现和成熟，标志在 Linux 阵营中“容器”的概念开始被重新审视和实现。更重要的是，这一次“容器”这个概念的倡导者变成了 Google：一个正在大规模使用容器技术定义世界级基础设施的开拓者。

在 2008 年，通过将 Cgroups 的资源管理能力和 Linux Namespace 的视图隔离能力组合在一起，LXC（Linux Container）这样的完整的容器技术出现在了 Linux 内核当中。尽管 LXC 提供给用户的能力跟前面提到的各种 Jails 以及 OpenVZ 等早期 Linux 沙箱技术是非常相似的，但伴随着 Linux 操作系统开始迅速占领商用服务器市场的契机，LXC 的境遇比前面的这些前辈要好上不少。 而更为重要的是，2008 年之后 AWS ，Microsoft 等巨头们持续不断的开始在公有云市场上进行发力，很快就催生出了一个全新的、名叫 PaaS 的新兴产业。 老牌云计算厂商在 IaaS 层的先发优势以及这一部分的技术壁垒，使得越来越多受到公有云影响的技术厂商以及云计算的后来者，开始思考如何在 IaaS 之上构建新的技术与商业价值，同时避免走入 GAE 当年的歧途。在这样的背景之下，一批以开源和开放为主要特点的平台级项目应运而生，将 “PaaS” 这个原本虚无缥缈的概念第一次实现和落地。这些 PaaS 项目的定位是应用托管服务，而不同于 GAE 等公有云托管服务 ，这些开放 PaaS 项目希望构建的则是完全独立于 IaaS 层的一套应用管理生态，目标是借助 PaaS 离开发者足够近的优势锁定云乃至所有数据中心的更上层入口。这样的定位，实际上就意味着 PaaS 项目必须能够不依赖 IaaS 层虚拟机技术，就能够将用户提交的应用进行封装，然后快速的部署到下层基础设施上。而这其中，开源、中立，同时又轻量、敏捷的 Linux 容器技术，自然就成为了 PaaS 进行托管和部署应用的最佳选择。

在 2009 年，VMware 公司在收购了 SpringSource 公司（Spring 框架的创始公司）之后，将 SpringSource 内部的一个 Java PaaS 项目的名字，套在了自己的一个内部 PaaS 头上，然后于 2011 年宣布了这个项目的开源。这个项目的名字，就叫做：Cloud Foundry。2009年，Cloud Foundry 项目的诞生，第一次对 PaaS 的概念完成了清晰而完整的定义。 这其中，“PaaS 项目通过对应用的直接管理、编排和调度让开发者专注于业务逻辑而非基础设施”，以及“PaaS 项目通过容器技术来封装和启动应用”等理念，也第一次出现在云计算产业当中并得到认可。值得一提的是，Cloud Foundry 用来操作和启动容器的项目叫做：Warden，它最开始是一个 LXC 的封装，后来重构成了直接对 Cgroups 以及 Linux Namespace 操作的架构。 Cloud Foundry 等 PaaS 项目的逐渐流行，与当初 Google 发布 GAE 的初衷是完全一样的。说到底，这些服务都认为应用的开发者不应该关注于虚拟机等底层基础设施，而应该专注在编写业务逻辑这件最有价值的事情上。这个理念，在越来越多的人得以通过云的普及开始感受到管理基础设施的复杂性和高成本之后，才变得越来越有说服力。在这幅蓝图中，Linux 容器已经跳出了进程沙箱的局限性，开始扮演的“应用容器”的角色。

在这个新的舞台上， 容器和应用终于画上了等号，这才最终使得平台层系统能够实现应用的全生命周期托管。 按照这个剧本，容器技术以及云计算的发展，理应向着 PaaS 的和以应用为中心的方向继续演进下去。如果不是有一家叫做 Docker 的公司出现的话。 容器：改写的软件交付的历程 如果不是亲历者的话，你很难想象 PaaS 乃至云计算产业的发展，会如何因为 2013 年一个创业公司开源项目的发布而被彻底改变。但这件事情本身，确实是过去 5 年间整个云计算产业变革的真实缩影。 Docker 项目的发布，以及它与 PaaS 的关系，想必我们已经无需在做赘述。一个“降维打击”，就足以给当初业界的争论不休画上一个干净利落的句号。 我们都知道， Docker 项目发布时，无非也是 LXC 的一个使用者，它创建和使用应用容器的逻辑跟 Warden 等没有本质不同。不过，我们现在也知道，真正让 PaaS 项目无所适从的，是 Docker 项目最厉害的杀手锏：容器镜像。 关于如何封装应用，这本身不是开发者所关心的事情，所以 PaaS 项目有着无数的发挥空间。但到这如何定义应用这个问题，就是跟每一位技术人员息息相关了。在那个时候，Cloud Foundry 给出的方法是 Buildpack，它是一个应用可运行文件（比如 WAR 包）的封装，然后在里面内置了 Cloud Foundry 可以识别的启动和停止脚本，以及配置信息。 然而，Docker 项目通过容器镜像，直接将一个应用运行所需的完整环境，即：整个操作系统的文件系统也打包了进去。这种思路，可算是解决了困扰 PaaS 用户已久的一致性问题，制作一个“一次发布、随处运行”的 Docker 镜像的意义，一下子就比制作一个连开发和测试环境都无法统一的 Buildpack 高明了太多。 更为重要的是，Docker 项目还在容器镜像的制作上引入了“层”的概念，这种基于“层”（也就是“commit” ) 进行 build，push，update 的思路，显然是借鉴了 Git 的思想。所以这个做法的好处也跟 Github 如出一辙：制作 Docker 镜像不再是一个枯燥而乏味的事情，因为通过 DockerHub 这样的镜像托管仓库，你和你的软件立刻就可以参与到全世界软件分发的流程当中。 至此，你就应该明白，Docker 项目实际上解决的确实是一个更高维度的问题：软件究竟应该通过什么样的方式进行交付？更重要的是，一旦当软件的交付方式定义的如此清晰并且完备的时候，利用这个定义在去做一个托管软件的平台比如 PaaS，就变得非常简单而明了了。这也是为什么 Docker 项目会多次表示自己只是“站在巨人肩膀上”的根本原因：没有最近十年 Linux 容器等技术的提出与完善，要通过一个开源项目来定义并且统一软件的交付历程，恐怕如痴人说梦。

云应用，与云原生,时至今日，容器镜像已经成为了现代软件交付与分发的事实标准。然而， Docker 公司却并没有在这个领域取得同样的领导地位。这里的原因相比大家已经了然于心：在容器技术取得巨大的成功之后，Docker 公司在接下来的“编排之争”中犯下了错误。事实上，Docker 公司凭借“容器镜像”这个巧妙的创新已经成功解决了“应用交付”所面临的最关键的技术问题。但在如何定义和管理应用这个更为上层的问题上，容器技术并不是“银弹”。在“应用”这个与开发者息息相关的领域里，从来就少不了复杂性和灵活性的诉求，而容器技术又天然要求应用的“微服务化”和“单一职责化”，这对于绝大多数真实企业用户来说都是非常困难的。而这部分用户，又偏偏是云计算产业的关键所在。 而相比于 Docker 体系以“单一容器”为核心的应用定义方式，Kubernetes 项目则提出了一整套容器化设计模式和对应的控制模型，从而明确了如何真正以容器为核心构建能够真正跟开发者对接起来的应用交付和开发范式。而 Docker 公司、Mesosphere 公司 以及 Kubernetes 项目在“应用”这一层上的不同理解和顶层设计，其实就是所谓“编排之争”的核心所在。

2017 年末，Google 在过去十年编织全世界最先进的容器化基础设施的经验，最终帮助 Kubernetes 项目取得到了关键的领导地位，并将 CNCF 这个以“云原生”为关键词的组织和生态推向了巅峰。 而最为有意思的是， Google 公司在 Kubernetes 项目倾其全力注入的“灵魂”，既不是 Borg/Omega 多年来积累下来的大规模调度与资源管理能力，也不是“三大论文”这样让当年业界望尘莫及的领先科技。Kubernetes 项目里最能体现 Google 容器理念的设计，是“源自 Borg/Omega 体系的应用编排与管理能力”。 我们知道，Kubernetes 是一个“重 API 层” 的项目，但我们还应该理解的是，Kubernetes 是一个“以 API 为中心”的项目。围绕着这套声明式 API，Kubernetes 的容器设计模式，控制器模型，以及异常复杂的 apiserver 实现与扩展机制才有了意义。而这些看似繁杂的设计与实现背后，实际上只服务于一个目的，那就是：如何让用户在管理应用的时候能最大程度的发挥容器和云的价值。 本着这个目的，Kubernetes 才会把容器进行组合，用 Pod 的概念来模拟进程组的行为。才会坚持用声明式 API 加控制器模型来进行应用编排，用 API 资源对象的创建与更新（PATCH）来驱动整个系统的持续运转。更确切的说，有了 Pod 和容器设计模式，我们的应用基础设施才能够与应用（而不是容器）进行交互和响应的能力，实现了“云”与“应用”的直接对接。而有了声明式 API，我们的应用基础而设施才能真正同下层资源、调度、编排、网络、存储等云的细节与逻辑解耦。我们现在，可以把这些设计称为“云原生的应用管理思想”，这是我们“让开发者专注于业务逻辑”、“最大程度发挥云的价值”的关键路径。 所以说，Kubernetes 项目一直在做的，其实是在进一步清晰和明确“应用交付”这个亘古不变的话题。只不过，相比于交付一个容器和容器镜像， Kubernetes 项目正在尝试明确的定义云时代“应用”的概念。

在这里，应用是一组容器的有机组合，同时也包括了应用运行所需的网络、存储的需求的描述。而像这样一个“描述”应用的 YAML 文件，放在 etcd 里存起来，然后通过控制器模型驱动整个基础设施的状态不断地向用户声明的状态逼近，就是 Kubernetes 的核心工作原理了。 未来：应用交付的革命不会停止 说到这里，我们已经回到了 2019 年这个软件交付已经被 Kubernetes 和容器重新定义的时间点。 在这个时间点上，Kubernetes 项目正在继续尝试将应用的定义、管理和交付推向一个全新的高度。我们其实已经看到了现有模型的一些问题与不足之处，尤其是声明式 API 如何更好的与用户的体验达成一致。在这个事情上，Kubernetes 项目还有不少路要走，但也在快速前行。

我们也能够看到，整个云计算生态正在尝试重新思考 PaaS 的故事。Google Cloud Next 2019 上发布的 Cloud Run，其实已经间接宣告了 GAE 正凭借 Kubernetes 和 Knative 的标准 API “浴火重生”。而另一个典型的例子，则是越来越多很多应用被更“极端”的抽象成了 Function，以便完全托管于与基础设施无关的环境（FaaS）中。如果说容器是完整的应用环境封装从而将应用交付的自由交还给开发者，那么 Function 则是剥离了应用与环境的关系，将应用交付的权利交给了 FaaS 平台。我们不难看到，云计算在向 PaaS 的发展过程中被 Docker “搅局”之后，又开始带着“容器”这个全新的思路向 “PaaS” 不断收敛。只不过这一次，PaaS 可能会换一个新的名字叫做：Serverless。 我们还能够看到，云的边界正在被技术和开源迅速的抹平。越来越多的软件和框架从设计上就不再会跟某云产生直接绑定。毕竟，你没办法抚平用户对商业竞争担忧和焦虑，也不可能阻止越来越多的用户把 Kubernetes 部署在全世界的所有云上和数据中心里。我们常常把云比作“水、电、煤”，并劝诫开发者不应该关心“发电”和“烧煤”的事情。但实际上，开发者不仅不关心这些事情，他们恐怕连“水、电、煤”是哪来的都不想知道。在未来的云的世界里，开发者完全无差别的交付自己的应用到世界任何一个地方，很有可能会像现在我们会把电脑插头插在房间里任何一个插孔里那样自然。这也是为什么，我们看到越来越多的开发者在讨论“云原生”。

我们无法预见未来，但代码与技术演进的正在告诉我们这样一个事实：未来的软件一定是生长于云上的。这将会是“软件交付”这个本质问题的不断自我革命的终极走向，也是“云原生”理念的最核心假设。而所谓“云原生”，实际上就是在定义一条能够让应用最大程度利用云的能力、发挥云的价值的最佳路径。在这条路径上，脱离了“应用”这个载体，“云原生”就无从谈起；容器技术，则是将这个理念落地、将软件交付的革命持续进行下去的重要手段之一。 而至于 Kubernetes 项目，它的确是整个“云原生”理念落地的核心与关键所在。但更为重要的是，在这次关于“软件”的技术革命中，Kubernetes 并不需要尝试在 PaaS 领域里分到一杯羹：它会成为连通“云”与“应用”的高速公路，以标准、高效的方式将“应用”快速交付到世界上任何一个位置。而这里的交付目的地，既可以是最终用户，也可以是 PaaS/Serverless 从而催生出更加多样化的应用托管生态。

“云”的价值，一定会回归到应用本身。 一起进入云原生架构时代 在云的趋势下，越来越多的企业开始将业务与技术向“云原生”演进。这个过程中最为艰难的挑战其实来自于如何将应用和软件向 Kubernetes 体系进行迁移、交付和持续发布。而在这次技术变革的浪潮中，”云原生应用交付“，已经成为了 2019 年云计算市场上最热门的技术关键词之一。 阿里巴巴从 2011 年开始通过容器实践云原生技术体系，在整个业界都还没有任何范例可供参考的大背境下，逐渐摸索出了一套比肩全球一线技术公司并且服务于整个阿里集团的容器化基础设施架构。在这些数以万计的集群管理经验当中，阿里容器平台团队探索并总结了四个让交付更加智能与标准化的方法：Everything on Kubernetes，利用 K8s 来管理一切，包括 K8s 自身； 应用发布回滚策略，按规则进行灰度发布，当然也包括发布 kubelet 本身；将环境进行镜像切分，分为模拟环境和生产环境；并且在监控侧下足功夫，将Kubernetes 变得更白盒化和透明化，及早发现问题、预防问题、解决问题。

近期，阿里云容器平台团队也官宣了两个社区项目：Cloud Native App Hub —— 面向所有开发者的 Kubernetes 应用管理中心，OpenKruise —— 源自全球顶级互联网场景的 Kubernetes 自动化开源项目集。 云原生应用中心（Cloud Native App Hub），它首先为国内开发者提供了一个 Helm 应用中国镜像站，方便用户获得云原生应用资源，同时推进标准化应用打包格式，并可以一键将应用交付到K8s 集群当中，大大简化了面向多集群交付云原生应用的步骤；而OpenKruise/Kruise 项目致力于成为“云原生应用自动化引擎”，解决大规模应用场景下的诸多运维痛点。OpenKruise 项目源自于阿里巴巴经济体过去多年的大规模应用部署、发布与管理的最佳实践，从不同维度解决了 Kubernetes 之上应用的自动化问题，包括部署、升级、弹性扩缩容、QoS 调节、健康检查，迁移修复等等。 在接下来，阿里云容器平台团队还会以此为基础，继续同整个生态一起推进云原生应用定义、K8s CRD 与 Operator 编程范式、增强型 K8s 自动化插件等一系列能够让云原生应用在规模化多集群场景下实现快速交付、更新和部署等技术的标准化与社区化。

##