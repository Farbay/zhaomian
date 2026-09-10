---
name: zhaomian-skills
description: 照面助手 — 账号引导与验证码、个人名片、图片上传、标签/问答/社媒/履历管理、圈子搜索与加入、活动搜索与报名
---

# 照面助手

通过远湾开放平台 Open API 调用照面接口，为 AI Agent 提供账号、资料、圈子与活动能力。

## 更新 Skill

重新执行安装命令即可更新到最新版本：

```bash
npx skills add farbay/zhaomian -g
```

## 按需加载

1. 先在下方「接口索引」按用户说法或接口名定位接口，**只读该接口对应的 `.md`**，不要一次性读取 `skills/` 下的全部文档；调用前必须读完，禁止凭字段名或经验猜测参数与枚举；
2. 按需补读：提交表单前读 `dynamic-form.md`；出现 `imageId`、`xxxImageId(s)`、`cdnUrl` 时读 `image/upload-image.md`；发送或提交验证码时读 `account/send-captcha.md`。

## 接口索引

| 接口 | 常用说法 | 方法 | 路径 | 文档 |
|------|----------|------|------|------|
| 获取账号信息 | 我的账号 | GET | `/open/account` | `account/get-account.md` |
| 获取 Onboarding 意图码表 | 我的意图是什么 / 有哪些意图 | GET | `/open/basic/onboarding-intent-codes` | `account/get-intent-codes.md` |
| 更新用户意图 | 换个意图 | PUT | `/open/account/intent` | `account/update-intent.md` |
| 确认 Onboarding 完成 | 引导做完了吗 | PUT | `/open/account/onboarding-confirmed` | `account/confirm-onboarding.md` |
| 获取未读消息总数 | 有未读消息吗 | GET | `/open/user/unread-counts` | `account/get-unread-counts.md` |
| 发送短信 / 邮箱验证码 | 填表单要手机号或邮箱验证码 | POST | `/open/basic/mobile/captcha`、`/open/basic/email/captcha` | `account/send-captcha.md` |
| 获取个人名片 | 看看我的名片 | GET | `/open/user/profile` | `profile/get-profile.md` |
| 创建或修改名片 | 改昵称、简介、头像 / 创建名片 | POST | `/open/user/profile` | `profile/upsert-profile.md` |
| 图片上传（凭证 + OSS 直传） | 传头像 / 传封面 / 表单要传图 | POST + PUT | `/open/basic/image/upload` | `image/upload-image.md` |
| 获取用户所有标签 | 我有哪些标签 | GET | `/open/user/tags` | `tag/list-tags.md` |
| 创建标签 | 加一个技能 / MBTI / 家乡标签 | POST | `/open/user/tags` | `tag/create-tag.md` |
| 更新标签 | 改标签 | PUT | `/open/user/tags/{tagId}` | `tag/update-tag.md` |
| 删除标签 | 删标签 | DELETE | `/open/user/tags/{tagId}` | `tag/delete-tag.md` |
| 获取用户所有问答 | 我有哪些问答 | GET | `/open/user/qas` | `qa/list-qas.md` |
| 创建问答 | 加一条问答 / 雷区 | POST | `/open/user/qas` | `qa/create-qa.md` |
| 更新问答 | 改问答 | PUT | `/open/user/qas/{qaId}` | `qa/update-qa.md` |
| 删除问答 | 删问答 | DELETE | `/open/user/qas/{qaId}` | `qa/delete-qa.md` |
| 获取用户所有社媒账号 | 我绑了哪些社媒 | GET | `/open/user/socials` | `social/list-socials.md` |
| 创建或更新社媒账号 | 绑定我的小红书 / GitHub | PUT | `/open/user/socials/{platformCode}` | `social/upsert-social.md` |
| 删除社媒账号 | 解绑小红书 | DELETE | `/open/user/socials/{platformCode}` | `social/delete-social.md` |
| 获取用户所有有效履历 | 我有哪些履历 | GET | `/open/user/resumes` | `resume/list-resumes.md` |
| 创建履历 | 加一段工作或教育经历 | POST | `/open/user/resumes` | `resume/create-resume.md` |
| 更新履历 | 改履历 | PUT | `/open/user/resumes/{resumeId}` | `resume/update-resume.md` |
| 删除履历 | 删履历 | DELETE | `/open/user/resumes/{resumeId}` | `resume/delete-resume.md` |
| 履历类型与字段语义 | 履历有哪些类型 / 每段填什么 | — | — | `resume/resume-types.md` |
| 搜索公开圈子 | 找圈子 | GET | `/open/circles/search` | `circle/search-circles.md` |
| 查询已加入圈子列表 | 我加入了哪些圈子 | GET | `/open/circles/joined` | `circle/list-joined-circles.md` |
| 查询圈子申请最近记录 | 我的申请通过了吗 | GET | `/open/circles/{cid}/submissions/latest` | `circle/get-latest-submission.md` |
| 申请加入圈子 | 加入这个圈子 | POST | `/open/circles/{cid}/submissions` | `circle/join-circle.md` |
| 创建圈子活动 | 在圈子里办个活动 | POST | `/open/circles/{cid}/events` | `circle/create-event.md` |
| 搜索活动 | 最近有什么活动 | GET | `/open/events/search` | `event/search-events.md` |
| 查询已参加活动列表 | 我报名了哪些活动 | GET | `/open/events/signed-up` | `event/list-signed-up-events.md` |
| 查询活动报名最近记录 | 我的报名通过了吗 | GET | `/open/events/{eid}/submissions/latest` | `event/get-latest-submission.md` |
| 报名参加活动 | 报名这个活动 | POST | `/open/events/{eid}/submissions` | `event/signup-event.md` |

