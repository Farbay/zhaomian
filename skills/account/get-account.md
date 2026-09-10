# get-account — 获取个人账号信息

`GET /open/account` · 鉴权：Bearer apiKey

返回当前 API Key 绑定账号的账号信息。

## 响应

| 字段 | 类型 | 描述 |
|------|------|------|
| `uid` | string | 账号 UID |
| `intent` | string | Onboarding 意图；未收集时为空字符串 |
| `createTime` | number | 注册时间，毫秒时间戳 |
| `status` | number | 账号状态：`2` 已完成引导，可正常使用；`1` 已认证但未完成引导 |
| `onboardingConfirmed` | number | Onboarding 确认状态：`1` 已确认、`0` 未确认 |

账号不存在时返回业务码 `100`；账号状态异常时在鉴权阶段返回 HTTP 401。

## 注意

- 展示 UID、意图、账号状态与 Onboarding 是否确认。
- Onboarding 未完成时，按 `confirm-onboarding.md` 的检查清单提示下一步操作。
