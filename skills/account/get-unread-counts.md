# get-unread-counts — 获取未读消息总数

`GET /open/user/unread-counts` · 鉴权：Bearer apiKey

返回当前用户未归档私信和群聊的未读消息总数，统计口径与 App 会话列表一致。

## 响应

| 字段 | 类型 | 描述 |
|------|------|------|
| `userConversationUnreadCount` | number | 未归档私信未读消息总数；没有未读时为 `0` |
| `userTopicConversationUnreadCount` | number | 未归档群聊未读消息总数；没有未读时为 `0` |

## 注意

- 分别展示私信和群聊未读数；都为 `0` 时回复「当前没有未读消息」。
- 只能查数量，不能查消息内容；用户想看消息内容时说明开放 API 不支持，引导到 App 查看。
