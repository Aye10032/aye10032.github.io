---

layout: post
title: "unity 粒子效果笔记"
date: 2023-01-21 10:34:00
updated: 2023-01-27 12:24:00
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



## 4、Renderer 模块

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



## 5、Noise 模块

{% note info%}
噪声信号相关
{% endnote %}

|       名称        |               作用                |   备注   |
| :---------------: | :-------------------------------: | :------: |
|   Separate Axes   |   在各个轴上设置噪声的影响程度    |          |
|     Strength      |          粒子的波动强度           |          |
|     Frequency     |        粒子发生波动的频率         |          |
|   Scroll Speed    |        粒子波动的变化速度         |          |
|      Damping      | Strength与Frequency是否<br>成正比 |          |
|      Octaves      |          叠加几层噪声图           | 开销较大 |
| Octave Multiplier |            强度的衰减             |          |
|   Octave Scale    |            频率的衰减             |          |
|      Quality      |         波动噪声图的质量          |          |
|       Remap       |      将最终的噪声值重新映射       |          |
|    Remap Curve    |    生命周期与噪声值的关系曲线     |          |
|  Position Amount  |        噪波值是否影响位置         |          |
|  Rotation Amount  |        噪波值是否影响角度         |          |
|    Size Amount    |        噪波值是否影响缩放         |          |





## 6、lifetime 相关模块

### Velocity Over Lifetime

{% note info%}
调节粒子的速度矢量
{% endnote %}

|     名称      |          作用          |         备注          |
| :-----------: | :--------------------: | :-------------------: |
|    Linear     |     各轴向上的速度     | 可以设定时间-速度曲线 |
|     Space     |      速度的参考系      |                       |
|    Orbital    |  设定粒子绕坐标轴旋转  |                       |
|    Offset     | 设定例子对坐标轴的便宜 |                       |
|    Radial     |     粒子远离的速度     |                       |
| Speed Monitor |  对上述参数的乘积倍率  |                       |

#### Space

- **Local**：局部坐标系，受transfor中设置影响
- **World**：世界坐标，固定不变



### Limit Velocity Over Lifetime

{% note info%}
控制粒子的最大速度
{% endnote %}

|         名称         |              作用              |        备注        |
| :------------------: | :----------------------------: | :----------------: |
|    Separate Axes     |         各轴向上的速度         |                    |
|        Speed         |   粒子在生命周期内的最大速度   |                    |
|        Dampen        |  当前速度衰减到最大数的的参数  | 当超过Speed时生效  |
|         Drag         |            空气阻力            | 与其他变量独立作用 |
|   Multiply By Size   | 尺寸更大的粒子受到阻力影响更大 |                    |
| Multiply By Velocity | 速度更大的粒子受到阻力影响更大 |                    |



### Force Over Lifetime

{% note info%}
生命周期中力对粒子作用的影响
{% endnote %}



|   名称    |        作用        |               备注                |
| :-------: | :----------------: | :-------------------------------: |
|  X、Y、Z  | 各个轴向上施加的力 |                                   |
|   Space   |       参考系       |                                   |
| Randomize | 在每一帧上进行随机 | 仅当X、Y、Z存在多个选择<br>时有用 |



### Color Over Lifetime

{% note info%}
粒子颜色随生命周期变化相关
{% endnote %}

渐变色的色条，上面的滑块调整透明的，下面的滑块调整颜色



### Color By Speed

{% note info%}
粒子颜色随速度变化相关
{% endnote %}

| 名称  |         作用         | 备注 |
| :---: | :------------------: | :--: |
| Color |                      |      |
| Speed | 速度与颜色的映射范围 |      |



### Size Over Lifetime

{% note info%}
粒子尺寸随生命周期变化相关
{% endnote %}

|     名称      |      作用      | 备注 |
| :-----------: | :------------: | :--: |
| Separate Axes | 指定缩放坐标轴 |      |
|     Size      | 速度——时间曲线 |      |



### Size By Speed

{% note info%}
粒子尺寸随速度变化相关
{% endnote %}

|     名称      |         作用         | 备注 |
| :-----------: | :------------------: | :--: |
| Separate Axes |    指定缩放坐标轴    |      |
|     Size      |    速度——时间曲线    |      |
|     Speed     | 速度与颜色的映射范围 |      |



### Rotation Over Lifetime

{% note info%}
粒子角度随生命周期变化相关
{% endnote %}

|       名称       |       作用       | 备注 |
| :--------------: | :--------------: | :--: |
|  Separate Axes   |  指定旋转坐标轴  |      |
| Angular Velocity | 角速度——时间曲线 |      |



### Rotation By Speed

{% note info%}
粒子角度随速度变化相关
{% endnote %}

|       名称       |          作用          | 备注 |
| :--------------: | :--------------------: | :--: |
|  Separate Axes   |     指定旋转坐标轴     |      |
| Angular Velocity |    角速度——时间曲线    |      |
|   Speed Range    | 速度与角速度的映射范围 |      |





## 7、Collision 模块

{% note info%}
粒子物理碰撞相关
{% endnote %}

