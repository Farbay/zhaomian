# list-tickles — 分页获取拍一拍互动名片

`GET /open/user/tickles` · 鉴权：Bearer apiKey

按拍一拍记录分页返回互动记录中的对方名片。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `direction` | string | 是 | `sent` 查询我拍过的；`received` 查询拍过我的 |
| `count` | number | 是 | 分页大小，1-100 |
| `next` | string | 是 | 第一页传空字符串，后续页传上一页的 `data.page.next` |

## 响应

| 字段 | 类型 | 说明 |
|------|------|------|
| `code` | number | 业务状态码，`0` 表示成功 |
| `msg` | string | 业务提示信息 |
| `data` | object | 分页结果 |
| `data.page` | object | 分页信息 |
| `data.page.count` | number | 本次请求的分页大小 |
| `data.page.next` | string | 下一页游标；空字符串表示没有下一页 |
| `data.total` | number | 拍一拍记录总数；仅第一页返回 |
| `data.list` | array | 对方名片列表；无数据时为 `[]` |
| `data.list[].uid` | string | 对方用户 UID |
| `data.list[].pid` | string | 互动记录中的对方名片 PID |
| `data.list[].nickname` | string | 名片名字 |
| `data.list[].avatarImageId` | string | 头像图片 ID |
| `data.list[].identity` | string | 身份信息 |
| `data.list[].bio` | string | 个人简介 |
| `data.list[].qrCodeImageId` | string | 名片二维码图片 ID；缓存回源可能补生成，生成失败时可为空 |
| `data.list[].createTime` | number | 拍一拍记录创建时间，毫秒时间戳 |

## 注意

- 名片资料由缓存补全；查询后名片失效时跳过该条，可能不足一页，按返回的 `next` 继续翻页。
- 切换接口或方向时将 `next` 重置为空；总数为符合过滤条件的记录数。

- 列表按拍一拍记录时间倒序，不按名片去重；同一人多次拍一拍时可能重复出现。
- 返回互动记录中的对方 PID 对应名片；名片已删除时不返回该记录。
- 展示名片时使用「名字」；头像和二维码按 `../image/upload-image.md` 组装预览地址，图片 ID 为空时不展示。

## 示例

```bash
curl "${FARBAY_OPEN_HOST}/open/user/tickles?direction=received&next=&count=10" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}"
```
