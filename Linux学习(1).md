# Linux学习(1)

### Linux常用命令

1、cd

Change directory
修改工作目录，cd和ls应该是使用最多的两个命令，尤其是对于Linux目录结构不熟的用户。

```bash
cd /  #进入根目录
cd -  #返回上次的目录
cd ~ #返回home目录
cd ../ # 返回上一级目录
```

2、ls

List files

```bash
-a  列出包括.a开头的隐藏文件的所有文件
-A  通-a，但不列出"."和".."
-l  列出文件的详细信息
-c  根据ctime排序显示
-t  根据文件修改时间排序
```

3、pwd

print working directory

```bash
[root@guocheng test]# pwd
/root/test
```

4、sh
运行脚本的命令，脚本是包含很多命令的一个文件，sh 这个文件，就可以之和运行，例如目录下有个多个sh文件，就可以一次运行。

```bash
sh *.sh
```

5、cp

cp: Copy file
拷贝并粘贴文件
-b  覆盖前做备份
-f  如存在不询问而强制覆盖
-i  如存在则询问是否覆盖
-u  较新才覆盖
-t  将多个源文件移动到统一目录下，目录参数在前，文件参数在后

```
实例 1：cp seq1 seq2
含义：将文件seq1 复制到文件seq2 中，保留seq1
实例2：cp *.fasta ../keep/
含义：将所有文件名以.fasta 结尾的文件复制到上级目录的keep 子目录中
实例 3：cp ../seq/hba_human.sw .
含义：将上级目录的seq 子目录中名为hba_human.sw 的文件复制到当前目录中
实例 4：cp /tmp/tf/zmtf-pep.fasta blast/
含义：将/tmp 目录中tf子目录中名为zmtf-pep.fasta 的文件复制到当前目录中名为blast/的目录中
```

6、mv

mv: Move file
移动文件，相当于windows下的剪切粘贴，如果剪切粘贴到同一目录下，则为重命名。
-b  覆盖前做备份
-f  如存在不询问而强制覆盖
-i  如存在则询问是否覆盖
-u  较新才覆盖
-t  将多个源文件移动到统一目录下，目录参数在前，文件参数在后

```
实例 1：mv seq1 seq2
含义：将文件seq1 改名为seq2，不保留seq1
实例 2：mv hba_human.fasta pku08s1/hba
含义：将当前目录下文件hba_human.fasta 移到子目录pku08s1 下的子目录
hba 中
```

7、rm

rm: Remove file
删除文件
-r  删除文件夹
-f  删除不提示
-i  删除提示
-v  详细显示进行步骤
注意！命令行模式下删除文件不可恢复。

```
rm -rf *.fna  #删除目录下所有以.fna结尾的文件
```

8、ln

ln: Link files
创建连接文件，包括软连接和硬链接，一般软连接比较常用，相当于windows下的快捷方式。
-s  建立软连接  
-v  显示详细的处理过程

```
ln -s /ifs1/Example ./     #为/ifs1/Example目录在当前目录下创建一个快捷方式
```

9、vim

vim是Linux系统自带的文本编辑器，可以理解成为windows系统下的word软件。

:w filename 将文章以指定的文件名保存起来  
:wq 保存并退出
:q! 不保存而强制退出
命令行模式功能键
1）插入模式
 按「i」切换进入插入模式「insert mode」，按"i"进入插入模式后是从光标当前位置开始输入文件；
 按「a」进入插入模式后，是从目前光标所在位置的下一个位置开始输入文字；
 按「o」进入插入模式后，是插入新的一行，从行首开始输入文字。

2）从插入模式切换为命令行模式
按「ESC」键。
3）移动光标
　　vi可以直接用键盘上的光标来上下左右移动，但正规的vi是用小写英文字母「h」、「j」、「k」、「l」，分别控制光标左、下、上、右移一格。
　　按「ctrl」+「b」：屏幕往"后"移动一页。
　　按「ctrl」+「f」：屏幕往"前"移动一页。
　　按「ctrl」+「u」：屏幕往"后"移动半页。
　　按「ctrl」+「d」：屏幕往"前"移动半页。
　　按数字「0」：移到文章的开头。
　　按「G」：移动到文章的最后。
　　按「$」：移动到光标所在行的"行尾"。
　　按「^」：移动到光标所在行的"行首"
　　按「w」：光标跳到下个字的开头
　　按「e」：光标跳到下个字的字尾
　　按「b」：光标回到上个字的开头
　　按「#l」：光标移到该行的第#个位置，如：5l,56l。

