# get-form — 获取动态表单结构

`GET /open/basic/dynamic-forms/{formId}` · 鉴权：Bearer apiKey

路径 formId 为 UUID v7；bizType 见 `create-form.md`。当前填写使用业务绑定的 formId，历史记录使用自身 formId；组件与填写值规则见 `../dynamic-form.md`。

## 响应示例

```json
{
  "code": 0,
  "msg": "ok",
  "data": {
    "formId": "019d0a18-1234-733f-acc2-92b8507b7004",
    "bizType": 2,
    "bizId": "019d0a18-1234-733f-acc2-92b8507b7001",
    "formJson": {
      "components": [
        {
          "id": "019cbfce-d0b7-7b74-976d-000000000002",
          "component": "Textarea",
          "label": "参加理由",
          "required": true,
          "props": {
            "placeholder": "请输入参加理由"
          },
          "options": [],
          "validates": [],
          "defaultValue": ""
        }
      ],
      "links": [],
      "strategies": []
    },
    "operatorUid": "019d0a18-1234-733f-acc2-92b8507b7002",
    "createTime": 1800000000000
  }
}
```
