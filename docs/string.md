# 字符串操作

## 返回子字符串

```bash
${varname:offset:length}
```

上面语法的含义是返回`$varname`的子字符串，从位置`offset`开始（以`0`作为基点），长度为`length`。

```bash
$ count=frogfootman
$ echo ${count:4:4}
foot
```

上面例子返回字符串`frogfootman`从4号位置开始的长度为4的子字符串。

如果省略`length`，则从位置`offset`开始，一直返回到字符串的结尾。

```bash
$ count=frogfootman
$ echo ${count:4}
footman
```

上面例子是返回变量`count`从4号位置一直到结尾的子字符串。

## 字符串的替换

```bash
# 如果 pattern 匹配字符串开头，删除非贪婪匹配的部分，返回其他
${variable#pattern}

# 如果 pattern 匹配字符串开头，删除贪婪匹配的部分，返回其他
${variable##pattern}

# 如果 pattern 匹配字符串结尾，删除非贪婪匹配部分，返回其他
${variable%pattern}

# 如果 pattern 匹配字符串结尾，删除贪婪匹配部分，返回其他
${variable%%pattern}

# 贪婪匹配 pattern 的部分被 string 替换，仅仅替换第一个匹配
${variable/pattern/string}

# 贪婪匹配 pattern 的部分被 string 替换，所有匹配都替换
${variable//pattern/string}

# 返回字符串的长度
${#varname}
```

```bash
# removes the shortest possible match from the left:
${VAR#pattern}

$ file=/home/tux/book/book.tar.bz2
$ echo ${file#*/}
home/tux/book/book.tar.bz2
```

