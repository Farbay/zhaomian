# list-signed-up-events — 查询已参加活动列表

`GET /open/events/signed-up` · 鉴权：Bearer apiKey

返回当前用户最近一次报名状态为待审核或已通过的活动。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `count` | number | 是 | 分页大小，1-100 |
| `next` | string | 是 | 第一页传空字符串，后续页传上一页返回的 `data.page.next` |

> 该接口的 `next` 不能省略，不传会报「缺失 next 参数」。

## 响应

| 字段 | 描述 |
|------|------|
| `data.page` | 分页信息，`data.page.next` 为空字符串表示没有下一页 |
| `data.total` | 已参加活动总数，仅第一页返回 |
| `data.list` | 活动列表；无匹配时为 `[]` |

`data.list[]` 的完整字段、状态枚举与活动关系结构见 `query-result.md`，调用本接口时一并读取。

## 注意

- 列表用编号展示标题、开始时间、`venueText` 地点和活动状态；根据 `buttonStatus` 提示可执行动作。
- 没有已参加活动时回复「你还没有参加任何活动，要不要看看最近有什么活动？」。

## 示例

```bash
curl "${FARBAY_OPEN_HOST}/open/events/signed-up?next=&count=10" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}"
```
