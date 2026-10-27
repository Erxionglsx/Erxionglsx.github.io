# python

[TOC]

## 基础

```python
# 1 定义一个变量
money = 500
print("钱包还有",money)
money = money-10
print("买冰激凌花费10元",money,"元")

# 2 查看类型信息type()
print(type("程序员"))
print(type(11))
print(type(12.5))

# 3 类型转换,str(),int(),float()
str(11)
int("12")
float("12.123")

# 4.1 通过占位的形式，完成拼接，
# %s 内容转字符串，放入占位
# %d 内容转整数，放入占位
# %f 内容转浮点型，放入占位, %.3f,保留3位小数
class_num = "总分"
sum_score = 500
avg_num = 49.5555
message = "一年级的%s有%d分,平均分%.3f" %(class_num,sum_score,avg_num)
print(message)

# 4.2 第二种占位符：f"{占位}"，适用对于精度没有要求
print(f"一年级的{class_num}有{sum_score}分,平均分{avg_num}")

# 5 对表达式进行字符串格式化
print("1 * 1 的结果是：%d" %(1*1))
print(f"1 * 2 的结果是: {1*2}")
print("字符串的类型名是：%s" % type("字符串"))

# 6 input()函数，获取键盘输入信息
#name = input("你是谁？")
#print("我是%s" %name)

# 7 比较运算符的使用,True,False
# ==,!=,>,<,>=,<=
num1 = 10
num2 = 10
print(f"10 == 10的结果是{num1 == num2}")

# 8 if elif else判断语句,注意缩进，通过缩进确定归属关系
height = 150
level = 5
if height >= 120:
    print("身高超出限制，不能免费")
    print("但是，如果vip级别大于5，可以免费，大于3，可以打五折")
    if level > 5:
        print("恭喜你，级别达标")
    elif level > 3:
        print("级别可以打五折")
    else:
        print("抱歉，需要买全价票")
else:
    print("欢迎小朋友，免费游玩")

# 9. while循环
i = 0;
while i < 10:
    print("我要学python")
    i += 1

# 10.1 for循环,字符串
name = "school"
for x in name:
    print(x)

# 10.2 for循环的嵌套，九九乘法表
for x in range(1,10):
    for y in range(1, x+1):
        print(f"{x} * {y} = {x*y}\t", end='')
    print()

# 10.3 for循环 continue break
money = 10000
for i in range(1,21):
    import random
    score = random.randint(1,10)
    if score > 5:
        print(f"员工{i}绩效{score}，满足，发放工资")
        money = money - 1000
    else:
        print(f"员工{i}绩效{score}，不满足，不发放工资")
        continue
    if money == 0:
        break

# 11 range 获得一个数字序列
# 11.1 range(num),从0开始
for x in range(10):
    # 从0开始，到9
    print(x)
# 11.2 range(num1，num2)
for x in range(5 ,10):
    # 从5开始，到10结束，不包含10本身的一个数字序列
    print(x)
# 11.3 range(num1，num2, step)
for x in range(5,10,2):
    # 从5开始，到10结束，不包含10本身的一个数字序列，数字之间的间隔是2
    print(x)
```

## 数据容器

### 1.列表list

