---
layout: post
title: "Catalan number"    
subtitle:   
date: 2020-08-06
author: "Mr Daemon"
catalog: true
tags:
    - Algorithm
        - Math
---

### Catalan number

#### Motivation

我在几天前曾遇到这样一道题：

> 箱子里有20个红球18个白球，每次往外拿一个  问，对于拿出的球至少一次白球数目等于红球的概率

我花了很长时间最后也并没有的出结果，在今天8.6刷leetcode第22题时，由于我使用C语言给出解法，这道题本身只需要使用一个递归即可得出结果，但在submit时遇到了`runtime error`: `heap-buffer-flow`. 我意识到也许是我估算的空间过小，以至于发生了溢出。

这道题本身需要估算的给定括号对数来求有多少种不重复的合法的括号表达式，这两道题本质的思想都可以抽象成
$$
S=\{\{a_{n}\}|a_{n}\in\{0,1\},n\in[0,2m],\sum_{a_{i}=0} 1=\sum_{a_{i}=1} 1=m\}\\
S'=\{\{b_{n}\}\subset S|\forall i\in[0,2m],\sum_{b_{i}=0} 1 \leq \sum_{b_{i}=1} 1\}\\
p=\frac{|S'|}{|S|}
$$
其中p或|S'|是我们所求。

这个问题还可以表述为[在矩阵中寻路问题](https://en.wikipedia.org/wiki/Catalan_number#Third_proof).

#### Intro

卡塔兰数的一般性公式为：

$C_n = \frac{1}{n+1}{2n \choose n} = \frac{(2n)!}{(n+1)!n!}$ 或 $ C_n = {2n\choose n} - {2n\choose n+1}\ \mbox{ for }n\ge 1$ 

由第二个一般性公式可知 $C_{n} \in \N$

#### Application

[wikipedia](https://zh.wikipedia.org/wiki/%E5%8D%A1%E5%A1%94%E5%85%B0%E6%95%B0#%E5%BA%94%E7%94%A8)

#### Proof

许多情况可转化为求一个*2n*位、含*n*个1、*n*个0的二进制数，满足从左往右扫描到任意一位时，经过的0数不多于1数。显然含*n*个1、*n*个0的*2n*位二进制数共有 $2n \choose n$ 个，下面考虑不满足要求的数目。

考虑一个含*n*个1、*n*个0的2n位二进制数，扫描到第*2m+1*位上时有*m+1*个0和*m*个1（容易证明一定存在这样的情况），则后面的0-1排列中必有*n-m*个1和*n-m-1*个0。将*2m+2*及其以后的部分0变成1、1变成0，则对应一个*n+1*个0和*n-1*个1的二进制数。反之亦然（相似的思路证明两者一一对应）。

从而 $C_{n}={2n \choose n}-{2n \choose n+1}={\frac {1}{n+1}}{2n \choose n}$ 。证毕。



