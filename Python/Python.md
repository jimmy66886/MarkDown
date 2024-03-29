2023年3月4日 15点29分

# Python学习笔记

对于Python,我感觉只需要学习一些跟Java语言的区别,其他就不必深究了,考试能过就行

## 1 Python基本语法

### 1.1 数据输入与输出

> 使用内置的输入函数 `input()`和输出函数 `print()`可以实现程序和用户的交互

#### 1.1.1 input()

最基本的使用:

```py
name = input("请输入姓名 ")
print(name)
```

但是input()函数语句只能得到文本(字符串),如果想要得到一个数字,则要将输入的数据进行处理:`变量 = eval(input("提示字符串"))`

```py
number = eval(input("请输入一个数字 "))
print(type(number))
```

**此时,如果输入的是整型,则输出int,若是浮点数,则为float**

#### 1.1.2 print()

那怎么能离开 `Hello world`

```py
print("Hello world")
```

默认print()是会有一个换行操作的,如果想去掉换行,则要加上 `end = ""`

```py
print("结果为: ",end="")
print(3+4)
```

![20230304154313](https://img01.zzmr.club/img/20230304154313.png)

## 2 数据类型

### 2.1 数值类型

#### 2.1.1 Python的特点

1. **Python中的整数位数可以为任意长度(只受限于计算机内存),而Python的浮点类型float与其他语言的double和float对应,Python语言中的浮点类型数据的精度与系统相关**
2. 对于整除,Java中整除是 `/`(整型之间是整除,而浮点型则是正常的除法),而Python中整除是 `//`(向下取整:`10/4=2`),Python的 `/`代表的是实数除法,即 `10/4=2.5`
3. 乘方为 `**`,`2**3=8`

> 有50,5,1元的人民币若干张,输入一个整数金额值,给出找钱的方案,假设人民币足够多,且优先使用面额大的钱币

```py
money = eval(input("请输入金额 "))
m50 = money // 50
m5 = money % 50//5
m1 = money % 50 % 5

print("需要50元",m50,"张  ",end="")
print("需要5元",m5,"张  ",end="")
print("需要1元",m1,"张  ",end="")
```

#### 2.1.2 内置数值运算函数

| 函数               | 描述                                                                                      |
| ------------------ | ----------------------------------------------------------------------------------------- |
| abs(x)             | 求x的绝对值                                                                               |
| divmod(x,y)        | 输出(x//y,x%y)                                                                            |
| pow(x,y[,z])       | 输出(x**y)%z,[]表示可选参数,<br />当z省略时,等价于x `**`y                               |
| round(x[,ndigits]) | 对x进行四舍五入操作,保留ndigits<br />位小数,当ndigits省略时,返回x四舍五入<br />后的整数值 |
| max(x1,x2,....xn)  | 返回x1,x2,...xn中的最大值                                                                 |
| min(x1,x2,.....xn) | 返回x1,x2,...xn中的最小值                                                                 |

#### 2.1.3 字符串

> 字符串的索引与切片

字符串是一个字符序列,要访问字符串中的一个或多个字符,则可以使用索引和切片的操作来完成

```py
s = "人生苦短,我学Python"
print(s[0])     # 结果为 "人"
print(s[12])    # 结果为 "n"
```

> 字符串的切片访问

使用方法:`s[头下标:尾下标]`,**包含上标不包含下标**

```py
s = "人生苦短,我学Python"
print(s[0:4])   # 从0取到4,不含4
print(s[:4])    # 从开始位置取到4,不含4
print(s[5:])    # 从5取到最后
print(s[:])     # 全部
```

*还有什么更复杂的使用,懒得写了,感觉也用不到*

## 3 内置函数

### map函数

map() 函数是 Python 内置函数之一，用于将一个函数应用到一个序列的每个元素上，返回一个新的序列。

具体来说，map() 函数的语法如下：

```py
map(function, iterable, ...)
```

其中，function 是一个函数对象，iterable 可以是一个列表、元组或其他序列类型，... 表示可以有多个 iterable。

map() 函数的工作原理是将 function 应用到 iterable 中的每个元素，生成一个新的可迭代序列，其中每个元素都是原序列中的元素经过 function 处理后的结果。

例如，下面的代码将一个列表中的每个元素加 1，并将结果存储在一个新的列表中：

```py
def add_one(x):
    return x + 1

original_list = [1, 2, 3, 4, 5]
new_list = list(map(add_one, original_list))
print(new_list) # 输出 [2, 3, 4, 5, 6]
```

在这个例子中，函数 add_one 接受一个参数 x，并返回 x+1 的值。map() 函数将 add_one 应用到 original_list 中的每个元素，生成一个新的列表 new_list，其中每个元素都是原列表中的元素加 1 的结果。

需要注意的是，map() 函数返回的是一个迭代器对象，而不是一个列表。如果需要将其转换为列表，可以使用 list() 函数。另外，如果传递给 map() 函数的函数对象需要多个参数，可以将多个 iterable 传递给 map() 函数，这些 iterable 中的元素将被依次传递给函数对象。

---

可以这样用:
```py
list = list(map(int,input().split(",")))
```

这样就可以将输入的用逗号分隔开的字符串转换为一个列表了

![20230422161933](https://img01.zzmr.club/img/20230422161933.png)

## 4 列表和字典

### 4.1 sorted函数

```py
studs = [{'sid':'103','Chinese': 90,'Math':95,'English':92},{'sid':'101','Chinese': 80,'Math':85,'English':82},{'sid':'102','Chinese': 70,'Math':75,'English':72}]

scores = {}
for stud in studs:
    sid = stud['sid']
    scores[sid] = [stud['Chinese'], stud['Math'], stud['English']]


sorted_scores = sorted(scores.items(), key=lambda x: x[0])

i = 0

for sid, grades in sorted_scores:
    i+=1
    if i==3:
        print(f"{sid}:{grades}",end="")
    else:
        print(f"{sid}:{grades}")
```

![20230520180715](https://img01.zzmr.club/img/20230520180715.png)

>python中的数值比较
```py
x=2

y=2.0

if(x==y): print(“Equal”)

else: print(“No Equal”)
```

输出Equal,虽然它们的数据类型不同，但在进行相等性比较时，Python会自动进行类型转换以便进行比较。在这种情况下，整数2和浮点数2.0是相等的，因为它们表示相同的数值。

>这个斐波那契数列

```py
f(n)=f(n−2)+f(n−1) (n≥3)

def f(n):
    if n<=1:
        return n
    else:
        return f(n-2) + f(n-1)

```


---

## 考试

1. 记得看关于字符串的函数
    - 转换ascii值的函数
