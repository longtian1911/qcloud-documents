## 功能描述

COS 支持为已存在的对象设置标签。PUT Object tagging 接口通过为对象添加键值对作为对象标签，可以协助您分组管理已有的对象资源，详情请参见 [对象标签概述](https://cloud.tencent.com/document/product/436/42993)。

如您使用子账号调用此项接口，请确保您已经在主账号处获取了`PUT Object tagging`这个接口的权限。

> !目前对象标签功能最多支持一个对象下设置10个不同的标签，如果超出设置上限，COS 将覆盖已有的对象标签为新的对象标签。

## 请求

#### 请求示例

```http
PUT /<ObjectKey>?tagging&VersionId=VersionId HTTP 1.1
Host:<BucketName-APPID>.cos.<Region>.myqcloud.com
Date: GMT Date
Authorization: Auth String
```

> ?
> - Authorization: Auth String（详情请参见 [请求签名](https://cloud.tencent.com/document/product/436/7778) 文档）。
> - 如果您的存储桶开启了版本控制，并且需要对指定版本的对象添加标签，可以在发起请求时携带 VersionId 参数，对象标签将添加在指定的对象版本下。

#### 请求头

此接口仅使用公共请求头部，详情请参见 [公共请求头部](https://cloud.tencent.com/document/product/436/7728) 文档。

#### 请求体

该请求需要设置如下标签集合：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Tagging>
  <TagSet>
    <Tag>
      <Key>string</Key>
      <Value>string</Value>
    </Tag>
  </TagSet>
</Tagging>
```

具体的数据描述如下：

| 节点名称（关键字） | 父节点             | 描述                                                         | 类型       | 是否必选 |
| ------------------ | ------------------ | ------------------------------------------------------------ | ---------- | -------- |
| Tagging            | 无                 | 标签集合                                                     | Container  | 是       |
| TagSet             | Tagging            | 标签集合                                                     | Container  | 是       |
| Tag                | Tagging.TagSet     | 标签集合，最多支持10个标签                                 | Containers | 是       |
| Key                | Tagging.TagSet.Tag | 标签键，长度不超过128字节，支持英文字母、数字、空格、加号、减号、下划线、等号、点号、冒号、斜线 | String     | 是       |
| Value              | Tagging.TagSet.Tag | 标签值，长度不超过256字节，支持英文字母、数字、空格、加号、减号、下划线、等号、点号、冒号、斜线 | String     | 是       |

## 响应

#### 响应头

此接口仅返回公共响应头部，详情请参见 [公共响应头部](https://cloud.tencent.com/document/product/436/7729) 文档。

#### 响应体

该请求响应体为空。

#### 错误码

以下描述此请求可能会发生的一些特殊的且常见的错误情况：

| 错误码                | 描述                                                         | HTTP 状态码     |
| --------------------- | ------------------------------------------------------------ | --------------- |
| SignatureDoesNotMatch | 提供的签名不符合规则，返回该错误码                           | 403 Forbidden   |
| NoSuchObject          | 如果对象不存在，则无法添加对象标签，将返回该错误码           | 404 Not Found   |
| MalformedXML          | XML 格式不合法，请跟 Restful API 文档仔细比对                | 400 Bad Request |
| BadRequest            | 超过了一个对象允许设置标签数量的最大值，目前最多支持10个标签 | 400 Bad Request |
| InvalidTag            | Tag 的 key 和 value 中包含了保留字符串 `cos:`或者 `Project`。 | 400 Bad Request |

## 实际案例

#### 请求

如下请求向存储桶`examplebucket-1250000000`中的对象`exampleobject.txt`写入了`{age:18}`和`{name:xiaoming}`两个标签。COS 配置标签成功并返回204成功请求。

```shell
PUT /exampleobject.txt?tagging HTTP/1.1
User-Agent: curl/7.29.0
Accept: */*
Host: examplebucket-1250000000.cos.ap-beijing.myqcloud.com
Authorization: Auth String
Content-Length: 127
Content-MD5:MD5 String
Content-Type: application/xml

<Tagging>
    <TagSet>
        <Tag>
            <Key>age</Key>
            <Value>18</Value>
        </Tag>
        <Tag>
            <Key>name</Key>
            <Value>xiaoming</Value>
        </Tag>
    </TagSet>
</Tagging>
```

#### 响应

```shell
HTTP/1.1 204 No Content
Content-Type: application/xml
Content-Length: 0
Connection: keep-alive
Date: Fri, 19 Jan 2020 11:40:22 GMT
Server: tencent-cos
x-cos-request-id: NWE2MWQ5MjZfMTBhYzM1MGFfMTA5ODVfMTVj****
```
