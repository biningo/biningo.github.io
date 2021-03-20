---
title: Shell脚本基本语法总结
date: 2021-03-19
categories: [Linux]
tags: [Linux,Shell]
---

## 变量

变量类型分为: 

- 全局 (`declare`或则直接写)
- 局部(函数内部) `local`声明
- 环境变量

只读变量

```bash
readonly name="AAA" #之后再次赋值会报错
```

```bash
#只读变量也可以后来才设置
name="BBB"
readonly name
```

删除变量 (**只读变量不能删除**)

```bash
name="AA"
unset name
if [ -z $name ];then
	echo "null"
else
 	echo "${name}"
fi
```

特殊变量有如下几个类型:

| 量     | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| `$0`   | 当前脚本的文件名                                             |
| `$<n>` | 传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。 |
| `$#`   | 传递给脚本或函数的参数个数。                                 |
| `$*`   | 传递给脚本或函数的所有参数。                                 |
| `$@`   | 传递给脚本或函数的所有参数。被双引号`""`包含时，与 $* 稍有不同 |
| `$?`   | 上个命令的退出状态，或函数的返回值，用于判断上一个函数或则命令是否执行成功 |
| `$$`   | 当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。 |

**`${@}`和`${*}` 的区别**

两个都是用于获取所有入参，区别在于`"${@}"`被双引号包裹会展开里面的所有的值，而`"${*}"`则会将所有值当做一个，如果不加双引号那么他们就没有区别

```bash
#下面只会执行一次循环
for i in "${*}";do
   echo $i    
done

#下面会输出每个参数
for i in "${@}";do
   echo $i    
done
```

变量替换

| 形式              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| `${var}`          | 变量本来的值                                                 |
| `${var:-word}`    | 如果变量 var 为空或已被删除(unset)，那么返回 word，但不改变 var 的值。 |
| `${var:=word}`    | 如果变量 var 为空或已被删除(unset)，那么返回 word，并将 var 的值设置为 word。 |
| `${var:?message}` | 如果变量 var 为空或已被删除(unset)，那么将消息 message 送到标准错误输出，可以用来检测变量 var 是否可以被正常赋值。 若此替换出现在Shell脚本中，那么脚本将停止运行。 |
| `${var:+word}`    | 如果变量 var 被定义，那么返回 word，但不改变 var 的值。      |

另外 **反引号** 或则`()`用于替将命令执行结果保存到变量中

```bash
files=`ls /bin`
files=$(ls /bin)
```

​     

## 模式扩展

`~` 扩展

```bash
pwd ~ #家目录
```

`?` `*` 扩展

```bash
ls ?.txt #只列出 a.txt b.txt 等一个单词的文件
ls ??.txt #列出两个 ab.txt ...
ls * #列出所有文件
ls a*.txt #列出所有a开头的
```

方括号、大括号扩展 (和正则表达式类似)

```bash
ls [ab].txt #列出 a.txt 或则 b.txt
ls *[^abc]* #或*[!abc]* 不包含a、b、c的
ls [0-9].txt
ls [a-z].txt
```

```bash
echo d{a,e,i,u,o}g #dag deg dig dug dog
touch {a,b,c}.txt
rm {a,b,c}.txt
echo a{A{1,2},B{3,4}}b #嵌套
echo {a..c} # a b c
echo {c..a} # c b a
echo Number_{1..5}
echo {j{p,pe}g,png} #嵌套
echo {01..5} #有0 则每个都会输出0
mkdir {2007..2009}-{01..12} #建立目录
#用于for循环
for i in {1..4}
do
  echo $i
done
#cp
cp a.log{,.bak} #逗号前面可以为空，相当于 cp a.log a.log.bak
#每个都会变量 a1 a2... b1 b2...
echo {a..d}{1..10}
```

字符扩展

