# list-tags — 获取用户所有标签

`GET /open/user/tags` · 鉴权：Bearer apiKey

返回当前用户全部未删除标签；没有标签时 `data` 为 `[]`。结果已按类型排好序。

## 响应

| 字段 | 类型 | 描述 |
|------|------|------|
| `tagId` | number | 标签 ID |
| `content` | string | 标签内容 |
| `description` | string | 标签描述；未填写时为空字符串 |
| `tagType` | number | 标签类型：`0` 普通、`1` MBTI、`2` 家乡、`3` 资源、`4` 技能 |
| `createTime` | number | 创建时间，毫秒时间戳 |

## 注意

- 按 `tagType` 分组展示，组内用编号方便用户选择后续更新或删除。
- 没有标签时回复「你还没有添加标签，要不要加一个？」。
