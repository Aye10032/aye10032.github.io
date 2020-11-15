---

layout: post

title: "windows terminal + power shell调教手册"

date: 2020-11-15

tag: 教程
typora-root-url: ..
---

# windows terminal + power shell调教手册



## 一、安装 windows terminal 和 power shell 7

windows terminal直接在应用商店里搜索安装即可：

![image-20201115172018617](/images/posts/winterminal/image-20201115172018617.png)

之后安装新版的power shell：

可以参考[官方文档](https://docs.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1)

全部安装好之后，从开始菜单打开power shell7，以管理员身份启动：

![image-20201115174738972](/images/posts/winterminal/image-20201115174738972.png)

下面的操作，除非特殊说明，记得**都要以管理员身份运行**。



## 二、安装Chocolatey[^1]

Chocolatey是一个Windows下的包管理系统，我们下面的很多插件等都需要使用他来进行安装，安装非常简单，只需要在power shell中执行：

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

即可完成安装：

![tem2](/images/posts/winterminal/tem2.jpg)

在下面的过程中，若碰到使用Choco命令安装软件包时出现超时报错的，可以考虑为Choco设置代理：

```powershell
choco config set proxy http://127.0.0.1:1080
```

![](/images/posts/winterminal/tem3.jpg)

## 三、安装oh-my-posh[^2]

目前，只要安装了Windows terminal就可以直接安装oh-my-posh了。

注意，下面的操作一定是在<font color=red>**以管理员身份运行的power shell中**</font>进行！

### 1、安装microsoft-windows-terminal包：

```powershell
choco install microsoft-windows-terminal
```

之中会碰到询问是否运行安装脚本，输入A（*意为全部同意*），回车

![tem3](/images/posts/winterminal/tem4.jpg)

### 2、分别安装posh-git和oh-my-posh：

```powershell
Install-Module posh-git -Scope CurrentUser
Install-Module oh-my-posh -Scope CurrentUser
```

同理，若网络条件不佳导致安装失败，可以考虑通过代理安装：

```powershell
Install-Module posh-git -Scope CurrentUser -Proxy "http://127.0.0.1:1080"
Install-Module oh-my-posh -Scope CurrentUser -Proxy "http://127.0.0.1:1080"
```

同样，按A，继续运行安装

![](/images/posts/winterminal/tem5.jpg)



### 3、安装PSReadLine

```powershell
Install-Module -Name PSReadLine -AllowPrerelease -Scope CurrentUser -Force -SkipPublisherCheck
```



### 4、生成配置文件

```powershell
if (!(Test-Path -Path $PROFILE )) { New-Item -Type File -Path $PROFILE -Force }
notepad $PROFILE
```

第一行代码运行之后，即可通过第二行代码打开配置文件

![](/images/posts/winterminal/tem6.jpg)

在此配置文件中输入如下信息：

```powershell
Import-Module posh-git
Import-Module oh-my-posh
Set-Theme Paradox
```

最后一行为主题，你也可以换成你自己喜欢的主题[^3]

之后运行此代码，使配置文件生效：

```powershell
set-executionpolicy remotesigned
```



### 5、安装支持字体

由于主题涉及到一些特殊字符，因此需要安装支持这些特殊字符的字体，

oh-my-posh官方文档中推荐的是[Meslo LG M Regular for Powerline Nerd Font](https://github.com/ryanoasis/nerd-fonts/blob/master/patched-fonts/Meslo/M/Regular/complete/Meslo%20LG%20M%20Regular%20Nerd%20Font%20Complete%20Mono.ttf)

当然，你也可以前往这个字体所在的GitHub项目[^4]选择其他自己喜欢的字体下载。



## 参考文献：

[^1]: [chocolatey官方文档](https://chocolatey.org/install#individual)
[^2]:[oh-my-posh](https://github.com/JanDeDobbeleer/oh-my-posh)
[^3]:[oh-my-posh 主题样例](https://github.com/JanDeDobbeleer/oh-my-posh#themes)
[^4]: [nerd-fonts项目仓库](https://github.com/ryanoasis/nerd-fonts)