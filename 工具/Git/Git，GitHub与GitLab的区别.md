# Git，GitHub与GitLab的区别

[toc]



## Git

![Git logo](https://raw.GitHubusercontent.com/JourWon/image/master/图标/Git%20logo3.png)

Git(读音为/gɪt/。)是一个开源的分布式版本控制系统，可以有效、高速地处理从很小到非常大的项目版本管理。Git 是 [Linus Torvalds](https://baike.baidu.com/item/Linus Torvalds/9336769) 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。

Torvalds 开始着手开发 Git 是为了作为一种过渡方案来替代 BitKe

[Git官网](https://git-scm.com/)

Git产生背景：同生活中的许多伟大事件一样，Git 诞生于一个极富纷争大举创新的年代。Linux 内核开源项目有着为数众广的参与者。绝大多数的 Linux 内核维护工作都花在了提交补丁和保存归档的繁琐事务上（1991－2002年间）。到 2002 年，Linux系统已经发展了十年了，代码库之大让Linus很难继续通过手工方式管理了，于是整个项目组开始启用分布式版本控制系统 BitKeeper 来管理和维护代码。

到 2005 年的时候，开发 BitKeeper 的商业公司同 Linux 内核开源社区的合作关系结束，他们收回了免费使用 BitKeeper 的权力。这就迫使 Linux 开源社区（特别是 Linux的缔造者 Linus Torvalds ）不得不吸取教训，只有开发一套属于自己的版本控制系统才不至于重蹈覆辙。



## GitHub

![GitHub logo](https://raw.GitHubusercontent.com/JourWon/image/master/图标/GitHub logo.jpg)GitHub是一个面向开源及私有软件项目的托管平台，因为只支持git作为唯一的版本库格式进行托管，故名GitHub。

GitHub于2008年4月10日正式上线，除了Git代码仓库托管及基本的 Web管理界面以外，还提供了订阅、讨论组、文本渲染、在线文件编辑器、协作图谱（报表）、代码片段分享（Gist）等功能。目前，其注册用户已经超过350万，托管版本数量也是非常之多，其中不乏知名开源项目Spring、MyBatis、React、Vue等。

2018年6月4日，微软宣布，通过75亿美元的股票交易收购代码托管平台GitHub。

[GitHub官网](https://github.com/)





## GitLab

![GitLab logo](https://raw.GitHubusercontent.com/JourWon/image/master/图标/GitLab.jpg)

GitLab 是一个用于仓库管理系统的开源项目，使用[Git](https://baike.baidu.com/item/Git)作为代码管理工具，并在此基础上搭建起来的web服务。安装方法是参考GitLab在GitHub上的Wiki页面。

**GitLab**是由GitLabInc.开发，使用[MIT许可证](https://baike.baidu.com/item/MIT许可证)的基于[网络](https://baike.baidu.com/item/网络)的[Git](https://baike.baidu.com/item/Git)[仓库](https://baike.baidu.com/item/仓库)管理工具，且具有[wiki](https://baike.baidu.com/item/wiki)和issue跟踪功能。使用[Git](https://baike.baidu.com/item/Git)作为代码管理工具，并在此基础上搭建起来的web服务。

GitLab由乌克兰程序员DmitriyZaporozhets和ValerySizov开发，它使用[Ruby语言](https://baike.baidu.com/item/Ruby语言)写成。后来，一些部分用[Go语言](https://baike.baidu.com/item/Go语言)重写。截止2018年5月，该公司约有290名团队成员，以及2000多名开源贡献者。GitLab被IBM，Sony，JülichResearchCenter，NASA，Alibaba，Invincea，O’ReillyMedia，Leibniz-Rechenzentrum(LRZ)，CERN，SpaceX等组织使用。

[GitLab官网](https://gitlab.com/users/sign_in)



## Git，GitHub与GitLab的区别

- Git是一种版本控制系统，是一种工具，用于代码的存储和版本控制。
- GitHub是一个基于Git实现的在线代码仓库，是目前全球最大的代码托管平台，可以帮助程序员之间互相交流和学习。
- GitLab是一个基于Git实现的在线代码仓库软件，你可以用GitLab自己搭建一个类似于GitHub一样的仓库，但是GitLab有完善的管理界面和权限控制，一般用于在企业、学校等内部网络搭建Git私服。
- GitHub和GiLlab两个都是基于Web的Git远程仓库，它们都提供了分享开源项目的平台，为开发团队提供了存储、分享、发布和合作开发项目的中心化云存储的场所。从代码的私有性上来看，GitLab 是一个更好的选择。但是对于开源项目而言，GitHub 依然是代码托管的首选。