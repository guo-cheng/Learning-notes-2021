# Linux文本三剑客：grep、sed、awk

### 2. grep

`grep`全称是**Global Regular Expression Print**，全面搜索正则表达式并把行打印出来，是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。

#### 2.1 命令格式

grep [option] pattern file

#### 2.2 命令功能

用于过滤/搜索的特定字符。可使用正则表达式能多种命令配合使用，使用上十分灵活。

#### 2.3 命令参数

- A<显示行数>：除了显示符合范本样式的那一列之外，并显示该行之后的内容。
-  -B<显示行数>：除了显示符合样式的那一行之外，并显示该行之前的内容。
-  -C<显示行数>：除了显示符合样式的那一行之外，并显示该行之前后的内容。
-  -c：统计匹配的行数
-  **-e ：实现多个选项间的逻辑or 关系**
-  **-E：扩展的正则表达式**
-  -f FILE：从FILE获取PATTERN匹配
-  -F ：相当于fgrep
-  -i --ignore-case #忽略字符大小写的差别。
-  -n：显示匹配的行号
-  -o：仅显示匹配到的字符串
-  -q： 静默模式，不输出任何信息
-  -s：不显示错误信息。
-  **-v：显示不被pattern 匹配到的行，相当于[^] 反向匹配**
-  -w ：匹配 整个单词

```bash
(ligc) u3567@GenekServer-3:~/test$ cat test2
aaa
bbbbbb
AAAaaa
BBBBASDAABBDA
(ligc) u3567@GenekServer-3:~/test$ grep -A2 b test2
bbbbbb
AAAaaa
BBBBASDAABBDA
(ligc) u3567@GenekServer-3:~/test$ grep -B1 b test2
aaa
bbbbbb
(ligc) u3567@GenekServer-3:~/test$ grep -C1 b test2
aaa
bbbbbb
AAAaaa
(ligc) u3567@GenekServer-3:~/test$ grep -c aaa test2
2
(ligc) u3567@GenekServer-3:~/test$ grep -e AAA -e bbb test2
bbbbbb
AAAaaa
(ligc) u3567@GenekServer-3:~/test$ grep -i -n b test
1:abc
(ligc) u3567@GenekServer-3:~/test$ grep -i -n B test
1:abc
(ligc) u3567@GenekServer-3:~/test$ grep -i -n B test2
2:bbbbbb
4:BBBBASDAABBDA
(ligc) u3567@GenekServer-3:~/test$ grep -o ASDA test
(ligc) u3567@GenekServer-3:~/test$ grep -o ASDA test2
ASDA
(ligc) u3567@GenekServer-3:~/test$ grep -v aaa test2
bbbbbb
BBBBASDAABBDA
(ligc) u3567@GenekServer-3:~/test$ grep -w aaa test2
aaa
(ligc) u3567@GenekServer-3:~/test$ grep -w bbb test2
(ligc) u3567@GenekServer-3:~/test$ vi grep.tst
(ligc) u3567@GenekServer-3:~/test$ cat grep.tst
aaa
(ligc) u3567@GenekServer-3:~/test$ grep -f grep.tst test2
aaa
AAAaaa
u3567@GenekServer-3:~/test$ grep "\<B.*A\>" test2
BBBBASDAABBDA
```



#### 2.4 正则表达式

正则表达式(regular expression)描述了一种字符串匹配的模式（pattern），可以用来检查一个串是否含有某种子串、将匹配的子串替换或者从某个串中取出符合某个条件的子串等。

<img src="http://cdn.liguocheng.top/blog/20200524/wpeNNomSdEVk.png?imageslim" alt="regular expression" style="zoom:80%;" />



#### 2.5 示例

```bash
(ligc) u3567@GenekServer-3:~/test$ cat > test1 <<END
ggle
gogle
google
gagle
goooooooooooooooooooooooooooogle
END
(ligc) u3567@GenekServer-3:~/test$ grep 'g[o]*gle' test1
ggle
gogle
google
goooooooooooooooooooooooooooogle
(ligc) u3567@GenekServer-3:~/test$ grep 'g[o].*gle' test1
gogle
google
goooooooooooooooooooooooooooogle
(ligc) u3567@GenekServer-3:~/test$ grep 'g[o]+gle' test1
(ligc) u3567@GenekServer-3:~/test$ grep 'g[o]\+gle' test1
gogle
google
goooooooooooooooooooooooooooogle
(ligc) u3567@GenekServer-3:~/test$ grep 'g[o]\?gle' test1
ggle
gogle
(ligc) u3567@GenekServer-3:~/test$ grep 'g[o]{1,2}gle' test1
(ligc) u3567@GenekServer-3:~/test$ grep -E 'g[o]{1,2}gle' test1
gogle
google
(ligc) u3567@GenekServer-3:~/test$ grep -E 'g[o]{10,}gle' test1
goooooooooooooooooooooooooooogle
(ligc) u3567@GenekServer-3:~/test$ grep -E 'g[o]{,10}gle' test1
ggle
gogle
google
(ligc) u3567@GenekServer-3:~/test$ egrep 'g[o]{,10}gle' test1
ggle
gogle
google
```

