# RESTful API 设计规范
## 目录
* [关于](#guanyu)
* [概要](#gaiyao)
  * [协议](#xieyi)
  * [域名](#yuming)
  * [版本](#banben)
  * [请求与返回](#qingqiuyufanhui)
* [路径](#lujing)
* [动词](#dongci)
* [参数](#canshu)
* [认证](#renzheng)
* [返回](#fanhui)
  * [状态码](#zhuangtaima)
  * [客户端错误](#kehuduancuowu)
  * [返回结果](#fanhuijieguo)
  * [元信息](#yuanxinxi)
* [重定向](#chongdingxiang)
* [超媒体](#chaomeiti)

## 关于
本文档描述了发布系统的接口实现规范。

本文使用不同的程度副词对陈述做出确切的限定，程度副词的含义如下。

* **总是**：始终需要遵循的；
* **必须**：始终需要遵循的；
* **应当**：强烈推荐的；
* **可以**：允许的；
* **可能**：可选的。

## 概要
### 协议

API 与客户端总是使用 HTTPS 协议进行通信。

### 域名

API 应当使用专有子域名。

```
https://api.example.com
```

如果 API 相对简单，可以放到主域名下，通过名为 api 的路径进行访问。

```
https://example.com/api
```

### 版本
API 的版本号放在 URL 中。

```
https://api.example.com/v1/
```

### 请求与返回

请求数据与返回数据均为 JSON 对象（或数组），数据使用 UTF-8 字符集编码。

```
Content-Type: application/json; charset=utf-8
```

数据中的时间值遵循 [ISO 8601 格式](https://www.wikiwand.com/en/ISO_8601)

```
YYYY-MM-DDTHH:MM:SSZ
```

## 路径
在 RESTful 架构中，每个路径代表一种资源。

对根路径的请求将返回完整的可用 API 列表。请阅读下面的“超媒体”一节。

路径中不能有动词，只能有名词。名词使用复数。举例来说，有一个 API 提供图书馆中的图书信息，它的路径必须被设计成下面这样。

```
https://api.example.com/v1/books
```

## 动词
对于资源的具体操作类型，由 HTTP 动词表示。

常用的动词如下。

| 动词      | 描述                      |
| ------- | ----------------------- |
| GET     | 从服务器获取资源（一项或多项）         |
| POST    | 在服务器新建一项资源              |
| PUT     | 在服务器覆盖式更新资源             |
| PATCH   | 在服务器增量式更新资源             |
| DELETE  | 从服务器删除资源                |
| HEAD    | 获取资源的元数据                |
| OPTIONS | 获取信息，关于资源的哪些属性是客户端可以改变的 |

下面是一些示例。

| 动词     | 路径                   | 描述          |
| ------ | -------------------- | ----------- |
| GET    | /books               | 获取所有图书      |
| POST   | /books               | 添加一本图书      |
| GET    | /books/ID            | 获取指定图书      |
| PUT    | /books/ID            | 覆盖式更新指定图书   |
| PATCH  | /books/ID            | 增量式更新指定图书   |
| DELETE | /books/ID            | 删除指定图书      |
| GET    | /books/ID/reviews    | 获取指定图书的所有评论 |
| DELETE | /books/ID/reviews/ID | 删除指定图书的指定评论 |

## 参数
API 可能要求额外的参数。

对于 GET 请求，参数由 HTTP query string 提供，格式为简单的字符串键值对。

下面是一些常见的参数示例。

```
?limit=10: 指定返回记录的数量
?offset=10: 指定返回记录的开始位置
?page=2&per_page=100: 指定第几页，以及每页的记录数
```

对于 POST, PUT, PATCH 以及 DELETE 请求，参数由 HTTP request body 提供，格式为 JSON 对象（或数组）。

## 认证
身份认证总是使用 [OAuth 2.0](https://oauth.net/2/) 协议。

## 返回
### 状态码

服务器向客户端返回 [符合语义的状态码](https://www.wikiwand.com/en/List_of_HTTP_status_codes) 。

### 客户端错误

客户端请求的参数可能存在错误。服务器按照下面的顺序对参数进行检查，并返回发现的第一条错误。

一. 无效的 JSON 格式，返回 `400 Bad Request`。

```
HTTP/1.1 400 Bad Request
Content-Length: 35

{"message":"Problems parsing JSON"}
```

二. 错误的 JSON 类型，返回 `400 Bad Request`。

```
HTTP/1.1 400 Bad Request
Content-Length: 40

{"message":"Body should be a JSON object"}
```

三.  JSON 数据中包含无效字段，返回 `422 Unprocessable Entity`。

```
HTTP/1.1 422 Unprocessable Entity
Content-Length: 149

{
  "message": "Validation Failed",
  "errors": [
    {
      "resource": "Issue",
      "field": "title",
      "code": "missing_field"
    }
  ]
}
```

此类错误中，`code` 字段指示了错误类型。下面是完整的 `code` 列表。

| 字段               | 描述                                       |
| ---------------- | ---------------------------------------- |
| `missing`        | 表示资源不存在                                  |
| `missing_fields` | 表示资源缺失必要字段                               |
| `invalid`        | 表示字段格式不正确                                |
| `already_exists` | 表示字段的值已经被其他资源占用，一般发生在字段值要求全局唯一的场景        |
| `custom`         | 其他类型的错误。这种类型的错误需要包含一个 `documentation_url` 字段，请阅读下面的“超媒体”一节 |

### 返回结果

返回的响应主体即是结果对象，不做任何嵌套封装。

坏的:

```
{
  "data": [
    {
      "id": 1,
      "title": "book1"
    },
    {
      "id": 2,
      "title": "book2"
    }
  ]
}
```

好的:

```
[
  {
    "id": 1,
    "title": "book1"
  },
  {
    "id": 2,
    "title": "book2"
  }
]
```

对于成功的操作，针对不同操作类型，服务器向客户端返回的结果必须合乎以下规范。

| 动词     | 路径        | 描述         |
| ------ | --------- | ---------- |
| GET    | /books    | 返回图书列表（数组） |
| GET    | /books/ID | 返回单个图书对象   |
| POST   | /books    | 返回新生成的图书对象 |
| PUT    | /books/ID | 返回完整的图书对象  |
| PATCH  | /books/ID | 返回完整的图书对象  |
| DELETE | /books/ID | 返回一个空对象    |

### 元信息

返回结果可能包含元信息。元信息总是被放置在响应首部中。

一种常见的元信息是数据列表的分页信息。

```
HTTP/1.1 200
X-Pagination-Count: 100
X-Pagination-Page: 6
X-Pagination-Limit: 2
Content-Type: application/json

[
  {
    "id": 11,
    "title": "book11"
  },
  {
    "id": 12,
    "title": "book12"
  }
]
```

## 重定向
客户端应当假设所有请求均有可能被重定向。重定向不能被当作错误进行处理，客户端必须对重定向进行跟踪。

重定向返回的 HTTP headers 中包含一个 `Location` 字段，该字段指示了资源的更新地址。

| 状态码      | 描述       |
| -------- | -------- |
| 301      | 永久重定向    |
| 302, 307 | 临时重定向    |
| *        | 其他重定向状态码 |

## 超媒体
API 的返回结果中应当包含额外的链接。客户端不必查询文档，也知道下一步应该做什么。超媒体链接存在于以下场景。

一. 请求根路径，返回一个包含所有可用 API 路径的列表。

```
{
  "books_url": "https://api.example.com/books",
  "users_url": "https://api.example.com/users"
}
```

二. 发生客户端错误时，返回的对象中包含 `documentation_url` 字段，指示帮助文档的位置。

```
{
  "message": "Validation Failed",
  "documentation_url": "https://api.example.com/docs/xxx",
  "errors": [
    {
      "resource": "Issue",
      "field": "title",
      "code": "custom"
    }
  ]
}
```

三. 正常返回中可以包含额外的帮助性链接。