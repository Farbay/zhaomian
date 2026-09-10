# confirm-onboarding — 确认 Onboarding 完成状态

`PUT /open/account/onboarding-confirmed` · 鉴权：Bearer apiKey

Body 传 `{}`，使用当前 API Key 绑定账号确认完成引导。

## 确认条件

**只有同时满足以下三个条件时才能确认成功**，任一未满足会返回「请先完成账号引导」：

1. 已加入圈子（`GET /open/circles/joined` 有记录）；
2. 已创建名片（`GET /open/user/profile` 返回 `pid`）；
3. 已收集意图（`intent` 非空，且为二级意图）。

## 工作流

1. 调 `get-account.md` 查看当前 `onboardingConfirmed`，已为 `1` 时无需重复调用。
2. 逐项检查三件前置事项，缺哪项补哪项：
   - 加入圈子：`../circle/list-joined-circles.md`，没有则按 `../circle/search-circles.md` + `../circle/join-circle.md` 加入；
   - 创建名片：`../profile/get-profile.md`，没有则按 `../profile/upsert-profile.md` 创建；
   - 收集意图：`get-intent-codes.md` + `update-intent.md`。
3. 三项完成后调用本接口，再重新查询账号确认 `onboardingConfirmed=1`。

## 注意

- 逐项说明「已加入圈子 / 已创建名片 / 已收集意图」的完成状态，未完成时给出下一步操作建议。
- 确认成功回复「Onboarding 已完成」。
