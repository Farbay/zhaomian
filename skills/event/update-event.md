# update-event — 修改活动

`PUT /open/events/{eid}` · 鉴权：Bearer apiKey

要求活动信息编辑权限。这是**全量替换**，不是局部更新；不会修改活动状态或报名表绑定。

## 构造请求

1. 先按 `get-management.md` 读取管理详情，保留所有未修改设置和创建时的创建者名片主理人。
2. 必须读取 `create-event.md` 的字段类型、场地、门槛和时间约束。请求字段与创建一致，但**不提交 `formId`、`status`**；修改绑定用 `bind-form.md`，状态流转用 `update-status.md`。
3. 将详情的 `relations` 按 `relationType` 转换，不直接回传详情对象：

| `relationType` | 请求字段 | 结构 |
| --- | --- | --- |
| `1` 主办方 | `organizer` | 单个关系对象 |
| `2` 联合举办方 | `coOrganizers` | 完整数组，无则 `[]` |
| `3` 主理人 | `hosts` | 完整数组，必须包含创建时的创建者名片 |

关系对象保留 `entityType`、`entityId`、`uid`、`sort`、`visible`。`sort` 越大越靠前，省略会重置为 `0`。

`venue`、`prerequisiteData` 必须为 JSON 对象字符串；详情若返回对象，提交前序列化一次，已是字符串则不要重复序列化。其余字段使用创建示例的格式，替换为本次编辑后的完整数据，不用示例值覆盖现有设置。

## 响应示例

```json
{
  "code": 0,
  "msg": "ok",
  "data": {}
}
```
