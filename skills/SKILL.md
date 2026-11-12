---
name: zhaomian-skills
version: 20261112210925
description: 照面小程序 — 账号引导与验证码、个人名片、图片上传、标签/问答/社媒/履历管理、圈子搜索与加入、活动搜索、报名、取消报名、个人活动创建与管理、知识库分组/目录/文章管理
---

# 照面小程序

通过远湾开放平台 Open API 调用照面接口，为 AI Agent 提供账号、资料、圈子、活动与知识库能力。

## 更新 Skill

重新执行安装命令即可更新到最新版本：

```bash
npx skills add https://cnb.cool/farbay/zhaomian.git -g -y
```

## 按需加载

1. 先在下方「接口索引」按用户说法或接口名定位接口，**只读该接口对应的 `.md`**，不要一次性读取 `skills/` 下的全部文档；调用前必须读完，禁止凭字段名或经验猜测参数与枚举；
2. 按需补读：处理动态表单时读 `dynamic-form.md`；处理图片时读 `image/upload-image.md`；发送短信 / 邮箱验证码时读 `account/send-captcha.md`。查询接口引用了结果字段文档时一并读取。

## 接口索引

| 接口 | 常用说法 | 方法 | 路径 | 文档 |
|------|----------|------|------|------|
| 获取账号信息 | 我的账号 | GET | `/open/account` | `account/get-account.md` |
| 获取 Onboarding 意图码表 | 我的意图是什么 / 有哪些意图 | GET | `/open/basic/onboarding-intent-codes` | `account/get-intent-codes.md` |
| 更新用户意图 | 换个意图 | PUT | `/open/account/intent` | `account/update-intent.md` |
| 确认 Onboarding 完成 | 引导做完了吗 | PUT | `/open/account/onboarding-confirmed` | `account/confirm-onboarding.md` |
| 获取未读消息总数 | 有未读消息吗 | GET | `/open/user/unread-counts` | `account/get-unread-counts.md` |
| 发送短信 / 邮箱验证码 | 发短信验证码 / 表单要邮箱验证码 | POST | `/open/basic/mobile/captcha`、`/open/basic/email/captcha` | `account/send-captcha.md` |
| 获取个人名片 | 看看我的名片 | GET | `/open/user/profile` | `profile/get-profile.md` |
| 创建或修改名片 | 改名字（昵称）、简介、头像 / 创建名片 | POST | `/open/user/profile` | `profile/upsert-profile.md` |
| 分页获取拍一拍互动名片 | 我拍过谁 / 谁拍过我 | GET | `/open/user/tickles` | `interaction/list-tickles.md` |
| 分页获取标签认可互动名片 | 我认可过谁 / 谁认可过我 | GET | `/open/user/tag-approvals` | `interaction/list-tag-approvals.md` |
| 分页获取关注互动名片 | 我关注了谁 / 我的粉丝 | GET | `/open/user/follows` | `interaction/list-follows.md` |
| 分页获取印象互动名片 | 我给谁写过印象 / 谁给我写过印象 | GET | `/open/user/impressions` | `interaction/list-impressions.md` |
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
| 创建个人活动 | 创建个人活动 | POST | `/open/events` | `event/create-event.md` |
| 修改活动 | 修改活动 | PUT | `/open/events/{eid}` | `event/update-event.md` |
| 更新活动状态 | 更新活动状态 | PUT | `/open/events/{eid}/status` | `event/update-status.md` |
| 更新活动报名表 | 更新活动报名表 | PUT | `/open/events/{eid}/form` | `event/bind-form.md` |
| 查询我创建的活动 | 查询我创建的活动 | GET | `/open/events/created` | `event/list-created-events.md` |
| 获取活动管理详情 | 获取活动管理详情 | GET | `/open/events/{eid}/management` | `event/get-management.md` |
| 查询活动报名名单 | 查询活动报名名单 | GET | `/open/events/{eid}/submissions` | `event/list-submissions.md` |
| 创建动态表单 | 创建活动报名表 / 圈子表单 | POST | `/open/basic/dynamic-forms` | `event/create-form.md` |
| 获取动态表单结构 | 获取当前或历史报名表结构 | GET | `/open/basic/dynamic-forms/{formId}` | `event/get-form.md` |
| 生成活动签到凭证 | 生成活动签到凭证 | POST | `/open/events/{eid}/check-in-scenes/{source}` | `event/generate-check-in.md` |
| 搜索活动 | 最近有什么活动 | GET | `/open/events/search` | `event/search-events.md` |
| 查询已参加活动列表 | 我报名了哪些活动 | GET | `/open/events/signed-up` | `event/list-signed-up-events.md` |
| 查询活动报名最近记录 | 我的报名通过了吗 | GET | `/open/events/{eid}/submissions/latest` | `event/get-latest-submission.md` |
| 报名参加活动 | 报名这个活动 | POST | `/open/events/{eid}/submissions` | `event/signup-event.md` |
| 取消活动报名 | 取消报名 / 撤回报名 / 不参加了 | DELETE | `/open/events/{eid}/submissions/me` | `event/cancel-signup.md` |
| 创建分组、目录和文章结点 | 建分组 / 建目录 / 写文章 | POST | `/open/knowledges/{knowledgeId}/nodes` | `knowledge/create-node.md` |
| 编辑分组、目录和文章结点 | 改分组 / 改目录 / 编辑文章 | PUT | `/open/knowledges/{knowledgeId}/nodes/{nodeId}` | `knowledge/edit-node.md` |
| 删除分组、目录和文章结点 | 删分组 / 删目录 / 删文章 | DELETE | `/open/knowledges/{knowledgeId}/nodes/{nodeId}` | `knowledge/delete-node.md` |
| 移动目录和文章结点 | 移动目录 / 移动文章 / 调整顺序 | PUT | `/open/knowledges/{knowledgeId}/nodes/{nodeId}/move` | `knowledge/move-node.md` |
| 保存文章草稿 | 保存草稿 | PUT | `/open/knowledges/{knowledgeId}/nodes/{nodeId}/draft` | `knowledge/save-draft.md` |
| 文章发布操作 | 发布文章 / 下架 / 重新上架 | POST | `/open/knowledges/{knowledgeId}/nodes/{nodeId}/publication` | `knowledge/publication.md` |
| 删除文章草稿 | 丢弃草稿 | DELETE | `/open/knowledges/{knowledgeId}/nodes/{nodeId}/draft` | `knowledge/delete-draft.md` |
| 分页获取文章 | 知识库有哪些文章 / 我的草稿 | GET | `/open/knowledges/{knowledgeId}/articles` | `knowledge/list-articles.md` |
| 分页获取文章版本 | 查看文章历史版本 | GET | `/open/knowledges/{knowledgeId}/nodes/{nodeId}/histories` | `knowledge/list-histories.md` |
| 获取知识库基本信息 | 知识库属于哪个圈子 / 我有什么权限 | GET | `/open/knowledges/{knowledgeId}` | `knowledge/get-knowledge.md` |
| 获取知识库结点树 | 查看所有分组、目录和文章 | GET | `/open/knowledges/{knowledgeId}/nodes` | `knowledge/get-nodes.md` |
| 获取指定版本内容 | 查看某个版本正文 | GET | `/open/knowledges/{knowledgeId}/nodes/{nodeId}/histories/{historyId}` | `knowledge/get-history.md` |
| 获取分组、目录和文章结点详情 | 查看分组 / 目录 / 文章详情 | GET | `/open/knowledges/{knowledgeId}/nodes/{nodeId}` | `knowledge/get-node.md` |

