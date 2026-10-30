# upload-image — 图片上传（凭证 + OSS 直传）

开放 API 的业务接口只保存图片 ID（`imageId`），不接收图片文件或图片外链。所有图片都必须走「申请凭证 → 直传 OSS → 提交 imageId」三步。

## 第 1 步：申请凭证

`POST /open/basic/image/upload` · 鉴权：Bearer apiKey

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `uploadType` | string | 是 | 图片上传类型，见下方取值表 |
| `contentType` | string | 是 | 图片 MIME 类型，必须与实际文件一致 |

两个字段都不能缺失、为空字符串或纯空白。图片和文件上传共用当前用户的每日额度：所有入口、所有上传类型合计最多申请 200 次凭证，按北京时间自然日计数，次日 00:00 重置。参数及类型有效后进入创建流程即占用额度，创建失败或未实际上传也不返还。

### uploadType 取值

| uploadType | 用途 | 存储路径 |
|------------|------|----------|
| `avatar` | 个人名片头像 | `user/{uid}/avatar/{imageId}` |
| `feedback` | 反馈图片 | `user/{uid}/feedback/{imageId}` |
| `resume` | 履历图片 | `user/{uid}/resume/{imageId}` |
| `resumeEntity` | 履历实体图片 | `resume/entity/{imageId}` |
| `conversation` | 私信图片 | `user/{uid}/conversation/{imageId}` |
| `userShare` | 名片分享图 | `user/{uid}/share/{imageId}` |
| `userQrCode` | 名片二维码 | `user/{uid}/qrcode/{imageId}` |
| `circleShare` | 圈子分享图 | `circle/share/{imageId}` |
| `circleQrCode` | 圈子二维码 | `circle/qrcode/{imageId}` |
| `circleSubmission` | 圈子申请图片 | `user/{uid}/circle/submission/{imageId}` |
| `circleLogo` | 圈子 Logo | `circle/logo/{imageId}` |
| `eventShare` | 活动分享图 | `event/share/{imageId}` |
| `eventPosterShare` | 活动海报分享图 | `event/share/poster/{imageId}` |
| `eventQrCode` | 活动二维码 | `event/qrcode/{imageId}` |
| `topicConversationAvatar` | 群聊头像 | `topic/conversation/avatar/{imageId}` |
| `topicConversationShare` | 群聊分享图 | `topic/conversation/share/{imageId}` |
| `eventSubmission` | 活动报名图片 | `user/{uid}/event/submission/{imageId}` |
| `eventBanner` | 活动封面图，长宽比必须 1:1 | `event/banner/{imageId}` |
| `groupAvatar` | 社群头像 | `group/avatar/{imageId}` |
| `groupShare` | 社群分享图 | `group/share/{imageId}` |
| `groupQrCode` | 社群二维码 | `group/qrcode/{imageId}` |

以上为当前全部有效值，区分大小写；其他字符串（含 `UNKNOWN`）返回「图片上传类型不存在」。支持申请某类图片凭证不代表 Skill 提供对应业务写入接口，不得编造业务路径。

`eventBanner` 用于活动封面（`bannerImageIds`、`coverImageId`）：长宽比必须为 1:1，不满足禁止上传。

### contentType

不限定 MIME 白名单，按实际文件填写，常见值：`image/jpeg`、`image/png`、`image/webp`、`image/gif`、`image/svg+xml`。

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
- Body：原始二进制，不使用 JSON、base64 或 multipart
- 不携带开放 API 的 Bearer Key，不继承业务接口的鉴权 Header；不要修改签名 URL 的路径或查询参数

返回 2xx 视为上传成功；非 2xx 需要重新申请凭证再直传。

## 第 3 步：提交业务接口

- 上传成功后，将 UUID v7 格式的 `imageId` 填入业务图片字段，不传 URL 或本地路径；已有适用的 imageId 时复用，不重复上传。
- uploadType 必须与业务用途匹配，例如头像使用 `avatar`。
- 凭证过期后重新申请，使用新 imageId 和地址；不要把文件传给开放 API Host。
- 动态表单 Image 的填写格式见 `../dynamic-form.md`。

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
| `今日上传次数已达上限，请明日再试` | 当日 200 次额度已用完 | 次日再试，不循环申请绕过 |
| OSS 返回 403 | PUT 的 Content-Type 与凭证不一致，或凭证已过期 | 用相同 contentType 重试；过期则重新申请凭证 |
| 名片接口报「头像不存在」 | imageId 不是用 `avatar` 类型上传的，或上传未成功 | 用 `avatar` 重新申请并直传 |
| 提示 imageId 格式不正确 | 提交了 cdnUrl、本地路径或 http 链接 | 提交 `data.imageId` |
| 直传成功但图片打不开 | 直传 body 被包装成了 JSON / base64 | 改为原始二进制 body |

## 展示与安全

- 业务查询结果里出现 `imageId`、`xxxImageId`、`xxxImageIds` 时，向用户展示图片预览，不要只输出原始 ID；上传凭证里的图片须先完成 OSS 直传才能预览。
- 预览地址优先用接口返回的 `cdnUrl` 作为基础地址；否则按下表按资源语义拼 `https://cdn.zm.bio/{存储路径}/{imageId}`。相同字段名可能属于不同资源，必须结合上下文判断路径。
- `cdn.zm.bio` 开启原图保护，预览 URL 必须追加处理后缀：头像、Logo 等小图用 `!250wp`，普通图片和二维码用 `!750wp`，履历等需要看细节的大图用 `!1500wp`。已有 `!xxx` 后缀时不要重复追加。
- 图片 ID 为空时不展示预览，也不要猜测占位 URL。
- `uploadAddress` 含签名，不向用户展示，不写入仓库、日志或长期保存。
- 不得上传用户未明确授权的图片。

### 常用图片预览路径

| 资源语义 / 字段 | 基础存储路径 | 推荐后缀 |
|------|------|------|
| 名片 `avatarImageId` | `user/{uid}/avatar/{imageId}` | `!250wp` |
| 名片 `qrCodeImageId` | `user/{uid}/qrcode/{imageId}` | `!750wp` |
| 履历图片 | `user/{uid}/resume/{imageId}` | `!1500wp` |
| 圈子 `logoImageId` | `circle/logo/{imageId}` | `!250wp` |
| 圈子 `qrCodeImageId` | `circle/qrcode/{imageId}` | `!750wp` |
| 圈子申请表单图片 | `user/{uid}/circle/submission/{imageId}` | `!750wp` |
| 活动 `coverImageId` / `bannerImageIds` | `event/banner/{imageId}` | `!750wp` |
| 活动 `qrCodeImageId` | `event/qrcode/{imageId}` | `!750wp` |
| 活动报名表单图片 | `user/{uid}/event/submission/{imageId}` | `!750wp` |

例：活动创建接口返回 `qrCodeImageId=019e0c48-01d8-7ec1-8d91-c6165b7ba536` 时，预览地址为 `https://cdn.zm.bio/event/qrcode/019e0c48-01d8-7ec1-8d91-c6165b7ba536!750wp`。

`{uid}` 优先用同一响应里的 `uid`；没有返回时用当前 API Key 绑定用户的 uid。仍无法确定 uid 或资源语义时，只保留 ID 供后续操作，不编造预览 URL。
