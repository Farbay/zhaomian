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

- 展示昵称、性别、生日、身份、简介、创建时间；性别转中文。
- 接口不返回头像 `cdnUrl`，按 `../image/upload-image.md` 用 `uid + avatarImageId` 组装预览地址。
- `data` 为 `null` 时说明还没有名片，回复「你还没有创建名片，要不要现在创建一个？」，并按 `upsert-profile.md` 创建。
- 开放 API 只能读写默认名片，没有多名片管理能力。
