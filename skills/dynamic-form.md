# dynamic-form — 动态表单

圈子申请与活动报名使用动态表单：接口先返回 `formJson`（表单结构），用户填写后按结构构造 `formInput`（表单数据）再提交。表单结构由后台配置，组件类型、必填、联动和校验规则都可能不同，因此**禁止凭经验拼表单**，必须基于当次返回的 `formJson` 构造。

## 适用接口

| 接口 | 表单来源字段 | 提交字段 |
|------|--------------|----------|
| `POST /open/circles/{cid}/submissions` | 圈子搜索结果 / 已加入圈子列表的 `formId`、`formJson` | `formId`、`formInput` |
| `POST /open/events/{eid}/submissions` | 活动搜索结果 / 已参加活动列表的 `formId`、`formJson` | `formId`、`formInput` |

## 核心约束

1. **只提交 `formJson.components` 里存在的组件**：`formInput[].id` 必须与组件 `id` 完全一致，重复 id 或未知 id 会被拒绝。
2. **`component` 必须与定义完全一致**：`formInput[].component` 是组件类型快照，必须等于 `formJson.components[].component`。
3. **`label` 必填且不能为空**：取该组件的 `label.fallback` 文案；不要自造文案。
4. **`value` 字段必须存在**：即使字段为空，也要按类型提交空值（如 `""`、`[]`、`{}`），不能省略 `value` 键。
5. **隐藏字段禁止提交**：按 `links` 判定为不可见的字段，提交会直接报「当前字段按联动规则应隐藏，不能提交」。
6. **可见必填字段必须提交且非空**：不可见字段即使 `required=true` 也不校验（因为本就不该提交）。
7. **`formId` 必须与圈子 / 活动当前配置一致**：圈子未绑定表单时 `formId` 传空字符串，后端会忽略 `formInput` 并固定保存 `[]`。
8. **图片先上传**：`Image` 组件只提交 `imageId`，必须先按 `image/upload-image.md` 完成上传。
9. **验证码要用户提供**：`MobileVerify`、`EmailVerify` 需要先按 `account/send-captcha.md` 发送验证码，再由用户提供；禁止伪造或用示例值。
10. **提交前后核对 `formId`**：列表结果里的 `formId` 与 `formJson` 是当次快照；如果用户停留过久，建议先重新查询圈子 / 活动再提交。

## formJson 结构

```json
{
  "components": [
    {
      "id": "apply_reason",
      "component": "Textarea",
      "label": { "fallback": "申请理由", "zh": "申请理由", "en": "Reason" },
      "description": "简单介绍你的背景和加入原因",
      "required": true,
      "props": { "rows": 4, "maxlength": 500 },
      "options": [],
      "validates": [{ "mode": "minLen", "value": 10 }],
      "defaultValue": ""
    }
  ],
  "links": [
    {
      "target": "company",
      "showWhen": { "id": "identity", "eq": "working" }
    }
  ],
  "strategies": [
    { "strategyId": "AUTO", "bind": ["apply_reason"] }
  ]
}
```

| 字段 | 说明 |
|------|------|
| `components` | 组件数组，决定字段顺序与结构 |
| `components[].id` | 组件唯一 ID，提交时用它关联 |
| `components[].component` | 组件类型，见下文类型表 |
| `components[].label` | 文案对象，提交 `label` 时取 `fallback` |
| `components[].description` | 字段说明，可空 |
| `components[].required` | 是否必填；仅对当前可见的字段生效 |
| `components[].props` | 组件配置，如 `maxlength`、`mode`、`maxCount`、`format`、`maxSizeMB` |
| `components[].options` | 选项数组，元素为 `{ "label": {...}, "value": ... }` |
| `components[].validates` | 校验规则数组，见下文规则表 |
| `components[].defaultValue` | 默认值，用户未填写时可作为兜底 |
| `links` | 联动规则数组，决定字段是否可见 |
| `strategies` | 审批策略数组，圈子申请使用；`AUTO` 表示可自动通过 |

## formInput 结构

```json
[
  {
    "id": "apply_reason",
    "component": "Textarea",
    "label": "申请理由",
    "value": "我想加入一起交流技术"
  }
]
```

