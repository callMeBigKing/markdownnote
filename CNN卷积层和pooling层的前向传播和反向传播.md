---
title: CNN卷积层和pooling层的前向传播和反向传播
tags: 新建,模板,小书匠
grammar_abbr: true
grammar_table: true
grammar_defList: true
grammar_emoji: true
grammar_footnote: true
grammar_ins: true
grammar_mark: true
grammar_sub: true
grammar_sup: true
grammar_checkbox: true
grammar_mathjax: true
grammar_flow: true
grammar_sequence: true
grammar_plot: true
grammar_code: true
grammar_highlight: true
grammar_html: true
grammar_linkify: true
grammar_typographer: true
grammar_video: true
grammar_audio: true
grammar_attachment: true
grammar_mermaid: true
grammar_classy: true
grammar_cjkEmphasis: true
grammar_cjkRuby: true
grammar_center: true
grammar_align: true
grammar_tableExtra: true
---

> 本文只包含CNN的前向传播和反向传播，主要是卷积层和pool层的前向传播和反向传播，一些卷积网络的基础知识不涉及



## 符号表示
**如果$l$层是卷积层：**

$f^{[l]}$: filter size
$p^{[l]}$: padding
$s^{[l]}$: stride
$n_c^{[l]}$ : number of  filters

==fliter size==:$$k_1^{[l]} \times k_2^{[l]}\times n_c^{[l-1]}$$ 
==Weights==: $W^{[l]}$ size is  $f^{[l]} \times f^{[l]} \times n_c^{l - 1} \times n_c^{l}$
==bais==: $b^{[l]}$ size is $n_{c}^{[l]}$
==liner==: $z^{[l]}$,size is $n_h^{[l]} \times n_w^{[l]} \times n_c^{[l ]}$
==Activations==: $a^{[l]}$  size is $n_h^{[l]} \times n_w^{[l]} \times n_c^{[l ]}$


==input==: $a^{[l-1]}$ size is $n_h^{[l-1]} \times n_w^{[l-1]} \times n_c^{[l - 1]}$
==output==: $a^{[l]}$  size is $n_h^{[l]} \times n_w^{[l]} \times n_c^{[l ]}$

$n_h^{[l]}$和$n_h^{[l-1]}$两者满足：
$$n_h^{[l]}({s^{[l]}} - 1) + {f^{[l]}} \leqslant n_h^{[l - 1]} + 2p$$
$$n_h^{[l]} = \left\lfloor {\frac{{n_h^{[l - 1]} + 2p - {f^{[l]}}}}{s} + 1} \right\rfloor $$
符号$\left\lfloor {x} \right\rfloor$表示向下取整，$n_w^{[l]}$和$n_w^{[l-1]}$两者关系同上

