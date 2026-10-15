# list-submissions — 查询活动报名名单

`GET /open/events/{eid}/submissions` · 鉴权：Bearer apiKey

要求活动报名查看或审核权限；仅返回每位报名者的最新记录，包含待审核、通过、拒绝和取消记录，不受普通访客名单可见性限制。返回完整原始 formInput、审核信息及 userProfileResult；名片失效时 userProfileResult 为 null。按每条记录的 formId 查询当时的报名表，不要用当前活动表单解释历史输入。

| Query | 必填 | 说明 |
| --- | --- | --- |
| count | 是 | 1–100，建议 10 |
| next | 是 | 第一页空字符串，后续使用 data.page.next；为空表示结束 |

返回 page 和 list，不返回 total。

| status | 报名状态 |
| --- | --- |
| 1 | 待审核 |
| 2 | 已通过 |
| 3 | 已拒绝 |
| 4 | 已取消 |

| 字段 | 枚举值 |
| --- | --- |
| signupSource | 0 用户主动报名；1 主办方邀请 |
| auditReason | 0 常规审核；1 超限审核 |
| approvalType | 0 系统自动；1 人工审核 |

名单涉及个人信息，仅按用户要求展示，不写入公开文档或外部服务。

## 响应示例

```json
{
  "code": 0,
  "msg": "ok",
  "data": {
    "page": {
      "next": "",
      "count": 10
    },
    "list": [
      {
        "submissionId": 1,
        "eid": "019d0a18-1234-733f-acc2-92b8507b7001",
        "pid": "019d0a18-1234-733f-acc2-92b8507b7003",
        "uid": "019d0a18-1234-733f-acc2-92b8507b7002",
        "channel": "",
        "signupSource": 0,
        "auditReason": 0,
        "status": 2,
        "formId": "019d0a18-1234-733f-acc2-92b8507b7004",
        "formInput": [
          {
            "id": "reason",
            "component": "Textarea",
            "label": "参加理由",
            "value": "希望认识技术伙伴"
          }
        ],
        "review": {},
        "approvalType": 0,
        "operatorUid": "",
        "operatorPid": "",
        "auditRemark": "",
        "auditTime": 0,
        "createTime": 0,
        "updateTime": 0,
        "userProfileResult": null
      }
    ]
  }
}
```
