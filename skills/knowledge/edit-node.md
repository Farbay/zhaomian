# edit-node — 编辑分组、目录和文章结点

`PUT /open/knowledges/{knowledgeId}/nodes/{nodeId}` · 鉴权：Bearer apiKey · 要求知识库管理权限

`knowledgeId` 为知识库 ID（圈子知识库等于 `cid`），`nodeId` 为该知识库内的结点 ID，均为 UUIDv7。先按 `get-node.md` 读取结点类型和当前内容，再完整提交该类型的字段；不支持修改结点类型。

## 请求参数

| 结点类型 | 必填字段 | 说明 |
| --- | --- | --- |
| 分组（1） | title、visibility | 非空标题最多 255 字符；阅读范围 `1` 所有人、`2` 圈子有效成员、`3` 知识库运营者 |
| 目录（2） | title、summary、parentNodeId | 非空标题最多 255 字符；简介须非空；父结点为同库分组或目录，不能是自身或自身后代 |
| 文章（3） | title、icon、bodyMarkdown、parentNodeId | 非空标题最多 255 字符；图标最多 32 字符，可为空；正文为 Markdown，可为空；父结点为同库分组或目录 |

所有字段类型均为 string，只有 `visibility` 为 integer。分组不传 `parentNodeId`；只提交对应类型的字段，不提交 `nodeType`、身份字段或发布状态。同层标题不能重名。

文章编辑结果保存为当前草稿；有已发布内容时不会替换线上正文，发布使用 `publication.md`。取当前内容时优先使用 `draftContent`，没有草稿时使用 `liveContent`，保留未修改的字段。`parentNodeId` 的变更立即生效，并继承目标分组阅读范围。

## 请求示例

编辑目录：

```json
{"title":"入门教程","summary":"开始使用的步骤","parentNodeId":"019f1000-0000-7000-8000-000000000001"}
```

编辑文章并保存草稿：

```json
{"title":"快速开始","icon":"","bodyMarkdown":"# 快速开始\n\n更新后的内容。","parentNodeId":"019f1000-0000-7000-8000-000000000002"}
```

成功返回 `data.nodeId`。
