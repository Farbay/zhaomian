# create-event — 创建个人活动

`POST /open/events` · 鉴权：Bearer apiKey

有名片即可创建，无需完成 onboarding。先调用 `../profile/get-profile.md` 获取当前名片的 uid、pid；没有名片返回业务码 110，按 `../profile/upsert-profile.md` 创建，不需要先创建圈子。

本接口支持个人或圈子主办，可保存草稿、自定义全部设置；圈子固定默认设置的简化创建见 `../circle/create-event.md`，两种请求体不可混用。

## 请求字段

以下字段创建时全部必传且不能为 `null`。integer 为 JSON 整数，不是字符串或布尔值；`title`、`content` 传文本，不传国际化对象。

| 字段 | JSON 类型 | 说明 |
| --- | --- | --- |
| `organizer` | object | 唯一主办方，关系结构见下方 |
| `coOrganizers` | object[] | 联合举办方完整列表，无则 `[]` |
| `hosts` | object[] | 主理人列表，只需创建者时传 `[]`；自动加入及人数规则见下方 |
| `title` | string | 标题，最多 128 字 |
| `content` | string | 活动详细介绍，最多 10000 字 |
| `bannerImageIds` | string[] | 至少一个非空 UUID v7 图片 ID；先按 `../image/upload-image.md` 上传 `eventBanner` 图片，不传 URL、本地路径或图片对象 |
| `formId` | string | `""` 不绑定，否则传有效报名表 ID；创建表单见 `create-form.md`，修改绑定见 `bind-form.md` |
| `prerequisite` | integer | 报名门槛，取值及配套数据见下方 |
| `prerequisiteData` | string | **JSON 对象字符串**，与 `prerequisite` 配套 |
| `requireApproval` | integer | 报名是否需审核：`0` 无需，`1` 需要；不是活动发布审核开关 |
| `participantLimit` | integer | 正式名额上限，必须 `>= 0`；`0` 无上限 |
| `allowOverLimit` | integer | `0` 满额后禁止新报名；`1` 允许超限申请但需审核，不自动获得名额；人数不限时无此问题 |
| `publishMode` | integer | `0` 立即发布，`1` 定时发布 |
| `publishTime` | integer | 毫秒时间戳，`>= 0`；立即发布传 `0`，提交审核时服务端覆盖为当前时间，草稿保存为 `0`；定时发布填写具体时刻 |
| `signupEndTime` | integer | 报名截止时间；`0` 表示不另设截止，**实际按 `endTime` 保存**；要开始前截止须显式填写 |
| `startTime` | integer | 开始时间 |
| `endTime` | integer | 结束时间；时间格式及先后约束见下方 |
| `venueVisibility` | integer | 地点详情可见性：`0` 公开，`1` 报名后可见；与活动本身的 `scope` 不同 |
| `venue` | string | **JSON 对象字符串**；完整场地字段与组合见下方 |
| `scope` | integer | 活动索引范围：`0` 私有（创建者及分享链接 / 二维码可见，不等于仅自己可见），`1` 公开；不接受历史值 `2` |
| `rosterVisibility` | integer | 普通用户的报名名单可见性：`0` 不可见，`1` 公开，`2` 报名后，`3` 签到后；不限制管理者按权限查看 |
| `status` | integer | 仅 `0` 草稿、`1` 提交审核；后续流转见 `update-status.md` |

### 主办方、联合举办方与主理人

`organizer`、`coOrganizers[]`、`hosts[]` 使用同一种结构：

| 字段 | JSON 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `entityType` | integer | 是 | `1` 用户名片，`2` 圈子；主理人只允许 `1` |
| `entityId` | string | 是 | UUID v7；用户名片填 **pid**，圈子填 **cid**，不能把 uid 当作 pid |
| `uid` | string | 是 | 名片填所属用户 uid，并且必须与 pid 匹配；圈子必须传 `""`，不能省略或填当前用户 uid |
| `sort` | integer | 否 | `>= 0`，越大越靠前；创建时统一保存为 `0`，同值按数组顺序；修改时才使用传入值，省略会重置为 `0` |
| `visible` | integer | 否 | `0` 隐藏，`1` 显示，默认 `1`；只是前端展示标记，不会取消管理权限、报名门槛或其他关联能力，不能作为隐私隔离开关 |

- 个人主办方必须属于当前用户；圈子主办方要求圈子管理员或 `event.info.update` 权限，可先查圈子的 `eventCreateAllowed`。
- API Key 不代填关系中的 `uid`、`entityId`；使用实际查询所得 ID，名称、头像无需提交。
- 主办方不能同时出现在联合举办方；联合举办方列表内部、主理人列表内部不能重复。
- 主办与联合举办方合计最多一个个人名片，其余只能是圈子；主理人不受此限制。
- 创建者当前默认名片自动置为第一位主理人，已有则合并，最终最多 30 人；修改时必须保留创建时的那张名片。主理人会获得活动运营者权限，不只是展示嘉宾。

### 报名门槛配置

`prerequisiteData`、`venue` 均先将对象序列化为字符串，再序列化整个请求体；不能直接传嵌套对象。