4）删除文字
　　「x」：每按一次，删除光标所在位置的"后面"一个字符。
　　「#x」：例如，「6x」表示删除光标所在位置的"后面"6个字符。
　　「X」：大写的X，每按一次，删除光标所在位置的"前面"一个字符。
　　「#X」：例如，「20X」表示删除光标所在位置的"前面"20个字符。
　　「dd」：删除光标所在行。
　　「#dd」：从光标所在行开始删除#行

5）复制
　　「yw」：将光标所在之处到字尾的字符复制到缓冲区中。
　　「#yw」：复制#个字到缓冲区
　　「yy」：复制光标所在行到缓冲区。
　　「#yy」：例如，「6yy」表示拷贝从光标所在的该行"往下数"6行文字。
　　「p」：将缓冲区内的字符贴到光标所在位置。注意：所有与"y"有关的复制命令都必须与"p"配合才能完成复制与粘贴功能。

6）替换
　　「r」：替换光标所在处的字符。
　　「R」：替换光标所到之处的字符，直到按下「ESC」键为止。

7）回复上一次操作
　　「u」：如果您误执行一个命令，可以马上按下「u」，回到上一个操作。按多次"u"可以执行多次回复。

8）更改
　　「cw」：更改光标所在处的字到字尾处
　　「c#w」：例如，「c3w」表示更改3个字

9）跳至指定的行
　　「ctrl」+「g」列出光标所在行的行号。
　　「#G」：例如，「15G」，表示移动光标至文章的第15行行首。

10、mkdir

mkdir：Make directory
创建文件夹
-p  递归创建目录，若父目录不存在则依次创建
-m  自定义创建目录的权限
-v  显示创建目录的详细信息

```
mkdir rnaseq #创建一个名为rnaseq的目录
rmdir rnaseq #删除目录
```

11、cat

cat: concatenate 连接
cat的一个作用是查看文件，一般是比较小的文件，行数小于一个屏幕，最多不要超过两个屏幕，否则会刷屏；
cat另一个作用是合并多个文件，一般配合重定向合并为一个新文件或者将一个文件内容追加到另一个文件结尾。

```bash
cat > cat1
含义：往cat1 中逐行输入文本，用Ctrl-D 结束输入
实例 2：cat >> cat1
含义：往cat1 中逐行追加文本，用Ctrl-D 结束输入
cat 1.txt 2.txt >new.txt
```



12、less / more

less和more都是文件查看工具，但是less功能更多一些，在windows系统下打开一个10G的文件比较困难，但是在Linux下非常方便，less可以打开非常大的文件，压缩格式也可以直接打开。
-m  显示类似于more命令的百分比
-N  显示行号
-S  格式化显示

```
$ less -SN GCF_002156985.1_Harm_1.0_genomic.fna
```

13、head / tail

这两个命令比较简单，只是取一个文件的头部和尾部多少行，默认10行，可以加-n进行设置，利用管道可以取文件中间行。

```
#取文件第21~40行
$ head -n 40 a.txt | tail -n 20
```

14、g(un)zip/ b(un)zip2

gzip和bzip2是文件压缩工具，默认直接对源文件进行处理，压缩比率在2/3左右，都可以进行设置。
加上un，为unpack的意思，表示解压缩。

```
$ gzip a.txt
$ gunzip a.txt.gz
bzip2 test.fa
bunzip2 test.fa
```

15、tar

tar：Tape archive （磁带档案）
tar是一个比较复杂的命令，tar主要用于打包，由于tar能调用gzip或者bzip2进行压缩，而打包和压缩经常如windows系统一样合并为一个过程，新手经常将二者混淆，
 -c  建立打包档案，可搭配 -v 来察看过程中被打包的档名(filename)
 -t  察看打包档案的内容含有哪些档名，重点在察看『档名』就是了；
 -x  解打包或解压缩的功能，可以搭配 -C (大写) 在特定目录解开
