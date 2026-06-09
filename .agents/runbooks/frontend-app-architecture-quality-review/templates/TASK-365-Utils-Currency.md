# Currency

> updated_by: Cascade - Cascade
> updated_at: 2026-05-06 11:40:00

- **Currency**：
  - 货币金额的表示是否统一（整数最小单位 vs Decimal），避免浮点误差
  - 格式化与舍入规则是否集中治理（round/floor/ceil、银行家舍入、展示精度）
  - 多币种与汇率来源是否明确（精度、时效、缓存、失败兜底）
  - i18n 与本地化展示是否一致（符号位置、千分位、小数点、负数）
  - **审查报告**：`/.plans/架构质量审查-365-Utils-Currency.md`
