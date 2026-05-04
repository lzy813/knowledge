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

- 问题：
  - <font color="red">**变量过多，拼接起来很麻烦**</font>
  - <font color="red">**字符串无法和数字或者其他类型完成拼接**</font>
- 使用字符串格式化的语法，完成字符串和变量的快速拼接
  - 其中%s
    - %表示：占位
    - s表示：将变量变成字符串放入占位的地方
    - 所以，综合起来的意思：先占个位置，等会有个变量过来，就把它变成字符串放到占位的位置
  - <font color="red">**多个变量占位，变量要用括号包起来，并按照占位顺序填入**</font>

~~~python
name = "lzy"
massage = "你好 %s" % name
print(massage)

age = 18
message = "%s今年%s岁" % (name, age)
print(message)

"""
结果
你好 lzy
lzy今年18岁
"""
~~~

- python中常用的数据类占位符

| 格式符号 | 转化                             |
| -------- | -------------------------------- |
| %s       | 将内容转换为字符串，放入占位位置 |
| %d       | 将内容转换为整数，放入占位位置   |
| %f       | 将内容转换为浮点数，放入占位位置 |

~~~python
name = "lzy"
age = 18
salary = 8888.88
message = "%s今年%d岁,工资%f" % (name, age, salary)
print(message)

"""
结果：
lzy今年18岁,工资8888.880000
"""
~~~



#### 1.8.4 格式化的精度控制

- 问题：上面的小数格式化的时候，8888.88输出后面多了4个0，现在想要只保留两位小数
- 使用辅助符号 "m.n" 来控制数据的宽度和精度
  - m：控制宽度，要求是数字（很少使用），<font color="red">**设置的宽度小于数字自身，不生效**</font>
  - n：控制小数点精度，要求是数字，<font color="red">**会进行小数的四舍五入**</font>
- 示例：
  - %5d，表示将整数的宽度控制在5位，如数字11，被设置成5d，就会变成：\[空格]\[空格]\[空格]11，用三个空格在左边补齐宽度
  - %5.2f，表示将宽度控制为5，小数点精度设置为2，小数点和小数部分也算入宽度计算。如11.345设置了%7.2f后，结果是\[空格]\[空格]11.35，2个空格补齐宽度，小数部分限制2位精度，四舍五入位.35

~~~python
num1 = 11
num2 = 11.345
print("数字11宽度限制5，结果是%5d" % num1)
print("数字11宽度限制1，结果是%1d" % num1)
print("数字11.345宽度限制7，小数精度为2，结果是%7.2f" % num2)
print("数字11.345不限制宽度，小数精度为2，结果是%.2fd" % num2)

"""
输出结果
数字11宽度限制5，结果是   11
数字11宽度限制1，结果是11
数字11.345宽度限制7，小数精度为2，结果是  11.35
数字11.345不限制宽度，小数精度为2，结果是11.35d
"""
~~~



#### 1.8.5 字符串格式化2

- 通过语法：<font color="red">**f"内容{变量}"的格式来快速格式化**</font>
- 特点
  - 不理会类型
  - 不做精度控制
- 适用于对精度没有要求的时候快速使用

~~~python
name = "lzy"
age = 18
message = f"{name}的年龄为{age}"
print(message)

"""
输出结果
lzy的年龄为18
"""
~~~



#### 1.8.6 表达式格式化

- 表达式：一条具有明确执行结果的代码语句
  - 如：1 + 1、5 * 2就是表达式，因为有具体结果，结果为一个数字
- 或者常见的表达式定义
  - name = "lzy"   age = 11 + 11
- 在字符串格式化的时候，可以直接格式化一个表达式
  - f"{表达式}"
  - "%s、%d、%f" % (表达式1, 表达式2, 表达式3)

~~~python
print("1 + 1的结果：%d" % (1 + 1))
print(f"1 + 1的结果：{1 + 1}")
print("字符串在python代码中的类型是：%s" % type("字符串"))

"""
输出结果
1 + 1的结果：2
1 + 1的结果：2
字符串在python代码中的类型是：<class 'str'>
"""
~~~



### 1.9 数据输入

- print()函数可以将内容（字面量、变量等）输出到屏幕上

- 与之对应的就是input()语句，用来获取键盘输入

  - 数据输入：input
  - 数据输出：print

- 使用方法

  - 使用input()语句从键盘获取输入
  - 使用一个变量接收（存储）input语句获取的键盘输入数据
  - 获取到的数据永远都是<font color="red">**字符串类型**</font>

  ~~~python
  变量 = input()
  或
  变量 = input(提示信息)
  ~~~

- 例子：

~~~python
a = input("你是谁：")
print(f"你是{a}")

"""
结果
你是谁：lzy
你是lzy
"""
~~~



## 2、判断语句

### 2.1 布尔类型和比较运算符

-   布尔（bool）表达现实生活中的逻辑，即真和假，True表示真，False表示假。<font color="red">**True本质上是一个数字记作1，False记作0**</font>
- 布尔类型的字面量
  - True 表示 真（是）
  - False 表示 假（否）
