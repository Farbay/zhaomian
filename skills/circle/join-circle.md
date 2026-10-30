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
| `channel` | string | 否 | 传当前工具名称，规则见 `../SKILL.md`；省略或空串记为 `""` |
| `formId` | string | 是 | 圈子当前绑定的表单 ID；未绑定表单时传空字符串 |
| `formInput` | array | 有表单时是 | 按 `../dynamic-form.md` 构造；无表单时可省略、传 null 或 `[]`，建议传 `[]` |

## 响应

| 字段 | 描述 |
|------|------|
| `data.id` | 申请记录 ID |
| `data.status` | `1` 已自动通过、`0` 待人工审核 |

## 注意

- 无表单或命中 AUTO 自动通过，其他情况待审核；策略命中和圈内唯一标识规则见 `../dynamic-form.md`。
- 加入圈子与名片绑定独立，申请无需 pid。
- 已加入或已有待处理申请时返回业务提示，不直接返回原记录；查询状态用 `get-latest-submission.md`，不要重复提交。
- 表单与配置不一致时，重新查询圈子当前绑定后再填写。

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
      { "id": "019cbfce-d0b7-7b74-976d-000000000002", "component": "Textarea", "label": "申请理由", "value": "我想加入一起交流" }
    ]
  }'
```
