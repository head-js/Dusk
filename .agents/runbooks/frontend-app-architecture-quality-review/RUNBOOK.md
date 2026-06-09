---
name: frontend-app-architecture-quality-review
description: This skill should be used when performing comprehensive architecture quality reviews for front-end applications. It provides systematic workflows for analyzing build & dependency management, base framework, utilities, business modules, and third-party integrations. The actual tech stack should be derived from the current project.
metadata:
  version: 0.0.3
---

# 前端应用架构质量审查计划（Skill）

> updated_by: Cascade - Cascade
> updated_at: 2026-05-15 18:08:00

## 技能概述

本技能指导 AI 助手对“前端应用”进行系统性的架构质量审查。审查应以当前项目事实为准（代码、依赖、构建配置、运行形态），并将结论输出到仓库的 `.plans/` 目录中。

## 使用时机

**重要说明：** 本技能专门用于项目级架构质量审查，关注整体架构设计、模块划分、依赖关系等架构层面的问题。

在以下情况下应该使用此技能：

**核心使用场景 - 架构审查：**

- 需要对整个前端应用进行架构质量审查
- 评估项目架构设计的合理性和可维护性
- 识别架构层面的设计问题和改进机会
- 评估技术选型和架构决策的合理性
- 项目重构前的架构评估

## 审查维度（通用）

本技能从前端应用的六个核心构成部分出发，对架构质量进行系统审查：可观测（Observability）、Build（构建与依赖管理）、基础框架（Base Framework）、Utils（工具层）、业务组件/模块、第三方库（集成与隔离）。

说明：本 Skill 的"前端应用"是对交互型客户端的抽象，既包括 Web（浏览器）、也包括各类客户端运行形态（如 iOS/Android、Hybrid/小程序等）。不同技术栈不改变审查结构，均按同一套六个 Phase 进行 review；`references/` 仅提供不同栈的落地口径样例。

### Phase 0 可观测（Observability）

#### 0.1 整体审查目标

- 判断前端应用是否具备可观测性基础能力，包括 Trace、日志、指标、告警与错误监控。
- 讨论和框架 Trace 有关的内容，明确 TraceId / SpanId 的生成、传播、日志关联、跨服务透传与采样策略。
- 讨论是否使用 SaaS 观测方案，如 DataDog、观测云或同类 APM/日志/指标平台。
- 识别可观测性链路是否覆盖应用入口、网络拦截器、页面生命周期、外部 HTTP 调用、异常处理与错误返回。
- 详细说明见：`templates/TASK-130-可观测.md`

#### 0.2 核心关注点

- **框架 Trace**：是否使用 OpenTelemetry、SkyWalking、Pinpoint、Firebase Performance、观测云 FT SDK、Sentry 或其它 Trace/APM SDK。
- **Trace 传播**：TraceId / SpanId 是否在应用入口生成并通过 HTTP Header、日志上下文向下游服务透传；Android 端作为请求链路起点，是否在 OkHttp/Retrofit Interceptor 中注入 Trace Header。
- **日志关联**：请求日志、业务日志、异常日志是否包含 TraceId、method、path、userId、错误码、耗时等可检索字段；日志模块（如 Lib-Log）是否与 Trace/APM SDK 联动。
- **指标与告警**：是否采集请求 QPS、延迟、错误率、下游依赖耗时、ANR、崩溃率、启动耗时、页面渲染耗时、内存/线程/网络指标。
- **SaaS 方案**：是否接入 DataDog、观测云、Firebase Crashlytics、Sentry、New Relic 或其它 SaaS 观测平台；配置、Agent、API Key、Endpoint 与环境隔离是否清晰。
- **敏感信息治理**：Trace、日志、APM tag、错误事件中是否可能泄露 Token、手机号、身份证、银行卡、设备指纹等敏感信息。
- **运行时一致性**：本地、测试、生产的 Trace 开关、采样率、日志级别、上报地址、告警规则是否一致或有明确差异说明。

#### 0.3 审查产物输出确认

- **核心关注点审查报告**：
  - `/.plans/架构质量审查-130-可观测.md`