辅选项：
 -j  透过 bzip2 的支持进行压缩/解压缩：此时档名最好为 *.tar.bz2
 -z  透过 gzip 的支持进行压缩/解压缩：此时档名最好为 *.tar.gz
 -v  在压缩/解压缩的过程中，将正在处理的文件名显示出来！
 -f filename -f 后面要立刻接要被处理的档名！
对于初学者，记住c是creat，创建，x是解包，z对应gzip，j对应bzip2即可，所以常用的命令如下：

```
$ tar -jcvf filename.tar.bz2 A B C #打包压缩为bz2结尾文件
$ tar -jxvf filename.tar.bz2 # 解压缩.tar.bz2结尾文件

$ tar -zcvf filename.tar.gz A B C #打包压缩为gz结尾文件
$ tar -zxvf filename.tar.gz # 解压缩.tar.gz 结尾文件
```

16、wc 

wc = Word Count
统计一个文件中，行数，单词数，字符数
-l filename 报告行数
-c filename 报告字节数
-m filename 报告字符数
-w filename 报告单词数

```bash
[root@guocheng test]# wc -l 1.pl
17 1.pl
[root@guocheng test]# wc -c 1.pl
381 1.pl
[root@guocheng test]# wc -w 1.pl
26 1.pl
```

17、sort

排序，默认按第一列排序，可以通过-k进行设置；默认排序规则为按ASCII码排序，可以通过-n进行修改；-r取相反方向；
-n  依照数值的大小排序。
-o  将排序后的结果存入指定的文件。
-r  以相反的顺序来排序(从大到小)。
-t  指定排序时所用的栏位分隔字符。
-k  选择以哪个区间进行排序。

```bash
u3567@GenekServer-3:~/rnaseq-apple-training/data/rnaseq-apple$ less -S query_seqs.fa.emapper.annotations
u3567@GenekServer-3:~/rnaseq-apple-training/data/rnaseq-apple$ cat genes.counts.matrix | sort | less -S
u3567@GenekServer-3:~/rnaseq-apple-training/data/rnaseq-apple$ cat genes.counts.matrix | sort -nr | less -S
u3567@GenekServer-3:~/rnaseq-apple-training/data/rnaseq-apple$ cat genes.counts.matrix | sort -nr | less -S
u3567@GenekServer-3:~/rnaseq-apple-training/data/rnaseq-apple$ cat genes.counts.matrix | sort -nrk3 -k2 | less -S
[root@guocheng ~]# sort -t ":" -k2 awk.demo
along:love:youou
linux:redhat:lalala:hahaha
hello:world
u3567@GenekServer-3:~/rnaseq-apple-training/data/rnaseq-apple$ awk '{print $1}' query_seqs.fa.emapper.annotations |sort -n |uniq|wc -l
38385
```

18、 chmod

change mode

用途：改变文件或目录权限

```
实例 1：chmod –w ppf1.fas
含义：取消所有用户对ppf1.fas 的写权限
实例 2：chmod +w seq1
含义：将当前目录下seq1 设置为本用户可写，其他用户权限不变
实例 3：chmod -w keep/
含义：取消子目录keep 写权限，不能在该目录下创建和删除文件或子目录
实例 4：chmod 755 bin/*
含义：将子目录bin 下所有文件设置为本用户可读可写可执行，其它用户可读可执行
```

19、cut

用途：按字段提取表格类文件信息


```
实例 1：cut –f 1 209hba.list
含义：提取文件209hba.list 中第1 字段信息，各字段间按制表键分隔
实例 2：cut –f 1,3 209hba.list
含义：提取文件209hba.list 中第1、3 字段信息，各字段间按制表键分隔
实例 3：cut –d ’ ’ –f 3- taxon.list
含义：提取文件taxon.list 中第3 字段开始所有各字段信息，各字段间按空格分隔
```

20、paste

命令 4：paste
用途：按字段合并表格类文件信息
实例 1：paste 209hba.id 209hba.ac
含义：将文件209hba.id 和209hba.ac 按字段逐行合并，个字段间以制表键分隔

```

```

21、diff

命令 5：diff
用途：显示文件差异
实例 1：diff 10hba.id 10hbb.id
含义：逐行显示文件10hba.id 和10hbb.id 差别

