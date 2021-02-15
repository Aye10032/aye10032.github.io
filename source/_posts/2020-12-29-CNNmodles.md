---

layout: post

title: "几种经典的CNN模型"

date: 2020-12-29

tag: 笔记
typora-root-url: ..
---

# 几种经典的CNN模型

首先，标准的CNN网络结构模型一般包括：

- 卷积层
- 批标准化
- 激活函数
- 池化
- dropout，休眠一定比例的神经元



## 1、LeNet

共5层，包括两层卷积计算和三层全连接

- 第一层卷积计算
  - 6个5*5卷积核，卷积步长为1，不使用全零填充
  - 无批标准化
  - 激活函数使用sigmoid
  - 最大池化，池化核尺寸2*2，步长为2，不使用全零填充
  - 无dropout
- 第二层卷积计算
  - 16个5*5卷积核，卷积步长为1，不使用全零填充
  - 无批标准化
  - 激活函数使用sigmoid
  - 最大池化，池化核尺寸2*2，步长为2，不使用全零填充
  - 无dropout
- fatten
- 全连接网络，120个神经元，使用sigmoid激活函数
- 全连接网络，84个神经元，使用sigmoid激活函数
- 全连接网络，10个神经元，使用softmax激活函数

![LeNet](/images/posts/CNNmodels/LeNet.png)

## 2、AlexNet

共8层，包括五层卷积计算和三层全连接

- 第一层卷积计算
  - 96个3*3卷积核，卷积步长为1，不使用全零填充
  - 使用局部相应标准化LRN
  - 激活函数使用relu
  - 最大池化，池化核尺寸3*3，步长为2
  - 无dropout
- 第二层卷积计算
  - 256个3*3卷积核，卷积步长为1，不使用全零填充
  - 使用局部相应标准化LRN
  - 激活函数使用relu
  - 最大池化，池化核尺寸3*3，步长为2
  - 无dropout
- 第三层卷积计算
  - 384个3*3卷积核，卷积步长为1，**使用**全零填充
  - 无标准化
  - 激活函数使用relu
  - 不使用池化
  - 无dropout
- 第四层卷积计算与第三层相同
  - 384个3*3卷积核，卷积步长为1，**使用**全零填充
  - 无标准化
  - 激活函数使用relu
  - 不使用池化
  - 无dropout
- 第五层卷积计算
  - 256个3*3卷积核，卷积步长为1，**使用**全零填充
  - 无标准化
  - 激活函数使用relu
  - 最大池化，池化核尺寸3*3，步长为2
  - 无dropout
- fatten
- 全连接网络，2048个神经元，使用relu激活函数，50% dropout
- 全连接网络，2048个神经元，使用relu激活函数，50% dropout
- 全连接网络，10个神经元，使用softmax激活函数

![image-20201231132729733](/images/posts/CNNmodels/AlexNet.png)

## 3、VGGNet

共16层，包括13层卷积计算及三层全连接

### 1、卷积计算层

| 层数 |                  卷积层                   | 批标准化 | 激活层 |                池化层                | dropout |
| :--: | :---------------------------------------: | :------: | :----: | :----------------------------------: | :-----: |
|  1   | 64个卷积核，尺寸为3*3，步长为1，全零填充  |  BN操作  |  relu  |                  无                  |   无    |
|  2   | 64个卷积核，尺寸为3*3，步长为1，全零填充  |  BN操作  |  relu  | 最大池化，池化核尺寸2*2，池化步长为2 |   0.2   |
|  3   | 128个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  |                  无                  |   无    |
|  4   | 128个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  | 最大池化，池化核尺寸2*2，池化步长为2 |   0.2   |
|  5   | 256个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  |                  无                  |   无    |
|  6   | 256个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  |                  无                  |   无    |
|  7   | 256个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  | 最大池化，池化核尺寸2*2，池化步长为2 |   0.2   |
|  8   | 512个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  |                  无                  |   无    |
|  9   | 512个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  |                  无                  |   无    |
|  10  | 512个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  | 最大池化，池化核尺寸2*2，池化步长为2 |   0.2   |
|  11  | 512个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  |                  无                  |   无    |
|  12  | 512个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  |                  无                  |   无    |
|  13  | 512个卷积核，尺寸为3*3，步长为1，全零填充 |  BN操作  |  relu  | 最大池化，池化核尺寸2*2，池化步长为2 |   0.2   |