#### **2.6 分组和后向引用**

（1）格式

① 分组：\(\) 将一个或多个字符捆绑在一起，当作一个整体进行处理

　　分组括号中的模式匹配到的内容会被正则表达式引擎记录于内部的变量中，这些变量的命名方式为: \1, \2, \3, ...

② 后向引用

引用前面的分组括号中的模式所匹配字符，而非模式本身

\1 表示从左侧起第一个左括号以及与之匹配右括号之间的模式所匹配到的字符

\2 表示从左侧起第2个左括号以及与之匹配右括号之间的模式所匹配到的字符，以此类推

\& 表示前面的分组中所有字符

```
u3567@GenekServer-3:~/test$ cat test4
Hello world Hello world
Hiiii world Hiiii world
Hello world Heiii wwwww
u3567@GenekServer-3:~/test$ grep '\(He\).*\(wo\).*\1' test4
Hello world Hello world
Hello world Heiii wwwww
u3567@GenekServer-3:~/test$ grep '\(He\).*\(wo\).*\2' test4
Hello world Hello world
u3567@GenekServer-3:~/test$ grep '\(He\).*\(ww\).*\2' test4
Hello world Heiii wwwww
u3567@GenekServer-3:~/test$ grep '\(He\).*\(ii\).*\2' test4
u3567@GenekServer-3:~/test$ grep '\(He\).*\(i\).*\2' test4
Hello world Heiii wwwww
```



### 3. **sed**

#### **3.2.1 命令格式**

```
sed [options] "[地址定界]command" file(s)
```

　　

#### **3.2.2 常用选项options**

-  **-n**：不输出模式空间内容到屏幕，即不自动打印，只打印匹配到的行
-  **-e：**多点编辑，对每行处理时，可以有多个Script
-  **-f**：把Script写到文件当中，在执行sed时-f 指定文件路径，如果是多个Script，换行写
-  **-r**：支持**扩展的正则**表达式
-  **-i**：直接将处理的结果写入文件
-  **-i.bak**：在将处理的结果写入文件之前备份一份

 

#### **3.2.3 地址定界**

-  不给地址：对全文进行处理
- 单地址：
  -  \#: 指定的行
  -  /pattern/：被此处模式所能够匹配到的每一行
- 地址范围：
  -  \#,#
  -  \#,+#
  -  /pat1/,/pat2/
  -  \#,/pat1/
- ~：步进
  -  sed -n **'1~2p'** 只打印奇数行 （1~2 从第1行，一次加2行）
  -  sed -n **'2~2p'** 只打印偶数行

 

#### **3.2.4 编辑命令command**

-  **d：删除**模式空间匹配的行，并立即启用下一轮循环
-  **p：打印**当前模式空间内容，追加到默认输出之后
-  **a**：在指定行**后面追加**文本，支持使用\n实现多行追加
-  **i**：在行**前面插入**文本，支持使用\n实现多行追加
-  **c**：**替换**行为单行或多行文本，支持使用\n实现多行追加
-  w：保存模式匹配的行至指定文件
-  r：读取指定文件的文本至模式空间中匹配到的行后
-  =：为模式空间中的行打印行号
-  **!**：模式空间中匹配行**取反**处理
- s///：查找替换，支持使用其它分隔符，如：s@@@，s###；
  -  **加g表示行内全局替换；**
  -  在替换时，可以加一下命令，实现大小写转换
  -  \l：把下个字符转换成小写。
  -  \L：把replacement字母转换成小写，直到\U或\E出现。
  -  \u：把下个字符转换成大写。
  -  \U：把replacement字母转换成大写，直到\L或\E出现。
  -  \E：停止以\L或\U开始的大小写转换

#### **3.3 sed用法演示**

#### **3.3.1 常用选项options演示**

