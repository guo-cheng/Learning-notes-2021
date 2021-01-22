# **PCA**

[TOC]

### PCA的简明含义

PCA就是把需要通过成百上千个变量描述的数据归结为只需要少数几个变量（经典的是2个）来描述数据。

### PCA的内在思想

PCA的算法如下：

1. 寻找中心点，并平移至中心点；
2. 计算PC1； 
   PC1是一条过原点的最佳拟合曲线，所有数据点与其的距离平方和最小。
3. 计算PC2； 
   PC2与PC1相垂直。
4. 以PC1和PC2为标准坐标轴，绘制PCA图。

#### **1. 寻找并平移中心点**

以小鼠的RNA-seq数据为例，简单起见，先看2个gene的情形：

![mouse](http://cdn.liguocheng.top/blog/20200304/o42JTvMYAQIq.png?imageslim)

如下图所示，先对Gene1求平均值，在对Gene2求平均值，于是就可以获得所有数据的中心点，然后将坐标系原点平移至此中心点。

![mark](http://cdn.liguocheng.top/blog/20200304/7cERnRvNhma3.png?imageslim)

#### **2. 计算PC1**

计算PC1就是为了获得一条过原点的直线，所有数据点距离此直线的距离平方和最小。以一个点为例，也就是下图中的距离a最小，由于每个数据点距离原点的距离是固定的，所以使得距离a最小，也就是使得距离b最大。也就是![mark](http://cdn.liguocheng.top/blog/20200304/4H0eQziycFu6.png?imageslim)最大。最终可以求得最佳拟合直线如下图所示，假定此直线斜率为0.25，也就是说**"PC1由4份Gene1和1份Gene2构成"**，计算方法是“奇异值分解”。

由于奇异值分解时，斜边c是标准化为1的，所以标准化之后，**PC1由”0.97份Gene1和0.242份Gene2构成"**

![mark](http://cdn.liguocheng.top/blog/20200304/I6m2JCgsj8q9.png?imageslim)

#### **3. 计算PC2**

在PCA中，各个主成分之间是相互垂直的，在这里PC2和PC1是垂直的，也就是PC2的斜率为4，经过标准化之后，**"PC2由-0.242份Gene1和0.97份Gene2构成"**。于是PC1和PC2都已经找到，分别是下图中的红色虚线和蓝色虚线坐标轴。

![mark](http://cdn.liguocheng.top/blog/20200304/RpBpK9RDO3Oe.png?imageslim)

#### **4. 绘制PCA图**

![mark](http://cdn.liguocheng.top/blog/20200304/7Ssfco6fIBep.png?imageslim)

**于是所有的数据点都可以转换为坐标（PC1，PC2），以PC1和PC2为坐标轴即可绘制出相应的PCA图：**

![mark](http://cdn.liguocheng.top/blog/20200304/EuLsHbb5g8oH.png?imageslim)

2个Gene的PCA过程如上，3个Gene的PCA同理，先获得中心点，然后对所有点进行拟合获得PC1，然后在垂直于PC2的线中，找到最佳拟合线，最后PC3是垂直于PC1和PC2的。

拟合的要求就是过中心点，同时点与拟合直线的距离平方和最小，也就是使得最大。

有几个样本就会有几个主成分，且主成分的重要性依次下降，PC1>PC2>PC3。

#### **计算PC1、PC2的贡献率**

![mark](http://cdn.liguocheng.top/blog/20200304/1hR7jaWYAi5o.png?imageslim)

### PCA在R中的实现

- 如何利用`prcomp()`进行PCA分析
- 如何利用`base`作图包和`ggplot2`作图包进行PCA可视化
- 如何计算每个主成分的离散贡献度
- 看看loading score

#### 新建一个示例数据

```R

# 使用内置函数prcomp ，目的是展示样本相互之间相关还是不相关# 新建一个10个样本，每个样本100个基因的表达矩阵，其中行为基因，列为样本
data.matrix <- matrix(nrow = 100,
                      ncol = 10)
# 给10个样本命名，用wt表示wild type(对照组)，ko表示knockout(敲除基因的处理组)
# 列名
colnames(data.matrix) <- c(
  paste("wt",1:5, sep = ""),
  paste("ko",1:5, sep = "")
)
# 行名
rownames(data.matrix) <- paste(
  "gene", 1:100, sep = ""
)
# 赋值
# 它是给100个行分别赋值，于是用了一个for循环，次数为100；然后每个循环中又要给10个样本进行赋值，前5个是wt，后5个是ko；rpois是创建泊松分布的数据，其中5是创建5个值，lamda是分布的均值，这个均值是sample从10到1000挑出来的一个值
for (i in 1:100){
  wt.value <- rpois(5, lambda = sample(x=10:1000, size = 1))
  ko.value <- rpois(5, lambda = sample(x=10:1000, size = 1))
  
  data.matrix[i,] <- c(wt.value, ko.value)
}

> head(data.matrix)
      wt1 wt2 wt3 wt4 wt5 ko1 ko2 ko3 ko4 ko5
gene1 764 786 795 699 734 199 172 198 168 150
gene2 550 533 572 492 586 285 235 265 242 266
gene3  29  27  39  36  49 163 163 136 162 162
gene4 173 183 185 187 204  32  33  28  28  36
gene5 333 380 341 414 402 827 846 810 794 786
gene6 567 563 595 549 575 143 136 124 119 118

# 运行PCA
# 使用内置函数prcomp ，目的是展示样本相互之间相关还是不相关
pca <- prcomp(t(data.matrix),
              scale = T)

# prcomp函数是对行进行相互比较，但这里我们的数据行是基因，我们并不关心两个不同基因之间的差异或者说相关性，而是关心两个不同样本之间的关系，因此需要让样本为行，于是就需要转置t() (英文是：transpose)

# 这个函数计算结果返回三个东西：

# x：它包含了作图需要使用的主成分信息(principle components)。一般作平面图就使用它的前两个PCs。主成分怎么计算的？

# 有多少样本就有多少PCs，x的结果就有多少列。比如我们现在有10个样本，计算结果就有10个PCs

# sdev：standard deviation 标准差，利用它可以计算每个主成分在原始数据中占据了多少比例的离散度。

# rotation：包含了PCA的变量信息，简而言之它记录了PCA使用的列的(也就是这里的基因)所占的权重

```

#### 作图

```R
# 利用base包（利用x）
plot(pca$x[,1], pca$x[,2])
# 其中一个点表示一个样本，它的横坐标根据PC1确定，纵坐标根据PC2确定
```

![mark](http://cdn.liguocheng.top/blog/20200304/lwBiF1zbpjVY.png?imageslim)

```R
# 每个主成分的离散贡献度(利用sdev)

# 利用标准差求出来方差，然后算每个PC的方差占比，最后利用barplot可视化

pca.var <- pca$sdev^2
pca.var.per <- round(pca.var/sum(pca.var)*100,1)
barplot(pca.var.per)
```

![mark](http://cdn.liguocheng.top/blog/20200304/NRdTLJ2n7U4J.png?imageslim)

```R
# 利用ggplot2优化

pca.data <- data.frame(Sample=rownames(pca$x),
                       X=pca$x[,1],
                       Y=pca$x[,2])
> head(pca.data)
    Sample         X          Y
wt1    wt1 -9.423527  0.1431157
wt2    wt2 -9.026152  0.1142019
wt3    wt3 -8.786758 -0.3014123
wt4    wt4 -8.766415 -0.7976410
wt5    wt5 -9.282003  0.7994244
ko1    ko1  9.205322 -0.8004204

# 画图
library(ggplot2)
ggplot(data=pca.data,
       aes(x=X, y=Y, label=Sample)) +
  geom_text() +
  xlab(paste("PC1 - ", pca.var.per[1], "%", sep="")) +
  ylab(paste("PC2 - ", pca.var.per[2], "%", sep="")) +
  theme_bw() + theme(plot.title = element_text(hjust = 0.5)) + ggtitle("My PCA Graph")
```

![mark](http://cdn.liguocheng.top/blog/20200304/eImj9GF7v5cS.png?imageslim)

#### 关于loading score

```R
# pca$rotation中存储了每个PC的变量的loading score，因为这里PC1是贡献值最大的，那么又是哪些基因导致了它的贡献值最大呢？

# 先取出PC1的loading score，也就是全部基因的权重
loading_scores <- pca$rotation[,1]


# 将样本推向左边👈的基因在其中的值为负数，将样本推向右边👉的基因为正数，因为对这两侧的"强推动"基因都比较感兴趣，于是取个绝对值，然后排序，取出前10个名字

gene_scores <- abs(loading_socres)
gene_socre_ranked <- sort(gene_scores,decreasing = T)
top10_genes <- names(gene_socre_ranked[1:10])
> top10_genes
 [1] "gene95" "gene21" "gene86" "gene96" "gene79" "gene25" "gene74" "gene93"
 [9] "gene17" "gene59"
> pca$rotation[top10_genes,1]
    gene95     gene21     gene86     gene96     gene79     gene25     gene74 
-0.1046586 -0.1046559  0.1046513 -0.1046302  0.1046266 -0.1046250 -0.1046122 
    gene93     gene17     gene59 
-0.1046092 -0.1045993  0.1045802 
```

![mark](http://cdn.liguocheng.top/blog/20200304/vsFkCwa4VBFD.png?imageslim)



### PCA实战

#### 数据初始化

```R
exprData <- "ehbio_salmon.DESeq2.normalized.symbol.txt"
# 非必须
sampleFile <- "sampleFile"
```

#### 数据读入

```R
data <- read.table(exprData, header=T, row.names=NULL,sep="\t")

# 处理重复名字，谨慎处理，先找到名字重复的原因再决定是否需要按一下方式都保留
rownames_data <- make.names(data[,1],unique=T)
data <- data[,-1,drop=F]
rownames(data) <- rownames_data

data <- data[rowSums(data)>0,]
data <- data[apply(data, 1, var)!=0,]
dim(data)
data[1:4,1:6]
```

#### 数据预处理

```R
# 计算中值绝对偏差 (MAD, median absolute deviation)度量基因表达变化幅度
# 在基因表达中，尽管某些基因很小的变化会导致重要的生物学意义，
# 但是很小的观察值会引入很大的背景噪音，因此也意义不大。
mads <- apply(data, 1, mad)

data <- data[rev(order(mads)),]
dim(data)
```

#### PCA分析

```R
# Pay attention to the format of PCA input 
# Rows are samples and columns are variables
data_t <- t(data)
variableL <- ncol(data_t)

if(sampleFile != "") {
  sample <- read.table(sampleFile,header = T, row.names=1,sep="\t")
  data_t_m <- merge(data_t, sample, by=0)
  rownames(data_t_m) <- data_t_m$Row.names
  data_t <- data_t_m[,-1]
  head(sample)
}
# By default, prcomp will centralized the data using mean.
# Normalize data for PCA by dividing each data by column standard deviation.
# Often, we would normalize data.
# Only when we care about the real number changes other than the trends,
# `scale` can be set to TRUE. 
# We will show the differences of scaling and un-scaling effects.
pca <- prcomp(data_t[,1:variableL], scale=T)

# sdev: standard deviation of the principle components.
# Square to get variance
# percentVar <- pca$sdev^2 / sum( pca$sdev^2)

# To check what's in pca
print(str(pca))
```

#### PCA展示样品分组信息

```R
library(factoextra)
# 碎石图展示每个主成分的贡献
fviz_eig(pca, addlabels = TRUE)
```

![mark](http://cdn.liguocheng.top/blog/20200304/s1jGE6qvGLmS.png?imageslim)

#### PCA样品聚类信息展示

```R
fviz_pca_ind(pca)
```

![mark](http://cdn.liguocheng.top/blog/20200304/AvlzUwN5Q99H.png?imageslim)

```R
# 样品数目中等时，自动调整文本标记的位置
fviz_pca_ind(pca, repel=T)
```

![mark](http://cdn.liguocheng.top/blog/20200304/IWlUMMXnGO7F.png?imageslim)



#### 根据样品分组上色

```R
fviz_pca_ind(pca, col.ind=data_t$conditions, mean.point=F, addEllipses = T, legend.title="Groups")
```

![mark](http://cdn.liguocheng.top/blog/20200304/vjK90bhUk2Xo.png?imageslim)

```R
# 根据分组上色并绘制
fviz_pca_ind(pca, col.ind=data_t$conditions, mean.point=F, addEllipses = T, legend.title="Groups", ellipse.type="confidence", ellipse.level=0.95)
```

![mark](http://cdn.liguocheng.top/blog/20200304/ncH2srsHJyyM.png?imageslim)



#### 展示贡献最大的变量 (基因)

~~~R
# 1. 展示与主坐标轴的相关性大于0.99的变量

```{r}
# Visualize variable with cos2 >= 0.99
fviz_pca_var(pca, select.var = list(cos2 = 0.99), repel=T, col.var = "cos2", geom.var = c("arrow", "text") )

fviz_pca_var(pca, select.var = list(cos2 = 0.95), repel=T, axes = c(2, 3), col.var = "cos2", geom.var = c("arrow", "text"))
```
~~~

![mark](http://cdn.liguocheng.top/blog/20200304/zS85X6D2p9li.png?imageslim)



![mark](http://cdn.liguocheng.top/blog/20200304/bpWe04vwrSKq.png?imageslim)



~~~R
# 2. 展示与主坐标轴最相关的10个变量

```{r}
# Top 10 active variables with the highest cos2
fviz_pca_var(pca, select.var= list(cos2 = 10), repel=T, col.var = "contrib")
```
~~~

![mark](http://cdn.liguocheng.top/blog/20200304/RhBj5sze21G0.png?imageslim)



~~~R
# 3. 展示自己关心的变量（基因）与主坐标轴的相关性分布

```{r}
# Select by names
# 这里选择的是MAD值最大的几个基因
name <- list(name = c("FN1", "DCN", "CEMIP","CCDC80","IGFBP5","COL1A1","GREM1"))
fviz_pca_var(pca, select.var = name)
~~~

![mark](http://cdn.liguocheng.top/blog/20200304/c54NPJcRIlwg.png?imageslim)



#### biPLot同时展示样本分组和关键基因

~~~R
```{r}
# top 5 contributing individuals and variable
fviz_pca_biplot(pca,
            fill.ind=data_t$conditions,
            palette="joo",
            addEllipses = T, 
            ellipse.type="confidence",
            ellipse.level=0.95,
            mean.point=F,
            col.var="contrib",
            gradient.cols = "RdYlBu",
            select.var = list(contrib = 10),
            ggtheme = theme_minimal())
```
~~~

![mark](http://cdn.liguocheng.top/blog/20200304/T6Avppw9TCVD.png?imageslim)



> - https://www.jianshu.com/p/8a74508c3737
> - https://www.jianshu.com/p/b83ac8f7f5a7
> - https://www.yuque.com/biotrainee/biostat/chapter3-13
> - https://mp.weixin.qq.com/s?__biz=MzI5MTcwNjA4NQ==&mid=2247489825&idx=1&sn=e56d490bec2bed4068cb2f294399e675&chksm=ec0ddcabdb7a55bd5ad91cc16c470250213d78c782da419e70039e851621525674203e47916f&scene=21#wechat_redirect