# update-tag — 更新标签

`PUT /open/user/tags/{tagId}` · 鉴权：Bearer apiKey

## 请求

### Path

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `tagId` | number | 是 | 标签 ID |

### Body

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `content` | string | 是 | 标签内容，1-12 字 |
| `description` | string | 是 | 标签描述，最长 255 字，允许空字符串 |
| `sort` | number | 是 | 排序值，不能小于 0，越大越靠前 |

`tagType` 不可修改；body 不需要传 `tagId`，以路径里的为准。

## 注意

1. 先用 `list-tags.md` 或上下文拿到 `tagId`。
2. 与用户确认新的内容、描述、排序值，然后提交完整字段。
3. `content` 与其他标签重复时返回「标签内容已存在」，需换一个内容。

## 示例

```bash
curl -X PUT "${FARBAY_OPEN_HOST}/open/user/tags/${TAG_ID}" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"content": "INTP", "description": "偏理性分析与系统思考", "sort": 10}'
```