```bash
cat demo
aaa
bbbb
AABBCCDD
sed "/aaa/p" demo  #匹配到的行会打印一遍，不匹配的行也会打印
aaa
aaa
bbbb
AABBCCDD
sed -n "/aaa/p" demo  #-n不显示没匹配的行
aaa
sed -e "s/a/A/" -e "s/b/B/" demo  #-e多点编辑
Aaa
Bbbb
AABBCCDD
cat sedscript.txt
s/A/a/g
sed -f sedscript.txt demo  #-f使用文件处理
aaa
bbbb
aaBBCCDD
sed -i.bak "s/a/A/g" demo  #-i.bak直接对文件进行处理并保存一个备份
cat demo
AAA
bbbb
AABBCCDD
cat demo.bak
aaa
bbbb
AABBCCDD
```

　　

#### **3.3.2 地址界定演示**

```bash
cat demo.txt
AAA
bbbb
AABBCCDD
u3567@GenekServer-3:~/test$ sed "/aaa/p" demo.txt
AAA
bbbb
AABBCCDD
u3567@GenekServer-3:~/test$ sed -n 'p' demo.txt
AAA
bbbb
AABBCCDD
u3567@GenekServer-3:~/test$ sed '2s/b/B/g' demo.txt
AAA
BBBB
AABBCCDD
u3567@GenekServer-3:~/test$ sed -n '/AAA/p' demo.txt
AAA
u3567@GenekServer-3:~/test$ sed -n '1,2p' demo.txt
AAA
bbbb
u3567@GenekServer-3:~/test$ sed -n '/AAA/,/DD/p' demo.txt
AAA
bbbb
AABBCCDD
u3567@GenekServer-3:~/test$ sed -n '2,/DD/p' demo.txt
bbbb
AABBCCDD
u3567@GenekServer-3:~/test$ sed "1~2s/[aA]/E/g" demo.txt
EEE
bbbb
EEBBCCDD
```

　　

#### **3.3.3 编辑命令command演示**

```bash
sed '2d' demo.txt
AAA
AABBCCDD
u3567@GenekServer-3:~/test$ sed -n '2p' demo.txt
bbbb
u3567@GenekServer-3:~/test$ sed '2a123\n456' demo.txt
AAA
bbbb
123
456
AABBCCDD
u3567@GenekServer-3:~/test$ sed '1i123' demo.txt
123
AAA
bbbb
AABBCCDD
u3567@GenekServer-3:~/test$ sed '3c123\n456' demo.txt
AAA
bbbb
123
456
u3567@GenekServer-3:~/test$ pwd
/home/u3567/test
u3567@GenekServer-3:~/test$ sed '3w/home/u3567/test/demo2' demo.txt
AAA
bbbb
AABBCCDD
u3567@GenekServer-3:~/test$ cat demo2
AABBCCDD
u3567@GenekServer-3:~/test$ sed '1r/home/u3567/test/demo2' demo.txt
AAA
AABBCCDD
bbbb
AABBCCDD
u3567@GenekServer-3:~/test$ sed -n '=' demo.txt
1
2
3
u3567@GenekServer-3:~/test$ sed -n '2!p' demo.txt
AAA
AABBCCDD
```

### 4. awk

#### **4.1 认识awk**

　　awk是一种编程语言，用于在linux/unix下对文本和数据进行处理。数据可以来自标准输入(stdin)、一个或多个文件，或其它命令的输出。它**支持用户自定义函数**和动态正则表达式等先进功能，是linux/unix下的一个强大编程工具。它在命令行中使用，但更多是作为脚本来使用。**awk有很多内建的功能**，比如数组、函数等，这是它和C语言的相同之处，灵活性是awk最大的优势。

#### **4.2 使用awk**

##### **4.2.1 语法**

```bash
awk` `[options] ``'program'` `var=value ``file``…
awk` `[options] -f programfile var=value ``file``…
awk` `[options] ``'BEGIN{ action;… } pattern{ action;… } END{ action;… }'` `file` `...
```

##### **4.2.2 常用命令选项**

-  -F fs：fs指定输入分隔符，fs可以是字符串或正则表达式，如-F:
-  -v var=value：赋值一个用户定义变量，将外部变量传递给awk
-  -f scripfile：从脚本文件中读取awk命令

#### **4.3 awk变量**

变量：内置和自定义变量，每个变量前加 -v 命令选项

##### **4.3.1 内置变量**

（1）格式

