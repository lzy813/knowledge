# 一、前置知识

## 1、了解知识

- Python 是荷兰计算机科学家吉多・范罗苏姆（Guido van Rossum，龟叔）在 1991 发布的一门语法简单、容易上手的**编程语言**。

- 语言：是人类进行沟通交流的主要表达方式。

- 编程语言：是人类与计算机之间进行信息交流的一种特殊的语言。
- 总结：**python是一门语法简单、容易上手且应用非常广泛的编程语言**



## 2、win环境安装

### 2.1 下载

- python官网：https://www.python.org/

![安装1](图片/安装/安装1.png)

- 官网下载Windows版本：https://www.python.org/downloads/windows/

![安装2](图片/安装/安装2.png)

- 下载后如图所示

![安装3](图片/安装/安装3.png)



### 2.2 安装

- 点击安装程序

![安装4](图片/安装/安装4.png)

- 如下图，建议选择自定义安装，确保勾选 "Add Python.exe to PATH" 选项（将 Python 添加到系统环境变量中，这样可以在命令行中直接运行 python 命令）

![安装5](图片/安装/安装5.png)

- 如下图，直接默认下一步

![安装6](图片/安装/安装6.png)

- 正在安装中

![安装8](图片/安装/安装8.png)

- 如下图，有一个提示Disable path length limit 点击关闭长度的限制，点击它然后安装完成。

![安装9](图片/安装/安装9.png)



### 2.3 验证安装

- 打开终端或命令提示符cmd（Windows用户）。

![安装10](图片/安装/安装10.png)

-  输入“python --version”或“python3 --version”，确认Python是否已成功安装。如果输出版本号，则表示安装成功。

![安装11](图片/安装/安装11.png)

- 输入“python”或“python3”，进入Python交互式环境。你可以在这里编写和运行Python代码。

![安装12](图片/安装/安装12.png)

- 查看pip版本

![安装13](图片/安装/安装13.png)



## 3、第一个python程序

- 打开cmd，输入python进入交互界面，输入下面代码后回车

~~~python
print('hello world')
~~~

- 输出：hello world

- 注意：<font color="red">**输入的双引号和括号都要使用英文的**</font>



## 4、第一个程序的常见问题

- 在命令提示符内输入 `python` 时，出现提示：

  ```
  'python' 不是内部或外部命令，也不是可运行的程序或批处理文件。
  ```

  - 原因：安装 Python 时，没有勾选 **Add Python 3.10 to PATH** 选项。

  - 解决步骤：

    1. 卸载当前 Python 程序。

    1. 重新运行 Python 安装程序，**务必勾选** `Add Python 3.10 to PATH` 选项（同时可勾选 `Install launcher for all users (recommended)`）。

    1. 安装完成后，重新打开命令提示符程序，即可正常使用 `python` 命令。

- 在命令提示符内直接执行 Python 代码时，出现提示：

  ```
  无法初始化设备 PRN
  ```

  - 原因：**没有进入 Python 解释器环境**，直接在 CMD 中执行了 Python 代码，系统误将 `print` 识别为 DOS 打印命令。
  - 解决步骤：
    - 在命令提示符中先输入 `python`，按下回车，等待出现 `>>>` 标记，代表已进入 Python 交互环境。
    - 在 `>>>` 后输入 Python 代码（如 `print("Hello World!!!!")`），再按下回车执行。

- 在 Python 交互环境中执行代码时，出现语法错误：

  ```
  SyntaxError: invalid character '“' (U+201C)
  ```

  - 原因：代码中使用了**中文符号**，Python 语法只识别英文符号。
  - 需要检查并修正的符号：
    - **双引号**：必须使用英文双引号 `" "`，不能使用中文双引号 `“ ”`
    - **小括号**：必须使用英文小括号 `( )`，不能使用中文小括号 `（ ）`



## 5、python解释器

- 计算机只认识二进制，即：0和1
- 所以计算机并不认识python代码，但是<font color="red">**通过python解释器程序将代码翻译成二进制**</font>，然后计算机就认识了

![python解释过程](图片/安装/python解释过程.png)

- python解释器的功能
  - 宏观功能
    - 翻译代码
    - 提交给计算机运行
  - 直接功能
    - 将 Python 代码翻译成计算机认识的 0 和 1 并提交计算机执行
    - 在解释器环境内可以一行行的执行我们输入的代码
    - 也可以使用解释器程序，去执行`.py`代码文件

- 解释器放在：python安装目录/python.exe
- cmd中执行python就是执行的这里的python.exe