| 字段 | 必填 | 说明 |
|------|------|------|
| `id` | 是 | 对应 `formJson.components[].id` |
| `component` | 是 | 对应组件的 `component`，必须完全一致 |
| `label` | 是 | 对应组件的 `label.fallback`，不能为空 |
| `value` | 是 | 用户填写值，类型见组件表；字段必须存在 |

## 组件类型与 value 格式

| component | 值类型 | 说明 |
|-----------|--------|------|
| `Input` | string | `props.type` 为 `number` / `integer` 等数字类型时，提交数字 |
| `Textarea` | string | 受 `props.maxlength` 限制 |
| `InputNumber` | number | 必须是数字 |
| `NumberSelect` | integer | 必须是 `props.start` 到 `props.end` 之间、按 `props.step` 可取的整数 |
| `Select` | 单值或数组 | `props.mode=single` 提交单个 option 值；`props.mode=multiple` 提交数组 |
| `SearchSelect` | string / number / boolean | `options` 非空时值必须在 options 内；`options` 为空时提交单个标量 |
| `Radio` | 单值 | 必须是 options 中的 `value` |
| `Checkbox` | 数组 | 数组元素必须是 options 中的 `value` |
| `Tabs` | 单值 | 必须是 options 中的 `value` |
| `Switch` | boolean | 无 options 时提交布尔值；有 options 时提交 options 中的 `value` |
| `Rating` | integer | `0` 或空值表示未评分；已评分必须是 1 到 `props.max`（或 `props.count`，默认 5）的整数。必填字段必须给出有效评分，不能留 `0` |
| `DatePicker` | string | 按 `props.format`（如 `YYYY-MM-DD`）提交，不要自行转换 |
| `DateRangePicker` | 数组 | 固定两个字符串 `[开始日期, 结束日期]`，格式同 `props.format` |
| `TimePicker` | string | 按 `props.format`（默认 `HH:mm`）提交 |
| `Image` | 数组 | 元素为 `{ "imageId": "..." }`，先读 `image/upload-image.md` 上传 |
| `File` | 数组 | 元素为 `{ "fileId": "..." }`；开放 API 暂无文件上传能力，无法获取 fileId 时告知用户到 App 端完成 |
| `MobileVerify` | 对象 | `{ "mobile": "手机号", "code": "验证码" }`；微信小程序手机号组件场景为 `{ "code": "..." }`。发送验证码见 `account/send-captcha.md` |
| `EmailVerify` | 对象 | `{ "email": "邮箱", "code": "验证码" }`；`props.domain` 非空时邮箱域名必须在白名单内。发送验证码见 `account/send-captcha.md` |

## validates 规则

| mode | 适用值 | 说明 |
|------|--------|------|
| `pattern` | string | 正则匹配 |
| `minLen` / `maxLen` | string | 字符串长度区间 |
| `min` / `max` | number | 数值区间 |
| `minCount` / `maxCount` | 数组 | 选择数量区间 |
| `enum` | 单值 / 数组 | 值必须在给定枚举内 |
| `email` | string | 邮箱格式 |
| `mobile` | string | 手机号格式 |
| `weixin` | string | 微信号格式 |
| `url` | string | 以 `http://` 或 `https://` 开头 |

## 联动（links）

`links[].target` 是受控字段，`links[].showWhen` 是显示条件：

```json
{ "target": "company", "showWhen": { "id": "identity", "eq": "working" } }
```

| 操作符 | 说明 |
|--------|------|
| `eq` | 来源值等于期望值 |
| `ne` | 来源值不等于期望值 |
| `in` | 来源值命中期望数组 |
| `gt` | 来源值大于期望值（仅数值组件） |
| `lt` | 来源值小于期望值（仅数值组件） |

判定顺序：从组件数组顺序计算可见性；来源字段不可见时，依赖它的字段也不可见；被判定不可见的字段**不能出现在 formInput 中**。

**示例**：`identity` 为 Radio（options：`student` / `working`），`company` 的 link 为 `{"showWhen":{"id":"identity","eq":"working"}}`。

用户选择 `working`：

```json
[
  { "id": "identity", "component": "Radio", "label": "身份", "value": "working" },
  { "id": "company", "component": "Input", "label": "公司", "value": "远湾科技" }
]
```

用户选择 `student`（`company` 隐藏，禁止提交）：

```json
[
  { "id": "identity", "component": "Radio", "label": "身份", "value": "student" }
]
```

