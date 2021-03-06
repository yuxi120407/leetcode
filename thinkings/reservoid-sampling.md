# 蓄水池抽样

力扣中关于蓄水池抽样问题官方标签是 2 道，根据我的做题情况来看，可能有三四道。比重算是比较低的，大家可以根据自己的实际情况选择性掌握。

蓄水池抽样的算法思维很巧妙，代码简单且容易理解，就算不掌握它，作为了解也是很不错的。

## 问题描述

给出一个数据流，我们需要在此数据流中随机选取 k 个数。由于这个数据流的长度很大，因此需要边遍历边处理，而不能将其一次性全部加载到内存。

请写出一个随机选择算法，使得数据流中所有数据被**等概率**选中。

这种问题的表达形式有很多。比如让你随机从一个矩形中抽取 k 个点，随机从一个单词列表中抽取 k 个单词等等，要求你等**概率随机抽取**。不管描述怎么变，其本质上都是一样的。今天我们就来看看如何做这种题。

## 算法描述

这个算法叫蓄水池抽样算法（reservoid sampling）。

其基本思路是：

- 构建一个大小为 k 的数组，将数据流的前 k 个元素放入数组中。
- 对数据流的前 k 个数**先**不进行任何处理。
- 从数据流的第 k + 1 个数开始，在 [1, i] 之间选一个数 rand，其中 i 表示当前是第几个数。
- 如果 rand 大于等于 k 什么都不做
- 如果 rand 小于 k， 将 rand 和 i 交换，也就是说选择当前的数代替已经被选中的数（备胎）。
- 最终返回幸存的备胎即可

这种算法的核心在于先以某一种概率选取数，并在后续过程以另一种概率换掉之前已经被选中的数。因此实际上每个数被最终选中的概率都是**被选中的概率 \* 不被替换的概率**。

伪代码：

> 伪代码参考的某一本算法书，并略有修改。

```py
Init : a reservoir with the size： k
for i= k+1 to N
    if(random(1, i) < k) {
        SWAP the Mth value and ith value
    }
```

这样可以保证被选择的数是等概率的吗？答案是肯定的。

- 当 i <= k ，i 被选中的概率是 1。
- 到第 k + 1 个数时，第 k + 1 个数被选中的概率（走进上面的 if 分支的概率）是 $\frac{k}{k+1}$，到第 k + 2 个数时，第 k + 2 个数被选中的概率（走进上面的 if 分支的概率）是 $\frac{k}{k+2}$，以此类推。那么第 n 个数被选中的概率就是 $\frac{k}{n}$
- 上面分析了被选中的概率，接下来分析不被替换的概率。到第 k + 1 个数时，前 k 个数被替换的概率是 $\frac{1}{k}$。到前 k + 2 个数时，第 k + 2 个数被替换的概率是 $\frac{1}{k}$，以此类推。也就是说所有的被替换的概率都是 $\frac{1}{k}$。知道了被替换的概率，那么不被替换的概率其实就是 1 - 被替换的概率。

因此对于前 k 个数，最终被选择的概率都是 1 \* 不被 k + 1 替换的概率 \* 不被 k + 2 替换的概率 \* ... 不被 n 替换的概率，即 1 \* (1 - 被 k + 1 替换的概率) \* (1 - 被 k + 2 替换的概率) \* ... (1 - 被 n 替换的概率)，即 $1 \times (1 - \frac{k}{k+1} \times \frac{1}{k}) \times (1 - \frac{k}{k+2} \times \frac{1}{k}) \times ... \times (1 - \frac{k}{n} \times \frac{1}{k}) = \frac{k}{n} $。

对于 第 i (i > k) 个数，最终被选择的概率是 第 i 步被选中的概率 \* 不被第 i + 1 步替换的概率 \* ... \* 不被第 n 步被替换的概率， 即 $\frac{k}{k+1} \times (1 - \frac{k}{k+2} \times \frac{1}{k}) \times ... \times (1 - \frac{k}{n} \times \frac{1}{k}) = \frac{k}{n} $。

总之，不管是哪个数，被选中的概率都是 $\frac{k}{n}$，满足概率相等的需求。

## 相关题目

- [382. 链表随机节点](https://leetcode-cn.com/problems/linked-list-random-node/ "382. 链表随机节点")
- [398. 随机数索引](https://leetcode-cn.com/problems/random-pick-index/ "398. 随机数索引")
- [497. 非重叠矩形中的随机点](https://leetcode-cn.com/problems/random-point-in-non-overlapping-rectangles/ "497. 非重叠矩形中的随机点")

## 总结

蓄水池抽样算法核心代码非常简单。但是却不容易想到，尤其是之前没见过的情况下。其核心点在于每个数被最终选中的概率都是**被选中的概率 \* 不被替换的概率**。于是我们可以采取某一种动态手段，使得每一轮都有概率选中和替换一些数字。 上面我们有给出了概率相等的证明过程，大家不妨自己尝试证明一下。之后结合文末的相关题目练习一下，效果会更好。
