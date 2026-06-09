# 网络与请求（Http）

> updated_by: Cascade - Cascade
> updated_at: 2026-05-06 11:40:00

- **网络与请求（Http）**：
  - 是否有统一的请求客户端与分层封装（base client / service / api modules），避免页面直调
  - 请求入参与响应的类型/Schema 是否有约束（TS 类型、运行时校验、转换层），避免隐式字段漂移
  - 是否有统一的超时、重试、取消（AbortController）、并发控制与防抖/合并策略
  - 错误处理是否标准化（错误码映射、业务错误 vs 系统错误、用户提示口径）
  - 是否支持可观测性（requestId、链路追踪、耗时统计、失败原因分桶）
  - 安全与合规（敏感 header/参数脱敏、证书/HTTPS、token 注入边界）
  - **审查报告**：`/.plans/架构质量审查-320-Utils-Http.md`
