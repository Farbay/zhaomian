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
- **意图只能填二级意图**：用户选定后取二级意图的 `label` 文案，按 `update-intent.md` 保存。用户给出的是一级意图时，先让他在该一级意图的二级意图里选一个。
