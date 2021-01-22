# R_Bioconductor入门-2



#### 2020/4/14

## 字符串处理

字符串处理是生物信息当中经常遇到的问题。常见的操作有：赋值，获取字符串长度，截取，替换，联接，分割，比较，排序，匹配，部分匹配，正则匹配，格式化，分行

### 常规操作

```R
#赋值
mychar <- c("ACTACCACTAACCACT","TCATCCATTCGTGGG","GTTGTTCCATAG")
#获取字串长度
nchar(mychar)
## [1] 16 15 12
length(mychar)
## [1] 3
#截取
substr(mychar, 2, 4)
## [1] "CTA" "CAT" "TTG"
substr(mychar, 2, 4)<-"TTT"
mychar
## [1] "ATTTCCACTAACCACT" "TTTTCCATTCGTGGG"  "GTTTTTCCATAG"
#替换
dna2rna<-function(inputStr){
   if(!is.character(inputStr))
         stop("need character input")
     is = toupper(inputStr)
     chartr("T", "U", is)
}
dna2rna(mychar)
## [1] "AUUUCCACUAACCACU" "UUUUCCAUUCGUGGG"  "GUUUUUCCAUAG"
#联接
paste("prefix",mychar,"postfix",sep="-")
## [1] "prefix-ATTTCCACTAACCACT-postfix" "prefix-TTTTCCATTCGTGGG-postfix" 
## [3] "prefix-GTTTTTCCATAG-postfix"
paste(mychar,collapse="--")
## [1] "ATTTCCACTAACCACT--TTTTCCATTCGTGGG--GTTTTTCCATAG"
#分割
strsplit(mychar,"C")
## [[1]]
## [1] "ATTT" ""     "A"    "TAA"  ""     "A"    "T"   
## 
## [[2]]
## [1] "TTTT"  ""      "ATT"   "GTGGG"
## 
## [[3]]
## [1] "GTTTTT" ""       "ATAG"
strsplit(mychar,"[CG]")
## [[1]]
## [1] "ATTT" ""     "A"    "TAA"  ""     "A"    "T"   
## 
## [[2]]
## [1] "TTTT" ""     "ATT"  ""     "T"    ""     ""    
## 
## [[3]]
## [1] ""      "TTTTT" ""      "ATA"
strsplit(mychar, "")
## [[1]]
##  [1] "A" "T" "T" "T" "C" "C" "A" "C" "T" "A" "A" "C" "C" "A" "C" "T"
## 
## [[2]]
##  [1] "T" "T" "T" "T" "C" "C" "A" "T" "T" "C" "G" "T" "G" "G" "G"
## 
## [[3]]
##  [1] "G" "T" "T" "T" "T" "T" "C" "C" "A" "T" "A" "G"
#比较
mychar[1]>mychar[2]
## [1] FALSE
#排序
sort(mychar)
## [1] "ATTTCCACTAACCACT" "GTTTTTCCATAG"     "TTTTCCATTCGTGGG"
#匹配
exT<-c("Intron", "Exon", "promoter","enhancer")
match("Exon", exT)
## [1] 2
"promoter" %in% exT
## [1] TRUE
#部分匹配
pmatch("E", exT)
## [1] 2
pmatch("X", exT)
## [1] NA
charmatch("E", exT)
## [1] 2
charmatch("x", exT)
## [1] NA
pmatch(c("Exo","enh"), exT)
## [1] 2 4
#格式化
format(1:10)
##  [1] " 1" " 2" " 3" " 4" " 5" " 6" " 7" " 8" " 9" "10"
format(1:10, trim = TRUE)
##  [1] "1"  "2"  "3"  "4"  "5"  "6"  "7"  "8"  "9"  "10"
zz <- data.frame("(row names)"= c("aaaaa", "b"), check.names=FALSE)
format(zz)
##   (row names)
## 1       aaaaa
## 2           b
format(zz, justify = "left")
##   (row names)
## 1       aaaaa
## 2       b
format(13.7, nsmall = 3)
## [1] "13.700"
format(c(6.0, 13.1), digits = 2)
## [1] " 6" "13"
format(c(6.0, 13.1), digits = 2, nsmall = 1)
## [1] " 6.0" "13.1"
## use of scientific
format(2^31-1)
## [1] "2147483647"
format(2^31-1, scientific = TRUE)
## [1] "2.147484e+09"
## a list
z <- list(a=letters[1:3], b=(-pi+0i)^((-2:2)/2), c=c(1,10,100,1000),
           d=c("a", "longer", "character", "string"))
format(z, digits = 2)
##                                                              a 
##                                                      "a, b, c" 
##                                                              b 
## "-0.32+0.00i, 0.00-0.56i, 1.00+0.00i, 0.00+1.77i, -3.14+0.00i" 
##                                                              c 
##                                             "1, 10, 100, 1000" 
##                                                              d 
##                   "a        , longer   , character, string   "
format(z, digits = 2, justify = "left", trim = FALSE)
##                                                                 a 
##                                                         "a, b, c" 
##                                                                 b 
## "-0.32+0.00i,  0.00-0.56i,  1.00+0.00i,  0.00+1.77i, -3.14+0.00i" 
##                                                                 c 
##                                          "   1,   10,  100, 1000" 
##                                                                 d 
##                      "a        , longer   , character, string   "
#分行
x <-paste(readLines(file.path(R.home(), "COPYING")), collapse="\n")
strwrap(x, 30, prefix = "BIOCONDUCTOR: ")[1:6]
## [1] "BIOCONDUCTOR: GNU GENERAL"     "BIOCONDUCTOR: PUBLIC LICENSE" 
## [3] "BIOCONDUCTOR: Version 2, June" "BIOCONDUCTOR: 1991"           
## [5] "BIOCONDUCTOR: "                "BIOCONDUCTOR: Copyright (C)"
writeLines(strwrap(x, 30, prefix="BIOCONDUCTOR: ")[1:6])
## BIOCONDUCTOR: GNU GENERAL
## BIOCONDUCTOR: PUBLIC LICENSE
## BIOCONDUCTOR: Version 2, June
## BIOCONDUCTOR: 1991
## BIOCONDUCTOR: 
## BIOCONDUCTOR: Copyright (C)
```

