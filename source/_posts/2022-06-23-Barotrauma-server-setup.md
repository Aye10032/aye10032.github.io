---

layout: post

title: "Barotrauma潜渊症服务器搭建笔记"

date: 2022-06-23 19:08:25

excerpt: "在Ubuntu系统下搭建Barotrauma潜渊症服务器的心得"

categories: 
	- 日常
	- 游戏
	- 潜渊症

tag: 
	- 日常
	- 游戏
	- 潜渊症

typora-root-url: ..

---


<iframe src="//player.bilibili.com/player.html?aid=385242548&bvid=BV1wZ4y1i7Za&cid=754041897&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>


# 一、安装SteamCMD

## 1、前置检查

首先检查你的源，确保后面加上了non-free：

![](/images/posts/barotrauma/non-free.png)

如果没有，加上就行了。



## 2、新建用户

由于steamCDM不推荐直接在root权限下运行，因此我们需要新建一个用户，我这里建立名为`steam`的用户：

```bash
sudo useradd -m steam
```

为这个账户设定密码：

```bash
sudo passwd steam 
```

下面，我们为这个用户赋予使用root指令的权限：

```bash
sudo nano /etc/sudoers
```

在原有的root下面加上你新建的用户名，后面直接照抄root的格式：

![](/images/posts/barotrauma/rooter.png)

按`CTRL+O`保存，`CTRL+X`退出。



接下来，切换到我们最新建立的这个账户，并进入它的根目录：

```bash
sudo -u steam -s
cd /home/steam
```

后续的操作全部在这个账户以及这个目录下，牢记。



## 3、安装

如果你的服务器是64位系统，那么执行以下指令：

```bash
 sudo add-apt-repository multiverse
 sudo dpkg --add-architecture i386
 sudo apt update
 sudo apt install lib32gcc-s1 steamcmd 
```

如果是32位系统，那么直接安装就可以了：

```bash
sudo apt install steamcmd
```



# 二、安装Barotrauma

## 1、安装文件

首先我们键入`steamcmd`进入steamCMD。

![](/images/posts/barotrauma/steam-cmd.png)

第一次运行之后会下载一些必要的文件，我们在它下载完成之后输入quit退出，之后开始下载潜渊症：

```bash
steamcmd +force_install_dir /home/steam/barotrauma-server +login anonymous +app_update 1026340 validate +exit
```

这里稍作解释：

- force_install_dir：指定下载路径，可以根据你的需要修改
- login anonymous：潜渊症需要匿名登陆之后才能下载
- app_update 1026340 validate：即下载潜渊症本体



## 2、创建steamclient.so的Symbolic link

这里的路径是相对于当前根目录而言的，即在/home/steam目录下

```bash
mkdir -p ~/.steam/sdk64/
ln -s ~/barotrauma-server/linux64/steamclient.so ~/.steam/sdk64/steamclient.so
```



## 3、修改配置文件

实际上一共有三个文件需要修改，首先是服务器配置文件

```bash
nano barotrauma-server/serversettings.xml
```

这个文件中首先要修改的是以下五个参数：

```xml
<serversettings
    name="" //服务器名字
    public="false"  //服务器是否可见
    password="" //服务器密码
    port="27015" //服务器登录端口
    queryport="27016" //服务器查询端口
</serversettings>        
```

配置文件全部的具体内容网上有很多，这里也列出一份[^1]：