```python
# 13.1 list列表，可以容纳不同类型的数据（混装 ）
my_list = ["apple",666,True]
# 正向索引，从0开始
print(my_list[0])
# 反向索引，从后往前，-1开始取值
print(my_list[-1])
print(type(my_list))

# 嵌套列表
mylist1 = [[1,2,3],[4,5,6]]
print(mylist1[0][2])

# list列表的常用操作
# 查找某元素在列表内的下标索引
arrlist = ["java","mysql","python"]
index = arrlist.index("python")
print(f"python在列表中的下标索引值是：{index}")

# 修改特定下标索引的值
arrlist[0] = "程序"
print(f"列表被修改元素值后，结果是{arrlist}")

# 在指定下标位置插入新元素
arrlist.insert(1,"best")
print(f"列表插入元素值后，结果是{arrlist}")

# 在列表的尾部追加`单个`新元素
arrlist.append("安全")
print(f"列表追加元素值后，结果是{arrlist}")

# 在列表的尾部追加`一批`新元素
arrlist2 = [1,2,3]
arrlist.extend(arrlist2)
print(f"列表追加一个新的列表后，结果是{arrlist}")

# 删除指定下标索引的元素（2种方式）
del arrlist[2]
print(f"列表算出元素后的结果是{arrlist}")
# 删除指定下标索引元素，并返回删除的元素值
element = arrlist.pop(2)
print(f"通过pop方法取出元素后列表内容：{arrlist}，取出的元素是{element}")

# 删除某元素在列表中的第一个匹配项
arrlist = ["arr1","arr2","arr1","arr1","arr4","arr2"]
arrlist.remove("arr1")
print(f"通过remove方法移除元素后，列表的结果是：{arrlist}")

# 清空列表
arrlist.clear()
print(f"清空列表后，列表的结果是：{arrlist}")

# 统计列表内某元素的数量
arrlist = ["arr1","arr2","arr1","arr1","arr4","arr2"]
count = arrlist.count("arr1")
print(f"列表中arr1的数量是：{count}")

# 统计列表中全部元素的数量
count = len(arrlist)
print(f"列表的元素数量总共有{count}个")

# list列表的循环，while和for两种方式
my_list = ["arr1","arr2","arr3"]

index = 0
while index < len(my_list):
    element = my_list[index]
    print(f"列表的元素{element}")
    index += 1

for element in my_list:
    print(f"列表的元素{element}")
```