- **整体审查报告（必须覆盖全部内容）**：
  - 必须覆盖本 Phase 全量整体审查目标与核心关注点
  - 不能仅做条目汇总；必须给出跨主题的全局整合视角（Trace 链路完整性、日志与指标联动、SaaS 方案治理、敏感信息风险、风险优先级、治理路线图）
  - 整体审查报告需在收敛阶段补齐跨主题整合结论。
  - **整体审查报告**：`/.plans/架构质量审查-130-可观测.md`

#### 0.4 Feature Flag / Deliver Flag / Config / 开关

##### 整体审查目标

- 判断前端应用是否具备统一的 Feature Flag / Deliver Flag / Config / 开关基础设施，明确各类控制机制的语义边界与适用场景。
- 审查 Flag/Config 的全生命周期管理：定义、获取、缓存、评估、降级、过期与清理，确保链路完整可控。
- 识别 Flag/Config 对应用启动时序、业务流程、安全合规与可观测性的影响，评估架构层面的风险与改进机会。
- 确保远程配置与本地开关之间的优先级、覆盖与回退策略清晰，避免因配置不一致导致的线上事故。
- 详细说明见：`templates/TASK-150-FF.md`

##### 核心关注点

- **分类与语义边界**：Feature Flag（功能开关）、Deliver Flag（投放开关）、Config（远程配置）、本地开关是否在概念与实现上清晰区分；是否存在统一抽象模型。
- **统一抽象与 API 设计**：是否存在类型安全的统一 Flag/Config 读取 API；Flag 评估逻辑是否集中；是否支持命名空间或分组。
- **远程获取与缓存策略**：获取时机是否影响启动性能；缓存有效期与刷新策略是否明确；远程获取失败时的回退策略是否覆盖。
- **灰度与分桶**：灰度策略是否由服务端决策；分桶依据是否稳定；跨会话/跨设备分桶一致性是否保证；A/B 实验结束后 Flag 是否及时清理。
- **时序与初始化**：Flag SDK 初始化时序是否安全；初始化完成前读取 Flag 是否返回安全默认值；Flag 变更后的实时生效范围是否明确。
- **默认值与降级**：每个 Flag/Config 是否有显式声明的合理默认值；降级路径是否经过测试验证。
- **安全与合规**：Flag/Config 中是否可能泄露敏感信息；远程配置通道是否加密；关键业务逻辑是否仅依赖服务端校验；调试开关是否在生产环境自动关闭。
- **生命周期管理**：Flag 的创建、使用、废弃是否有完整流程；是否存在僵尸开关；临时开关是否有自动过期机制；是否有全量清单与文档。
- **可观测性**：Flag 评估结果是否可追踪；远程配置获取是否可监控；是否支持远程调试；Flag 变更是否可审计。

##### 审查产物输出确认

- **核心关注点审查报告**：
  - `/.plans/架构质量审查-150-FF.md`

- **整体审查报告（必须覆盖全部内容）**：
  - 必须覆盖本 Phase 全量整体审查目标与核心关注点
  - 不能仅做条目汇总；必须给出跨主题的全局整合视角（分类体系完整性、远程-本地一致性、时序安全、灰度策略合理性、生命周期治理、风险优先级、治理路线图）
  - 整体审查报告需在收敛阶段补齐跨主题整合结论。
  - **整体审查报告**：`/.plans/架构质量审查-150-FF.md`

### Phase 1 Build（构建与依赖管理）

Build 关注构建工具链与打包配置结构清晰、职责单一、便于维护和扩展，控制依赖版本与冲突，清晰区分不同环境（开发 / 测试 / 生产）的构建配置，并优化构建性能。

详细说明见：`templates/TASK-199-Build.md`

### Phase 2 基础框架（Base Framework）

#### 2.1 整体审查目标

- 确保应用整体架构模式在各模块中得到一致、正确的实现，建立清晰的分层与边界。
- 通过统一的基础组件、基础能力与公共治理机制提升复用性与可维护性。
- 提供稳定的错误处理、日志、配置、鉴权、版本治理与全局整合机制，并明确主报告的全局整合作用。
- 详细说明见：`templates/TASK-299-基础框架.md`

#### 2.2 核心关注点

- **UI 框架与设计系统**：关注项目主体使用的 UI 框架/组件库、二次封装层，以及样式体系是否统一，是否存在样式污染与冲突。
  - 详细说明见：`templates/TASK-210-基础框架-UI.md`

