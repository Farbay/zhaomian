# send-captcha — 发送短信 / 邮箱验证码

动态表单的 `MobileVerify`、`EmailVerify` 组件需要用户提供有效验证码，开放 API 提供两个发送接口：

| 用途 | 方法 | 路径 |
|------|------|------|
| 短信验证码 | POST | `/open/basic/mobile/captcha` |
| 邮箱验证码 | POST | `/open/basic/email/captcha` |

鉴权：Bearer apiKey。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `mobile` | string | 短信接口必填 | 接收验证码的手机号 |
| `email` | string | 邮箱接口必填 | 接收验证码的邮箱 |

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/basic/mobile/captcha" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"mobile": "13800000000"}'
```

## 注意

- 只能在用户明确给出手机号 / 邮箱并同意接收后调用，不要自行猜测、拼接或遍历号码。
- 验证码有效期、重发间隔与每小时 / 每天发送条数由服务端限制；开放端还会按账号限制 60 秒发送间隔与 24 小时 20 条上限。触发限制时 `msg` 会给出具体提示，直接转述给用户，不要自行重试绕过。
- 验证码由用户本人查收后提供，禁止编造或用示例值代替。
- 提交表单时按 `../dynamic-form.md` 的组件格式填写：
  - 短信：`{ "mobile": "手机号", "code": "验证码" }`
  - 邮箱：`{ "email": "邮箱", "code": "验证码" }`