| 符号           | 含义                                        |
| -------------- | ------------------------------------------- |
| `[[:alnum:]]`  | 匹配任意英文字母与数字                      |
| `[[:alpha:]]`  | 匹配任意英文字母                            |
| `[[:blank:]]`  | 空格和 Tab 键                               |
| `[[:cntrl:]]`  | ASCII 码 0-31 的不可打印字符                |
| `[[:digit:]]`  | 匹配任意数字 0-9                            |
| `[[:graph:]]`  | A-Z、a-z、0-9 和标点符号                    |
| `[[:lower:]]`  | 匹配任意小写字母 a-z                        |
| `[[:print:]]`  | ASCII 码 32-127 的可打印字符                |
| `[[:punct:]]`  | 标点符号（除了 A-Z、a-z、0-9 的可打印字符） |
| `[[:upper:]]`  | 匹配任意大写字母 A-Z                        |
| `[[:xdigit:]]` | 16进制字符（A-F、a-f、0-9）                 |

```bash
ls [[:alnum:]].txt
```

​        

## 算术运算

进行数学运算有如下三种方式

```bash
a=10
b=20
val=`expr $a + $b`
echo "a + b : $val"
```

```bash
a=10
b=20
val=$((a*b))
echo "a + b : $val"
```

```bash
a=10
b=20
let val=${a}*${b}
echo "a + b : $val"
```

​    

## 字符串

`''`和 `""` 的区别

```bash
name="lyer"
s1='hello,${name}\n' #不会转义,原样输出
s2="hello,${name}\n" #转义
```

字符串拼接

```bash
s1="abc"
s2="def"
s3=${s1}"-"${s2}
```

获取字符串的长度

```bash
name="lyer"
echo ${#name} #和array的操作一样
```

获取子串

```bash
${username:1} #[1,]
${username:1:2} #1开始获取2个 包括1
${username:1:-2} #[1,-2)
${username::2} #截取0-2 
${username: 2}
${username: -3}：提取最后3个字符，注意冒号后面添加一个空格：txt
```

​    

## 数组

数组和字符串操作有很多相识的地方

```bash
arr=("a" "b" "c" "d" "e")
echo "size: ${#arr[*]}"  
echo "${arr} | ${arr[*]:2:2}" #a | c d
```

```bash
for i in ${arr[*]};do
   echo $i          
done
echo "2:"${arr[2]} #获取指定元素
```

数组的copy

```bash
arr2=(${arr[*]})
arr2=(${arr[*]} a b c ) #也可以添加新的成员
arr2+=(d e f)
```

​    

## IF判断

逻辑判断

| 运算符     | 说明                                                |
| ---------- | --------------------------------------------------- |
| `-eq` `==` | 检测两个数是否相等，相等返回 true                   |
| `-ne` `!=` | 检测两个数是否相等，不相等返回 true                 |
| `-gt`      | 检测左边的数是否大于右边的，如果是，则返回 true     |
| `-lt`      | 检测左边的数是否小于右边的，如果是，则返回 true     |
| `-ge`      | 检测左边的数是否大等于右边的，如果是，则返回 true   |
| `-le`      | 检测左边的数是否小于等于右边的，如果是，则返回 true |

布尔判断

| 运算符 | 说明                                              |
| ------ | ------------------------------------------------- |
| `!`    | 非运算，表达式为 true 则返回 false，否则返回 true |
| `-o`   | 或运算，有一个表达式为 true 则返回 true           |
| `-a`   | 与运算，两个表达式都为 true 才返回 true           |

字符串

| 运算符       | 说明                                                         | 举例                   |
| ------------ | ------------------------------------------------------------ | ---------------------- |
| `=`          | 检测两个字符串是否相等，相等返回 true                        | [ $a = $b ] 返回 false |
| `!=`         | 检测两个字符串是否相等，不相等返回 true ,注意字符串不能使用`eq ne`等判断是否相等 | [ $a != $b ] 返回 true |
| `-z`         | 检测字符串长度是否为0，为0返回 true                          | [ -z $a ] 返回 false   |
| 直接写字符串 | 检测字符串是否为空，不为空返回 true                          | [ $a ] 返回 true       |

