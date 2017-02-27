---
layout: post
title: '从斐波那契数列谈谈代码的性能优化'
date: 2017-02-25 06:38:57.000000000 +09:00
tags: ['算法']
---


> 根据高德纳（Donald Ervin Knuth）的《计算机程序设计艺术》（The Art of Computer Programming），
1150年印度数学家Gopala和金月在研究箱子包装物件长宽刚好为1和2的可行方法数目时，首先描述这个数列。
在西方，最先研究这个数列的人是比萨的列奥那多（意大利人斐波那契Leonardo Fibonacci），
他描述兔子生长的数目时用上了这数列:
>
> - 第一个月初有一对刚诞生的兔子
> - 第二个月之后（第三个月初）它们可以生育
> - 每月每对可生育的兔子会诞生下一对新兔子
> - 兔子永不死去
>
>假设在n月有兔子总共a对，n+1月总共有b对。在n+2月必定总共有a+b对：<br/>
>因为在n+2月的时候，前一月（n+1月）的b对兔子可以存留至第n+2月（在当月属于新诞生的兔子尚不能生育）。而新生育出的兔子对数等于所有在n月就已存在的a对


费波那契数列由0和1开始，之后的费波那契系数就是由之前的两数相加而得出:
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233……

如果用数学语言来描述大概是下面这个样子：<br/>
$$F_0 = 0$$<br/>
$$F_1 = 1$$<br/>
$$F_n = F_(n-1) + F_(n-2)$$<br/>


## 递归求解
学过编程的人，第一反应肯定是用递归求解：
```python
def fib(n):
    assert n >= 0, 'input invalid'
    return n if n<=1 else fib(n-1) + fib(n-2)
```

```javascript
function fib(n) {
    return n <= 1 ? n : fib(n - 1) + fib(n - 2);
}
```

递归的好处就是代码清晰明了，写起来干净利索，丝毫没有拖泥带水的感觉

这种递归求解的方法的过程可以简化为如下图所示的二叉树：
![斐波那契二叉树](http://7xort8.com1.z0.glb.clouddn.com/2017-02-25-fibonacci.png)
总的计算量近似可以等于高度为n-1的二叉树的节点总数，所以它的时间复杂度为O(2^n)

下面是我统计不同语言用递归算法求解斐波那契数列第41项所需的时间：

![fibonacci_speed_compare](http://7xort8.com1.z0.glb.clouddn.com/fibonacci_speed_compare.png)

> ps：这里直接用的系统自带的`time`命令来统计的运行时间等信息


## 为什么不建议使用递归来求解
由于龟叔认为程序员根本用不到递归，所以一直拒绝为python加上尾递归优化，甚至当递归深度超过1000时，直接抛出RuntimeError: `maximum recursion depth exceeded`
具体内容请参见：[Tail Recursion Elimination](http://neopythonic.blogspot.jp/2009/04/tail-recursion-elimination.html)

> 那篇09年的博客里是这样说的: <br/>
> 我不认为递归是编程的基础。递归是一些计算机科学家们，尤其是那些热爱Scheme （lisp的一支）和喜欢用‘cons’ 来教表头表尾和递归的人们。
> 但是对我（Guido）来说，递归只是一些为基础数学研究而存在的理论手段（例如分形几何学），而不是日常的编程工具。

Python的哲学是`“做一件事情有且只有一种方法”（There should be one-- and preferably only one --obvious way to do it.）`
龟叔坚持不给Python加上尾递归的优化恰恰体现了这种哲学，这个设计哲学不仅减轻了人们在开发时的认知负担和选择成本，对于提高开发效率是很有帮助的。
同时，这个特点使得不同的人用Python写出来的代码不至于相差很大，这对于团队合作也是很有用的。


## 递归转化为非递归求解
所以我们的斐波那契数列当然不能直接用递归求解啦，比较常见的思路是把递归改为递推，把斐波那契的前两项先初始化为数组，
然后根据`f(n) = f(n-1) + f(n-2)`用循环一次算出后面的每一项，这种算法的时间复杂度为O(n)。
我在我的电脑上测了一下，下面这段代码求第41项只用了0.02秒。

```python
def fast_fib(n):
    f = [0, 1]
    for i in range(2, n+1):
        f.append(f[i-1] + f[i-2])
    return f[n]
```


比较一下递归法和递推法：<br/>
二者都用了分治的思想——把目标问题拆为若干个小问题，利用小问题的解得到目标问题的解。
二者的区别实际上就是普通分治算法和动态规划的区别。


## 问题结束了吗
其实还有一个更加巧妙的办法（利用通项公式求解除外）

我们先把斐波那契数列中相邻的两项：F(n)和F(n - 1)写成一个2x1的矩阵，然后对其进行变形：
$$
\begin{equation*} \begin{bmatrix}F_n\\F_{n-1}\end{bmatrix} =\begin{bmatrix}F_{n-1}+F_{n-2}\\F_{n-1}\end{bmatrix} =\begin{bmatrix}1\times F_{n-1}+1\times F_{n-2}\\1\times F_{n-1}+0\times F_{n-2}\end{bmatrix} =\begin{bmatrix}1&1\\1&0\end{bmatrix}\times\begin{bmatrix}F_{n-1}\\F_{n-2}\end{bmatrix} \end{equation*}
$$


继续推导可以得到：<br/>

$$
\begin{equation*} \begin{bmatrix}F_n\\F_{n-1}\end{bmatrix} =\begin{bmatrix}1&1\\1&0\end{bmatrix}^{n-1}\times\begin{bmatrix}F_{1}\\F_{0}\end{bmatrix} =\begin{bmatrix}1&1\\1&0\end{bmatrix}^{n-1}\times\begin{bmatrix}1\\0\end{bmatrix} \end{equation*}
$$

利用矩阵来运算的话，整个算法的时间复杂度是O(log n)，空间复杂度是O(1)

斐波那契数列通项公式的推导也是个很有意思的题目，可以利用生成函数来推导，这里就不展开了