-  **FS** ：**输入字段分隔符**，**默认为空白字符**
-  **OFS** ：**输出字段分隔符**，默认为空白字符
-  RS ：**输入记录分隔符**，指定输入时的换行符，原换行符仍有效
-  ORS ：**输出记录分隔符**，输出时用指定符号代替换行符
-  **NF** ：字段数量，**共有**多少字段， **$NF引用最后一列，$(NF-1)引用倒数第2列**
-  **NR** ：**行号**，后可跟多个文件，第二个文件行号继续从第一个文件最后行号开始
-  FNR ：各文件分别计数, 行号，后跟一个文件和NR一样，跟多个文件，第二个文件**行号从1开始**
-  FILENAME ：**当前文件名**
-  ARGC ：**命令行参数**的个数
-  ARGV ：数组，保存的是命令行所给定的各参数，**查看参数**

 

（2）演示

```bash
u3567@GenekServer-3:~/test$ cat awk.demo
hello:world
linux:redhat:lalala:hahaha
along:love:youou
u3567@GenekServer-3:~/test$ awk -v FS=':' '{print $1,$2}' awk.demo
hello world
linux redhat
along love
u3567@GenekServer-3:~/test$ awk -v FS=':' -v OFS='---' '{print $1,$2}' awk.demo
hello---world
linux---redhat
along---love
u3567@GenekServer-3:~/test$ awk -v RS=':' '{print $1,$2}' awk.demo
hello
world linux
redhat
lalala
hahaha along
love
youou
u3567@GenekServer-3:~/test$ awk -v FS=':' -v ORS='---' '{print $1,$2}' awk.demo
hello world---linux redhat---along love---u3567
u3567@GenekServer-3:~/test$ awk -F : '{print NF}' awk.demo
2
4
3
u3567@GenekServer-3:~/test$ awk -F : '{print (NF-1)}' awk.demo
1
3
2
u3567@GenekServer-3:~/test$ awk -F : '{print $(NF-1)}' awk.demo
1
3
2
u3567@GenekServer-3:~/test$ awk -F : '{print $1,$2}' awk.demo
hello world
linux redhat
along love
u3567@GenekServer-3:~/test$ awk '{print NR}' awk.demo
1
2
3
u3567@GenekServer-3:~/test$ vi awk.demo1
u3567@GenekServer-3:~/test$ awk '{print NR}' awk.demo awk.demo1
1
2
3
4
5
6
u3567@GenekServer-3:~/test$ awk END'{print NR}' awk.demo awk.demo1
6
u3567@GenekServer-3:~/test$ awk '{print FNR}' awk.demo awk.demo1
1
2
3
1
2
3
u3567@GenekServer-3:~/test$ awk '{print FILENAME}' awk.demo
awk.demo
awk.demo
awk.demo
u3567@GenekServer-3:~/test$ awk '{print FILENAME}' awk.demo1
awk.demo1
awk.demo1
awk.demo1
u3567@GenekServer-3:~/test$ awk 'BEGIN {print ARGC}' awk.demo
2
u3567@GenekServer-3:~/test$ awk 'BEGIN {print ARGC}' awk.demo awk.demo1
3
u3567@GenekServer-3:~/test$ awk 'BEGIN {print ARGV[0]}' awk.demo awk.demo1
awk
u3567@GenekServer-3:~/test$ awk 'BEGIN {print ARGV[1]}' awk.demo awk.demo1
awk.demo
u3567@GenekServer-3:~/test$ awk 'BEGIN {print ARGV[2]}' awk.demo awk.demo1
awk.demo1
```

##### **4.3.2 自定义变量**

自定义变量( 区分字符大小写)

（1）-v var=value

① 先定义变量，后执行动作print

```bash
u3567@GenekServer-3:~/test$ awk -v name='along' -F: '{print name":"$0}' awk.demo
along:hello:world
along:linux:redhat:lalala:hahaha
along:along:love:youou
```

② 在执行动作print后定义变量

```bash
u3567@GenekServer-3:~/test$ awk -F: '{print name":"$0;name="along"}' awk.demo
:hello:world
along:linux:redhat:lalala:hahaha
along:along:love:youou
u3567@GenekServer-3:~/test$ awk -F: '{print name":"$1;name="along"}' awk.demo
:hello
along:linux
along:along
u3567@GenekServer-3:~/test$ awk -F: '{print name":"$3;name="along"}' awk.demo
:
along:lalala
along:youou
```

（2）在program 中直接定义

可以把执行的动作放在脚本中，直接调用脚本 -f

