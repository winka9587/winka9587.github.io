---
title: 时间复杂度与概率 
date: 2025-05-09 18:32
category: 
author: 
tags: ['回旋镖']
summary: 
math: true
---

离散数学回旋镖系列 :) 想到一个问题：

**假设有n个数字，有一个函数能够每次取一个数（概率相同），取完之后对应的标签位置为1，（可能取到位置为1的数，如果取到，则重新取），取完$n$个不同的数字最终的时间复杂度是多少**


### 优惠券收集问题

概念: 期望 = 事件 * 事件发生的概率

在优惠券收集问题中，设总共有$n$种不同的优惠券，每次收集时得到一种新的优惠券的概率取决于已经收集到的优惠券数量。

当已经收集到k种优惠券时，下一次收集到新的优惠券的概率是 $(n - k) / n$。

因此，从$k$种到$k+1$种优惠券，需要的期望次数是$n / (n - k)$。

为了收集齐所有n种优惠券，总期望次数$E$为：

$E = E(从0到1) + E(从1到2) + ... + E(从n-1到n)
= n/n + n/(n-1) + n/(n-2) + ... + n/1
= n * (1 + 1/2 + 1/3 + ... + 1/n)$

其中，$1 + 1/2 + 1/3 + ... + 1/n$ 是第$n$个调和数，记作$H_n$。

因此，$E = n * H_n$。

**调和数的性质**
第$n$个调和数$H_n = 1 + 1/2 + 1/3 + ... + 1/n$ 有以下近似：

$$H_n ≈ ln(n) + γ + 1/(2n)$$

其中，$γ ≈ 0.5772156649$ 是欧拉-马歇罗尼常数。对于较大的$n$，$H_n ≈ ln(n) + γ$。

因此，$E ≈ n * (ln(n) + γ) = n ln(n) + γ n$。

更精确地，$E = n ln(n) + γ n + 1/2 + O(1/n)$。

### 应用到原问题

每次选取一个数字，如果标签为0，则成功标记；如果为1，则无效。

这相当于在已经标记了$k$个数字后，下一次成功标记一个新数字的概率是 (n - k)/n，需要的期望次数是 $n/(n - k)$。

因此，**总期望时间**为：
$$
T = n/n + n/(n-1) + n/(n-2) + ... + n/1
= n * (1 + 1/2 + 1/3 + ... + 1/n)
= n H_n
$$
**时间复杂度**
因此，这个过程的总时间复杂度（即预期的总选取次数）是：
$$
T = n H_n ≈ n ln(n) + γ n + 1/2 + O(1/n)
$$
对于大n，主导项是 n ln(n)，所以通常说时间复杂度为 $O(n log n)$。

### 举例
例如，当n=4时：
$$
H_4 = 1 + 1/2 + 1/3 + 1/4 = 25/12 ≈ 2.083
$$
$$
T = 4 * 25/12 = 25/3 ≈ 8.333
$$
即，预期需要约8.33次选取才能标记完所有4个数字。

验证
让我们验证一下$n=1$和$n=2$的情况：

$n=1$：

只有一个数字，第一次选取必然成功。
$$
T = 1 * H_1 = 1 * 1 = 1 ✔
$$
$n=2$：
$$
H_2 = 1 + 1/2 = 3/2
$$
$$
T = 2 * 3/2 = 3
$$

验证：

第一次选取：必然成功（标记一个数字）。

之后，每次选取有1/2的概率成功（标记第二个数字）。

从第一次成功到第二次成功，是几何分布，期望次数 = 1 / (1/2) = 2。

因此，总期望次数 = 1 (第一次) + 2 (第二次) = 3 ✔

### 可能的误区
可能会忽略“选取已经标记的数字时需要重新选取”这一点，从而误认为每次选取都是独立的成功。但实际上，无效的选取（即选到已经标记的数字）会增加总的时间。因此，必须考虑在已经标记了k个数字后，成功标记一个新数字的概率是 $(n - k)/n$，对应的期望次数是 $n/(n - k)$。

另一个可能的误区是认为时间复杂度是$O(n^2)$。虽然每一项是$n/(n - k)$，但总和是$n H_n$，即$O(n log n)$，而不是$O(n^2)$0。

### 更正式的数学推导：

设T为总期望时间。可以将$T$分解为各个阶段的期望时间之和：
$$
T = T_0 + T_1 + ... + T_{n-1}
$$
其中，$T_k$ 表示从已经标记了$k$个数字到标记$k+1$个数字所需的期望时间。

在已经标记了$k$个数字时，一个新数字被选中的概率是 $p = (n - k)/n$。

因此，$T_k$ 服从几何分布，其期望为 $E[T_k] = 1/p = n/(n - k)$。

因此：
$$
T = Σ_{k=0}^{n-1} n/(n - k) = n Σ_{k=1}^n 1/k = n H_n
$$
结论
综上所述，这个过程的总时间复杂度（即预期的总选取次数）为：
$$
T = n H_n ≈ n ln(n) + γ n + 1/2 + O(1/n)
$$
其中，$H_n$ 是第$n$个调和数，$γ$ 是欧拉-马歇罗尼常数。

对于大n，可以简记为 $O(n log n)$。



## 问题

### 优惠券收集问题是平均情况下所需的次数

而时间复杂度是最坏情况(那岂不是无穷？)

~~~
最坏情况时间复杂度（Worst-case time complexity）：算法在所有可能的输入中，运行时间最长的那个输入所对应的时间复杂度。

平均情况时间复杂度（Average-case time complexity）：算法在所有可能的输入中，运行时间的期望值（即平均运行时间）。

最好情况时间复杂度（Best-case time complexity）：算法在所有可能的输入中，运行时间最短的那个输入所对应的时间复杂度。
~~~

是因为平时leetcode刷太多了, 反而忽略了**随机算法/概率性问题**的复杂度分析。