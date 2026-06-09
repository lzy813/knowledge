# 一、FastAPI 介绍

## 1、什么是 FastAPI

- FastAPI 是一个现代的、基于 Python 3.7+ 的 Web 框架，用于构建 API 服务。它使用 Python 的类型提示（type hints）作为核心特性，并基于 Starlette 和 Pydantic 构建，既兼具高性能，又拥有优秀的开发体验。

- 这个框架的目标是让你：

  - 编写更少的代码，做更多的事

  - 获得自动生成的 API 文档

  - 保持高性能（媲美 Node.js、Go）

  - 拥有类型安全的开发体验

- FastAPI 被广泛用于构建 RESTful API、微服务架构，甚至可以作为完整的后端框架来使用



## 2、FastAPI 主要特性

- <font color="red">**高性能**</font>：得益于 Starlette 和 Pydantic 的加持，FastAPI 拥有非常高的性能，接近 Node.js 和 Go，是现今最快的 Python Web 框架之一。性能对比（基于 TechEmpower 等基准测试，简化为每秒请求数）：
  - FastAPI：~3000 请求/秒（异步，轻量）
  - Flask：~1000 请求/秒（同步，受 WSGI 限制）
  - Django：~800 请求/秒（同步，ORM 和中间件开销较大）
- <font color="red">**开发效率高**</font>：FastAPI 使用 Python 类型提示（通过 Pydantic）进行数据验证，减少手动校验代码。类型提示使代码更易读，IDE（如 VSCode）提供自动补全和错误提示。
- <font color="red">**自动生成 API 文档**</font>：FastAPI 内置 Swagger UI 和 ReDoc，自动生成交互式 API 文档。开发者只需编写代码，文档即自动生成，减少维护成本。
- <font color="red">**异步支持**</font>：支持 async/await 语法，适合高并发场景（如实时聊天、流处理）。比传统同步框架（如 Flask）更适合现代 Web 应用



## 3、FastAPI 的适用场景

- RESTful API 接口
- 微服务系统（配合 Docker / Kubernetes）
- AI / ML 服务接口（如模型预测 API）
- 高并发的异步后台任务
- 快速开发原型项目

许多大型项目（如 Uber、Netflix 内部项目、Microsoft 等）都在使用 FastAPI，社区活跃，生态逐渐成熟



# 二、快速入门

## 1、环境准备

- python：FastAPI 要求 Python 版本为 3.8 及以上。

- uv：建议使用 uv 进行项目依赖包管理。

- ASGI 服务器：FastAPI 本身不包含运行服务器，所以你需要同时安装一个 ASGI 服务器。推荐使用 Uvicorn

- 使用 pycharm 新建项目



## 2、开发步骤

- 新建一个文件夹：FastApiDemo
- <font color="red">**用uv初始化开发环境：uv init**</font>
- 添加依赖

~~~bash
# fastapi的依赖
uv add fastapi
# ASGI 服务器：FastAPI 本身不包含运行服务器，所以你需要同时安装一个 ASGI 服务器。推荐使用 Uvicorn
uv add uvicorn
~~~

- 创建一个名为 `main.py` 的文件，并写入以下代码：

~~~python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    """
    处理根路径的GET请求
    
    Returns:
        dict: 包含欢迎消息的字典，格式为{"message": "Hello World"}
    """
    return {"message": "Hello World"}
~~~

- <font color="red">**运行服务器：uvicorn main:app --reload**</font>
  - `main`：表示 Python 文件名（不带 `.py`）。
  - `app`：表示 FastAPI 应用的实例名。
  - `--reload`：启用热重载，适合开发阶段使用。

- 访问以下地址即可看到运行结果：
  - API 接口：http://127.0.0.1:8000
  - Swagger 文档：http://127.0.0.1:8000/docs
  - ReDoc 文档：http://127.0.0.1:8000/redoc



# 三、详细解析

## 1、路由

### 1.1 什么是路由

- 在 Web 开发中，“路由（Route）”指的是将 <font color="red">**URL 路径**</font>与对应的<font color="red">**处理逻辑（函数）**</font>关联起来。它决定了当用户访问某个特定网址时，服务器应该执行那段代码来返回结果

- FastAPI 中通过装饰器（如 `@app.get()`、`@app.post()` 等）来定义路由，告诉框架：<font color="red">**当客户端访问某个路径，并使用某个方法时，应该调用哪个函数来处理请求**</font>



### 1.2 路由写法

~~~bash
@app.get("/")
async def root():
    return {"message": "Hello World"}
~~~

- <font color="red">**FastAPI路由定义基于python的装饰器模式**</font>
  - app：FastApi的示例
  - get：请求方法
  - ("/")：请求地址
  - return：返回结果

![路由](图片/路由.png)