### 规范文档

- `dynamic-form.md` — 动态表单结构、联动规则与 `formInput` 提交格式
- `image/upload-image.md` — 图片上传凭证、OSS 直传与 `imageId` 使用规范
- `account/send-captcha.md` — 短信 / 邮箱验证码的发送与在表单中的提交格式
- `circle/query-result.md` — 圈子列表字段与状态枚举
- `event/query-result.md` — 活动列表字段、状态枚举与活动关系结构

---

# 调用规范

## 统一入口

所有接口都在开放平台 Host 的 `/open` 下，形如 `${FARBAY_OPEN_HOST}/open/...`。`FARBAY_OPEN_HOST` 从环境变量读取；未设置时提示用户 `export FARBAY_OPEN_HOST=<开放平台 Host>`，不要猜测地址。

## 鉴权

- Header：`Authorization: Bearer ${FARBAY_OPEN_API_KEY}`，Key 形如 `fb-sk-...`
- Key 只从环境变量读取，不写死、不入库、不在对话中完整回显；未设置时提示 `export FARBAY_OPEN_API_KEY=<你的apikey>`（登录开放平台站点，微信扫码后在首页「接入配置」复制）
- Key 已绑定用户身份（uid），需要用户身份的接口自动注入，无需手动传
- 缺失或失效返回 HTTP 401 `{"code":1,"msg":"Authorized Failed"}`：不要重试相同请求，提示用户检查或刷新 Key

## 请求格式

