# 日期时间与时区（DateTime / Timezone）

> updated_by: Cascade - Cascade
> updated_at: 2026-05-06 11:40:00

- **日期时间与时区（DateTime / Timezone）**：
  - 日期解析与格式化是否统一入口（避免各处硬编码 `yyyy-MM-dd HH:mm:ss`、`dd/MM/yyyy`、ISO 模板）
  - 时区与 Locale 策略是否清晰（展示使用本地时区、服务端交互使用约定时区），并避免隐式依赖系统默认值
  - 时间戳单位是否显式治理（`timestampMs` / `timestampSec`），避免秒/毫秒混用导致比较与排序错误
  - 是否覆盖夏令时、跨时区、跨日边界（00:00 附近）等关键边界场景，避免日期偏移与展示错误
  - 国家/地区日期展示规则是否配置化，避免按国家码散落条件分支造成维护成本上升
  - **审查报告**：`/.plans/架构质量审查-360-Utils-DateTime.md`