![enter description here](https://www.github.com/callMeBigKing/story_writer_note/raw/master/小书匠/1535388857043.png)

## Cross-correlation与Convolution

> 很多文章或者博客中把Cross-correlation(互相关)和Convolution(卷积)都叫卷积，把互相关叫做翻转的卷积，在我个人的理解里面两者是有区别的，本文将其用==两种表达式分开表示==，不引入翻转180度。

### Cross-correlation

对于大小为$h \times w$图像$I$和 大小为$(k_1 \times k_2)$kernel $K$,定义其Cross-correlation：

$${(I \otimes K)_{ij}} = \sum\limits_{m = 0}^{{k_1} - 1} {\sum\limits_{n = 0}^{{k_2}} {I(i + m,j + n)} } K(m,n)$$

其中

$$0 \leqslant i \leqslant h - {k_1} + 1$$
$$0 \leqslant j \leqslant w - {k_2} + 1$$

注意这里的i的范围，不考虑padding的话Cross-correlation会产生一个较小的矩阵

![Cross-correlation](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/20161013103614788.gif )

### Convolution
首先回顾一下连续函数的卷积和一维数列的卷积分别如下，卷积满足交换律：
$$h(t) = \int_{ - \infty }^\infty  {f(\tau )g(t - \tau )d\tau } $$

$$c(i) = \sum\limits_{i =  - \infty }^\infty  {a(i)b(n - i)di} $$

对于大小为$h \times w$图像$I$和大小为$(k_1 \times k_2)$kernel $K$,convolution为 :
$${(I * K)_{ij}} = {(K * I)_{ij}} = \sum\limits_{m = 0}^{{k_1} - 1} {\sum\limits_{n = 0}^{{k_2} - 1} {I(i - m,j - n)k(m,n)} } $$
$$\eqalign{
  & 0 \leqslant i \leqslant h + {k_1} - 1  \cr 
  & 0 \leqslant j \leqslant w + {k_2} - 1 \cr} $$

==注意==：这里的Convolution和前面的cross-correlation是不同的:
1.  $i,j$范围变大了，卷积产生的矩阵size变大了
2.  这里出现了很多$I(-x,-y)$,这些负数索引可以理解成padding


缺少一幅图回去画


如果把卷积的padding项扔掉那么就变成下图这样，此时Convolution和Cross-correlation相隔的就是一个180度的翻转

![卷积](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1535634166234.png)

两者在有padding的时候是相等的。


**卷积核旋转180度**
[参考自卷积核翻转方法](https://blog.csdn.net/lucky_yw/article/details/80077443)
翻转卷积核有三种方法，具体步骤移步卷积核翻转方法
1. 围绕卷积核中心旋转180度 （奇数行列好使）

2. 沿着两条对角线翻转两次

3. 同时翻转行和列 （偶数行列好使）




## 前向传播

### 卷积层

前向传播：计算$z^{[l]}$和$a^{[l]}$

**输入**：$a^{[l-1]}$ size is $n_h^{[l-1]} \times n_w^{[l-1]} \times n_c^{[l - 1]}$

**输出**：$a^{[l]}$



为了方便后续的反向传播的方便，只讨论$l$层的参数，把部分上标$[l]$去掉，同时另$n_c^{[l]}=n_c^{[l-1]}=1$，前向传播公式如下：

$$\eqalign{
  {z^l}(i,j) =  & \sum\limits_{m = 0}^{{k_1} - 1} {\sum\limits_{n = 0}^{{k_2} - 1} {a_{i + m,j + n}^{l - 1} \times } {w_{m,n}}}  + b \cr 
  {a^l} =  & g({z^l}) \cr} $$


----------
**考虑多个通道**

用$z_{{c^{[l]}}}^{[l]}$表示$z^{[l]}$的第$c^{[l]}$个channel：

$$\eqalign{
  & z_{{c^{[l]}}}^{[l]} (i,j)= \sum\limits_{{c^{[l - 1]}} = 0}^{n_c^{l - 1} - 1} {(a_{{c^{[l - 1]}}}^{[l-1]} \otimes W_{{c^{[l - 1]}},{c^{[l]}}}^{[l]})}  + b_{{c^{[l]}}}^{[l]}  \cr 
  &  = \sum\limits_{{c^{[l - 1]}} = 0}^{n_c^{l - 1} - 1} {(\sum\limits_{m = 0}^{{f^{[l]}} - 1} {\sum\limits_{n = 0}^{{f^{[l]}} - 1} {a_{{c^{[l - 1]}}}^{[l-1]}(i + m,j + n) \times } W_{{c^{[l - 1]}},{c^{[l]}}}^{[l]}(m,n)} )}  + b_{{c^l}}^{[l]} \cr} $$

其中，${W_{{c^{[l - 1]}},{c^{[l]}}}^{[l]}}$为$f^{[l]} \times f^{[l]}$的卷积核${W_{{c^{[l - 1]}},{c^{[l]}}}^{[l]}}={{W^{[l]}}(:,:,{c^{[l - 1]}},{c^{[l]}})}$

$$a_{{c^{[l]}}}^{[l]} = g(z_{{c^{[l]}}}^{[l]})$$

$g(x)$为激活函数


----------


**考虑padding和stride情况下：**

$$z_{{c^{[l]}}}^{[l]}(i,j) = \sum\limits_{{c^{[l - 1]}} = 0}^{n_c^{l - 1} - 1} {(\sum\limits_{m = 0}^{{f^{[l]}} - 1} {\sum\limits_{n = 0}^{{f^{[l]}} - 1} {{a^{[l-1]}}(i*s + m - p,j*s + n - p,{c^{[l - 1]}}) \times } {W^{[l]}}(m,n,{c^{[l - 1]}},{c^{[l]}})} )}  + b_{{c^l}}^{[l]}$$

$$a_{{c^{[l]}}}^{[l]} = g(z_{{c^{[l]}}}^{[l]})$$

$a^{[l]}$索引越界部分表示padding，其值为0



### pooling层
pooling层进行下采样，maxpool可以表示为：

$${a^l}(i,j) = \mathop {\max }\limits_{0 \leqslant m \leqslant {k_1} - 1,0 \leqslant n \leqslant {k_2} - 1} ({a^{l - 1}}(i{\text{*}}{k_1} + m,j{\text{*}}{k_2} + n))$$

avepool可以表示为：
$${a^l}(i,j) = \frac{1}{{{k_1} \times {k_2}}}\sum\limits_{m = 0}^{{k_1} - 1} {\sum\limits_{n = 0}^{{k_2} - 1} {{a^{l - 1}}(i{\text{*}}{k_1} + m,j{\text{*}}{k_2} + n)} } $$

![pooling层](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1535636592185.png)


## 反向传播

### 卷积层的反向传播

**1. 已知$\frac{{\partial E}}{{\partial {z^l}}}$求$\frac{{\partial E}}{{\partial {w^l}}}$**

![关联关系](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1535637686290.png)

由上图可知$W$对每一个元素都有贡献，（偷来的图，用的符号不一致），使用链式法则有：

$$\frac{{\partial E}}{{\partial w_{m',n'}^l}} = \sum\limits_{i = 0}^{n_h^l - 1} {\sum\limits_{j = 0}^{n_w^l - 1} {\frac{{\partial E}}{{\partial z_{i,j}^l}}} } \frac{{\partial z_{i,j}^l}}{{\partial w_{m',n'}^l}}$$

$$\frac{{\partial z_{i,j}^l}}{{\partial w_{m',n'}^l}} = \frac{{\partial \left( {\sum\limits_{m = 0}^{{k_1} - 1} {\sum\limits_{n = 0}^{{k_2} - 1} {a_{i + m,j + n}^{l - 1} \times } {w_{m,n}}}  + b} \right)}}{{\partial w_{m',n'}^l}} = a_{i + m',j + n'}^{l - 1}$$

记$\delta _{i,j}^l = \frac{{\partial E}}{{\partial z_{i,j}^l}}$，有：

$$\eqalign{
  \frac{{\partial E}}{{\partial w_{m',n'}^l}} =  & \sum\limits_{i = 0}^{n_h^l - 1} {\sum\limits_{j = 0}^{n_w^l - 1} {a_{i + m',j + n'}^{l - 1}\delta _{i,j}^l} }  \cr 
  \frac{{\partial E}}{{\partial {w^l}}} =  & {a^l} \otimes {\delta ^l} \cr} $$

**2. 根据$\frac{{\partial E}}{{\partial {z^l}}}$求$\frac{{\partial E}}{{\partial {z^{l - 1}}}}$**

![两层之间的关系](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1535676566240.png)

上图是偷来的图，把那边的$X^l$,当成$z^l$理解，与$z_{i',j'}^{l - 1}$
有关的$a_{i,j}^{l - 1}$，索引是从$(i' - {k_1} + 1,j' - {k_2} + 1)$到$(i',j')$（出现负值或者是越界当成是padding），根据链式法则：
$$\frac{{\partial E}}{{\partial z_{i',j'}^{l - 1}}} = \sum\limits_{m = 0}^{{k_1} - 1} {\sum\limits_{n = 0}^{{k_2} + 1} {\frac{{\partial E}}{{\partial z_{i' - m,j' - n}^l}}\frac{{\partial z_{i' - m,j' - n}^l}}{{\partial z_{i',j'}^{l - 1}}}} } $$

将$\frac{{\partial z_{i' - m,j' - n}^l}}{{\partial z_{i',j'}^{l - 1}}}$展开有：
$$\frac{{\partial z_{i' - m,j' - n}^l}}{{\partial z_{i',j'}^{l - 1}}} = \frac{{\partial \sum\limits_{s = 0}^{{k_1} - 1} {\sum\limits_{t = 0}^{{k_2} + 1} {z_{i' - m + s,j' - n + t}^{l - 1}w_{s,t}^l} } }}{{\partial z_{i',j'}^{l - 1}}} = w_{m,n}^l$$

从而可以得到：
$$\eqalign{
  \frac{{\partial E}}{{\partial z_{i',j'}^{l - 1}}} =  & \sum\limits_{m = 0}^{{k_1} - 1} {\sum\limits_{n = 0}^{{k_2} + 1} {\frac{{\partial E}}{{\partial z_{i' - m,j' - n}^l}}w_{m,n}^l} }  \cr 
   =  & \sum\limits_{m = 0}^{{k_1} - 1} {\sum\limits_{n = 0}^{{k_2} + 1} {\delta _{i' - m,j' - n}^lw_{m,n}^l} }  \cr 
   =  & {\delta ^l} * {W^l} \cr} $$
   
这里的$*$代表是卷积操作

### pooling层的反向传播

pooling层的反向传播比较简，没有要训练的参数

![pooling层误差传播](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1535681696763.png)

maxpool，最大的那个为1其他的均为0：

$$\eqalign{
  & \frac{{\partial E}}{{\partial a_{i',j'}^{l - 1}}} = 1\;\;if(i',j') = \arg \left( {\mathop {\max }\limits_{\left\lfloor {\frac{i}{{{k_1}}}} \right\rfloor {k_1} \leqslant i \leqslant \left\lfloor {\frac{i}{{{k_1}}}} \right\rfloor  - 1,\left\lfloor {\frac{j}{{{k_2}}}} \right\rfloor {k_2} \leqslant j \leqslant \left\lfloor {\frac{j}{{{k_2}}}} \right\rfloor  - 1} \left( {a_{i,j}^{l - 1}} \right)} \right)  \cr 
  & \frac{{\partial E}}{{\partial a_{i',j'}^{l - 1}}} = 0\;\;else \cr} $$



avepool，每个都是

$$\frac{{\partial E}}{{\partial a_{i',j'}^{l - 1}}} = \frac{1}{{{k_1} \times {k_2}}}$$
