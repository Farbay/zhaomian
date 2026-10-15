# generate-check-in — 生成活动签到凭证

`POST /open/events/{eid}/check-in-scenes/{source}` · 鉴权：Bearer apiKey

公共码与分享入口要求活动签到管理权限；活动创建者默认拥有。沿用小程序签到页，参会者扫码或打开分享后在小程序完成签到。不要把管理员的个人码作为参会者公共码。

| source | checkInSource | 用途 |
| --- | --- | --- |
| personal | 1 | 个人签到码，工作人员扫码为本人签到 |
| public | 2 | 公共签到码，参会者扫码后确认签到 |
| share | 4 | 分享签到入口，返回 sharePath |

返回 scene、checkInSource、expireTime、qrCode、sharePath。qrCode 是图片数据，sharePath 是小程序路径；过期后重新生成。

## 响应示例

```json
{
  "code": 0,
  "msg": "ok",
  "data": {
    "scene": "示例Scene",
    "checkInSource": 2,
    "expireTime": 1800086400000,
    "qrCode": "data:image/png;base64,...",
    "sharePath": ""
  }
}
```