![python解释器](图片/安装/python解释器.png)

- .py文件是什么

  - python语言的代码文件，里面记录了python代码

- 执行多行代码方式

  - 编写一个python.py文件

  ~~~python
  print('lzy')
  print('djb')
  ~~~

  - 然后通过cmd命令：python py文件

  ~~~bash
  python D:\test.py
  ~~~

- cmd退出python模式

  - exit()



# 二、一阶段

## 1、基础语法

### 1.1 字面量

- 字面量：在代码中，<font color="red">**被写下来的固定的值**</font>，称为字面量
- python中有6种数据类型

| 类型               | 描述                   | 说明                                                         |
| ------------------ | ---------------------- | ------------------------------------------------------------ |
| 数字（Number）     | 整数（int）            | 整数（int），如：10、-10                                     |
|                    | 浮点数（float）        | 浮点数（float），如：13.14、-13.14                           |
|                    | 复数（complex）        | 复数（complex），如：4+3 j，以 j 结尾表示复数                |
|                    | 布尔（bool）           | 布尔（bool）表达现实生活中的逻辑，即真和假，True表示真，False表示假。<font color="red">**True本质上是一个数字记作1，False记作0**</font> |
| 字符串（String）   | 描述文本的一种数据类型 | 字符串（String）由任意数量的字符组成                         |
| 列表（List）       | 有序的可变序列         | 使用最频繁的数据类型，可有序记录一堆数据                     |
| 元组（Tuple）      | 有序的不可变序列       | 可有序记录一堆不可变的python数据集合                         |
| 集合（Set）        | 无序不重复集合         | 可无序记录一堆不重复的python数据集合                         |
| 字典（Dictionary） | 无序key-value集合      | 可有序记录一堆key-value型的python数据集合                    |

- 字符串（String）：又称文本，由任意数量的字符如中文、英文、各类符号、数字等组成。所以叫做字符的串
  - python中，<font color="red">**字符串需要用引号包起来，被引号包起来的，都是字符串**</font>
  - 如："lzy"、"djb"
- 最常用的字面量

| 类型           | 程序中的写法 | 说明                                                         |
| -------------- | ------------ | ------------------------------------------------------------ |
| 整数           | 666、-88     | 和现实中的写法一致                                           |
| 浮点数（小数） | 13.14、-5.21 | 和现实中的写法一致                                           |
| 字符串（文本） | "lzy"        | <font color="red">**程序中需要加上双引号来表示字符串**</font> |

- 用print打印上述字面量

~~~python
print(666)
print(13.14)
print("lzy")
~~~



### 1.2 注释

- 注释：在程序代码中对程序代码进行解释说明的文字

- 作用：注释不是程序，<font color="red">**不能被执行**</font>，只是对程序代码进行解释说明，让别人可以看懂程序代码的作用，能够大大增强程序的可读性

- 注释类型

  - 单行注释：以 <font color="red">**#开头**</font>，#右边的所有文字当作说明，而不是真正要执行的程序，起辅助说明作用
    - 注：<font color="red">**#号和注释内容一般建议一个空格隔开**</font>

  ~~~python
  # 这是单行注释
  print("单行注释")
  ~~~

  - 多行注释：以<font color="red">**一对三个双括号**</font>引起来，("""注释内容""")来解释说明一段代码的作用和使用方法

  ~~~python
  """
  多行注释1
  多行注释2
  """
  print("多行注释")
  ~~~



### 1.3 变量

- 变量：<font color="red">**在程序运行时**</font>，能<font color="red">**存储**</font>计算结果或能<font color="red">**表示值**</font>的抽象概念
- 简单的说，<font color="red">**变量就是程序时，记录数据用的**</font>
- 定义格式
  - 变量名：每一个变量都有自己的名称，称之为：<font color="red">**变量名，也就是变量本身**</font>
  - =：赋值，表示<font color="red">**将等号右边的值，赋给左侧的变量**</font>
  - 变量值：每一个变量都有自己存储的值（内容），称之为：<font color="red">**变量值**</font>

~~~python
变量名 = 变量值
~~~

- 变量的特征：变量值可以改变

~~~python
# 定义一个变量
money = 10
print("金额为：", money)

# 变量改变
money = money + 1
print("修改后的金额为：", money)

"""
输出：
金额为： 10
修改后的金额为： 11
"""
~~~



### 1.4 数据类型

- 用type查看数据的类型
  - 可以查看字面量的类型
  - 可以查看变量的类型
  - <font color="red">**type带返回值，一般用print()打印type输出**</font>

