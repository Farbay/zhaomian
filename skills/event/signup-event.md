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
| `channel` | string | 否 | 报名渠道，传当前工具名称（工具叫什么就传什么，如 Codex 传 `codex`）；未传时记录为空字符串 |
| `formId` | string | 是 | 活动当前绑定的报名表单 ID；未绑定表单时传空字符串 |
| `formInput` | array | 是 | 动态表单数据；按 `../dynamic-form.md` 构造，未绑定表单时传 `[]` |

**不需要传 `cid`**：open 入口统一按「不关联圈子申请」处理。

## 响应

| 字段 | 描述 |
|------|------|
| `data.submissionId` | 报名记录 ID |
| `data.status` | `2` 已通过、`1` 待审核 |
| `data.review` | 审批关联信息 |

| 业务码 | 说明 |
|--------|------|
| `150` | 活动不存在 |
| `151` | 活动报名申请不存在 |
| `170` | 动态表单不存在 |

## 注意

1. **必须上报 `channel`**：传当前工具名称（规则见 `../SKILL.md` 的「channel 渠道标识」），不要传 `open`。
2. **`pid` 必填**：先用 `../profile/get-profile.md` 获取；没有名片时先按 `../profile/upsert-profile.md` 创建，不能凭空生成 pid。
3. `formId` 必须与活动**当前**绑定表单一致；`formInput` 必须严格按该 `formJson` 构造，先读 `../dynamic-form.md`。
4. 是否直接通过取决于是否报满、活动是否需要审核；已有待审核或已通过记录时接口会直接返回原记录，不会重复创建。
5. `data.status=2` 表示报名成功，`data.status=1` 表示等待审核，回复时必须区分这两种结果。
6. 报「报名名片不属于当前用户」时，重新获取 `pid` 再试；报「报名表单与活动配置不一致」时，提示重新搜索活动后再提交。
7. 用户想确认状态时读 `get-latest-submission.md`。

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
      { "id": "reason", "component": "Textarea", "label": "报名理由", "value": "我想参加这次活动" }
    ]
  }'
```