```bash
u3567@GenekServer-3:~/test$ cat awk.txt
{name="along";print name":"$1}
u3567@GenekServer-3:~/test$ awk -F: -f awk.txt awk.demo
along:hello
along:linux
along:along
```

#### **4.4 printf命令**

比print更强大

##### **4.4.1 格式**

（1）格式化输出

```
printf` `"FORMAT"``, item1,item2, ...
```

①  必须指定FORMAT

②  **不会自动换行，需要显示给出换行控制符，\n**

③  FORMAT 中需要分别为后面每个item 指定格式符

 

（2）格式符：与item 一一对应

-  %c:  显示字符的ASCII码
-  %d, %i:  显示十进制整数
-  %e, %E: 显示科学计数法数值
-  **%f ：显示为浮点数，小数**  %5.1f，带整数、小数点、整数共5位，小数1位，不够用空格补上
-  %g, %G ：以科学计数法或浮点形式显示数值
-  **%s ：显示字符串**；例：%5s最少5个字符，不够用空格补上，超过5个还继续显示
-  **%u ：无符号整数**
-  %%:  显示% 自身

 

（3）修饰符：放在%c[/d/e/f...]之间

-  \#[.#]：第一个数字控制显示的宽度；第二个# 表示小数点后精度，%5.1f
-  -：左对齐（**默认右对齐**） %-15s
-  +：显示数值的正负符号 %+d

 

##### **4.4.2 演示**

```bash
[root@guocheng test]# awk -F: '{print $1,$3}' /etc/passwd
root 0
bin 1
daemon 2
adm 3
lp 4
sync 5
shutdown 6
halt 7
mail 8
operator 11
games 12
ftp 14
nobody 99
systemd-network 192
dbus 81
polkitd 999
sshd 74
postfix 89
chrony 998
nscd 28
tcpdump 72
caddy 997
nginx 996
apache 48
saslauth 995
mysql 994
[root@guocheng test]# awk -F: '{print $1,$2,$3}' /etc/passwd
## 第一列显示小于20的字符串；第2列显示整数并换行
root x 0
bin x 1
daemon x 2
adm x 3
lp x 4
sync x 5
shutdown x 6
halt x 7
mail x 8
operator x 11
games x 12
ftp x 14
nobody x 99
systemd-network x 192
dbus x 81
polkitd x 999
sshd x 74
postfix x 89
chrony x 998
nscd x 28
tcpdump x 72
caddy x 997
nginx x 996
apache x 48
saslauth x 995
mysql x 994
[root@guocheng test]# awk -F: '{printf "%20s---%u\n",$1,$3}' /etc/passwd
## 使用-进行左对齐；第2列显示浮点数
                root---0
                 bin---1
              daemon---2
                 adm---3
                  lp---4
                sync---5
            shutdown---6
                halt---7
                mail---8
            operator---11
               games---12
                 ftp---14
              nobody---99
     systemd-network---192
                dbus---81
             polkitd---999
                sshd---74
             postfix---89
              chrony---998
                nscd---28
             tcpdump---72
               caddy---997
               nginx---996
              apache---48
            saslauth---995
               mysql---994
[root@guocheng test]# awk -F: '{printf "%-20s---%10.3f\n",$1,$3}' /etc/passwd
root                ---     0.000
bin                 ---     1.000
daemon              ---     2.000
adm                 ---     3.000
lp                  ---     4.000
sync                ---     5.000
shutdown            ---     6.000
halt                ---     7.000
mail                ---     8.000
operator            ---    11.000
games               ---    12.000
ftp                 ---    14.000
nobody              ---    99.000
systemd-network     ---   192.000
dbus                ---    81.000
polkitd             ---   999.000
sshd                ---    74.000
postfix             ---    89.000
chrony              ---   998.000
nscd                ---    28.000
tcpdump             ---    72.000
caddy               ---   997.000
nginx               ---   996.000
apache              ---    48.000
saslauth            ---   995.000
mysql               ---   994.000
[root@guocheng test]# awk -F: 'BEGIN{printf "username           userid\n---------------\n"}{printf "%-20s|%-10.3f\n",$1,$3}' /etc/passwd
## 使用printf制作表格
username           userid
---------------
root                |0.000
bin                 |1.000
daemon              |2.000
adm                 |3.000
lp                  |4.000
sync                |5.000
shutdown            |6.000
halt                |7.000
mail                |8.000
operator            |11.000
games               |12.000
ftp                 |14.000
nobody              |99.000
systemd-network     |192.000
dbus                |81.000
polkitd             |999.000
sshd                |74.000
postfix             |89.000
chrony              |998.000
nscd                |28.000
tcpdump             |72.000
caddy               |997.000
nginx               |996.000
apache              |48.000
saslauth            |995.000
mysql               |994.000
```

　　

