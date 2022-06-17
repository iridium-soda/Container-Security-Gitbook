# 容器镜像漏洞隐患

虽然虚拟机镜像中庞杂的内容与巨大的体量致其难以迁移、传播或进行二次开发，但也使得其中的安全问题相对简单且易于应对。然而，容器镜像轻量小巧的特点决定了其与虚拟机镜像迥异的使用方式，同时也面临着更加复杂的安全问题。大部分开发者倾向于使用已有的容器镜像作为基础镜像进行二次开发，并随着种类丰富的容器镜像在社区内不断传播，最终形成以DockerHub镜像仓库为代表的庞大生态圈。镜像仓库的诞生虽然为开发者提供了巨大的便利，但也成为了恶意镜像的温床，而容器镜像易于传播的特性则进一步加剧了镜像中恶意内容和漏洞的扩散风险。容器镜像作为云服务实例化的根本，其安全性是保障云服务安全的基础。本小节将概述容器镜像安全方面的研究现状。

面对规模庞大且种类丰富的容器镜像，Shu等人\[21]于2017年对DockerHub中的镜像进行了深入调研与分析，希望更加全面理解当前容器镜像的安全现状。具体而言，通过对DockerHub中85 000个不同种类共计300 000个版本的镜像进行自动化分析后，当前镜像仓库的安全现状可概括为：1）Docker官方镜像与社区提供的镜像中平均包含超过180个漏洞，并且超过80%的官方和社区镜像中至少存在一个高危漏洞；2）大量官方和社区镜像长时间没有进行更新维护从而导致其中漏洞没有及时被修复，仅有少部分官方镜像的最新版本拥有较好的维护；3）镜像中的漏洞通常是在二次开发过程中扩散的，即从开发所用的基础镜像传播到新的镜像中。该研究表明当下需要更多自动化的方法来帮助容器镜像进行安全更新并及时修补其中的漏洞。

随着时间的推移，DockerHub中镜像的种类与规模均在持续上涨，其中镜像的安全问题也引发普遍关注。Tak等人\[22]于2018年再次对DockerHub中10 000个镜像进行了研究，结果发现：1）开发者越来越倾向于使用更轻量的镜像作为二次开发的基础镜像，因此减少了镜像中存在漏洞的可能性；2）大量镜像中依旧存在许多违反安全规则的行为，如设置较弱的SSH连接密码等；3）镜像间存在复杂的继承关系，90%的镜像中仍包含高危漏洞。研究结果表明，由于容器镜像的高灵活性与动态性，因此需要持续对容器镜像进行扫描以降低其中安全风险。

Zerouali等人\[23]于2019年更加深入分析研究了镜像中系统软件包的更新状态与其中漏洞的数量间的关系，在对7 380个流行镜像进行研究后发现：1）几乎所有镜像中的系统软件包均包含数量不等且影响程度不同的漏洞；2）大量的系统软件包没有被维护至最新的版本，且软件包的过期时间越长，其中的漏洞数量越多。作为对该工作的补充，同年Zerouali团队\[24]还对镜像中第三方软件包所包含的漏洞进行了抽样研究，通过对961个镜像中的JavaScript软件包进行深入分析，发现因第三方软件包过期所导致的漏洞问题同样严重，其中漏洞的数量也会随着软件包过期时间的增加而大幅增加。

Wist等人\[25]于2020年从漏洞数量、漏洞引入渠道和漏洞所在程序包等多个维度研究了容器镜像的安全现状：1）DockerHub中新引入的漏洞数量依旧在急剧增加；2）较严重的漏洞通常由Python和JavaScript两种脚本引入；3）Python 2.X和Jackson-databin程序包中存在的高危漏洞数量最多。同年，Liu等人\[26]从DockerHub中收集并分析了2 227 244镜像中存在的恶意行为，并发现：1）一些镜像开发者所建议的实例化参数会导致宿主机中信息被泄露，甚至引发宿主机拒绝服务；2）其中42个镜像中存在恶意命令可以使攻击者远程连接至容器实例中，或在容器实例中实施加密挖矿攻击；3）对镜像中漏洞进行修补的工作依旧处于被忽视且严重滞后的状态。

此外，容器镜像作为DevOps流程中的重要组件，在镜像传播或更新过程中会不可预料地引入漏洞。然而DockerHub等镜像仓库通常不具备镜像间继承谱系的查询功能，以帮助用户回溯并识别漏洞被引入的过程。Tak等人\[27]研究指出镜像在分发和重用过程中，多方开发者会因不了解其他开发者的安全设置，而意外向镜像引入安全风险或漏洞；另一方面，容器运行过程中对服务手动（或自动）的配置与更新同样可能打破镜像中预置的安全策略，从而引起容器中服务的安全性漂移。因此，该研究认为定期的镜像安全扫描和容器运行时安全检查需要同步进行，以应对因容器镜像灵活性和动态性所导致的安全问题。