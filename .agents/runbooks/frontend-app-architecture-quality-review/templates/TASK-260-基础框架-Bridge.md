# Bridge（Native-WebView 桥接）

> updated_by: Kilo - GLM-5
> updated_at: 2026-05-12 17:46:00

- **Bridge（Native-WebView 桥接）**：
  - 注入方式是否统一：是否通过单一入口向 WebView 注入 Bridge 对象（如 `addJavascriptInterface` / `WKUserContentController` 等价机制），是否存在多套注入方式并存或散落各处
  - 协议设计是否规范：Bridge 方法命名是否遵循统一约定（命名空间/模块前缀/动词+名词），参数与返回值是否采用结构化 JSON Schema，是否版本化并支持向后兼容
  - 调用方向是否清晰：Native→H5（evaluateJavascript / postMessage）与 H5→Native（Bridge 调用）两条链路是否均有统一封装，是否避免 H5 直接调用未暴露的 Native 方法
  - 类型安全与校验：Bridge 参数在 Native 侧是否进行类型校验与容错处理，是否避免 JSON 解析异常导致崩溃；H5 侧是否提供类型声明（.d.ts / JSDoc）供前端开发使用
  - 错误处理与降级：Bridge 调用失败（方法不存在、参数错误、权限不足、运行时异常）是否有统一错误码与回退策略，是否避免静默失败导致流程卡死
  - 安全与风控：注入对象是否仅暴露必要方法，是否防止 H5 通过反射调用 Native 私有方法；敏感 Bridge（如支付、权限申请、文件访问）是否有鉴权与白名单机制；是否校验 WebView 加载的 URL 来源
  - 生命周期与内存：Bridge 对象是否与 WebView 生命周期绑定，页面销毁时是否正确移除注入对象，是否避免内存泄漏或已销毁 WebView 上的回调触发
  - 版本兼容与灰度：新增/变更 Bridge 方法是否支持版本协商（H5 侧检测能力是否存在再调用），是否支持按 App 版本 / 灰度比例渐进发布
  - 异步与回调机制：Bridge 异步调用的回调是否可靠（Promise / Callback 完备性），是否处理超时与多次回调场景，是否避免回调丢失或嵌套过深
  - 可观测性闭环：Bridge 调用是否可追踪（调用方、方法名、参数摘要、耗时、结果/错误码），是否支持按方法维度统计成功率与耗时，异常是否可告警
  - **审查报告**：`/.plans/架构质量审查-260-基础框架-Bridge.md`