### 规范文档

- `dynamic-form.md` — 动态表单组件、校验、联动、审核策略、唯一标识与 `formInput` 提交 / 存储格式
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

- 名片创建 / 修改、圈子申请和活动报名均传当前工具名称，如 `pi`、`codex`；无枚举或白名单，不带版本号，不额外加后缀。
- 服务端归一化为小写中划线格式（≤64 字符）。仅无工具名的手写调用使用 `open`，不要用 `api` 等来源描述。
- 省略或传空串时，名片记为 `open`，圈子申请和活动报名记为空字符串；这是接口默认行为，不是调用建议。

## 响应格式

- 统一返回 `{"code": 0, "msg": "ok", "data": ...}`；`code` 为 `0` 表示成功，非 0 时 `msg` 是中文提示，直接转述
- 失败时可能附带 `det`（数组），为动态表单等结构化错误明细
- 成功响应中的 `null`、`{}` 或 `[]` 表示无数据；查无记录也可能返回业务错误码，以具体接口为准
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
| `151` | 当前没有有效报名记录 | 可能未报名或已取消，不要解释为从未报名 |
| `170` | 动态表单不存在 | 重新查询圈子 / 活动后再提交 |
| `171` | 社媒平台不存在 | 说明该平台暂未接入 |
| `173` `174` `178` | 知识库、结点或文章版本不可用 | 重新查询目标，不跨知识库或文章替换 ID |
| `175` | 结点类型不正确 | 按结点类型选择操作 |
| `176` | 同层名称已存在 | 修改标题或选择其他父结点 |
| `177` | 草稿不存在 | 重新读取文章详情 |
| `179` | 当前草稿已变化 | 重新读取详情并确认草稿后再发布 |

其余非 0 业务码直接转述 `msg`，不要自行解释成其他含义。

## 分页

- 分页接口的 `count` 必传，建议取 `10`，不是服务端默认值；知识库文章与版本列表范围为 1–200，其余为 1–100。`circles/search`、`circles/joined`、`events/search`、知识库文章与版本列表的 `next` 可省略，第一页建议传空字符串；已参加活动、互动名片、活动管理列表的 `next` 必传。后续页只传上一页的 `data.page.next`，空字符串表示结束，不拼接或猜测游标
- 搜索、已加入圈子、已参加活动、互动名片、知识库文章与版本列表返回 `data.list`、`data.page`，`data.total` 仅第一页返回；我创建的活动和活动报名名单不返回 total。第一页按实际数量提示，由用户决定是否继续
- 用户资料类列表（`user/tags`、`user/qas`、`user/socials`、`user/resumes`）不分页：一次返回全量 `data` 数组，没有 `page` 和 `total`，`count`、`next` 传了也不生效
- 默认只取第一页：用户没说「还有吗」「下一页」「全部列出来」时，禁止自动翻页、连翻多页或循环拉取（仅适用于搜索类接口）