## 正则匹配

正则的使用主要涉汲以下7个函数：grep, grepl, sub, gsub, regexpr, gregrexpr, regexec。而象strsplit, apropos以及browseEnv都是基于这7个函数基础之上的。

```R
pattern="^[A-Za-z0-9\\._%+-]+@[A-Za-z0-9\\.-]+\\.[A-Za-z]{2,4}$"
str<-c("abc","someone@bioconductor.org","efg","anotherone@gmail.com","thirdone@yahoo.cn")
#grepl会返回一个逻辑值，l就代表logical, g就代表global
grepl(pattern,str)
## [1] FALSE  TRUE FALSE  TRUE  TRUE
#grep会返回匹配的id
grep(pattern,str)
## [1] 2 4 5
#regexpr会返回一个数字，1表示匹配，-1表示不匹配，还会返回两个属性，匹配的长度以及是否使用useBytes。useBytes一般很少会使用到false，因为我们不处理宽字符。
regexpr(pattern,str)
## [1] -1  1 -1  1  1
## attr(,"match.length")
## [1] -1 24 -1 20 17
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
#regexec会返回一个list，下面的内容是第一个匹配及其长度
regexec("\\w+@\\w+\\.[a-zA-Z]{2,4}","abc@bioconductor.org,efd@bioconductor.org")
## [[1]]
## [1] 1
## attr(,"match.length")
## [1] 20
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
正则的使用已经看到了，但是让人看不明白就是字符. \ | ( ) [ { ^ $ * + - ? 这些符号都是什么意思呢？下面就来仔细讲讲perl中的正则符号。

.  # 除了换行以外的任意字符
^    # 一行字符串的起始，它并不代表第一个字符，只代表这里开始新的一行字符串。
$    # 一行字符串的结束，它并不代表最后一个字符（因为换行符并不会被包含进匹配当中），只代表一行字符串到这里结束。
*    # 零个或者多个之前的字符
+    # 一个或者多个之前的字符
?    # 零个或者一个之前的字符
# 示例部分
t.e    # t后面跟任意一个非换行字符然后跟字符e
    # 它可以匹配        the
    #                 tre
    #                 tle
    # 但是不匹配 te
    #           tale
^f    # 一行字符以f起始
^ftp    # 一行字符以ftp起始
e$    # 一行字符以e结尾
tle$    # 一行字符以tle结尾
und*    # un跟着零个或者多个d字符
    # 它会匹配 un
    #         und
    #         undd
    #         unddd (etc)
.*    # 任意一个无换行的字符串，
    #  . 匹配任何一个非换行字符
    #  * 将匹配一个或者多个之前的字符.
^$    # 一个空行.

 # 在正则中有方括号[]，代表可以匹配其中任何一个字符。而^在[]中就有了新的意义，代表“非”, -代表了“之间”
[qjk]        # q，j，k中任意一个字符
[^qjk]        # 非q，j，k的任意其它字符
[a-z]        # a至z中任意一个小写字符
[^a-z]        # 非任意一个a至z小写字符的其它字符（可以是大写字符）
[a-zA-Z]    # 任意一个英文字母
[a-z]+        # 一个或者多个小写英文字母

 # |代表或者 小括号(...)可以把或者的部分括起来。注意小括号可能还有别的用途，但是在R当中先不使用。

jelly|cream    # jelly或者cream
(eg|le)gs    # eggs或者legs
(da)+        # da或者dada或者dadada 或者更多个da的重复

 # 大括号括住1至2个数字，代表重复次数。
*    # 零个或者多个之前的字符
+    # 一个或者多个之前的字符
?    # 零个或者一个之前的字符
{n}    # n个之前的字符
{n,}    # 大于等于n个之前的字符
{n,m}    # n至m个之前的字符

 # 下面的是一些字符被转义符\转义会赋以了一些新的（有可能是约定俗成的）意义
\n        # 换行符
\t        # tab
\w        # 任意字母（包括下划线）或者数字
        # 等同于[a-zA-Z0-9_]
\W        # \w的反义.
        # 等同于[^a-zA-Z0-9_]
\d        # 任意一个数字，等同于[0-9]
\D        # \d的反义，等同于[^0-9]
\s        # 任意一个空格，比如,
        # space, tab, newline, 等
\S        # \s的反义，任意一个非空格
\b        # 单词的边界, 不能使用在[]内
\B        # \b的反义

 # 很明显，对于保留字符$, |, [, ), \, / 等等都需要转义字符\来转义表示：

\|        # 竖线
\[        # \[左方括号 \]右方括号
\)        # \(左小括号 \)右小括号
\*        # 星号
\^        # 开号
\/        # 反斜杠
\\        # 斜杠
POSIX中定义的一些特殊意义的字符
[:alnum:]  # [:alpha:]和[:digit:]的组合
[:alpha:]    # [:lower:]和[:upper:]的组合
[:blank:]    # 空格（space, tab），不包括换行符
[:cntrl:]    # 控制符，在ASCII码中000~037或者177
[:digit:]    # 任意数字：0-9
[:graph:]    # [:alnum:]和[:punct:]的组合
[:lower:]    # 当前字符集的小写字母
[:print:]    # 可打印出来的字符，[:graph:]以及空格
[:punct:]    # 标点符号，包括：^ ! " # $ % & ' ( ) * + - . / : ; < = > ? @ [ ] \ _ { } ` ~
[:space:]    # 空格，包括tab, newline, vertical tab, form feed, carriage return, and space
[:upper:]    # 当前字符集的大写字母
[:xdigit:]    # 16进制数 0-9a-fA-F
锚点特殊字符
^ # 一行字符串的起始，它并不代表第一个字符，只代表这里开始新的一行字符串。
$    # 一行字符串的结束，它并不代表最后一个字符（因为换行符并不会被包含进匹配当中），只代表一行字符串到这里结束。
\<    # 单词左边界
\>    # 单词右边界
依据上面的介绍，网址的简单匹配可以写为： pattern <- "^[A-Za-z0-9\\._%+-]+@[A-Za-z0-9\\.-]+\\.[A-Za-z]{2,4}$" 可以改写为 pattern <- "^[\\w\\._%+-]+@[\\w\\.-]+\\.[A-Za-z]{2,4}$" 或者 pattern <- "^[[:alnum:]\\._%+-]+@[[:alnum:]\\.-]+\\.[[:alpha:]]{2,4}$" 为什么转义字符()都要写两次呢？因为R本身也把\当成转义字符，所以在写pattern的时候，就需要使用\\来表示转义字符。还有一种办法就是设置fixed为TRUE。
```

那么参数中perl是什么意思呢？其实就是指是否使用PCRE的算法，我们来看实例：

```R
#gregexpr也会返回一个list， 下面的内容是每一个匹配及其长度以及useBytes。g就代表global
gregexpr("\\w+@\\w+\\.[a-zA-Z]{2,4}","abc@bioconductor.org,efd@bioconductor.org")
## [[1]]
## [1]  1 22
## attr(,"match.length")
## [1] 20 20
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
#sub和gsub都用来做正则替换，其区别只在于g所代表的global。sub只替换遇到的第一个匹配，而gsub会替换所有的匹配。
#需要注意的是，这里的匹配都是对应的一个字符串而言的，如果是多个字符串，要区别为每一个来单独对待。
sub("\\w+@\\w+\\.[a-zA-Z]{2,4}","sub_function","abc@bioconductor.org,efd@bioconductor.org")
## [1] "sub_function,efd@bioconductor.org"
gsub("\\w+@\\w+\\.[a-zA-Z]{2,4}","gsub_function","abc@bioconductor.org,efd@bioconductor.org")
## [1] "gsub_function,gsub_function"
regexpr("foo|foobar","myfoobar")
## [1] 3
## attr(,"match.length")
## [1] 6
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
regexpr("foo|foobar","myfoobar", perl=TRUE)
## [1] 3
## attr(,"match.length")
## [1] 3
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
```

## 生物字符串

生物字符串的常见操作比如求互补序列，反向序列，反向互补序列，翻译，转录，逆转录，碱基频率统计，序列比对等. 提供这些常操作的软件包是Biostrings。它有一个基础类，BString，下面有三个继承类：DNAString, RNAString以及AAString。比对的结果会放在BStringAlign类当中.

```R
library(Biostrings)
dna<-DNAString("TCTCCCAACCCTTGTATAAGTATAAATCGT")
RNAString(dna) ## DNA转RNA，只是将T换成U
##   30-letter "RNAString" instance
## seq: UCUCCCAACCCUUGUAUAAGUAUAAAUCGU
reverse(dna) ## 反向
##   30-letter "DNAString" instance
## seq: TGCTAAATATGAATATGTTCCCAACCCTCT
complement(dna) ## 互补
##   30-letter "DNAString" instance
## seq: AGAGGGTTGGGAACATATTCATATTTAGCA
reverseComplement(dna) ## 反向互补
##   30-letter "DNAString" instance
## seq: ACGATTTATACTTATACAAGGGTTGGGAGA
rna<-RNAString(complement(dna)) ## 转录，注意它与dna2rna的不同
rna
##   30-letter "RNAString" instance
## seq: AGAGGGUUGGGAACAUAUUCAUAUUUAGCA
DNAString(rna) ## RNA转DNA，只是将U换成T
##   30-letter "DNAString" instance
## seq: AGAGGGTTGGGAACATATTCATATTTAGCA
DNAString(complement(rna)) ## 逆转录为cDNA
##   30-letter "DNAString" instance
## seq: TCTCCCAACCCTTGTATAAGTATAAATCGT
codons(rna) ## 密码子
##   Views on a 30-letter RNAString subject
## subject: AGAGGGUUGGGAACAUAUUCAUAUUUAGCA
## views:
##      start end width
##  [1]     1   3     3 [AGA]
##  [2]     4   6     3 [GGG]
##  [3]     7   9     3 [UUG]
##  [4]    10  12     3 [GGA]
##  [5]    13  15     3 [ACA]
##  [6]    16  18     3 [UAU]
##  [7]    19  21     3 [UCA]
##  [8]    22  24     3 [UAU]
##  [9]    25  27     3 [UUA]
## [10]    28  30     3 [GCA]
translate(rna) ## 翻译
##   10-letter "AAString" instance
## seq: RGLGTYSYLA
alphabetFrequency(dna) ## 碱基频率统计
##  A  C  G  T  M  R  W  S  Y  K  V  H  D  B  N  -  +  . 
##  9  8  3 10  0  0  0  0  0  0  0  0  0  0  0  0  0  0
hasOnlyBaseLetters(dna)
## [1] TRUE
uniqueLetters(dna)
## [1] "A" "C" "G" "T"
letterFrequency(dna, letters="ACG")
## A|C|G 
##    20
## 计算GC比
GC_content <- letterFrequency(dna, letters="CG")/letterFrequency(dna, letters="ACGT"); GC_content
##       C|G 
## 0.3666667
##搜索
(mT <- matchPattern("TATA", dna))
##   Views on a 30-letter DNAString subject
## subject: TCTCCCAACCCTTGTATAAGTATAAATCGT
## views:
##     start end width
## [1]    15  18     4 [TATA]
## [2]    21  24     4 [TATA]
## 比对
aa1<-AAString("HXBLVYMGCHFDCXVBEHIKQZ")
aa2<-AAString("QRNYMYCFQCISGNEYKQN")
pairwiseAlignment(aa1, aa2, substitutionMatrix="BLOSUM62", type="global")
## Global PairwiseAlignmentsSingleSubject (1 of 1)
## pattern: HXBLVYMGCHFDCXVBEHIKQZ
## subject: QRN--YMYC-FQCISGNEYKQN
## score: 9
## 局部比对
pairwiseAlignment(aa1, aa2, substitutionMatrix="BLOSUM62", type="local")
## Local PairwiseAlignmentsSingleSubject (1 of 1)
## pattern: [6] YMGCHFDCXVBEHIKQ
## subject: [4] YMYC-FQCISGNEYKQ
## score: 24
s1 <- 
    DNAString("ACTTCACCAGCTCCCTGGCGGTAAGTTGATCAAAGGAAACGCAAAGTTTTCAAG")
