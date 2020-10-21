---

layout: post

title: "汤家凤概率论笔记2.1"

date: 2019-11-01

tag: 学习笔记

---



# 第二章 一维随机变量及其分布

<iframe style="min-width: 500px;min-height: 300px"   src="https://www.acfun.cn/player/ac11364026" id="ACFlashPlayer-re"  scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"></iframe>



## 2.1 随机变量及分布



### 一、随机变量

Ω为随机试验E的样本空间，如果∀A⊆Ω，∃\| X(ω)[^1] 与 ω对应，称X=X(ω)为随机变量。

> 如：
>
> ①：扔硬币，Ω={正面、反面}
>
> {X=-1}={反面}，{X=1}={正面}
>
> ②：掷骰子，Ω={1,2,3,4,5,6}
>
> {X=i}={朝上的数为i} (1≤i≤6)
>
> {X≤0} = Ø
>
> {X≤6} = Ω

即将事件结果与取值一一对应，此时X就是随机变量。

随机变量X的取值范围本质上就是随机事件



### 二、分布函数

X为**随机变量（r.v.）**[^2]，定义P{X≤x}为F(x)，(-∞≤ax≤+∞)，则F(x)即为**分布函数**。



> 例1：随机变量X可能的取值为0、1、2，对应的概率分别为¼，½，¼，求x的分布函数F(x)。
>
> 解：F(x)=P{X≤x}
>
> ​		x<0时，F(x) = 0；
>
> ​		0≤x<1时，F(x) = P{x=0} = 1/4;
>
> ​		1≤x<2时，F(x) = P{x=0} + P{x=1}= 3/4;
>
> ​		x≥2时，F(x) = 1;



#### 性质：

