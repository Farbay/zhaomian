# list-joined-circles — 查询已加入圈子列表

`GET /open/circles/joined` · 鉴权：Bearer apiKey

返回当前用户已加入且状态正常的圈子。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `count` | number | 是 | 分页大小，1-100 |
| `next` | string | 是 | 第一页传空字符串 |

## 响应

| 字段 | 描述 |
|------|------|
| `data.page` | 分页信息，`data.page.next` 为空字符串表示没有下一页 |
| `data.total` | 已加入且状态正常的圈子总数，仅第一页返回 |
| `data.list` | 圈子列表；没有已加入圈子时为 `[]` |

`data.list[]` 的完整字段与状态枚举见 `query-result.md`，调用本接口时一并读取。

## 注意

- 列表用编号展示名称、简称、口号、是否官方。
- Onboarding 检查「是否已加入圈子」时以本接口是否有记录为准，见 `../account/confirm-onboarding.md`。
- 没有已加入圈子时回复「你还没有加入任何圈子，要不要搜一个看看？」。

## 示例

```bash
curl "${FARBAY_OPEN_HOST}/open/circles/joined?count=10&next=" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}"
```
