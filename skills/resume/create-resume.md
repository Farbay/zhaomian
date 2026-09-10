# create-resume — 创建用户履历

`POST /open/user/resumes` · 鉴权：Bearer apiKey

各类型的字段含义与常用取值见 `resume-types.md`，先读它再收集字段。

## 请求

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `resumeType` | number | 是 | `1`-`8`，见 `resume-types.md` |
| `parentResumeId` | number | 是 | 父级履历 ID，顶级填 `0` |
| `subject` | string | 是 | 主体名称，最长 255 字 |
| `qualifier` | string | 是 | 限定名称，最长 255 字，允许空字符串 |
| `classifier` | string | 是 | 分类维度，最长 128 字，允许空字符串 |
| `startTime` | number | 是 | 开始时间，毫秒时间戳 |
| `endTime` | number | 是 | 结束时间，毫秒时间戳；`-1` 表示至今 |
| `location` | string | 是 | 地点或城市，最长 128 字，允许空字符串 |
| `description` | string | 是 | 描述说明，最长 255 字，允许空字符串 |

`resource`、`relation`、`extra` 由服务端回填默认值（`[]`、`[]`、`{}`），**不需要提交**。

## 响应

| 字段 | 描述 |
|------|------|
| `data.resumeId` | 新建履历 ID |

## 注意

- `subject` 必须是非空名称；`qualifier`、`classifier` 必须提交，确实不适用时传空字符串。
- 教育履历按「学校 + 专业」去重：同组合重复创建会更新已有履历，不会新增。
- 「至今」用 `endTime=-1`。
- 创建成功后记录返回的 `resumeId`，供后续更新 / 删除使用。

## 示例

```bash
curl -X POST "${FARBAY_OPEN_HOST}/open/user/resumes" \
  -H "Authorization: Bearer ${FARBAY_OPEN_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "resumeType": 1,
    "parentResumeId": 0,
    "subject": "远湾科技",
    "qualifier": "后端开发工程师",
    "classifier": "全职",
    "startTime": 1704038400000,
    "endTime": -1,
    "location": "上海",
    "description": "负责用户域与履历模块开发"
  }'
```
