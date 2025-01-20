<a name="readme-top"></a>

[![Static Badge](https://img.shields.io/badge/status-beta-blue?style=for-the-badge&label=status)](https://github.com/thijsmie/pocketbase-async)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](https://github.com/thijsmie/pocketbase-async/blob/main/LICENSE.txt)
[![Checks status](https://img.shields.io/github/actions/workflow/status/thijsmie/pocketbase-async/check.yml?style=for-the-badge&label=Checks)](https://github.com/thijsmie/pocketbase-async/actions)
[![Coverage](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/thijsmie/a41c81ee9f5d3944d2f9946c3eae4aae/raw/coverage.json)](https://github.com/thijsmie/pocketbase-async/actions)
[![GitHub Release](https://img.shields.io/github/v/release/thijsmie/pocketbase-async?style=for-the-badge)](https://github.com/thijsmie/pocketbase-async/releases)

<div align="center">
  <hr/>
  <a href="https://github.com/thijsmie/pocketbase-async">
    <img src="https://raw.githubusercontent.com/thijsmie/pocketbase-async/main/images/logo.svg" alt="Logo" width="80" height="80">
  </a>
  <h3 align="center">pocketbase-async</h3>

  <p align="center">
    An async Python 3.11+ PocketBase SDK
    <hr/>
    <a href="https://github.com/thijsmie/pocketbase-async"><strong>Repository</strong></a>
    <br />
    <a href="https://github.com/thijsmie/pocketbase-async/issues">Report Bug</a>
    ·
    <a href="https://github.com/thijsmie/pocketbase-async/issues">Request Feature</a>
  </p>
</div>

## About

PocketBase is an amazing tool to have in your developers backpack for a quick backend for your project. I found it pleasant to work with in Python but Vaphes existing Python SDK is in sync code while most of my application development is async these days. I started with a fork of Vaphes' SDK and tried to add async support but I gave up quite quickly and just started from scratch. You see the results here.

## Installation

Note that this package is compatible with Python 3.11 and up. You can install this package directly from PyPi:
```bash
pip install pocketbase-async
# or if you use poetry
poetry add pocketbase-async
```

## Usage

The API is mostly the same as the official JS SDK and the Vaphes Python SDK, with some exceptions. Authentication methods are namespaced under an extra `.auth`. There are some examples to help you along. More info to come.

- [View the examples](https://github.com/thijsmie/pocketbase-async/tree/main/examples)

## Roadmap

See the [project board](https://github.com/thijsmie/pocketbase-async/projects?query=is%3Aopen) for the list of planned work. See the [open issues](https://github.com/thijsmie/pocketbase-async/issues) for a full list of proposed features (and known issues).

## Contributing

Contributions are welcome and appreciated, be it typo-fix, feature or extensive rework. I recommend you to open an issue if you plan to spend significant effort on making a pull request, to avoid dual work or getting your work rejected if it really doesn't fit this project.

Don't forget to give the project a star! Thanks again!

1. Fork the project
2. Create your feature branch (`git checkout -b feat/some-nice-feature`)
3. Commit your changes (`git commit -m 'feat: Add some AmazingFeature'`)
4. Push to the pranch (`git push -u origin feat/some-nice-feature`)
5. Open a pull request

## License

Distributed under the MIT License. See `LICENSE.txt` for more information.

## Attributions

The `pocketbase-async` package was inspired and guided in implementation by several other projects:

- The official js sdk: https://github.com/pocketbase/js-sdk
- The Python SDK by Vaphes: https://github.com/vaphes/pocketbase
- The official documentation: https://pocketbase.io/docs/

Furthermore, a lot of the API tests were adapted from Vaphes' work (licensed MIT).

## Doc
# PocketBase Python SDK 使用手册

## 目录
- [安装](#安装)
- [基本用法](#基本用法)
- [身份认证](#身份认证)
- [记录操作](#记录操作)
- [文件管理](#文件管理)
- [实时订阅](#实时订阅)
- [集合管理](#集合管理)
- [备份管理](#备份管理)
- [错误处理](#错误处理)

## 安装

```bash
pip install pocketbase
```

## 基本用法

首先需要创建一个 PocketBase 客户端实例:

```python
from pocketbase import PocketBase

client = PocketBase('http://127.0.0.1:8090')  # 替换为你的 PocketBase 服务器地址
```

## 身份认证

### 管理员认证

```python
# 管理员登录
admin_auth = await client.admins.auth.with_password("admin@example.com", "password123")

# 刷新管理员认证令牌
await client.admins.auth.refresh()
```

### 用户认证

```python
# 用户登录
auth_data = await client.collection("users").auth.with_password(
    "user@example.com",
    "password123"
)

# 刷新用户认证令牌
await client.collection("users").auth.refresh()

# OAuth2 认证
oauth2_auth = await client.collection("users").auth.with_OAuth2({
    "provider": "google",
    "code": "auth_code",
    "codeVerifier": "code_verifier",
    "redirectUrl": "redirect_url"
})

# 获取认证方法
auth_methods = await client.collection("users").auth.methods()
```

## 记录操作

### 基本 CRUD 操作

```python
# 创建记录
record = await client.collection("posts").create({
    "title": "Hello world!",
    "content": "This is my first post"
})

# 获取单条记录
record = await client.collection("posts").get_one("RECORD_ID")

# 获取记录列表
records = await client.collection("posts").get_list(
    page=1,
    per_page=20,
    options={
        "sort": "-created",
        "filter": "created >= '2024-01-01'"
    }
)

# 获取所有记录
all_records = await client.collection("posts").get_full_list(options={
    "sort": "-created",
    "filter": "status = true"
})

# 更新记录
updated = await client.collection("posts").update("RECORD_ID", {
    "title": "Updated title"
})

# 删除记录
await client.collection("posts").delete("RECORD_ID")
```

### 高级查询

```python
# 使用过滤器
filtered = await client.collection("posts").get_list(1, 20, {
    "filter": "title ~ 'welcome' && created >= '2024-01-01'"
})

# 使用排序
sorted_records = await client.collection("posts").get_list(1, 20, {
    "sort": "-created,title"
})

# 获取第一条匹配记录
first = await client.collection("posts").get_first({
    "filter": "status = true"
})
```

## 文件管理

```python
# 上传文件
record = await client.collection("posts").create({
    "title": "Post with image",
    "image": FileUpload(("image.jpg", open("image.jpg", "rb")))
})

# 获取文件 URL
file_url = client.files.get_url("collection_id", "record_id", "filename.jpg")

# 下载文件
file_content = await client.files.download_file(
    "collection_id",
    "record_id",
    "filename.jpg",
    options={"thumb": "100x100"}
)
```

## 实时订阅

```python
# 订阅单条记录的变更
async def on_record_change(event):
    print("Record changed:", event)

unsubscribe = await client.collection("posts").subscribe(
    on_record_change,
    "RECORD_ID"
)

# 订阅整个集合的变更
unsubscribe_all = await client.collection("posts").subscribe_all(on_record_change)

# 取消订阅
await unsubscribe()
await unsubscribe_all()

# 关闭实时连接
await client.realtime.close()
```

## 集合管理

```python
# 创建集合
collection = await client.collections.create({
    "name": "posts",
    "type": "base",
    "schema": [
        {
            "name": "title",
            "type": "text",
            "required": True
        }
    ]
})

# 获取集合列表
collections = await client.collections.get_full_list()

# 更新集合
updated = await client.collections.update("COLLECTION_ID", {
    "name": "new_name"
})

# 删除集合
await client.collections.delete("COLLECTION_ID")

# 导入集合
await client.collections.import_collections([
    {
        "name": "posts",
        "type": "base",
        "schema": [...]
    }
], delete_missing=False)
```

## 备份管理

```python
# 创建备份
await client.backups.create("backup_name.zip")

# 获取备份列表
backups = await client.backups.get_full_list()

# 下载备份
backup_content = await client.backups.download("backup_name.zip")

# 还原备份
await client.backups.restore("backup_name.zip")

# 删除备份
await client.backups.delete("backup_name.zip")
```

## 错误处理

库中定义了以下异常类型：
- `PocketBaseError`: 基础异常类
- `PocketBaseBadRequestError`: 400 错误
- `PocketBaseUnauthorizedError`: 401 错误
- `PocketBaseForbiddenError`: 403 错误
- `PocketBaseNotFoundError`: 404 错误
- `PocketBaseServerError`: 500 错误

使用 try-except 处理异常:

```python
from pocketbase import PocketBaseError, PocketBaseNotFoundError

try:
    record = await client.collection("posts").get_one("NON_EXISTENT_ID")
except PocketBaseNotFoundError:
    print("记录不存在")
except PocketBaseError as e:
    print(f"发生错误: {e.status} - {e.data}")
```

## 高级用法

### 自定义请求处理

可以通过重写 `before_send` 和 `after_send` 方法来自定义请求处理：

```python
class MyPocketBase(PocketBase):
    async def before_send(self, request):
        # 在发送请求前修改请求
        request.headers["Custom-Header"] = "value"
        return request

    async def after_send(self, response):
        # 在收到响应后处理响应
        print(f"Response status: {response.status_code}")
        return response

client = MyPocketBase("http://127.0.0.1:8090")
```

### 设置自定义请求头

```python
class MyPocketBase(PocketBase):
    def headers(self) -> dict[str, str]:
        headers = super().headers()
        headers["Custom-Header"] = "value"
        return headers

client = MyPocketBase("http://127.0.0.1:8090")
```

记住，这个库是异步的，所有的操作都需要使用 `async/await` 语法。确保在异步环境中使用这些方法。


<hr/>
<a href="#readme-top">:arrow_up_small: Back to top</a>
