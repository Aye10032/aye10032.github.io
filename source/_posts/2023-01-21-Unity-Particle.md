---

layout: post
title: "unity 粒子效果笔记"
date: 2023-01-21 10:34:00
updated: 2023-01-22 11:18:00
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



## 2、Emission 模块

{% note info%}
与粒子发射有关
{% endnote %}

|        名称        |           作用           |             备注             |
| :----------------: | :----------------------: | :--------------------------: |
|   Rate Over Time   |    每秒发射多少个粒子    | 与Rate Over Distance可以混用 |
| Rate Over Distance | 每移动一米发射多少个粒子 |   与Rate Over Time可以混用   |



### Bursts

设置爆发例子

|    名称     |            作用            |             备注             |
| :---------: | :------------------------: | :--------------------------: |
|    Time     |         爆发的时间         |                              |
|    Count    |         粒子的数量         | 存活时间由Start Lifetime决定 |
|   Cycles    | 在Duration内进行多少轮发射 |                              |
|  Interval   |       周期之间的延迟       |                              |
| Probability |     每轮进行发射的概率     |                              |



## 3、Shape 模块

{% note info%}
与粒子发射体积和发射方向相关
{% endnote %}

|        名称         |             作用             |                       备注                        |
| :-----------------: | :--------------------------: | :-----------------------------------------------: |
|        Shape        |        发射体积的形状        |                                                   |
|       Texture       | 纹理，固定处于发射形状的基底 |                                                   |
|      Position       |        粒子系统的位置        |                  与transform等效                  |
|      Rotation       |        粒子系统的旋转        | 会影响Align to Direction<br>的生效，transform不会 |
|        Scale        |        粒子系统的缩放        |                  与transform等效                  |
| Align to Direction  |  粒子的方向根据初始方向前进  |                 可以被干扰项覆盖                  |
| Randomize Direction |       将粒子的方向随机       |                                                   |
| Spherize Direction  | 粒子是否被约束在发射体形状内 |                                                   |
| Randomize Position  |    相对于发射点的随机偏移    |                                                   |



### Shape

#### Sphere、Hemisphere

球体、半球体

- **Radius**：半径
- **Radius Thickness**：壁厚，0~1之
  - 0从外轮廓发射
  - 1从中心发射

- **Arc**：弧角度
  - **Mode**：发射模式
  - **Spread**

![Arc=30](/images/posts/unityParticle/arc.png)



#### Cone

圆台

![](/images/posts/unityParticle/cone.png)

- **Angle**：圆锥的角度
- **Radius**：半径
- **Radius Thickness**：壁厚，0~1之
  - 0从外轮廓发射
  - 1从中心发射
- **Arc**：弧角度
  - **Mode**：发射模式 
  - **Spread**
- **Emit Form**：粒子从锥底发射还是从整个锥体中发射



#### Mesh

从网格发送

![](/images/posts/unityParticle/mesh.png)

- **type**：发射模式，与mode类似
- **Mesh**：网格材质
- **Signal Material**：是否从一个mesh的特定材质发射
- **Use Material Color**：是否将材质顶点颜色赋值给粒子
- **Normal Offset**：粒子发射平面与网格的法线方向偏移量

 

### Texture

- **Clip Channel**：过滤使用的通道
- **Clip Threshold**：过滤阈值
- **Color affects Particles**
- **Alpha affect Particles**
- **Bilinear Filtering**：对小尺寸纹理进行优化



## 3、Renderer 模块

{% note info%}
渲染相关
{% endnote %}



|           名称           |                     作用                      |                     备注                      |
| :----------------------: | :-------------------------------------------: | :-------------------------------------------: |
|       Render Mode        |                   渲染模式                    |                                               |
|     Normal Direction     |                与照明模式相关                 | Billboard的默认朝向受<br>Render Alignment影响 |
|         Material         |                     材质                      |                                               |
|      Trial Material      |                   拖尾材质                    |                                               |
|        Sort Mode         |              粒子之间的排序模式               |                                               |
|        Sort Fudge        |            两个粒子系统之间的排序             |                  小的在前面                   |
|    Min Particle Size     | 粒子渲染的最小大小<br/>（占屏幕的百分比大小） |                                               |
|    Max Particle Size     | 粒子渲染的最大大小<br>（占屏幕的百分比大小）  |                                               |
|     Render Alignment     |                 渲染对齐方向                  |                                               |
|           Flip           |                     翻转                      |                                               |
|        Allow Roll        |                  在VR中有用                   |                                               |
|          Pivot           |          粒子渲染相对于中心点的偏移           |                                               |
|     Visualize Pivot      |               显示粒子的中心点                |                                               |
|         Masking          |                     遮罩                      |                                               |
| Apply Active Color Space |                  与光照有关                   |                                               |
|  Custom Vertex Streams   |                 自定义顶点流                  |                                               |
|       Cast Shadows       |                 是否投射阴影                  |                                               |
|       Shadow Bias        |         粒子上是否会有别的粒子的阴影          |                                               |
|      Motion Vectors      |          是否用运动矢量跟踪粒子系统           |                                               |
|     Sorting Layer ID     |                  与排序相关                   |                                               |
|      Order In Layer      |                  与排序相关                   |                                               |
|       Light Probes       |                   光照探针                    |                                               |
|    Reflection Probes     |                   反射探针                    |                                               |



### Render Mode

- **Billboard**：二维粒子材质，平面永远朝向摄像机
- **Stretched Billboard**：拉伸的材质
- **Horizontal Billboard**：二位粒子材质，永远平行于地面
- **Vertical Billboard**：二位粒子材质，永远面向摄像机且垂直于地面
- **Mesh**：三维材质



### Sort Mode

- **None**
- **By Distance**：按照距离排序，近的会覆盖远的
- **Oldest in Front**：旧的会覆盖新的
- **Youngest in Front**：新的会覆盖旧的



### Render Alignment

- **View**：朝向相机
- **World**：永远朝向世界坐标
- **Local**：朝向局部坐标方向，受transform中的rotation影响
- **Facing**：面向游戏对象的位置
- **Velocity**：面向当前速度的方向



### Masking

- **No Masking**：无遮罩
- **Visible Inside Mask**：在遮罩范围内显示
- **Visible Outside Mask**：在遮罩范围外显示





# 二、相关技巧

## 不选中也持续播放粒子特效

将Simulate Layers改为Everything

![](/images/posts/unityParticle/粒子特效显示.png)

