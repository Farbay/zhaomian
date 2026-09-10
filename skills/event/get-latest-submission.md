# get-latest-submission — 查询活动报名最近记录

`GET /open/events/{eid}/submissions/latest` · 鉴权：Bearer apiKey

查询当前用户在指定活动的最近一条未取消报名记录；没有记录或最近一条已取消时返回业务码 `151`。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `eid` | string | 是 | 活动 ID |

## 响应

| 字段 | 类型 | 描述 |
|------|------|------|
| `submissionId` | number | 报名记录 ID |
| `eid` | string | 活动 ID |
| `pid` | string | 报名名片 ID |
| `uid` | string | 报名用户 ID |
| `channel` | string | 报名渠道：提交时所用工具的标识；未上报时为空字符串 |
| `signupSource` | number | `0` 用户主动报名、`1` 主办方邀请、`-2147483648` 未知 |
| `auditReason` | number | `0` 常规报名审核、`1` 超限候补审核、`-2147483648` 未知 |
| `status` | number | 报名状态：`1` 待审核、`2` 通过、`3` 拒绝、`4` 已取消 |
| `formId` | string | 报名表单 ID |
| `formInput` | array | 表单数据 |
| `review` | object | 审批关联信息；没有时为 `{}` |
| `approvalType` | number | `0` 系统自动、`1` 人工审核、`-2147483648` 未知 |
| `operatorUid` | string | 审批人用户 ID；没有时为空字符串 |
| `auditRemark` | string | 审核备注；没有时为空字符串 |
| `auditTime` | number | 审核时间，毫秒时间戳；未审核时为 `0` |
| `createTime` | number | 创建时间，毫秒时间戳 |
| `updateTime` | number | 更新时间，毫秒时间戳 |

## 注意

- 展示报名状态、报名时间和非空审核备注；状态转中文，不要展示数字。
- `151` 表示没有有效报名记录，直接回复「你还没有报名这个活动。」。
