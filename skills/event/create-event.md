# create-event — 创建个人活动

`POST /open/events` · 鉴权：Bearer apiKey

有名片即可创建，无需完成 onboarding。先调用 `../profile/get-profile.md` 获取当前名片的 uid、pid，填写 organizer；没有名片返回业务码 110，按 `../profile/upsert-profile.md` 创建，不需要先创建圈子。登录身份由 API Key 注入，关系对象中的 uid 表示名片归属，仍需显式填写。圈子活动也可使用此完整接口。

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| organizer | 是 | 主办方；个人使用自己的 uid、pid |
| coOrganizers | 是 | 联合举办方完整列表，无则 []；个人活动仅支持圈子 |
| hosts | 是 | 主理人名片列表；创建时自动加入创建者，修改时必须保留创建时的名片；最终最多 30 个 |
| title | 是 | 标题，最多 128 字 |
| content | 是 | 介绍，最多 10000 字 |
| bannerImageIds | 是 | 非空图片 ID 数组；先上传 eventBanner 图片 |
| formId | 创建必填 | 空字符串表示不绑定；修改时使用独立的表单绑定接口 |
| prerequisite | 是 | 报名门槛，枚举见下方 |
| prerequisiteData | 是 | 无门槛传 {}；指定圈子传 {"cids":["圈子ID"]} |
| requireApproval | 是 | 是否需要审核 |
| participantLimit | 是 | 人数上限，0 不限 |
| allowOverLimit | 是 | 是否允许超额候补 |
| publishMode | 是 | 发布方式 |
| publishTime | 是 | 毫秒时间戳；立即发布传 0，由服务端决定 |
| signupEndTime | 是 | 毫秒时间戳；0 不限，服务端按活动结束时间保存 |
| startTime / endTime | 是 | 毫秒时间戳；提交审核要求结束晚于开始 |
| venueVisibility | 是 | 地点可见性 |
| venue | 是 | 地点对象：offline、online 可组合；notice 不能与其他节点共存 |
| scope | 是 | 活动可见范围 |
| rosterVisibility | 是 | 报名名单可见性 |
| status | 创建必填 | 初始状态；修改时使用独立状态接口 |

### 枚举

| 字段 | 值 | 含义 |
| --- | --- | --- |
| organizer.entityType / coOrganizers[].entityType | 1 | 用户名片，entityId 为 pid，uid 为名片归属用户 |
| organizer.entityType / coOrganizers[].entityType | 2 | 圈子，entityId 为 cid，uid 传空字符串 |
| hosts[].entityType | 1 | 主理人只允许用户名片 |
| visible | 0 | 隐藏 |
| visible | 1 | 显示，未传时默认值 |
| prerequisite | 0 | 无门槛 |
| prerequisite | 1 | 主办方和联合举办方圈子；无圈子时无人可新报名 |
| prerequisite | 2 | 指定圈子 |
| requireApproval / allowOverLimit | 0 | 否 |
| requireApproval / allowOverLimit | 1 | 是 |
| publishMode | 0 | 立即发布 |
| publishMode | 1 | 定时发布 |
| venueVisibility | 0 | 公开 |
| venueVisibility | 1 | 报名后可见 |
| scope | 0 | 私有 |
| scope | 1 | 公开 |
| rosterVisibility | 0 | 不可见 |
| rosterVisibility | 1 | 公开 |
| rosterVisibility | 2 | 报名后 |
| rosterVisibility | 3 | 签到后 |
| status（创建） | 0 | 草稿 |
| status（创建） | 1 | 提交审核 |

圈子主办方要求圈子管理员或活动编辑权限；个人主办方必须属于当前用户。主办方不能同时作为联合举办方，不能有多个个人举办方。主理人不受这个限制。

草稿可留空标题、介绍、时间和地点，但封面仍必填。提交审核时必须有标题、介绍、有效场地、开始和结束时间；报名截止时间不能晚于结束时间，也不能早于发布时间；定时发布须满足 0 < publishTime < startTime。

## 请求体示例

```json
{
  "organizer": {
    "entityType": 1,
    "entityId": "019d0a18-1234-733f-acc2-92b8507b7003",
    "uid": "019d0a18-1234-733f-acc2-92b8507b7002",
    "visible": 1
  },
  "coOrganizers": [],
  "hosts": [],
  "title": "周末技术交流",
  "content": "交流技术与协作经验",
  "bannerImageIds": [
    "019d0a18-1234-733f-acc2-92b8507b7777"
  ],
  "formId": "",
  "prerequisite": 0,
  "prerequisiteData": {},
  "requireApproval": 0,
  "participantLimit": 0,
  "allowOverLimit": 0,
  "publishMode": 0,
  "publishTime": 0,
  "signupEndTime": 0,
  "startTime": 1800003600000,
  "endTime": 1800010800000,
  "venueVisibility": 0,
  "venue": {
    "notice": {
      "title": "地点另行通知"
    }
  },
  "scope": 1,
  "rosterVisibility": 2,
  "status": 1
}
```

创建请求超时后不要直接重试；先用 `list-created-events.md` 核对是否已创建。无法确认时停止并让用户决定，避免重复创建。

## 响应示例

```json
{
  "code": 0,
  "msg": "ok",
  "data": {
    "eid": "019d0a18-1234-733f-acc2-92b8507b7001"
  }
}
```
