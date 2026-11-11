# list-articles — 分页获取文章

`GET /open/knowledges/{knowledgeId}/articles` · 鉴权：Bearer apiKey · 要求知识库查看权限

`knowledgeId` 为知识库 ID，圈子知识库等于 `cid`，格式为 UUIDv7。返回当前知识库的文章，包含草稿、已发布和已下架文章，不返回分组或目录。

| Query 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| count | integer | 是 | 每页 1–200 条，建议 10，无默认值 |
| next | string | 否 | 第一页省略或传空字符串；后续使用上一页 `data.page.next` |
| status | integer | 否 | `0` 未发布、`1` 已发布、`-1` 已下架；省略表示全部 |
| hasDraft | boolean | 否 | `true` 有当前草稿、`false` 无当前草稿；省略表示全部 |

优先返回有草稿的文章，再按更新时间、结点 ID 倒序排列。翻页保持筛选条件一致，`data.page.next` 为空字符串时结束，不自行构造游标。

## 响应字段

`data.list` 为文章数组，`data.page` 包含 `count`、`next`；`data.total` 仅第一页返回。

| list[] 字段 | 说明 |
| --- | --- |
| nodeId | 文章结点 ID |
| title、icon | 有草稿时使用草稿标题和图标，否则使用发布内容 |
| status、hasDraft | 发布状态及是否有当前草稿 |
| liveTitle、liveIcon | 发布版本标题、图标，无发布版本时为 null |
| draftTitle、draftIcon | 当前草稿标题、图标，无草稿时为 null |
| parentNodeId | 所属分组或目录 ID |
| path | 从分组到父目录的路径数组，每项为 `nodeId`、`nodeType`、`title` |
| effectiveVisibility | 继承的阅读范围：`1` 所有人、`2` 圈子有效成员、`3` 知识库运营者 |
| author | 作者对象：`uid`、`pid`、`name`、`avatarImageId` |
| firstPublishTime、lastPublishTime | 首次、最近发布时间，毫秒时间戳，`0` 表示尚未发布 |
| createTime、updateTime | 创建、更新时间，毫秒时间戳 |

本接口不返回正文，读取文章内容使用 `get-node.md`。
