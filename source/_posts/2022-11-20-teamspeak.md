---

layout: post

title: "teamspeak服务器搭建指南"

date: 2022-11-20 21:54:00

excerpt: "新版teamspeak搭建手册"

categories: 
	- 教程

tag: 
	- 教程

typora-root-url: ..
---





# 一、前言

首先搬上teamspeak的官网：[https://www.teamspeak.com/en/](https://www.teamspeak.com/en/)

先来介绍一下teamspeak吧，简单的来说，他是一款老牌的开源VoIP工具软件，可以进行语音通话，在线聊天，文件共享等等功能，不过呢，最大的特点还是它的可定制性强，以及非常低的带宽占用和计算机资源占用，下面的话我就以Ubuntu 20系统为例，简单的讲一下怎样去搭建一个属于你自己的语音服务器。



<iframe src="//player.bilibili.com/player.html?aid=41828207&bvid=BV1Vt411t7f5&cid=73444569&page=1" scrolling="yes" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="100%" height="500px"></iframe>



# 二、前期准备

首先，我们先做好前期的准备工作。你要准备的有：

- 一台服务器，拥有公网IP
- 下载好对应服务器系统的teamspeak服务端文件
- 下载好teamspeak客户端文件
- 能够远程链接你的服务器以及向服务器上传文件的工具，例如putty，Xshell，winscp或MobaXterm等



讲一下服务端的下载，进入teamspeak的官网之后，点击Downloads并选择server选项卡

![](/images/posts/teamspeak/服务器下载1.png)



并选择与你服务器系统相适应的服务端进行下载。我这里因为服务器安装的是64位Ubuntu系统，因此选择LINUX系统的SERVER 64-BIT服务端文件进行下载：

![](/images/posts/teamspeak/服务器下载2.png)



之后的操作也全都是在Linux系统下进行的操作，winserver版本更简单，下载下来直接双击运行唯一的一个exe文件就行了，这里不再赘述。

下载好服务端后，客户端的下载仍然差不多，选择client选项卡，下载你客户端系统相应的文件即可。 

![](/images/posts/teamspeak/客户端.png)

当然你也可以下新版客户端，好看一点，用起来大差不差

![在beta选项卡下载新版客户端](/images/posts/teamspeak/客户端2.png)

新版客户端长这样：

![新版客户端](/images/posts/teamspeak/客户端3.png)



# 二、安装配置服务端

在准备好文件之后，就开始安装服务端了，

首先升级系统更新依赖包

```bash
sudo apt update
```



一般来说，我们都是以roo权限登陆的服务器，但是由于teamspeak是不能用这一用户运行的，因此我们需要新建一个用户来运行teamspeak服务端文件：

```bash
 useradd teamspeak -m
 passwd teamspeak
```

第二个指令之后会要求你输入新建的这个用户的密码并再次确认，跟着走就行了。 

接下来，将之前准备好的服务端文件上传至服务器并解压，同时重命名文件夹（我这里是直接上传至根目录）

```bash
tar -xvf teamspeak3-server_linux_amd64-3.13.7.tar.bz2
mv teamspeak3-server_linux_amd64 teamspeak3
```

注意，这里服务端的版本号可能随着更新而变化，使用的时候不要直接复制

由于我们是将用teamspeak这一用户来运行服务端文件，因此我们还要把它拷贝给该用户并设置权限： 

```bash
 cp -R teamspeak3 /home/teamspeak/
 chown -R teamspeak:teamspeak /home/teamspeak/teamspeak3/
```

接下来就是运行服务端文件了，首先切换到我们刚才新建的用户： 

```bash
su - teamspeak
```

接下来进入服务端文件所在的目录（也就是我们之前重命名并拷贝过来的那一个）：

```bash
cd teamspeak3
```

接下来先不急着运行，我们先来创建一个新文件。如果这里我们不这么做，那么在运行时服务端会报错，因为它没有读到这个授权文件：

```bash
touch .ts3server_license_accepted
```

在授权文件建立好了之后，我们就可以运行服务端了：

```bash
./ts3server_startscript.sh start
```

