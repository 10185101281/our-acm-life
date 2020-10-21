# min-max容斥

---

## 前置知识

### 二项式反演

​	$f(n)=\sum_{i=0}^nC_n^ig(i) \Leftrightarrow g(n)=\sum_{i=0}^n(-1)^{n-i}C_n^if(i)$

## 描述

​	给定集合$S$，设$max(S)$为$S$中最大值，$min(S)$为$S$中最小值，则有：

​		$max(S)=\sum _{T\subseteq S}(-1)^{|T|-1}min(T)$

## 证明

 	构造容斥系数$f(x)$，使得：

​		$max(S)=\sum _{T\subseteq S}f(|T|)min(T)$

​	考虑第$x+1$大的元素会被统计到的贡献（即有哪些集合的最小值会为第$x+1$大的元素），为：

​		$[x=0]=\sum_{i=0}^xC_x^i\times f(i+1)$

​	由二项式反演，得到：

​		$f(x+1)=\sum_{i=0}^x(-1)^{x-i}C_x^i[i=0]$

​	有：

​		$f(x+1)=(-1)^x$

​	因此：

​		$f(x)=(-1)^{x-1}$

​	综上，有：

​		$max(S)=\sum _{T\subseteq S}(-1)^{|T|-1}min(T)$