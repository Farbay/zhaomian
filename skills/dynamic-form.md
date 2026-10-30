# dynamic-form — 动态表单

圈子申请与活动报名先读取当次 `formJson`，再构造 `formInput`。禁止凭经验拼字段，也不能用当前表单解释历史申请或报名。

## 适用接口

| 接口 | 表单来源 / 用途 |
| --- | --- |
| `POST /open/circles/{cid}/submissions` | 圈子搜索 / 已加入列表返回的 `formId`、`formJson` |
| `POST /open/events/{eid}/submissions` | 活动搜索 / 已参加列表返回的 `formId`、`formJson` |
| `POST /open/basic/dynamic-forms` | 创建表单，见 `event/create-form.md` |
| `GET /open/basic/dynamic-forms/{formId}` | 读取指定版本，见 `event/get-form.md` |
| `PUT /open/events/{eid}/form` | 绑定新报名表或解除绑定，见 `event/bind-form.md` |

## 核心约束

1. `formId` 必须与圈子 / 活动当前绑定一致；未绑定时传 `""`，`formInput` 建议传 `[]`，也可省略或传 `null`，服务端忽略填写值并保存 `[]`。圈子无表单自动通过，活动无表单不代表免审核。
2. `formInput` 为 JSON 数组，每项为 `{id, component, label, value}`；`id` 取真实组件 UUID v7，同次不重复；`component` 与定义一致，`label` 取组件的非空字符串。
3. 每个已提交项必须有 `value` 键；可见必填字段必须提交且非空。可见选填字段未填写时可省略，不要为了命中策略伪造填写项。
4. 隐藏字段清空且不提交，也不校验必填；切换条件后按组件顺序重新计算后续联动。
5. 组件创建时生成 UUID v7，修改属性或排序时保持 ID。表单保存后不可原地修改，变更需创建新版本并重新绑定；历史记录按自身 `formId` 读取结构。

## formJson 与公共字段

顶层必须包含 `components`、`strategies`、`links` 三个数组，允许空数组；组件按展示顺序排列。

| 组件字段 | 类型 / 说明 |
| --- | --- |
| `id` | 必填，UUID v7，同一表单不可重复 |
| `component` | 必填，组件类型，见下表 |
| `label` | 必填，非空字符串；选项与级联层级的 `label` 也使用字符串 |
| `source` | 可选，来源字符串；普通表单编辑器使用 `form` |
| `editable` | 可选，布尔值，是否允许编辑 |
| `required` | 可选，布尔值，默认 `false` |
| `description` | 可选，说明字符串 |
| `props` | 可选，当前组件支持的配置对象，不混用其他组件或旧版属性 |
| `options` | 按组件提供，元素为 `{label, value}`；级联选项可有 `children` |
| `validates` | 可选，附加校验数组，无规则时 `[]` |
| `defaultValue` | 可选，与组件类型匹配；`null` 表示无默认值，保留合法 `false`、`0`、空串；省略必填项不会自动补默认值 |
| `outputKey` | 可选字符串，保留字段，当前不参与业务输出或唯一标识提取 |
| `unique` | 可选布尔值，默认 `false`；圈子唯一标识标记，见下文 |
| `uniqueNormalize` | 可选字符串，仅 `unique=true` 时生效，见下文 |
| `$xxx` | 外部元数据，如 `$name`、`$externalKey`、`$meta`；组件及其配置中的扩展均以 `$` 开头 |

`$xxx` 不参与协议处理，不依赖其持久化、回传或输出映射，也不能用于绕过业务规则。其他未知组件字段会被拒绝。

## 组件类型与 value 格式

仅使用下表组件及其配置；遇到未知结构先核对版本，不自动替换类型。