- 定义变量存储布尔类型数据：<font color="red">**变量名 = 布尔类型字面量**</font>
- 布尔类型不仅可以通过自行定义出来，也可以通过计算得来，即：<font color="red">**通过比较运算符进行比较运算得到布尔类型的结果**</font>

```python
result = 10 > 5
print(f"10 > 5的结果是：{result}, 类型为：{type(result)}")

result = (True == 1)
print(result)

"""
结果
10 > 5的结果是：True, 类型为：<class 'bool'>
True
"""
```

- 比较运算符

| 运算符 | 描述                                                         | 示例                          |
| ------ | ------------------------------------------------------------ | ----------------------------- |
| ==     | 判断内容<font color="red">**是否相等**</font>，满足为True，不满足为False | a = 3, b = 3, (a == b) 为True |
| !=     | 判断内容<font color="red">**是否不相等**</font>，满足为True，不满足为False | a = 1, b = 3, (a != b) 为True |
| >      | 判断运算符左侧内容<font color="red">**是否大于**</font>右侧内容，满足为True，不满足为False | a = 7, b = 3, (a > b) 为True  |
| <      | 判断运算符左侧内容<font color="red">**是否小于**</font>右侧内容，满足为True，不满足为False | a = 3, b = 7, (a < b) 为True  |
| \>=    | 判断运算符左侧内容<font color="red">**是否大于等于**</font>右侧内容，满足为True，不满足为False | a = 3, b = 3, (a >= b) 为True |
| <=     | 判断运算符左侧内容<font color="red">**是否小于等于**</font>右侧内容，满足为True，不满足为False | a = 3, b = 3, (a <= b) 为True |

- 例子

~~~python
bool_1 = True
bool_2 = False
print(f"bool_1的变量内容是：{bool_1}, 类型是{type(bool_1)}")
print(f"bool_2的变量内容是：{bool_2}, 类型是{type(bool_2)}")

num1 = 10
num2 = 5
print(f"10 == 5的而己过：{num1 == num2}")
print(f"10 != 5的而己过：{num1 != num2}")
print(f"10 > 5的而己过：{num1 > num2}")
print(f"10 < 5的而己过：{num1 < num2}")
print(f"10 >= 5的而己过：{num1 >= num2}")
print(f"10 <= 5的而己过：{num1 <= num2}")

"""
输出结果
bool_1的变量内容是：True, 类型是<class 'bool'>
bool_2的变量内容是：False, 类型是<class 'bool'>
10 == 5的而己过：False
10 != 5的而己过：True
10 > 5的而己过：True
10 < 5的而己过：False
10 >= 5的而己过：True
10 <= 5的而己过：False
"""
~~~



### 2.2 if语句的基本格式

- if语句的基本格式
  - <font color="red">**注意缩进**</font>：属于if语句的代码块，需要在前面填充4个空格
  - <font color="red">**条件判断后面有个冒号**</font>
  - 判断语句的结果必须是布尔类型True或者False
  - True会执行if内代码语句
  - False不会执行

~~~python
if 要判断的条件:
    条件成立时，要做的事情
~~~

- 例子

~~~python
age = 17
if age < 18:
    print("不可以羞羞")
    
"""
输出结果
不可以羞羞
"""
~~~



### 2.3 if else语句

- if else语句的基本格式
  - <font color="red">**注意缩进**</font>：属于if语句和else的代码块，需要在前面填充4个空格
  - <font color="red">**条件判断和else后面有个冒号**</font>
  - 判断语句的结果必须是布尔类型True或者False
  - True会执行if内代码语句
  - False会执行else内代码语句
  - else不需要判断条件，当if的条件不满足的时候else会执行

~~~python
if 条件:
    满足条件要做的事1
    满足条件要做的事2
    满足条件要做的事3
    ...
else:
    不满足条件要做的事1
    不满足条件要做的事2
    不满足条件要做的事3
    ...
~~~

- 例子：

~~~python
age = 18
if age >= 18:
    print("成年人")
else:
    print("未成年")
    
"""
输出结果
成年人
"""
~~~



### 2.4 if elif else语句

- if elif else语句的基本格式
  - <font color="red">**注意缩进**</font>：属于if语句、elif语句和else的代码块，需要在前面填充4个空格
  - <font color="red">**if语句、elif语句和else后面有个冒号**</font>
  - 判断语句的结果必须是布尔类型True或者False
  - 满足那个条件就执行哪个代码块的代码，所有条件都不满足就执行else的代码块内容
  - <font color="red">**判断条件是互斥且有序的**</font>，上一个条件满足就不会执行后面的代码块
  - elif可以写多个
  - else可省略不写，相当于三个独立的if判断

```python
if 条件1:
    满足条件1要做的事
    满足条件1要做的事
    ...
elif 条件2:
    满足条件2要做的事
    满足条件2要做的事
    ...
elif 条件3:
    满足条件3要做的事
    满足条件3要做的事
    ...
else:
    不满足条件要做的事1
    不满足条件要做的事2
    ...
```

- 例子：