~~~python
type(被查看数据)
print(type(被查看数据))
~~~

- 例子

~~~python
# 打印字面量类型
print(type(10))
print(type(1.15))
print(type("lzy"))

# 打印变量量类型
a = 10
b = 1.15
c = "lzy"
print(type(a))
print(type(b))
print(type(c))

"""
输出：
<class 'int'>
<class 'float'>
<class 'str'>
<class 'int'>
<class 'float'>
<class 'str'>
"""
~~~

- 问题：
  - 通过type(变量)可以输出类型，那么是查看变量的类型还是数据的类型
    - <font color="red">**查看的是：变量存储的数据的类型**</font>。因为变量无类型，但是它存储的数据有
    - <font color="red">**变量是没有类型的**</font>



### 1.5 数据类型转换

- 数据类型之间，在特定的场景下，是可以互相转换的，如字符串转数字、数字转字符串等
- 数据类型转换的场景
  - 从文件中读取的数字，默认是字符串，需要转为数字类型
  - 后续input()语句，默认结果是字符串，若需要数字也需要转换
  - 将数字转换成字符串用于写到外部系统
  - 等等
- 转换方法：<font color="red">**这三个方法都是带返回值的，可以直接用print()打印**</font>

| 语句(函数) | 说明                |
| ---------- | ------------------- |
| int(x)     | 将x转换为一个整数   |
| float(x)   | 将x转换为一个浮点数 |
| str(x)     | 将x转换为一个字符串 |

- 注意：
  - <font color="red">**浮点数转整数，会丢失精度，也就是小数部分**</font>
  - <font color="red">**字符串转数字，字符串必须要是数字才可以转，不然就会报错**</font>
- 例子

~~~python
# 整数转浮点数
float_num = float(10)
print(float_num)

# 浮点数转整数
int_num = int(1.5)
print(int_num)

# 数字转字符串
num_str = str(11)
print(type(num_str), num_str)

float_str = str(1.5)
print(type(float_str), float_str)

# 字符串转数字
num1 = int("10")
print(type(num1), num1)

num2 = float(1.5)
print(type(num2), num2)

num3 = int("lzy")
print(type(num3), num3)


"""
10.0
1
<class 'str'> 11
<class 'str'> 1.5
<class 'int'> 10
<class 'float'> 1.5
Traceback (most recent call last):
  File "D:\AI\code\final\multimodal\api\multimodal\Test.py", line 23, in <module>
    num3 = int("lzy")
           ^^^^^^^^^^
ValueError: invalid literal for int() with base 10: 'lzy'
"""
~~~



### 1.6 标识符

- 在python程序中，可以给很多东西取名，比如：
  - 变量名
  - 方法名
  - 类名
- 这些名字，统一称为标识符，用来左内容的标识
- 总结：<font color="red">**标识符是用户在编程的时候所使用的一系列名字，用于给变量、类、方法等命名**</font>

- 标识符命名规则
  - <font color="red">**内容限定**</font>：只允许出现英文、中文、数字、下划线_这四类
    - 不推荐使用中文
    - 数字不可以开头
  - <font color="red">**大小写敏感**</font>：小写字母和大写字母是不一样的
  - <font color="red">**不可以使用关键字**</font>：python中有一系列单词是关键字，有特定作用，所以标识符命名不可以用这些

~~~python
# 内容限定：只允许出现英文、中文、数字、下划线_这四类，数字不可以开头
# 错误代码示例：1_name = "张三"
# 错误代码示例：name_! = "张三"
name_1 = "张三"
print(name_1)

# 大小写敏感
a = "lzy"
A = "lzy"
print(a)
print(A)

# 不可以使用关键字
# 错误代码示例：True = "123"
~~~

- 标识符命名规范

  - 变量命名
    - 明了：变量名要能明确知道他是干啥的
    - 简洁：在确保"明了"的前提下，减少名字长度
    - <font color="red">**多个单词组合变量名，要使用下划线做分隔**</font>
    - <font color="red">**命名变量中的英文字母要全小写**</font>

  ~~~python
  single_name = "lzy"
  ~~~



### 1.7 运算符

- 算术运算符

| 运算符 | 描述   | 实例                                          |
| ------ | ------ | --------------------------------------------- |
| +      | 加     | 两个对象相加a + b 输出结果30                  |
| -      | 减     | 一个数减另一个数 a - b = 10                   |
| *      | 乘     | 两个数相乘                                    |
| /      | 除     | b / a 输出结果为2                             |
| //     | 取整数 | 返回商的整数部分 9 // 2 = 4，9.0 // 2.0 = 4.0 |
| %      | 取余   | 返回除法的余数b % a 输出结果为0               |
| **     | 指数   | a**b为10的3次方，输出1000                     |

