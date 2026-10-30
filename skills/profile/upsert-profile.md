# upsert-profile — 创建或修改名片

`POST /open/user/profile` · 鉴权：Bearer apiKey

已有默认名片时更新该名片，否则创建名片。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `channel` | string | 否 | 传当前工具名称，规则见 `../SKILL.md`；省略或空串记为 `open` |
| `nickname` | string | 是 | 名字，最长 16 字 |
| `gender` | number | 是 | `0` 未知、`1` 男、`2` 女 |
| `birthday` | string | 是 | `YYYY-MM-DD` 或空字符串 |
| `bio` | string | 是 | 个人简介，15-255 字，会做语义检测 |
| `identity` | string | 是 | 身份信息，最长 64 字 |
| `avatarImageId` | string | 创建时必填 | 头像 `imageId`（UUIDv7）；更新时省略或传空字符串表示沿用现有头像 |

## 响应

| 字段 | 描述 |
|------|------|
| `data.pid` | 名片 ID |

## 注意

- **更新是完整提交**：`nickname`、`gender`、`birthday`、`bio`、`identity` 都要提交，不换头像时 `avatarImageId` 可省略或传空字符串。
- **头像必须先上传**：`avatarImageId` 必须是用 `avatar` 类型按 `../image/upload-image.md` 上传成功的 `imageId`，禁止提交 `cdnUrl` 或本地路径；用错类型会报「头像不存在」。
- 简介被语义检测拒绝时转述 msg，引导用户补充有实际含义的内容。
- 创建成功后记录 `pid`，供后续报名活动、创建活动使用，不要向用户复述。

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
