# list-qas — 获取用户所有问答

`GET /open/user/qas` · 鉴权：Bearer apiKey

返回当前用户全部未删除问答；没有问答时 `data` 为 `[]`。结果已按类型排好序。

## 响应

| 字段 | 类型 | 描述 |
|------|------|------|
| `qaId` | number | 问答 ID |
| `qaType` | number | 问答类型：`0` 普通问答、`1` 雷区 |
| `question` | string | 问题内容 |
| `answer` | string | 回答内容 |
| `summary` | string | 摘要 / 简短回答；未填写时为空字符串 |
| `createTime` | number | 创建时间，毫秒时间戳 |

## 注意

- 按类型分组展示，标注「普通问答 / 雷区」，展示问题与回答；`summary` 非空时一并展示。
- 用编号方便用户选择后续更新或删除。
- 没有问答时回复「你还没有添加问答，要不要加一个？」。
