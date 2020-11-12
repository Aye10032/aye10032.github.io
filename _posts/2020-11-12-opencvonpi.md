---

layout: post

title: "树莓派安装python3+OpenCV4.0"

date: 2020-11-12

tag: 教程
typora-root-url: ..
---

# 树莓派安装python3+OpenCV4.0



## 一、树莓派配置

### 1、换源

```bash
# 编辑 `/etc/apt/sources.list` 文件，删除原文件所有内容，用以下内容取代：
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib rpi
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main non-free contrib rpi

# 编辑 `/etc/apt/sources.list.d/raspi.list` 文件，删除原文件所有内容，用以下内容取代：
deb http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui
```

编辑器推荐使用nano，例如：

```bash
sudo nano /etc/apt/sources.list
```

编辑完毕后 CTRL+O 保存、CTRL+X 退出。

更新源列表：

```bash
sudo apt-get update
sudo apt-get -y dist-upgrade
```



### 2、安装screen

```bash
sudo apt-get install screen
```

screen可以让进程在**后台挂起**，方便后面编译时脱机编译



### 3、修改交换分区及显存

```bash
sudo nano /etc/dphys-swapfile
```

选择将交换分区大小设为内存的两倍，即：

```bash
# set size to computed value, this times RAM size, dynamically adapts,
#   guarantees that there is enough swap without wasting disk space on excess
CONF_SWAPFACTOR=2
```

并将其他的配置注释掉。

重启服务：

```bash
sudo /etc/init.d/dphys-swapfile stop
sudo /etc/init.d/dphys-swapfile start
```

之后检查是否修改成功：

```bash
free -m
```

若看到类似如下信息，则说明修改成功：

![](images\posts\picv\Screen-Shot-2018-11-21-at-1.16.59-PM.png)



修改显存：

```bash
sudo nano /boot/config.txt
```

将 gpu_mem 设为128

```bash
gpu_mem=128
```

（可以适当的再改大一点）

重启树莓派：

```bash
sudo reboot
```



### 4、安装依赖

git：

```bash
sudo apt-get install git
```

编译工具：

```bash
sudo apt-get install cmake
sudo apt-get install gcc g++
```

图形界面、媒体、编码等依赖：

```bash
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev
```

GTK支持：

```bash
sudo apt-get install libgtk2.0-dev libgtk-3-dev
```

图片解码：

```bash
sudo apt-get install libpng-dev
sudo apt-get install libjpeg-dev
sudo apt-get install libopenexr-dev
sudo apt-get install libtiff-dev
sudo apt-get install libwebp-dev
```



### 5、python相关

安装python3：

```bash
sudo apt-get install python3-dev
```

执行以下指令，检查是否有安装pip：

```bash
pip3 -h
```

若没有安装，则安装pip3：

```bash
sudo apt-get install python3-pip
```

安装 numpy 和 scipy：

```bash
sudo pip3 install numpy scipy
```



### 6、正式编译

首先分别下载两个源码：

```bash
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
```

若git下载缓慢，可以用gitee进行转载：

![image-20201112202141441](images\posts\picv\image-20201112202141441.png)

![image-20201112202225347](images\posts\picv\image-20201112202225347.png)

![image-20201112202331350](images\posts\picv\image-20201112202331350.png)



之后只要将仓库代码换为gitee上你down下来的仓库地址即可：

![image-20201112202546806](images\posts\picv\image-20201112202546806.png)



下载下来后，在应该存在两个文件夹，分别名为opencv和opencv_contrib。

建立编译文件夹：

```bash
cd ~/opencv/  # 这里是opencv本体源码的文件夹，请根据你的实际路径加以调整
mkdir build
cd build
```

添加编译配置信息：

```bash
cmake -D CMAKE_BUILD_TYPE=RELEASE \
	 -D CMAKE_INSTALL_PREFIX=/usr/local \
	-D INSTALL_C_EXAMPLES=OFF \
	-D INSTALL_PYTHON_EXAMPLES=OFF \
	-D OPENCV_EXTRA_MODULES_PATH=/home/pi/opencv_contrib/modules \  # 这里是contrib文件夹所在路径，请根据你的实际路径加以调整
	-D BUILD_EXAMPLES=OFF ..
```



配置完成后，开始编译：

```bash
make -j4
```

这里是使用4个线程进行编译，理论上树莓派4的性能应该是可以支持8线程的，可以将后面参数改为 `-j8`

编译结束后，开始安装：

```bash
sudo make install
sudo ldconfig
sudo apt-get update
```

之后重启系统：

```bash
sudo reboot
```



### 7、可能问题

若安装完成后import cv2仍然报错，可以考虑手动拷贝文件：

```bash
cd ~/opencv/build/lib/python3
sudo cp cv2.cpython-37m-arm-linux-gnueabihf.so /usr/local/lib/python3.7/dist-packages/cv2.so
```

这里的文件路径及python版本根据实际情况进行相应修改即可。



------

#### 参考文档：

【1】: [OpenCV官方文档](https://docs.opencv.org/master/d2/de6/tutorial_py_setup_in_ubuntu.html)

【2】: [installing opencv4.0 on raspberrypi 3b](https://www.alatortsev.com/installing-opencv-4-0-on-raspberry-pi-3-b/)

【3】: [opnecv_contrib文档](https://github.com/opencv/opencv_contrib)

【4】: [清华大学开源镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/raspbian/)



