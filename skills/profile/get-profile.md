# get-profile — 获取个人名片

`GET /open/user/profile` · 鉴权：Bearer apiKey

返回当前用户的默认名片；没有有效名片时 `data` 为 `null`。

## 响应

| 字段 | 类型 | 描述 |
|------|------|------|
| `uid` | string | 账号 UID |
| `pid` | string | 名片 PID，报名活动等接口的关键参数 |
| `nickname` | string | 名片昵称 |
| `gender` | number | 性别：`0` 未知、`1` 男性、`2` 女性 |
| `birthday` | string | 生日，`YYYY-MM-DD`；未填写时为空字符串 |
| `avatarImageId` | string | 头像图片 ID |
| `bio` | string | 个人简介；未填写时为空字符串 |
| `identity` | string | 身份信息，如「远湾产品经理」 |
| `createTime` | number | 创建时间，毫秒时间戳 |

## 注意

- 默认展示昵称、身份、简介、创建时间。
- 默认不展示 `pid`、生日、性别，也不要就这些字段做任何说明或引导，静默省略即可。
- 用户明确要求时才展示对应字段（性别转中文）；同一对话里用户要过的字段，之后按其要求继续展示，不用反复确认；用户主动问起为什么不显示时，可以说明。
- 字段级的隐藏一律静默，不影响通用规则的下一步引导。
- 接口不返回头像 `cdnUrl`，按 `../image/upload-image.md` 用 `uid + avatarImageId` 组装预览地址。
- `data` 为 `null` 时说明还没有名片，回复「你还没有创建名片。你可以自己写，也可以把简历、个人主页链接等资料发给我，我整理好之后先给你确认，再创建。」，并按 `upsert-profile.md` 创建；创建时头像必填，整理资料时一并提醒用户提供头像图片。
- 开放 API 只能读写默认名片，没有多名片管理能力。