- **骨架屏**：关注语义边界是否清晰，“首屏/路由切换/局部模块”三类加载态是否分层治理，骨架屏是否与最终 UI 结构一致，以及超时失败兜底、资源性能与可观测性是否完善。
  - 详细说明见：`templates/TASK-220-基础框架-骨架屏.md`

- **弹窗**：关注范围是否明确、统一入口是否存在、依赖方向是否正确、使用方式是否可治理，并关注并发与队列策略、生命周期关系、配置化、安全风控、可观测性闭环与用户体验。
  - 详细说明见：`templates/TASK-230-基础框架-弹窗.md`

- **登录与账号体系**：关注登录入口与鉴权链路、Token/Session 的存储策略、登录态续期/刷新、登出与失效处理，以及不同环境下鉴权策略的一致性。
  - 详细说明见：`templates/TASK-240-基础框架-登录.md`

- **IAM（用户身份与权限管理）**：关注用户登录身份与权限模型、菜单权限管理、接口权限管理、按钮权限管理、路由 Guard、默认拒绝策略、权限变更同步与安全审计。
  - 详细说明见：`templates/TASK-245-基础框架-IAM.md`

- **检查更新与版本治理**：关注更新策略、阻断时机、频控与用户体验、下载与安装链路、安全与合规，以及可观测性闭环。
  - 详细说明见：`templates/TASK-250-基础框架-检查更新.md`

- **Bridge（Native-WebView 桥接）**：关注 App 往 WebView 注入 Bridge 能力的统一性与安全性，以及与原生交互的协议设计、调用约定、类型安全、错误处理、版本兼容与可观测性是否完善。
  - 详细说明见：`templates/TASK-260-基础框架-Bridge.md`

#### 2.3 审查产物输出确认

- **核心关注点审查报告**：
  - `/.plans/架构质量审查-210-基础框架-UI.md`
  - `/.plans/架构质量审查-220-基础框架-骨架屏.md`
  - `/.plans/架构质量审查-230-基础框架-弹窗.md`
  - `/.plans/架构质量审查-240-基础框架-登录.md`
  - `/.plans/架构质量审查-245-基础框架-IAM.md`
  - `/.plans/架构质量审查-250-基础框架-检查更新.md`
  - `/.plans/架构质量审查-260-基础框架-Bridge.md`

- **整体审查报告（必须覆盖全部内容）**：
  - 必须覆盖本 Phase 全量整体审查目标与核心关注点
  - 不能仅做条目汇总；必须给出跨主题的全局整合视角（分层边界、基础能力复用、全局治理、风险优先级、治理路线图）
  - 整体审查报告需在收敛阶段补齐跨主题整合结论。
  - **整体审查报告**：`/.plans/架构质量审查-299-基础框架.md`

### Phase 3 Utils（工具层）

#### 3.1 整体审查目标

- 将通用能力从业务中抽离为工具层，提升代码复用率，减少重复实现。
- 控制工具类的职责范围，避免形成难以测试和理解的"工具黑洞"。
- 确保工具层对框架运行时（响应式、生命周期、全局单例等）的依赖是清晰、可控且不会引入内存泄漏。
- 关注职责单一性、命名与组织、静态方法与全局状态、响应式使用、平台依赖与资源句柄，以及可测试性。
- 详细说明见：`templates/TASK-399-Utils.md`

#### 3.2 核心关注点

- **本地存储与缓存（Storage）**：关注存储分层、多套实现治理、Key/Schema 治理、数据生命周期、安全合规、性能可靠性，以及可观测性是否完善。
  - 详细说明见：`templates/TASK-310-Utils-Storage.md`

- **网络与请求（Http）**：关注统一请求客户端与分层封装、类型与 Schema 约束、超时重试取消与并发控制、错误处理标准化、可观测性，以及安全与合规边界。
  - 详细说明见：`templates/TASK-320-Utils-Http.md`

- **WebSocket**：关注连接管理、心跳重连与网络切换策略、消息协议版本化与验证、安全鉴权，以及可观测性是否完善。
  - 详细说明见：`templates/TASK-325-Utils-WebSocket.md`

- **日志（Log）**：关注日志分级与环境策略、统一日志 API 与结构化字段、敏感信息保护、持久化与上传兜底，以及与错误收集和性能指标的联动闭环。
  - 详细说明见：`templates/TASK-330-Utils-Log.md`

