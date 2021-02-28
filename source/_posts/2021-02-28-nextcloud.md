---

layout: post

title: "安装自己的网盘--nextcloud服务端搭建手册"

date: 2021-02-28 10:54:30

excerpt: "基于Debain10的nextcloud搭建心得"

categories: 
	- 教程
	- 计算机

tag: 
	- 教程
	- 计算机
typora-root-url: ..
---



# 前言

本教程参考官方文档[^1]编写，加以一些补充说明。

{% note warning%}

本文系统为Debain10，实际操作时请以你的系统为准

{% endnote %}

对于一些操作过程中可能遇到的问题，写在了[额外说明](# 额外说明)一章中，可供参考。

# 安装过程

## 一、安装依赖

### 安装PHP4.7

```bash
sudo apt -y install lsb-release apt-transport-https ca-certificates 
sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/php.list
sudo apt update
sudo apt -y install php7.4
```

### 安装apache、mariadb及依赖的PHP模块

```bash
sudo apt update
sudo apt -y install apache2 mariadb-server libapache2-mod-php7.4
sudo apt -y install php7.4-gd php7.4-mysql php7.4-curl php7.4-mbstring php7.4-intl
sudo apt -y install php7.4-gmp php7.4-bcmath php-imagick php7.4-xml php7.4-zip
```



# 额外说明



# 参考文档

[^1]: [https://docs.nextcloud.com/server/21/admin_manual/installation/source_installation.html#](https://docs.nextcloud.com/server/21/admin_manual/installation/source_installation.html#)