${VAR##pattern}
removes the longest possible match from the left:

```bash
$ file=/home/tux/book/book.tar.bz2
$ echo ${file##*/}
book.tar.bz2
```

${VAR%pattern}
removes the shortest possible match from the right:

```bash
$ file=/home/tux/book/book.tar.bz2
$ echo ${file%.*}
/home/tux/book/book.tar
```

${VAR%%pattern}
removes the longest possible match from the right:

```bash
$ file=/home/tux/book/book.tar.bz2
$ echo ${file%%.*}
/home/tux/book/book
```

${VAR/pattern_1/pattern_2}
substitutes the content of VAR from the PATTERN_1 with PATTERN_2:

```bash
$ file=/home/tux/book/book.tar.bz2
$ echo ${file/tux/wilber}
/home/wilber/book/book.tar.bz2
```

## 返回变量名的展开

`${!prefix*}`和`${!prefix@}`，会返回以 prefix 开头的已有变量名，它们的执行结果相同。

```bash
$ echo ${!BASH*}
BASH BASH_ARGC BASH_ARGV BASH_COMMAND BASH_COMPLETION
BASH_COMPLETION_DIR BASH_LINENO BASH_SOURCE BASH_SUBSHELL
BASH_VERSINFO BASH_VERSION
```

## 字符串的展开

字符串长度

```bash
${#parameter}
```

`${#parameter}`展开成由 parameter 所包含的字符串的长度。通常，parameter 是一个字符串；然而，如果 parameter 是`@`或者是`*` 的话， 则展开结果是位置参数的个数。

```bash
$ foo="This string is long."
$ echo "'$foo' is ${#foo} characters long."
'This string is long.' is 20 characters long.
```

截取子字符串。

```bash
${parameter:offset}

${parameter:offset:length}
```

这些展开用来从 parameter 所包含的字符串中提取一部分字符。提取的字符始于 第 offset 个字符（从字符串开头算起）直到字符串的末尾，除非指定提取的长度。

```bash
$ foo="This string is long."
$ echo ${foo:5}
string is long.
$ echo ${foo:5:6}
string
```

若 offset 的值为负数，则认为 offset 值是从字符串的末尾开始算起，而不是从开头。注意负数前面必须有一个空格， 为防止与 ${parameter:-word} 展开形式混淆。length，若出现，则必须不能小于零。

```bash
$ foo="This string is long."
$ echo ${foo: -5}
long.
$ echo ${foo: -5:2}
lo
```

如果 parameter 是 @，展开结果是 length 个位置参数，从第 offset 个位置参数开始。

清除匹配的模式

```bash
${parameter#pattern}

${parameter##pattern}
```

这些展开会从 paramter 所包含的字符串中清除开头一部分文本，这些字符要匹配定义的 patten。pattern 是 通配符模式，就如那些用在路径名展开中的模式。这两种形式的差异之处是该 # 形式清除最短的匹配结果， 而该 ## 模式清除最长的匹配结果。

```bash
$ foo=file.txt.zip
$ echo ${foo#*.}
txt.zip
$ echo ${foo##*.}
zip
```

清除匹配的模式（从尾部开始）

```bash
${parameter%pattern}

${parameter%%pattern}
```

这些展开和上面的 # 和 ## 展开一样，除了它们清除的文本从 parameter 所包含字符串的末尾开始，而不是开头。

```bash
$ foo=file.txt.zip
$ echo ${foo%.*}
file.txt
$ echo ${foo%%.*}
file
```

查找和替换

```bash
${parameter/pattern/string}

${parameter//pattern/string}

${parameter/#pattern/string}

${parameter/%pattern/string}
```

这种形式的展开对 parameter 的内容执行查找和替换操作。如果找到了匹配通配符 pattern 的文本， 则用 string 的内容替换它。在正常形式下，只有第一个匹配项会被替换掉。在该 // 形式下，所有的匹配项都会被替换掉。 该 /# 要求匹配项出现在字符串的开头，而 /% 要求匹配项出现在字符串的末尾。/string 可能会省略掉，这样会 导致删除匹配的文本。

```bash
$ foo=JPG.JPG
$ echo ${foo/JPG/jpg}
jpg.JPG
$ echo ${foo//JPG/jpg}
jpg.jpg
$ echo ${foo/#JPG/jpg}
jpg.JPG
$ echo ${foo/%JPG/jpg}
JPG.jpg
```

## declare

`declare`命令可以用来把字符串规范成大写或小写字符。使用 declare 命令，我们能强制一个 变量总是包含所需的格式，无论如何赋值给它。

```bash
#!/bin/bash
# ul-declare: demonstrate case conversion via declare
declare -u upper
declare -l lower
if [[ $1 ]]; then
    upper="$1"
    lower="$1"
    echo $upper
    echo $lower
fi
```

在上面的脚本中，我们使用 declare 命令来创建两个变量，upper 和 lower。我们把第一个命令行参数的值（位置参数1）赋给 每一个变量，然后把变量值在屏幕上显示出来。

```bash
$ ul-declare aBc
ABC
abc
```

有四个参数展开，可以执行大小写转换操作。

- `${parameter,,}`	把 parameter 的值全部展开成小写字母。
- `${parameter,}`	仅仅把 parameter 的第一个字符展开成小写字母。
- `${parameter^^}`	把 parameter 的值全部转换成大写字母。
- `${parameter^}`	仅仅把 parameter 的第一个字符转换成大写字母（首字母大写）。

```bash
#!/bin/bash
# ul-param - demonstrate case conversion via parameter expansion
if [[ $1 ]]; then
    echo ${1,,}
    echo ${1,}
    echo ${1^^}
    echo ${1^}
fi
```

下面是脚本运行结果。

```bash
$ ul-param aBc
abc
aBc
ABC
ABc
```

## 算数求值

```bash
$((expression))
```

这里的 expression 是一个有效的算术表达式。

`(( ))`命令把结果映射成 shell 正常的退出码

```bash
$ if ((1)); then echo "true"; else echo "false"; fi
true
$ if ((0)); then echo "true"; else echo "false"; fi
false
```

## 数值的进制

- number	默认情况下，没有任何表示法的数字被看做是十进制数（以10为底）。
- 0number	在算术表达式中，以零开头的数字被认为是八进制数。
- 0xnumber	十六进制表示法
- base#number	number 以 base 为底

下面是一些例子。

```bash
$ echo $((0xff))
255
$ echo $((2#11111111))
255
```

## 算术运算符

- +	加
- -	减
- *	乘
- /	整除
- **	乘方
- %	取模（余数）

因为 shell 算术只操作整形，所以除法运算的结果总是整数：

```bash
$ echo $(( 5 / 2 ))
2
```

这使得确定除法运算的余数更为重要。

```bash
$ echo $(( 5 % 2 ))
1
```

算术表达式可能执行赋值运算。

```bash
$ foo=
$ echo $foo
$ if (( foo = 5 ));then echo "It is true."; fi
It is true.
$ echo $foo
5
```

复合命令`(( foo = 5 ))`完成两件事情：1）它把5赋值给变量 foo，2）它计算测试条件为真，因为 foo 的值非零。

- `parameter = value`	简单赋值。给 parameter 赋值。
- `parameter += value`	加。等价于 parameter = parameter + value。
- `parameter -= value`	减。等价于 parameter = parameter – value。
- `parameter *= value`	乘。等价于 parameter = parameter * value。
- `parameter /= value`	整除。等价于 parameter = parameter / value。
- `parameter %= value`	取模。等价于 parameter = parameter % value。
- `parameter++`	后缀自增变量。等价于 parameter = parameter + 1 (但，要看下面的讨论)。
- `parameter--`	后缀自减变量。等价于 parameter = parameter - 1。
- `++parameter`	前缀自增变量。等价于 parameter = parameter + 1。
- `--parameter`	前缀自减变量。等价于 parameter = parameter - 1。

自增和自减运算符可能会出现在参数的前面或者后面。然而它们都是把参数值加1或减1，这两个位置有个微小的差异。 若运算符放置在参数的前面，参数值会在参数返回之前增加（或减少）。若放置在后面，则运算会在参数返回之后执行。

```bash
$ foo=1
$ echo $((foo++))
1
$ echo $foo
2

$ foo=1
$ echo $((++foo))
2
$ echo $foo
2
```

脚本中数值的精确计算，可以执行bc命令。

```bash
$ bc <<< "2+2"
4
```

## 位运算符

- ~	按位取反。对一个数字所有位取反。
- <<	位左移. 把一个数字的所有位向左移动。
- >>	位右移. 把一个数字的所有位向右移动。
- &	位与。对两个数字的所有位执行一个 AND 操作。
- |	位或。对两个数字的所有位执行一个 OR 操作。
- ^	位异或。对两个数字的所有位执行一个异或操作。

注意除了按位取反运算符之外，其它所有位运算符都有相对应的赋值运算符（例如，<<=）。

下面是左移运算符的例子。

```bash
$ for ((i=0;i<8;++i)); do echo $((1<<i)); done
1
2
4
8
16
32
64
128
```

## 逻辑运算符

- <=	小于或相等
- >=	大于或相等
- <	小于
- >	大于
- ==	相等
- !=	不相等
- &&	逻辑与
- ||	逻辑或
- expr1?expr2:expr3	条件（三元）运算符。若表达式 expr1 的计算结果为非零值（算术真），则 执行表达式 expr2，否则执行表达式 expr3。

三元运算符执行一个单独的逻辑测试。 它用起来类似于 if/then/else 语句。它操作三个算术表达式（字符串不会起作用），并且若第一个表达式为真（或非零）， 则执行第二个表达式。否则，执行第三个表达式。

```bash
$ a=0
$ ((a<1?++a:--a))
$ echo $a
1
$ ((a<1?++a:--a))
$ echo $a
0
```

如果在表达式内部赋值，可以放在圆括号中，否则会报错。

```bash
$ ((a<1?(a+=1):(a-=1)))
```

## 本地变量

本地变量是只有当前脚本可以使用的变量，`set`命令可以显示所有本地变量。

```bash
$ set
```

`unset`命令用于删除变量。

```bash
x=2
echo $x
unset x
echo $x
```

上面代码只会输出一个2。

- `-v` 确保删除变量定义，同名的函数会保留
- `-f` 删除函数定义

没有`-f`和`-v`时，如果存在该变量，则删除该变量；否则就删除同名函数。

Bash默认将未定义变量处理为空值。那么，为什么要删除一个变量，而不是将它设为空值呢？因为Bash发现引用未定义变量，会报错。`set -u`命令会使得未定义变量会报错，`set +u`会关闭未定义警告。

```bash
$ set -u
$ VAR1=var1
$ echo $VAR1
var1
$ unset VAR1
$ echo $VAR1
bash: VAR1: unbound variable
$ VAR1=
$ echo $VAR1

$ unset VAR1
$ echo $VAR1
bash: VAR1: unbound variable
$ unset -v VAR1
$ set +u
$ echo $VAR1

```

## declare 命令

`declare`命令用来为声明变量，提供更多的限制。

```bash
declare OPTION(s) VARIABLE=value
```

- `-a` 变量是一个数组。
- `-f` 仅使用函数名称。
- `-i` 该变量将被视为整数；给变量赋值时执行算术评估。
- `-p`：显示每个变量的属性和值。使用-p时，将忽略其他选项。
- `-r`：使变量为只读。然后，这些变量不能被后续的赋值语句赋值，也不能被取消设置。
- `-t`：给每个变量跟踪属性。
- `-X`：标记每个变量以通过环境导出到后续命令。

```bash
$ declare -i VARIABLE=12
$ VARIABLE=string
$ echo $VARIABLE
0
$ declare -p VARIABLE
declare -i VARIABLE="0"
```

## readonly 命令

`readonly`命令可以创建常量。

```bash
readonly OPTION VARIABLE(s)
```

```bash
$ readonly TUX=penguinpower
$ TUX=Mickeysoft
bash: TUX: readonly variable
```