#### **4.5 操作符**

##### **4.5.1 格式**

- 算术操作符：
  -  x+y, x-y, x*y, x/y, x^y, x%y
  -  -x:  转换为负数
  -  +x:  转换为数值
-  字符串操作符：没有符号的操作符，字符串连接
- 赋值操作符：
  -  =, +=, -=, *=, /=, %=, ^=
  -  ++, --
- 比较操作符：
  -  ==, !=, >, >=, <, <=
-  模式匹配符：~ ：左边是否和右边匹配包含 !~ ：是否不匹配
-  逻辑操作符：与&& ，或|| ，非!
-  函数调用： function_name(argu1, argu2, ...)
- 条件表达式（三目表达式）：selector?if-true-expression:if-false-expression
  -  注释：先判断selector，如果符合执行 ? 后的操作；否则执行 : 后的操作

##### **4.5.2 演示**

（1）模式匹配符

```bash
## ---查询以/dev开头的磁盘信息
[root@guocheng test]# df -h | awk -F: '$0 ~ /^\/dev/'
/dev/vda1        40G   21G   18G  55% /
## 只显示磁盘使用状况和磁盘名
[root@guocheng test]# df -h | awk '$0 ~ /^\/dev/{print $(NF-1)"---"$1}'
55%---/dev/vda1
[root@guocheng test]# df -h | awk '$0 ~ /^\/dev/'
/dev/vda1        40G   21G   18G  55% /
[root@guocheng test]# df -h | awk '$0 ~ /^\/dev/{print $(NF-1)"---"$0}'
55%---/dev/vda1        40G   21G   18G  55% /
[root@guocheng test]# df -h | awk '$0 ~ /^\/dev/{print $(NF-1)"---"$1}'
55%---/dev/vda1
[root@guocheng test]# df -h | awk '$0 ~ /^\/tmpfs/{print $(NF-1)"---"$1}'
[root@guocheng test]# df -h | awk '$0 ~ /^tmpfs/{print $(NF-1)"---"$1}'
0%---tmpfs
1%---tmpfs
0%---tmpfs
0%---tmpfs
[root@guocheng test]# df -h | awk '$0 ~ /^tmpfs/{print $(NF-1)"---"$0}'
0%---tmpfs           919M     0  919M   0% /dev/shm
1%---tmpfs           919M  460K  919M   1% /run
0%---tmpfs           919M     0  919M   0% /sys/fs/cgroup
0%---tmpfs           184M     0  184M   0% /run/user/0
[root@guocheng test]# df -h | awk '$0 ~ /^tmpfs/{print $(NF-1)"---"$0}'| awk -F% '$1 >0'
1%---tmpfs           919M  460K  919M   1% /run
## 查找磁盘小于1%的
[root@guocheng test]# df -h | awk '$0 ~ /^tmpfs/{print $(NF-1)"---"$0}'| awk -F% '$1 <1'
0%---tmpfs           919M     0  919M   0% /dev/shm
0%---tmpfs           919M     0  919M   0% /sys/fs/cgroup
0%---tmpfs           184M     0  184M   0% /run/user/0
```

　　

（2）逻辑操作符

```bash
awk -F: '$3>=0 && $3<=1000{print $1,$3}' /etc/passwd
root 0
bin 1
daemon 2
adm 3
lp 4
sync 5
shutdown 6
halt 7
mail 8
operator 11
games 12
ftp 14
nobody 99
systemd-network 192
dbus 81
polkitd 999
sshd 74
postfix 89
chrony 998
nscd 28
tcpdump 72
caddy 997
nginx 996
apache 48
saslauth 995
mysql 994
[root@guocheng test]# awk -F: '$3>=0 || $3<=1000{print $1}' /etc/passwd
root
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
games
ftp
nobody
systemd-network
dbus
polkitd
sshd
postfix
chrony
nscd
tcpdump
caddy
nginx
apache
saslauth
mysql
[root@guocheng test]# awk -F: '!($3==0){print $1}' /etc/passwd
bin
daemon
adm
lp
sync
shutdown
halt
mail
operator
games
ftp
nobody
systemd-network
dbus
polkitd
sshd
postfix
chrony
nscd
tcpdump
caddy
nginx
apache
saslauth
mysql
[root@guocheng test]# awk -F: '!($0 ~ /bash$/){print $1,$3}' /etc/passwd
bin 1
daemon 2
adm 3
lp 4
sync 5
shutdown 6
halt 7
mail 8
operator 11
games 12
ftp 14
nobody 99
systemd-network 192
dbus 81
polkitd 999
sshd 74
postfix 89
chrony 998
nscd 28
tcpdump 72
caddy 997
nginx 996
apache 48
saslauth 995
[root@guocheng test]# awk -F: '($0 ~ /bash$/){print $1,$3}' /etc/passwd
root 0
mysql 994
```

　　

