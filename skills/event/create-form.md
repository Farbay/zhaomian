# create-form — 创建活动报名表

`POST /open/basic/dynamic-forms` · 鉴权：Bearer apiKey

创建活动表单，bizType 传 2。formJson 为完整表单对象；创建后用返回的 formId 创建活动或更新绑定。增删改字段均生成新表单版本；提交前读取 `../dynamic-form.md`，按现有组件、校验和联动规则构造。

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| bizType | 是 | 活动传 2 |
| formJson | 是 | 完整表单对象，含 components、links、strategies |

## 请求体示例

```json
{
  "bizType": 2,
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
  }
}
```

## 响应示例

```json
{
  "code": 0,
  "msg": "ok",
  "data": {
    "formId": "019d0a18-1234-733f-acc2-92b8507b7004"
  }
}
```
