# Git与SVN的区别

[toc]



## SVN

SVN是**集中式版本控制系统**，版本库是集中放在中央服务器的，而干活的时候，用的都是自己的电脑，所以首先要从中央服务器哪里得到最新的版本，然后干活，干完后，需要把自己做完的活推送到中央服务器。集中式版本控制系统是必须联网才能工作，如果在局域网还可以，带宽够大，速度够快，如果在互联网下，如果网速慢的话，就郁闷了。

下图就是标准的集中式版本控制工具管理方式：

 ![集中式版本控制工具管理方式](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0dpdCVFNyVBRSU4MCVFNCVCQiU4Qi8lRTklOUIlODYlRTQlQjglQUQlRTUlQkMlOEYlRTclODklODglRTYlOUMlQUMlRTYlOEUlQTclRTUlODglQjYlRTUlQjclQTUlRTUlODUlQjclRTclQUUlQTElRTclOTAlODYlRTYlOTYlQjklRTUlQkMlOEYucG5n)

集中管理方式在一定程度上看到其他开发人员在干什么，而管理员也可以很轻松掌握每个人的开发权限。

但是相较于其优点而言，集中式版本控制工具缺点很明显：

- 服务器单点故障
- 容错性差



## Git

Git是**分布式版本控制系统**，它没有中央服务器，每个人的电脑就是一个完整的版本库，这样工作的时候就不需要联网了，因为版本都是在自己的电脑上。既然每个人的电脑都有一个完整的版本库，那多个人如何协作呢？比如说自己在电脑上改了文件A，其他人也在电脑上改了文件A，这时，你们两之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。

下图就是分布式版本控制工具管理方式：

![分布式版本控制工具管理方式](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0dpdCVFNyVBRSU4MCVFNCVCQiU4Qi8lRTUlODglODYlRTUlQjglODMlRTUlQkMlOEYlRTclODklODglRTYlOUMlQUMlRTYlOEUlQTclRTUlODglQjYlRTUlQjclQTUlRTUlODUlQjclRTclQUUlQTElRTclOTAlODYlRTYlOTYlQjklRTUlQkMlOEYucG5n)

## Git与SVN的区别

Git不仅仅是个版本控制系统，它也是个内容管理系统(CMS),工作管理系统等。
如果你是一个具有使用SVN背景的人，你需要做一定的思想转换，来适应Git提供的一些概念和特征。
Git 与 SVN 区别点：

1. Git是分布式的，SVN不是：这是Git和其它非分布式的版本控制系统，例如SVN，CVS等，最核心的区别。
2. Git把内容按元数据方式存储，而SVN是按文件：所有的资源控制系统都是把文件的元信息隐藏在一个类似.svn,.cvs等的文件夹里。
3. Git分支和SVN的分支不同：分支在SVN中一点不特别，就是版本库中的另外的一个目录。
4. Git没有一个全局的版本号，而SVN有：目前为止这是跟SVN相比GIT缺少的最大的一个特征。
5. Git的内容完整性要优于SVN：Git的内容存储使用的是SHA-1哈希算法。这能确保代码内容的完整性，确保在遇到磁盘故障和网络问题时降低对版本库的破坏。

## Git 与其他版本管理系统的区别

Git 在保存和对待各种信息的时候与其它版本控制系统有很大差异，尽管操作起来的命令形式非常相近，理解这些差异将有助于防止你使用中的困惑。

下面我们主要说一个关于 Git 其他版本管理系统的主要差别：**对待数据的方式**。

**Git采用的是直接记录快照的方式，而非差异比较。**

大部分版本控制系统（CVS、Subversion、Perforce、Bazaar 等等）都是以文件变更列表的方式存储信息，这类系统**将它们保存的信息看作是一组基本文件和每个文件随时间逐步累积的差异。**

具体原理如下图所示，理解起来其实很简单，每个我们对提交更新一个文件之后，系统记录都会记录这个文件做了哪些更新，以增量符号Δ(Delta)表示。下图来源于Git官网。

![文件变更列表的方式存储信息](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0dpdCVFNyVBRSU4MCVFNCVCQiU4Qi8lRTYlOTYlODclRTQlQkIlQjYlRTUlOEYlOTglRTYlOUIlQjQlRTUlODglOTclRTglQTElQTglRTclOUElODQlRTYlOTYlQjklRTUlQkMlOEYlRTUlQUQlOTglRTUlODIlQTglRTQlQkYlQTElRTYlODElQUYucG5n)



**我们怎样才能得到一个文件的最终版本呢？**

很简单，高中数学的基本知识，我们只需要将这些原文件和这些增加进行相加就行了。

**这种方式有什么问题呢？**

比如我们的增量特别特别多的话，如果我们要得到最终的文件是不是会耗费时间和性能。

Git 不按照以上方式对待或保存数据。 反之，Git 更像是把数据看作是对小型文件系统的一组快照。 每次你提交更新，或在 Git 中保存项目状态时，它主要对当时的全部文件制作一个快照并保存这个快照的索引。 为了高效，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。 Git 对待数据更像是一个 **快照流**。下图来源于Git官网。

![快照流方式存储信息](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0dpdCVFNyVBRSU4MCVFNCVCQiU4Qi8lRTUlQkYlQUIlRTclODUlQTclRTYlQjUlODElRTYlOTYlQjklRTUlQkMlOEYlRTUlQUQlOTglRTUlODIlQTglRTQlQkYlQTElRTYlODElQUYucG5n)