## 写操作

- 创建、修改、删除、报名、申请等写操作，在参数齐全后先向用户复述一遍即将提交的内容，确认后再调用
- 删除类操作必须先确认目标（用列表编号让用户选择），不要凭 ID 猜测
- 活动报名在已有待审核或已通过记录时返回原记录；圈子申请在已加入或已有待处理申请时返回业务提示，不直接返回原记录。超时后先查询最近记录，不因不会重复创建就无条件重试，验证组件也可能需要重新获取真实验证码 / 授权码
- 网络超时或 HTTP 5xx 可按接口文档决定是否重试；创建活动先查询结果，禁止直接重试。HTTP 4xx 和业务码非 0 时不要原样重试，先按 `msg` 修正
- 取消活动报名的授权与超时处理以 `event/cancel-signup.md` 为准，避免重复确认或误取消新报名

### ⚠️ 知识库修改：先预览，确认后提交

- 所有知识库写操作（创建、编辑、保存草稿、移动、删除、发布、下架、重新上架）都必须先展示操作预览，等待用户明确确认后，才能调用写接口。用户首次提出“帮我修改……”只是修改需求，不代表已确认预览。
- 修改已有内容前，先读取当前内容；预览须展示目标、修改前后差异及影响。新增文章展示拟提交的标题和正文；移动、删除、发布等操作展示目标及影响范围，不能只说“将按要求修改”。
- 预览仅在对话中展示，**不得为了生成预览先调用写接口，也不得先保存草稿再询问用户**。
- 用户确认仅授权本次预览中的内容与操作。只确认修改或保存草稿，不代表同意发布；若预览明确包含“修改并发布”，确认后可执行这两步。
- 用户继续提出调整时，须更新预览并重新等待确认；实际提交内容或影响范围与已确认预览不一致时，也须重新确认。未确认前只允许查询和准备内容。

## 不支持的能力

以下能力不在本 Skill 接口范围内，引导用户到「照面」小程序完成，不编造接口：

- 撤回圈子申请
- 删除活动或名片（活动支持编辑、取消和归档）
- 切换或管理多张名片（开放 API 只能读写默认名片）
- 查看消息内容（只能查询未读数量）
- 上传普通文件（动态表单 `File` 组件没有对应的文件上传接口）
- 获取微信手机号授权码（动态表单 `MobileVerify` 需在小程序授权，短信验证码不可替代）
- 除短信 / 邮箱验证码之外的账号安全操作（改密码、注销等）

## 通用规则

1. **时间数据**：`xxxTime` 字段均为毫秒级 Unix 时间戳，展示时按 `Asia/Shanghai` 转 `YYYY-MM-DD HH:mm:ss`（如 `1789018856000` → `2026-09-10 13:40:56`），不直接展示原始数字；`-1`、`0` 等哨兵值按对应接口文档解释。用户给自然语言时间（如「下周三」「2024 年 3 月」）时先按 `Asia/Shanghai` 解析成具体时刻再转毫秒，只给日期按当天 `00:00:00` 计算
2. **文案字段**：圈子 `name`、`description`、活动 `title`、`content` 及表单 `label` 均为字符串
3. **图片数据**：提交业务接口时只传 `imageId`（UUIDv7），禁止提交 URL 或本地路径；展示图片时按 `image/upload-image.md` 组装 CDN 预览地址，不直接展示 ID。Codex Markdown 对远程图片（`https://`）存在跨平台预览问题，需要在回复中展示远程图片时先下载到临时文件，再用本地绝对路径引用；不要直接把 CDN/远程 URL 写入 Markdown 图片语法
4. **动态表单**：按 `dynamic-form.md` 处理当前结构、填写值和历史版本，不凭经验拼字段
5. **结果展示**：列表用编号，枚举转中文；空结果如实说明当前无匹配或有效记录，不推断从未添加、加入或报名，不套固定回复
6. **上下文衔接**：记住已查询的 cid / eid / pid / tagId / qaId / resumeId / knowledgeId / nodeId / historyId，后续操作无需用户重复提供；不要向用户复述这些已缓存的标识，也不要说明它们已经可用
7. **隐私**：只展示接口实际返回的内容，不推断、不补充他人未公开的信息
8. **下一步引导**：拿到结果后主动问一句要不要继续相关动作（看活动、加入圈子、补全资料等），一次最多给一到两个选项；只做引导，不自动搜索、不自动报名或申请；同一方向引导过就不再重复；接口文档另有规定时以接口文档为准