- **设备信息（Device Info）**：关注最小化采集、字段来源与口径统一、设备指纹相关合规约束、失败与权限受限场景兜底，以及可观测性建设。
  - 详细说明见：`templates/TASK-335-Utils-设备信息.md`

- **加解密**：关注算法与实现统一入口、敏感材料管理、编码与格式一致性、异常兼容策略，以及性能与安全权衡是否可控。
  - 详细说明见：`templates/TASK-340-Utils-加解密.md`

- **日期时间与时区（DateTime / Timezone）**：关注统一解析与格式化入口、时区与 Locale 策略、时间戳单位治理、关键边界场景覆盖，以及国家/地区展示规则的配置化。
  - 详细说明见：`templates/TASK-360-Utils-DateTime.md`

- **Currency**：关注金额表示方式、格式化与舍入规则、多币种与汇率来源，以及 i18n 与本地化展示的一致性。
  - 详细说明见：`templates/TASK-365-Utils-Currency.md`

- **Icon**：关注图标资源组织与命名、加载策略、主题与多态支持、性能与包体影响，以及可访问性是否考虑完整。
  - 详细说明见：`templates/TASK-367-Utils-Icon.md`

- **报表**：关注指标口径与数据源、查询与聚合性能、展示与导出格式、权限与审计，以及异常与边界处理是否明确。
  - 详细说明见：`templates/TASK-390-Utils-报表.md`

#### 3.3 审查产物输出确认

- **核心关注点审查报告**：
  - `/.plans/架构质量审查-310-Utils-Storage.md`
  - `/.plans/架构质量审查-320-Utils-Http.md`
  - `/.plans/架构质量审查-325-Utils-WebSocket.md`
  - `/.plans/架构质量审查-330-Utils-Log.md`
  - `/.plans/架构质量审查-335-Utils-设备信息.md`
  - `/.plans/架构质量审查-340-Utils-加解密.md`
  - `/.plans/架构质量审查-360-Utils-DateTime.md`
  - `/.plans/架构质量审查-365-Utils-Currency.md`
  - `/.plans/架构质量审查-367-Utils-Icon.md`
  - `/.plans/架构质量审查-390-Utils-报表.md`

- **整体审查报告（必须覆盖全部内容）**：
  - 必须覆盖本 Phase 全量整体审查目标与核心关注点
  - 不能仅做条目汇总；必须给出跨主题的全局整合视角（职责边界、依赖方向、复用策略、风险优先级、治理路线图）
  - 整体审查报告需在收敛阶段补齐跨主题整合结论。
  - **整体审查报告**：`/.plans/架构质量审查-399-Utils.md`

### Phase 4 业务组件（业务功能层）

#### 4.1 整体审查目标

- 确保 UI 组件/页面结构清晰、职责明确，避免臃肿和过多业务逻辑堆积。
- 实现视图与业务逻辑的合理分离（如通过复用逻辑单元、状态管理、service 层等），提升可维护性与扩展性。
- 在业务复杂场景下保证组件状态管理、路由导航与数据交互的可控与可测试。
- 覆盖组件复杂度、状态管理、视图与业务逻辑分离、路由与导航、列表与复杂 UI、异常与边界处理等整体性治理目标。
- 详细说明见：`templates/TASK-499-业务页面.md`

#### 4.2 核心关注点

- **首页**：关注首屏与冷启动体验是否可控，以及模块化与可配置性是否清晰。
  - 详细说明见：`templates/TASK-410-业务页面-首页.md`

- **主旅程**：关注关键路径是否可追溯，以及状态与副作用是否可控。
  - 详细说明见：`templates/TASK-420-业务页面-主旅程.md`

- **营销位**：关注配置化与风控边界是否清晰，以及展示与跳转协议是否统一。
  - 详细说明见：`templates/TASK-430-业务页面-营销位.md`

- **下单旅程**：关注表单/确认/支付等环节的状态机是否清晰，以及并发与一致性风险是否评估。
  - 详细说明见：`templates/TASK-440-业务页面-下单旅程.md`

- **品牌**：关注内容与资源治理是否可控，以及 SEO/分享/落地页协议（若适用）是否统一。
  - 详细说明见：`templates/TASK-450-业务页面-品牌.md`

