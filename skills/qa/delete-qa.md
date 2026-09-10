# delete-qa — 删除问答

`DELETE /open/user/qas/{qaId}` · 鉴权：Bearer apiKey

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `qaId` | number | 是 | 问答 ID |

## 注意

1. 先用 `list-qas.md` 或上下文拿到 `qaId`，向用户确认要删除的问题。
2. 用户确认后调用本接口，成功后明确说明已删除。
