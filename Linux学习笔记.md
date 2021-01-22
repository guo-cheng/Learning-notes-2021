## Linux学习笔记

### Linux系统简介与目录理解

```bash
##文件最下端为最新文件或目录
ls -ltr
##不报错
mkdir -p test
##文件写入
cat<<END >test.fa
>test.fa
ATTTGGAGTCCCCTAGCTAG
END
##ctrl+D编辑结束
cat > test.sh
#！ bin/bash
echo "hello world!!"
##略过标题行
tail -n +2 test.fa
##按时间排序
ls -lt
##tac: 文件翻转，第一行变为最后一行，第二行变为倒数第二行；
cat <<END | tac
> ATGC
> A
> T
> end
> END
end
T
A
ATGC
##rev: 每列反转，第一个字符变为最后一个字符，第二个字符变为倒数第二个字符。
cat <<END | rev
ATGC
END

CGTA

## In ubuntu,rename
rename 's/test/ligc/' test*.fa

##软链接(原文件要使用全路径。全路径指以/开头的路径)
##井号注释掉命令
##反引号表示得到命令输出
#ln -sf `pwd`/ligc.fa transcriptome/

cut -d ' ' -f 1 ligc.fa | less
sed -i 's/HAHA//' ligc.fa | less
grep '>' ligc.fa | wc -l

##!! 表示上一条命令
##:gs 表示替换，把上一个命令中全部的ligc1替换为ligc2; g: global; s: substitute
cut -d ' ' -f 1 ligc1.fa | less
!!:gs/1/2
cut -d ' ' -f 2 ligc2.fa | less


#转换fastq为fasta，并按顺序重复命名序列
awk 'NR % 4==2 {print ">E" NR/4+0.5 "\n" $0}' 1.fq > 1.fa

#统计序列长度分布
grep -v '>' 1.fa | awk '{print length($0)}' | sort | uniq -c

##反向互补序列
echo 'ACGTAAA' | tr 'ACGT' 'TGCA'| rev
TTTACGT

## cat 命令之前也用过，输出一段文字
## diff 是比较2 个文件的差异的，需要2 个参数
## - (短横线) 表示上一个命令的输出，传递给diff
## < 表示其后的命令的输出，也重定向给diff
cat <<END | diff - <(echo "1 2 3" | tr ' ' '\n')


##
echo "a b c" | tr ' ' '\n' | sed = | sed 'N;s/\n/\t/'
## 把多条序列转成FATSA 格式
## sed = 同时输出行号
## N: 表示读入下一行；sed 命令每次只读一行，加上N 之后就是缓存了第2 行，所有的操作都针对第一行；
## s: 替换；把读取的奇数行行首加一个'>'（偶数行相当于被隐藏了）
## 于是FASTA 格式序列就出来了
##加上N后，相当于echo ''>A1\nactg''然后将\n替换为\t
echo "actg aaaaa cccccg" | tr ' ' '\n' | sed = | sed 'N;s/^/>A/'
>A1
actg
>A2
aaaaa
>A3
cccccg

##将fq四行换成一行
sed 'N;N;N;s/\n/\t/g' 2.fq
@SRR1039513.1 1/1       CCTTGAACCTGGTGCGCTGGCCGGC       +       CHBEDHIIIGIGFF@GIIII;DHGF
##1到10，步长为5
seq -s ' ' 1 2 10

cat <(seq 0 3 17) <(seq 3 6 18) >test
cat test
0369
12
15
39
15

• OFS: 输出文件的列分隔符(output file column separtor)；
• FS 为输入文件的列分隔符(默认为空白字符)；
• awk 中的列从第1 到n 列，分别记录为$1, $2 … $n；
• BEGIN 表示在文件读取前先设置基本参数；与之相对应的是END，只文件读取完成之后进行操作；
• 不以BEGIN, END 开头的{} 就是文件读取、处理的部分。每次对一行进行处理。
sort 2.txt | uniq -c | awk 'BEGIN{OFS="\t";}{print $2,$1}'>2.txt

##第二列按数值大小排序
# 第二列相同的再按第一列的字母顺序的逆序排序(-r)
# 注意看前3 行的顺序与上一步结果的差异
ct@ehbio:~$ sort test2 | uniq -c | awk 'BEGIN{OFS="\t";}{print $2,$1}' | sort -k2,2n -k1,1r


##序列提取
# grep 匹配含有SOX2 的行
# -A 1 表示输出的行中，包含匹配行的下一行(A: after)
grep -A 1 -w 'SOX2' test.fasta
>SOX2
ACGAGGGACGCATCGGACGACTGCAGGACTGTC

##-f : file
head id
>SOX2
>NANOG
grep -A 1 -f id -w test.fasta
>SOX2
ACGAGGGACGCATCGGACGACTGCAGGACTGTC
>NANOG
CGGAAGGTAGTCGTCAGTGCAGCGAGTCCGT

##去除文件中的--
grep -A 1 -f id -w test.fasta | gerp -v -- '--'


# 先判断当前行是不是> 开头，如果是，表示是序列名字行，替换掉大于号，取出名字。
# sub 替换, sub(被替换的部分，要替换成的，待替换字符串)
# 如果不以大于号开头，则为序列行，存储起来。
# seq[name]: 相当于建一个字典，name 为key，序列为值。然后就可以使用name 调取序列。
# 若命令太长，可在末尾加一个\, 换行书写
# awk 中$0 ~ />/ 里面的~ 不表示家目录，而是一个运算符，用来做模式匹配的
# /pattern/ 则表示与什么模式进行匹配，pattern 代表的是匹配模式
# awk 对文件是按行操作的，{} 里面的语句会对文件的每一行都进行判断或操作，循环执行
# $0: 表示当前行所有内容；$1, $2, $3 表示当前行第1,2,3 列
# 关于引号，如果最外层用的是单引号，那么里面最好不要再出现单引号
# 如果最外面用的是双引号，则里面最好不要再出现单引号
# 命令会寻找最近的同样引号进行匹配。
awk 'BEGIN{OFS=FS="\t"}ARGIND==1{if($0~/^>/) {name=$0; sub(">","",name);a[name]=$0;} else a[name]=a[name]"\n"$0;}ARGIND==2{print a[$0];}' test.fasta id
>SOX2
ACGAGGGACGCATCGGACGACTGCAGGACTGTC
>SOX21
ATAGTCGATGCTATCGTAGTC
>NANOG
ATCGTAGCTAGTCG
>NAG
AATCGTAGCTGATCGTTAG
## 理解字典a[key]=value
awk 'BEGIN{OFS=FS="\t"}ARGIND==1{a[$1]=$2}ARGIND==2{print a[$1]}' idmap id
1234
456
5678
7890

## 多行FASTA序列提取要麻烦些，一个办法就是转成单行序列
cat <<END >test.fasta
> >SOX2
> ACGAGGGACGCATCGGACGACTGCAGGACTGTC
> ACGAGGGACGCATCGGACGACTGCAGGACTGTC
> ACGAGGGACGCATCGGACGACTGCAGGAC
> >POU5F1
> CGGAAGGTAGTCGTCAGTGCAGCGAGTCCGT
> CGGAAGGTAGTCGTCAGTGCAGCGAGTCC
> >NANOG
> ACGAGGGACGCATCGGACGACTGCAGGACTGTC
> ACGAGGGACGCATCGGACGACTGCAGG
> ACGAGGGACGCATCGGACGACTGCAGGACTGTC
> ACGAGGGACGCATCGGACGACTGCAGGACTGT
> END
# 第一步所有行变为一行,这一步使用tr 是因为tr里面可以直接识别换行符，而sed不可以,其它的替换都使用sed
# >号前面加换行符
# 先把第一个TAB键变为换行符， 实现序列名字和序列的分离
# 再去掉序列中所有的TAB键
cat test.fasta | tr '\n' '\t'|sed 's/\t>/\n>/g'|sed 's/\t/\n/'|sed 's/\t//g' > 1.fasta

##-A, --show-all
cat -A test.fasta
>SOX2$
ACGAGGGACGCATCGGACGACTGCAGGACTGTC$
ACGAGGGACGCATCGGACGACTGCAGGACTGTC$
ACGAGGGACGCATCGGACGACTGCAGGAC$
>POU5F1$
CGGAAGGTAGTCGTCAGTGCAGCGAGTCCGT$
CGGAAGGTAGTCGTCAGTGCAGCGAGTCC$

##对文件夹中的所有文件修改权限
chmod -R 755 

##连接服务器运行.bash_profile文件
cat .bashrc
if [ -f .bash_profile ]; then
	. .bash_profile
fi


## LD_LIBARY_PATH: 指定动态链接库(so 文件) 的位置，一般在安装软件出错时会用到；
## PYTHONPATH: 指定Python 的安装包的路径；
## PERL5LIB: 指定perl 的安装包的路径

##软件安装软链接
ln -s `pwd`/* /transcriptome/soft

##python包安装
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple htseq

# 安装Github的R包
install.packages("devtools")
devtools::install_github("JustinaZ/pcaReduce")








```