- **用户协议**：关注合规链路是否闭环，以及跳转与拦截策略是否安全。
  - 详细说明见：`templates/TASK-460-业务页面-用户协议.md`

#### 4.3 审查产物输出确认

- **核心关注点审查报告**：
  - `/.plans/架构质量审查-410-业务页面-首页.md`
  - `/.plans/架构质量审查-420-业务页面-主旅程.md`
  - `/.plans/架构质量审查-430-业务页面-营销位.md`
  - `/.plans/架构质量审查-440-业务页面-下单旅程.md`
  - `/.plans/架构质量审查-450-业务页面-品牌.md`
  - `/.plans/架构质量审查-460-业务页面-用户协议.md`

- **整体审查报告（必须覆盖全部内容）**：
  - 必须覆盖本 Phase 全量整体审查目标与核心关注点
  - 不能仅做条目汇总；必须给出跨主题的全局整合视角（组件拆分策略、状态边界、路由与导航治理、风险优先级、治理路线图）
  - 整体审查报告需在收敛阶段补齐跨主题整合结论。
  - **整体审查报告**：`/.plans/架构质量审查-499-业务页面.md`

### Phase 5 第三方SDK（集成与隔离）

#### 5.1 整体审查目标

- 通过适配层或封装层对第三方SDK进行隔离，降低对业务代码的侵入与耦合。
- 确保第三方SDK的使用安全、合规，可控地采集数据并满足隐私与合规要求。
- 便于后续替换或升级第三方SDK，降低供应商锁定风险。
- 覆盖封装与抽象、初始化与生命周期、错误与降级、版本管理、权限与隐私、多库共存等整体性治理目标。
- 详细说明见：`templates/TASK-599-SDK.md`

#### 5.2 核心关注点

- **埋点/分析 SDK（行为分析、日志事件、崩溃与可观测性）**：关注技术栈边界、事件/用户体系、数据质量监控、可观测性闭环、性能与合规，以及事件协议演进治理。
  - 详细说明见：`templates/TASK-510-SDK-埋点.md`

- **归因/结算 SDK（投放效果归因与转化回传）**：关注技术栈边界、转化回传审计、口径统一、策略配置化、对账与合规，以及回传可靠性与成本控制。
  - 详细说明见：`templates/TASK-520-SDK-广告归因.md`

- **地图/定位 SDK（地理能力、POI、路线与地址解析）**：关注适配层隔离、权限与合规、稳定性兜底、风控与成本、性能影响，以及地址与坐标体系统一。
  - 详细说明见：`templates/TASK-530-SDK-地图.md`

- **消息推送 SDK（Google Firebase Push、极光等）**：关注推送能力抽象、厂商通道适配、Token 与设备绑定、通知权限与合规、到达率与可靠性、前后台处理、消息路由与安全，以及可观测性闭环。
  - 详细说明见：`templates/TASK-540-SDK-Push.md`

#### 5.3 审查产物输出确认

- **核心关注点审查报告**：
  - `/.plans/架构质量审查-510-SDK-埋点.md`
  - `/.plans/架构质量审查-520-SDK-广告归因.md`
  - `/.plans/架构质量审查-530-SDK-地图.md`
  - `/.plans/架构质量审查-540-SDK-Push.md`

- **整体审查报告（必须覆盖全部内容）**：
  - 必须覆盖本 Phase 全量整体审查目标与核心关注点
  - 不能仅做条目汇总；必须给出跨主题的全局整合视角（隔离策略、供应链风险、合规边界、风险优先级、治理路线图）
  - 整体审查报告需在收敛阶段补齐跨主题整合结论。
  - **整体审查报告**：`/.plans/架构质量审查-599-SDK.md`

### Phase 6 KYC（用户归一化与渠道归因）

#### 6.1 整体审查目标

- 确保用户在不同登录方式、设备、渠道之间可被正确识别与合并，归一化规则由服务端裁决、客户端仅做状态同步。
- 确保渠道归因参数（channel / source / utm_x）的采集、持久化、传递与覆盖策略统一、可审计、与广告归因 SDK 口径对齐。
- 覆盖用户归一化与渠道归因的边界场景、安全合规与可观测性。
- 详细说明见：`templates/TASK-610-KYC.md`

#### 6.2 核心关注点

