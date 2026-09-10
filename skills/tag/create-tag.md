# create-tag — 创建标签

`POST /open/user/tags` · 鉴权：Bearer apiKey

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `tagType` | number | 是 | `0` 普通、`1` MBTI、`2` 家乡、`3` 资源、`4` 技能 |
| `content` | string | 是 | 标签内容，1-12 字 |
| `description` | string | 是 | 标签描述，最长 255 字，允许空字符串 |
| `sort` | number | 是 | 排序值，不能小于 0，越大越靠前 |

## 响应

| 字段 | 描述 |
|------|------|
| `data.tagId` | 新建标签 ID |

## 注意

- 同一用户下 `content` 不能重复，重复会返回「标签内容已存在」；创建前可先读 `list-tags.md`，或依赖服务端报错。
- 用户没给 `description` / `sort` 时：`description` 传空字符串，`sort` 传 `0`（新标签排在最后）。
- 创建成功后记录返回的 `tagId`，供后续更新 / 删除使用。
