# create-form — 创建动态表单

`POST /open/basic/dynamic-forms` · 鉴权：Bearer apiKey

创建前读取 `../dynamic-form.md` 的组件、校验和联动协议。

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| bizType | 是 | 业务类型，见下表 |
| formJson | 是 | JSON 对象，包含 components、strategies、links 三个数组 |

| bizType | 用途 |
| --- | --- |
| 1 | 圈子 |
| 2 | 活动 |
| 3 | 圈子成员信息 |

- 创建时不传 eid、cid、bizId 或 formId，不自动绑定业务。
- 活动表单使用 bizType=2，取得 formId 后按 `create-event.md` 创建活动或按 `bind-form.md` 绑定；其他业务无对应 Skill 绑定接口。
- bizType=3 的规范化内容未变时可复用原 ID；组件和选项顺序变化属于内容变化。

## 请求体示例

活动报名表，参加理由限 1–500 字符：

```json
{
  "bizType": 2,
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
        "validates": [
          {
            "mode": "pattern",
            "value": "^[\\s\\S]{1,500}$"
          }
        ]
      }
    ],
    "strategies": [],
    "links": []
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