- **用户归一化**：关注多登录方式身份识别与合并、设备更换/重装/多设备场景下的身份关联、合并/解绑/换绑策略、归一化标识全局一致性，以及登录态与归一化状态的联动。
  - 详细说明见：`templates/TASK-610-KYC.md`

- **渠道归因（channel / source / utm_x）**：关注归因参数采集入口统一性、持久化与生命周期策略、业务链路传递完整性、多来源冲突优先级与覆盖规则、安全合规，以及端到端可审计性。
  - 详细说明见：`templates/TASK-610-KYC.md`

#### 6.3 审查产物输出确认

- **核心关注点审查报告**：
  - `/.plans/架构质量审查-610-KYC.md`

- **整体审查报告（必须覆盖全部内容）**：
  - 必须覆盖本 Phase 全量整体审查目标与核心关注点
  - 不能仅做条目汇总；必须给出跨主题的全局整合视角（归一化与归因的联动、身份与渠道的关联、风险优先级、治理路线图）
  - 整体审查报告需在收敛阶段补齐跨主题整合结论。
  - **整体审查报告**：`/.plans/架构质量审查-699-KYC.md`

## 生成审查任务（TASK）

### 任务生成流程

为了指导 Agent 执行具体的架构质量审查，需要根据 `templates/` 中的模板生成对应的任务文件至 `.context/` 目录。

### 生成步骤

1. **确定任务编号与名称**
   - 根据审查维度确定任务编号（如 TASK-199-Build、TASK-210-基础框架-UI 等）
   - 任务名称应清晰反映审查内容

2. **提取模板内容**
   - 从 `templates/TASK-XXX.md` 中读取审查目标、关注点、输出要求等内容

3. **识别项目技术栈**
   - 分析当前项目的实际技术栈（如 Android、iOS、Web 等）
   - 从 `references/` 中查找对应的技术栈参考文件

4. **生成任务文件**
   - 在 `.context/TASK-XXX.md` 中创建任务文件
   - 包含以下结构：
     - **任务目标**：明确审查的目标与输出位置，并在开头写入如下固定提示词：
       ```
       本审查任务独立执行，仅基于当前项目代码与配置得出结论，不依赖、不参照其他 Phase 或其他 TASK 的审查报告。
       ```
     - **审查范围**：包括审查目标与核心关注点
     - **执行步骤**：项目分析 → 逐点审查 → 跨主题整合 → 报告生成
     - **报告输出要求**：明确输出路径与内容要求
     - **技术栈补充参考**：融入对应技术栈的特定审查要点

5. **补充技术栈参考**
   - 从 `references/` 中提取与当前任务相关的技术栈补充参考
   - 作为独立章节"技术栈补充参考"插入任务文件
   - 包含补充审查目标与补充关注点

### TASK 初始化清单

根据 `templates/` 中的模板文件，生成对应的 TASK 文件至 `.context/` 目录。

