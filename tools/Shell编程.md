# Shell学习笔记

Linux马上就要考试了,我猜这个shell编程是一个考点,所以,看看吧.

## 1 Shell概述

Shell是一个命令行解释器,它接收应用程序/用户命令,然后调用操作系统内核

![20230531155508](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531155508.png)

或者说是shell脚本,可以一行一行解释程序

查看系统提供的shell解释器:`cat /etc/shells`
- ![312213](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/312213.png)
- ![20230531160824](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531160824.png)

>centos7内置的shell解释器就是bash,或者说是sh,系统内的sh链接到了bash,所以后面使用sh或者bash都是一样的效果

## 2 Shell脚本入门

1. 脚本格式
    - 脚本以`#!/bin/bash`开头(指定解析器)
2. 第一个Shell脚本:`hello.sh`
```sh
#!/bin/bash
echo "hello,world!"
```
3. 执行:`bash(sh) hello.sh`-也可以指定目录,相对路径和绝对路径都行
![20230531162006](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531162006.png)
![20230531162046](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531162046.png)

>还有一种执行方式就是直接输文件名:
![20230531162628](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531162628.png)
一执行就提示没有权限了,因为一个文件**默认是只有读写权限的,并没有可执行的权限,所以要先授予该文件以执行权限**
授予权限前:
![20230531162910](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531162910.png)
执行:`chmod +x hello.sh`授予权限,会发现多了一个x,即代表该文件拥有了可执行权限
![20230531162930](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531162930.png)
然后根据绝对路径就可以执行了-相对路径也可,但是**直接输入文件名是不行的**
![20230531163128](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531163128.png)
不过可以根据`./file_name`来执行:
![20230531163428](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531163428.png)

## 3 变量

### 3.1 系统预定义变量

1. 常用系统变量
    - `$HOME`
    - `$PWD`
    - `$SHELL`
    - `$USER`
2. 案例实操
    - 查看系统变量的值
        - ![20230531165404](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531165404.png)
    - 显示当前Shell中所有变量:set(局部变量)

### 3.2 自定义变量

**shell变量不关心类型,只要正确的赋值就能进行使用**

1. 基本语法
    - 定义变量:`变量名=变量值`,**=号前后不能有空格**(这里还是很奇怪的,竟然不能有空格,但是这里毕竟是命令行,如果有空格,那么变量名很可能被识别成命令,还有就是如果要赋值的字符串中有空格,那么可以用双引号括起来)
    - ![20230531170957](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531170957.png)
    - ![20230531171515](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531171515.png)
    - 撤销变量:`unset 变量名`
    - 声明静态(只读)变量:`readonly 变量`,**不能进行unset**
2. 变量定义规则
    - 变量名称可以由字母,数字,下划线组成,但是不能以数字开头,**环境变量名建议大写**
    - 等号两侧不能有空格
    - 在bash中,变量默认类型都是字符串类型,无法直接进行数值运算
    - 变量的值如果有空格,需要使用双引号或者单引号括起来

我们直接定义的变量都是局部变量,但是我们可以把一个局部变量提升为一个全局变量:`export name`
![20230531171959](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531171959.png)
此时就可以在子shell中访问到此变量,但是子shell更改此变量后,那么父shell是接收不到更改的.

引申出,如果在外面定义一个局部变量,那么直接使用`bash hello.sh`是访问不到的,因为`bash`相当于又起了一个子bash,但是使用`source hello.sh`是可以的

>下面这种方式不能直接实现赋值:
![20230531173325](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531173325.png)
如果想要实现a=6,那么可以使用`$(())`或者`$[]`
![fdassdafdasf](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/fdassdafdasf.png)


---

有没有一种方法,可以直接执行脚本,而不需要写其他的内容-配置系统变量的`$PATH`

### 3.3 特殊变量

- `$n`:n为数字,`$0`代表该脚本名称,`$1~$9`代表**第一到第九个参数**,十以上的参数需要用大括号包含:`${10}`(就是用于传参)
```sh
#!/bin/bash
echo '===============$n=============='
echo shell name: $0
echo 1st paramater: $1
echo 1st paramater: $2
```
![20230604105030](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230604105030.png)
- `$#`:获取所有输入参数的**个数**,常用于循环,判断参数的个数是否正确以及加强脚本的健壮性
```sh
#!/bin/bash
echo '===============$n=============='
echo shell name: $0
echo 1st paramater: $1
echo 1st paramater: $2
echo '===============$#=============='
echo paramater numbers: $#
```
![20230604105619](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230604105619.png)
![20230604105634](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230604105634.png)

- `$*和$@`
    - `$*`:这个变量代表命令行中所有的参数,`$*`把所有的参数看成一个整体
    - `$@`:这个变量也代表命令行中的所有的参数,不过`$@`**把每个参数区别对待(可以用于for循环遍历)**
