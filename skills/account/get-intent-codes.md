# get-intent-codes — 获取 Onboarding 意图码表

`GET /open/basic/onboarding-intent-codes` · 鉴权：Bearer apiKey

返回有效的 Onboarding 意图码表，按二层树组织；没有数据时 `data` 为 `[]`。

## 响应

| 字段 | 类型 | 描述 |
|------|------|------|
| `label` | string | 一级意图展示文案 |
| `level` | number | 层级，固定为 `1` |
| `children` | array | 二级意图列表；无子项时为 `[]` |
| `children[].label` | string | 二级意图展示文案 |
| `children[].level` | number | 层级，固定为 `2` |

## 注意

- 按一级意图分组展示，二级意图缩进列出，并用编号方便用户选择。
- 引导用户优先选择二级意图，取 `label` 文案按 `update-intent.md` 保存；只有一级意图时再请用户选具体方向。服务端只校验文本非空和长度，不将码表归属作为硬性参数校验。
