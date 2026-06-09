# Vue 2 + iView 选型审查参考

## 1. 适用场景
- 现有项目已基于 Vue 2 Options API 构建
- UI 组件库为 iView（View UI）或其衍生生态
- 项目以 iView Admin 类脚手架为基础演进

## 2. Vue 2 Options API 规范要点
- **data**：必须为函数，返回纯对象；避免使用箭头函数（影响 this 绑定）
- **methods**：方法中不使用箭头函数；注意 this 绑定
- **computed**：计算属性应有明确依赖；避免副作用
- **watch**：合理使用 deep 与 immediate；避免滥用 watch 充当状态机
- **props**：定义类型与默认值；必要时使用 validator
- **lifecycle hooks**：注意 beforeDestroy 中清理定时器/事件监听

## 3. Vuex 状态管理
- **State**：单一状态树，避免状态冗余
- **Mutations**：同步、可追踪
- **Actions**：承载异步；避免把复杂业务全部堆在 Actions
- **Modules**：模块化、命名空间清晰
- **模块注册**：避免集中注册在单文件，建议自动化导入 + namespaced

## 4. Vue Router
- 路由表命名规范与分模块组织（避免单文件 1500+ 行）
- 合理使用 meta（权限、标题、缓存、面包屑）
- 导航守卫职责清晰，避免在守卫里写过多业务逻辑
- **动态路由安全**：从 localStorage 读取时需签名或白名单校验，避免路由注入风险
- **权限校验**：前端仅做展示层过滤，核心权限校验下沉到后端；缺失权限数据时默认拒绝访问

## 5. iView 组件使用关注点
- **Table**：大数据量性能（分页/虚拟滚动/按需渲染）、columns 固定列与宽度策略
- **Form**：FormItem 的 prop/rules 一致；校验类型与 v-model 值类型一致
- **Select**：远程搜索 filterable + remote；避免一次性加载超大字典
- **Modal/Drawer**：关闭时是否销毁；避免残留事件与定时器
- **Upload**：before-upload 校验类型/大小；上传失败提示与重试
- **DatePicker**：format/value-format 策略统一；注意时区与提交格式；统一使用 Date 对象绑定 v-model

## 6. Mixins 最佳实践（Vue 2 常见坑）
- 命名冲突与数据合并规则
- 生命周期执行顺序
- 避免 mixins 变成不可控的"隐式依赖"

## 7. Build 与依赖管理
- **依赖版本**：避免使用 ^ 宽松版本，关键库锁定到小版本或补丁版本
- **依赖升级**：定期安全审计，优先升级 axios、vue、iview、moment
- **环境注入**：避免构建阶段写文件副作用（如 vue.config.js 写入 env.js），优先使用 VUE_APP_* 标准方式
- **eval 使用**：禁止使用 eval 解析环境变量（如 BD_ENV），使用显式布尔解析
- **协议安全**：生产环境强制 HTTPS，避免 http 明文地址

## 8. UI 与 Design Token
- **iView 样式**：避免同时引入 iview.css 与 iview/src/styles/index.less，只保留一种样式源
- **Design Token**：建立统一 token 层（颜色、字号、间距、圆角、阴影），避免硬编码颜色值
- **表格体系**：明确默认表格组件（如基于 iView Table 的封装），制定第三方表格准入规则
- **图标体系**：明确主图标源（iView 或 iconfont 二选一），移除未使用的 Font Awesome 等冗余依赖

## 9. 网络请求与鉴权
- **Token 注入**：统一所有环境的 token 注入策略，避免仅在 development 开启
- **Cookie 策略**：服务端设置 HttpOnly + Secure + SameSite，前端仅保留必要读写
- **错误处理**：业务错误返回标准化结构或抛出异常，避免返回 false 破坏 Promise 语义
- **超时与重试**：增加全局超时、重试与请求取消能力（AbortController 或 CancelToken）
- **登录失效**：统一错误码策略，避免耦合后端实现（如通过 responseURL 包含 login.jsp 判断）

