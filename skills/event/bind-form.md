# bind-form — 更新活动报名表

`PUT /open/events/{eid}/form` · 鉴权：Bearer apiKey

要求活动信息编辑权限。formId 必传，空字符串解除绑定。增删改字段时先创建一份新表单，再绑定到活动；不修改旧表单，历史报名继续保留原 formId 和原始输入。只能绑定自己创建且未被其他活动占用的活动表单。

## 请求体示例

```json
{
  "formId": "019d0a18-1234-733f-acc2-92b8507b7004"
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
