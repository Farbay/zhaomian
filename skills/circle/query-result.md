# 圈子查询结果字段

`search-circles.md` 与 `list-joined-circles.md` 的 `data.list[]` 使用同一结构。

| 字段 | 类型 | 描述 |
|------|------|------|
| `cid` | string | 圈子 ID |
| `name` | string | 圈子名称；没有时为空字符串 |
| `shortName` | string | 圈子简称；没有时为空字符串 |
| `sortName` | string | 圈子排序名；没有时为空字符串 |
| `slogan` | string | 圈子口号；没有时为空字符串 |
| `description` | string | 圈子介绍；没有时为空字符串 |
| `qrCodeImageId` | string | 圈子二维码 Image ID；暂时生成失败时为空字符串 |
| `official` | number | `1` 官方，`0` 非官方 |
| `formId` | string | 申请表单 ID；未绑定时为空字符串 |
| `formJson` | object | 申请表单结构；未绑定时为 `{}` |
| `createTime` | number | 创建时间，毫秒时间戳 |
| `identityStatus` | number | 当前用户在该圈子的身份状态 |
| `submissionStatus` | number | 当前用户在该圈子的申请状态 |
| `eventCreateAllowed` | boolean | 当前用户是否可在该圈子创建活动 |

### identityStatus

| 值 | 说明 |
|----|------|
| `1` | 正常 |
| `0` | 被禁用 |
| `-1` | 已退出 |
| `-2147483648` | 未加入 |

### submissionStatus

| 值 | 说明 |
|----|------|
| `0` | 待审核 |
| `1` | 通过 |
| `-1` | 拒绝 |
| `-2` | 撤回 |
| `-2147483648` | 无申请 |

`identityStatus`、`submissionStatus` 均为当前 API Key 绑定用户在该圈子的状态。
