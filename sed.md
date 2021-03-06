<!-- markdownlint-disable MD001 MD033 -->

# sed

## <table><tr><td bgcolor=yellow>原理</td></tr></table>

>sed处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容自动打印到屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有改变，除非使用重定向存储输出．

具体说下这一过程：
文件test.txt内容：

```c
Tom　　 2012-12-11 car 53000
John　　 2013-01-13 bike 41000
vivi 2013-01-18 car 42800
Tom　　 2013-01-20 car 32500
John　　 2013-01-28 bike 63500
```

执行　sed 's/Tom/John/' test.txt　的过程如下：

>1. 把第一行放入pattern space
>2. 执行 s/Tom/John/, 就是把Tom替换成John, 此时pattern space>中的＂Tom　　 2012-12-11 car 53000＂变为＂John　　 2012-12-11 car 53000＂
>3. 将pattern space中的＂John　　 2012-12-11 car 53000＂打印到终端
>4. 继续处理一下行
>5. ...

-n选项，就是不自动打印pattern space中的内容，即不执行第3步

## <table><tr><td bgcolor=yellow>使用实例</td></tr></table>

### </font><table><tr><td bgcolor=orange>替换操作：s命令</td></tr></table>

替换文本中第一个匹配的字符串：

```sh
sed 's/book/books/' file
```

**-n选项**和**p命令**一起使用表示只打印那些发生替换的行：

```sh
sed -n 's/book/books/p' file
```

直接编辑文件**选项-i**，会匹配file文件中每一行的第一个book替换为books：

```sh
sed -i 's/book/books/' file
```

### </font><table><tr><td bgcolor=orange>全部替换标记g</td></tr></table>

使用后缀 /g 标记会替换每一行中的所有匹配：

```sh
sed 's/book/books/g' file
```

当需要从第N处匹配开始替换时，可以使用 /Ng：

```sh
echo sksksksksksk | sed 's/sk/SK/2g'
skSKSKSKSKSK
```

### </font><table><tr><td bgcolor=orange>删除操作：d命令</td></tr></table>

删除空白行

```sh
sed '/^$/d' file
```

删除只含有任意个空格的空白行,注意+号转义

```sh
sed '/^\s\+$/d' file
```

删除第2行

```sh
sed '2d' file
```

删除2到末尾所有行

```sh
sed '2,$d' file
```

删除以test开头的行

```sh
sed '/^test/'d file 或 sed '/^test/d' file
```

### </font><table><tr><td bgcolor=orange>已匹配字符串标记&</td></tr></table>

正则表达式 \w\+ 匹配每一个单词，使用 [&] 替换它，& 对应于之前所匹配到的单词：

```sh
echo this is a test line | sed 's/\w\+/[&]/g'
[this] [is] [a] [test] [line]
```

所有以192.168.0.1开头的行都会被替换成它自已加localhost：

```sh
sed 's/^192.168.0.1/& localhost/' file
192.168.0.1 localhost
```

### </font><table><tr><td bgcolor=orange>子串匹配标记\1</td></tr></table>

匹配给定样式的其中一部分：

```sh
echo this is digit 7 in a number | sed 's/digit \([0-9]\)/\1/'
this is 7 in a number
```

命令中 digit 7，被替换成了 7。样式匹配到的子串是 7，\(..\) 用于匹配子串，对于匹配到的第一个子串就标记为 **\1**，依此类推匹配到的第二个结果就是 **\2**

### </font><table><tr><td bgcolor=orange>组合多个表达式</td></tr></table>

```sh
sed '表达式' | sed '表达式'

等价于：

sed '表达式; 表达式'
```

### </font><table><tr><td bgcolor=orange>引用</td></tr></table>

```sh
test=hello
echo hello WORLD | sed "s/$test/HELLO"
HELLO WORLD
```

### </font><table><tr><td bgcolor=orange>选定行的范围：,（逗号）</td></tr></table>

所有test和check所确定的范围内的行都被打印：

```sh
sed -n '/test/,/check/p' file
```

注意，如果第二个参数没有匹配的行，则会将后面所有行都打印，拿上面例子来说，如果没有行匹配check，则会将test行及后面所有行都打印．

打印从第5行开始到第一个包含以test开始的行之间的所有行：

```sh
sed -n '5,/^test/p' file
```

