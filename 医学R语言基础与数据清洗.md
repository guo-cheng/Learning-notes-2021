## 医学R语言基础与数据清洗

### R包安装与向量

#### R包安装

赋值语句：`Alt + -`等同于 `<-`

```R
library(devtools)
install_github("lijian13/rinds")
library(rinds)
.libPaths()
rm(list = ls())
```

#### 向量(vector)

==向量是有方向性的==；==元素下标(index),即向量中元素的位置==；

```R
a <- 1:5
b <- c(1,2,3,5,4)
x <- c(1,"hello",TRUE,1:4)
identical(a,b)
a[1:3]
b[-c(1,4,5)]
vec <- c(1,4,"hello")
a[1:4]
a[-c(2,4)]
x <- 1
result <- a + x
result
```

### 数值型与逻辑型向量

#### 数值型向量(numeric)

```R
##integer
y <- 1:3
class(y)
##numeric
z <- 1.5
class(z)
seq(from=1,to=5,by=0.5)
seq(from=10,to=1,by=-1)
seq(1,5,4)
seq(1,5,length.out = 4)
seq(1,5,len=10)
seq(1,5,along.with = 1:4)
rep(c(1:3),times=3)
x <- rep(c(1:3),each=3,len=5)
x <- rep(c(1:3),len=5)
length(x)
```

#### 逻辑型向量(本质数值型)(logical)

```R
logic <- rep(c(TRUE,FALSE),len=3)
logic
logic[1] == 1
sum(logic)
```

#### 逻辑表达式与逻辑运算符

```R
logic2 <- c(100>1,99>100)
class(logic2)
logic3 <- c(100==1, 100!=1 , 100>=10, 100<=100,100>90 & 100<99,100>90 | 100<99)
```

```R
##[]只接受TRUE
index <- x>80
index
x[index]
x[x>80]
which(x>80)
x[which(x>80)]
x[17]
x[x>80 & x<90]
x[x>80 | x<90]
x[which(x>80 & x<90)]
```

### 字符串向量(character)

```R
##字符串向量,引号“”
string <- c("abc","def",1,2,3:6)
class(string[3])
is.character(string)
letters
LETTERS
letters[1:4]
letters[25:30]
```

### 因子型向量(有序和无序)--分类变量

#### 有序因子型向量(factor)

```R
my_fac <- factor(rep(c(1,2),times=2),levels = c(1,2),labels = c("male","female"))
my_fac2 <- factor(LETTERS[1:5],labels = letters[1:5])
my_fac3 <- factor(1:5,labels = letters[1:5])
##gl(n, k, length = n*k, labels = seq_len(n), ordered = FALSE)
##Generate factors by specifying the pattern of their levels.
my_fac4 <- gl(2,5,labels = c("control","treatment"))
my_fac5 <- gl(2,1,len=8,labels = c("control","treatment"))
temp_string <- c("a","ab","o","b")
my_fac6<- as.factor(temp_string)
nlevels(my_fac6)
levels(my_fac6)
```

#### 哑变量与有序因子变量

```R
##哑变量(生成reference)
my_fac7 <- relevel(my_fac6,ref="B")
my_fac7
##有序因子变量
x <- c("0mg","10mg","50mg")
my_order_fac <- factor(x,ordered = T)
y <- c("Placebo","10mg","50mg")
my_order_fac2 <- factor(x,ordered = T)
library(DescTools)
my_order_fac3 <- reorder.factor(my_order_fac,new.order = y)
```

### 列表&矩阵

#### 列表(list)

```R
my_list <- list(1,2,3,"hello",TRUE,FALSE)
my_list[4]
class(my_list[4])
my_list[[4]]
class(my_list[[4]])
my_list2 <- list(1:10,letters[1:5])
my_list2[[1]]
class(my_list2[[1]])
my_list2[[2]][1]
class(my_list2[[2]][1])
my_list3 <- list(1:10,letters[1:5],list(10:15,LETTERS[1:5]))
my_list3[[3]][[2]][1]
```

#### 矩阵(二维数组)(matrix)

```R
my_matrix <- matrix(data=1:10,nrow=5,byrow = TRUE)
my_matrix2 <- matrix(data=1:10,nrow=5)
my_matrix3 <- matrix(data=letters[1:8],nrow=3)
my_matrix4 <- matrix(data=1:6,nrow = 3,dimnames = list(c("A","B","C"),c("V1","V2")))
my_matrix5 <- matrix(c(1:5,letters[1:5]),nrow = 2)
##转置(transformation)
t(my_matrix5)
```

### 数组和数据框

#### 数组(行,列,层)(array)

```R
my_array <- array(data=1:16,dim = c(2,4,2))
dim(my_array) <- c(4,2,2)
my_array2 <- array(1:16,dim = c(2,4,2),dimnames = list(LETTERS[1:2],c("COL1","COL2","COL3","COL4"),c("first","second")))
my_array[1,2,2]
my_array[,2,2]
my_array[2,,]
```

