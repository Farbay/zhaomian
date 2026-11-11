# move-node — 移动目录和文章结点

`PUT /open/knowledges/{knowledgeId}/nodes/{nodeId}/move` · 鉴权：Bearer apiKey · 要求知识库管理权限

`knowledgeId` 为知识库 ID（圈子知识库等于 `cid`），`nodeId` 为待移动的目录或文章结点 ID，均为 UUIDv7。本接口不移动分组。

| Body 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| parentNodeId | string | 是 | 目标父结点，须为同一知识库内的分组或目录 ID，不能传空字符串 |
| beforeNodeId | string | 是 | 插到哪个同类型结点之前；空字符串表示目标父结点下同类型末尾 |

参照结点须在目标父结点下、类型相同，且不能是当前结点。目录不能移入自身或自身后代；目标层存在同名标题或冲突的草稿标题时拒绝移动。同父结点下也可用此接口调整顺序。

移动目录时其子内容一起移动；位置和继承的分组阅读范围立即生效。不修改文章正文、草稿或发布状态，不生成内容版本。

```json
{"parentNodeId":"019f1000-0000-7000-8000-000000000002","beforeNodeId":""}
```

成功返回 `data.nodeId`。
