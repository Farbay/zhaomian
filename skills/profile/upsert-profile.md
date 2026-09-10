# upsert-profile — 创建或修改名片

`POST /open/user/profile` · 鉴权：Bearer apiKey

已有默认名片时更新该名片，否则创建名片。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `channel` | string | 否 | 创建/修改渠道，传当前工具名称（工具叫什么就传什么，如 Codex 传 `codex`）；不传或传空串时后端记为 `open` |
| `nickname` | string | 是 | 昵称，最长 16 字 |
| `gender` | number | 是 | `0` 未知、`1` 男、`2` 女 |
| `birthday` | string | 是 | `YYYY-MM-DD` 或空字符串 |
| `bio` | string | 是 | 个人简介，15-255 字，会做语义检测 |
| `identity` | string | 是 | 身份信息，最长 64 字 |
| `avatarImageId` | string | 创建时必填 | 头像 `imageId`（UUIDv7）；更新时省略表示沿用现有头像 |

## 响应

| 字段 | 描述 |
|------|------|
| `data.pid` | 名片 ID |

## 注意

- **必须上报 `channel`**：传当前工具名称，取值规则见 `../SKILL.md` 的「channel 渠道标识」；不要传 `open`。
- **更新是完整提交**：`nickname`、`gender`、`birthday`、`bio`、`identity` 都要提交，不换头像时才省略 `avatarImageId`。
- **头像必须先上传**：`avatarImageId` 必须是用 `avatar` 类型按 `../image/upload-image.md` 上传成功的 `imageId`，禁止提交 `cdnUrl` 或本地路径；用错类型会报「头像不存在」。
- 创建时没有头像会报「创建名片时 avatarImageId 不能为空」。
- `bio` 需 15-255 字且有实际含义，无意义内容（重复字符、乱码等）会被拒绝；被拒时直接转述 `msg` 并给出改写建议。
- 创建成功后记录 `pid`，可提示用户用它报名活动。

## 示例

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/user/profile" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "channel": "codex",
    "nickname": "Farbay Creator",
    "gender": 1,
    "birthday": "2001-05-06",
    "bio": "关注产品与社区，喜欢把复杂问题拆成可落地的方案。",
    "identity": "远湾产品经理",
    "avatarImageId": "019d24d6-c2e8-7669-b2c0-4759bb23acb8"
  }'
```