| Phase | 模板文件 | TASK 文件 |
|-------|---------|---------|
| Phase 0 | `templates/TASK-130-可观测.md` | `.context/TASK-130-可观测.md` |
| Phase 0 | `templates/TASK-150-FF.md` | `.context/TASK-150-FF.md` |
| Phase 1 | `templates/TASK-199-Build.md` | `.context/TASK-199-Build.md` |
| Phase 2 | `templates/TASK-210-基础框架-UI.md` | `.context/TASK-210-基础框架-UI.md` |
| Phase 2 | `templates/TASK-220-基础框架-骨架屏.md` | `.context/TASK-220-基础框架-骨架屏.md` |
| Phase 2 | `templates/TASK-230-基础框架-弹窗.md` | `.context/TASK-230-基础框架-弹窗.md` |
| Phase 2 | `templates/TASK-240-基础框架-登录.md` | `.context/TASK-240-基础框架-登录.md` |
| Phase 2 | `templates/TASK-245-基础框架-IAM.md` | `.context/TASK-245-基础框架-IAM.md` |
| Phase 2 | `templates/TASK-250-基础框架-检查更新.md` | `.context/TASK-250-基础框架-检查更新.md` |
| Phase 2 | `templates/TASK-260-基础框架-Bridge.md` | `.context/TASK-260-基础框架-Bridge.md` |
| Phase 2 | `templates/TASK-299-基础框架.md` | `.context/TASK-299-基础框架.md` |
| Phase 3 | `templates/TASK-310-Utils-Storage.md` | `.context/TASK-310-Utils-Storage.md` |
| Phase 3 | `templates/TASK-320-Utils-Http.md` | `.context/TASK-320-Utils-Http.md` |
| Phase 3 | `templates/TASK-325-Utils-WebSocket.md` | `.context/TASK-325-Utils-WebSocket.md` |
| Phase 3 | `templates/TASK-330-Utils-Log.md` | `.context/TASK-330-Utils-Log.md` |
| Phase 3 | `templates/TASK-335-Utils-设备信息.md` | `.context/TASK-335-Utils-设备信息.md` |
| Phase 3 | `templates/TASK-340-Utils-加解密.md` | `.context/TASK-340-Utils-加解密.md` |
| Phase 3 | `templates/TASK-360-Utils-DateTime.md` | `.context/TASK-360-Utils-DateTime.md` |
| Phase 3 | `templates/TASK-365-Utils-Currency.md` | `.context/TASK-365-Utils-Currency.md` |
| Phase 3 | `templates/TASK-367-Utils-Icon.md` | `.context/TASK-367-Utils-Icon.md` |
| Phase 3 | `templates/TASK-390-Utils-报表.md` | `.context/TASK-390-Utils-报表.md` |
| Phase 3 | `templates/TASK-399-Utils.md` | `.context/TASK-399-Utils.md` |
| Phase 4 | `templates/TASK-410-业务页面-首页.md` | `.context/TASK-410-业务页面-首页.md` |
| Phase 4 | `templates/TASK-420-业务页面-主旅程.md` | `.context/TASK-420-业务页面-主旅程.md` |
| Phase 4 | `templates/TASK-430-业务页面-营销位.md` | `.context/TASK-430-业务页面-营销位.md` |
| Phase 4 | `templates/TASK-440-业务页面-下单旅程.md` | `.context/TASK-440-业务页面-下单旅程.md` |
| Phase 4 | `templates/TASK-450-业务页面-品牌.md` | `.context/TASK-450-业务页面-品牌.md` |
| Phase 4 | `templates/TASK-460-业务页面-用户协议.md` | `.context/TASK-460-业务页面-用户协议.md` |
| Phase 4 | `templates/TASK-499-业务页面.md` | `.context/TASK-499-业务页面.md` |
| Phase 5 | `templates/TASK-510-SDK-埋点.md` | `.context/TASK-510-SDK-埋点.md` |
| Phase 5 | `templates/TASK-520-SDK-广告归因.md` | `.context/TASK-520-SDK-广告归因.md` |
| Phase 5 | `templates/TASK-530-SDK-地图.md` | `.context/TASK-530-SDK-地图.md` |
| Phase 5 | `templates/TASK-540-SDK-Push.md` | `.context/TASK-540-SDK-Push.md` |
| Phase 5 | `templates/TASK-599-SDK.md` | `.context/TASK-599-SDK.md` |
| Phase 6 | `templates/TASK-610-KYC.md` | `.context/TASK-610-KYC.md` |

### 技术栈补充参考说明

对于每个 TASK 文件，如果当前项目的技术栈在 `references/` 中有对应的参考文档，应在生成的 TASK 文件中添加"技术栈补充参考"章节。

**步骤**：

1. 识别当前项目的技术栈（如 Android、iOS、Vue 3 等）
2. 在 `references/` 中查找对应的技术栈参考文件（如 `references/常用选型-android.md`）
3. 根据当前 TASK 的审查维度，从技术栈参考文件中提取相关内容
4. 在 TASK 文件末尾添加"技术栈补充参考"章节，包含：
   - **补充审查目标**：该技术栈在此维度的特定审查目标
   - **补充关注点**：该技术栈在此维度的特定关注点

## 架构审查报告模板

待补充。

## 常见技术选型参考（References）

- Vue 3 + UniApp：见 `references/选型-vue3+uniapp.md`
- Vue 3 + Element Plus：见 `references/常用选型-vue3+elementplus.md`
- Vue 2 + iView：见 `references/常用选型-vue2-iview.md`
- Vue 2 + Vux + Webpack 3：见 `references/常用选型-vue2-vux-webpack3.md`
- Android：见 `references/常用选型-android.md`
- iOS：见 `references/常用选型-ios.md`
