---
title: deeplearning Codding
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


> 本文主要目的是零散的记录一下深度学习编程时学习到的技巧，主要是numpy，tensorflow，等库的使用

**==np.squeeze()==**
序列化，可以将其变成1维的tuple、二维的行向量或者列向量
``` llvm
	>>> x = np.array([[[0], [1], [2]]])
	 >>>x.shape
    (1, 3, 1)
    >>> np.squeeze(x).shape
    (3,)
    >>> np.squeeze(x, axis=0).shape
    (3, 1)
    >>> np.squeeze(x, axis=1).shape
    Traceback (most recent call last):
    ...
    ValueError: cannot select an axis to squeeze out which has size not equal to one
    >>> np.squeeze(x, axis=2).shape
    (1, 3)
```

**图片归一化**
所有的像素点除以255
图像归一化的好处，留着
``` python
X_train = X_train_orig/255.  
X_test = X_test_orig/255.
```

==one-hot==
训练集合的标签由单个的数字，转化成one-hot形式
convert_to_one_hot

``` python
def convert_to_one_hot(Y, C):
    Y = np.eye(C)[Y.reshape(-1)].T
    return Y
```
分析一下
==np.eye(N)==：生成一个单位矩阵
==np.eye(N)[np.array([1,2,3])]==：取单位矩阵的1,2,3行。这个是一种高级索引的方式




**==array.reshape==**
作用很简单的了，就是reshape，然后里面可以有-1这个参数，单填入-1时表示这个参数用程序自动其他的参数，

注意：这个地方reshape并不改变原来的array，二十返回一个新的array

``` python
>>>z = np.array([[1, 2, 3, 4],[5, 6, 7, 8],[9, 10, 11, 12],[13, 14, 15, 16]])
>>> print(z.reshape(-1)) 
[ 1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16]
>>> print(z.reshape(2,-1))  # 确定两行列数由程序自动计算
[[ 1  2  3  4  5  6  7  8]
 [ 9 10 11 12 13 14 15 16]]
```

**==tf.placeholder==**
tensorflow的占位符，在使用的时候需要使用feed把数据填充进去，一般用作x,y

==dtype==:数据类型
==shape==:维度，允许一个None，一般用作batchsize
==name==：tensorboard画图统计用的
``` python
def placeholder(dtype, shape=None, name=None)
tf.placeholder(tf.float32, shape=[None, n_H0, n_W0, n_C0])
```