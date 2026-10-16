# create-event — 创建圈子活动

`POST /open/circles/{cid}/events` · 鉴权：Bearer apiKey

当前用户须已有名片，且为圈子管理员或有 `event.info.update` 权限；可先查 `search-circles.md` / `list-joined-circles.md` 的 `eventCreateAllowed`，无权限返回 HTTP `403`。

这是固定默认设置的简化接口。需要草稿或自定义下方默认设置时，使用 `../event/create-event.md` 的完整接口并将 organizer 设为圈子；不要混用请求字段。

## 请求

Path `cid`：实际查询得到的主办方圈子 UUID v7，不是名称、uid 或 pid。

Body 以下字段全部必传且不能为 `null`；integer 为 JSON 整数，不是字符串或布尔值。

| 字段 | JSON 类型 | 说明 |
| --- | --- | --- |
| `title` | string | 非空白标题，最多 128 字 |
| `content` | string | 非空白介绍，最多 10000 字，不传国际化对象 |
| `coverImageId` | string | 单个非空 UUID v7；先按 `../image/upload-image.md` 上传 `eventBanner` 图片，不传数组、URL 或本地路径 |
| `startTime` | integer | 开始毫秒时间戳，`> 0`；同时作为报名截止时间，不能早于服务端当前发布时间 |
| `endTime` | integer | 结束毫秒时间戳，必须 `> startTime` |
| `venue` | string | **JSON 对象字符串**，结构见下方 |
| `scope` | integer | `0` 私有（创建者及分享链接 / 二维码可见，不是仅自己可见），`1` 公开；不是地点可见性 |
| `requireApproval` | integer | 报名审核：`0` 无需，`1` 需要；不是活动发布审核 |

时间按 `Asia/Shanghai` 解析，精确到分钟（`time % 60000 == 0`），不传秒级时间戳或日期字符串。

### 场地

构造前必须读取 `../event/create-event.md` 的「venue 场地结构」，按其中字段语义填写场地名、参与指引、线上链接、地址和坐标，不猜测未知信息。地点已确定时使用 `offline` 节点，必须提供非空的 `offline.longitude` 和 `offline.latitude`，缺失时先补齐再提交；主办方明确地点待定时使用 `notice`（如「线下地点待定」），无需经纬度，可以创建。不要仅因缺少坐标就擅自改为地点待定。

本接口不支持空场地：至少包含 `notice`、`online`、`offline` 中一个对象节点；`offline` 与 `online` 可组合，`notice` 不可与其他节点共存。将场地对象序列化为字符串后放入 `venue`，不要直接传嵌套对象；请求示例见下方。

## 固定默认设置（无需提交）

- 主办方为路径 cid；创建者为当前用户，当前默认名片自动成为主理人，无联合举办方。
- 状态为待审核 `1`，立即发布；实际状态由后续流转决定，不保证返回时已发布。
- 报名截止为 `startTime`；无圈子门槛、人数上限或超限报名，不绑定报名表。
- 封面列表为 `[coverImageId]`；地点报名后可见，报名名单不可见。

## 响应与重试

| 字段 | 说明 |
| --- | --- |
| `data.eid` | 新建活动 ID，保存用于查询与管理 |
| `data.qrCodeImageId` | 二维码图片 ID，生成失败时为 `""`；非空时按图片文档组装预览地址 |

成功后提示已提交审核；实际状态可用 `../event/get-management.md` 查询。超时或 HTTP 5xx 后禁止直接重试，先按 `../event/list-created-events.md` 核对，无法确认则停止并让用户决定，避免重复创建。

## 示例

执行前替换实际圈子 / 图片 ID 和用户确认的未来活动时间，并确认场地、可见范围与报名审核设置；示例值不是默认选择。

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/circles/${CID}/events" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "远湾技术沙龙",
    "content": "围绕技术协作的线下交流活动。",
    "coverImageId": "019d0a18-1234-733f-acc2-92b8507b7777",
    "startTime": 1800003600000,
    "endTime": 1800010800000,
    "venue": "{\"offline\":{\"title\":\"远湾创新中心 3F\",\"country\":\"中国\",\"province\":\"上海市\",\"city\":\"上海市\",\"district\":\"浦东新区\",\"address\":\"示例路 100 号\",\"longitude\":\"121.500000\",\"latitude\":\"31.230000\"}}",
    "scope": 1,
    "requireApproval": 0
  }'
```
