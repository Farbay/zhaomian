# get-nodes — 获取知识库结点树

`GET /open/knowledges/{knowledgeId}/nodes` · 鉴权：Bearer apiKey · 要求知识库查看权限

`knowledgeId` 为必填路径参数，格式为 UUIDv7，圈子知识库等于 `cid`。返回当前知识库下所有分组、目录、文章的树形结构，包含尚未发布的草稿文章。无 Query 参数和请求体。

## 响应字段

`data.knowledgeId` 为知识库 ID，`data.nodes` 为根层分组数组。通过 `children` 递归包含目录和文章；文章的 `children` 为空数组。

| 结点字段 | 说明 |
| --- | --- |
| nodeId | 结点 ID，UUIDv7 |
| parentNodeId | 父结点 ID；根层分组为空字符串 |
| nodeType | `1` 分组、`2` 目录、`3` 文章 |
| title | 结点标题；文章有发布版本时为发布标题，只有草稿时为草稿标题 |
| visibility | 分组的阅读范围：`1` 所有人、`2` 圈子有效成员、`3` 知识库运营者；目录、文章为 `0`，表示继承 |
| effectiveVisibility | 实际生效的所属分组阅读范围，取值 1–3 |
| summary | 仅目录返回，目录简介 |
| icon | 仅文章返回，文章图标 |
| status | 仅文章返回：`0` 未发布、`1` 已发布、`-1` 已下架 |
| hasDraft | 仅文章返回，是否有当前草稿，boolean |
| children | 子结点数组，同层目录在文章之前，同类型按当前顺序排列 |

树不返回正文。查看单个分组、目录或文章结点详情使用 `get-node.md`。
