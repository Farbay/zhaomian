# get-history — 获取指定版本内容

`GET /open/knowledges/{knowledgeId}/nodes/{nodeId}/histories/{historyId}` · 鉴权：Bearer apiKey · 要求知识库查看权限

| 路径参数 | 说明 |
| --- | --- |
| knowledgeId | 知识库 ID，圈子知识库等于 `cid`，UUIDv7 |
| nodeId | 当前知识库内的文章结点 ID，UUIDv7 |
| historyId | 该文章的版本 ID，UUIDv7，从 `list-histories.md` 获取 |

无 Query 参数和请求体。版本必须属于指定文章，文章必须属于指定知识库；不能跨文章读取版本。

## 响应字段

| data 字段 | 说明 |
| --- | --- |
| historyId、nodeId | 版本 ID、文章结点 ID |
| title、icon | 该版本的标题和图标 |
| bodyMarkdown | 该版本的 Markdown 正文 |
| savedBy | 保存者对象：`uid`、`pid`、`name`、`avatarImageId` |
| createTime | 版本创建时间，毫秒时间戳 |
| isLive | 是否为当前绑定的发布版本；不代表文章当前已上架 |
| isDraft | 是否为当前草稿版本 |

本接口只读取内容，不恢复或发布版本。若需使用历史内容，须明确用户意图，再按 `save-draft.md` 保存或按 `publication.md` 提交内容并发布。
