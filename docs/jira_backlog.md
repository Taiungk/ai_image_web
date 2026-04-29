# AIGC Studio Jira Backlog（可执行版）V1.2

## 1. Epic清单
1. Auth
2. Generate + Queue
3. Model Gateway
4. Subscription
5. Wallet/Billing
6. Payment
7. Assets
8. Admin
9. Observability
10. Compliance
11. QA/Release

## 2. Story粒度建议（最小可交付）
- 每个Story控制在1~2人天。
- 每个Story必须有：接口定义、验收标准、回归点。
- 涉及计费/支付的Story统一加 `billing-critical` 标签。

## 3. 6周里程碑
### Sprint 1（1-2周）
- 登录/鉴权
- 任务提交/队列消费
- 至少1图模型+1视频模型打通
- 资产入库基础能力

### Sprint 2（3-4周）
- 钱包与扣费引擎
- 支付充值与订阅购买
- 账单查询
- 运营后台基础管理

### Sprint 3（5-6周）
- 监控告警完善
- 风控合规上线
- 端到端回归、压测、灰度发布

## 4. Definition of Done（DoD）
- 代码合并前：单测通过、接口文档更新、关键日志完整。
- 提测前：自测清单完成、回归用例补齐。
- 上线前：灰度验证通过，阻塞项全部清零。

## 5. 上线阻塞项（任一未过不可上线）
- 支付回调幂等通过
- 扣费账实一致通过
- 失败返还机制通过
- 至少一个备通道可用
- 监控告警可用
- 协议与隐私政策上线

## 6. 建议标签
- v1-must
- billing-critical
- payment-critical
- security
- ops
