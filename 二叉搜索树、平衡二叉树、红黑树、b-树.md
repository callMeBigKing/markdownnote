---
title: 二叉搜索树、平衡二叉树、红黑树、b-树
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---


# 二叉搜索树
``` cpp
符号约定
树一般用T表示，
T.root 根节点

对已节点z
z.key 表示节点的值
z.left 表示左孩子
z.right 表示右孩子
z.parent 表示父节点，根节点的父节点为空
```
## 查找
利用二叉搜索树的性质，z.left <=z<z.right

``` python
def search(T,key):
	x=T.root
	while x!=null and x.key!=key:
		if x.ke==key:
			break;
		elif key<=x.key:
			x=x.left
		else x=x.right
	return x
```

## 排序

[参考博客](https://blog.csdn.net/ryjflyshy/article/details/78250348)

中序遍历的结果就是排序，这个用栈给出一个非递归的方法

``` python
def sort(T):
	sorted=[]
	stack=[]
	p=T.root
	while(p!=null or !stack.empty):
		
		while p!=null:
			stack.push(p)
			p=p.left
		
		p=stack.pop()
		sorted.append(p.key) # 表示访问
		p=p.right  # 空的话没有关系
		
```


## 最大元素最小元素

最左边的就是最小值，最右边的就是最大值
以某一节点为根节点的最小值和最大值
``` python
def min(z):
	x=z
	while x.left!=null:
		x=x.left
	return x
	
def max(z):
	x=z
	while x.right!=null
		x=x.right
	return x
```


## 前驱和后继
找到中须遍历下的前驱节点或者后继节点
分两种情况讨论，
 **后继**
有右孩子
: 这种情况下后继是右子树中最小的节点

没有右孩子
: 这种情况后继节点是某一个祖先节点y，节点z位于y的左子树，且y是从下到上第一个满足这种要求的


**前驱**
有左孩子
: 左子树中最大的节点

没有左孩子
: 这种情况前驱节点是某一个祖先节点y，节点z位于y的右子树，且y是从下到上第一个满足这种要求的
``` python
def next(z):
	if z.right!=null:
		return min(z.right)
	else 
		y=z.parent
		x=z
		while y!=null and x!=y.left:
			x=y
			y=y.parent
		return y

def pre(z):
	if z.left!=null:
		return max(z)
	else 
		y=z.parent
		x=z
		while y!=null and x!=y.right:
			x=y
			y=y.parent
		return y
```

## 插入
> 插入的思想就是，从根节点开始比较，比根节点大就去和左子树根节点比较，反正就去和右子树的根节点比较，直到需要比较的节点为空，这个时候插入进去。
伪代码如下

``` python

def insert(T,z):
	x=T.root
	y=null

	while x!=nulll
		y=x
		if z.key>x.key
			x=x.left
		else 
			x=x.right

	#到这里就已经找到了要插入的地方了y
	if y==null:
		T.root=z
	elif y.key<x.key:
		y.lefg=x
	else 
		y.right=x， 

```

## 删除
删除需要对结构进行调整，分以下几种情况进行讨论
1. 没有孩子节点直接删除
2. 只有一个孩子节点，将孩子节点和父节点相连接，

3. 有左右节点，那么需要 找到右子树中值最小的节点y  用其替换要删除的节点z，然后再删除节点y。   节点y是z的右子树中值最小的点，意味着y至少没有左子树，是前面两种情况之一。



``` python
def delete(T,z):
	if z.left=null or z.right=null:
		y=z
	else:
		y=next(z)
	# 这个时候 就是删除y了,y的左右孩子至少有一个是为空
	if y.left!=null;
		x=y.left
	else:
		x=y.right
	if x!=null
		x.parent=y.parent   # 完成了从下到上的连接，从上到下还没有完成 
	
	# 完成从上到下的连接
	if y==null:
		T.root=z
	else:
		if y.parent.left==y:
			y.parent.left=x
		else
			y.parent.right=x
	# 第三种情况，将y的值赋值给z
	if y!=z
		z.key=y.key
```

## 平衡二叉树

> [主要参考自](http://www.cnblogs.com/polly333/p/4798944.html)

定义
:一颗二叉树， 对于任意一个节点其左右子树的高度差不超过1，则称这颗二叉树为平衡二叉树

平衡因子
: 平衡二叉树的左子树的深度减去右子树的深度

最小不平衡子树
: 距离插入结点最近的，且平衡因子的绝对值大于1的结点为根的子树，称为最小不平衡子树


### 最小不平衡子树旋转


![四种特殊情况](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1536751332008.png)
注意着只是四种特殊情况，

``` cpp
// LL  对应图1 传入进来的是一颗子树
void R_rotate(BiTree *t)
{
         BiTree s;
         s = (*t)->lchild;                    //s指向t的左子树根结点
         (*t)->lchild = s->rchild;          //s的右子树挂接为t的左子树
         s->rchild = (*t);
         *t = s;                                //t指向新的根结点
}

// RR 对应图4
void L_rotate(BiTree *t)
{
         BiTree s;
         s = (*t)->rchild;                    //s指向t的右子树根结点
         (*t)->rchild = s->lchild;          //s的左子树挂接为t的右子树
         s->lchild = (*t);
         *t = s;                                //t指向新的根结点
}

//LR 对应图2 注意补全成完全二叉树

void L_rotate(BiTree *t)
{	 	// 记录三个关键节点  根，LR中的L 和R
		 a=t;
		 b=a->left;
		 c=b->right;
		 // a<c<b 选中间的点当根
		 a->left=c->right
		 b->right=c->left
		 c->right=a
		 c->left=b
		 retunr c
		 
}

// RL 对应图3 注意补全成完全二叉树
void R_lotate(BiTree *t)
{
		 a=t;
		 b=a->right;
		 c=b->left;
		 // a>c>b 选中间的点当根
		 a->right=c->left
		 b->left=c->right
		 c->right=b
		 c->left=a
		 retunr c
}