|            名称             |              作用              |                        备注                         |
| :-------------------------: | :----------------------------: | :-------------------------------------------------: |
|            Type             |           碰撞的类型           |                                                     |
|            Mode             |         碰撞的游戏模式         |                 仅type为world时有效                 |
|        Visualization        |         可视化显示类型         |                仅type为planes时有效                 |
|         Scale Plane         |          可视化的缩放          |                仅type为planes时有效                 |
|           Dampen            |    粒子碰撞后损失的速度比例    |                                                     |
|           Bounce            |    粒子碰撞后反弹的速度衰减    |                                                     |
|        Lifetime Loss        |      碰撞后生命周期的衰减      |                                                     |
|       Min Kill Speed        |       消失的最低速度阈值       |                                                     |
|       Max Kill Speed        |       消失的最大速度阈值       |                                                     |
|        Radius Scale         |           碰撞箱大小           |                                                     |
|      Collision Quality      |            碰撞质量            |                 仅type为world时有效                 |
|        Collides With        |       与哪些图层发生碰撞       |                 仅type为world时有效                 |
|    Max Collision Shapes     | 粒子最多可以与多少个面发生碰撞 |                 仅type为world时有效                 |
|  Enable Dynamic Colliders   |     粒子是否与刚体发生碰撞     |                 仅type为world时有效                 |
|       Collider Force        |    粒子与刚体碰撞时的作用力    | 仅type为world，且<br>Enable Dynamic Colliders时有效 |
| Multiply by Collision Angle |                                |                                                     |
| Multiply by Particle Speed  |                                |                                                     |
|  Multiply by Particle Size  |                                |                                                     |
|   Send Collision Message    |          发送碰撞事件          |                                                     |
|      Visualize Bounds       |        可视化例子碰撞箱        |                                                     |



### Type

- **Planes**：可以指定碰撞对象
- **World**：会与所有带有碰撞箱的物体发生碰撞



### Visualization

- **Solid**：平面
- **Grid**：网格

## 8、Trigger 模块

{% note info%}
与触发相关
{% endnote %}

|       名称       |       作用       | 备注 |
| :--------------: | :--------------: | :--: |
|    Colliders     |    碰撞体对象    |      |
|      Inside      |     在内部时     |      |
|     Outside      |     在外部时     |      |
|      Enter       |      进入时      |      |
|       Exit       |      离开时      |      |
|   Radius Scale   |    碰撞箱大小    |      |
| Visualize Bounds | 可视化例子碰撞箱 |      |

### 事件类型

- **Kill**：杀死
- **Ignore**：忽略
- **Callback**：回调函数



## 9、Light 模块

{% note info%}
与粒子光效有关的模块
{% endnote %}

|          名称           |            作用            | 备注 |
| :---------------------: | :------------------------: | :--: |
|          Light          |          光照效果          |      |
|          Ratio          |      粒子有光效的概率      |      |
|   Random Distribution   |     Ratio是否随机生效      |      |
|   Use Particle Color    |     是否使用粒子的颜色     |      |
|   Size Affects Range    | 光照范围是否受粒子尺寸影响 |      |
| Alpha Affects Intensity |  光照强度是否受透明度影响  |      |
|    Range Multiplier     |      光照范围变化曲线      |      |
|  Intensity Multiplier   |      光照强度变化曲线      |      |
|     Maximum Lights      |          最大光强          |      |



## 10、Trails 模块

{% note info%}
与粒子尾迹有关的模块
{% endnote %}

|            名称             |                 作用                 |               备注               |
| :-------------------------: | :----------------------------------: | :------------------------------: |
|            Mode             |               尾迹模式               |                                  |
|            Ratio            |           粒子有尾迹的比例           |     仅当Particle Mode时生效      |
|          Lifetime           | 尾迹的生命周期（粒子生命周期的几倍） |     仅当Particle Mode时生效      |
|   Minimum Vertex Distance   |        轨迹曲线顶点的采样距离        |     仅当Particle Mode时生效      |
|         World Space         |     轨迹是否跟随粒子源移动而移动     |     仅当Particle Mode时生效      |
|      Die With Particle      |       尾迹是否随粒子消亡而消亡       |     仅当Particle Mode时生效      |
|        Ribbon Count         |               丝带数量               |      仅当Ribbon Mode时生效       |
|  Split Sub Emitter Ribbons  |          与子发射器相关设置          |      仅当Ribbon Mode时生效       |
| Attach Ribbons to Transform |        丝带是否连接到发射原点        |      仅当Ribbon Mode时生效       |
|        Texture Mode         |             纹理映射模式             |                                  |
|     Size Affects Width      |      轨迹宽度是否受粒子宽度影响      |                                  |
|    Size Affects Lifetime    |    轨迹生命周期是否受粒子尺寸影响    |                                  |
|   Inherit Particle Color    |         轨迹是否继承粒子颜色         |                                  |
|     Color Over Lifetime     |            颜色——时间曲线            |    仅轨迹不继承粒子颜色时生效    |
|      Width Over Trail       |            宽度——长度曲线            | 仅轨迹宽度不受粒子宽度影响时生效 |
|     Color Over Lifetime     |            颜色——长度曲线            |    仅轨迹不继承粒子颜色时生效    |
|   Generate Lighting Data    |                                      |                                  |
|         Shadow Bias         |               阴影偏差               |                                  |



### Mode

- **Particle**：每个粒子一条尾迹
- **Ribbon**：粒子之间连成一条



### Texture Mode

- **Stretch**：拉伸
- **Tile**：平铺
- **Repeat per Segment**：按照宽度平铺
- **Distribute per Segment**：按照宽度拉伸





# 二、相关技巧

## 不选中也持续播放粒子特效

将Simulate Layers改为Everything

![](/images/posts/unityParticle/粒子特效显示.png)

