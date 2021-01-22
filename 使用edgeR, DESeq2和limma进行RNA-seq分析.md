# 使用edgeR, DESeq2和limma进行RNA-seq分析

#### 小虫子

#### 2020/4/10

## 

\##edgeR

edgeR是继limma推出后较早出现的针对RNA-seq差异表达分析的软件包，有着广泛的用户。在运行速度上具有一定的优势。

```
library(edgeR)
## Loading required package: limma
load(file = "ds1.Rdata")
class(counts)
## [1] "matrix"
dim(counts)
## [1] 30727    27
head(counts[, 1:7], 3)
##                 ES.07985 DE.07981 GT.66339 FG.08004 PE.07980 FE.66350 ES.66342
## ENSG00000254468        0        0        0        1        0        0        1
## ENSG00000177951       44       50       24       37       38       41      162
## ENSG00000188076        0        0        0        0        0        0        0
## 为载入的数据分组。这里使用列名提供的信息分组。
grp <- as.factor(substr(colnames(counts), 1, 2)) ##substr截取列名的前两个字母
table(grp)
## grp
## DE ES FE FG GT IS PE PH 
##  3  3  4  3  3  2  6  3
o <- order(grp)
pairs(log2(1+counts[,o[1:7]]), pch=".",lower.panel=NULL) ##这个图给我们样品间关系的一揽印象
```

