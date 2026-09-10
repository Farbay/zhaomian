# delete-social — 删除社媒账号

`DELETE /open/user/socials/{platformCode}` · 鉴权：Bearer apiKey

解绑当前用户某个社媒平台的账号。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `platformCode` | string | 是 | 平台编码，见 `upsert-social.md` 平台表 |

## 注意

1. 先用 `list-socials.md` 或上下文确认要解绑的平台与账号。
2. 用户确认后调用本接口，成功后明确说明已解绑；返回「用户社媒不存在」时说明该平台本来就没有绑定。