![20230604110259](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230604110259.png)

- `$?`:最后一次执行的命令的返回返回状态,如果这个变量的值为0,证明上一个命令正确执行,如果这个变量的值为非0,则证明上一个命令执行不正确
![20230604110742](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230604110742.png)

*是不是感觉很奇怪,返回的值0为正,1或其他为假*

## 4 运算符

基本语法:
`$((运算式))`或者`$[运算式]`

这个也没啥难的
```sh
#!/bin/bash
result1=$[$1+$2]
result2=$((($1+$2)*$2))
echo $result1
echo $result2
```

![20230604144915](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230604144915.png)

>在Linux中有一个命令专门用于计算数值的表达式:`expr`

![6-4](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/6-4.png)

但是注意,要在运算符左右加上**空格**

但是`*`就不行了,要换成:
![20230604143833](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230604143833.png)

**但是这也太麻烦了,所以才会有**`$[]/$(())`这种形式

## 5 条件判断

基本语法:
1. `test condition`
2. `[ condition ]`(注意condition前面要有空格)
注意:条件非空即为true,`[ ]`返回false

常用判断条件:
1. 两个整数之间比较
    - `-eq`:等于
    - `-ne`:不等于(not equal)
    - `-lt`:小于(less than)
    - `-le`:小于等于(less equal)
    - `-gt`:大于(greater than)
    - `-ge`:大于等于
2. 字符串就是`=`和`!=`了,没什么区别
3. 按照文件类型进行判断
    - `-r`有读的权限
    - `-w`有写的权限
    - `-x`有执行的权限(execute)
4. 按照文件类型进行判断
    - `-e`文件存在(existence)
    - `-f`文件存在并且是一个常规的文件(file)
    - `-d`文件存在并且是一个目录(directory)

![2313123213](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/2313123213.png)
之前授予过执行权限的hello.sh经过判断返回的就是0,而新创建的a.sh由于没有授予执行权限,所以返回是1

>**多条件判断**

`&&`表示前一条命令执行成功时,才执行后一条命令,`||`表示上一条命令执行失败后,才执行下一条命令

![20230604154944](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230604154944.png)

## 6 流程控制(重点)

### 6.1 单分支

下面的分号then,就表示一行里写上了两条命令,拿分号隔开
1. 单分支
```sh
if [ 条件判断式 ];then
    程序
fi
```
或者(我还是比较喜欢这个)
```sh
if [ 条件判断式 ]
then
    程序
fi
```
拿`fi`表示if结束,这是真的有点随意?但是好像还挺有意思

例子:
```sh
#!/bin/bash
if [ $1 = zzmr ]
then
        echo "welcome zzmr"
fi
```
此时就是当输入zzmr时,会提示`welcome zzmr`

但是实际开发中,不会这么写,由于传入的参数可能为空,那么执行这个脚本就会报错,此时可以该写程序为:
```sh
#!/bin/bash
if [ "$1"x = "zzmr"x ]
then
        echo "welcome zzmr"
fi
```

条件也是可以用`&&`或者`||`进行拼接的
```sh
#!/bin/bash
if [ $1 -gt 5 ] && [ $1 -lt 10 ]
then
        echo "welcome zzmr"
fi
```

也可以写在同一个中括号中:
```sh
#!/bin/bash
# if [ $1 -gt 5 ] && [ $1 -lt 10 ]
if [ $1 -gt 5 -a $1 -lt 10 ]
then
        echo "welcome zzmr"
fi
```

### 6.2 多分支

双分支:
```sh
#!/bin/bash
if [ $1 -lt 18 ]
then
        echo "未成年"
else
        echo "成年人"
fi
```

多分支:
```sh
#!/bin/bash
if [ $1 -lt 18 ]
then
        echo "未成年"
elif [ $1 -lt 35 ]
then
        echo "青年人"
elif [ $1 -lt 60 ]
then
        echo "中年人"
else
        echo "老年人"
fi
```

---

>**case语句**

```sh
case $变量名 in
"值1")
    如果变量的值等于值1,则执行程序1
;;
"值2")
    如果变量的值等于值2,则执行程序2
;;
    ...省略其他分支
*)
    如果变量的值都不是以上的值,则执行此程序
;;
esac
```

注意事项
1. case行尾必须为单词`in`,每一个模式匹配必须以右括号`)`结束
2. 双分号`;;`表示命令序列结束,相当于Java中的break
3. 最后的`*)`表示默认模式,相当于java中的default

*感觉用这个太麻烦了,还不如去用elif*

```sh
#!/bin/bash
case $1 in
1)
        echo "one"
;;
2)
        echo "two"
;;
3)
        echo "three"
;;
*)
        echo "number else"
;;
esac
```

### 6.3 for循环

