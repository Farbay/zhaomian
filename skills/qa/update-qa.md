# update-qa — 更新问答

`PUT /open/user/qas/{qaId}` · 鉴权：Bearer apiKey

## 请求

### Path

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `qaId` | number | 是 | 问答 ID |

### Body

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `qaType` | number | 是 | `0` 普通问答、`1` 雷区 |
| `question` | string | 是 | 问题，最长 255 字 |
| `answer` | string | 是 | 回答，最长 255 字 |
| `summary` | string | 是 | 摘要 / 简短回答，最长 255 字，允许空字符串 |

更新可以调整 `qaType`；body 不需要传 `qaId`，以路径里的为准。更新是覆盖式的，四个字段都要提交。

## 注意

1. 先用 `list-qas.md` 或上下文拿到 `qaId`。
2. 与用户确认问题、回答、类型、摘要，然后提交完整字段。

## 示例

```bash
curl -X PUT "${FARBAY_OPEN_HOST}/open/user/qas/${QA_ID}" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"qaType": 0, "question": "你的爱好是什么？", "answer": "这是更新后的回答", "summary": ""}'
```
