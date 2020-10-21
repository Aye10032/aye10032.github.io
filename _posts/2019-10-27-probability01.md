---

layout: post

title: "汤家凤概率论笔记1.1"

date: 2019-10-27

tag: 学习笔记
typora-root-url: ..
---



# 第一章 事件与概率

<iframe style="min-width: 500px;min-height: 300px"   src="https://www.acfun.cn/player/ac11364026" id="ACFlashPlayer-re"  scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe>


## 1.1 事件与运算



### 一、随机试验

E为一个试验，若满足：

1. 相同条件下可重复；
2. 试验的结果具有多样性，试验前所有的结果都是已知的；
3. 试验前不确定何种结果发生。

则**E称为随机试验**。

 

> 如投币、仍骰子等



### 二、样本空间

E为**随机试验**，E的所有可能的**基本结果**组成的集合，称为E的**样本空间**，记为 Ω 。



> 基本结果：指结果具有**不可再分性**
>
> 例如对于扔骰子而言，  Ω  ={1、2、3、4、5、6}，
>
> 事件A={2、4、6}，即点数为偶数，A是事件，但不是基本事件



### 三、随机事件

E为**随机试验**， Ω为E的样本空间，Ω的子集称为**随机事件**。其中：

- 对于 Ø⊆Ω，这里Ø指的是**不可能事件**；

- Ω⊆Ω ，这里 Ω 指的是**必然事件**。



### 四、事件的关系与运算

#### （一）运算

设A、B为事件

1. 和：A或B发生的事件称为A、B的**和事件**，记为A∪B或A+B：![](/images/posts/probability01/AorB.png)
2. 积：A、B同时发生的事件，称为A、B的**积事件**，记为AB或A∩B：![](/images/posts/probability01/AandB.png)
3. 差：A发生且B不发生的事件，称为A与B的**差事件**，记为A-B：![](/images/posts/probability01/AnotB.png)
4. 补：A不发生的事件，称为A的**补事件**，记为<SPAN style="TEXT-DECORATION: overline">A</SPAN>：![](/images/posts/probability01/notA.png)



#### （二）关系

设A、B为事件

1. 包含：若A发生，则B一定发生，称A包含于B，记为A⊆B：![](/images/posts/probability01/AinB.png)
2. 相等：若A发生，则B一定发生；反之，若B发生，则B也一定发生，即A⊆B且B⊆A，称为A、B相等，记为A=B
3. 互斥（不相容）：A、B不能同时发生，即AB = Ø，称为A、B**互斥**；
   - A、B互斥的充要条件：AB = Ø
4. 对立：A、B不能同时发生，且至少一个发生，称A、B**对立**。
   - A、B对立的充要条件(1)：
     - AB=Ø
     - A+B=Ω
   - A、B对立的充要条件(2):
     - B = <SPAN style="TEXT-DECORATION: overline">A</SPAN>

> A = (A-B) + AB
>
> A+B = (A-B) + AB + (B-A)

