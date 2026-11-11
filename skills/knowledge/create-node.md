# create-node — 创建分组、目录和文章结点

`POST /open/knowledges/{knowledgeId}/nodes` · 鉴权：Bearer apiKey · 要求知识库管理权限

`knowledgeId` 为知识库 ID，圈子知识库使用圈子 `cid`，格式为 UUIDv7。分组、目录和文章统一称为结点，`nodeType` 分别为 `1`、`2`、`3`。

## 请求参数

按结点类型提交对应字段，不混用其他类型字段。身份由 API Key 确定，不提交 `uid`、`authedUid` 或 `pid`。

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| nodeType | integer | 是 | `1` 分组、`2` 目录、`3` 文章 |
| parentNodeId | string | 是 | 分组传空字符串；目录、文章传同一知识库内的分组或目录 ID |
| title | string | 是 | 非空标题，最多 255 字符；同一父结点下不能重名，包括其他文章的草稿标题 |
| visibility | integer | 分组必填 | `1` 所有人、`2` 圈子有效成员、`3` 知识库运营者；目录、文章不传，继承所属分组 |
| summary | string | 目录必填 | 非空简介；分组、文章不传 |
| icon | string | 文章必填 | 最多 32 字符，可传空字符串；分组、目录不传 |
| bodyMarkdown | string | 文章必填 | Markdown 正文；保存草稿可为空字符串，发布时须非空 |
| saveMode | string | 文章必填 | `draft` 保存草稿、`publish` 直接发布；分组、目录不传 |

分组只能位于根层；目录、文章不能直接放在根层。新结点放在父结点下同类型结点的末尾。

## 请求示例

创建分组：

```json
{"nodeType":1,"parentNodeId":"","title":"使用指南","visibility":1}
```

创建目录：

```json
{"nodeType":2,"parentNodeId":"019f1000-0000-7000-8000-000000000001","title":"入门","summary":"开始使用前需要了解的内容"}
```

创建文章草稿：

```json
{"nodeType":3,"parentNodeId":"019f1000-0000-7000-8000-000000000002","title":"快速开始","icon":"","bodyMarkdown":"# 快速开始\n\n欢迎使用。","saveMode":"draft"}
```

## 响应

成功返回 `data.nodeId`，为新结点的 UUIDv7 ID。发布草稿前按 `get-node.md` 读取当前 `draftHistoryId`。创建超时后先查询结点树或文章列表确认结果，不直接重复创建。
