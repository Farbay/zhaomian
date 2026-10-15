# update-status — 更新活动状态

`PUT /open/events/{eid}/status` · 鉴权：Bearer apiKey

要求活动信息编辑权限。取消活动与取消自己报名不同；取消前确认目标活动，不能直接设置活动为已发布。只允许当前状态支持的流转。

| status | 含义 |
| --- | --- |
| 1 | 提交审核 |
| 7 | 取消活动 |
| 9 | 归档活动 |

## 请求体示例

```json
{
  "status": 7
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
