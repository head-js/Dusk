# 可观测（Observability）

> updated_by: HBR - GPT-5
> updated_at: 2026-05-20 17:16:35

## 审查目标

- 判断前端应用是否具备可观测性基础能力，包括 Trace、日志、指标、告警与错误监控。
- 讨论和框架 Trace 有关的内容，明确 TraceId / SpanId 的生成、传播、日志关联、跨服务透传与采样策略。
- 讨论是否使用 SaaS 观测方案，如 DataDog、观测云或同类 APM/日志/指标平台。
- 识别可观测性链路是否覆盖应用入口、网络拦截器、页面生命周期、外部 HTTP 调用、异常处理与错误返回。

## 核心关注点

- **框架 Trace**：是否使用 OpenTelemetry、SkyWalking、Pinpoint、Firebase Performance、观测云 FT SDK、Sentry 或其它 Trace/APM SDK。
- **Trace 传播**：TraceId / SpanId 是否在应用入口生成并通过 HTTP Header、日志上下文向下游服务透传；Android 端作为请求链路起点，是否在 OkHttp/Retrofit Interceptor 中注入 Trace Header。
- **日志关联**：请求日志、业务日志、异常日志是否包含 TraceId、method、path、userId、错误码、耗时等可检索字段；日志模块（如 Lib-Log）是否与 Trace/APM SDK 联动。
- **指标与告警**：是否采集请求 QPS、延迟、错误率、下游依赖耗时、ANR、崩溃率、启动耗时、页面渲染耗时、内存/线程/网络指标。
- **SaaS 方案**：是否接入 DataDog、观测云、Firebase Crashlytics、Sentry、New Relic 或其它 SaaS 观测平台；配置、Agent、API Key、Endpoint 与环境隔离是否清晰。
- **敏感信息治理**：Trace、日志、APM tag、错误事件中是否可能泄露 Token、手机号、身份证、银行卡、设备指纹等敏感信息。
- **运行时一致性**：本地、测试、生产的 Trace 开关、采样率、日志级别、上报地址、告警规则是否一致或有明确差异说明。

## 审查产物输出确认

- **核心关注点审查报告**：
  - `/.plans/架构质量审查-130-可观测.md`

- **整体审查报告（必须覆盖全部内容）**：
  - 必须覆盖本 Phase 全量整体审查目标与核心关注点
  - 不能仅做条目汇总；必须给出跨主题的全局整合视角（Trace 链路完整性、日志与指标联动、SaaS 方案治理、敏感信息风险、风险优先级、治理路线图）
  - 整体审查报告需在收敛阶段补齐跨主题整合结论。
  - **整体审查报告**：`/.plans/架构质量审查-130-可观测.md`

## 技术栈补充参考

<!-- // 根据项目技术栈（如 Android、iOS、Web 等），从 references/ 中提取对应的可观测性审查补充参考，包含补充审查目标与补充关注点 -->
