# list-resumes — 获取用户所有有效履历

`GET /open/user/resumes` · 鉴权：Bearer apiKey

返回当前用户的有效履历，并补全主体名称和限定名称；没有有效履历时 `data` 为 `[]`。

## 响应

| 字段 | 类型 | 描述 |
|------|------|------|
| `resumeId` | number | 履历 ID |
| `resumeType` | number | 履历类型，见 `resume-types.md` |
| `parentResumeId` | number | 父级履历 ID；顶级为 `0` |
| `subject` | string | 主体名称，如公司、学校；没有时为空字符串 |
| `qualifier` | string | 限定名称，如职位、专业；没有时为空字符串 |
| `classifier` | string | 分类维度，如职位类型、学位；未填写时为空字符串 |
| `startTime` | number | 开始时间，毫秒时间戳；未填写时为 `0` |
| `endTime` | number | 结束时间，毫秒时间戳；`-1` 表示至今；未填写时为 `0` |
| `location` | string | 地点或城市；未填写时为空字符串 |
| `description` | string | 描述说明；未填写时为空字符串 |
| `resource` | array | 附件信息；未填写时为 `[]` |
| `relation` | array | 关联信息；未填写时为 `[]` |
| `extra` | object | 扩展信息；未填写时为 `{}` |
| `createTime` | number | 创建时间，毫秒时间戳 |
| `updateTime` | number | 更新时间，毫秒时间戳 |

`resource`、`relation`、`extra` 是只读字段，创建 / 更新时不需要提交，见 `create-resume.md`。

## 注意

- 按 `resumeType` 分组展示主体、限定、时间区间、地点和描述；用编号方便用户选择后续更新或删除。
- `endTime=-1` 显示「至今」，时间字段为 `0` 时表示未填写。
- 没有履历时回复「你还没有添加履历，要不要加一段经历？」。
