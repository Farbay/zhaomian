# delete-draft — 删除文章草稿

`DELETE /open/knowledges/{knowledgeId}/nodes/{nodeId}/draft` · 鉴权：Bearer apiKey · 要求知识库管理权限

| 路径参数 | 说明 |
| --- | --- |
| knowledgeId | 知识库 ID，圈子知识库等于 `cid`，UUIDv7 |
| nodeId | 当前知识库内的文章结点 ID，UUIDv7 |

无请求体。本接口仅用于文章：已有发布内容时仅清除当前草稿，保留发布内容和当前发布状态；从未发布、没有发布内容时，删除草稿会同时删除整个文章结点。操作前先按 `get-node.md` 确认目标及是否存在发布内容。

成功返回 `data.nodeId` 和 boolean `data.deleted`：`true` 表示文章结点也被删除，`false` 表示只清除了当前草稿。没有草稿时返回业务码 `177`。超时后先重新读取详情，避免误删之后保存的新草稿。
