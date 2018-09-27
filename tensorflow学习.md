---
title: tensorflow学习
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---

> 这次的主要目的是把官网的教程全部过一遍

### loss

tf.losses.sparse_softmax_cross_entropy

tf.losses后面还有很多的loss

### 优化方法
tf.train后面

SGD如下
``` python
optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)
```

### 一些训练的时候常用的参数意义

==epochs==：数据集合训练次数


### 评估


## 可视化画图界面TensorBoard 