#### 数据框(参数即列名)(行:观测值，列:变量)(dataframe)

```R
my_df <- data.frame(name=c("TOM","CANDY","MIKE"),age=c(21,25,30),height=c(156,158,177))
dim(my_df)
View(my_df)
nrow(my_df)
View(iris)
my_df <- data.frame(one = c(1,1.2,3,4,1.5),two = c("tony","mike","sarry","john","TANG"),three = c(TRUE,F,T,F,T),stringsAsFactors = F)
str(my_df)
##去除变量
my_df[-1,-2]
my_df$two <- NULL
##新增变量
my_df$four <- c('q','w','e','r','f')
##修改数据框
my_df2<- edit(my_df)
fix(my_df)
##查看数据框
head(iris,n=10)
tail(iris)
library(psych)
describe(iris)
names(my_df)
dim(my_df)
names(my_df) <- c("Height","Name","Weight")
fix(my_df)
##处理数据框中的中文乱码
Sys.setlocale(locale = 'chinese')
my_df
```

### 数据框的基本操作

```R
##数据框的基本操作
##融合
my_df <- data.frame(one = c(1.2,2,3,5.6,6.4),two = c('张三','李四','王五','赵其','刘能'),three = c(TRUE,T,T,F,T))
my_df2 <- data.frame(six= c(1.2,2,3,5.6),four = c('张三','李四','王五','赵其'),five = c(TRUE,T,T,F))
my_df3 <- cbind(my_df,my_df2)
my_df4 <- rbind(my_df,my_df2)
my_df5 <- merge(my_df,my_df2,by = 'one')
my_df6 <- merge(my_df,my_df2,all=T)
##切分
View(iris)
str(iris)
##设置种子数
set.seed(2020)
##sample
iris_sub <- iris[sample(1:nrow(iris),30),]
set.seed(2020)
iris_sub2 <- iris[sample(1:nrow(iris),30),]
identical(iris_sub,iris_sub2)
##split
iris_sub3 <- split(iris,f = iris$Species)
class(iris_sub3)
setosa <- iris_sub3[1]
##方括号
iris_sub4 <- iris[iris$Species == 'setosa' & iris$Sepal.Length > 5,1:2]
##subset
iris_sub5 <- subset(iris,iris$Species == 'setosa' & iris$Sepal.Length > 5,select = 1:2)
```

### 条件与循环

```R
##条件与循环
##条件
##if
i <- 1
if (i > 0) print('i is positive') else
  print('i is negative')

i <- 3
if (i>0 & i<2) print('i = 1') else
  print('i don\'t know')

x <- 1
if (x > 1){
  y = x * 2
  z = y + 3
} else
{
  y = x + 3
  z = y * 3
}
y;z

x <- 10
y <- c(8,9,10,11)
if (x > y) y else x  

##循环
##repeat
i <- 5
repeat{if (i > 25) break else{
  print(i)
  i = i + 5}
}
##while
i <- 5
while(i <= 25){ 
  print(i)
  i = i + 5
}
##for循环
for (i in 1:5){
  print(i)
}

set.seed(2020)
x <- sample(1:100,10)
y <- sample(10:100,10)
for(i in 1:10){
  z[i] = x[i] > y[i]
}
z

set.seed(2020)
x <- sample(1:100,10)
y <- sample(10:100,10)
z <- NULL
for(i in 1:10){
  if(x[i] > y[i]){
    z = append(z,x[i])
  }
}
z
##for循环嵌套
mat <- matrix(NA,nrow = 4,ncol=5)
for(i in 1:4){
  for(j in 1:5){
    mat[i,j] = 2
  }
}
mat
```

### 自定义函数与数据读取

```R
##自定义函数
mean(c(1:5,NA),na.rm = T)
my_fun1 <- function(x,y){
  x + y
}
my_fun1(1,2)

my_fun2 <- function(x,y=2){
  x + y
}
my_fun2(2,2)
my_fun2(1)

values <- c(sqrt(1:100))
my_fun3 <- function(x,...){
  print(x)
  summary(...)
}
my_fun3("Here is my summary for values:",values,digits=2)

addemup <- function(x,...){
  args <- list(...)
  for (a in args) 
    x <- x + a 
    x
}
addemup(1,2,3,4,5)

normalize <- function(x,m = mean(x,...),s = sd(x,...),...){
  (x-m) / s
}
normalize(1:50)

##数据读取
my_iris2 <- read.csv(file='测序仪性能比较大全.csv',header = T,sep=",",
                     na.strings = c(999,000),stringsAsFactors = F)
my_iris3 <- read.table(file = 'SNMP_group_file.txt',header = T)
library("readxl")
my_iris4 <- read_excel(path = '测序仪性能比较大全.xlsx',sheet = 1,col_types = "text")
library("XLConnect")
my_iris5 <- readWorksheetFromFile(file = '测序仪性能比较大全.xlsx',sheet = 1,startRow=2,endRow=4,startCol=1,endCol=4)
```

