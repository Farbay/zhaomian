# update-event — 修改活动

`PUT /open/events/{eid}` · 鉴权：Bearer apiKey

要求活动信息编辑权限。先读取管理详情，再全量提交活动字段；保留创建者名片主理人以及未修改的设置。不是局部更新，不会修改活动状态或报名表绑定。关系中的 sort 越大越靠前，更新时保留原值；省略会重置为 0。

请求为完整活动字段，除 formId、status 外与创建契约一致；见 `create-event.md`。relations 中 relationType 分别为主办方、联合举办方、主理人，提交时转换成 organizer、coOrganizers、hosts。

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
  "hosts": [
    {
      "entityType": 1,
      "entityId": "019d0a18-1234-733f-acc2-92b8507b7003",
      "uid": "019d0a18-1234-733f-acc2-92b8507b7002",
      "visible": 1
    }
  ],
  "title": "周末技术交流",
  "content": "交流技术与协作经验",
  "bannerImageIds": [
    "019d0a18-1234-733f-acc2-92b8507b7777"
  ],
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
  "rosterVisibility": 2
}
```

## 响应示例

```json
{
  "code": 0,
  "msg": "ok",
  "data": {}
}
```