## strategies 与自动通过

- `strategyId` 取值：`AUTO`（自动通过）、`MANUAL`（人工审核）。
- 当本次 `formInput` 的 id 集合覆盖了某条策略 `bind` 中的全部 id 时，该策略命中。
- 圈子申请命中 `AUTO` 策略（或圈子未绑定表单）时会自动通过并加入圈子；否则进入人工审核。

## 工作流

1. **取表单**：调 `GET /open/circles/search`、`GET /open/circles/joined`、`GET /open/events/search` 或 `GET /open/events/signed-up`，拿到目标圈子 / 活动的 `formId` 与 `formJson`。
2. **判断是否需要表单**：`formId` 为空字符串时，提交时 `formId` 传 `""`、`formInput` 传 `[]`，直接进入下一步。
3. **解析字段**：读取 `formJson.components`，排除 `links` 判定为不可见的字段；对 `required=true` 的可见字段向用户收集值。
4. **补齐值类型**：按组件类型表构造每个可见字段的 `value`；图片先按 `image/upload-image.md` 上传并只提交 `imageId`；验证码按 `account/send-captcha.md` 发送。
5. **构造 formInput**：按 `components` 原顺序输出可见字段的 `{id, component, label, value}` 数组。
6. **提交**：
   - 圈子：`POST /open/circles/{cid}/submissions`（详见 `circle/join-circle.md`），body `{ "channel": "codex", "formId": "...", "formInput": [...] }`
   - 活动：`POST /open/events/{eid}/submissions`（详见 `event/signup-event.md`），body `{ "pid": "报名名片ID", "channel": "codex", "formId": "...", "formInput": [...] }`；`pid` 取自 `profile/get-profile.md` 的 `data.pid`
   - 两处的 `channel` 都传**当前工具标识**（见 `../SKILL.md` 的「channel 渠道标识」），上面写法只是格式示例
7. **处理回包**：校验失败时读取 `msg` 与 `det[]` 定位字段，修正后重试；不要跳过校验直接重试相同数据。

## 完整示例

圈子绑定了表单，包含必填的申请理由、选填的公司（联动）、一张图片：

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/circles/${CID}/submissions" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "channel": "codex",
    "formId": "019cbfe9-4bbb-733f-acc2-92b8507b3920",
    "formInput": [
      { "id": "identity", "component": "Radio", "label": "身份", "value": "working" },
      { "id": "apply_reason", "component": "Textarea", "label": "申请理由", "value": "我想加入一起交流技术" },
      { "id": "company", "component": "Input", "label": "公司", "value": "远湾科技" },
      { "id": "proof", "component": "Image", "label": "证明材料", "value": [{ "imageId": "019d24d6-c2e8-7669-b2c0-4759bb23acb8" }] }
    ]
  }'
```

无表单提交：

```json
{ "channel": "codex", "formId": "", "formInput": [] }
```

## 常见错误

| 错误提示 | 原因 | 处理 |
|----------|------|------|
| `formInput.id 不能为空` | 提交项缺少 `id` | 补齐组件 `id` |
| `提交的 id 不存在于 formJson.components` | 自造字段或提交了旧版本字段 | 以当次返回的 `formJson` 为准 |
| `同一个 id 不能重复提交` | 同一组件出现多次 | 去重 |
| `formInput.component 与 formJson 定义不一致` | 类型快照写错 | 直接复制 `formJson.components[].component` |
| `formInput.label 不能为空` | 未传 `label` 或传了空串 | 传 `label.fallback` |
| `formInput.value 字段缺失` | 省略了 `value` 键 | 空值也要提交 `"value": ""` / `[]` / `{}` |
| `必填字段未提交` / `必填字段未填写` | 漏传可见必填字段，或 Rating 未评分 | 补齐该字段 |
| `当前字段按联动规则应隐藏，不能提交` | 提交了隐藏字段 | 按 links 重新计算可见字段 |
| `formInput JSON 格式不正确` | `formInput` 不是数组 | 使用 JSON 数组 |
| `表单数据不能为空` | `formInput` 为空字符串 / 空值 | 无表单时也要传 `[]` |
| `申请表单与圈子配置不一致` / `报名表单与活动配置不一致` | `formId` 与当前配置不同 | 重新查询圈子 / 活动后再提交 |
