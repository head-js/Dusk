> updated_by: Cascade - Cascade
> updated_at: 2026-02-13 09:37:00

# 常用选型：Vue 2 + Vux + Webpack 3

## 适用范围

本文件用于沉淀 **Vue 2 + Vux + Webpack 3** 技术栈下的常见架构/工程约束与审查要点。

说明：项目级“架构质量审查”请使用 `frontend-app-architecture-quality-review` Skill；本文件仅保留该技术栈的专项内容，避免与通用审查维度重复。

## Vue 2 Options API 规范要点

- **data**
  - 必须为函数，返回纯对象
  - 避免使用箭头函数（影响 `this` 绑定）
- **methods**
  - 方法中不使用箭头函数
  - 注意 `this` 绑定与回调上下文（尤其是定时器/事件回调）
- **computed**
  - 计算属性应有明确的依赖关系
  - 避免在 computed 中产生副作用（发请求/写状态）
- **watch**
  - 合理使用 `deep` 与 `immediate`
  - 避免滥用 watch 替代数据流设计
- **props**
  - 定义类型与默认值
  - 必要时使用 `validator` 做输入约束
- **components**
  - 建议使用 PascalCase 命名
- **lifecycle hooks**
  - 正确使用 `created` / `mounted` / `updated` / `destroyed`
  - 在 `beforeDestroy` 中完成事件监听/定时器等资源清理

## Vuex 状态管理规范要点

- **State**
  - 单一状态树
  - 避免状态冗余与派生状态落到 state
- **Getters**
  - 用于计算派生状态
  - 关注缓存行为与依赖正确性
- **Mutations**
  - 必须是同步函数
  - 建议具备明确的类型/命名约定
- **Actions**
  - 承接异步操作
  - 可组合多个 mutations 完成事务式更新
- **Modules**
  - 采用模块化划分
  - 合理命名空间（避免全局污染）
- **mapHelpers**
  - 正确使用 `mapState` / `mapGetters` / `mapActions` / `mapMutations`

## Vue Router 规范要点

- **路由配置**
  - 路由表清晰，命名规范
- **导航守卫**
  - 全局守卫：`beforeEach` / `afterEach`
  - 路由守卫：`beforeEnter`
  - 组件守卫：`beforeRouteEnter` / `beforeRouteUpdate` / `beforeRouteLeave`
- **路由参数**
  - 正确获取与传递参数（避免在多处手拼字符串路径）
- **路由元信息**
  - 合理使用 `meta` 字段承载标题、鉴权、缓存策略等

## Vux 组件使用规范要点

- **Scroller / LoadMore**
  - 大数据量场景使用分页与分片渲染
- **Popup / Actionsheet**
  - 使用 `v-if` 控制显示，避免重复渲染与无意义的常驻节点
- **XInput / Datetime**
  - 明确 `format` 与输入校验规则
- **XImg**
  - 图片尺寸约束与懒加载策略
- **Toast / Loading**
  - 异步流程统一收敛，避免多处同时触发造成体验与状态错乱

## Webpack 3 构建审查要点

### Webpack 3 配置规范

- 入口与输出命名一致性（多入口场景）
- loader 链路顺序（css/less/sass、postcss）
- ExtractTextPlugin 与缓存策略配置
- UglifyJsPlugin 压缩配置与 source map 策略
- dll 与 vendor 拆分策略的可维护性

### 升级到 Webpack 4 的提示点（仅提示，不自动改造）

- **构建模式**
  - 引入 `mode` 并调整 production/development 差异配置
- **优化配置**
  - 迁移到 `optimization.splitChunks` 与 `runtimeChunk`
- **插件替换**
  - ExtractTextPlugin 替换为 `mini-css-extract-plugin`
- **Terser 替换**
  - UglifyJsPlugin 替换为 `terser-webpack-plugin`
- **babel 与 loader 兼容**
  - 升级到 webpack 4 兼容版本的 loader
- **devServer 变更**
  - 选项命名与默认值变化检查
- **dll 策略**
  - 评估是否保留 dll，或迁移为 splitChunks
- **依赖版本**
  - `webpack-cli`、`webpack-dev-server` 版本匹配
- **Node 版本**
  - 确认 Node 版本满足 webpack 4 最低要求

## Mixins 最佳实践

- **命名空间**
  - 使用 `$options.name` 或前缀避免冲突
- **生命周期**
  - 明确 mixins 生命周期执行顺序
- **数据合并**
  - 明确 Vue 对 `data`、`methods` 等选项的合并规则
- **合理使用**
  - 避免过度使用导致维护困难（必要时转向更清晰的组合/服务层抽象）