基本语法:
```sh
for (( 初始值;循环控制条件;变量变化 ))
do
    程序
done
```

举例:
```sh
#!/bin/bash
sum=0
for ((i=0;i<=100;i++))
do
        sum=$[$sum+$i]
done
echo $sum
```

执行脚本就会输出5050,主要的问题就在于:`sum=$[$sum+$i]`,因为shell中是没有`+=`这个运算符的

**和其他语言的主要区别就在于在for循环中,执行的语句要加上`do和done`**

>**写了个批量删除之前创建的用户的脚本**
```sh
#!/bin/bash
for (( i=0;i<10;i++ ))
do
        userdel -r "stu"_$i
done
```

没啥问题,其实我挺迷惑这个字符串拼接的,但是的确又是这么个回事.嗯

---

**但是shell中for循环一般是另一种写法**:
```sh
for 变量 in 值1 值2 值3...
do
    程序
done
```

举例:
```sh
for os in linux windows macos; do echo $os; done
# 输出以下内容
# linux
# windows
# macos
```

还是计算1加到100的和:
```sh
sum=0
for i in {1..100}
do
        sum=$[$sum+$i]
done
echo $sum
```
这样也能计算正确.


学了for循环之后,就可以分别出`$*`和`$@`的区别了:
```sh
#!/bin/bash

echo '================$*==============='
for para in $*
do
        echo $para
done

echo '================$@==============='
for para1 in $@
do
        echo $para1
done
```

此时看,会发现两者并没有什么区别:
![20230606142645](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606142645.png)

但是把`$*`和`$@`都加上双引号,就能发现区别了:
![20230606142818](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606142818.png)

这就表示,`$*`把所有的参数当做一个整体,而`$@`则是会拿到数据的每一项.

### 6.4 while循环

这个用起来也是挺简单的.
```sh
#!/bin/bash
i=1
sum=0
while [ $i -le 100 ]
do
        sum=$[$i+$sum]
        i=$[$i+1]
done

echo $sum
```

**初始化变量还是很重要的,不然很容易报错**

>**毕竟shell并不是一门用来复杂编程的语言,只是用来输入一些命令,更好的控制底层的linux内核,做操作系统上的一些操作.**

哈哈哈,老师最后讲了个简写:
```sh
#!/bin/bash
i=1
sum=0
while [ $i -le 100 ]
do
#       sum=$[$i+$sum]
#       i=$[$i+1]
        let sum+=i
        let i++
done

echo $sum
```

就记这个吧,简单得多,**注意`+=`还是不能加入空格**

## 7 read读取控制台输入

基本语法:
`read (选项) (参数)`
1. 选项:
    - `-p`:指定读取值时的提示符
    - `-t`:指定读取值时等待的时间(秒)如果`-t`不加表示一直等待
2. 参数
    - 变量:指定读取值的变量名

实例实操

**提示7秒内,读取控制台输入的名称**

```sh
#!/bin/bash
read -p "请在7秒内完成输入" -t 7 value
echo $value
```

此时,执行脚本,7秒内不输入数据,则程序自动向下执行

## 8 函数

### 8.1 系统函数

>**basename**

基本语法
`basename [string/pathname] [suffix]`:basename命令会删除所有的前缀包括最后一个`/`字符,然后将字符显示出来,basename可以理解为**取路径里的文件名称**

`[suffix]`表示后缀,如果指定该后缀,`basename`会将pathname或string中的suffix去掉

![20230606150820](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606150820.png)

![20230606150941](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606150941.png)

这玩意实际上就是一个字符串拼接,就算路径错误也一样可以调用该命令

---

>**date**

显示系统时间/时间戳

```sh
#!/bin/bash

filename="$1"_log_$(date +%s)
echo $filename
```

`date +%s`就是显示时间戳的意思,这里要用`$()`表示**转义命令**,也叫命令替换

![20230606152253](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606152253.png)

>**dirname**

![20230606153132](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606153132.png)

这玩意也就是相当于截取除了最后一个斜杠之后的内容...

### 8.2 自定义函数

基本语法
```sh
# 这里的中括号可写可不写
[function] function_name[()]  
{
    Action;
    [return int;]
}
```

>**经验技巧**
1. 必须在调用函数地方之前先声明函数,shell脚本是逐行运行,不会像其他语言一样先编译
2. 函数返回值,只能通过`$?`系统变量获得,可以显示加return返回,如果不加,将以最后一条命令运行结果,作为返回值,return后跟数值n(0~255)

举例1
```sh
#!/bin/bash
function add(){
        s=$[$1 + $2]
        echo "和为"$s
}

read -p "请输入第一个整数: " a
read -p "请输入第二个整数: " b
add $a $b
```

![20230606155031](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606155031.png)

获取返回值:

```sh
#!/bin/bash
function add(){
        s=$[$1 + $2]
        echo $s
}

read -p "请输入第一个整数: " a
read -p "请输入第二个整数: " b

sum=$(add $a $b)
echo "和:"$sum
```

由于return受限于255,所以我们可以这么写,
- 先echo这个结果,然后拿一个变量接收,最后对这个变量进行处理即可

## 9 综合案例:归档文件

实际生产应用中,往往需要对重要数据进行**归档备份**
需求:实现一个每天对指定目录归档备份的脚本,输入一个目录名称(末尾不带/),将目录下所有文件按天归档保存,并将归档日期附加在归档文件名上,放在`/root/archive`下
这里用到了归档命令:tar
后面可以加上`-c`选项表示归档,加上`-z`选项表示同时进行压缩,得到的文件后缀名为`tar.gz`

```sh
#!/bin/bash

# 首先判断输入参数个数是否为1
if [ $# -ne 1 ]
then
        echo "参数个数错误,应该输入一个参数,作为归档的目录名"
        exit
fi

# 从输入参数中获取目录名称
if [ -d $1 ]
then
        echo 
else
        echo
        echo "目录不存在"
        echo
        exit
fi

DIR_NAME=$(basename $1)
DIR_PATH=$(cd $(dirname $1); pwd)

# 获取当前的日期
DATE=$(date +%y%m%d)

# 定义生成的归档文件名称
FILE=archive_${DIR_NAME}_$DATE.tar.gz
DEST=/home/yangjin/archive/$FILE

# 开始归档目录文件
echo "开始归档"
echo

tar -czf $DEST $DIR_PATH/$DIR_NAME

if [ $? -eq 0 ]
then
        echo
        echo "归档成功"
        echo "归档的文件为$DEST"
        echo
else
        echo "归档错误"
        echo
fi
```

也可以加入一个定时任务
输入`crontab -l`显示当前的定时任务
输入`crontab -e`编辑定时任务
```sh
0 2 * * * /home/yangjin/shell_study/daily_archive.sh /home/yangjin/archive
```

系统会在凌晨两点执行该shell脚本并传入参数

## 10 正则表达式入门

在Linux中,grep,sed,awk等文本处理工具都支持通过正则表达式进行模式匹配

### 10.1 常规匹配

一串不包含特殊字符的正则表达式匹配它自己,例如:

![20230606173031](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606173031.png)

### 10.2 常用特殊字符

使用特殊字符可以进行模糊匹配

1. `^`:会匹配一行的开头,例如:
![20230606172712](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606172712.png)
2. `$`匹配一行的结束
![20230606173334](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606173334.png)
3. 两个一起用可以匹配空行:
![20230606173829](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606173829.png)
4. `.`通配符,用于代替任何的字符
![20230606174220](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606174220.png)
5. `*`不单独使用,它和上一个字符连用,表示匹配上一个字符0次或多次,下面这个指令就可以匹配`rt,rot,root,rooot等r*t的所有行`
```sh
cat /etc/passwd | grep ro*t
```
6. `.*`两个一起用,表示任意字符任意个数
![20230606174856](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606174856.png)
7. `[]`,字符区间
	- `[]`表示匹配某个范围内的一个字符,例如
	- `[6,8]`匹配6或者8
	- `[0-9]`匹配一个0~9的数字
	- `[0-9]*`匹配任意长度的数字字符串
	- `[a-z]`匹配一个a-z之间的字符
	- `[a-z]*`匹配任意长度的字母字符串
	- `[a-c,e-f]`匹配a-c或者e-f之间的任意字符
8. `\`表示转义,用于匹配其他特殊字符,**注意要用单引号括起来**
![20230606180419](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606180419.png)

---

匹配手机号:`cat phone_number.sh | grep ^1[34578][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]$`
![20230606181152](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606181152.png)

简写:`cat phone_number.sh | grep -E ^1[34578][0-9]{9}$`,`-E`就表示开启扩展语法
![20230606181310](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230606181310.png)


## 11 文本处理工具

### 11.1 cut

cut的工作原理就是`剪`,具体说就是在文件中负责剪切数据用的,cut命令从文件的每一行剪切字节,字符和字段并将这些字节,字符和字段输出

基本语法:
`cut [选项参数] filename`
**默认分隔符是制表符**

**选项参数**:
1. `-f`:列号,提取第几列
2. `-d`分隔符,按照指定分隔符分隔列,默认是制表符`\t`
3. `-c`按字符进行切割,后加n,n表示取到第几列,比如`-c 1`

案例:

>cut.txt
```txt
dong shen
guan zhen
wo wo
lai lai
```

执行`cut -d " " -f cut.txt`:
![20230607095658](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230607095658.png)

换个文件,可以看的更加明显一些
![20230607100155](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230607100155.png)

嗯...

就到这吧