```
[root@guocheng ~]# cat sort1.txt
hello:world
linux:redhat:lalala:hahaha
along:love:youou
[root@guocheng ~]# cat sort1.txt~
111111111111
linux:redhat:lalala:hahaha
along:love:youou
[root@guocheng ~]# diff sort1.txt sort1.txt~
1c1
< hello:world
---
> 111111111111
```

22、uniq

命令 8：uniq
用途：归并
实例 1：uniq hba_hbb.id
含义：归并已排序文件hba_hbb.id 中相同行，若有重复行，只显示一次
实例 2：uniq –d hba_hbb.id
含义：归并已排序文件hba_hbb.id 中相同行，只显示有重复的行
实例 3：uniq –u hba_hbb.id
含义：归并已排序文件hba_hbb.id 中相同行，只显示没有重复的行

```

```

23、xargs

xargs是给命令行传递参数的一个过滤器，也是组合多个命令的一个工具。它把一个数据流分割为一些足够小的块，以方便过滤器和命令进行处理。通常情况下，xargs从管道或者标准输入中读取数据，但是它也能够从文件的输出中读取数据。xargs 是一个强大的命令，它能够捕获一个命令的输出结果，然后传递给另外一个命令。

选项参数：

-a file 从文件中读入作为sdtin。

-d delim 分隔符，默认的xargs分隔符是回车，这里修改的是xargs的分隔符

-e flag flag必须是一个以空格分隔的标志，当xargs分析到含有flag这个标志的时候就停止。

-i 或者是-I，使用-I指定一个替换字符串{}，这个字符串在xargs扩展时会被替换掉，当-I与xargs结合使用，每一个参数命令都会被执行一次： 

-L 后面接一个数字，用从标准输入读取的指定行数的非空参数运行命令。

-n num 后面加次数，表示命令在执行的时候一次用的argument的个数，默认是用所有的。

-P 修改最大的进程数，默认是1，为0时候为尽可能多 ，一般不常用。

-p 询问是否运行命令的参数。

-r no-run-if-empty 当xargs的输入为空的时候则停止xargs，不用再去执行了。

-s num 命令行的最好字符数，指的是xargs后面那个命令的最大命令行字符数。

-t 表示先打印命令，然后再执行。

-x exit的意思，主要是配合-s使用。 

```bash
find ./ -name *.fna | xargs rm
# 搜索当前目录下所有一点fna结尾的文件，然后删除掉。
echo --help  |  xargs cat
# 将--help参数传递给cat命令
find ./ -name "*.fna" | xargs tar -zcvf genome.tar.gz
# 搜索当前目录下所有一点fna结尾的文件，然后打包压缩。
cat passwd.txt |xargs -d: -n 2
# 使用-d来修改分隔符，配合-n，让每行输出固定的内容。
find  ../../ -name  "*.fq.gz  | xargs -I {}  cp {} .
# 将某个目录下fq.gz格式的文件，拷贝至当前目录。
```

24、find

**find 搜索expression部分介绍。**

-perm    #按执行权限来查找 ，比如 755

-user  #按文件属主来查找

-group     #按组来查找

-nogroup  #查无有效属组的文件，即文件的属组在/etc/groups中不存在

-nouser  #查无有效属主的文件，即文件的属主在/etc/passwd中不存

-mtime  -n +n  #按文件更改时间来查找文件，-n指n天以内，+n指n天以前

-atime   -n +n  #按文件访问时间来查GIN: 0px">

-ctime   -n +n #按文件创建时间来查找文件，-n指n天以内，+n指n天以前

-newer  f1 !f2  #查更改时间比f1新但比f2旧的文件

-depth  #使查找在进入子目录前先行查找完本目录

-prune  #忽略某个目录

-empty:空的档案

```bash
**案例一：搜索Data目录下以点fna结尾的文件；**

find  ../Data -name *.fna

**案例二：搜索系统中最近5分钟内编辑过的文件；**

find / -amin 5

注意：当在根目录下搜索的时候，需要小心，由于文件较多，往往速度比较慢，并且系统中有很多实时更新的文件，所以，一般情况下尽量不要在根目录下搜索。

**案例三：查找大于100M的文件；**

find ./-size 100M
**案例四：按照文件类型搜索；**

find  ./ -type 文件类型

c 的档案

d: 目录 

b: 区块装置档案 ，

p: 具名贮列

f: 一般档案

l: 符号连结

s: socket

**案例五：搜索文件，直接处理；**

find ./temp/ -name *.fna -exec rm v{} \;

-exec ，也就是execise，执行。相应命令的形式为'command' { } \;筛选以点fna结尾文件，直接将查找出来的文件删除掉。

**案例六：多条件逻辑操作；**

find ./temp/ -name *.fna -and -size 1M

查找名字中包含fna，并且文件大小大于1M的文件。
```