REST 风格，Method / Path 见接口索引，Query 参数按接口定义，Body 为 JSON（`Content-Type: application/json`）。

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/user/tags" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"tagType": 4, "content": "活动策划", "description": "", "sort": 0}'
```

图片上传是唯一例外：凭证里的 `uploadAddress` 必须用 `PUT` 直传二进制文件，详见 `image/upload-image.md`。

## channel 渠道标识

`channel` 记录「是谁调用的」，值是调用方当前所用工具的名称；它是**自报字段**，后端只按字符串存储（≤64 字符），无枚举、无白名单校验，任何工具都传自己的名字，不需要事先登记。

- 用工具名本身，小写、不带版本号、不加 `-agent` / `-client` 后缀：如 Codex 传 `codex`（示例仅示范格式，实际传当前工具名）
- 接口定义了 `channel` 就一律传，不要省略；不要传 `open`、`api` 这类来源描述，只有确实没有工具名时才传 `open`（如手写 curl）
- 省略或传空串时的落库值以各接口文档为准：名片记为 `open`，圈子申请、活动报名记为空字符串

带 `channel` 的接口：`profile/upsert-profile.md`、`circle/join-circle.md`、`event/signup-event.md`。

## 响应格式

- 统一返回 `{"code": 0, "msg": "ok", "data": ...}`；`code` 为 `0` 表示成功，非 0 时 `msg` 是中文提示，直接转述
- 失败时可能附带 `det`（数组），为动态表单等结构化错误明细
- 单条记录查不到时 `data` 是 `null` 或 `{}`，列表无数据时是 `[]`，都不是接口错误
- HTTP 层错误：`400 Params Not Valid`、`401 Authorized Failed`、`403 Forbidden`

### 需要区分处理的业务码

| 业务码 | 含义 | 处理方式 |
|--------|------|----------|
| `100` | 账号不存在 | 提示用户确认开放平台账号与 API Key |
| `110` | 用户名片不存在 | 按 `profile/upsert-profile.md` 创建名片 |
| `112` `113` `115` `116` | 问答 / 标签 / 履历 / 社媒不存在 | 重新查询列表拿最新 ID，不要沿用旧 ID |
| `121` `122` | 履历实体已被使用 / 用户履历已被引用 | 提示用户先解除引用再删除 |
| `127` | 简介未通过语义检测 | 直接转述 `msg`，引导用户改写简介 |
| `131` | 圈子不存在 | 重新搜索圈子 |
| `133` | 圈子申请不存在 | 说明用户还没申请过该圈子 |
| `150` | 活动不存在 | 重新搜索活动 |
| `151` | 活动报名申请不存在 | 说明用户还没报名该活动 |
| `170` | 动态表单不存在 | 重新查询圈子 / 活动后再提交 |
| `171` | 社媒平台不存在 | 说明该平台暂未接入 |

其余非 0 业务码直接转述 `msg`，不要自行解释成其他含义。

## 分页

- 列表接口统一游标分页：`count`（1-100）必传，默认 `10`
- `next` 一律显式传：第一页传空字符串，后续页传上一页的 `data.page.next`；为空字符串表示没有下一页，不要自行拼接或猜测游标
- 默认只取第一页：用户没说「还有吗」「下一页」「全部列出来」时，禁止自动翻页、连翻多页或循环拉取
- 第一页返回 `data.total`，按「共 N 个，已显示前 10 个」提示，由用户决定是否继续

## 写操作

- 创建、修改、删除、报名、申请等写操作，在参数齐全后先向用户复述一遍即将提交的内容，确认后再调用
- 删除类操作必须先确认目标（用列表编号让用户选择），不要凭 ID 猜测
- 报名、申请类接口在已有有效记录时会直接返回原记录，不会重复创建，重试是安全的
- 网络超时或 HTTP 5xx 可以按相同参数重试一次；HTTP 4xx 和业务码非 0 时不要原样重试，先按 `msg` 修正

## 不支持的能力

开放 API 只覆盖「读写自己的账号与资料」和「搜索、申请圈子与活动」。以下诉求没有对应接口，需要引导用户到「照面」App 完成，不要猜测路径或编造接口：

- 取消活动报名、撤回圈子申请
- 删除或修改已创建的活动、删除名片
- 切换或管理多张名片（开放 API 只能读写默认名片）
- 查看消息内容（只能查询未读数量）
- 上传普通文件（动态表单 `File` 组件没有对应的文件上传接口）
- 除短信 / 邮箱验证码之外的账号安全操作（改密码、注销等）

## 通用规则

1. **时间数据**：`xxxTime` 字段均为毫秒级 Unix 时间戳，展示时按 `Asia/Shanghai` 转 `YYYY-MM-DD HH:mm:ss`（如 `1789018856000` → `2026-09-10 13:40:56`），不直接展示原始数字；`-1`、`0` 等哨兵值按对应接口文档解释。用户给自然语言时间（如「下周三」「2024 年 3 月」）时先按 `Asia/Shanghai` 解析成具体时刻再转毫秒，只给日期按当天 `00:00:00` 计算
2. **文案字段**：圈子 `name`、`description`，活动 `title`、`content` 等文案字段接口已统一返回字符串，直接展示即可，不要按多语言对象解析
3. **图片数据**：提交业务接口时只传 `imageId`（UUIDv7），禁止提交 URL 或本地路径；展示图片时按 `image/upload-image.md` 组装 CDN 预览地址，不直接展示 ID
4. **动态表单**：`formInput` 必须严格按返回的 `formJson` 构造，隐藏字段禁止提交，详见 `dynamic-form.md`
5. **结果展示**：列表用编号展示方便用户选择；枚举值转成中文，不要直接展示数字；空结果时给出引导语
6. **上下文衔接**：记住已查询的 cid / eid / pid / tagId / qaId / resumeId，后续操作无需用户重复提供
7. **隐私**：只展示接口实际返回的内容，不推断、不补充他人未公开的信息
