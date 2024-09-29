---

layout: post	
title: "一些Linux服务器的初始设置"	
date: 2024-08-01 10:21:37	
updated: 2024-08-15 01:25:28	
excerpt: "自用笔记，基于Ubuntu22.04"	
categories: 
- 教程
- 计算机
tag: 
- 教程
- 计算机
typora-root-url: ..
---



# 前言

本文基于Ubuntu22.04系统

## 初始系统安装

### ssh登录相关

```bash
sudo apt install openssh-server
```



### 安装浏览器

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome-stable_current_amd64.deb
```



### zsh

安装zsh

```bash
apt install zsh git
```

安装oh-my-zsh

```bash
sh -c "$(wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```



激活conda（若安装完zsh再安装conda则无需这一步）

```bash
miniconda3/bin/conda init zsh
```



开关代理

```bash
# where proxy
proxy () {
  export NO_PROXY=localhost,127.0.0.1
  export HTTP_PROXY="http://127.0.0.1:7890"
  export HTTPS_PROXY="http://127.0.0.1:7890"
  echo "HTTP Proxy on"
}

# where noproxy
noproxy () {
  unset HTTP_PROXY
  unset HTTPS_PROXY
  echo "HTTP Proxy off"
}
```



#### autojump

```bash
sudo apt install autojump
```

接下来，将这一行加入`.zshrc`：

```
. /usr/share/autojump/autojump.sh
```

之后刷新

```bash
source .zshrc
```



### 为`apt`指令设置代理

```bash
sudo nano /etc/apt/apt.conf.d/proxy.conf
```

```bash
Acquire::http::Proxy "http://127.0.0.1:7890/";
Acquire::https::Proxy "http://127.0.0.1:7890/";
```



### ifconfig命令

```bash
sudo apt install net-tools
```



## 开发环境相关

### conda

```bash
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-x86_64.sh
sudo chmod 777 ./Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh
```



### CUDA

安装编译环境：

```bash
sudo apt install gcc-12 make cmake
```

CUDA使用[脚本](https://developer.nvidia.com/cuda-downloads)安装：

```bash
wget https://developer.download.nvidia.cn/compute/cuda/12.6.0/local_installers/cuda_12.6.0_560.28.03_linux.run
sudo sh cuda_12.6.0_560.28.03_linux.run
```

> /===========
> /= Summary =
> /===========
>
> Driver:   Installed
> Toolkit:  Installed in /usr/local/cuda-12.6/
>
> Please make sure that
>  -   PATH includes /usr/local/cuda-12.6/bin
>  -   LD_LIBRARY_PATH includes /usr/local/cuda-12.6/lib64, or, add /usr/local/cuda-12.6/lib64 to /etc/ld.so.conf and run ldconfig as root
>
> To uninstall the CUDA Toolkit, run cuda-uninstaller in /usr/local/cuda-12.6/bin
> To uninstall the NVIDIA Driver, run nvidia-uninstall
> Logfile is /var/log/cuda-installer.log



cudnn（[官网地址](https://developer.nvidia.com/cudnn-downloads)）

```bash
wget https://developer.download.nvidia.cn/compute/cudnn/9.3.0/local_installers/cudnn-local-repo-ubuntu2204-9.3.0_1.0-1_amd64.deb
sudo dpkg -i cudnn-local-repo-ubuntu2204-9.3.0_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2204-9.3.0/cudnn-*-keyring.gpg /usr/share/keyrings/
sudo apt update
sudo apt -y install cudnn
```

最后在`.zshrc`中添加：

```bash
export CUDA_HOME=/usr/local/cuda-12.6
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CUDA_HOME/lib64:/usr/local/cuda/extras/CUPTI/lib64
export PATH=$PATH:$CUDA_HOME/bin
```



当更新Linux内核后，此方法安装的驱动会失效，此时先使用以下指令卸载参与驱动文件：

```bash
sudo apt-get remove --purge '^nvidia-.*'
sudo apt-get remove --purge '^libnvidia-.*'
sudo apt-get remove --purge '^cuda-.*'
```

之后重新安装即可



### docker

```bash
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo wget -O /etc/apt/keyrings/docker.asc https://download.docker.com/linux/ubuntu/gpg
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
```

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

将当前用户加入docker用户组

```bash
sudo gpasswd -a <user> docker
newgrp docker
```

设置代理

```bash
sudo mkdir -p /etc/systemd/system/docker.service.d
sudo nano /etc/systemd/system/docker.service.d/proxy.conf
```

设置其内容为：

```bash
[Service]
Environment="HTTP_PROXY=http://127.0.0.1:7890/"
Environment="HTTPS_PROXY=http://127.0.0.1:7890/"
Environment="NO_PROXY=localhost,127.0.0.1"
```

#### 安装N卡驱动支持container-toolkit

```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

sudo apt update
sudo apt install -y nvidia-container-toolkit
```

设置docker

```bash
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```



### mysql

安装mysql

```bash
sudo apt update
sudo apt install mysql-server
sudo systemctl start mysql.service
```

设置密码

```bash
sudo mysql
```

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

```bash
sudo mysql_secure_installation
```



## 其它

### 切换图形界面和GUI

```bash
sudo systemctl set-default graphical.target
sudo systemctl set-default multi-user.target
```



### 创建新用户

```bash
sudo useradd -m -s /usr/bin/zsh user
```



### 切换GCC版本

```bash
sudo ln -s -f /usr/bin/gcc-12 /usr/bin/gcc
sudo ln -s -f /usr/bin/gcc-11 /usr/bin/gcc
```



### 安装latex环境

```bash
wget https://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz
zcat < install-tl-unx.tar.gz | tar xf -
cd install-tl-*
sudo ./install-tl
```

安装时取消选择所有其它语言

将下述内容加入环境变量：

```bash
export MANPATH=/usr/local/texlive/2024/texmf-dist/doc/man
export INFOPATH=/usr/local/texlive/2024/texmf-dist/doc/info
export PATH=/usr/local/texlive/2024/bin/x86_64-linux
```