### 1.3 支持的请求方法

- FastAPI 支持所有常见的 HTTP 请求方法：

  | 方法      | 说明                     |
  | --------- | ------------------------ |
  | `GET`     | 获取资源                 |
  | `POST`    | 创建资源                 |
  | `PUT`     | 更新整个资源（完全替换） |
  | `PATCH`   | 部分更新资源（局部修改） |
  | `DELETE`  | 删除资源                 |
  | `OPTIONS` | 获取服务器支持的通信选项 |
  | `HEAD`    | 获取响应头而不返回响应体 |

- 常用的是前五种（GET、POST、PUT、PATCH、DELETE）



### 1.4 使用案例

#### 1.4.1 定义基本路由

~~~python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def read_root():
    """
    处理根路径的GET请求

    Returns:
        dict: 包含欢迎消息的字典，格式为{"message": "Hello, FastAPI!"}
    """
    return {"message": "Hello World"}
~~~

- 访问 `http://127.0.0.1:8000/` 时，FastAPI 会执行 `read_root()` 函数，返回一个 JSON 响应。



#### 1.4.2 为同一路径定义多个方法

~~~python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    """
    商品数据模型类

    Attributes:
        name (str): 商品名称
        price (float): 商品价格
    """
    name: str
    price: float


@app.get("/items/")
def read_items():
    """
    获取商品列表接口

    Returns:
        list: 包含商品信息的字典列表，每个字典包含商品名称和价格
    """
    return [{"name": "Apple", "price": 3.5}, {"name": "Banana", "price": 2.0}]


@app.post("/items/")
def create_item(item: Item):
    """
    创建新商品接口

    Args:
        item (Item): 商品对象，包含名称和价格信息

    Returns:
        dict: 包含创建成功消息和商品信息的字典
    """
    return {"message": "Item created", "item": item}
~~~

- 访问 `GET /items/` 会返回一个商品列表；
- 向 `POST /items/` 发送 JSON 数据，会创建一个新商品。

这种方式正是 RESTful API 设计的核心：一个路径代表一个资源，不同方法代表对该资源的不同操作



#### 1.4.3 使用路径参数定义路由

- 可以在路径中使用变量来动态匹配内容，例如：

~~~python
from fastapi import FastAPI

app = FastAPI()


@app.get("/users/{user_id}")
def get_user(user_id: int):
    """
    根据用户ID获取用户信息
    
    参数:
        user_id (int): 用户的唯一标识符
        
    返回:
        dict: 包含用户ID的字典对象
    """
    return {"user_id": user_id}
~~~

- 访问 `/users/123` 时，`user_id` 的值将为 `123`，类型为整数。
- FastAPI 会自动进行类型转换和验证，如果传入的是非整数，例如 `/users/abc`，会返回 422 错误



#### 1.4.4 多个路径指向同一个函数

- 你可以为不同路径设置相同的处理函数，实现路径别名的效果。例如：

~~~python
from fastapi import FastAPI

app = FastAPI()


@app.get("/home")
@app.get("/index")
def homepage():
    """
    处理主页请求的视图函数
    
    该函数绑定到两个路由路径："/home" 和 "/index"，当用户访问这两个路径时，
    将返回欢迎信息。
    
    返回值:
        dict: 包含欢迎消息的字典，格式为 {"message": "Welcome to homepage!"}
    """
    return {"message": "Welcome to homepage!"}
~~~

- 这样无论访问 `/home` 还是 `/index`，都会返回相同的结果



## 2、路径参数

### 2.1 路径参数

- <font color="red">**路径参数（Path Parameter）是 URL 路径中的一部分，用来表示某个资源的标识**</font>。通常用于获取某个特定实体的信息，例如用户 ID、文章 ID 等

- 位置：URL路径的一部分 /book/id
- 作用：指向唯一的、特定的资源
- 方法：GET
- 写法

~~~python
from fastapi import FastAPI

app = FastAPI()

@app.get("/users/{user_id}")
def get_user(user_id: int):
    """
    根据用户ID获取用户信息
    
    参数:
        user_id (int): 用户的唯一标识符
        
    返回值:
        dict: 包含用户ID的字典对象
    """
    return {"user_id": user_id}
~~~

- 访问 `http://127.0.0.1:8000/users/100` 将返回：


~~~bash
{
  "user_id": 100
}
~~~

- `{user_id}` 是路径参数，FastAPI 会自动将 URL 中的部分提取出来并传给函数。
- `user_id: int` 指定了类型为整数，如果传入字符串等类型不匹配的值，FastAPI 会自动返回 422 错误，表示请求验证失败



### 2.2 路径参数类型转换

- FastAPI 会根据类型提示自动完成转换与验证，支持的类型包括：