| component | value | 配置与约束 |
| --- | --- | --- |
| `Input` | 字符串 | 单行文本 / 网址；`props.placeholder`；格式限制放 `validates` |
| `Textarea` | 字符串 | 长文本；`props.placeholder`；长度限制用 `pattern` |
| `Select` | 单个选项值或选项值数组 | `props.mode=single` / `tabs` 为单选，`multiple` 为多选；可配 `placeholder` |
| `EmailVerify` | `{"email":"demo@example.com","code":"1234"}` | `props` 支持 `placeholder`、`verify`、`codeLength`、`cooldownSeconds`、`domain`；`verify=false` 时可省略 `code` |
| `MobileVerify` | `{"code":"微信手机号授权码"}` | 微信手机号授权，不是短信验证码；`props` 支持 `placeholder`、`verify`、`codeLength`、`cooldownSeconds` |
| `Image` | `[{"imageId":"实际图片UUID"}]` | 先按 `image/upload-image.md` 上传；无组件专属 props |
| `Checkbox` | 布尔值 | 必填时必须为 `true`；条款用 `props.mode=terms`，正文 `text`、链接 `url`，无选项数组 |
| `DatePicker` | 日期 / 时间字符串 | `props.format` 支持 `YYYY`、`YYYY-MM`、`YYYY-MM-DD`、`HH:mm`、`HH:mm:ss`；可配 `placeholder` |
| `File` | `[{"fileId":"实际文件ID"}]` | 无开放文件上传接口；必填且无有效文件时引导用户到小程序完成 |
| `DateRangePicker` | `["开始日期或时间","结束日期或时间"]` | 固定两个字符串，格式按 `props.format`，可配 `placeholder` |
| `CascadePicker` | 从根开始的选项值路径数组 | `props.mode=default`、`placeholder`、`levels`；选项树用 `options.children` |
| `Rating` | 数字 | `props.min`、`max`、`step` 定义范围和步长；省略时分别为 1、5、1 |
| `SocialInput` | `{"platform":"GitHub","content":"https://github.com/example"}` | `props.mode=single`、`placeholder`；平台必须取本组件 `options[].value`，不套用用户社媒接口的平台编码 |
| `CompanyInput` | `{"company":"示例公司","position":"产品经理"}` | `props.position` 控制职位输入，可配 `placeholder`；按配置收集职位 |
| `WechatInput` | 微信号字符串 | 可配 `props.placeholder`，格式校验使用 `validates` |

选项按原始 JSON 类型提交，数字 `1` 与字符串 `"1"` 不相同，不将布尔值转成字符串；数字必须有限。

### 验证组件

- `EmailVerify`：开启验证（`verify` 未设置或为 `true`）时，先按 `account/send-captcha.md` 发送邮箱验证码，由用户查收后提供。`domain` 非空时限制邮箱域名；`validates` 校验完整邮箱，不校验验证码，也不替代真实性验证。`verify=false` 时提交 `{"email":"demo@example.com"}`，仍需满足邮箱格式、域名和附加校验。
- `codeLength` 必须与 `account/send-captcha.md` 的实际验证码位数一致；不一致时停止并反馈配置冲突，不补位、不截断。
- `MobileVerify` 只接受小程序真实授权 `{code}`，不能使用 `{mobile,code}` 或短信验证码；无法取得授权码时引导用户在小程序完成。
- 服务端验证解析后，记录中的邮箱 / 手机号 `value` 保存为字符串，不回传验证码；不能把历史记录里的字符串原样当作新申请的验证输入。

### CascadePicker

`props.levels` 至少两级，每级 `label` 为非空字符串；`outputKey`、`outputLabelKey` 都是保留字段，不参与业务输出。选项树不得超过配置层数。

`value` 如 `["computer","software"]`，必须逐层匹配父子关系，选到末级或叶子才结束。切换父级清空后代；可见选填未填写时可省略或提交 `[]`，必填不能为空。级联只能作为联动目标，不能作为条件来源。

## validates

`validates` 与 `props`、`required` 同级；必填由 `required` 单独判定。`null`、空白字符串和空数组跳过附加规则，不能因此绕过必填。

| mode | 配置 / 语义 |
| --- | --- |
| `email` | 邮箱格式，无附加参数 |
| `mobile` | 手机号格式，无附加参数 |
| `weixin` | 微信号格式，无附加参数 |
| `url` | 网址格式，无附加参数 |
| `number` | 数字格式，可校验数字字符串，不改变 `Input` 的字符串提交类型 |
| `pattern` | `value` 为非空 Java 正则字符串，不含两端斜杠；使用查找匹配，完整匹配需显式 `^` / `$`；保留正则首尾空白 |
| `range` | `min` / `max` 包含边界，可省略一侧；按十进制数值比较，数字文本也按数值解析，**不是文本长度** |
| `enum` | `values` 为非空允许值数组，按类型严格匹配 |

长度限制用正则；Select 不必重复配置与选项相同的枚举。JSON 中正则反斜杠需转义，例如 1–100 字符：

```json
{"validates":[{"mode":"pattern","value":"^[\\s\\S]{1,100}$"}]}
```

## links

每个 `target` 只配置一条联动；`target` 与 `showWhen.id` 都必须是真实组件 ID，目标位于来源之后。`showWhen` 除 `id` 外仅配置一个操作符。

| 操作符 | 条件值 / 含义 |
| --- | --- |
| `eq` | 等于指定单值 |
| `ne` | 不等于指定单值 |
| `in` | 属于非空数组中的任一值 |
| `gt` | 大于有限数字 |
| `lt` | 小于有限数字 |

