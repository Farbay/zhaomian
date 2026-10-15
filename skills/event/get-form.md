# get-form — 获取报名表结构

`GET /open/basic/dynamic-forms/{formId}` · 鉴权：Bearer apiKey

读取当前或历史报名表结构，用于查看字段和解释报名者原始输入。修改字段需创建新表单后重新绑定，不原地覆盖历史表单。

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
    "operatorUid": "019d0a18-1234-733f-acc2-92b8507b7002",
    "createTime": 1800000000000
  }
}
```
