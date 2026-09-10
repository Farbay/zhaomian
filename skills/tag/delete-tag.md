# delete-tag — 删除标签

`DELETE /open/user/tags/{tagId}` · 鉴权：Bearer apiKey

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `tagId` | number | 是 | 标签 ID |

## 注意

1. 先用 `list-tags.md` 或上下文拿到 `tagId`，向用户确认要删除的标签内容。
2. 用户确认后调用本接口，成功后明确说明已删除。
