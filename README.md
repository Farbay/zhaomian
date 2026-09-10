# 照面 Skills

为 AI Agent 提供「照面」开放能力的 Skill 集合，支持账号引导、名片资料、标签/问答/社媒/履历、圈子与活动搜索和报名等能力。

> 项目名：`zhaomian`（照面拼音，`zm` 为其缩写）。
> 每个接口一个独立文档，Agent 按需加载；完整接口索引见 [`skills/SKILL.md`](skills/SKILL.md)。

## 安装

```bash
npx skills add farbay/zhaomian -g
```

### 一键安装提示词

把下面这段提示词发给任意支持 Skills 的 Agent（Claude Code、Codex、Cursor 等）即可完成安装：

```text
请帮我安装照面 Skills：
npx skills add farbay/zhaomian -g
```

## 配置

使用前需要配置开放平台 Host 与 API Key 两个环境变量：

```bash
export FARBAY_OPEN_HOST=https://open.zm.bio
export FARBAY_OPEN_API_KEY=fb-sk-xxxxxxxx
```

获取方式：

1. 打开开放平台站点 <https://open.zm.bio>
2. 使用微信扫码登录
3. 在首页「一键安装 Skill」复制提示词，其中已包含与你账号绑定的 Host 与 API Key；Key 泄露时可在同处点「刷新 Key」重新生成

> Host 与 API Key 均绑定你当前登录的开放平台站点与用户身份（uid），需要的用户身份接口会自动注入，无需手动传入。
> 真实 API Key 只保存在本地环境变量中，禁止写入仓库或提交远程。

## 使用

安装后直接用自然语言与 Agent 对话即可：

```
"看看我的名片"
"给我加一个技能标签：活动策划"
"最近有什么技术活动"
"帮我申请加入这个圈子"
"这个活动还能报名吗"
"看看我的个人简介和履历"
```

## 功能

| 能力 | 说明 | 文档目录 |
|------|------|----------|
| 账号与引导 | 账号信息、Onboarding 意图、完成确认、未读消息、短信/邮箱验证码 | [`skills/account/`](skills/account/) |
| 个人名片 | 查看、创建、修改个人名片 | [`skills/profile/`](skills/profile/) |
| 图片上传 | 上传凭证、OSS 直传、imageId 使用规范 | [`skills/image/`](skills/image/) |
| 标签管理 | 标签的增删改查 | [`skills/tag/`](skills/tag/) |
| 问答管理 | 普通问答与雷区问答的增删改查 | [`skills/qa/`](skills/qa/) |
| 社媒账号 | 社媒账号的绑定与解绑 | [`skills/social/`](skills/social/) |
| 履历管理 | 职业、教育等履历的增删改查（含类型字段语义） | [`skills/resume/`](skills/resume/) |
| 圈子 | 搜索公开圈子、已加入圈子、申请加入、创建活动 | [`skills/circle/`](skills/circle/) |
| 活动 | 搜索活动、已参加活动、报名 | [`skills/event/`](skills/event/) |
| 动态表单 | 表单结构、联动规则与提交格式 | [`skills/dynamic-form.md`](skills/dynamic-form.md) |

## 版本

当前版本：**0.1.0**

## 许可证

[Apache-2.0](./LICENSE) — Copyright © 2026 Farbay
