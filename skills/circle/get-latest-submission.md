# get-latest-submission — 查询圈子申请最近记录

`GET /open/circles/{cid}/submissions/latest` · 鉴权：Bearer apiKey

查询当前用户在指定圈子的最近一条申请记录；没有申请记录时返回业务码 `133`。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `cid` | string | 是 | 圈子 ID |

## 响应

| 字段 | 类型 | 描述 |
|------|------|------|
| `submissionId` | number | 申请记录 ID |
| `cid` | string | 圈子 ID |
| `uid` | string | 申请人用户 ID |
| `channel` | string | 申请渠道：提交时所用工具的标识；未上报时为空字符串 |
| `formId` | string | 申请表单 ID |
| `formInput` | array | 表单数据 |
| `review` | object | 审批关联信息；没有时为 `{}` |
| `approvalType` | number | `0` 系统自动、`1` 人工审核、`-2147483648` 未知 |
| `operatorUid` | string | 审批人用户 ID；没有时为空字符串 |
| `auditRemark` | string | 审核备注；没有时为空字符串 |
| `auditTime` | number | 审核时间，毫秒时间戳；未审核时为 `0` |
| `status` | number | 申请状态：`0` 待审核、`1` 通过、`-1` 拒绝、`-2` 撤回 |
| `createTime` | number | 创建时间，毫秒时间戳 |
| `updateTime` | number | 更新时间，毫秒时间戳 |

## 注意

- 展示申请状态、申请时间和非空审核备注；状态转中文，不要展示数字。
- `133` 表示没有申请过，直接回复「你还没有申请加入这个圈子。」。