（3）条件表达式（三目表达式）

```bash
[root@guocheng ~]# awk -F: '{$3 >= 500?usertype="common user":usertype="sysadmin user";print usertype,$1,$3}' /etc/passwd
sysadmin user root 0
sysadmin user bin 1
sysadmin user daemon 2
sysadmin user adm 3
sysadmin user lp 4
sysadmin user sync 5
sysadmin user shutdown 6
sysadmin user halt 7
sysadmin user mail 8
sysadmin user operator 11
sysadmin user games 12
sysadmin user ftp 14
sysadmin user nobody 99
sysadmin user systemd-network 192
sysadmin user dbus 81
common user polkitd 999
sysadmin user sshd 74
sysadmin user postfix 89
common user chrony 998
sysadmin user nscd 28
sysadmin user tcpdump 72
common user caddy 997
common user nginx 996
sysadmin user apache 48
common user saslauth 995
common user mysql 994
```

#### **4.6 awk PATTERN 匹配部分**

##### **4.6.1 格式**

PATTERN：根据pattern 条件，过滤匹配的行，再做处理

（1）如果未指定：空模式，匹配每一行

（2）/regular expression/ ：仅处理能够模式匹配到的行，**正则**，需要用/ / 括起来

（3）relational expression：**关系表达式，结果为“真”才会被处理**

真：结果为非0值，非空字符串

假：结果为空字符串或0值

（4）line ranges：行范围

　　startline(起始行),endline(结束行)：/pat1/,/pat2/  不支持直接给出数字，可以有多段，中间可以有间隔

（5）BEGIN/END 模式

　　BEGIN{}:  仅在开始处理文件中的文本之前执行一次

　　END{} ：仅在文本处理完成之后执行

```bash
(base) [root@guocheng ~]# awk -F: '/along/{print $1}' awk.demo
along
(base) [root@guocheng ~]# awk -F: '1{print $1}' awk.demo
hello
linux
along
(base) [root@guocheng ~]# awk -F: '0{print $1}' awk.demo
(base) [root@guocheng ~]# awk -F: '2{print $1}' awk.demo
hello
linux
along
(base) [root@guocheng ~]# awk -F: '/^h/,/^a/{print $1}' awk.demo
hello
linux
along
(base) [root@guocheng ~]# awk -F: 'BEGIN{print "aaa"}{print $1} END{print "bbb"}' awk.demo
aaa
hello
linux
along
bbb
```

### 5、awk高阶用法

##### **5.1 awk控制语句—if-else判断**

（1）语法

```bash
if(condition){statement;…}[else statement]  双分支
if(condition1){statement1}else if(condition2){statement2}else{statement3}  多分支
```

（2）使用场景：对awk 取得的整行或某个字段做条件判断

（3）演示