条件值与来源填写值类型一致；数值比较仅用于数值语义，不将字符串直接当数字。条件满足显示，否则清空并隐藏，不提交隐藏项；来源未提交时不显示依赖字段。

## strategies

每项为 `{strategyId, bind}`；`bind` 是非空、不重复且存在的组件 ID 数组，不使用 `outputKey` 或填写值匹配。

| strategyId | 含义 |
| --- | --- |
| `AUTO` | 自动通过策略 |
| `MANUAL` | 人工审核策略，不覆盖已命中的 AUTO |

本次有效 `formInput` 的 ID 集合包含某条 `bind` 的全部 ID 时命中：单条内 AND，多条同名策略 OR，隐藏字段不参与。两个互斥分支任一可自动通过时应配置两条 AUTO，不把两个互斥字段放进同一 `bind`。

圈子无表单或命中 AUTO 自动通过，未命中则待审核；活动是否通过仍按 `event/signup-event.md` 的审批规则。

## unique 与圈子唯一标识

`unique`、`uniqueNormalize` 位于组件顶级，不放进 `props` 或 `validates`，也不依赖 `outputKey`。

| uniqueNormalize | 处理 |
| --- | --- |
| 省略 / 空字符串 | 去首尾空白，保留大小写 |
| `lowercase` | 去首尾空白后转小写 |
| `emailPrefixLowercase` | 去首尾空白，取邮箱前缀并转小写，再去前缀首尾空白 |

只从有效且实际提交的 `unique=true` 字段提取；同次最多一个非空字符串，归一化后最多 255 字符。归一化仅用于唯一标识，不改原填写值；空值不占用。

同一圈子正常与禁用成员共同占用，退出释放，不按组件 ID 分不同命名空间；待审核申请不预占。提交申请时检查，通过或重新入圈时再次检查，待审核不保证最终占用成功。冲突提示「该唯一标识已被圈内成员使用」。历史成员不自动回填；活动表单只保存配置，不提供报名唯一性保证。

## 完整示例

以下表单按身份显示公司字段；示例 ID 仅用于说明，调用已有表单必须使用真实返回的 ID。

```json
{
  "components": [
    {
      "id": "019cbfce-d0b7-7b74-976d-000000000001",
      "component": "Select",
      "required": true,
      "label": "身份",
      "props": {"mode":"tabs","placeholder":"请选择身份"},
      "options": [{"label":"在校","value":"student"},{"label":"在职","value":"working"}]
    },
    {
      "id": "019cbfce-d0b7-7b74-976d-000000000002",
      "component": "Textarea",
      "required": true,
      "label": "申请理由",
      "props": {"placeholder":"请输入申请理由"},
      "validates": [{"mode":"pattern","value":"^[\\s\\S]{10,500}$"}],
      "defaultValue": null
    },
    {
      "id": "019cbfce-d0b7-7b74-976d-000000000003",
      "component": "CompanyInput",
      "required": true,
      "label": "公司信息",
      "props": {"placeholder":"请输入公司","position":true},
      "validates": [],
      "defaultValue": null
    }
  ],
  "strategies": [],
  "links": [
    {
      "target": "019cbfce-d0b7-7b74-976d-000000000003",
      "showWhen": {"id":"019cbfce-d0b7-7b74-976d-000000000001","eq":"working"}
    }
  ]
}
```

在职分支的圈子申请 body：

```json
{
  "channel": "pi",
  "formId": "019cbfe9-4bbb-733f-acc2-92b8507b3920",
  "formInput": [
    {"id":"019cbfce-d0b7-7b74-976d-000000000001","component":"Select","label":"身份","value":"working"},
    {"id":"019cbfce-d0b7-7b74-976d-000000000002","component":"Textarea","label":"申请理由","value":"我想加入圈子一起交流技术与产品经验"},
    {"id":"019cbfce-d0b7-7b74-976d-000000000003","component":"CompanyInput","label":"公司信息","value":{"company":"示例公司","position":"产品经理"}}
  ]
}
```

选择 `student` 时不提交公司项，即使它 `required=true`。`channel` 换成当前工具名称，见 `SKILL.md`。活动报名还需传 `pid`，取自 `profile/get-profile.md`，详见 `event/signup-event.md`。

## 提交与错误处理

1. 只有 formId 时按 `event/get-form.md` 读取结构；停留较久或表单不一致时重新查询业务当前绑定，不仅重读旧 ID。
2. 按组件顺序构造可见填写项，向用户确认后提交；验证码 / 授权码须真实，必填条款须用户同意，不能伪造或代勾。
3. 失败时读取 `msg`、`det[]` 定位字段，修正后再提交，不原样循环重试。