1. F(x)的取值范围：0≤F(x)<1；
2. F(x)单调不减；
3. F(x)右连续 ，右极限等于函数值，即：![](https://latex.codecogs.com/gif.latex?%5Clim_%7Bx%5Crightarrow%20x_%7B0%5E%7B&plus;%7D%7D%7DF%28x%29%20%3D%20F%28x_%7B0%7D%29)；
4. F(-∞) = 0，F(∞) = 1



> **Notes：**
>
> ①、P{X<x} = F(x-0)；
>
> ②、P{X=x} = P{X≤x} - P{X<x} = F(x) - F(x-0)；
>
> ③、P{a<x≤b} = P{X≤b} - P{X≤a} = F(a) - F(b)



### 三、离散型随机变量及分布律

#### （一）离散型随机变量

Ω为随机试验E的**样本空间**，X为Ω上的**随机变量**。若X的可能取值是有限个或可列个，则称X为**离散型随机变量**。

#### （二）分布律

设X为离散型随机变量，可能取值为X<sub>1</sub>、X<sub>2</sub>、......、X<sub>n</sub>、......，其对应的概率分别为P<sub>1</sub>、P<sub>2</sub>、......、P<sub>n</sub>、......，

即P{X=x<sub>i</sub>} = P<sub>i</sub>，i=1、2、... 、n、...

或：

|  X   | X<sub>1</sub> | X<sub>2</sub> | ...  | X<sub>n</sub> | ...  |
| :--: | :-----------: | :-----------: | :--: | :-----------: | :--: |
|  P   | P<sub>1</sub> | P<sub>2</sub> | ...  | P<sub>n</sub> | ...  |

这个式子或表格就被称为**分布律**



> **Notes：**
>
> 设X的分布律为：P{X=x<sub>i</sub>} = P<sub>i</sub>，i=1、2、... 、n、...，则：
>
> ①、P<sub>i</sub>≥0，i=1、2、... 、n、...；
>
> ②、P<sub>1 </sub>+ P<sub>2 </sub>+ ... + P<sub>n </sub> + ... = 1；



> 例：X的分布律为：
>
> |  X   |  -1  |  0   |  1   |  2   |
> | :--: | :--: | :--: | :--: | :--: |
> |  P   | 1/4  | 1/4  | 1/4  | 1/4  |
>
> 求：
>
> 1）P{X≤0} 
>
> 2）F(x)
>
> 解：1）P{X≤0} = P{X=-1} + P{X=0} = 1/2
>
> ​		2）F(x)=P{X≤x}
>
> ​		x<-1时，F(x) = 0；
>
> ​		-1≤x<0时，F(x) = 1/4;
>
> ​		0≤x<1时，F(x) = 1/2;
>
> ​		1≤x<2时，F(x) = 3/4;
>
> ​		x≥2时，F(x) = 1;



### 四、连续型随机变量及密度函数

Ω为随机试验E的**样本空间**，X为Ω上的**随机变量**。F(x)=P{X≤x}为分布函数，若∃ f(x)≥0，使得

![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?%5Cint_%7B-%5Cinfty%20%7D%5E%7Bx%7Df%28t%29dt%20%3D%20F%28x%29)，则称X为**连续型随机变量**，f(x)称为X的**密度函数**。



> **Notes：**
>
> 设X为连续型随机变量，其密度函数为f(x)，则：
>
> ①、f(x) ≥ 0；
>
> ②、![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?%5Cint_%7B-%5Cinfty%20%7D%5E%7B&plus;%5Cinfty%7Df%28x%29dx%20%3D%201)



> 例：设连续型随机变量X的密度为
>
> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?f%28x%29%3D%5Cleft%5C%7B%5Cbegin%7Bmatrix%7D%202x%2Cx%5Cin%20%280%2C1%29%5C%5C%200%2Cx%5Cin%28-%5Cinfty%20%2C0%5D%5Ccup%20%5B1%2C&plus;%5Cinfty%29%20%5Cend%7Bmatrix%7D%5Cright.)
>
> 求：1）P{\|X\|<1/3}
>
> ​		2）F(x)
>
> 解：1）
>
> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?P%5Cleft%20%5C%7B%5Cvert%20X%20%5Cvert%20%3C%20%5Cfrac%7B1%7D%7B3%7D%5Cright%20%5C%7D%20%3D%20P%5Cleft%20%5C%7B%20-%5Cfrac%7B1%7D%7B3%7D%3CX%3C%5Cfrac%7B1%7D%7B3%7D%20%5Cright%20%5C%7D)
>
> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?%3D%20%5Cint_%7B-%5Cfrac%7B1%7D%7B3%7D%7D%5E%7B%5Cfrac%7B1%7D%7B3%7D%7Df%28x%29dx%20%3D%20%5Cint_%7B0%7D%5E%7B%5Cfrac%7B1%7D%7B3%7D%7Df%28x%29dx)
>
> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?%3D%20%5Cint_%7B0%7D%5E%7B%5Cfrac%7B1%7D%7B3%7D%7D2xdx%20%3D%20%5Cfrac%7B1%7D%7B9%7D)
>
> ​		2）
>
> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?F%28x%29%20%3D%20%5Cint_%7B-%5Cinfty%20%7D%5E%7Bx%7Df%28x%29dx)

> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?F%28x%29%20%3D%200%2C%20x%5Cin%20%28-%5Cinfty%20%2C0%29%3B)
>
> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?F%28x%29%20%3D%20%5Cint_%7B0%7D%5E%7Bx%7D2xdx%20%3D%20x%5E%7B2%7D%2C%20x%5Cin%20%5B0%20%2C1%29%3B)
>
> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?F%28x%29%20%3D%20%5Cint_%7B-%5Cinfty%20%7D%5E%7Bx%7Df%28x%29dx)
>
> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?%3D%20%5Cint_%7B0%7D%5E%7Bx%7Df%28x%29dx)
>
> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?%3D%20%5Cint_%7B0%7D%5E%7B1%7D2xdx%20%3D%201%2Cx%20%5Cin%5B1%2C&plus;%5Cinfty%20%29)
>
> ​			∴  
>
> ![This is the rendered form of the equation. You can not edit this directly. Right click will give you the option to save the image, and in most browsers you can drag the image onto your desktop or another program.](https://latex.codecogs.com/gif.latex?F%28x%29%3D%5Cleft%5C%7B%5Cbegin%7Bmatrix%7D%200%2Cx%5Cin%28-%5Cinfty%20%2C0%29%5C%5C%20x%5E%7B2%7D%2Cx%5Cin%5B0%2C1%29%5C%5C%201%2Cx%5Cin%5B1%2C&plus;%5Cinfty%29%20%5Cend%7Bmatrix%7D%5Cright.)
>
> 

------

[^1]: 总存在唯一的X(ω)
[^2]: random variable，随机变量的缩写

