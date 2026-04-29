# AIGC Studio 技术设计文档（可开发版）V1.1

日期：2026-04-29

## 1. 总体架构
- Web/App：Next.js
- 服务层：Auth / Generation / Billing / Wallet / Payment / Asset / Admin API
- 异步层：BullMQ（队列+Worker）
- 数据层：PostgreSQL + Redis
- 文件层：S3兼容对象存储 + CDN
- 可观测：Prometheus + Grafana + Sentry

## 2. 核心模块职责
1. Model Gateway：统一模型抽象、路由、熔断、降级、重试。  
2. Generation：任务创建、参数校验、入队、状态查询。  
3. Billing：预扣冻结、成功实扣、失败返还。  
4. Payment：订单创建、回调验签、幂等入账。  
5. Asset：资产入库、签名下载、删除恢复、生命周期清理。

## 3. 状态机
### 3.1 任务状态
`pending -> running -> succeeded | failed | cancelled`

### 3.2 订单状态
`created -> paid | failed | closed`，可选 `paid -> refunded`

## 4. 幂等策略
- 任务提交：`Idempotency-Key`（客户端UUID）
- 支付回调：`channel + order_no + channel_txn_no`
- 结算幂等：`task_id + settle_stage`
- 实施：唯一索引 + 事务 + Redis去重

## 5. 一致性策略
- 强一致（本地事务）：
  - 钱包余额变更 + 钱包流水
  - 任务终态变更 + 结算流水
- 最终一致（异步补偿）：
  - 超时任务返还
  - 已支付未到账补单
  - 异常冻结自动解冻

## 6. 路由与熔断
- 每个模型配置主/备渠道。
- 主渠道失败触发一次备通道重试。
- 连续失败超过阈值进入熔断窗口（30s）。
- 熔断期间仅走备通道并持续健康探测。

## 7. 超时与并发
- 图像任务：120s超时
- 视频任务：600s超时
- 并发上限：Free1 / Basic2 / Pro4 / Business10
- 队列分级：图像高优先、图像普通、视频高优先、视频普通

## 8. 监控告警
- 核心指标：任务成功率、支付回调失败率、队列积压、渠道错误率。
- 告警阈值：
  - 任务成功率 <95%（5分钟）
  - 渠道错误率 >20%（3分钟）
  - 队列积压 >1000（10分钟）