文件相关

| 运算符 | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| `-d`   | 检测文件是否是目录，如果是，则返回 true                      |
| `-f `  | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true |
| `-r `  | 检测文件是否可读，如果是，则返回 true                        |
| `-w `  | 检测文件是否可写，如果是，则返回 true                        |
| `-x`   | 检测文件是否可执行，如果是，则返回 true                      |
| `-s`   | 检测文件是否为空（文件大小是否大于0），不为空返回 true       |
| `-e`   | 检测文件（包括目录）是否存在，如果是，则返回 true            |

```bash
file="/tmp/a.txt"
if [ -e $file ];then
	echo "文件存在"
else
	echo "文件不存在"
fi	
```

c风格判断数字大小

```bash
if ((1<2)); then echo "OK"; fi    
```

多分支

```bash
if [ xxx ];then
	xxx
elif [ xx ]
	xxx
elif [ xx ]
	xxx
else
	xxx
fi
```

和`test`命令配合

```bash
if test $[2*3] -eq $[1+5]; then echo 'The two numbers are equal!'; fi;
```

​    

## 循环

Shell里面有三种循环:

- while
- until 和`while`差不多,只是条件为false则循环,while是条件为真则循环
- for

```bash
for i in {1..5};do
	echo $i
done
```

```bash
count=5
while [ count -gt 0 ];do
	echo $count
	let count+=1
done
```

```bash
count=5
until [ count -le 0 ];do
	echo $count
	let count+=1
done
```

​    

## case

```bash
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
a|b|c)
        echo "输入字母$aNum"
    ;;
    *)
        echo "其他"
    ;;
esac
```

​    

## 函数

函数调用和传参

```bash
hello(){
        username=$1
        age=$2
        echo "hello,world"
        echo "hello,$username,$age"
}
hello icepan 18
```

函数返回值只能返回数值类型代表函数执行成功还是失败，使用`$?`来查看上一个函数或则命令是否执行成功，如果是`0`表示执行成功，其他则表示执行失败

```bash
hello(){
	return 0
}
$? #0
```

因为函数只能返回数值，那么为了能返回其他值则可以借助全局变量，直接赋予全局变量值即可

```bash
name=""
age=0
hello(){
	name="lyer"
	age=18 
	#默认返回0
}
hello #调用函数
echo $name $age
```

也可以设置局部变量`local`而不影响外界的变量

```bash
name="a"
hello(){
	local name
	name="b"
}
hello
```

​    

## 引入脚本和export变量

```bash
source
. 
export
```

shell也可以和c一样import到一个shell进程中，这样就可以调用其他shell脚本的函数了，但是在引入的时候会执行整个脚本，可以用`source` `.` 等方式来引入，两种方式差不多，都会在当前进程中执行脚本并且设置变量和函数

```bash
#------------tmp.sh--------------
name="lyer"
hello(){
	echo "hello,world"
}
#---------------------------------
#引入到当前的shell终端
. tmp.sh
hello #直接调用函数

#-----------------------
#引入到其它shell脚本 
. tmp.sh
echo $name #使用其他脚本的变量
```

另外一个shell脚本中的变量只能在此进程中使用，子进程都无法使用，为了让子进程使用和本脚本的变量，则可以使用`export <变量>` 方式

```bash
name="aa"
export name
bash a.sh #在a.sh中可以使用name并且改变name  同时会影响
```

​    

## echo和printf

```bash
echo "hello,\t world" #转义
echo 'hello,\t world' #不转义
```

```bash
printf "%s,\t%s\n" "hello" "world" #单引号双引号一样
```

​    

## 参考

- https://wangdoc.com/bash/intro.html 【阮一峰Bash脚本教程】