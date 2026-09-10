# create-event — 创建圈子活动

`POST /open/circles/{cid}/events` · 鉴权：Bearer apiKey

以路径中的圈子为主办方创建活动，直接进入「待审核」状态；创建者和主理人为当前登录用户。uid 由 API Key 解析。

## 请求

### Path

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `cid` | string | 是 | 主办方圈子 ID |

### Body

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `title` | string | 是 | 活动标题，最长 128 字 |
| `content` | string | 是 | 活动详细介绍，最长 10000 字 |
| `coverImageId` | string | 是 | 封面图 ID，先用 `eventBanner` 类型按 `../image/upload-image.md` 上传 |
| `startTime` | number | 是 | 开始时间，毫秒时间戳 |
| `endTime` | number | 是 | 结束时间，毫秒时间戳，必须大于开始时间 |
| `venue` | object | 是 | 场地详情 JSON，见下方说明 |
| `scope` | number | 是 | 可见范围：`0` 私有、`1` 公开 |
| `requireApproval` | number | 是 | 是否需要报名审核：`0` 无需审核、`1` 需要审核 |

### venue 场地结构

| 节点 | 说明 |
|------|------|
| `offline` | 线下场地对象，可含 `title` / `name`、`province`、`city`、`district`、`address` |
| `online` | 线上活动对象，可含 `title` |
| `notice` | 「另行通知」对象，可含 `title` |

规则：`offline`、`online`、`notice` 至少要有一个；`notice` 不能与其他节点共存。用户说的具体地点从「省 / 市 / 区」拆到对应字段，只给一句话地址时放到 `address`。

## 响应

| 字段 | 描述 |
|------|------|
| `data.eid` | 新建活动 ID |
| `data.qrCodeImageId` | 活动二维码 Image ID；图片服务暂时生成失败时为空字符串 |

HTTP `403` 表示当前用户不是圈子管理员，也没有该圈子的活动信息编辑权限。

## 服务端默认回填

以下字段不需要提交：报名截止时间（等于开始时间）、活动状态（待审核 `1`）、主办方（路径中的 cid）、创建者 / 主理人（当前用户）、封面图列表（`[coverImageId]`）、报名门槛（无）、人数上限（无）、发布模式（立即发布）、地点可见性（报名后可见）、报名名单可见性（不可见）、报名表单（未绑定）。

## 注意

1. 只有圈子管理员，或拥有该圈子 `event.info.update` 权限的用户才能创建；创建前可先看 `search-circles.md` / `list-joined-circles.md` 返回的 `eventCreateAllowed`。
2. 封面必须先用 `../image/upload-image.md` 的 `eventBanner` 类型上传，业务接口只接受 `imageId`。
3. 创建成功后提示活动已提交审核，并记录返回的 `eid`；`qrCodeImageId` 非空时展示二维码预览。

## 示例

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/circles/${CID}/events" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "远湾技术沙龙",
    "content": "围绕技术协作的线下交流活动。",
    "coverImageId": "019d0a18-1234-733f-acc2-92b8507b7777",
    "startTime": 1741425600000,
    "endTime": 1741432800000,
    "venue": { "offline": { "title": "远湾创新中心 3F", "city": "上海", "district": "浦东新区" } },
    "scope": 1,
    "requireApproval": 0
  }'
```