```bash
awk -F: '{if($3>10 && $3<500)print $1,$3}' /etc/passwd
operator 11
games 12
ftp 14
nobody 99
systemd-network 192
dbus 81
sshd 74
postfix 89
nscd 28
tcpdump 72
apache 48
[root@guocheng ~]# awk -F: '{if($NF == "/bin/bash")print $1,$NF}' /etc/passwd
root /bin/bash
mysql /bin/bash
[root@guocheng ~]# awk -F: '{if(NF>2) print $0}' awk.demo
linux:redhat:lalala:hahaha
along:love:youou
# 第3列>=500为Common user，反之是root or Sysuser
[root@guocheng ~]# awk -F: '{if($3>=500) {printf "Common user:%s\n",$1} else {printf "root or Sysuser:%s\n",$1}}' /etc/passwd
root or Sysuser:root
root or Sysuser:bin
root or Sysuser:daemon
root or Sysuser:adm
root or Sysuser:lp
root or Sysuser:sync
root or Sysuser:shutdown
root or Sysuser:halt
root or Sysuser:mail
root or Sysuser:operator
root or Sysuser:games
root or Sysuser:ftp
root or Sysuser:nobody
root or Sysuser:systemd-network
root or Sysuser:dbus
Common user:polkitd
root or Sysuser:sshd
root or Sysuser:postfix
Common user:chrony
root or Sysuser:nscd
root or Sysuser:tcpdump
Common user:caddy
Common user:nginx
root or Sysuser:apache
Common user:saslauth
Common user:mysql
[root@guocheng ~]# df -h | awk -F% '/^\/dev/{print $1}'
/dev/vda1        40G   21G   18G  55
[root@guocheng ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        909M     0  909M   0% /dev
tmpfs           919M     0  919M   0% /dev/shm
tmpfs           919M  428K  919M   1% /run
tmpfs           919M     0  919M   0% /sys/fs/cgroup
/dev/vda1        40G   21G   18G  55% /
tmpfs           184M     0  184M   0% /run/user/0
[root@guocheng ~]# df -h | awk -F% '/^\/dev/{print $1}'| awk '$NF > 40 {print $1,$NF}'
/dev/vda1 55
[root@guocheng ~]# df -h | awk -F% '/^\/dev/{print $0}'| awk '$NF > 40 {print $1,$NF}'
[root@guocheng ~]# df -h | awk -F% '/^\/dev/{print $0}'
/dev/vda1        40G   21G   18G  55% /
# 磁盘利用率超过40的设备名和利用率
[root@guocheng ~]# df -h | awk -F% '/^\/dev/{print $1}'| awk '$NF > 40 {print $1,$NF}'
/dev/vda1 55
[root@guocheng ~]# awk 'BEGIN{test=100;if(test>90){print "very good"}else if(test>60){print "good"}else{"no pass"}}'
very good
[root@guocheng ~]# awk 'BEGIN{test=50;if(test>90){print "very good"}else if(test>60){print "good"}else{"no pass"}}' [root@guocheng ~]# awk 'BEGIN{test=50;if(test>90){print "very good"}else if(test>60){print "good"}else{print "no pass"}}'
no pass
```

##### **5.2 awk控制语句—while循环**

（1）语法

```
while(condition){statement;…}
```

注：条件“真”，进入循环；条件“假”， 退出循环

 

（2）使用场景

　　对一行内的多个字段逐一类似处理时使用

　　对数组中的各元素逐一处理时使用

 

（3）演示

```bash
[root@guocheng ~]# awk -F: '/^along/{i=1;while(i<=NF){print $i,length($i);i++}}' awk.demo
along 5
love 4
youou 5
[root@guocheng ~]# awk -F: '/^along/{i=1;while(i<=NF){if(length($i)>=6){print $i,length($i)};i++}}' awk.demo
[root@guocheng ~]# awk -F: '{i=1;while(i<=NF){if(length($i)>=6){print $i,length($i)};i++}}' awk.demo
redhat 6
lalala 6
hahaha 6
[root@guocheng ~]# awk 'BEGIN{i=1;sum=0;while(i<=100){sum+=i;i++};print sum}'
5050
```

　　

##### **5.3 awk控制语句—do-while循环**

（1）语法

```
do` `{statement;…}``while``(condition)
```

意义：无论真假，至少执行一次循环体

 

（2）计算1+2+3+...+100=5050

```bash
[root@guocheng ~]# awk 'BEGIN{sum=0;i=1;do{sum+=i;i++}while(i<=100);print sum}'
5050
```

　　

##### **5.4 awk控制语句—for循环**

（1）语法

```
for``(expr1;expr2;expr3) {statement;…}
```

　　

（2）特殊用法：遍历数组中的元素

```
for``(var ``in` `array) {``for``-body}
```

　　

（3）演示

```bash
[root@guocheng ~]# awk -F: '{for(i=1;i<=NF;i++){print $i,length($i)}}' awk.demo
hello 5
world 5
linux 5
redhat 6
lalala 6
hahaha 6
along 5
love 4
youou 5
[root@guocheng ~]# cat sort.txt
xiaoming m 90
xiaohong f 93
xiaohei m 80
xiaofang f 99
[root@guocheng ~]# awk '{m[$2]++;score[$2]+=$3}END{for(i in m){printf "%s:%6.2f\n",i,score[i]/m[i]}}' sort.txt
m: 85.00
f: 96.00
```

### 6. Reference

https://www.cnblogs.com/along21/p/10366886.html