# list-socials — 获取用户所有社媒账号

`GET /open/user/socials` · 鉴权：Bearer apiKey

返回当前用户已填写的社媒账号；没有记录时 `data` 为 `[]`。

## 响应

| 字段 | 类型 | 描述 |
|------|------|------|
| `platformCode` | string | 社媒平台编码 |
| `content` | string | 社媒账号内容 |
| `createTime` | number | 创建时间，毫秒时间戳 |

## 注意

- 展示平台中文名 + 账号内容；平台编码到中文名的映射见 `upsert-social.md` 的平台表。
- 没有社媒账号时回复「你还没有绑定社媒账号，要不要绑定一个？」。
