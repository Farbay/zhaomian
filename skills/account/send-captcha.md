# send-captcha — 发送短信 / 邮箱验证码

以下接口均发送 **4 位数字验证码**：

| 用途 | 方法 | 路径 |
| --- | --- | --- |
| 短信验证码 | POST | `/open/basic/mobile/captcha` |
| 邮箱验证码 | POST | `/open/basic/email/captcha` |

鉴权：Bearer apiKey。邮箱验证码用于动态表单 `EmailVerify`；`MobileVerify` 使用微信手机号授权 code，**不使用短信验证码**。

## 请求

| 参数 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `mobile` | string | 短信接口必填 | 接收手机号，使用 E.164 格式，如 `+8613800000000` |
| `email` | string | 邮箱接口必填 | 有效邮箱地址 |

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/basic/email/captcha" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"email": "demo@example.com"}'
```

成功返回 `code=0`、`msg=ok`，无额外业务数据。

## 注意

- 只能在用户明确给出手机号 / 邮箱并同意接收后调用，不猜测、拼接或遍历接收方。
- 接收方的有效期、重发间隔及短信每小时 / 每天条数由服务端限制；开放端另外按账号对短信和邮箱共用 60 秒发送间隔与 24 小时 20 条上限。触发限制直接转述 msg，不重试绕过。
- 验证码由用户本人查收后提供，按字符串传入，保留前导零，不编造或使用示例值。
- 表单填写及 codeLength 冲突处理见 `../dynamic-form.md`。
