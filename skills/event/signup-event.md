# signup-event — 报名参加活动

`POST /open/events/{eid}/submissions` · 鉴权：Bearer apiKey

创建当前用户对指定活动的报名申请；uid 由 API Key 解析，报名来源固定为用户主动报名。

## 请求

### Path

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `eid` | string | 是 | 活动 ID |

### Body

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `pid` | string | 是 | 报名名片 ID，必须属于当前用户 |
| `channel` | string | 否 | 传当前工具名称，规则见 `../SKILL.md`；省略或空串记为 `""` |
| `formId` | string | 是 | 活动当前绑定的报名表单 ID；未绑定表单时传空字符串 |
| `formInput` | array | 有表单时是 | 按 `../dynamic-form.md` 构造；无表单时可省略、传 null 或 `[]`，建议传 `[]` |

常规报名不传 `cid`，不关联圈子申请；审批、名额及关联圈子申请状态共同决定是否通过，未绑定表单或配置 AUTO 策略均不代表活动免审核。

## 响应

| 字段 | 描述 |
|------|------|
| `data.submissionId` | 报名记录 ID |
| `data.status` | `2` 已通过、`1` 待审核 |
| `data.review` | 审批关联信息 |

## 注意

- pid 从 `../profile/get-profile.md` 获取；没有名片时先按 `../profile/upsert-profile.md` 创建。
- 已有待审核或已通过记录时返回原记录，不重复创建。
- 名片归属错误时重新获取 pid；表单与配置不一致时重新查询活动当前绑定后再填写。
- 查询状态见 `get-latest-submission.md`；取消待审核或已通过报名见 `cancel-signup.md`。

## 示例

无表单报名：

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/events/${EID}/submissions" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"pid": "019cbfe9-55bc-733f-acc2-92b8507b3923", "channel": "codex", "formId": "", "formInput": []}'
```

有表单报名：

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/events/${EID}/submissions" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "pid": "019cbfe9-55bc-733f-acc2-92b8507b3923",
    "channel": "codex",
    "formId": "019cbfe9-4bbb-733f-acc2-92b8507b3920",
    "formInput": [
      { "id": "019cbfce-d0b7-7b74-976d-000000000002", "component": "Textarea", "label": "报名理由", "value": "我想参加这次活动" }
    ]
  }'
```