![](https://share.note.youdao.com/yws/api/personal/file/WEBffa5b5cbaeb0ec4704e91ba963c0f089?method=download&shareKey=2c84e2fb07a8bed99eb8fc7bfc29f627)

### 2.元组tuple

```python
# 13.2 元组,定义使用小括号，值不可修改
# 定义元组
t1 = (1,"Hello",True)
t2 = ()
t3 = tuple()
print(f"t1的类型是{type(t1)},内容是{t1}")
print(f"t2的类型是{type(t2)},内容是{t2}")
print(f"t3的类型是{type(t3)},内容是{t3}")

# 定义单个元素的元组,必须有逗号
t4 = ("hello",)
print(f"t4的类型是{type(t4)},内容是{t4}")
# 元组的嵌套
t5 = ((1,2,3),(4,5,6))
print(f"t5的类型是{type(t5)},内容是{t5}")
#下索引取出内容
num = t5[1][2]
print(f"从嵌套元组中取出的数据是：{num}")

# 元组的操作：index查找方法
t6 = ("香蕉","苹果","菠萝")
index = t6.index("香蕉")
print(f"在元组t6中查找香蕉，的下标是：{index}")
# 元组的操作：count统计方法
t7 = ("香蕉","苹果","菠萝","香蕉","香蕉")
index = t7.count("香蕉")
print(f"在元组t7中统计香蕉的数量有：{index}")
# 元组的操作：index查找方法
t8 = ("香蕉","苹果","菠萝","菠萝")
index = len(t8)
print(f"在元组t8的元素有：{index}个")
# 适用while for循环
```

### 3.字符串str

```python
# 13.3 字符串，同元组一样，无法修改的数据容器
#
my_str = "school and system"
# 通过下标索引取值
value = my_str[2]
value2 = my_str[-3]
print(f"取出下标为2的元素：{value}，取出下标为-3的元素： {value2}")

# index方法
value = my_str.index("and")
print(f"在字符串{my_str}中查找and,其起始下标是：{value}")
# replace方法
new_my_str = my_str.replace("school","学校")
print(f"将字符串{my_str},进行替换后得到：{new_my_str}")
# split方法
my_str = "hello list python java"
my_str_list = my_str.split(" ")
print(f"将字符串{my_str}进行切分后得到：{my_str_list}，类型是{type(my_str_list)}")
# strip方法
my_str = " today is good day "
new_my_str = my_str.strip() # 不传入参数，去除首尾空格
print(f"字符串{my_str}后strip后，结果：{new_my_str}")
my_str = "12today is good day21"
new_my_str = my_str.strip("12")
print(f"字符串{my_str}后strip('12')后，结果：{new_my_str}")
# count()和len()的用法如上
```

![](https://share.note.youdao.com/yws/api/personal/file/WEB448a4117b4db0b4bb0e60703e2d20366?method=download&shareKey=b937dd50b1f036efbcd4d19f8294ecbd)

### 序列操作-切片

```python
# 13.4 序列操作-切片
# 语法：序列[起始下标:结束下标:步长]
# 步长标识，依次取元素的间隔

# 对list进行切片，从1开始，4结束，步长1
my_list = [0,1,2,3,4,5,6]
result1 = my_list[1:4]
print(f"结果1：{result1}")
# 对tuple元组进行切片，从头开始，到最后结束，步长1
my_tuple = (0,1,2,3,4,5,6)
result2 = my_tuple[:] # 起始和结束不写表示从头到尾，步长为1可以省略
print(f"结果2：{result2}")
# 对str进行切片，从头开始，到最后结束，步长2
my_str = "01234567"
result3 = my_str[::2]
print(f"结果3：{result3}")
# 对str进行切片，从头开始，到最后结束，步长-1
my_str = "01234567"
result4 = my_str[::-1]  # 等同于将序列反转了
print(f"结果4：{result4}")
# 对列表进行切片，从3开始，1结束，步长-1
my_list = [0,1,2,3,4,5,6]
result5 = my_list[3:1:-1]
print(f"结果5：{result5}")
```

### 4.集合set

```python
# 13.5 集合
# 特点：不允许重复，无序(不支持下标索引)，支持for循环

# 定义集合
my_set = {"长江","黄河","黄河","汾河","长江"}
my_set_empty = set()   # 定义空集合
print(f"my_set的内容是{my_set}，类型是“{type(my_set_empty)}")
# 添加新元素
my_set.add("流沙河")
my_set.add("长江")
# 移除元素
my_set.remove("流沙河")
# 随机取出一个元素，原集合元素会少
element = my_set.pop()
print(f"集合被取出元素是：{element}，取出元素后：{my_set}")
# 统计集合元素数量
num = len(my_set)
# 清空集合
my_set.clear()

# 取两个集合的差集
# 语法：集合1.difference(集合2)
# 功能：取出集合1和集合2的差集（集合1有而集合2没有的）
# 结果：得到一个新集合，集合1和集合2不变
set1 = {1,2,3}
set2 = {1,5,6}
set3 = set1.difference(set2)
print(set3) # 结果：{2,3}

# 消除2个集合的差集
# 语法：集合1.difference_update(集合2)
# 对比集合1和集合2，在集合1内，删除和集合2相同的元素
# 结果：集合1被修改，集合2不变
set1 = {1,2,3}
set2 = {1,5,6}
set1.difference_update(set2)
print(set1) # 结果：{2,3}
print(set2) # 结果：{1,5,6}

# 2个集合合并
# 功能：将集合1和集合2组合成新集合
set1 = {1,2,3}
set2 = {1,5,6}
set3 = set1.union(set2)
print(set3) # 结果：{1,2,3,5,6}

# 集合的遍历
# 集合不支持下标索引，不能用while循环
# 可以用for循环
set1 = {1,2,3,5,5}
for element in set1:
    print(f"集合元素有{element}")
```

![](https://share.note.youdao.com/yws/api/personal/file/WEB2c3c49fd6369859d47cc1ff88a847783?method=download&shareKey=e67b523a6dffab07db259057295d666a)

### 5.字典dist

```python
# 13.6 字典dist
# 同样使用{}，不过存储的元素是一个个的：键值对，key不能重复
# 字典的key和value可以是任意数据类型（key不可为字典）
# 定义字典
my_dist1 = {"张三":99,"李四":88,"王五":50}
# 定义空字典
my_dist2 = {}
my_dist3 = dict()
print(f"字典1的内容是：{my_dist1}，类型：{type(my_dist1)}")
# 从字典中基于key获取value
source = my_dist1["张三"]
print(f"张三的考试分数是：{source}")
# 定义嵌套字典
stu_score_dict = {
    "张三":{
        "语文": 77,
        "数学": 90
    },
    "李四":{
        "语文": 44,
        "数学": 50
    },
}
score = stu_score_dict["张三"]["数学"]

# 字典的常用操作
my_dist = {"张三": 99, "李四": 88, "王五": 50}
# 新增元素
my_dist["李六"] = 59
print(f"字典经过新增后，结果：{my_dist}")
# 更新元素
my_dist["张三"] = 86
print(f"字典经过更新后，结果：{my_dist}")
# 删除元素
score = my_dist.pop("张三")
# 清空元素
my_dist.clear()
# 获取全部的key
my_dist = {"张三": 99, "李四": 88, "王五": 50}
keys = my_dist.keys()
# 遍历字典
# 方式1： 通过获取到全部的keys来完成遍历
for key in keys:
    print(f"字典的key是：{key}")
    print(f"字典的value是：{my_dist[key]}")
# 方式2：直接对字典进行for循环，每一次循环都是直接得到key
for key in my_dist:
    print(f"2字典的key是：{key}")
    print(f"2字典的value是：{my_dist[key]}")
# 统计字典的元素数量
num = len(my_dist)
```

![](https://share.note.youdao.com/yws/api/personal/file/WEB0b591d114189d8df85a3c5cf01791ee8?method=download&shareKey=90e5f13d0d5a6466e7ee35e60b2c7fde)

### 容器对比

![](https://share.note.youdao.com/yws/api/personal/file/WEBae4a37f5595529b047be4fd5fc74893a?method=download&shareKey=71107d4eeb7ea1d6e8cf738c9d31ff01)

### 容器通用方法

![](https://share.note.youdao.com/yws/api/personal/file/WEB8e1dd93fd53b5fefabb52658a08cad46?method=download&shareKey=e75797c7352e86f18c194e9f200b61a7)

## 函数

### 函数定义

```python
# 12.1 自定义函数，如果没有return，默认返回None(false)，"""函数说明文档"""
def add(a,b):
    """
    add函数进行两数相加的功能
    :param a: 相加的其中一个数字
    :param b: 相加的另一个数字
    :return: 返回值是2数相加的结果
    """
    result = a+b
    return result

r = add(3,4)
print(r)

# 12.2 函数 global关键字
num = 100
def test_b():
    global num # 设置内部定义的变量为全局变量
    num = 500

test_b()
print(num) # num=500
```

### 函数多种参数

```python
# 函数多返回值，支持不同类型
def test_return():
    return 1,"hello",True
x,y,z = test_return()
print(x,y,z)

# 1.位置参数：调用函数时根据函数定义的参数位置来传递参数
def user_info(name,age,gender):
    print(f'姓名是{name},年龄是{age},性别是{gender}')
user_info('TOM',20,'男')

# 2.关键字参数：函数调用时通过“键=值”形式传递参数
# 作用：可以让函数更加清晰、容易使用，同时也清除了参数的顺序需求
# 可以不按照固定顺序
user_info(age=20, gender="男", name="小米")
# 可以和位置参数混用，位置参数必须在前，且匹配参数顺序
user_info("小米",age=20,gender="男")

# 3.缺省参数(默认参数)：用于定义函数，为参数提供默认值，调用函数时可不传该默认参数的值,默认值只能在最后
# 作用：当调用函数时没有传递参数，就会使用默认是用缺省参数对应的值
def user_info(name,age,gender='男'):
    print(f'姓名是{name},年龄是{age},性别是{gender}')
user_info('Tim',20)
user_info('Lolo',12,'女')

# 4. 不定长参数
# 位置不定长，以*号标记一个形式参数，传进的所有参数都会被args变量收集，他会根据传进参数的位置合并为一个元组(tuple)，args是元组类型
def user_info(*args):
    print(args)
user_info('TOM')
user_info('TIM',18)

# 关键字不定长，以**号标记一个形式参数，参数是“键=值”形式的情况下，所有的“键=值”都会被kwargs接受，会根据“键=值”组成字典(dict)
def user_info(**kwargs):
    print(kwargs)
user_info(name='TOM',age=20,id=110)
```

### 匿名函数

```python
# 函数作为参数传递
# 将函数传入的作用在于：传入计算逻辑，而非传入数据
def test_func(compute):
    result = compute(1,2)
    print(f"计算结果：{result}")
def compute(x,y):
    return x+y
test_func(compute)

# lambda匿名函数
# 语法：lambda 传入函数:函数体(一行代码)
# 使用def和使用lambda，定义的函数功能完全一致，只是lambda关键字定义的函数是匿名的，无法二次使用
def test_func(compute):
    result = compute(1,2)
    print(f"计算结果：{result}")
test_func(lambda x,y: x+y)
```

## 文件

![](https://share.note.youdao.com/yws/api/personal/file/WEB1096cb134384572584adc4d74f7287f6?method=download&shareKey=8344faee73328fc84d75037c5cdcc864)

### 文件读取

```python
# open()打开函数
# open(name,mode,encoding)
# name：是要打开的目标文件名的字符串
# mode：设置打开文件的模式：只读、写入、追加等
# r模式，读取文件
# encoding：编码格式（推荐UTF-8）
f = open("D:/测试.txt","r",encoding="utf-8")
# read()方法：文件对象.read(num)
# num表示要从文件中读取的数据的长度，如果没有传入num，那么就表示读取文件中所有的数据
print(f'读取10个字节的结果：{f.read(10)}')
print(f'read方法读取全部内容的结果是：{f.read()}')

# readlines()方法读取文件
# readlines()可以按照行的方式把整个文件中的内容进行一次性读取，并且返回的是一个列表，其中每一行的数据为一个元素
lines = f.readlines()
print(f'lines对象的内容是：{lines}')

# readline()方法读取文件，一次读取一行
line1 = f.readline()
line2 = f.readline()
line3 = f.readline()

# for循环读取文件行
for line in lines:
    print(f"每一行数据是：{line}")

# 文件的关闭
f.close()

# with open,执行完成自动关闭，不用close
with open("D:/测试.txt","r",encoding="utf-8") as f:
    for line in f:
        print(f"每一行数据是：{line}")
```

### 写入文件

```python
# W模式，文件不存在，会创建新文件
# W模式，文件存在，会清空原有内容
f = open("D:/test.txt","w",encoding="utf-8")
# write写入，暂存内存，未实际写入
f.write("Hello World")
# flush，将内存中积攒的内容，写入到硬盘的文件中
f.flush()
# close方法，内置了flush功能
f.close()

# a模式，文件不存在，会创建新文件
# a模式，文件存在，会在原有内容后面继续写入
f = open("D:/test.txt","a",encoding="utf-8")
f.write("长江")
f.write("\n黄河")
f.close()
```

![](https://share.note.youdao.com/yws/api/personal/file/WEBd1a70649c5138a0df6d7712f0b8da967?method=download&shareKey=44d3c08195cbe613960d7facbc86032e)

## 异常

```python
# 异常
# 捕获异常基本语法
# try:
#     可能发生错误的代码
# except:
#     如果出现异常执行的代码
try:
    print(name)
except:
    print("出现异常了")

# 捕获指定的异常
try:
    print(name)
except NameError as e:
    print("Name Error")

# 捕获多个异常
try:
    1/0
except (NameError,ZeroDivisionError) as e:
    print("ZeroDivisionError 或者 NameError 异常")

# 捕获所有异常
try:
    1/0
except Exception as e:
    print(f"出现异常了 {e}")
else:
    print("没有异常")
finally:
    print("无论是否有异常，都要执行")
```

## 模块

```python
# python模块，是一个Python文件，以.py结尾，模块能定义函数，类和变量，模块里也能包含可执行的代码
# 语法：[from 模块名] import [模块|类|变量|函数|*] [as 别名]
import time # 导入Python内置的time模块（time.py）
time.sleep(5)

# 使用from导入time的sleep功能
from time import sleep
sleep(5)

# 使用 * 导入time模块的全部功能
from time import *
sleep(5)

# 使用as给特定功能加上别名
import time as t
t.sleep(5)
```

## 包

```python
# 创建python包，包下有默认文件_init_.py
# _init_.py中可以定义__all__=['my_module1']来控制import *能导入的内容
import my_package.my_module1
import my_package.my_module2
my_package.my_module1.info_print1()
my_package.my_module2.info_print2()

from my_package import my_module1
from my_package import my_module2
my_module1.info_print1()
my_module2.info_print2()

from my_package.my_module1 import info_print1
from my_package.my_module2 import info_print2
info_print1()
info_print2()

# 安装第三方包
pip install numpy
# 使用镜像下载，速度更快
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple numpy
```

## JSON转换

```PYTHON
# 导入json模块
import json

# 通过json.dumps(data)方法把python数据转化为了json数据
# 列表转换json
data = [{"name":"老王","age":16},{"name":"张三","age":20}]
data_str = json.dumps(data)
# 字典转换json
data = {"name":"老王","age":16}]
data_str = json.dumps(data)

# 通过json.loads(data)方法把json数据转化为了python数据
# 转为了列表类型
s = '[{"name":"老王","age":16},{"name":"张三","age":20}]'
data = json.loads(s)
# 转为了字典类型
s = '{"name":"老王","age":16}'
data = json.loads(s)
```

## pyecharts

```python
# pyecharts的基础入门
# 导包
from pyecharts.charts import Line
from pyecharts.options import TitleOpts

# 创建一个折线图对象
line = Line()
# 给折线图对象添加x轴的数据
line.add_xaxis(["中国","美国","俄罗斯"])
# 给折线图对象添加y轴的数据
line.add_yaxis("GDP",[30,20,10])
# 设置全局配置项set_global_opts来设置
line.set_global_opts(
    title_opts=TitleOpts(title="GDP展示",pos_left="center"),
)
# 通过render方法，将代码生成为图像
line.render()
```

## 类

```python
#1.设计一个类（类比生活中：设计一张登记表）
class Student:
 name = None
 gender = None
 nationality = None
 native_place = None #
 age = None
#2.创建一个对象（类比生活中：打印一张登记表）
stu_1 = Student()
#3，对象属性进行赋值（类比生活中：填写表单）
stu_1.name="林军杰"
stu_1.gender="男"
stu_1.nationality="中国"
stu_1.native_place="山东省"
stu_1.age = 31
```

### 成员变量和成员方法

```python
# 类中定义的属性（变量），我们称之为：成员变量
# 类中定义的行为（函数），我们称之为：成员方法

# 定义一个带有成员方法的类
# self，表示类对象本身，只有通过self，成员方法才能访问类的成员变量
class Student:
 name = None
# 学生的姓名
 def say_hi(self):
  print(f"大家好呀，我是{self.name}，欢迎大家多多关照")
 def say_hi2(self, msg):
  print(f"大家好，我是：{self.name}，{msg}")

stu = Student()
stu.name = "周杰轮"
stu.say_hi()
stu.say_hi2("不错呦")
```

### 构造方法

```python
# Python类可以使用：_init_()方法，称之为构造方法。
# 在创建类对象（构造类）的时候，会自动执行。
# 在创建类对象（构造类）的时候，将传入参数自动传递给__init__方法使用。
# __init__，注意init前后的2个下划线符号
class Student:
    name = None
    age= None
    tel = None

    def __init__(self,name,age,tel):
        self.name = name
        self.age = age
        self.tel =tel
        print("Student类创建了一个类对象")

stu = Student("王丽丽",22,"123456")
print(stu.name)
```

### 魔术方法

```python
# 内置的类方法
class Student:
    name = None
    age= None
    tel = None

    # __init__魔术方法
    def __init__(self,name,age,tel):
        self.name = name
        self.age = age
        self.tel =tel
        print("Student类创建了一个类对象")

    # __str__魔术方法，控制类转换为字符串的行为
    def __str__(self):
        return f"student类对象,name:{self.name}，age:{self.age}"

    # __lt__魔术方法，自行定义比较字段，对象无法进行比较
    def __lt__(self, other):
        return self.age < other.age

    # __le__魔术方法
    def __le__(self, other):
        return self.age <= other.age

    # __eq__魔术方法
    def __eq__(self, other):
        return self.age == other.age

str = Student("王丽丽",22,"123456")
print(str)

stu1=Student("周杰轮",31,123)
stu2=Student("林俊节",6,211)
print(stu1 < stu2)
```

![](https://share.note.youdao.com/yws/api/personal/file/WEBc52fc283902d1f9a60725b7c73ae7cce?method=download&shareKey=87cff5784b4cf692fe50e49a634eff0f)

## 封装

```python
# 私有成员变量：变量名以__开头（2个下划线）
# 私有成员方法：方法名以__开头（2个下划线）
# 类对象无法访问私有成员，类中的其他成员可以访问私有成员

#定义一个类，内含私有成员变量和私有成员方法
class Phone:
    __current_voltage =0.5
    # 当前手机运行电压
    def __keep_single_core(self):
        print("让CPU以单核模式运行")
    def call_by_5g(self):
        if self.__current_voltage >=1:
            print("5g通话已开启")
        else:
            self.__keep_single_core()
    print("电量不足，无法使用5g通话，并已设置为单核运行进行省电。")
    
phone = Phone()
phone.call_by_5g()
```

## 继承

```python
# 单继承：一个类继承另一个类
# 多继承：一个类继承多个类，按照顺序从左向右依次继承
class Phone:
    IMEI=None # 序列号
    producer="HM" #厂商
    def call_by_4g(self):
        print("4g通话")

class NFCReader:
    nfc_type="第五代"
    producer="HM"
    def read_card(self):
        print("NFC读卡")
    def write_card(self):
        print("NFC写卡")

# 单继承
class Phone2022(Phone):
    face_id="10001"
    # 面部识别ID
    def call_by_5g(self):
        print("2022年新功能：5g通话")

# 多继承
# 如果父类有同名方法或属性，先继承的优先级高于后继承
class MyOhone(Phone,NFCReader):
    pass  # 占位语句，用来保证类定义的完整性，表示无内容，空的意思

pob=MyOhone()
pob.call_by_4g()
pob.write_card()
```

### 复写

```python
# 复写，在子类中重新定义同名的属性或方法，对父类的成员属性或成员方法进行重新定义
# 定义子类，复写父类成员
class MyPhone(Phone):
    producer ="ITHEIMA"
    #复写父类的成员属性
    def call_by_5g(self):
        print("开启CPU单核模式，确保通话的时候省电")
        # 方式1
        # print(f"父类的厂商是：{Phone.producer}")
        # Phone.call_by_5g(self)
        # print("关闭CPU单核模式，确保性能")
        # 方式2
        print(f"父类的厂商是：{super().producer}")
        super().call_by_5g()
        print("关闭CPU单核模式，确保性能")
```

### 类型注解

```python
# 类型注解，协助做代码提示，不强制
# 基础数据类型注解
import json
import random
var_1: int =10
var_2: str ="itheima"
var_3: bool =True
# 类对象类型注解
class Student:
    pass
stu: Student = Student()
# 基础容器类型注解
my_list:list =[1,2, 3]
my_tuple:tuple =(1,2,3)
my_dict:dict= {"itheima":666}
# 容器类型详细注解
my_list:list[int] = [1,2,3]
my_tuple:tuple[int, str,bool] = (1, "itheima",True)
my_dict:dict[str,int] = {"itheima":666}
# 在注释中进行类型注解
var_1 = random.randint(1,10) # type:int
var_2 = json.loads('{"name":"zhangsan"}') #type: dict[str, str]

# 对函数（方法）进行类型注解
# 对形参进行类型注解
def add(x:int, y:int):
	return x + Y
# 对返回值进行类型注解
def func(data: list) -> list:
	return data

# Union类型
# 使用Union可以定义联合类型注解
#使用Union类型，必须先导包
from typing importUnion

my_list: list[Union[int,strl]=[1,2, "itheima"，"itcast"]
def func(data: Union[int,strl)-> Union[int,str]:
	pass
```

## 多态

```python
# 多态指的是，同一个行为，使用不同的对象获得不同的状态。
# 抽象类，包含抽象方法的类，称之为抽象类。抽象方法是指：没有具体实现的方法（pass）
# 演示抽象类
class AC:
    def cool_wind(self):
        """制冷"""
        pass
    def hot_wind(self):
        """制热"""
        pass
    def swing_l_r(self):
        """左右摆风"""
        pass

class Midea_AC(AC):
    def cool_wind(self):
        print("美的空调制冷")
    def hot_wind(self):
        print("美的空调制热")
    def swing_l_r(self):
        print("美的空调左右摆风")

def make_cool(ac: AC):
    ac.cool_wind()

midea_ac = Midea_AC()
make_cool(midea_ac)
```

## python操作数据库

```python
from pymysql import Connection

# 构建MySQL数据库的连接
conn = Connection(
        user='root',
        password='123456',
        host="localhost",
        port=3306,
        autocommit=True  # 自动提交（确认）
)

# 执行非查询性质SQL
cursor = conn.cursor()  # 获取到游标对象
# 选择数据库
conn.select_db("test")
# 游标对象.execute()执行SQL
cursor.execute("select * from students")

result = cursor.fetchall()
for row in result:
    print(row)

# cursor.execute("insert into students values('100002,'test',31)")
# # 对数据库有修改操作的行为时，需要通过commit确认
# conn.commit()

# 关闭连接
conn.close()
```

## PySpark

![](https://share.note.youdao.com/yws/api/personal/file/WEB3dc5b6b6be188374e97eb876f7a626bc?method=download&shareKey=6613cc2891042b1ad020dd78b1852e7d)

### RDD对象

如图可见，PySpark支持多种数据的输入，在输入完成后，都会得到一个：RDD类的对象
RDD全称为:弹性分布式数据集（Resilient DistributedDatasets)
PySpark针对数据的处理，都是以RDD对象作为载体，即：

* 数据存储在RDD内
* 各类数据的计算方法，也都是RDD的成员方法
* RDD的数据计算方法，返回值依旧是RDD对象

```python
from pyspark import SparkConf, SparkContext

conf = SparkConf().setMaster("local[*]").setAppName("test_spark")
sc = Sparkcontext(conf=conf)

#通过parallelize方法将Python对象加载到Spark内，成为RDD对象
rdd1 = sc.parallelize([1,2,3,4,5])
rdd2 = sc.parallelize((1,2,3,4,5))
rdd3 = sc.parallelize("abcdefg")
rdd4 = sc.parallelize({1,2,3,4,5})
rdd5 = sc.parallelize({"key1":"value1", "key2":"value2"})
```

### 读取文件转RDD对象

PySpark也支持通过SparkContext入口对象，来读取文件，来构建出RDD对象。

## 闭包

```python
# 闭包:定义双层嵌套函数，内层函数可以访问外层函数的变量，将内层函数作为外层函数的返回，此内层函数就是闭包函数
# 优点：不定义全局变量，也可以让函数持续访问和修改一个外部变量。
# 优点：闭包函数引用的外部变量，是外层函数的内部变量。作用域封闭难以被误操作修改。
# 缺点：额外的内存占用
# nonlocal关键字的作用：在闭包函数（内部函数中）想要修改外部函数的变量值，需要用nonlocal声明这个外部变量
def outer(logo):
    def inner(msg):
        nonlocal logo
        logo = logo + msg
        print(f"<{logo}>{msg}<{logo}>")

    return inner

fn1 = outer("输出1")
fn1("World")
fn2 = outer("输出2")
fn2("World")

#<输出1World>World<输出1World>
#<输出2World>World<输出2World>
```

## 装饰器

```python
# 装饰器就是使用创建一个闭包函数，在闭包函数内调用目标函数。
# 可以达到不改动目标函数的同时，增加额外的功能。

# 装饰器的一般写法（闭包)
def outer(func):
    def inner():
        print("我睡觉了")
        func()
        print("我起床了")
    return inner

def sleep():
    import random
    import time
    print("睡眠中...")
    time.sleep(random.randint(1,5))

fn=outer(sleep)
fn()

# 装饰器的快捷写法（语法糖)
def outer(func):
    def inner():
        print("我睡觉了")
        func()
        print("我起床了")

    return inner

@outer
def sleep():
    import random
    import time
    print("睡眠中...")
    time.sleep(random.randint(1,5))

sleep()
```























