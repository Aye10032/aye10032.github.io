---

layout: post
title: "unity 粒子效果笔记"
date: 2023-01-21 10:34:00
updated: 2023-01-21 12:18:00
excerpt: "unity 粒子效果模块相关"
categories: 
- 学习
- unity
tag: 
- 学习
- unity
typora-root-url: ..
---





# 一、各个模块

## 1、主模块

{% note info%}
与粒子初始状态有关
{% endnote %}

|       名称        |                             作用                             |                   其它                   |
| :---------------: | :----------------------------------------------------------: | :--------------------------------------: |
|     Duration      |                    整个粒子效果的运行时间                    |                                          |
|      Looping      |                         是否循环播放                         |                                          |
|      Prewarm      |           预加载（粒子不用先从<br>无到有发射出来）           |         只有Looping为true时有效          |
|    Start Delay    |           发射延迟，将**整个粒子运行时间**向后推迟           |                                          |
|  Start Lifetime   |                    单个粒子的初始生命周期                    |             会被其它设置覆盖             |
|    Start Speed    |                      粒子的初始发射速度                      |                                          |
|   3D Start Size   | **单个粒子**的初始缩放，高级的<br/>Start Size，可以调整三个轴向上的缩放 |             与Start Size互斥             |
|    Start Size     |                    **单个粒子**的初始缩放                    |             会被其它设置覆盖             |
| 3D Start Rotation | **单个粒子**的初始旋转角度，高级的<br/>Start Rotation，可以调整三个轴向上的旋转 |           与Start Rotation互斥           |
|  Start Rotation   |                  **单个粒子**的初始旋转角度                  |             会被其他设置覆盖             |
|   Flip Rotation   |   粒子的翻转，取值在0~1之间，<br>代表有百分之几的粒子翻转    |                                          |
|    Start Color    |                        粒子的初始颜色                        | 只有render中材质为<br>粒子材质时才会生效 |
| Gravity Modifier  |                          粒子的重力                          |                                          |
| Simulation Space  |                        粒子的参考坐标                        |                                          |
| Simulation Speed  |                    整个粒子系统的更新速度                    |                                          |
|    Delta Time     |        粒子系统是否受到Time类中时间<br>流速控制的影响        |                                          |
|   Scaling Mode    |                        粒子的缩放模式                        |                                          |
|   Play on Awake   | 粒子效果在创建时就进行播放<br>（不选中则需要相关操作来激活效果） |                                          |
| Emitter Velocity  |                  使用什么组件来计算粒子速度                  |                                          |
|   Max Particles   |                  当前系统内粒子数量的最大值                  |                                          |
| Auto Random Seed  |                     是否设置random的种子                     |                                          |
|    Stop Action    |                       粒子结束后的操作                       |                                          |
|   Culling Mode    |                  当粒子不在摄像机内时的操作                  |                                          |
| Ring Buffer Mode  |                      与粒子消除逻辑相关                      |                                          |



### Start Size

- **Constant**：固定大小
- **Curve**：大小为与生命周期有关的曲线
- **Random Between Two Constant**：在固定值之间随机
- **Random Between Two Curve**：在不同函数关系之间随机



### Start Color

- **Color**：单一颜色

- **Gradient**：可以选择渐变色，则初始颜色会在渐变色之间**顺序生成**
- **Random Between Two Colors**：在两个颜色中随机
- **Random Between Two Gradient**：在两个渐变色中随机
- **Random Color**：可以选择渐变色，则初始颜色会在渐变色之间**随机生成**



###  Simulation Space

- **Local**：影响所有粒子（整坨粒子一起动）
- **World**：只影响当前发出的粒子（尾迹效果）
- **Custom**：绑定在某个物体上



### Scaling Mode

- **Hierarchy**：与父类一同缩放
- **Local**：忽略父类，对**单个粒子**和shape同时进行缩放
-  **Shape**：仅对shape进行缩放



### Stop Action

- **Disable**：禁用
- **Destroy**：销毁
- **Callback**：触发回调事件



### Culling Mode

- **Automatic**：如果是单次播放则不管，若果是循环播放则暂停
- **Pause And Catch-up**：暂停渲染但是继续计算位置，当再次出现时继续渲染
- **Pause**：暂停
- **Always Simulate**：不暂停



### Ring Buffer Mode

- **Disabled**：按照生命周期删除粒子
- **Pause Until Replaced、Loop Until Replaced**：不删除粒子，而是按照粒子最大数量进行新老替换等操作



# 二、相关技巧

## 不选中也持续播放粒子特效

将Simulate Layers改为EveryThing

![](/images/posts/unityParticle/粒子特效显示.png)

