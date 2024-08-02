---

layout: post	
title: "一些Linux服务器的初始设置"	
date: 2024-08-01 10:21:37	
updated: 2024-08-02 09:12:28	
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
  export http_proxy="http://127.0.0.1:7890"
  export https_proxy="http://127.0.0.1:7890"
  echo "HTTP Proxy on"
}

# where noproxy
noproxy () {
  unset http_proxy
  unset https_proxy
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



## 开发环境相关

### conda

```bash
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-x86_64.sh
sudo chmod 777 ./Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh
```



### CUDA

首先安装驱动：

```bash
sudo ubuntu-drivers install nvidia
```

驱动理论上只决定支持的CUDA版本的上限，所以无脑装最新的就行了。

CUDA使用脚本安装，不安装驱动：

```bash
wget https://developer.download.nvidia.com/compute/cuda/12.1.0/local_installers/cuda_12.1.0_530.30.02_linux.run
sudo chmod 777 cuda_12.1.0_530.30.02_linux.run
./cuda_12.1.0_530.30.02_linux.run
```

cudnn

```bash
wget https://developer.download.nvidia.com/compute/cudnn/9.2.1/local_installers/cudnn-local-repo-ubuntu2204-9.2.1_1.0-1_amd64.deb
sudo dpkg -i cudnn-local-repo-ubuntu2204-9.2.1_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2204-9.2.1/cudnn-*-keyring.gpg /usr/share/keyrings/
sudo apt update
sudo apt -y install cudnn-cuda-12
```

最后在`.zshrc`中添加：

```bash
export CUDA_HOME=/usr/local/cuda-12.1
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-12.1/lib64:/usr/local/cuda/extras/CUPTI/lib64
export PATH=$PATH:$CUDA_HOME/bin
```



### docker

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

