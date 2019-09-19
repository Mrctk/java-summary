# 使用Docker安装GitLab

> 注意：确保安装docker成功

我们知道Git远程仓库分为公有远程仓库和私有远程仓库。

**私有远程仓库**

远程仓库实际上和本地仓库一样，纯粹为了7x24小时开机并交换大家的修改。GitHub就是一个免费托管开源代码的远程仓库。但是对于某些视源代码如生命的商业公司来说，既不想公开源代码，又舍不得给GitHub交保护费，那就只能自己搭建一台Git服务器作为私有仓库使用。这里我们选择GitLab私有仓库。


## GitLab简介

GitLab 是一个用于仓库管理系统的开源项目，使用Git作为代码管理工具，并在此基础上搭建起来的web服务。
更重要的是，GitLab是免费开源的，公司或者组织可以搭建自己的私有远程仓库GitLab。方便内部人员进行项目开发，提高开发效率，既不用公开源代码，又不需要花费额外的费用。




## 安装GitLab远程仓库

### 搜索GitLab版本

```shell
docker search gitlab
```

![搜索GitLab版本](https://raw.githubusercontent.com/JourWon/image/master/使用Docker安装GitLab/搜索GitLab版本.png)

我这里下载官方版gitlab，如果需要汉化版的请自行下载汉化版gitlab

### 查看GitLab镜像

```shell
docker images
```

![GitLab镜像](https://raw.githubusercontent.com/JourWon/image/master/使用Docker安装GitLab/GitLab镜像.png)

这里可以看到镜像还是比较大的，官方版的镜像有1.8G，汉化版的也有1.6G



### 启动GitLab

GitLab默认需要使用80和443端口，因为我的服务器是新的，没有占用80和443端口，于是我这里也使用这两个端口启动服务。**如果端口被占用请自行修改将容器内部端口向外映射的第二个参数。**

```shell
docker run -d  -p 443:443 -p 80:80 --name gitlab --restart always -v /opt/gitlab/config:/etc/gitlab -v /opt/gitlab/log:/var/log/gitlab -v /opt/gitlab/data:/var/opt/gitlab gitlab/gitlab-ce
```

```shell
# -d：后台运行
# -p：将容器内部端口向外映射，第一个参数是宿主机端口，第二个参数容器端口
# --name：命名容器名称
# --restart always在容器退出时总是重启容器
# -v：将容器内配置、日志、数据文件等文件夹挂载到宿主机指定目录
```

通常会将 GitLab 的配置 (etc) 、 日志 (log) 、数据 (data) 放到容器之外， 便于日后升级， 因此请先准备这三个目录。分别是/opt/gitlab/config，/opt/gitlab/log，/opt/gitlab/data

![GitLab宿主机目录](https://raw.githubusercontent.com/JourWon/image/master/使用Docker安装GitLab/GitLab宿主机目录.png)



GitLab首次启动较慢，请耐心等待，执行如下命令查看容器是否启动

```shell
docker ps
```

![GitLab容器](https://raw.githubusercontent.com/JourWon/image/master/使用Docker安装GitLab/GitLab容器.png)



### 配置GitLab

按上面的方式，gitlab容器运行没问题，但在gitlab上创建项目的时候，生成项目的URL访问地址是按容器的hostname来生成的，也就是容器的id。作为gitlab服务器，我们需要一个固定的URL访问地址，于是需要配置gitlab.rb（宿主机路径：/opt/gitlab/config/gitlab.rb）。

```shell
# gitlab.rb文件内容默认全是注释
$ vim /opt/gitlab/config/gitlab.rb
```

```shell
# 配置http协议所使用的访问地址,不加端口号默认为80
external_url 'http://10.172.0.201'

# 配置ssh协议所使用的访问地址和端口
gitlab_rails['gitlab_ssh_host'] = '10.172.0.201'
# 此端口是run时22端口映射的22端口
gitlab_rails['gitlab_shell_ssh_port'] = 22
```

```shell
#保存配置文件并退出
:wq
```

```shell
# 重启gitlab容器
$ docker restart gitlab
```



输入ip和端口，我这里因为使用的是80端口只需要输入服务器的ip即可，我这里输入10.172.0.201

首次打开GitLab网址的页面如下，我们需要设置root用户的密码，我这里设置为root123456

![首次打开GitLab网址](https://raw.githubusercontent.com/JourWon/image/master/使用Docker安装GitLab/首次打开GitLab网址.png)

### GitLab首页

![GitLab首页](https://raw.githubusercontent.com/JourWon/image/master/使用Docker安装GitLab/GitLab首页.png)

至此，GitLab私有远程仓库已经安装成功！👏👏



## 取消注册功能

一般我们的GitLab是暴露在外网的，我们不希望其他人可以注册用户，看到我们的项目，这时我们可以禁用注册用户的功能。

我们使用root用户登录Gitlab私有远程仓库

1. 点击【Admin Area】

2. 在【Settings】页面点击【General】 

3. 然后将【Sign-up enabled】的勾选框去掉，即取消掉注册功能。

4. 然后滚动到页面最底端，点击【Save】保存按钮，将配置保存起来。

![取消注册功能](https://raw.githubusercontent.com/JourWon/image/master/使用Docker安装GitLab/取消注册功能.png)



退出登录，查看效果

![取消注册功能后的登录页面](https://raw.githubusercontent.com/JourWon/image/master/使用Docker安装GitLab/取消注册功能后的登录页面.png)