![mark](http://cdn.liguocheng.top/blog/20200410/IIgE37n26HST.png?imageslim)

```
d <- DGEList(counts=counts, group=grp)
dim(d)
## [1] 30727    27
d <- calcNormFactors(d)
d$samples
##          group lib.size norm.factors
## ES.07985    ES   769440    0.9720613
## DE.07981    DE   947798    1.0560409
## GT.66339    GT   548227    0.8668307
## FG.08004    FG   726088    1.2161278
## PE.07980    PE   688903    1.0602042
## FE.66350    FE   521573    1.1357497
## ES.66342    ES  2281754    0.9035550
## DE.66333    DE  2834208    0.9780780
## GT.66341    GT  1596550    0.7662695
## FG.66346    FG  2070020    1.1650270
## PE.66344    PE  1978504    1.0384803
## FE.66331    FE  1517276    1.0618221
## PH.66332    PH   840380    1.3760235
## PH.66336    PH   852548    1.3735312
## PE.66345    PE   915042    1.2393702
## PE.66330    PE   938173    1.2295965
## FE.66348    FE   629687    1.1130709
## FE.66334    FE   647313    1.1128918
## ES.66335    ES  2048439    0.7027366
## DE.66349    DE  1843746    0.7488982
## GT.66337    GT  1761089    0.8635605
## FG.66351    FG  1417106    0.9453883
## PE.66338    PE  2317354    0.8958021
## PH.66340    PH  4087566    1.1389491
## PE.66329    PE  3700620    1.0792805
## IS.66347    IS 12583285    0.5621091
## IS.66343    IS 33286768    0.9297169
## 这里的norm.factors计算方法有3种，分别是TMM,RLE和upperquartile。默认使用TMM方法
## 去除低表达值的数据，这一步可以减少很多计算量，不做也不影响最终的结果
cps <- cpm(d) ## count per million
k <- rowSums(cps>=1) > 2
d <- d[k,]
dim(d)
## [1] 21707    27
## 如果同一组的biological replicates不 能相聚在一起， 必要时需要考虑重新实验。如果条件允许，这一步就要抛弃一些意外的数据
cols <- as.numeric(d$samples$group)
plotMDS(d,col=cols)
```

![mark](http://cdn.liguocheng.top/blog/20200410/i9bjJtxBKzne.png?imageslim)

```
## multidimensional scaling plot有很多种手段。 LFC就是log2 fold change, BCV就是 biological coefficient of variation. 为了快速绘图，默认情况下只是抽取了top 500个值。

par(mfrow=c(2,2))
plotMDS(d, col=cols, main="500 / lLFC")
plotMDS(d, col=cols, method="bcv", main="500 / BCV")
plotMDS(d, col=cols, top=2000, main="2000 / lLFC")
plotMDS(d, col=cols, top=2000, method="bcv", main="2000 / BCV")
```

![mark](http://cdn.liguocheng.top/blog/20200410/Trl3SKUhFPIb.png?imageslim)

```
## 而后构建模型。
mm <- model.matrix(~-1+grp)
mm
##    grpDE grpES grpFE grpFG grpGT grpIS grpPE grpPH
## 1      0     1     0     0     0     0     0     0
## 2      1     0     0     0     0     0     0     0
## 3      0     0     0     0     1     0     0     0
## 4      0     0     0     1     0     0     0     0
## 5      0     0     0     0     0     0     1     0
## 6      0     0     1     0     0     0     0     0
## 7      0     1     0     0     0     0     0     0
## 8      1     0     0     0     0     0     0     0
## 9      0     0     0     0     1     0     0     0
## 10     0     0     0     1     0     0     0     0
## 11     0     0     0     0     0     0     1     0
## 12     0     0     1     0     0     0     0     0
## 13     0     0     0     0     0     0     0     1
## 14     0     0     0     0     0     0     0     1
## 15     0     0     0     0     0     0     1     0
## 16     0     0     0     0     0     0     1     0
## 17     0     0     1     0     0     0     0     0
## 18     0     0     1     0     0     0     0     0
## 19     0     1     0     0     0     0     0     0
## 20     1     0     0     0     0     0     0     0
## 21     0     0     0     0     1     0     0     0
## 22     0     0     0     1     0     0     0     0
## 23     0     0     0     0     0     0     1     0
## 24     0     0     0     0     0     0     0     1
## 25     0     0     0     0     0     0     1     0
## 26     0     0     0     0     0     1     0     0
## 27     0     0     0     0     0     1     0     0
## attr(,"assign")
## [1] 1 1 1 1 1 1 1 1
## attr(,"contrasts")
## attr(,"contrasts")$grp
## [1] "contr.treatment"
#依据模型估算离散度（dispersion）
#离散度为生物变异系数（biological coefficient of variation）的平方
#(Total CV^2 = Technical CV^2 + Biological CV^2)
d <- estimateGLMCommonDisp(d,mm)
d <- estimateGLMTrendedDisp(d,mm)
d <- estimateGLMTagwiseDisp(d,mm)
## 使用plotBCV来察看同一组内不同样品的不同表达水平值的方差分析
plotBCV(d)
```

![mark](http://cdn.liguocheng.top/blog/20200410/J1ju8JBzcyBu.png?imageslim)

```
##红线所在的Common dispersion有0.34左右。但是当我们调用d$common.dispersion时，它却只有0.12。这是怎么回事呢？这是因为求方根之后才是真正的值

d$common.dispersion
## [1] 0.1200132
sqrt(d$common.dispersion)
## [1] 0.3464291
##对于plotBCV给出的红线，它在0.2~0.4之间都是容易接受的。但如果超过0.5，则需要考虑实验样品之间的差异是否过大了。

plotMeanVar(d, show.raw=TRUE, show.tagwise=TRUE, show.binned=TRUE)
```

![mark](http://cdn.liguocheng.top/blog/20200410/0IxiF2sjfdoD.png?imageslim)

```
##因为生物重复差异在整体的变异系数中占主导地位，所以我们会观察到其基因水平的变异系数反而会随着表达值的升高而升高
##MAplot
par(mfrow=c(1,2))
plotSmear(d, pair=c("ES","DE"), ylim=c(-5,5))
plotSmear(d, pair=c("DE","GT"), ylim=c(-5,5))
```

![mark](http://cdn.liguocheng.top/blog/20200410/CjOJIkQ3Q8yF.png?imageslim)

```
##先做广义线性拟合（Generalized linear models），而后构建比较结构（construct contrast），最后依据比较结构进行比较，给出差异表达的基因
f <- glmFit(d,mm)
con <- makeContrasts("DE-ES"=grpDE-grpES,levels=colnames(mm))
con
##        Contrasts
## Levels  DE-ES
##   grpDE     1
##   grpES    -1
##   grpFE     0
##   grpFG     0
##   grpGT     0
##   grpIS     0
##   grpPE     0
##   grpPH     0
lrt <- glmLRT(f,contrast=con)
topTags(lrt,20)
## Coefficient:  1*grpDE -1*grpES 
##                     logFC   logCPM       LR       PValue          FDR
## ENSG00000095596  8.532652 6.645529 282.6776 1.959308e-63 4.253071e-59
## ENSG00000076356  7.976896 7.286288 268.6676 2.214634e-60 2.403653e-56
## ENSG00000164292 10.583178 6.429308 262.3183 5.360365e-59 3.878581e-55
## ENSG00000185823  7.349748 5.670224 248.5299 5.431810e-56 2.947708e-52
## ENSG00000125798  9.891060 8.036530 223.1547 1.854754e-50 8.052229e-47
## ENSG00000126005 -4.501176 6.472884 219.3086 1.279941e-49 4.630615e-46
## ENSG00000132130 11.314026 6.383096 216.6707 4.815257e-49 1.493211e-45
## ENSG00000104332  4.707115 6.774628 210.5888 1.021903e-47 2.772806e-44
## ENSG00000119242  6.265638 6.646337 197.0680 9.113442e-45 2.198061e-41
## ENSG00000158815 14.931767 7.285482 190.1951 2.882384e-43 6.256791e-40
## ENSG00000266283 11.070748 8.357598 180.5410 3.692255e-41 7.286162e-38
## ENSG00000120875  7.806684 5.493589 179.8987 5.099552e-41 9.224664e-38
## ENSG00000110799  7.638549 4.829652 177.3832 1.806330e-40 2.989779e-37
## ENSG00000121966  7.529798 5.850626 177.2533 1.928268e-40 2.989779e-37
## ENSG00000182798  5.062455 7.539929 171.0935 4.269045e-39 6.177878e-36
## ENSG00000124942  6.854595 7.485109 166.1924 5.021581e-38 6.812716e-35
## ENSG00000110148  6.789228 6.109827 164.4648 1.197330e-37 1.528850e-34
## ENSG00000141448 13.186471 6.557188 162.4788 3.251433e-37 3.859188e-34
## ENSG00000164488  6.428542 5.558317 162.4030 3.377922e-37 3.859188e-34
## ENSG00000169071  5.920406 5.767217 159.4254 1.510780e-36 1.639725e-33
##检查结果
cps <- cpm(d)
o <- order(colnames(counts))
barplot(cps["ENSG00000095596",o], col=cols[o], las=2)
```

![mark](http://cdn.liguocheng.top/blog/20200410/yXcbyKcR30Hb.png?imageslim)

```
##输出结果至文件
tt <- topTags(lrt, n=Inf)$table
write.table(tt, file="LRT1.xls", row.names=T, sep="\t", quote=FALSE)
```

\##DESeq2

```
library(DESeq2)
##我们载入与上一例相同的数据。
load(file = "ds1.Rdata")
ls()
##  [1] "cols"   "con"    "counts" "cps"    "d"      "f"      "grp"    "k"     
##  [9] "lrt"    "mm"     "o"      "tt"
head(counts[, 1:7], 3)
##                 ES.07985 DE.07981 GT.66339 FG.08004 PE.07980 FE.66350 ES.66342
## ENSG00000254468        0        0        0        1        0        0        1
## ENSG00000177951       44       50       24       37       38       41      162
## ENSG00000188076        0        0        0        0        0        0        0
grp <- as.factor(substr(colnames(counts), 1, 2)) ##substr截取列名的前两个字母
dds <- DESeqDataSetFromMatrix(counts, colData=data.frame(grp), design=formula(~-1+grp)) ##这里的colData必须是一个DataFrame或者data.frame。每一行都对应着counts中的一列。design中的公式和limma中的方法一致。
design(dds)
## ~-1 + grp
## ~-1 + grp
dds <- DESeq(dds) ##一步到位，不需要太多步骤
res <- results(dds) ##得到结果
res
## log2 fold change (MLE): grp PH vs DE 
## Wald test p-value: grp PH vs DE 
## DataFrame with 30727 rows and 6 columns
##                            baseMean     log2FoldChange             lfcSE
##                           <numeric>          <numeric>         <numeric>
## ENSG00000254468   0.428679361589238   -1.0076335372903  2.78802991911959
## ENSG00000177951    101.985167633575  0.305638713223266  0.25577414088142
## ENSG00000188076   0.162090719218632                  0  6.34189621938139
## ENSG00000177947    2.62488959493876  -3.02562374318892  1.41532099793108
## ENSG00000254559    496.343480879515  -0.93880360032314 0.312216359706555
## ...                             ...                ...               ...
## ENSG00000248275     19.843767061515   1.59102522415255 0.441870716720352
## ENSG00000248103  0.0137429900755897                  0  6.34189621938139
## ENSG00000238035    127.559550716107  -0.38284132385681 0.283709098447013
## ENSG00000250765    1.86059354928966 -0.516047974980787  1.24577861393626
## ENSG00000230178 0.00750996287943926                  0  6.34189618086069
##                               stat              pvalue                padj
##                          <numeric>           <numeric>           <numeric>
## ENSG00000254468 -0.361414176505138   0.717789851885949                  NA
## ENSG00000177951   1.19495548756418   0.232104421785906   0.385031677559668
## ENSG00000188076                  0                   1                  NA
## ENSG00000177947  -2.13776503536073  0.0325358181254791  0.0856625615722522
## ENSG00000254559  -3.00690073129256 0.00263925933971239  0.0109045105622684
## ...                            ...                 ...                 ...
## ENSG00000248275   3.60065775791036 0.00031741316600316 0.00176820318077756
## ENSG00000248103                  0                   1                  NA
## ENSG00000238035  -1.34941503798233   0.177203693281204   0.315935322835318
## ENSG00000250765 -0.414237304451903   0.678700324172708   0.800188850358528
## ENSG00000230178                  0                   1                  NA
mcols(res, use.names=TRUE) ##结果中各例的意义
## DataFrame with 6 rows and 2 columns
##                        type                               description
##                 <character>                               <character>
## baseMean       intermediate mean of normalized counts for all samples
## log2FoldChange      results      log2 fold change (MLE): grp PH vs DE
## lfcSE               results              standard error: grp PH vs DE
## stat                results              Wald statistic: grp PH vs DE
## pvalue              results           Wald test p-value: grp PH vs DE
## padj                results                      BH adjusted p-values
plotMA(res)
```

![mark](http://cdn.liguocheng.top/blog/20200410/iuAc6n9Iwh8v.png?imageslim)

```
plotDispEsts(dds)
```

![mark](http://cdn.liguocheng.top/blog/20200410/jPCK6MUnhpMd.png?imageslim)

```
write.csv(as.data.frame(res), file="results.csv") ##输出结果
```

\##limma::voom

```
library(limma)
v <- voom(d, mm, plot=TRUE) ##这里的d就是edgeR中的d，下面就是标准的limma流程了。
```

![mark](http://cdn.liguocheng.top/blog/20200410/39OxjXDdvWUT.png?imageslim)

```
vf <- lmFit(v,mm)            # 'mm' 前文定义了
cf <- contrasts.fit(vf,con)  # 'con' 前文定义了
cf <- eBayes(cf)
topTable(cf) 
##                     logFC   AveExpr         t      P.Value    adj.P.Val
## ENSG00000104332  4.682177 5.3343226  16.81841 4.643415e-17 1.007946e-12
## ENSG00000182798  5.049057 2.3421470  16.01505 1.801505e-16 1.781439e-12
## ENSG00000126005 -4.484601 5.7958460 -15.46928 4.668542e-16 1.781439e-12
## ENSG00000147869  7.394596 1.6008457  15.35351 5.732867e-16 1.781439e-12
## ENSG00000076356  7.788253 5.7036419  15.35234 5.744724e-16 1.781439e-12
## ENSG00000158815 11.806049 1.6304752  15.55458 4.016065e-16 1.781439e-12
## ENSG00000164292  9.515117 5.0669158  15.50043 4.418496e-16 1.781439e-12
## ENSG00000164736  9.722025 0.6449381  15.08829 9.218841e-16 2.501417e-12
## ENSG00000003056 -3.453527 8.4615514 -13.93917 7.794904e-15 1.538218e-11
## ENSG00000125798  8.925413 7.0947107  14.10853 5.644888e-15 1.361484e-11
##                        B
## ENSG00000104332 28.59891
## ENSG00000182798 27.43927
## ENSG00000126005 26.28989
## ENSG00000147869 26.22548
## ENSG00000076356 25.21774
## ENSG00000158815 24.91895
## ENSG00000164292 24.89276
## ENSG00000164736 24.23738
## ENSG00000003056 23.84213
## ENSG00000125798 22.78923
```

\##Summary 在Bioconductor的RNA-seq数据的分析中，大多数都是从一个原始的计数表格开始的。然后消除library size的影响，做标准化，收敛dispersion，最后进行差异分析。