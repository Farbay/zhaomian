# search-circles — 搜索公开圈子

`GET /open/circles/search` · 鉴权：Bearer apiKey

按关键词模糊搜索正常且公开的圈子，匹配圈子名称、简称、排序名、口号和详细介绍，不区分大小写。
`keyword` 不传、传空字符串或纯空格时表示不按关键词过滤，返回全部符合条件的圈子。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `keyword` | string | 否 | 搜索关键词，去除首尾空格后最长 32 字；不传或为空表示不按关键词过滤 |
| `count` | number | 是 | 分页大小，1-100 |
| `next` | string | 是 | 第一页传空字符串 |

## 响应

| 字段 | 描述 |
|------|------|
| `data.page` | 分页信息，`data.page.next` 为空字符串表示没有下一页 |
| `data.total` | 当前条件下的公开圈子总数，仅第一页返回 |
| `data.list` | 圈子列表；无匹配时为 `[]` |

`data.list[]` 的完整字段与状态枚举见 `query-result.md`，调用本接口时一并读取。

## 注意

- **`keyword` 可不传**：不传或为空时返回全部公开圈子，可直接当作「圈子列表」使用；用户只是想随便看看时不必先追问方向。结果按置顶、官方标记、更新时间、圈子 ID 依次倒序，不是个性化推荐。
- 用户有明确方向（如「技术」「创业」）时，带上 `keyword` 搜索更准确。
- 列表用编号展示名称、简称、口号、是否官方；已加入或已申请时提示当前用户状态。
- 用户选中某个圈子后可以：申请加入（读 `join-circle.md`，表单字段来自本次结果的 `formId` / `formJson`）；或在 `eventCreateAllowed=true` 时按 `create-event.md` 创建活动。
- 用户问「我加入了哪些圈子」时改用 `list-joined-circles.md`。
- 没有搜索结果时回复「没有找到相关圈子，换个关键词试试？」。

## 示例

```bash
curl "${FARBAY_OPEN_HOST}/open/circles/search?keyword=%E6%8A%80%E6%9C%AF&count=10&next=" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}"
```
