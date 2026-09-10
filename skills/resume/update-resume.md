# update-resume — 更新用户履历

`PUT /open/user/resumes/{resumeId}` · 鉴权：Bearer apiKey

## 请求

### Path

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `resumeId` | number | 是 | 履历 ID |

### Body

与 `create-resume.md` 相同：`resumeType`、`parentResumeId`、`subject`、`qualifier`、`classifier`、`startTime`、`endTime`、`location`、`description`。`resource`、`relation`、`extra` 不需要提交，服务端会保留原值。

**更新是覆盖式的**，不要只传要改的字段；body 不需要传 `resumeId`，以路径里的为准。

## 注意

1. 先用 `list-resumes.md` 或上下文拿到 `resumeId`。
2. 用户只说要改某一项时，用列表里现有值补齐其余字段后再提交。
3. 履历不存在时返回业务码 `115`。

## 示例

```bash
curl -X PUT "${FARBAY_OPEN_HOST}/open/user/resumes/${RESUME_ID}" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "resumeType": 1,
    "parentResumeId": 0,
    "subject": "远湾科技",
    "qualifier": "技术负责人",
    "classifier": "全职",
    "startTime": 1704038400000,
    "endTime": -1,
    "location": "上海",
    "description": "负责后端开发"
  }'
```
