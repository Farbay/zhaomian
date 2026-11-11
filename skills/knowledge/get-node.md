# get-node — 获取分组、目录和文章结点详情

`GET /open/knowledges/{knowledgeId}/nodes/{nodeId}` · 鉴权：Bearer apiKey · 要求知识库查看权限

`knowledgeId` 为知识库 ID（圈子知识库等于 `cid`），`nodeId` 为该知识库内的分组、目录或文章结点 ID，均为 UUIDv7。无 Query 参数和请求体。

## 分组、目录详情

| data 字段 | 说明 |
| --- | --- |
| knowledgeId、nodeId | 知识库 ID、结点 ID |
| nodeType | `1` 分组、`2` 目录 |
| parentNodeId | 父结点 ID；分组为空字符串 |
| title | 结点标题 |
| visibility | 分组阅读范围：`1` 所有人、`2` 圈子有效成员、`3` 知识库运营者；目录为 `0`，表示继承 |
| effectiveVisibility | 实际生效的所属分组阅读范围，取值 1–3 |
| summary | 目录简介；分组为空字符串 |
| path | 从分组到父目录的路径数组，每项为 `nodeId`、`nodeType`、`title`；分组为空数组 |
| createTime、updateTime | 创建、更新时间，毫秒时间戳 |

## 文章详情

| data 字段 | 说明 |
| --- | --- |
| knowledgeId、nodeId、nodeType | 知识库 ID、结点 ID、类型（`3`） |
| status | `0` 未发布、`1` 已发布、`-1` 已下架 |
| hasDraft | 是否有当前草稿，boolean |
| author | 作者对象：`uid`、`pid`、`name`、`avatarImageId` |
| parentNodeId、path | 所属分组或目录 ID、从分组到父目录的路径 |
| effectiveVisibility | 继承的阅读范围：`1` 所有人、`2` 圈子有效成员、`3` 知识库运营者 |
| liveHistoryId、draftHistoryId | 发布版本 ID、当前草稿版本 ID；没有时为空字符串 |
| liveContent、draftContent | 发布内容、当前草稿内容；没有时为 null |
| firstPublishTime、lastPublishTime | 首次、最近发布时间，毫秒时间戳，`0` 表示尚未发布 |
| createTime、updateTime | 创建、更新时间，毫秒时间戳 |

内容对象均含 `historyId`、`title`、`icon`、`bodyMarkdown`、`savedBy`、`createTime`；保存者对象字段与 `author` 相同。文章标题、图标和正文从内容对象读取，不从详情顶层猜测。

编辑时优先取 `draftContent`，无草稿时取 `liveContent`。发布现有草稿必须使用本次详情返回的 `draftHistoryId`。读取文章草稿不表示其已经发布。
