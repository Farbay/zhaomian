# upload-image — 图片上传（凭证 + OSS 直传）

开放 API 的业务接口只保存图片 ID（`imageId`），不接收图片文件或图片外链。所有图片都必须走「申请凭证 → 直传 OSS → 提交 imageId」三步。

## 第 1 步：申请凭证

`POST /open/basic/image/upload` · 鉴权：Bearer apiKey

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `uploadType` | string | 是 | 图片上传类型，见下方取值表 |
| `contentType` | string | 是 | 图片 MIME 类型，必须与实际文件一致 |

### uploadType 取值

| uploadType | 用途 | 存储路径 |
|------------|------|----------|
| `avatar` | 个人名片头像 | `user/{uid}/avatar/{imageId}` |
| `resume` | 履历相关图片 | `user/{uid}/resume/{imageId}` |
| `circleSubmission` | 圈子申请动态表单里的图片 | `user/{uid}/circle/submission/{imageId}` |
| `eventSubmission` | 活动报名动态表单里的图片 | `user/{uid}/event/submission/{imageId}` |
| `eventBanner` | 活动封面图 | `event/banner/{imageId}` |

只需使用上表取值；其他值会返回「图片上传类型不存在」，不要尝试。

### contentType

必须非空，且与实际文件、OSS `PUT` Header 完全一致。后端不维护 MIME 白名单，常见值：`image/jpeg`、`image/png`、`image/webp`、`image/gif`、`image/svg+xml`。

### 响应

| 字段 | 说明 |
|------|------|
| `imageId` | 图片 ID，业务接口提交这个字段 |
| `cdnUrl` | CDN 展示地址，仅供展示 |
| `uploadAddress` | OSS 预签名直传地址，10 分钟有效 |

## 第 2 步：直传 OSS

- Method：`PUT`
- Path：`{uploadAddress}`（第 1 步返回值）
- Header：`Content-Type` 必须与第 1 步的 `contentType` **完全一致**
- Body：图片原始二进制内容

返回 2xx 视为上传成功；非 2xx 需要重新申请凭证再直传。

## 第 3 步：提交业务接口

把 `imageId` 填进对应业务接口的图片字段（如名片的 `avatarImageId`、活动的 `coverImageId`）。

## 核心约束

1. **业务接口只认 `imageId`**：格式为 UUIDv7（如 `019d24d6-c2e8-7669-b2c0-4759bb23acb8`）；`cdnUrl` 只能用于展示。
2. **凭证 10 分钟内有效**：过期后重新申请；每次申请都会生成新的 `imageId`。
3. **直传必须用 `PUT`**：`Content-Type` 与凭证不一致时 OSS 会因签名校验失败返回 403。
4. **必须传二进制 body**：不要 base64、不要包成 JSON，也不要把文件发到 `FARBAY_OPEN_HOST`。
5. **uploadType 与用途必须匹配**：头像只能用 `avatar`，用错类型会在名片接口报「头像不存在」。
6. **同一张图不要重复上传**：本地已有 `imageId` 时直接复用。

图片大小与数量由业务接口或动态表单组件控制：动态表单 `Image` 组件通过 `props.maxSizeMB`（默认 5MB）和 `props.maxCount`（默认 1）限制，提交前先读组件 `props`。

## 示例

```bash
# 1. 申请凭证
curl -X POST "${FARBAY_OPEN_HOST}/open/basic/image/upload" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"uploadType":"avatar","contentType":"image/jpeg"}'

# 2. 直传（uploadAddress 取自第 1 步）
curl -X PUT "${UPLOAD_ADDRESS}" \
  -H "Content-Type: image/jpeg" \
  --data-binary @avatar.jpg
```

## 常见错误

| 现象 | 原因 | 处理 |
|------|------|------|
| `图片上传类型不存在` | uploadType 不在支持范围 | 使用上表的精确值 |
| OSS 返回 403 | PUT 的 Content-Type 与凭证不一致，或凭证已过期 | 用相同 contentType 重试；过期则重新申请凭证 |
| 名片接口报「头像不存在」 | imageId 不是用 `avatar` 类型上传的，或上传未成功 | 用 `avatar` 重新申请并直传 |
| 提示 imageId 格式不正确 | 提交了 cdnUrl、本地路径或 http 链接 | 提交 `data.imageId` |
| 直传成功但图片打不开 | 直传 body 被包装成了 JSON / base64 | 改为原始二进制 body |

## 展示与安全

- 业务查询结果里出现 `imageId`、`xxxImageId`、`xxxImageIds` 时，向用户展示图片预览，不要只输出原始 ID；上传凭证里的图片须先完成 OSS 直传才能预览。
- 预览地址优先用接口返回的 `cdnUrl` 作为基础地址；否则按下表按资源语义拼 `https://cdn.zm.bio/{存储路径}/{imageId}`。相同字段名可能属于不同资源，必须结合上下文判断路径。
- `cdn.zm.bio` 开启原图保护，预览 URL 必须追加处理后缀：头像、Logo 等小图用 `!250wp`，普通图片和二维码用 `!750wp`，履历等需要看细节的大图用 `!1500wp`。已有 `!xxx` 后缀时不要重复追加。
- 图片 ID 为空时不展示预览，也不要猜测占位 URL。
- `uploadAddress` 含签名信息且 10 分钟过期，不向用户展示，也禁止写入仓库、日志或长期保存。
- 不得上传用户未明确授权的图片。

### 常用图片预览路径

| 资源语义 / 字段 | 基础存储路径 | 推荐后缀 |
|------|------|------|
| 名片 `avatarImageId` | `user/{uid}/avatar/{imageId}` | `!250wp` |
| 履历图片 | `user/{uid}/resume/{imageId}` | `!1500wp` |
| 圈子 `logoImageId` | `circle/logo/{imageId}` | `!250wp` |
| 圈子 `qrCodeImageId` | `circle/qrcode/{imageId}` | `!750wp` |
| 圈子申请表单图片 | `user/{uid}/circle/submission/{imageId}` | `!750wp` |
| 活动 `coverImageId` / `bannerImageIds` | `event/banner/{imageId}` | `!750wp` |
| 活动 `qrCodeImageId` | `event/qrcode/{imageId}` | `!750wp` |
| 活动报名表单图片 | `user/{uid}/event/submission/{imageId}` | `!750wp` |

例：活动创建接口返回 `qrCodeImageId=019e0c48-01d8-7ec1-8d91-c6165b7ba536` 时，预览地址为 `https://cdn.zm.bio/event/qrcode/019e0c48-01d8-7ec1-8d91-c6165b7ba536!750wp`。

`{uid}` 优先用同一响应里的 `uid`；没有返回时用当前 API Key 绑定用户的 uid。仍无法确定 uid 或资源语义时，只保留 ID 供后续操作，不编造预览 URL。
