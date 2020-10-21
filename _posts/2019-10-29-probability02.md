---

layout: post

title: "汤家凤概率论笔记1.2"

date: 2019-10-29

tag: 学习笔记
typora-root-url: ..
---



# 第一章 事件与概率

[视频](https://www.acfun.cn/v/ac11364026_2)

## 1.2 概率及其基本公式



### 一、概率的定义

E为随机试验，Ω为样本空间，∀A⊆Ω[^1]

定义P(A)，若A满足：

1. ∀A⊆Ω，有P(A)≥0（非负性）；
2. P(Ω)=1（归一性）
3. 设A<sub>1</sub>,A<sub>2</sub>, ...... ,A<sub>n</sub>, ......两两互斥，有：
	P(A<sub>1</sub>+A<sub>2</sub>+ ...... ) = P(A<sub>1</sub>)+P(A<sub>2</sub>) + ......（可列可加性）

则称P(A)为事件A的**概率**。

> （1）、P(Ø) = 0
>
>
> >证：
> >取A<sub>1</sub>=A<sub>2</sub>=......=Ø，A<sub>1</sub>,A<sub>2</sub>, ......两两互斥，则：
> >
> >P(Ø+Ø+.......) = P(Ø)+P(Ø)+.......
> >
> > ⇒ P(Ø)=P(Ø)+P(Ø)+.......
> >
> > ∵ P(Ø)≥0
> >
> > ∴ P(Ø)=0
>
> （2）、有限可加性:
>
> 设A<sub>1</sub>,A<sub>2</sub>, ...... ,A<sub>n</sub>两两互斥，则：
>
> P(A<sub>1</sub>+A<sub>2</sub>+ ...... +A<sub>n</sub>) = P(A<sub>1</sub>)+P(A<sub>2</sub>) + ...... P(A<sub>n</sub>)
>
> > 证：
> >
> > 令A<sub>n+1</sub> = A<sub>n+2</sub> = ...... = Ø，此时
> >
> > A<sub>1</sub>,A<sub>2</sub>, ...... ,A<sub>n</sub>, ......两两互斥，则：
> >
> > P(A<sub>1</sub>+A<sub>2</sub>+ ...... +A<sub>n</sub>+ ......) = P(A<sub>1</sub>)+P(A<sub>2</sub>) + ...... P(A<sub>n</sub>) + ......
> >
> > ⇒ P(A<sub>1</sub>+A<sub>2</sub>+ ...... +A<sub>n</sub>) = P(A<sub>1</sub>)+P(A<sub>2</sub>) + ...... P(A<sub>n</sub>)
>
> （3）、补性质：
>
> A + <SPAN style="TEXT-DECORATION: overline">A</SPAN>  = Ω，且 A•<SPAN style="TEXT-DECORATION: overline">A</SPAN> = Ø，则：
>
> P(<SPAN style="TEXT-DECORATION: overline">A</SPAN>) = 1 - P(A)
>
> > 证：
> >
> > ∵  A•<SPAN style="TEXT-DECORATION: overline">A</SPAN> = Ø
> >
> > ∴  P(A + <SPAN style="TEXT-DECORATION: overline">A</SPAN>) = P(A) + P(<SPAN style="TEXT-DECORATION: overline">A</SPAN>)
> >
> > 又∵  A + <SPAN style="TEXT-DECORATION: overline">A</SPAN>  = Ω
> >
> > ∴  P(A + <SPAN style="TEXT-DECORATION: overline">A</SPAN>) = P(Ω) = 1
> >
> > ⇒ P(A) + P(<SPAN style="TEXT-DECORATION: overline">A</SPAN>) = 1
> >
> > ⇒ P(<SPAN style="TEXT-DECORATION: overline">A</SPAN>) = 1 - P(A)



### 二、概率的基本公式

![](/images/posts/probability02/AB.png)

#### 1、减法公式

>  A = (A -B) + AB，且A-B与AB互斥
>
> ∴  P(A) = P(A-B) + P(AB)
>
> ∴  P(A-B) = P(A) - P(AB)
>
> 又 A = AΩ = A(B+<SPAN style="TEXT-DECORATION: overline">B</SPAN>) = AB + A<SPAN style="TEXT-DECORATION: overline">B</SPAN>
>
> ∵ AB ⊆ B；A<SPAN style="TEXT-DECORATION: overline">B</SPAN> ⊆ <SPAN style="TEXT-DECORATION: overline">B</SPAN>
>
> ∴ AB与A<SPAN style="TEXT-DECORATION: overline">B</SPAN>互斥
>
> ⇒ P(A<SPAN style="TEXT-DECORATION: overline">B</SPAN>) = P(A) - P(AB)
>
> ∴ **P(A-B) = P(A<SPAN style="TEXT-DECORATION: overline">B</SPAN>) = P(A) - P(AB)**(减法公式)



#### 2、加法公式

> A+B = (A-B) + AB + (B-A)
>
> 且A-B，AB，B-A两两互斥
>
> ∴ P(A+B) = P(A-B) + P(AB) + P(B-A)
>
> ∵ P(A-B) = P(A) - P(AB)
>
> ​	P(B-A) = P(B) - P(AB)
>
>  ∴  **P(A+B) = P(A) + P(B) - P(AB)** ①两个事件

> **P(A+B+C) = P(A) + P(B) + P(C) - P(AB) - P(AC) - P(BC) + P(ABC)** ②三个事件



------



[^1]: 任意取样本空间的一个子集A


