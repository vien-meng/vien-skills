# vien-skills

一套从实际项目中提炼、可独立发布复用的 Claude Code Skills。

## generic-dev-sop

通用项目开发 SOP：从零搭建新项目（前端 / 后端 / 跨端客户端 / 服务端均适用）并完整交付。生命周期四步：①项目搭建（架构决策 + 质量底座）②详细任务计划 ③单功能开发闭环 ④完整交付（发布候选 → 灰度回滚 → 文档同步）。

### 内容

```text
skills/generic-dev-sop/
├── SKILL.md                                  # 主 SOP：项目搭建 + 任务计划 + 单功能闭环 + 完整交付
└── references/
    ├── architecture-template.md              # 架构决策模板（产品边界、技术栈、目录、数据模型、契约、风险决策门）
    ├── default-stack.md                      # 默认技术栈（用户未指定时采用：Electron/Flutter/NestJS 已验证组合）
    ├── task-plan-template.md                 # 分阶段任务计划模板（执行原则、阶段总览、详细任务、功能矩阵、质量门禁）
    ├── development-record-template.md        # 单功能开发记录模板
    ├── coding-standards.md                   # 通用编码规范（注释、命名、类型、路径、错误/安全、平台边界）
    └── review-checklist.md                   # 独立代码审查清单
```

### 核心要点

- **默认技术栈**：用户指定则以用户为准；未指定时用 `default-stack.md` 已验证组合（Electron+React+MobX+AntD / Flutter+Riverpod / NestJS+PostgreSQL+TypeORM）。
- **纵向切片交付**：UI、契约、实现、测试、验证证据同步完成，禁止只做底层逻辑后宣称完成。
- **七态状态机**：`未开始 / 分析中 / 设计确认 / 开发中 / 审查测试中 / 已完成 / 阻塞`。
- **设计稿强制门禁**：有 UI 先出覆盖默认/加载/空/错误/禁用/无权限/离线状态的静态稿；无 UI 功能说明依据即可跳过。
- **三级质量门禁**：切片级（最小可运行检查）、阶段级（format/lint/type/test/build）、发布候选级（回滚、安全、合规）。
- **编码规范 + 独立审查**：`coding-standards.md` 覆盖注释、命名、类型、路径别名、错误/安全、平台边界；`review-checklist.md` 提供交付前逐项核对清单。
- **进度回写**：计划、契约、设计稿、测试、证据一致才标记完成。

### 安装

**方式一：Claude Code 插件市场（CLI / 桌面端 / IDE 通用）**

```text
/plugin marketplace add vien-meng/vien-skills
```

添加后启用 `vien-skills` 插件，`generic-dev-sop` skill 即可在任意对话中使用。本地开发时也可直接添加本地路径：

```text
/plugin marketplace add ~/Desktop/working/vien_job/vien-skills
```

**方式二：手动安装到单项目**

```bash
cp -r skills/generic-dev-sop <你的项目>/.claude/skills/
```

**方式三：手动全局安装**

```bash
cp -r skills/generic-dev-sop ~/.claude/skills/
```

**其他平台**

本仓库按 ponytail 模式做了跨平台封装，skill 内容为纯 markdown，通用可移植。各平台封装均按官方格式提供：

- **Kimi Code CLI**：SKILL.md 格式与 Claude Code 一致（frontmatter `name`/`description`）。`cp -r skills/generic-dev-sop ~/.kimi-code/skills/`（全局）或项目 `.kimi-code/skills/`（本地），重启会话后用 `/skill:generic-dev-sop` 调用。
- **Codex**：`codex plugin marketplace add vien-meng/vien-skills` 直接添加，再在 config 启用 `[plugins."vien-skills@vien-skills"] enabled = true`。
- **OpenCode**：`cp -r .opencode/command/generic-dev-sop.md <项目>/.opencode/command/`，用 `/generic-dev-sop` 调用；或复制根 `AGENTS.md` 到项目根作为常驻上下文。如需原生 skill 语义，可把 `skills/generic-dev-sop/` 复制到项目 `.opencode/skills/`。
- **Gemini CLI**：`gemini extensions install /path/to/vien-skills` 或 `gemini extensions install vien-meng/vien-skills`，安装后扩展根目录的 `AGENTS.md` 作为上下文生效（`gemini-extension.json` 的 `contextFileName` 指向它）。注意：不是放根目录自动生效，必须先 install。
- **Cursor / Cline / Kiro**：把 `.cursor/rules/generic-dev-sop.mdc`、`.clinerules/generic-dev-sop.md`、`.kiro/steering/generic-dev-sop.md` 复制到你的项目同名目录即可自动生效（Cursor `alwaysApply: true`、Cline 全局规则、Kiro `inclusion: always`）。
- **Windsurf**：把 `.windsurf/rules/generic-dev-sop.md` 复制到项目 `.windsurf/rules/`（frontmatter `trigger: always_on`，自动应用）。
- **Devin**：`.devin-plugin/plugin.json` 元数据已提供，技能以 `/vien-skills:generic-dev-sop` 调用。

### 用法

新项目流程：先确认技术栈（用户指定，未指定或直接回车则用 `default-stack.md` 默认组合），按 `references/architecture-template.md` 产出架构决策、按 `references/task-plan-template.md` 产出任务计划，再按 `SKILL.md` 第一部分搭建工程底座；之后每个功能按第三部分闭环交付并回写 `references/features/<功能短名>.md` 开发记录；P0 完成后按第四部分走发布候选、灰度回滚、文档同步与项目交付检查。

### 来源

由以下项目的实际开发 SOP 提炼共性后去项目化：

- coral-music-desktop / coral-music-mobile（桌面与 Flutter 跨端客户端）
- smart-cut / smart-cut-server（Web + Electron 编辑器与其 NestJS 服务端）
- jiyu-flutter / jiyu-server（Flutter 三端 + NestJS 服务端）

## License

Apache-2.0，见 [LICENSE](LICENSE)。
