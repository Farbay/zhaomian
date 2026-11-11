# list-histories — 分页获取文章版本

`GET /open/knowledges/{knowledgeId}/nodes/{nodeId}/histories` · 鉴权：Bearer apiKey · 要求知识库查看权限

`knowledgeId` 为知识库 ID（圈子知识库等于 `cid`），`nodeId` 为文章结点 ID，均为 UUIDv7。分组、目录没有文章版本。

| Query 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| count | integer | 是 | 每页 1–200 条，建议 10，无默认值 |
| next | string | 否 | 第一页省略或传空字符串；后续使用上一页 `data.page.next` |

按版本创建时间、版本 ID 倒序排列。`data.page.next` 为空字符串时结束。

## 响应字段

`data.list` 为版本摘要数组，`data.page` 包含 `count`、`next`；`data.total` 仅第一页返回。

| list[] 字段 | 说明 |
| --- | --- |
| historyId | 版本 ID，UUIDv7 |
| nodeId | 所属文章结点 ID |
| title、icon | 该版本的标题和图标 |
| savedBy | 保存者对象：`uid`、`pid`、`name`、`avatarImageId` |
| createTime | 版本创建时间，毫秒时间戳 |
| isLive | 是否为当前绑定的发布版本；已下架文章也可保留此标记 |
| isDraft | 是否为当前草稿版本 |

不返回正文。查看指定版本全文使用 `get-history.md`；不能将任意历史版本 ID 直接当作当前 `draftHistoryId` 发布。
