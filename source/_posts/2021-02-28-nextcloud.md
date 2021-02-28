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

在debain10系统中，以root用户登陆时输入所有命令均无需sudo

为了适合一般情况，本文在所有命令前添加了sudo

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

### 配置数据库

首先创建root用户：

```bash
sudo /etc/init.d/mysql start
sudo mysql -u root -p
```

回车之后会要求为root账户设定密码，输入之后回车，即可看见类似如下的字样：

![](/images/posts/nextcloud/image-20210228113414161.png)

接下来为nextcloud创建用户名：

```sql
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES ON nextcloud.* TO 'username'@'localhost';
FLUSH PRIVILEGES;
```

{% note info%}

将上述命令中的`username`替换为你自己的用户名，`password`替换为你自己的密码

{% endnote %}

![](/images/posts/nextcloud/image-20210228114126688.png)

之后，可以输入

```sql
quit;
```

退出数据库命令行。

## 二、安装nextcloud

首先，进入apache的web目录：

```bash
cd /var/www/html
```

接下来前往[https://nextcloud.com/install/](https://nextcloud.com/install/)下载服务端安装包，选择Download for server：

![下载界面](/images/posts/nextcloud/image-20210228131212430.png)

之后，可以点击download按钮将压缩包下载到本地，再用FTP传到服务端，也可以右键复制下载链接之后用命令行下载：

```bash
wget https://download.nextcloud.com/server/releases/nextcloud-21.0.0.zip
```

![下载服务端](/C:/Users/yeyu6/AppData/Roaming/Typora/typora-user-images/image-20210228131258395.png)

解压：

```bash
sudo unzip nextcloud-21.0.0.zip
```

{% note info%}

如果你不是21.0.0，记得换成相应的版本，反正tab一打就补全了

{% endnote %}

重启apache：

```bash
systemctl restart apache2
```

## 三、配置apache

编辑apache的配置文件：

```bash
nano /etc/apache2/sites-available/nextcloud.conf
```

将其内容写为：

```
Alias /nextcloud "/var/www/nextcloud/"

<Directory /var/www/nextcloud/>
  Require all granted
  AllowOverride All
  Options FollowSymLinks MultiViews

  <IfModule mod_dav.c>
    Dav off
  </IfModule>
</Directory>
```

按CTRL+O保存，CTRL+X退出。

之后使配置文件生效：

```bash
a2ensite nextcloud.conf
systemctl reload apache2
```

接下来，设置启用一些模块：

```bash
a2enmod rewrite
a2enmod headers
a2enmod env
a2enmod dir
a2enmod mime
a2enmod ssl
a2ensite default-ssl
systemctl reload apache2
```



## 四、从网页端完成安装

当上面所有信息配置完毕之后，访问 serverIP/nextcloud 即可看到网盘的初始化界面：

![填写相关信息](/images/posts/nextcloud/image-20210228160855466.png)

等待安装完成之后，即可看到相应的界面：

![登录界面](/images/posts/nextcloud/image-20210228161853337.png)



# 额外说明

## 一、关于数据库

可以不安装mariadb而换用SQLite，但是当网盘文件存放较多，使用者也较多时，还是建议安装mariadb。

## 二、使用phpMyAdmin图形化管理数据库

首先进入apache的web根目录，一般为：

```bash
cd /var/www/html
```

之后在根目录下载：

```bash
sudo wget https://files.phpmyadmin.net/phpMyAdmin/5.1.0/phpMyAdmin-5.1.0-all-languages.zip
sudo unzip phpMyAdmin-5.1.0-all-languages.zip
sudo cp -R phpMyAdmin-5.1.0-all-languages phpMyAdmin
sudo rm -rf phpMyAdmin-5.1.0-all-languages
```

有点蠢（~~其实就是我忘了剪切的指令又懒得去搜罢了~~）

然后重启apache：

```bash
systemctl restart apache2
```

之后前往 serverIP/phpMyAdmin 即可访问管理界面：

![](/images/posts/nextcloud/image-20210228125301455.png)

若发现无法通过root用户登录数据库，可以先从命令行登录数据库后，执行：

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
```

{% note info%}

将上述命令中的`password`替换为你自己的密码

{% endnote %}

之后就可以通过密码登录了。

# 参考文档

[^1]: [https://docs.nextcloud.com/server/21/admin_manual/installation/source_installation.html#](https://docs.nextcloud.com/server/21/admin_manual/installation/source_installation.html#)

