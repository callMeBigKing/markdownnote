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


**如果$l$层是卷积层：**

$f^{[l]}$: filter size
$p^{[l]}$: padding
$s^{[l]}$: stride
$n_c^{[l]}$ : number of  filters

fliter size: $f^{[l]} \times f^{[l]} \times n_c^{[l-1]}$
Weights: $W^{[l]}$ size is  $f^{[l]} \times f^{[l]} \times n_c^{l - 1} \times n_c^{l}$
bais: $b^{[l]}$ size is $n_{c}^{[l]}$
liner: $z^{[l]}$,size is $n_h^{[l]} \times n_w^{[l]} \times n_c^{[l ]}$
Activations: $a^{[l]}$  size is $n_h^{[l]} \times n_w^{[l]} \times n_c^{[l ]}$


input: $a^{[l-1]}$ size is $n_h^{[l-1]} \times n_w^{[l-1]} \times n_c^{[l - 1]}$
output: $a^{[l]}$  size is $n_h^{[l]} \times n_w^{[l]} \times n_c^{[l ]}$


![enter description here](https://www.github.com/callMeBigKing/story_writer_note/raw/master/小书匠/1535388857043.png)


## 前向传播
前向传播：计算$z^{[l]}$和$a^{[l]}$

前向传播的时候用的是互相关（Cross-correlation），对于图像$I$和 kernel $K$,其Cross-correlation：

$$\begin{equation} 
                   (I*K)_{ij}=
\end{equation}$$





## 反向传播