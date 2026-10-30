# update-intent — 更新用户意图

`PUT /open/account/intent` · 鉴权：Bearer apiKey

新增或更新当前账号的 Onboarding 意图，重复提交为覆盖更新。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `intent` | string | 是 | 当前账号的意图；不能为空，最长 255 字 |

## 注意

- 优先取 `get-intent-codes.md` 返回的二级意图 `label` 文案；这是引导建议，服务端不校验是否属于码表。用户明确提供自定义意图时可在确认后按原文提交。
- 「找人」「看机会」「逛一逛」属于未完成占位值，即使保存成功也不满足 Onboarding 的已收集意图条件，需引导用户补充具体意图。
- 用户不确定填什么意图时，先读 `get-intent-codes.md` 拉取码表。
- 如果用户正在完成 Onboarding，保存后再按 `confirm-onboarding.md` 确认完成状态。

## 示例

```bash
curl -X PUT "${FARBAY_OPEN_HOST}/open/account/intent" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"intent": "找投资人"}'
```