对于test和west之间的行，每行的末尾用字符串aaa bbb替换：

```sh
sed '/test/,/west/s/$/aaa bbb/' file
```

### </font><table><tr><td bgcolor=orange>多点编辑：e命令</td></tr></table>

**-e**选项允许在同一行里执行多条命令：

```sh
sed -e '1,5d' -e 's/test/check/' file
```

上面sed表达式的第一条命令删除1至5行，第二条命令用check替换test。命令的执行顺序对结果有影响。如果两个命令都是替换命令，那么第一个替换命令将影响第二个替换命令的结果。

## <table><tr><td bgcolor=camle>速查手册</td></tr></table>

### <table><tr><td bgcolor=aqua>选项</td></tr></table>

```sh
-e<script>或--expression=<script>：以选项中的指定的script来处理输入的文本文件；
-f<script文件>或--file=<script文件>：以选项中指定的script文件来处理输入的文本文件；
-h或--help：显示帮助；
-n或--quiet或——silent：不自动打印pattern space的内容；
-V或--version：显示版本信息。
```

### <table><tr><td bgcolor=aqua>sed命令</td></tr></table>

```c
a\ 在当前行下面插入文本。
i\ 在当前行上面插入文本。
c\ 把选定的行改为新的文本。
d 删除，删除选择的行。
D 删除模板块的第一行。
s 替换指定字符
h 拷贝模板块的内容到内存中的缓冲区。
H 追加模板块的内容到内存中的缓冲区。
g 获得内存缓冲区的内容，并替代当前模板块中的文本。
G 获得内存缓冲区的内容，并追加到当前模板块文本的后面。
l 列表不能打印字符的清单。
n 读取下一个输入行，用下一个命令处理新的行而不是用第一个命令。
N 追加下一个输入行到模板块后面并在二者间嵌入一个新行，改变当前行号码。
p 打印模板块的行。
P(大写) 打印模板块的第一行。
q 退出Sed。
b lable 分支到脚本中带有标记的地方，如果分支不存在则分支到脚本的末尾。
r file 从file中读行。
t label if分支，从最后一行开始，条件一旦满足或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。
T label 错误分支，从最后一行开始，一旦发生错误或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。
w file 写并追加模板块到file末尾。
W file 写并追加模板块的第一行到file末尾。
! 表示后面的命令对所有没有被选定的行发生作用。
= 打印当前行号码。
# 把注释扩展到下一个换行符以前。
```

### <table><tr><td bgcolor=aqua>sed替换标记</td></tr></table>

```sh
g 表示行内全面替换。
p 表示打印行。
w 表示把行写入一个文件。
x 表示互换模板块中的文本和缓冲区中的文本。
y 表示把一个字符翻译为另外的字符（但是不用于正则表达式）
\1 子串匹配标记
& 已匹配字符串标记
```

### <table><tr><td bgcolor=aqua>sed元字符集</td></tr></table>

```sh
^ 匹配行开始，如：/^sed/匹配所有以sed开头的行。
$ 匹配行结束，如：/sed$/匹配所有以sed结尾的行。
. 匹配一个非换行符的任意字符，如：/s.d/匹配s后接一个任意字符，最后是d。
* 匹配0个或多个字符，如：/*sed/匹配所有模板是一个或多个空格后紧跟sed的行。
[] 匹配一个指定范围内的字符，如/[ss]ed/匹配sed和Sed。
[^] 匹配一个不在指定范围内的字符，如：/[^A-RT-Z]ed/匹配不包含A-R和T-Z的一个字母开头，紧跟ed的行。
\(..\) 匹配子串，保存匹配的字符，如s/\(love\)able/\1rs，loveable被替换成lovers。
& 保存搜索字符用来替换其他字符，如s/love/**&**/，love这成**love**。
\< 匹配单词的开始，如:/\<love/匹配包含以love开头的单词的行。
\> 匹配单词的结束，如/love\>/匹配包含以love结尾的单词的行。
x\{m\} 重复字符x，m次，如：/0\{5\}/匹配包含5个0的行。
x\{m,\} 重复字符x，至少m次，如：/0\{5,\}/匹配至少有5个0的行。
x\{m,n\} 重复字符x，至少m次，不多于n次，如：/0\{5,10\}/匹配5~10个0的行。
```

参考：
http://man.linuxde.net/sed