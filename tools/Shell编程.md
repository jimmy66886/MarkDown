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

>系统预定义变量

1. 常用系统变量
    - `$HOME`
    - `$PWD`
    - `$SHELL`
    - `$USER`
2. 案例实操
    - 查看系统变量的值
        - ![20230531165404](https://gcore.jsdelivr.net/gh/jimmy66886/picgo_two@main/img/20230531165404.png)
    - 显示当前Shell中所有变量:set(局部变量)

>自定义变量

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