- 例子

```python
# 算术运算符
print("1 + 1 = ", 1 + 1)
print("1 - 1 = ", 1 - 1)
print("2 * 2 = ", 2 * 2)
print("3 / 2 = ", 3 / 2)
print("3 // 2 = ", 3 // 2)
print("10 ** 3 = ", 10 ** 3)

"""
输出结果
1 + 1 =  2
1 - 1 =  0
2 * 2 =  4
3 / 2 =  1.5
3 // 2 =  1
10 ** 3 =  1000
"""
```

- 赋值运算符

| 运算符 | 描述       | 实例                                                         |
| ------ | ---------- | ------------------------------------------------------------ |
| =      | 赋值运算符 | 把=右边的结果赋给左边的变量，如num = 1 + 2 * 3，结果num的值为7 |

- 复合赋值运算符

| 运算符 | 描述           | 实例                       |
| ------ | -------------- | -------------------------- |
| +=     | 加法赋值运算符 | c += a 等价于 c = c + a    |
| -=     | 减法赋值运算符 | c -= a 等价于 c = c - a    |
| *=     | 乘法赋值运算符 | c *= a 等价于 c = c * a    |
| /=     | 除法赋值运算符 | c /= a 等价于 c = c / a    |
| %=     | 取余赋值运算符 | c %= a 等价于 c = c % a    |
| **=    | 幂赋值运算符   | c \**= a 等价于 c = c ** a |
| //=    | 取整赋值运算符 | c //= a 等价于 c = c // a  |

- 例子

~~~python
# 赋值运算符
num = 1
num += 2
print("num += 2：", num)
num -= 2
print("num -= 2：", num)
num *= 2
print("num *= 2：", num)
num /= 2
print("num /= 2：", num)
num %= 2
print("num %= 2：", num)
num //= 2
print("num //= 2：", num)
num **= 2
print("num **= 2：", num)

"""
输出结果
num += 2： 3
num -= 2： 1
num *= 2： 2
num /= 2： 1.0
num %= 2： 1.0
num //= 2： 0.0
num **= 2： 0.0
"""
~~~



### 1.8 字符串扩展

#### 1.8.1 字符串的三种定义方式

- 字符串在python中有多种定义形式
  - 单引号定义法：name = 'lzy'
  - 双引号定义法：name = "lzy"
  - 三引号定义法：name = """lzy"""
    - 三引号定义法，和多行注释一样，支持换行操作
    - 使用变量接收它，它就是字符串
    - 不使用变量接收，就可以作为多行注释使用

~~~python
# 单引号
name1 = '单引号'

# 双引号
name2 = "双引号"

# 三引号
name3 = """
三引号1
三引号2
三引号3
"""

print(name1)
print(name2)
print(name3)

"""
输出结果
单引号
双引号

三引号1
三引号2
三引号3
"""
~~~

- 如果要定义的字符串本身，就包含：单引号或者双引号本身
  - 单引号定义法：可以含双引号
  - 双引号定义法：可以含单引号
  - 可以使用转义字符（\）来将引号解除效用，变成普通字符串

~~~python
# 单引号
name1 = '单引号"'

# 双引号
name2 = "双引号'"

# 转义字符
name3 = "\"转义字符\""
name4 = '\'转义字符\''

print(name1)
print(name2)
print(name3)
print(name4)
"""
输出结果
单引号"
双引号'
"转义字符"
'转义字符'
"""
~~~



#### 1.8.2 字符串拼接

- 如果有两个字符串（文本）字面量，可以将其拼接成一个字符串，通过+号即可完成
  - print("lzy " + "djb")
  - 输出：lzy djb
- 字面量和变量 或 变量和变量之间也可以使用拼接

~~~python
# 正常拼接
name = "lzy"
address = "湖北武汉"
print("你好 " + name)
print(name + "住在" + address)

# 非正常拼接
age = 18
print("年龄 " + age)

"""
输出结果
Traceback (most recent call last):
  File "D:\AI\code\final\multimodal\api\multimodal\Test.py", line 9, in <module>
    print("年龄 " + age)
          ~~~~~~~~^~~~~
TypeError: can only concatenate str (not "int") to str
你好 lzy
lzy住在湖北武汉
"""
~~~

- 注意：<font color="red">**无法和非字符串之间进行拼接**</font>



#### 1.8.3 字符串格式化

