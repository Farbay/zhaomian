# join-circle — 申请加入圈子

`POST /open/circles/{cid}/submissions` · 鉴权：Bearer apiKey

创建当前用户对指定圈子的申请记录；uid 由 API Key 解析。

## 请求

### Path

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `cid` | string | 是 | 圈子 ID |

### Body

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `channel` | string | 否 | 申请渠道，传当前工具名称（工具叫什么就传什么，如 Codex 传 `codex`）；未传时记录为空字符串 |
| `formId` | string | 是 | 圈子当前绑定的表单 ID；未绑定表单时传空字符串 |
| `formInput` | array | 是 | 动态表单数据；按 `../dynamic-form.md` 构造，未绑定表单时传 `[]` |

## 响应

| 字段 | 描述 |
|------|------|
| `data.id` | 申请记录 ID |
| `data.status` | `1` 已自动通过、`0` 待人工审核 |

| 业务码 | 说明 |
|--------|------|
| `131` | 圈子不存在 |
| `170` | 动态表单不存在 |

## 注意

1. **必须上报 `channel`**：传当前工具名称（规则见 `../SKILL.md` 的「channel 渠道标识」），不要传 `open`。
2. **`formId` 必须与圈子当前绑定表单一致**，`formInput` 必须严格按该 `formId` 对应的 `formJson` 构造；先读 `../dynamic-form.md`，禁止凭经验拼表单。
2. 圈子未绑定表单（`formId=""`）时，后端会忽略 `formInput` 并固定保存为 `[]`，提交后自动通过并加入圈子。
3. 圈子绑定表单且配置了 `AUTO` 策略时，校验通过后自动通过；其他情况进入人工审核。
4. 已加入圈子或存在待处理申请时不能重复提交。
5. 展示结果时明确区分「已自动通过，已加入圈子」和「已提交，等待人工审核」；报「申请表单与圈子配置不一致」时，提示重新搜索圈子后再提交。
6. 用户想确认状态时读 `get-latest-submission.md`。

## 示例

无表单申请：

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/circles/${CID}/submissions" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"channel": "codex", "formId": "", "formInput": []}'
```

有表单申请：

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/circles/${CID}/submissions" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "channel": "codex",
    "formId": "019cbfe9-4bbb-733f-acc2-92b8507b3920",
    "formInput": [
      { "id": "reason", "component": "Textarea", "label": "申请理由", "value": "我想加入一起交流" }
    ]
  }'
```
