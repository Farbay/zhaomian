# save-draft — 保存文章草稿

⚠️ 调用前必须遵守 [SKILL.md](../SKILL.md) 的「知识库修改：先预览，确认后提交」规范：先展示操作预览，用户明确确认后再提交；保存草稿也不例外。

`PUT /open/knowledges/{knowledgeId}/nodes/{nodeId}/draft` · 鉴权：Bearer apiKey · 要求知识库管理权限

`knowledgeId` 为知识库 ID（圈子知识库等于 `cid`），`nodeId` 为文章结点 ID，均为 UUIDv7。分组、目录不能保存文章草稿。

| Body 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| title | string | 是 | 非空标题，最多 255 字符，同层不能重名 |
| icon | string | 是 | 最多 32 字符，可为空字符串 |
| bodyMarkdown | string | 是 | Markdown 正文，可为空字符串 |
| parentNodeId | string | 是 | 同一知识库内的分组或目录 ID，不能传空字符串 |

完整提交四个字段，不提交身份或版本 ID。编辑已有文章前按 `get-node.md` 读取当前草稿或发布内容，保留未修改的字段。有线上内容时只更新草稿，不替换线上正文；位置变更立即生效。

```json
{"title":"快速开始","icon":"","bodyMarkdown":"# 快速开始\n\n待完善的正文。","parentNodeId":"019f1000-0000-7000-8000-000000000002"}
```

成功返回 `data.nodeId`。保存草稿不等于发布；仅当用户已明确确认发布时，才按 `get-node.md` 读取当前 `draftHistoryId` 并按 `publication.md` 发布。