```python
score = 60
if score >= 90.0:
    print(f"{score}分是A等")
elif score >= 80.0:
    print(f"{score}分是B等")
elif score >= 70.0:
    print(f"{score}分是C等")
elif score >= 60.0:
    print(f"{score}分是D等")
else:
    print(f"{score}分是不及格")
    
"""
输出结果
60分是D等
"""
```



### 2.5 判断语句的嵌套

- 基础语法格式如下

~~~python
if 条件1:
    满足条件1要做的事情1
    满足条件1要做的事情2
    
    if 条件2:
        满足条件2要做的事情1
        满足条件2要做的事情1
~~~

- 第二个if，属于第一个if内，只有第一个if满足条件，才会执行第二个if
- 嵌套的关键点
  - <font color="red">**空格缩进**</font>
  - 通过空格缩进，来决定语句之间的：<font color="red">**层级关系**</font>

- 例子

~~~python
print("欢迎来到动物园")
if int(input("请输入你的身高：")) > 120:
    print("你的身高大于120cm，不免费")
    print("如果你的vip等级高于3，可以免费")

    if int(input("请输入你的vip等级：")) > 3:
        print("你的vip等级高于3，可免费")
    else:
        print("你的vip等级不高于3，不可免费")
else:
    print("身高太低，免费")
    
"""
结果
欢迎来到动物园
请输入你的身高：123
你的身高大于120cm，不免费
如果你的vip等级高于3，可以免费
请输入你的vip等级：4
你的vip等级高于3，可免费
"""
~~~

- 总结
  - 嵌套判断语句可以用于多条件、多层次的逻辑判断
  - 嵌套判断语句可以根据需求，自由组合if elif else来构建多层次判断
  - 嵌套判断语句，<font color="red">**一定要注意空格缩进，python通过空格缩进来决定层级关系**</font>



## 3、循环语句

### 3.1 while循环

#### 3.1.1 while循环的基础语法

- while循环语句的语法
- 只要条件满足时，会无限循环执行
- 注意
  - while的条件需要得到布尔类型，True表示继续循环，False表示结束循环
  - 需要设置循环终止的条件，如：i += 1配合i < 10，就能确保10次后停止，否则将会无限循环
  - 空格缩进和if判断一样，都需要设置

~~~python
while 条件:
    条件满足时，做的事情
    条件满足时，做的事情
    条件满足时，做的事情
    ...
~~~

- 例子

~~~python
i = 0
while i < 10:
    print("我喜欢你")
    i += 1
    
"""
输出结果
我喜欢你
我喜欢你
我喜欢你
我喜欢你
我喜欢你
我喜欢你
我喜欢你
我喜欢你
我喜欢你
我喜欢你
"""
~~~



#### 3.1.2 while例子

- while循环例1：求1到100的和

~~~python
# 求1~100的和
sum = 0
i = 1
while i < 100:
    sum = sum + i
    i = i + 1

print(f"1~100的和{sum}")

"""
输出结果
1~100的和4950
"""
~~~

- 猜数字游戏

~~~python
# 获取范围在1-100的随机数字
import random
num = random.randint(1, 100)
# 定义一个变量，记录总共猜测了多少次
count = 0

# 通过一个布尔类型的变量，做循环是否继续的标记
flag = True
while flag:
    guess_num = int(input("请输入你猜测的数字:"))
    count += 1
    if guess_num == num:
        print("猜中了")
        # 设置为False就是终止循环的条件
        flag = False
    else:
        if guess_num > num:
            print("你猜的大了")
        else:
            print("你猜的小了")

print(f"你总共猜测了{count}次")
~~~



#### 3.1.3 while的嵌套

- 嵌套语句格式如下

~~~python
while 条件1:
    满足条件1要做的事情1
    满足条件1要做的事情2
    ...
    while 条件2:
        满足条件2要做的事情1
        满足条件2要做的事情1
        ...
~~~

- 使用注意的地方
  - 注意条件控制，避免无限循环
  - 多层嵌套，注意空格缩进来确定层级关系
- 例子

~~~python
"""
演示while循环的嵌套使用
"""

# 外层：表白100天的控制
# 内层：每天的表白都送10只玫瑰花的控制

i = 1
while i <= 100:
    print(f"今天是第{i}天，准备表白......")

    # 内层循环的控制变量
    j = 1
    while j <= 10:
        print(f"送给小美第{j}只玫瑰花")
        j += 1

    print("小美，我喜欢你")
    i += 1

print(f"坚持到第{i}天，表白成功")
~~~



#### 3.1.4 while循环例子

- 九九乘法表

~~~python
"""
演示使用while的嵌套循环
打印输出九九乘法表
"""

# 定义外层循环的控制变量
i = 1
while i <= 9:

    # 定义内层循环的控制变量
    j = 1
    while j <= i:
        # 内层循环的print语句，不要换行，通过\t制表符进行对齐
        print(f"{j} * {i} = {j * i}\t", end='')
        j += 1

    i += 1
    print()         # 输出一个换行
~~~



### 3.2 for循环