### 2、全连接层

- fatten
- 全连接网络，512个神经元，使用relu激活函数，20% dropout
- 全连接网络，512个神经元，使用relu激活函数，20% dropout
- 全连接网络，10个神经元，使用softmax激活函数



![VGGNet](/images/posts/CNNmodels/VGGNet.png)

## 4、InceptionNet

主体结构为Inception结构块，结构如下图所示：

<div class="mermaid">
    classDiagram
    卷积连接器 <-- 1X1卷积核a
    卷积连接器 <-- 3X3卷积核b
    3X3卷积核b <-- 1X1卷积核b : 降维
    卷积连接器 <-- 5X5卷积核c
    5X5卷积核c <-- 1X1卷积核c : 降维
    卷积连接器 <-- 1X1卷积核d : 降维
    1X1卷积核d <-- 3X3最大池化核
    1X1卷积核a <-- 输入层
    1X1卷积核b <-- 输入层
    1X1卷积核c <-- 输入层
    3X3最大池化核 <-- 输入层
    卷积连接器 : 将收到的四路特征
    卷积连接器 : 按照深度拼接
</div>

|    卷积核     |                    卷积层                     | 批标准化 | 激活层 |                       池化层                        | dropout |
| :-----------: | :-------------------------------------------: | :------: | :----: | :-------------------------------------------------: | :-----: |
|  1X1卷积核a   | 16个卷积核，尺寸为1*1，<br/>步长为1，全零填充 |  BN操作  |  relu  |                         无                          |   无    |
|  1X1卷积核b   | 16个卷积核，尺寸为1*1，<br/>步长为1，全零填充 |  BN操作  |  relu  |                         无                          |   无    |
|  3X3卷积核b   | 16个卷积核，尺寸为3*3，<br/>步长为1，全零填充 |  BN操作  |  relu  |                         无                          |   无    |
|  1X1卷积核c   | 16个卷积核，尺寸为1*1，<br/>步长为1，全零填充 |  BN操作  |  relu  |                         无                          |   无    |
|  5X5卷积核c   | 16个卷积核，尺寸为5*5，<br/>步长为1，全零填充 |  BN操作  |  relu  |                         无                          |   无    |
| 3X3最大池化核 |                      无                       |    无    |   无   | 最大池化，池化核尺寸3*3，<br/>池化步长为1，全零填充 |   无    |
|  1X1卷积核d   | 16个卷积核，尺寸为1*1，<br/>步长为1，全零填充 |  BN操作  |  relu  |                         无                          |   无    |

神经网络结构范例如下所示，包括四个Inception结构块，两两一组，分为block_0和block_1