25、nohup

nohup命令：如果你正在运行一个进程，而且你觉得在退出帐户时该进程还不会结束，那么可以使用nohup命令。该命令可以在你退出帐户/关闭终端之后继续运行相应的进程。nohup就是不挂起的意思( no hang up)。

```bash
nohup ./command.sh > output 2>&1 &
# 最后的&：是指在后台运行，当用户退出（挂起）的时候，命令自动跟着结束
# > output 2>&1表示标准错误和标准输出都导入文件output
# 当一般很大的stdout和stderr不关心的时候可以利用stdout和stderr定向到这里
nohup ./command.sh >/dev/null 2>&1 &
```

26、screen

```
screen [-AmRvx -ls -wipe][-d <作业名称>][-h <行数>][-r <作业名称>][-s <shell>][-S <作业名称>]
```

**参数说明**：

- -A 　将所有的视窗都调整为目前终端机的大小。
- -d<作业名称> 　将指定的screen作业离线。
- -h<行数> 　指定视窗的缓冲区行数。
- -m 　即使目前已在作业中的screen作业，仍强制建立新的screen作业。
- -r<作业名称> 　恢复离线的screen作业。
- -R 　先试图恢复离线的作业。若找不到离线的作业，即建立新的screen作业。
- -s<shell> 　指定建立新视窗时，所要执行的shell。
- -S<作业名称> 　指定screen作业的名称。
- -v 　显示版本信息。
- -x 　恢复之前离线的screen作业。
- -ls或--list 　显示目前所有的screen作业。
- -wipe 　检查目前所有的screen作业，并删除已经无法使用的screen作业。

### 实例

创建 screen 终端

```
# screen //创建 screen 终端
```

创建 screen 终端 并执行任务

```
# screen vi ~/main.c //创建 screen 终端 ，并执行 vi命令
```

离开 screen 终端

```
# screen vi ~/main.c //创建 screen 终端 ，并执行 vi命令

#include 

main ()
{

}

"~/mail.c"       0,0-1    

在 screen 终端 下 按下 Ctrl+a d键
```

重新连接离开的 screen 终端

```
# screen -ls  //显示已创建的screen终端 
There are screens on:
2433.pts-3.linux    (2013年10月20日 16时48分59秒)    (Detached)
2428.pts-3.linux    (2013年10月20日 16时48分05秒)    (Detached)
2284.pts-3.linux    (2013年10月20日 16时14分55秒)    (Detached)
2276.pts-3.linux    (2013年10月20日 16时13分18秒)    (Detached)
4 Sockets in /var/run/screen/S-root.

# screen -r 2276 //连接 screen_id 为 2276 的 screen终端
```

25、grep

文本搜索工具，类似于正则表达式搜索，可以在一个大的文件中快速搜索到满足一定规则的内容。

```bash
#案例一：统计fasta文件中序列的条数；
grep -c ">"  gene.ffn
#案例二：输出满足条件的序列；
grep -A 2 "3 gi 29732 34486" lastz.axt
#案例三：筛选出不满足条件的内容；
ps -fx | grep -v "S"
```

26、sed

sed = Stream Editor
流处理器，sed有非常强大的功能.

