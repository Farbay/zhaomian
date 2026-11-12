# publication — 文章发布操作

⚠️ 调用前必须遵守 [SKILL.md](../SKILL.md) 的「知识库修改：先预览，确认后提交」规范：先展示操作预览，用户明确确认后再提交；保存草稿也不例外。

`POST /open/knowledges/{knowledgeId}/nodes/{nodeId}/publication` · 鉴权：Bearer apiKey · 要求知识库管理权限

`knowledgeId` 为知识库 ID（圈子知识库等于 `cid`），`nodeId` 为文章结点 ID，均为 UUIDv7。本接口仅用于文章。

## 请求参数

`action` 为必填 string：`publish` 发布、`unpublish` 下架、`republish` 重新上架。

发布有两种请求格式，不能混用：

| 方式 | Body 字段 | 说明 |
| --- | --- | --- |
| 发布当前草稿 | action、draftHistoryId | `action` 为 `publish`；`draftHistoryId` 为 `get-node.md` 返回的当前草稿 ID，string / UUIDv7 |
| 提交内容并发布 | action、title、icon、bodyMarkdown、parentNodeId | `action` 为 `publish`；其余均为 string，必须完整提交 |

直接发布时，`title` 非空且最多 255 字符，`icon` 最多 32 字符、可为空字符串，`bodyMarkdown` 为非空 Markdown 正文，`parentNodeId` 为同库分组或目录 ID。同层不能重名。发布草稿时，其标题和正文也必须非空。

下架和重新上架只传 `action`。仅已发布文章可下架；仅已有发布内容的已下架文章可重新上架，重新上架不会发布草稿。

## 请求示例

发布当前草稿：

```json
{"action":"publish","draftHistoryId":"019f1000-0000-7000-8000-000000000005"}
```

提交内容并发布：

```json
{"action":"publish","title":"快速开始","icon":"","bodyMarkdown":"# 快速开始\n\n正式内容。","parentNodeId":"019f1000-0000-7000-8000-000000000002"}
```

下架 / 重新上架分别提交：

```json
{"action":"unpublish"}
```

```json
{"action":"republish"}
```

成功返回 `data.nodeId`。发布成功后清空当前草稿引用，文章状态为 `1`；下架后为 `-1`。业务码 `179` 表示草稿已变化，须重新读取详情并确认当前草稿，不直接换用新版本发布。超时后先读详情确认状态与内容，不直接重复提交。