s2 <-
    DNAString("GTTTCACTACTTCCTTTCGGGTAAGTAAATATATAAATATATAAAAATATAATTTTCATC")

mat <- nucleotideSubstitutionMatrix(match = 1, mismatch = -3, baseOnly = TRUE)
pairwiseAlignment(s1, s2, substitutionMatrix = mat,
                      gapOpening = -5, gapExtension = -2)
## Global PairwiseAlignmentsSingleSubject (1 of 1)
## pattern: ACTTCACCAGCTCCCTGGCGGTAAGTTGATC---AAAGG---AAACGCAAAGTTTTCAAG
## subject: GTTTCACTACTTCCTTTCGGGTAAGTAAATATATAAATATATAAAAATATAATTTTCATC
## score: -52
pairwiseAlignment(s1, s2, type = "local", substitutionMatrix = mat,
                      gapOpening = -5, gapExtension = -2)
## Local PairwiseAlignmentsSingleSubject (1 of 1)
## pattern: [20] GGTAAGT
## subject: [20] GGTAAGT
## score: 7
pairwiseAlignment(s1, s2, type = "overlap", substitutionMatrix = mat,
                      gapOpening = -5, gapExtension = -2)
## Overlap PairwiseAlignmentsSingleSubject (1 of 1)
## pattern: [54] G
## subject:  [1] G
## score: 1
```