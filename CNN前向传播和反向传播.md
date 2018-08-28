---
title: CNN前向传播和反向传播
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

**注**：本文只包含CNN的前向传播和反向传播，主要是卷积层和pool层的前向传播和反向传播，不过多关注整个网络结构

## 引言

本文主要引用吴恩达课程的符号

### 符号表示
**如果$l$层是卷积层：**

$f^{[l]}$: filter size
$p^{[l]}$: padding
$s^{[l]}$: stride
$n_c^{[l]}$ : number of  filters

==fliter size==: $f^{[l]} \times f^{[l]} \times n_c^{[l-1]}$
==Weights==: $W^{[l]}$ size is  $f^{[l]} \times f^{[l]} \times n_c^{l - 1} \times n_c^{l}$
==bais==: $b^{[l]}$ size is $n_{c}^{[l]}$
==liner==: $z^{[l]}$,size is $n_h^{[l]} \times n_w^{[l]} \times n_c^{[l ]}$
==Activations==: $a^{[l]}$  size is $n_h^{[l]} \times n_w^{[l]} \times n_c^{[l ]}$


==input==: $a^{[l-1]}$ size is $n_h^{[l-1]} \times n_w^{[l-1]} \times n_c^{[l - 1]}$
==output==: $a^{[l]}$  size is $n_h^{[l]} \times n_w^{[l]} \times n_c^{[l ]}$

关系式满足
$$n_h^{[l]}({s^{[l]}} - 1) + {f^{[l]}} \leqslant n_h^{[l - 1]} + 2p$$
$$n_h^{[l]} = \left\lfloor {\frac{{n_h^{[l - 1]} + 2p - {f^{[l]}}}}{s} + 1} \right\rfloor $$
符号$\left\lfloor {x} \right\rfloor$表示向下取整

![enter description here](https://www.github.com/callMeBigKing/story_writer_note/raw/master/小书匠/1535388857043.png)

### Cross-correlation
对于大小为$h \times w$图像$I$和 大小为$(k_1 \times k_2)$kernel $K$,定义其Cross-correlation(下标从0开始)：

$${(I \otimes K)_{ij}} = \sum\limits_{m = 0}^{{k_1} - 1} {\sum\limits_{n = 0}^{{k_2}} {I(i + m,j + n)} } K(m,n)$$

其中

$$0 \leqslant i \leqslant h - {k_1} + 1$$
$$0 \leqslant j \leqslant w - {k_2} + 1$$

注意这里的i的范围，这里$I$是没有padding的


### Convolution
连续函数的卷积：
$$h(t) = \int_{ - \infty }^\infty  {f(\tau )g(t - \tau )d\tau } $$

一维数组的卷积：
$$c(i) = \sum\limits_{i =  - \infty }^\infty  {a(i)b(n - i)di} $$

对于图像$I$和 大小为$(k_1 \times k_2)$kernel $K$,convolution为（卷积满足交换律，这里进行了一下交换） :
$${(I * K)_{ij}} = {(K * I)_{ij}} = \sum\limits_{m = 0}^{{k_1} - 1} {\sum\limits_{n = 0}^{{k_2} - 1} {I(i - m,j - n)k(m,n)} } $$
$$\eqalign{
  & 0 \leqslant i \leqslant h + {k_1} - 1  \cr 
  & 0 \leqslant j \leqslant w + {k_2} - 1 \cr} $$

==注意==：这里的i，j范围和前面的cross-correlation是不同的，这里出现了很多$I(-x,-y)$,这些负数可以理解成padding  0，对比两者有

$${(I * K)_{{k_1} - 1,{k_2} - 1}} = {(I \otimes K)_{0,0}}$$

不考虑卷积的padding项，那么有：

$$ro{t_{{{180}^ \circ }}}\{ K\}  \otimes I = K*I$$

如下图所示：

![卷积示意](https://www.github.com/callMeBigKing/noteimg/raw/master/小书匠/1535447636074.png)

如果扔掉卷积公式中有padding参与的项，两者的区别相当于把卷积核翻转（flip）180度

**卷积核旋转180度**
[参考自卷积核翻转方法](https://blog.csdn.net/lucky_yw/article/details/80077443)
翻转卷积核有三种方法，具体步骤移步卷积核翻转方法
1. 围绕卷积核中心旋转180度 （奇数行列好使）

2. 沿着两条对角线翻转两次

3. 同时翻转行和列 （偶数行列好使）

### 总结
在CNN领域把==Cross-correlation==叫做卷积，所以在CNN中==Cross-correlation==，叫做卷积，==数学意义上的卷积==叫做==翻转的卷积==。

## 前向传播

### 卷积层

前向传播：计算$z^{[l]}$和$a^{[l]}$

输入：$a^{[l-1]}$ size is $n_h^{[l-1]} \times n_w^{[l-1]} \times n_c^{[l - 1]}$

输出：$a^{[l]}$，$z^{[l]}$

用$z_{{c^{[l]}}}^{[l]}$表示$z^{[l]}$的第$c^{[l]}$channel：

$$\eqalign{
  & z_{{c^{[l]}}}^{[l]} (i,j)= \sum\limits_{{c^{[l - 1]}} = 0}^{n_c^{l - 1} - 1} {(a_{{c^{[l - 1]}}}^{[l]} \otimes W_{{c^{[l - 1]}},{c^{[l]}}}^{[l]})}  + b_{{c^l}}^{[l]}  \cr 
  &  = \sum\limits_{{c^{[l - 1]}} = 0}^{n_c^{l - 1} - 1} {(\sum\limits_{m = 0}^{{f^{[l]}} - 1} {\sum\limits_{n = 0}^{{f^{[l]}} - 1} {a_{{c^{[l - 1]}}}^{[l]}(i + m,j + n) \times } W_{{c^{[l - 1]}},{c^{[l]}}}^{[l]}(m,n)} )}  + b_{{c^l}}^{[l]} \cr} $$

其中，${W_{{c^{[l - 1]}},{c^{[l]}}}^{[l]}}$为$f^{[l]} \times f^{[l]}$的卷积核

$${W_{{c^{[l - 1]}},{c^{[l]}}}^{[l]}}={{W^{[l]}}(:,:,{c^{[l - 1]}},{c^{[l]}})}$$

$$a_{{c^{[l]}}}^{[l]} = g(z_{{c^{[l]}}}^{[l]})$$


### pool层



## 反向传播

