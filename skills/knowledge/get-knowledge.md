# get-knowledge — 获取知识库基本信息

`GET /open/knowledges/{knowledgeId}` · 鉴权：Bearer apiKey · 要求知识库查看权限

`knowledgeId` 为必填路径参数，格式为 UUIDv7；圈子知识库与圈子共用同一个 ID，即 `cid`。无 Query 参数和请求体。

## 响应字段

| data 字段 | 说明 |
| --- | --- |
| knowledgeId | 知识库 ID |
| ownerType | 所属类型，当前 `1` 表示圈子 |
| owner | 所属圈子：`cid`、`name`、`shortName`、`slogan`、`logoImageId` |
| permissions.query | 当前用户是否具有知识库查看权限，boolean |
| permissions.manage | 当前用户是否具有知识库管理权限，boolean；管理权限包含查看权限 |

圈主、圈内管理员拥有查看和管理权限；其他用户须有相应知识库授权。普通圈子成员或可阅读公开文章，不代表可使用这些管理接口。缺少权限或圈子不可用时返回 HTTP 403。
