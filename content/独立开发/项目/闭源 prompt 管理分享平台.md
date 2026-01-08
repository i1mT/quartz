# 背景

用户创作 prompt 应该作为个人的资产，但是在社交网络上分享只能明文共享出来。
对于一些价值比较高的prompt，分享出来之后，是无法追溯别人的 复制、修改等使用的；同时也丧失了个人对于创作出的 prompt 的版权。

# 功能设计

## prompt 管理

可以管理自己的 prompt，展示prompt的案例。
可以选择公开 或者 私密分享 或 私密：
- 公开：明文公开，任何人可以复制使用
- 私密分享：可以在平台上输入参数，将参数拼接到prompt中，在平台后端执行（不在前端、网络层展示prompt），直接返回结果
- 私密：仅用户个人可查看

## 私密调用

对于私密分享的 prompt ，可以在平台私密调用。

## prompt as a service (paas)

将 prompt 看做一个 api 服务，可以提供给别人调用。

## 流水线编辑

在 pass 的基础上，可以将平台的所有的 service 编排成流水线，完成比较复杂的任务

## 经济机制

用户使用需要消耗 token，记为积分。
用户可以通过分享 prompt 来获取积分奖励，每次被使用被奖励的积分数按照下面公式计算：

award = (action_use_token - provider_token) * profit_ratio

其中:
- action_use_token 为用户在使用该prompt时，消耗的 token。也就是用户消费的费用。
- provider_token 为供应商的 token 数量，也就是成本价格。
- profit_ratio 是盈利比例。action_use_token - provider_token 就是在一次用户使用时，平台赚取的

对于平台来说，只需要满足下面公式即可：

用户