```bash
#案例一：输出固定的行
sed -n '1307p'  seq.fna   #输出文件第1307行；
sed -n '100,200' seq.fna  #输出文件第100到200行；

#案例二：替换操作
sed -e 's/gi/GI/' seq.fna  #将文件中gi全部替换为大写GI；
sed -i 's/gi/GI/g' seq.fna   #在原文件上进行替换，并且进行全部替换；
sed -i.bak 's/GI/gi/g' seq.fna  #在原文件上进行替换，并进行备份；
sed -e 's/gi/GI/2；s/ref/REF/2' seq.fna   #只将第二次出现的gi和ref进行替换；
sed -f sed.list cds.list    #根据文件中的模式进行替换，可同时进行多条件替换；
sed -n 's/gi/GI/p' seq.fna  #打印发生替换的行；

#案例三：删除空白行；
sed -e '/^*$/d'  seq.fna  #删除文件中的空白行；

#案例四：行寻址
sed -n '/ref/p' seq.fna   #输出文件中包含ref关键字的行；
sed  '100,2000s/GI/gi/g' seq.fa  #则只替换100行到2000行的内容；
sed  '100,2000！s/GI/gi/g' seq.fa  #加感叹号取反，在这个范围之外的执行操作；

#案例五：删除操作
sed -e '/>/d' seq.fna #删除包含ref的行；
sed -e 's/:.*//g' seq.fna   #删除冒号之后的所有内容；

#案例六：对应替换，类似于tr的功能
sed -e 'y/ATCG/atcg/' seq.fna  #修改大小写
```

27、awk

awk也是非常强大的文本处理工具，awk本身也是一门编程语言；

```bash
#案例1：输出一个列表任意行；
awk '{print $1}' blast_m8.out  #输出blast m8 格式结果的第一列；
awk -F ":" '{print $1,$NF}' passwd.list   #通过-F修改默认分隔符为冒号，输出第一列与最后一列；

#案例2：格式转换
awk '{print"@" $1"\n"$10"\n""+\n"$11""}' all.sam  #将短序列比对上的reads输出出来，生成fastq文件；

#案例3：过滤blast结果
awk ’{if ($3>=80 && $4>=100) print $0}'  blast_m8.out  #过滤blast比对结果，将identity 大于80，并且比对长度大于100bp的结果输出；

#案例4：比较
awk '$8>$10' input.txt #输出第8列大于第10列的行。

#案例5：匹配输出
awk '$0~ /wang/{print $0}' passwd.list   #利用正则表达式，将秘密表中姓wang的账户都输出出来；

#案例6：格式化输出
awk 'BEGIN{print "The Program Begin\n"}{if ($3>=80 && $4>=100) print $0}END{print " The Program End\n"}' input.txt  #利用BEGIN和END关键字生成报告；

#案例7：修改字段和记录分隔符
awk 'BEGIN{OFS="\t"}{print $2,$4,$5}' input.txt   #在BEGIN中设定字段分隔符和记录分隔符；

#案例8：awk编程计算
awk '{x+=$3}END{print x/NR}' input.txt   #计算第三列的平均值，最后在END将其输出出来。

#案例9：awk编程比较大小
awk   'BEGIN { max=100 ;print "max=" max} {max=($1 >max ?$1:max); print $1,"Now max is "max}' input.txt  #取得文件最后一个域的最大值。 

#案例10：awk编程求和
awk '{print $0,$3+$4}' input.txt  #计算第3列和第4列的和。

#案例11：输出固定行内容
awk 'NR>=20&&NR<=80' input.txt  #输出第20到第80行内容。

#案例12：合并文件
awk 'BEGIN{while((getline<"file1")>0)l[$1]=$0}$1 in l{print $0"\t"l[$1]}' file2  #将两个文件按列合并起来，类似jion命令的功能。

#案例13：去重复
awk '!($0 in a) {a[$0];print}' input.txt  # 打印不重复的行，类似uniq的功能;
awk '!($2 in l){print;l[$2]=1}' input.txt #计算第二列内容非冗余的次数，类似于uniq的功能;

#案例14：统计字符
awk '{for(i=1;i!=NF;++i)c[$i]++}END{for (x in c) print x,c[x]}' input.txt 计算每个字符出现的次数，类似wc的功能。

#案例15：替换
awk '{sub(/test/, "no", $0);print}' input.txt 进行替换，类似sed的功能，

#案例16：fastq转换为fasta
awk '{getline seq;getline plus;getline qual;sub("@",">",$0);print $0 "\n"seq}'  test.fastq；
```



30、快捷键

- Ctrl+A 快速回到命令行行首
- Ctrl+E 快速回到命令行行尾
- Ctrl+C 终止当前命令
- Ctrl+D 给程序一个终止信号，一般相当于exit
- Ctrl+R 快速搜索历史记录
- Ctrl+Z 暂停当前任务
- Ctrl+L 清空当前屏幕
- Tab 补齐
- !!：执行上一条命令
- !num：执行历史命令中第num条命令