| `prerequisite` | `prerequisiteData` 请求值 | 语义 |
| --- | --- | --- |
| `0` | `"{}"` | 不限制圈子成员资格；是否审核仍由 `requireApproval` 决定 |
| `1` | `"{}"` | 服务端从主办及联合举办圈子生成 `cids`，不能用此配置另选圈子；没有任何举办圈子时保存 `{"cids":[]}`，任何用户都不能新报名 |
| `2` | `"{\"cids\":[\"实际圈子UUID\"]}"` | `cids` 必须为非空字符串数组，每项是实际存在且有效的圈子 UUID v7；满足任一指定圈子的成员资格即可，不是必须加入全部圈子 |

### 时间与草稿约束

- 四个时间字段均为非负整数毫秒时间戳，按 `Asia/Shanghai` 解析，精确到分钟（`time % 60000 == 0`）；不传秒级时间戳或日期字符串。时间含糊时先确认。
- `status=0` 可用 `title=""`、`content=""`、时间 `0`、`venue="{}"` 占位；封面仍必填，已填时间仍需符合顺序约束。
- `status=1` 必须有非空白标题、介绍、有效场地组合、开始和结束时间，且 `endTime > startTime > 0`。
- 非零报名截止不能晚于结束时间，不能早于有效发布时间；定时发布提交审核时必须满足 `0 < publishTime < startTime`。报名截止允许晚于开始时间，不要擅自强制按开始时间截止。
- `status=1` 表示提交审核，不是保证立即发布成功；创建后可用 `get-management.md` 查询当前实际状态。

### venue 场地结构

以下为 **`venue` 字符串解析后的结构**：`notice` 另行通知、`online` 线上、`offline` 线下；线上线下可组合为混合活动，`notice` 不可与其他节点共存。不额外提交 `type`。

提交审核时至少一个节点，节点值必须是 object，不能是字符串、数组或 `null`；仅草稿允许 `{}`。内部字段均为 string，无逐项必填和长度校验，按已知信息填写：

| 字段路径 | JSON 类型 | 填写内容 |
| --- | --- | --- |
| `notice.title` | string | 通知名称，如「地点另行通知」 |
| `notice.content` | string | 补充说明，如通知时间或获取方式 |
| `online.title` | string | 平台 / 参与方式名称，如「腾讯会议」 |
| `online.content` | string | 参与指引，如会议号、密码、入会说明 |
| `online.url` | string | 实际线上入口链接；不要用它代替会议说明或线下地址 |
| `offline.title` | string | 场地名称，如「远湾创新中心 3F」；统一用 `title`，不使用 `name` 别名 |
| `offline.content` | string | 补充指引，如入口、电梯、楼层、签到位置 |
| `offline.country` | string | 国家名称，如「中国」 |
| `offline.province` | string | 省 / 直辖市，如「广东省」 |
| `offline.city` | string | 城市，如「深圳市」 |
| `offline.district` | string | 区 / 县，如「南山区」 |
| `offline.address` | string | 街道、门牌等详细地址；不要把省市区或场地名混写到不相干字段 |
| `offline.longitude` | string | 经度的十进制字符串，如 `"113.950000"`，不是纬度 |
| `offline.latitude` | string | 纬度的十进制字符串，如 `"22.540000"`，不是经度 |

不猜测行政区、坐标、链接或密码；无法可靠拆分的地址保留在 `address`。地点未定时确认是否用 `notice`。

以下是线下场地的**序列化前对象**，不能直接作为 `venue` 字段发送：

```json
{
  "offline": {
    "title": "远湾创新中心 3F",
    "content": "从北门进入，在三楼签到",
    "country": "中国",
    "province": "广东省",
    "city": "深圳市",
    "district": "南山区",
    "address": "示例路 100 号",
    "longitude": "113.950000",
    "latitude": "22.540000"
  }
}
```

## 请求体示例

示例为个人活动、无门槛、不绑定表单、地点另行通知并提交审核。执行前替换实际 ID 和时间，并确认可见范围、报名审核、名额及地点设置；示例值不是默认选择。

```json
{
  "organizer": {
    "entityType": 1,
    "entityId": "019d0a18-1234-733f-acc2-92b8507b7003",
    "uid": "019d0a18-1234-733f-acc2-92b8507b7002",
    "visible": 1
  },
  "coOrganizers": [],
  "hosts": [],
  "title": "周末技术交流",
  "content": "交流技术与协作经验",
  "bannerImageIds": [
    "019d0a18-1234-733f-acc2-92b8507b7777"
  ],
  "formId": "",
  "prerequisite": 0,
  "prerequisiteData": "{}",
  "requireApproval": 0,
  "participantLimit": 0,
  "allowOverLimit": 0,
  "publishMode": 0,
  "publishTime": 0,
  "signupEndTime": 0,
  "startTime": 1800003600000,
  "endTime": 1800010800000,
  "venueVisibility": 0,
  "venue": "{\"notice\":{\"title\":\"地点另行通知\"}}",
  "scope": 1,
  "rosterVisibility": 2,
  "status": 1
}
```

创建请求超时或返回 HTTP 5xx 后不要直接重试；先用 `list-created-events.md` 核对是否已创建，必要时用 `get-management.md` 对比详情。无法确认时停止并让用户决定，避免重复创建。

## 响应示例

```json
{
  "code": 0,
  "msg": "ok",
  "data": {
    "eid": "019d0a18-1234-733f-acc2-92b8507b7001"
  }
}
```

成功后保存 `eid` 用于查询与管理；本接口不返回 `qrCodeImageId`，不要照搬圈子简化接口的响应字段。
