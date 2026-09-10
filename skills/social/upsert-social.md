# upsert-social — 创建或更新社媒账号

`PUT /open/user/socials/{platformCode}` · 鉴权：Bearer apiKey

绑定或更新当前用户某个社媒平台的账号，同一平台重复提交为覆盖更新。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `platformCode` | string | 是 | 平台编码，小写字母开头，仅含小写字母、数字、下划线，必须是下表中已接入的平台 |
| `content` | string | 是 | 社媒账号内容，最长 255 字，不能为空 |

`platformCode` 走路径参数，`content` 走 body。

## platformCode 平台编码

| 分类 | 编码 |
|------|------|
| 内容创作 | `youtube`、`substack`、`medium`、`gongzhonghao`、`bilibili`、`rednote`、`xiao_yu_zhou_fm`、`zhihu`、`tiktok`、`shipinhao`、`lofter`、`afdian`、`buy_me_a_coffee`、`ko_fi`、`mbd` |
| 开发技术 | `github`、`gitlab`、`stackoverflow`、`gitee`、`product_hunt`、`hugging_face`、`vercel` |
| 设计视觉 | `behance`、`dribbble`、`figma`、`pinterest`、`art_station`、`deviant_art`、`five_hundred_px` |
| 学术研究 | `foundation`、`google_scholar`、`orcid`、`research_gate`、`academia` |
| 社交社区 | `x`、`linked_in`、`instagram`、`facebook`、`weibo`、`jike`、`threads`、`steam`、`mastodon`、`bsky` |
| 音乐 | `music163`、`spotify` |

## 注意

- 平台列表可能随线上配置增加；用户提到的平台不在上表时，无法确认编码就告知暂不支持，不要自造编码。
- 小红书是 `rednote`（不是 `xiaohongshu`），X 是 `x`（不是 `twitter`）。
- 报「社媒平台不存在」时，说明该平台暂未接入，建议换一个平台。

## 示例

```bash
curl -X PUT "${FARBAY_OPEN_HOST}/open/user/socials/rednote" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"content": "farbay_creator"}'
```