| 类型  | 示例                  |
| ----- | --------------------- |
| int   | /items/123            |
| float | /price/12.5           |
| str   | /search/keyword       |
| bool  | /flag/true 或 /flag/0 |



### 2.3 路径参数中的路径顺序

- 路径参数中的路径顺序很重要，你不能在两个路径参数不明确区分的情况下定义多个路径。例如：

~~~python
from fastapi import FastAPI

app = FastAPI()

@app.get("/files/{file_path}")
def read_file(file_path: str):
    """
    读取指定路径的文件信息
    
    参数:
        file_path (str): 文件路径参数，从URL中提取
        
    返回值:
        dict: 包含文件路径信息的字典，格式为{"file_path": file_path}
    """
    return {"file_path": file_path}

@app.get("/files/static")
def static_file():
    """
    处理静态文件请求
    
    返回值:
        dict: 包含静态文件消息的字典，格式为{"message": "Static file"}
    """
    return {"message": "Static file"}
~~~

- 访问 `/files/static` 时会优先匹配 `/files/{file_path}`，因为它更早被声明。因此建议将更“具体”的路径放在前面，或使用 `Path(..., regex=...)` 来更精确地控制。

- 响应：{"file_path":"static"}



### 2.4 Path()用法（路径参数）

- 它们的作用：给参数添加<font color="red">**校验规则**</font>（长度、范围、正则）、**默认值**、**描述**、是否必填等，让接口更规范、更安全
- 路径参数是 URL 路径的一部分，<font color="red">**必须通过 Path() 做校验 / 声明**</font>，语法：

~~~python
from fastapi import FastAPI, Path

app = FastAPI()

@app.get("/items/{item_id}")
def read_item(item_id: int = Path(..., 参数配置)):
    return {"item_id": item_id}
~~~

- 核心参数（常用）
  1. `...`：表示**必填参数**（路径参数默认必填）
  2. `gt` / `ge` / `lt` / `le`：数值范围校验
     - `gt`：大于
     - `ge`：大于等于
     - `lt`：小于
     - `le`：小于等于
  3. `title` / `description`：接口文档描述
  4. `pattern`：正则表达式校验
  5. `deprecated=True`：标记参数废弃

- 实例

~~~python
from fastapi import FastAPI, Path

app = FastAPI()

# 1. 基础校验：item_id 必须 ≥ 1
@app.get("/items/{item_id}")
def read_item(
    # ... 表示必填，ge=1 表示大于等于1
    item_id: int = Path(..., ge=1, description="物品ID，必须大于等于1")
):
    return {"item_id": item_id}

# 2. 正则校验：只允许字母和数字
@app.get("/users/{user_id}")
def get_user(
    user_id: str = Path(..., pattern="^[a-zA-Z0-9]+$", title="用户ID")
):
    return {"user_id": user_id}
~~~



### 2.5 路径参数的高级用法

- <font color="red">**使用Path(...)表示这是一个必需的路径参数
                          路径参数允许捕获包含斜杠的完整路径**</font>

- FastAPI 支持你通过 `path` 类型获取包含 `/` 的路径内容：

```python
from fastapi import FastAPI
from fastapi import Path
app = FastAPI()



@app.get("/files/{file_path:path}")
def read_file(file_path: str = Path(...)):
    """
    读取文件路径信息

    该函数通过FastAPI的路径参数捕获功能，接收任意深度的文件路径，
    并将其作为响应返回。

    参数:
        file_path (str): 文件路径字符串，使用Path(...)表示这是一个必需的路径参数
                        路径参数允许捕获包含斜杠的完整路径

    返回值:
        dict: 包含file_path键的字典，值为传入的文件路径字符串
    """
    return {"file_path": file_path}

```

- 访问 `/files/docs/intro.md` 会返回：

```bash
{
  "file_path": "docs/intro.md"
}
```

- <font color="red">**`:path` 表示该参数可以包含斜杠 `/`，适合表示文件路径、嵌套资源等场景**</font>



## 3、查询参数

### 3.1 使用查询参数

- 查询参数（Query Parameter）是 URL 中以 `?` 开头，`key=value` 形式出现的部分，常用于过滤、排序、分页等可选参数。

~~~python
from fastapi import FastAPI

app = FastAPI()

@app.get("/items/")
def list_items(category: str = "all", limit: int = 10):
    """
    获取物品列表
    
    参数:
        category (str): 物品分类，默认为"all"表示所有分类
        limit (int): 返回物品数量限制，默认为10
    
    返回值:
        dict: 包含分类和限制数量的字典
    """
    return {"category": category, "limit": limit}

~~~

- 访问 `http://127.0.0.1:8000/items/?category=fruit&limit=5` 将返回：

~~~bash
{
  "category": "fruit",
  "limit": 5
}
~~~