<div class="mermaid">
    graph TB
    id1(3*3 conv, filters=16)
    style id1 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    id2_1(Filter concatenation)
    id2_2(1*1 conv)
    id2_3(1*1 conv)
    id2_4(3*3 conv)
    id2_5(1*1 conv)
    id2_6(5*5 conv)
    id2_7(1*1 conv)
    id2_8(3*3 pooling)
    id2_9(Previous layer)
    style id2_1 fill: #B9CF9320, stroke: #B9CF93, stroke-width:2px
    style id2_9 fill: #B9CF9320, stroke: #B9CF93, stroke-width:2px
    style id2_2 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id2_4 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id2_6 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id2_3 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id2_5 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id2_7 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id2_8 fill: #CFA3A420, stroke: #CFA3A4, stroke-width:2px
    id3_1(Filter concatenation)
    id3_2(1*1 conv)
    id3_3(1*1 conv)
    id3_4(3*3 conv)
    id3_5(1*1 conv)
    id3_6(5*5 conv)
    id3_7(1*1 conv)
    id3_8(3*3 pooling)
    id3_9(Previous layer)
    style id3_1 fill: #B9CF9320, stroke: #B9CF93, stroke-width:2px
    style id3_9 fill: #B9CF9320, stroke: #B9CF93, stroke-width:2px
    style id3_2 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id3_4 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id3_6 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id3_3 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id3_5 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id3_7 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id3_8 fill: #CFA3A420, stroke: #CFA3A4, stroke-width:2px
    subgraph block_0
    id2_9 --> id2_2
    id2_2 --> id2_1
    id2_9 --> id2_3
    id2_3 --> id2_4
    id2_4 --> id2_1
    id2_9 --> id2_5
    id2_5 --> id2_6
    id2_6 --> id2_1
    id2_9 --> id2_8
    id2_8 --> id2_7
    id2_7 --> id2_1
    id3_9 --> id3_2
    id3_2 --> id3_1
    id3_9 --> id3_3
    id3_3 --> id3_4
    id3_4 --> id3_1
    id3_9 --> id3_5
    id3_5 --> id3_6
    id3_6 --> id3_1
    id3_9 --> id3_8
    id3_8 --> id3_7
    id3_7 --> id3_1
    id2_1 --> id3_9
    end
    id4_1(Filter concatenation)
    id4_2(1*1 conv)
    id4_3(1*1 conv)
    id4_4(3*3 conv)
    id4_5(1*1 conv)
    id4_6(5*5 conv)
    id4_7(1*1 conv)
    id4_8(3*3 pooling)
    id4_9(Previous layer)
    style id4_1 fill: #B9CF9320, stroke: #B9CF93, stroke-width:2px
    style id4_9 fill: #B9CF9320, stroke: #B9CF93, stroke-width:2px
    style id4_2 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id4_4 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id4_6 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id4_3 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id4_5 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id4_7 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id4_8 fill: #CFA3A420, stroke: #CFA3A4, stroke-width:2px
    id5_1(Filter concatenation)
    id5_2(1*1 conv)
    id5_3(1*1 conv)
    id5_4(3*3 conv)
    id5_5(1*1 conv)
    id5_6(5*5 conv)
    id5_7(1*1 conv)
    id5_8(3*3 pooling)
    id5_9(Previous layer)
    style id5_1 fill: #B9CF9320, stroke: #B9CF93, stroke-width:2px
    style id5_9 fill: #B9CF9320, stroke: #B9CF93, stroke-width:2px
    style id5_2 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id5_4 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id5_6 fill: #3991B620, stroke: #3991B6, stroke-width:2px
    style id5_3 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id5_5 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id5_7 fill: #FFFF6940, stroke: #FFFF69, stroke-width:2px
    style id5_8 fill: #CFA3A420, stroke: #CFA3A4, stroke-width:2px
    subgraph block_1
    id4_9 --> id4_2
    id4_2 --> id4_1
    id4_9 --> id4_3
    id4_3 --> id4_4
    id4_4 --> id4_1
    id4_9 --> id4_5
    id4_5 --> id4_6
    id4_6 --> id4_1
    id4_9 --> id4_8
    id4_8 --> id4_7
    id4_7 --> id4_1
    id5_9 --> id5_2
    id5_2 --> id5_1
    id5_9 --> id5_3
    id5_3 --> id5_4
    id5_4 --> id5_1
    id5_9 --> id5_5
    id5_5 --> id5_6
    id5_6 --> id5_1
    id5_9 --> id5_8
    id5_8 --> id5_7
    id5_7 --> id5_1
    id4_1 --> id5_9
    end
    id1 --> id2_9
    id3_1 --> id4_9
    id6(global avgpool)
    style id6 fill: #CFA3A420, stroke: #CFA3A4, stroke-width:2px
    id7(Dense 10)
    style id7 fill: #B9CF9320, stroke: #B9CF93, stroke-width:2px
    id5_1 --> id6
    id6 --> id7
</div>

![InceptionNet](/images/posts/CNNmodels/InceptionNet.png)

## 5、ResNet

将前面的输出特征结果越过对叠层直接传递到后面，并与堆叠卷积的非线性输出叠加，有效的缓解了神经网络模型堆叠导致的退化

<div class="mermaid">
    graph TB
        id0(( ))
        id1(3X3conv, filters=512<br/>strides=2)
        id2(3X3conv, filters=512)
        id3(("H(x)"))
        id4(3X3conv, filters=512)
        id5(3X3conv, filters=512)
        id6(("H(x)"))
        subgraph  
        id0 --> id1
        id1 --> id2
        id2 -->|"F(x)"| id3
        end
        subgraph  
        id3 --> id4
        id4 --> id5
        id5 -->|"F(x)"| id6
        id0 -."W(x)".-> id3
        id3 -->|x| id6
        end
</div>


其中，虚线表示维度不同时，此时H(x)=F(x)+W(x)，其中W(x)是1*1卷积操作，用于调整x的维度

实线表示维度相同时，此时H(x)=F(x)+x

![ResNet](/images/posts/CNNmodels/ResNet.png)

