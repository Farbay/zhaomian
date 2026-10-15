# list-created-events — 查询我创建的活动

`GET /open/events/created` · 鉴权：Bearer apiKey

仅查询当前账号创建且未删除的活动，包含草稿、待审核、取消和归档，不混入仅报名或仅担任主理人的活动。按活动 ID 降序分页。

| Query | 必填 | 说明 |
| --- | --- | --- |
| count | 是 | 1–100，建议 10 |
| next | 是 | 第一页空字符串，后续使用 data.page.next；为空表示结束 |

返回 page 和 list，不返回 total。

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
        "eid": "019d0a18-1234-733f-acc2-92b8507b7001",
        "title": "周末技术交流",
        "bannerImageIds": [
          "019d0a18-1234-733f-acc2-92b8507b7777"
        ],
        "signupEndTime": 0,
        "startTime": 1800003600000,
        "endTime": 1800010800000,
        "status": 4,
        "prerequisite": 0
      }
    ]
  }
}
```