```xml
<?xml version="1.0" encoding="utf-8"?>

<serversettings

  name="" //服务器名字
  public="true"  //服务器是否可见
  password="" //服务器密码
  port="27015" //服务器登录端口
  queryport="27016" //服务器查询端口
  playstyle="Serious" //服务器游戏风格 Serious=严肃 Casual=休闲 Roleplay=角色扮演 Rampage=暴乱 SomethingDifferent=mod服
  maxplayers="16" //服务器最大玩家人数
  enableupnp="false" //开启upnp转发
  autorestart="true" //自动重新开始
  LevelDifficulty="100" //游戏难度百分比
  AllowedRandomMissionTypes="Random,Salvage,Monster,Cargo,Combat"   //允许的随机任务类型
  AllowedClientNameChars="32-33,38-46,48-57,65-90,91-91,93-93,95-122,192-255,384-591,1024-1279,19968-40959,13312-19903,131072-15043983,15043985-173791,173824-178207,178208-183983,63744-64255,194560-195103" //设置玩家姓名中允许的字符编码
  ServerMessage="" //进入服务器消息提示
  tickrate="20" //设置服务器的时钟频率
  randomizeseed="True" //随机化
  userespawnshuttle="True" //用户生成穿梭机
  respawninterval="300" //复活时间
  maxtransporttime="180" //设置重生的停靠潜水艇时长
  minrespawnratio="0.2" //最小复活时间
  autorestartinterval="60" //自动重新启动间隔
  startwhenclientsready="False" //玩家准备好就开始
  startwhenclientsreadyratio="0.8" //玩家准备就绪比例开始
  allowspectating="True" //允许观察
  saveserverlogs="True" //保存服务器日志
  allowragdollbutton="True" //允许碎布玩偶按钮
  allowfiletransfers="True" //允许文件传输
  voicechatenabled="True" //语音聊天已启用
  allowvotekick="True" //允许投票
  allowendvoting="True" //允许结束投票
  allowrespawn="True" //允许重生
  botcount="0" //机器人数量
  maxbotcount="16" //最大机器人
  botspawnmode="Normal" //机器人生成模式 Normal = 正常 Fill=补充玩家空缺
  allowdisguises="True" //允许玩家通过其他身份来伪装自己
  allowrewiring="True" //允许玩家重新布线
  allowfriendlyfire="True" //允许友军开火
  traitorsenabled="No" //是否开启叛徒 yes = 有 no=没有 maybe=可能有
  subselectionmode="Random" //潜艇选择 Manual=手动 Random=随机 Vote=投票
  modeselectionmode="Random" //游戏模式 Manual=手动 Random=随机 Vote=投票
  endvoterequiredratio="0.6" //最终投票人比例
  kickvoterequiredratio="0.6" //踢人投票率
  killdisconnectedtime="120" //终止断开连接的时间
  kickafktime="600" //踢出挂机人时间
  traitoruseratio="True" //叛徒玩家
  traitorratio="0.2" //叛徒比率
  karmaenabled="True" //因果报应开启 - 在有叛徒模式下才能体现此系统的作用
  karmapreset="default" //恶报预设 default= 默认 strict=严格 custom=自定义
  gamemodeidentifier="sandbox" //游戏模式标识符 Sandbox=沙盒 Mission=任务 Campaign=战役
  missiontype="all" //任务类型 Salvage=打捞 Monster=怪物 Cargo=货物 Combat=战斗 Random=随机 all=全部
  autobantime="3600" //投票禁止玩家自动禁止时间
  maxautobantime="86400" //最大自动禁止时间 
</serversettings>

```



接下来编辑config_player.xml

```bash
nano barotrauma-server/config_player.xml
```

将游戏语言修改为简体中文：

```xml
<config
	language="Simplified Chinese"
</config>
```



最后一个文件是clientpermissions.xml，可以通过steam的浏览本地文件功能找到游戏的根目录，这个文件位于根目录的Data文件夹中

![](/images/posts/barotrauma/本地文件.png)

将你本地这个文件的内容复制下来，并覆盖掉服务端的该文件：

```bash
nano barotrauma-server/Data/clientpermissions.xml
```

这个文件夹实际上是管理用户权限的，这样一来你就拥有了服务器的全部权限，可以使用控制台的所有指令了。



# 三、运行服务器

## 1、错误修复

首先，直接运行服务端文件：

```
barotrauma-server/DedicatedServer
```

然后会出现一个报错，提示有一个目录不存在，这个目录的路径应该是`.local/share/Daedalic Entertainment GmbH/Barotrauma`

看了下是由于游戏会强制尝试在这里创建存档文件[^2]，好像官方也没啥修的意思，但是解决的方法也很简单，手动创建下这个文件夹就好了：

```bash
mkdir -p .local/share/Daedalic\ Entertainment\ GmbH/Barotrauma
```

接下来再运行服务端，即可看到游戏成功运行了：

![](/images/posts/barotrauma/运行成功.png)



## 2、服务器后台常驻

可以使用screen来让服务器进程在后台保持常驻，首先安装screen：

```bash
sudo apt install screen -y
```

接下来创立一个新的后台：

```bash
screen -S barotrauma
```

终端会切换到新的界面，在这里运行服务端文件，之后按`CTRL+A+D`退出

想要再次进入，只需要输入：

```bash
screen -r barotrauma
```



# 参考

[^1]: [如何在Linux上部署潜渊症服务器](https://www.bilibili.com/read/cv13816065)
[^2]: [https://github.com/Regalis11/Barotrauma/issues/1521](https://github.com/Regalis11/Barotrauma/issues/1521)
