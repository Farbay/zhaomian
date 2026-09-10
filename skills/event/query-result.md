# 活动查询结果字段

`search-events.md` 与 `list-signed-up-events.md` 的 `data.list[]` 使用同一结构。

| 字段 | 类型 | 描述 |
|------|------|------|
| `eid` | string | 活动 ID |
| `title` | string | 标题；没有时为空字符串 |
| `content` | string | 详情摘要；没有时为空字符串 |
| `qrCodeImageId` | string | 活动二维码 Image ID；暂时生成失败时为空字符串 |
| `startTime` | number | 开始时间，毫秒时间戳 |
| `endTime` | number | 结束时间，毫秒时间戳 |
| `status` | number | 活动状态，见下表 |
| `formId` | string | 报名表单 ID；未绑定时为空字符串 |
| `formJson` | object | 报名表单结构；未绑定时为 `{}` |
| `createTime` | number | 创建时间，毫秒时间戳 |
| `signupStatus` | number | 当前用户是否已报名：`1` 是、`0` 否 |
| `checkInStatus` | number | 当前用户是否已签到：`1` 是、`0` 否 |
| `buttonStatus` | number | 当前用户可执行动作，见下表 |
| `venueVisibility` | number | `0` 地点公开、`1` 报名后可见 |
| `venue` | object | 完整地点；不可见时为 `{}` |
| `venueText` | string | 地点文案；不可见时为区域、「线上参与」或「另行通知」 |
| `memberCount` | number | 正常活动成员数 |
| `hosts` | array | 主理人关系列表；无数据时为 `[]` |
| `organizer` | object \| null | 主办方关系；无数据时为 `null` |
| `coOrganizers` | array | 联合主办方关系列表；无数据时为 `[]` |

## status

| 值 | 说明 |
|----|------|
| `0` | 草稿 |
| `1` | 待审核 |
| `2` | 待发布 |
| `3` | 已发布 |
| `4` | 报名中 |
| `5` | 报名截止 |
| `6` | 进行中 |
| `7` | 已取消 |
| `8` | 已结束 |
| `9` | 已归档 |
| `-2147483648` | 未知 |

搜索接口只返回 `3`、`4`、`5`、`6`、`8`；已参加列表可能返回其他历史状态。

## buttonStatus

| 值 | 说明 |
|----|------|
| `0` | 未知 / 当前不可报名 |
| `1` | 活动结束 |
| `2` | 活动取消 |
| `3` | 已签到 |
| `5` | 审核中 |
| `6` | 候补中 |
| `7` | 待签到 |
| `8` | 报名成功 |
| `9` | 进行中 |
| `10` | 已报满 |
| `11` | 可候补 |
| `12` | 报名截止 |
| `13` | 报名 |

`4` 是已停用的历史值，当前接口不会返回。

## 活动关系

`hosts[]`、`organizer`、`coOrganizers[]` 的结构：

| 字段 | 类型 | 描述 |
|------|------|------|
| `relationType` | number | `1` 主办方、`2` 联合主办方、`3` 主理人 |
| `entityType` | number | `1` 用户名片、`2` 圈子 |
| `sort` | number | 排序值，越大越靠前 |
| `entityResult` | object | 对应的用户名片或圈子信息，结构见下方 |

### entityType=1（用户名片）

| 字段 | 类型 | 描述 |
|------|------|------|
| `pid` | string | 名片 ID |
| `uid` | string | 用户 ID |
| `nickname` | string | 昵称 |
| `avatarImageId` | string | 头像 Image ID |
| `identity` | string | 身份描述 |
| `isSelf` | number | 是否为当前用户：`1` 是、`0` 否 |
| `userIntersectionLatestResult` | object \| null | 与当前用户的最近交集；无交集时为 `null` |

`userIntersectionLatestResult` 包含 `uid`、`targetUid`、`intersectionId`、`intersectionType`、`intersectionKey`、`createTime`、`intersectionContent`、`intersectionResult`、`memberInfo`。`intersectionResult` 随交集类型返回简化名片、圈子或活动对象；`memberInfo.items[]` 包含 `label`、`field`、`value`。

### entityType=2（圈子）

| 字段 | 类型 | 描述 |
|------|------|------|
| `cid` | string | 圈子 ID |
| `categoryId` | number | 圈子分类 ID |
| `name` | string | 圈子名称 |
| `shortName` | string | 圈子简称 |
| `slogan` | string | 圈子口号 |
| `description` | string | 圈子介绍 |
| `logoImageId` | string | 圈子 Logo Image ID |
| `visibility` | number | 圈子可见性 |
| `identityStatus` | number | 当前用户圈子身份状态 |
| `submissionStatus` | number | 当前用户圈子申请状态 |

这里的圈子是关系里的简化结构，没有 `formId`、`formJson`、`eventCreateAllowed`；要申请加入或创建活动，需按 `../circle/search-circles.md` 或 `../circle/list-joined-circles.md` 重新查询圈子。