## 10. Utils 工具层
- **职责拆分**：避免 util.js 承载路由构造、权限处理、本地存储、DOM 操作等多类职责
- **参数解析**：getParams 等函数增加空值与 decode 处理，避免异常与参数解析错误
- **算法正确性**：修复遍历逻辑缺陷（如 getIntersection、findNodeDownward），补齐边界条件
- **业务耦合**：将 flatMenuToRouterList 等业务逻辑下沉到业务层服务，工具层仅保留纯函数

## 11. 日期时间处理
- **类型统一**：表单模型里日期时间字段统一使用 Date 对象（v-model 绑定 Date）
- **提交格式**：统一提交格式（仅日期：YYYY-MM-DD；日期时间：YYYY-MM-DD HH:mm:ss）
- **禁止使用**：禁止使用 toLocaleString() 作为固定展示口径（依赖浏览器 locale）
- **formatter 工具**：抽离统一 formatter 工具，处理 null/undefined、毫秒/秒时间戳、字符串解析失败兜底

## 12. 金额与数字处理
- **精度控制**：涉及前端计算时引入 decimal.js 或 big.js 做定点计算，避免 JS 浮点误差
- **格式化工具**：建立统一 formatAmount/formatNumber 工具，明确小数位、千分位、空值兜底
- **单位约定**：明确后端金额单位（元/分），禁止页面自行换算；利率/百分比字段统一通过 formatPercent
- **locale 支持**：多国家场景采用 Intl.NumberFormat，避免手写千分位与小数逻辑

## 13. 日志与错误处理
- **敏感信息**：禁止在 console 输出 token/sessionId/URL 参数等敏感字段
- **生产环境**：构建阶段移除或运行时禁用 console.log
- **统一日志**：建立统一日志工具层（info/warn/error），集中采集请求失败、路由异常与关键业务动作
- **错误上报**：引入前端错误上报（如 Sentry 或自建平台），补齐用户、路由、请求上下文信息

## 14. 加密与安全
- **传输安全**：强制生产环境 HTTPS 与 HSTS，前端启动时校验协议
- **公钥管理**：公钥下发改为服务端动态获取（带版本号与有效期），提供缓存与轮换机制
- **加密兜底**：对加密失败进行显式处理（提示用户、停止请求、上报日志）
- **防重放**：对关键请求引入签名/防重放参数（timestamp/nonce + 后端校验）

## 15. 报表与图表
- **图表主题**：将 ECharts palette 与项目 token 做映射，保证 primary/success/warning/error 等关键色一致
- **报表下载**：抽离统一下载服务封装，统一拼接下载 URL、session/cookie 处理、错误提示与兜底
- **报表分类**：定义报表分类（分析型/趋势型：ECharts 渲染；对账/明细导出型：后端生成文件；外部系统型：iframe 集成）
- **iframe 治理**：明确 iframe 白名单，增加资源释放策略（关闭标签时销毁 iframe、限制最大 iframe 数）

## 16. 第三方依赖管理
- **依赖清单**：建立第三方库清单与变更记录（版本、用途、负责人、升级计划）
- **CDN 安全**：CDN 资源增加 SRI 校验与本地/内网镜像兜底
- **库类型分散**：统一第三方库治理与升级策略，避免库类型分散导致维护成本高
- **埋点 SDK**：明确是否需要埋点，选择合规的 SDK 或自建方案，统一事件命名与属性字典

## 17. 常见反模式与风险
- **env 双源**：避免 config/env.js 与 src/config/env.js 同时存在，导致环境漂移
- **内置 Demo 路由**：确保生产构建产物不暴露 /components、/multilevel 等示例模块
- **样式重复引入**：避免 iView 样式同时引入 CSS 与 Less 源
- **表格方案分裂**：避免多套 Table 方案并存导致视觉/交互不一致
- **token 注入不一致**：避免按环境分支的 token 注入策略
- **eval 使用**：禁止使用 eval 解析配置或环境变量
- **localStorage 敏感信息**：减少 localStorage 持久化的用户敏感字段，必要字段采用签名或加密
- **登出未调用后端**：登出时调用后端注销接口，确保会话真正失效
