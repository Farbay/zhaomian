# delete-node — 删除分组、目录和文章结点

`DELETE /open/knowledges/{knowledgeId}/nodes/{nodeId}` · 鉴权：Bearer apiKey · 要求知识库管理权限

| 路径参数 | 说明 |
| --- | --- |
| knowledgeId | 知识库 ID，圈子知识库等于 `cid`，UUIDv7 |
| nodeId | 当前知识库内的分组、目录或文章结点 ID，UUIDv7 |

无请求体。分组、目录存在子结点时不能删除，不递归删除子内容。已发布文章须先按 `publication.md` 下架，再删除文章结点。

成功返回 `data.nodeId` 和 `data.deleted: true`。删除文章结点与删除文章草稿是不同操作；只删除草稿使用 `delete-draft.md`。
