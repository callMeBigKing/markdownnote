---
title: 二查搜索树红黑树AVL
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---


# 二叉搜索树
``` cpp
符号约定
树一般用T表示，
T.root 根节点

节点z
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
	while x!=None and x.key!=key:
		if x.key==key:
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
	
	stack=[]
	p=T.root
	while(p!=null or !stack.empty):
		
		while p!=null:
			stack.push(p)
			p=p.left
		
		p=stack.pop()
		printf(p.key) 
		p=p.right  

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
			x=x.right
		else 
			x=x.left

	#到这里就已经找到了要插入的地方了y
	if y==null:
		T.root=z
	elif y.key<x.key:
		y.right=x
	else 
		y.left=x

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
### 平衡判断
根据左右子树的高度来判断,高度可以递归来得到，非递归算法，暂时没想到

``` python
def height(z):
	if z=None:
		return 0
	else:
		return max(height.left,height.right)+1
def notAVL(z):
	leftHeight=height(z.left)
	rightHeight=height(z.right)
	return abs(leftHeight-rightHeight)>1


```


### 最小不平衡子树旋转


我的理解和记忆方式
1. 左左，左右，右右，右左，第一个左或者右表示，最小不平衡子树根节点的左子树还是右子树；第二个左或右表示，前面一个左右子树的子树。以左左为例

![左左意思](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1536814273816.png)

2. 算法记忆的时候画一个满二叉树进行类比
3. 旋转的时候关注的是三个点，以左左为例关注的 是 跟节点，第一个左，第二个左表示的节点

![四种特殊情况](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1536751332008.png)
注意着只是四种特殊情况，

``` python
# 为了简化代码这里 树的节点是假设是左右孩子结构

def LL(z):
	zl=z.left 
	zlr=z.left.right
	
	z.left=zlr
	zl.right=z
	
	return zl # 替换z作为根节点

def RR(z):
	zr=z.right
	zrl=z.right.left
	
	z.right=zrl
	zl.left=z
	
	return zr 
	
	
#先对LR 的“R”进行变换，将其变成LL形式然后在调用LL
def LR(z):  

	z=RR(z)
	
	z=LL(z)
	
	return z
	
def RL(z):
	z=LL(z)
	z=RR(z)	
	return z

def rotateType(z)
	# 返回函数
	if notAVL(z):
		# 左右或者左左
		if height(z.left)>height(z.right):
			if height(z.left.left)>height(z.left.right):
				return LL # 左左
			else:
				return LR #左右
		else:
			if height(z.right.left)>height(z.right.right):
				return RL # 左左
			else:
				return RR #左右
	else:
		return None
```

### 插入

[参考博客](https://blog.csdn.net/hello_bravo_/article/details/52948871)

有了旋转之后插入就简单了，每次插入之后，判断插入之后，从下到上判断子树是否平衡，如果不平衡就进行旋转。



``` python

def AVLInsert(T,z):
	insert(T,z)
	
	# 找最小不平衡子树进行旋转，至多只有一次旋转
	y=z.parent
	while y not None:
		func=rotateType(y)
		if func not None:
			func(z)
			break  # 只会产生一次旋转
		y=y.parent
```

### 删除

在删除之后进行调整，同样不用到递归，如果没有父亲节点就需要用到递归


1. 没有孩子节点直接删除
2. 只有一个孩子节点，将孩子节点和父节点相连接，

3. 有左右节点，那么需要 找到右子树中值最小的节点y  用其替换要删除的节点z，然后再删除节点y。   节点y是z的右子树中值最小的点，意味着y至少没有左子树，是前面两种情况之一。

在这个基础上找到要删除的节点的父节点，层层往上查看是否平衡
修改一下之前的删除，让其返回删除的节点y

``` python
	
def AVLdelete(T,z):
	y=delete(T,z)
	while y not None:
		func=rotateType(y)
		if func not None:
			func(z)
			break  # 只会产生一次旋转
		y=y.parent
		
	

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
	return y
```

## 红黑树

[参考地址](http://www.cnblogs.com/skywang12345/p/3245399.html)

  R-B Tree，全称是Red-Black Tree，又称为“红黑树”，它一种特殊的二叉查找树。红黑树的每个节点上都有存储位表示节点的颜色，可以是红(Red)或黑(Black)


红黑树的特性:
（1）每个节点或者是黑色，或者是红色。
（2）根节点是黑色。
（3）每个叶子节点（NIL）是黑色。 [注意：这里叶子节点，是指为空(NIL或NULL)的叶子节点！]
（4）如果一个节点是红色的，则它的子节点必须是黑色的。
（5）从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。

注意：
(01) 特性(3)中的叶子节点，是只为空(NIL或null)的节点。
(02) 特性(5)，确保没有一条路径会比其他路径长出俩倍。因而，红黑树是相对是接近平衡的二叉树。

节点的数据结构如下

``` python
符号约定
树一般用T表示，
T.root 根节点

