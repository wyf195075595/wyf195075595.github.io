---
title: python app
date: 2022-06-17 18:23:10
tags: app
categories: python
---

## 创建虚拟环境

这一步并非必要，却十分重要。主要用于多个 python 项目时，同名依赖不同版本情况。创建虚拟环境可以隔离项目之间的依赖（也可以使用 docker 来实现项目隔离）

#### 创建虚拟环境：

```bash
python -m venv venv  # Windows/macOS/Linux 通用命令
```

#### 激活虚拟环境：

```bash
# Windows
.\venv\Scripts\activate

# macOS/Linux
source venv/bin/activate
```

#### 安装依赖：

```bash
pip install flask  # 仅安装到当前虚拟环境
```

#### 查看已安装的包：

```bash
pip list
```

#### 退出虚拟环境：

```bash
deactivate
```

虚拟环境是 Python 开发的**标准实践**，虽然不是强制要求，但它能避免 90% 的环境相关问题。建议养成 “每个项目一个虚拟环境” 的习惯，从一开始就使用它！

## 依赖安装

### 1、requirements.txt

自动生成项目中的依赖 到 requirements.txt 文件

```shell
pip freeze > requirements.txt
```

类似于yarn 的一键安装项目依赖

```shell
pip install -r requirements.txt
```

### 2、**`pyproject.toml`**

```tcl
# pyproject.toml
[build-system]
requires = ["setuptools>=42"]
build-backend = "setuptools.build_meta"

[project]
name = "my-project"
version = "0.1.0"
dependencies = [
    "Flask>=2.0.0",
    "requests>=2.28.0",
]
```

安装方式

```
pip install .  # 安装项目及其依赖
```

### 3、**`setup.py`（传统方法）**

```python
# setup.py
from setuptools import setup

setup(
    name="my-project",
    version="0.1.0",
    install_requires=[
        "Flask>=2.0.0",
        "requests>=2.28.0",
    ],
)
```

```shell
pip install .  # 安装项目及其依赖
```



## 敏感参数配置

项目推荐使用 .env 文件放置，通过 dotenv 第三方模块来加载。.env 文件不提交。生产环境时可以

1. 在生产环境新建一个.env 文件

	```shell
	FLASK_APP=app
	# FLASK_DEBUG=True
	SECRET_KEY=your-secret-key
	```

	

2. 在运行环境设置环境变量

	```shell
	# 临时环境变量
	# Linux/macOS
	export SECRET_KEY=prod_secret_key
	export FLASK_DEBUG=False
	
	# Windows（PowerShell）
	$env:SECRET_KEY = "prod_secret_key"
	$env:FLASK_DEBUG = "False"
	
	# 永久环境变量
	# Linux/macOS：写入 shell 配置文件（如 ~/.bashrc、~/.zshrc）
	echo 'export SECRET_KEY=prod_secret_key' >> ~/.bashrc
	echo 'export FLASK_DEBUG=False' >> ~/.bashrc
	source ~/.bashrc  # 立即生效
	# Windows：通过「系统属性 → 高级 → 环境变量」图形界面配置，或使用 PowerShell 命令永久设置。
	```

3. docker 容器部署

	docker run 命令直接传入

	```shell
	docker run -e "SECRET_KEY=prod_secret" -e "FLASK_DEBUG=False" your-image
	```

	docker-compose.yml 文件配置

	```yml
	# docker-compose.yml
	services:
	  app:
	    image: your-image
	    environment:
	      - SECRET_KEY=prod_secret
	      - FLASK_DEBUG=False
	    # 或引用服务器上的 .env 文件（但需确保该文件仅在服务器存在）
	    env_file:
	      - .env.prod  # 服务器上的生产环境变量文件
	```

	

## app项目架构

```lua
flask_project/
│
├── app/                 # 应用包
│   ├── __init__.py      # 应用工厂
│   ├── routes/          # 路由模块
│   │   ├── __init__.py
│   │   ├── main.py      # 主路由
│   │   └── api.py       # API路由
│   ├── models/          # 业务模块
│   ├── test/            # 测试业务模块
│   │   ├── __init__.py
│   │   └── test.py      # 测试业务逻辑
│   │   └── __init__.py
│   ├── utils/          # 工具类模块
│   │   ├── db.py       # 数据库工具类
│   │   └── __init__.py
│   ├── templates/       # 模板文件
│   │   ├── index.html   # index 页面
│   │   └── about.html   # about 页面,如果使用单页面应用，可以打包成只有一个html文件
│   ├── static/          # 静态文件
│   └── config.py        # 配置文件
│
├── .env                 # 环境变量
├── requirements.txt     # 依赖清单
└── run.py               # 应用入口
```