- 查询参数特点：
  - <font color="red">**无需写在路径中，而是通过 URL `?key=value` 的方式传递**</font>
  - <font color="red">**可以有默认值，也可以声明为必填项（使用 `= Query(...)`）**</font>
  - <font color="red">**不存在参数时会使用默认值**</font>



### 3.2 路径参数和查询参数的对比

~~~python
from fastapi import FastAPI

app = FastAPI()


@app.get("/users/{user_id}")
def get_user(user_id: int):
    """
    根据用户ID获取用户信息

    参数:
        user_id (int): 用户的唯一标识符

    返回值:
        dict: 包含用户ID的字典对象
    """
    return {"user_id": user_id}


@app.get("/book")
def get_book(book_id: int):
    """
    根据书籍ID获取书籍信息

    参数:
        book_id (int): 书籍的唯一标识符

    返回值:
        dict: 包含书籍ID的字典对象
    """
    return {"user_id": book_id}
~~~

- 上面两个接口的访问路径有不同

~~~bash
user：路径参数：http://127.0.0.1:8000/users/1
curl -X 'GET' \
  'http://127.0.0.1:8000/users/1' \
  -H 'accept: application/json'
  
book：查询参数：http://127.0.0.1:8000/book?book_id=1
curl -X 'GET' \
  'http://127.0.0.1:8000/book?book_id=1' \
  -H 'accept: application/json'
~~~

- 不同点

|                           路径参数                           |                           查询参数                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| 写代码：需要在注解里的路径中拼接具体的串：@app.get("/users/{user_id}") |            写代码：不需要拼接：@app.get("/book")             |
|     访问时采用restful风格：http://127.0.0.1:8000/users/1     | 访问时以?key=value的风格：http://127.0.0.1:8000/book?book_id=1 |



### 3.3 路径参数与查询参数组合使用

- 可以同时使用路径参数和查询参数，例如：

~~~python
from fastapi import FastAPI

app = FastAPI()

@app.get("/users/{user_id}/orders")
def get_orders(user_id: int, limit: int = 5):
    """
    获取指定用户的订单列表
    
    Args:
        user_id (int): 用户ID，用于标识要查询订单的用户
        limit (int, optional): 返回订单数量限制，默认值为5
        
    Returns:
        dict: 包含用户ID和限制数量的字典对象
    """
    return {"user_id": user_id, "limit": limit}

~~~

- 访问 `http://127.0.0.1:8000/users/42/orders?limit=10`，将得到：

~~~bash
{
  "user_id": 42,
  "limit": 10
}
~~~



### 3.4 Query () 用法（查询参数）

- 查询参数是 URL 问号后的键值对，**用 Query() 声明校验规则**，语法：

```python
from fastapi import FastAPI, Query

@app.get("/items/")
def read_items(name: str = Query(默认值, 参数配置)):
    return {"name": name}
```

- 核心参数（常用）

1. `default`：默认值（写在第一个位置）
2. `...`：必填查询参数
3. `min_length` / `max_length`：字符串长度校验
4. `gt`/`ge`/`lt`/`le`：数值范围
5. `pattern`：正则校验
6. `alias`：参数别名（如用 `item-name` 代替 `item_name`）
7. `deprecated=True`：标记废弃

- 示例代码

```python
from fastapi import FastAPI, Query
from typing import Optional

app = FastAPI()

# 1. 基础：可选参数，默认值，长度校验
@app.get("/items/")
def read_items(
    # 可选参数，默认 None，长度 2-10
    name: Optional[str] = Query(None, min_length=2, max_length=10)
):
    return {"name": name}

# 2. 必填查询参数
@app.get("/required/")
def required_param(q: str = Query(..., description="必填参数")):
    return {"q": q}

# 3. 数值范围 + 别名
@app.get("/price/")
def get_price(
    # 别名 price_min，参数 ≥ 0
    price: float = Query(..., alias="price_min", ge=0)
):
    return {"price_min": price}

# 4. 接收多个值（列表）
@app.get("/multi/")
def multi_params(tags: list[str] = Query(["default"])):
    return {"tags": tags}
```



### 3.5 Path + Query 混合使用

- 实际开发中，**路径参数 + 查询参数**经常一起用：

```python
from fastapi import FastAPI, Path, Query

app = FastAPI()

@app.get("/users/{user_id}/items/{item_id}")
def get_user_item(
    # 路径参数：user_id ≥ 1，item_id 1-1000
    user_id: int = Path(..., ge=1, title="用户ID"),
    item_id: int = Path(..., gt=0, le=1000),
    
    # 查询参数：可选，长度 3-50
    q: Optional[str] = Query(None, min_length=3, max_length=50)
):
    return {"user_id": user_id, "item_id": item_id, "q": q}
```



## 4、请求体和数据类型

