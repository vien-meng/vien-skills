# 默认技术栈

**决策规则**：用户明确指定技术栈时以用户为准，本文件不覆盖；用户未指定时，默认采用下面这套已在源项目中验证的组合，不在选型上重复争论。按项目形态匹配取用；只引入当前切片需要的依赖，不一次性堆满。

## Web / 桌面应用（Electron）

- Electron + React + Vite + TypeScript strict；纯 Web 形态去掉 Electron 壳，renderer 与构建方式一致。
- 状态管理：MobX（RootStore + 领域 Store）；渲染组件不持有私有业务状态，只渲染 Store 输出并转发事件。
- UI：Ant Design 承担按钮、表单、菜单、弹窗、列表与反馈；时间线、画布、波形等专业交互自绘并沿用同一主题 token。
- 多语言：i18next，默认 `zh-CN`，回退 `en-US`；数字、日期、百分比与文件大小用 `Intl`。
- 桌面安全：`contextIsolation: true`、`nodeIntegration: false`、`sandbox: true`、`webSecurity: true`；privileged 操作只经 preload 白名单，IPC 输入校验并返回稳定错误码。
- 单构建：一次 Vite build 的 renderer 同一目录既部署 Web 也打入 Electron；禁止 web/desktop 两次构建与两套页面树。
- 平台差异：运行时 `PlatformAdapter` 区分，禁止 `VITE_PLATFORM` 等构建分叉。
- 测试：Vitest + 相关 UI 测试；构建后校验 Web 发布目录与 Electron 包内 renderer hash 一致。

## 跨端移动应用（Flutter）

- Flutter / Dart + Material 3；一套业务代码覆盖 iOS / Android / HarmonyOS。
- 状态：Riverpod + GoRouter + Dio。
- 存储：sqflite（SQLite）+ flutter_secure_storage（令牌进系统安全存储）。
- 后台音频/媒体：just_audio + audio_service。
- 分层：`app/core/domain/features/platform`，每功能 `data/state/view`；view 不直接调 Dio、SQLite、地图、传感器或平台 SDK，统一走 controller 与 platform 接口。
- 验证顺序：`dart format` → `flutter analyze` → `flutter test` → 各平台 debug build → 音频、后台、权限等真机复验。

## 服务端（NestJS）

- NestJS + TypeScript strict。
- 数据库：PostgreSQL（空间场景加 PostGIS）+ TypeORM + 显式 migration；生产禁止 `synchronize: true`。
- Redis：只做缓存、限流、幂等锁、短期会话与实时扇出；业务真相禁止只存 Redis。
- 文档：Swagger/OpenAPI 作为客户端/服务端契约证据。
- 鉴权：JWT access + refresh 轮换/吊销 + `@Roles()` RBAC；令牌只放非敏感声明。
- 日志：Winston JSON + requestId + 脱敏。
- 容器化：compose/Dockerfile，健康检查（live/ready）与优雅关闭。
- 模块：`common/`、`config/`、`database/`、`modules/<业务>/`；Controller 薄层，业务在 Service，外部输入在 DTO/校验层验证。
- 测试：Jest unit + contract/e2e；统一错误码与归属校验。