### 数据库集成

数据库使用的是 pymysql，连接池 dbutils.pooled_db

.env 环境配置

```yml
# 数据库配置
DB_HOST=*.*.*.*
DB_PORT=18453
DB_USER=root
DB_PASSWORD=***
DB_NAME=anjian
DB_CHARSET=utf8mb4
DB_AUTOCOMMIT=True

# 数据库连接池配置
DB_POOL_MIN_CACHED = 5
DB_POOL_MAX_CACHED = 10
DB_POOL_MAX_SHARED = 5
DB_POOL_MAX_CONNECTIONS = 20
DB_POOL_BLOCKING = True
```



```python
'''
Description: 
Author:  
Date: 2025-07-08 17:41:42
LastEditTime: 2025-07-09 09:50:19
LastEditors:  
'''
# app/utils/db.py
import os
import pymysql
from typing import Generator, Any, List, Tuple, Sequence, Callable, Union
from contextlib import contextmanager
from dbutils.pooled_db import PooledDB

DB_CONFIG = {
    "host": os.getenv("DB_HOST", "localhost"),
    "port": int(os.getenv("DB_PORT", 3306)),
    "user": os.getenv("DB_USER", "admin_db"),
    "password": os.getenv("DB_PASSWORD", "***"),
    "database": os.getenv("DB_NAME", "test_db"),
    "charset": os.getenv("DB_CHARSET", "utf8mb4"),
    "autocommit": os.getenv("DB_AUTOCOMMIT", "true").lower() == "true",  # 是否自动提交事务
}

DB_POOL_CONFIG = {
    'mincached': int(os.getenv("DB_POOL_MINCACHED", 5)),                      # 连接池中空闲连接的初始数量
    'maxcached': int(os.getenv("DB_POOL_MAXCACHED", 10)),                     # 连接池中空闲连接的最大数量
    'maxshared': int(os.getenv("DB_POOL_MAXSHARED", 5)),                      # 共享连接的最大数量
    'maxconnections': int(os.getenv("DB_POOL_MAX_CONN_NUM", 20)),             # 连接池允许的最大连接数
    "blocking": os.getenv("DB_POOL_BLOCKING", "true").lower() == "true", # 连接池达到最大连接数时是否阻塞
    "ping": int(os.getenv("DB_POOL_PING", 1)),                           # 检查连接的有效性 (0=从不，1=默认，2=每一次获得连接时，4=每一次游标执行时，7=总是)
    "maxusage": None,                                                    # 一个连接最多被重复使用的次数，None 表示无限制
}
# 创建数据库连接池
pool = PooledDB(
    creator=pymysql,               # 使用 pymysql 作为数据库驱动
    **DB_CONFIG,                   # 数据库连接配置
    #autocommit=True,  # 自动提交事务
    # 连接池参数
    **DB_POOL_CONFIG,              # 连接池配置
)

# 数据库操作上下文管理器
from contextlib import contextmanager

@contextmanager
def get_db_cursor(dict_=False):
    """获取数据库游标，支持字典格式返回。
    Args:
        dict_: 是否使用字典游标（默认 False）
    """
    try:
        with pool.connection() as conn:
            cursor_cls = pymysql.cursors.DictCursor if dict_ else None
            with conn.cursor(cursor_cls) as cur:
                yield cur
    except Exception as e:
        print(f"Database operation failed: {e}")
        raise e


# 使用示例
def fetch_one(sql: str,
              params: tuple | list = None,
              dict_: bool = True) -> Any | None:
    with get_db_cursor(dict_) as cur:
        cur.execute(sql, params)
        return cur.fetchone()

```

db.py 中的 fetch_one 方法 可以到 mudules.test.test.py 中 引入使用

```python
# mudules.test.test.py
from app.utils.db import  fetch_one

# Example usage
def select_query_plan_file_mesures():
    try:
        #
        data_sql = (
            " SELECT * from query_wztp "
        )
        params = []
        rows = fetch_one(data_sql, params, True)
        print(f"是否跨越查询计划：{rows}")
        return rows
    except Exception as e:
        print(f"是否跨越查询计划出错：{e}")
        return None
```

