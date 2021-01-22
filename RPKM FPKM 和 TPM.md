## **RPKM vs FPKM vs TPM**

### RPKM,FPKM,TPM是为了消除基因长度和测序深度的影响

在RNA-Seq的分析中，为了获得差异表达基因，只需要对不同基因的测序Read数进行比较即可。然而比对到不同基因上的Read数目并不能直接用于比较这两个基因的表达量差异，因为在RNA-seq中有一个很浅显的道理，基因越长，比对到此基因上的Read就会越多；测序深度越大，那么本次RNA-seq的所有Read数都会增加。也就是说Read数除了和基因表达量相关外，也和基因的长度、测序深度有关，因此为了比较多个RNA-seq重复（测序深度有一定差异）的不同基因（基因长度有一定差异）之间的表达量差异，那么就不能使用Read数直接进行比较，而是需要对Read数进行标准化。

1. **不同基因在同一个样本中**，基因越长，随即打断之后得到的片段就越多，该基因被测到的概率就越大，落在该基因上的 reads 就越多。所以，要排除**基因长度**的影响。
2. **同一个基因在不同样本中**，样本的测序深度越高，相当于抽样次数越多，落在该样本中基因上的 reads 就越多。所以，要排除**测序深度**的影响。

### 标准化

要去除**测序深度**和**基因长度的**的影响，最简单的办法就是给 read counts 除以**总 Reads 数**和**基因长度的**。

![mark](http://cdn.liguocheng.top/blog/20200305/oPqtasswcCnQ.png?imageslim)

1.给 read counts 除以基因长度，这里以 kb 为单位, 得到以下表格

![mark](http://cdn.liguocheng.top/blog/20200305/z9G5fgls6L2K.png?imageslim)

2.将每个样品的 read counts 合并，添加一行 Total Reads

![mark](http://cdn.liguocheng.top/blog/20200305/Q3SoOh9wdv8E.png?imageslim)

3.再除以 Total Reads，以million(M)为单位，得到以下表格

![mark](http://cdn.liguocheng.top/blog/20200305/NNzyQCI5asTb.png?imageslim)

通过标准化之后的结果可以发现：

1. 在样本1 中，基因A和基因B的表达量是一样高的；
2. 基因A，在样本3中的表达只是略高于样本2。



### RPKM VS FPKM

RPKM的 全称是 **Reads per kilobase of exon per million reads mapped**，这个 Per Kilobase 是基因长度的单位、Per Millon 是测序深度的单位。

FPKM的全称是 **Fragments per kilobase of exon per million reads mapped**。建库测序是一个随机抽样的过程，而这个抽样的样品事实上是以 Fragments 为单位，而不是 Reads。

当 single-end 测序的时候，RPKM 与 FPKM 是等价的；当 pair-end 测序的时候，应该使用 FPKM。所以，FPKM 是更为通用的单位。



### TPM

TPM 的全称是 **Transcripts per million reads**。它与 FPKM 唯一的区别是，第二步除的不是 Total Reads

![mark](http://cdn.liguocheng.top/blog/20200305/66PYVJoGe5tx.png?imageslim)

1.给 read counts 除以基因长度，这里以 kb 为单位, 得到以下表格

![mark](http://cdn.liguocheng.top/blog/20200305/rW15cFptP4ah.png?imageslim)

2.将每个样品的数值求和，添加一行 Total

![mark](http://cdn.liguocheng.top/blog/20200305/nHlAuppCoWej.png?imageslim)

3.再除以 Total，得到以下表格

![mark](http://cdn.liguocheng.top/blog/20200305/dXxzq2HB5XLY.png?imageslim)



### FPKM VS TPM

FPKM 的公式中，![mark](http://cdn.liguocheng.top/blog/20200305/cTFnwNierAY0.png?imageslim)没有考虑基因长度的影响，相当于直接使用了总 Fragments 数目。

所以，TPM ![mark](http://cdn.liguocheng.top/blog/20200305/KeUQXTCp0eJo.png?imageslim)更符合我们对相对表达量的定义，而FPKM 似乎没有明确的意义。

![mark](http://cdn.liguocheng.top/blog/20200305/4XhD6UU6OHpG.png?imageslim)

### 表达量定义

如果我们能够明确知道一个细胞中、或者是一定摩尔量的 RNA中，有多少条某种转录本。这叫**绝对定量**。

但是，我们在进行转录组测序的时候，并不知道用于建库测序的 RNA 来自多少个细胞，总过有多少转录本。所以只能进行**相对定量**。相对定量，描述的是该基因的转录本占样本中所有转录本的百分比。

如果能够对样品进行细胞计数或者添加 spike-in, 转录组也可以实现绝对定量，不过这不在本文的讨论范围。

FPKM／RPKM／TPM都是描述相对定量的单位。

![mark](http://cdn.liguocheng.top/blog/20200305/N8RgmN4sEXCk.png?imageslim)

![mark](http://cdn.liguocheng.top/blog/20200309/X0IMRULLE7zf.png?imageslim)

TPM中各个样本的总和是相等的，而FPKM则不相等。所以更推荐用TPM。**TPM值就是RPKM的百分比！！！**

### 如何将FPKM转换成TPM？

