# get-management — 获取活动管理详情

`GET /open/events/{eid}/management` · 鉴权：Bearer apiKey

要求活动信息查看权限。返回所有活动设置、原始 relations 和当前报名表 formJson。编辑时从 relations 分别取主办方、联合举办方和主理人；不要把仅用于展示的 organizer、hosts 等字段当作编辑参数。

| 值 | 活动状态 |
| --- | --- |
| 0 | 草稿 |
| 1 | 待审核 |
| 2 | 待发布 |
| 3 | 已发布 |
| 4 | 报名中 |
| 5 | 报名截止 |
| 6 | 进行中 |
| 7 | 已取消 |
| 8 | 已结束 |
| 9 | 已归档 |

| relations[].relationType | 含义 |
| --- | --- |
| 1 | 主办方 |
| 2 | 联合举办方 |
| 3 | 主理人 |

个人活动没有联合举办方时，展示端不显示举办方栏；有联合举办方时沿用圈子活动样式。

## 响应示例

```json
{
  "code": 0,
  "msg": "ok",
  "data": {
    "eid": "019d0a18-1234-733f-acc2-92b8507b7001",
    "creatorUid": "019d0a18-1234-733f-acc2-92b8507b7002",
    "creatorPid": "019d0a18-1234-733f-acc2-92b8507b7003",
    "creator": null,
    "operatorUid": "019d0a18-1234-733f-acc2-92b8507b7002",
    "operator": null,
    "title": "周末技术交流",
    "content": "交流技术与协作经验",
    "bannerImageIds": [
      "019d0a18-1234-733f-acc2-92b8507b7777"
    ],
    "shareImageId": "",
    "qrCodeImageId": "",
    "posterShareImageId": "",
    "formId": "019d0a18-1234-733f-acc2-92b8507b7004",
    "formJson": {
      "components": [
        {
          "id": "reason",
          "component": "Textarea",
          "label": {
            "fallback": "参加理由"
          },
          "required": true,
          "props": {
            "maxlength": 500
          },
          "options": [],
          "validates": [],
          "defaultValue": ""
        }
      ],
      "links": [],
      "strategies": []
    },
    "reserved": {},
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
    "scope": 0,
    "rosterVisibility": 0,
    "status": 4,
    "deleted": 0,
    "createTime": 0,
    "updateTime": 0,
    "buttonStatus": 0,
    "admin": 0,
    "relations": [
      {
        "relationType": 1,
        "entityType": 1,
        "entityId": "019d0a18-1234-733f-acc2-92b8507b7003",
        "uid": "019d0a18-1234-733f-acc2-92b8507b7002",
        "visible": 1,
        "sort": 0
      },
      {
        "relationType": 3,
        "entityType": 1,
        "entityId": "019d0a18-1234-733f-acc2-92b8507b7003",
        "uid": "019d0a18-1234-733f-acc2-92b8507b7002",
        "visible": 1,
        "sort": 0
      }
    ],
    "hosts": [],
    "organizer": null,
    "coOrganizers": [],
    "memberCount": 0,
    "checkInRank": 0,
    "signupStatus": 0,
    "checkInStatus": 0,
    "memberVisible": null,
    "previewMembers": [],
    "eventConversation": null
  }
}
```