对已节点z
z.key 表示节点的值
z.left 表示左孩子
z.right 表示右孩子
z.parent 表示父节点，根节点的父节点为空
z.color black和red 可以当做是宏定义或者字符串
```

### 性能分析

为什么优秀，书上的证明，逻辑不规范。
[参考地址](https://blog.csdn.net/lanchunhui/article/details/75905478)

注意一点：最长路径至多是最短路径的两倍，这不是一个严格的AVL

### 左旋和右旋

类似于AVL在插入和删除的时候需要通过旋转来维持特性
在红黑树中只用到了两种旋转，左旋和右旋，类似于AVL从满二叉树的角度来思考，X,Y不为空，其他的$\alpha, \beta , \gamma$ 可以为空

![左旋](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1537189247291.png)

**左旋**，**右旋** python 风格伪代码

``` python
# 左旋右旋都是错了，一x.parent 连接y时要判断x在做子树还是右子树  2，判断是否是根节点
def L(x):
	y = x.right
	x.right=y.left
	y.left=x
	
	# 父指针
	y.left.parent=x
	y.parent=x.parent
	x.parent=y
		
	return y



def R(x):
	y = x.left
	x.right=y.left
	y.right=x
	
	# 父指针
	y.right.parent=x
	y.parent=x.parent
	x.parent=y
	
	return y

	
```



### 增加操作

具体步骤看前面贴的参看[博客](http://www.cnblogs.com/skywang12345/p/3245399.html#aa5)参考的算法导论的代码，代理逻辑不清晰，基本无法从代码中整理出逻辑，
ps：博客中的图有错误，120和140的color要对调一下


[java风格的代码](https://blog.csdn.net/tcorpion/article/details/54968644)讲解不够细致，但是代码逻辑清晰

![三种情况](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1537191431745.png)

``` python
def RBInsert(T,z):
	z.color=red   # 先使其颜色变成红色
	insert(T,z)  # 一般二叉查找树的插入
	
	RBInsertFixup(T,z)

	RBInsertFixup(T,z)
```
重点是这个fixup，和前面的AVL相识，从下到上循环修改，有父亲节点，没有父亲节点的话就只能递归了
	
``` python
def RBInsertFixup(T,z):
	while z.parent.color=red:
		if z.parent=z.parent.parent.left: # 父亲是左分支
			uncle=z.parent.parent.right
			grandpa=z.parent.parent.
			if uncle.color==red:
				z.parent.color=black
				uncle=black
				grandpa=red
				z=grandpa
			else if uncle.color=back z=father.right:
				z=z.parent
				L(T,z)
			else if uncle.color=back and  z=father.right
				z.parent.color=blck
				grandpa.color=red
				R(T,grandpa)
				
			
		else:
			exchange(z.parent.left,z.parent.right)
			
	T.root.color=balck
	
```


![enter description here](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1537835938480.png)

![enter description here](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1537836025981.png)

![enter description here](https://hosbimkimg.oss-cn-beijing.aliyuncs.com/pic/1537836033176.png)


### 删除

同理参考上面，逻辑过于啰嗦就不介绍了


## AVL 红黑树对比

[参考地址](https://blog.csdn.net/db199410/article/details/52443161)

     今天看了STL源码剖析中关于红黑树的原理和实现，看完复杂的节点插入、节点颜色变换后不禁想：这些功能经典的AVL树也能实现，为什么要提出红黑树？查了些资料，并且加上自己理解，感叹红黑树的巧妙。

       首先红黑树是不符合AVL树的平衡条件的，即每个节点的左子树和右子树的高度最多差1的二叉查找树。但是提出了为节点增加颜色，红黑是用非严格的平衡来换取增删节点时候旋转次数的降低，而AVL是严格平衡树，因此在增加或者删除节点的时候，根据不同情况，旋转的次数比红黑树要多。所以红黑树的插入效率更高！！！

       这里引用一下知乎上的回答：


       Answer 1：
       1. 如果插入一个node引起了树的不平衡，AVL和RB-Tree都是最多只需要2次旋转操作，即两者都是O(1)；但是在删除node引起树的不平衡时，最坏情况下，AVL需要维护从被删node到root这条路径上所有node的平衡性，因此需要旋转的量级O(logN)，而RB-Tree最多只需3次旋转，只需要O(1)的复杂度。
       2. 其次，AVL的结构相较RB-Tree来说更为平衡，在插入和删除node更容易引起Tree的unbalance，因此在大量数据需要插入或者删除时，AVL需要rebalance的频率会更高。因此，RB-Tree在需要大量插入和删除node的场景下，效率更高。自然，由于AVL高度平衡，因此AVL的search效率更高。
      3. map的实现只是折衷了两者在search、insert以及delete下的效率。总体来说，RB-tree的统计性能是高于AVL的。
      作者：Acjx
      链接：http://www.zhihu.com/question/20545708/answer/58717264


       Answer 2  这个总结比较好：
       红黑树的查询性能略微逊色于AVL树，因为他比avl树会稍微不平衡最多一层，也就是说红黑树的查询性能只比相同内容的avl树最多多一次比较，但是，红黑树在插入和删除上完爆avl树，avl树每次插入删除会进行大量的平衡度计算，而红黑树为了维持红黑性质所做的红黑变换和旋转的开销，相较于avl树为了维持平衡的开销要小得多
       作者：陈智超
       链接：http://www.zhihu.com/question/43744788/answer/98258881


       Answer 3 ：
       功能、性能、空间开销的折中结果。
       AVL更平衡，结构上更加直观，时间效能针对读取而言更高；维护稍慢，空间开销较大。
       红黑树，读取略逊于AVL，维护强于AVL，空间开销与AVL类似，内容极多时略优于AVL，维护优于AVL。
       基本上主要的几种平衡树看来，红黑树有着良好的稳定性和完整的功能，性能表现也很不错，综合实力强，在诸如STL的场景中需要稳定表现。
       作者：Coldwings
       链接：http://www.zhihu.com/question/20545708/answer/44370878