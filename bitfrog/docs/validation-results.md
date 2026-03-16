# Phase 0 Validation Results

## Test 1: Agent Plugin Registration
- Date: 2026-03-17
- VS Code version: 1.111.0
- Plugin mode works: NOT TESTED (需要单独验证 plugin.json 安装)
- .github/agents/ mode works: **YES** — test-router 和 test-worker 均出现在 agent 下拉框
- Notes: `.github/agents/` 路径下的 `.agent.md` 文件被 VS Code 自动发现并注册。无需任何 TypeScript 代码或 extension 注册。

## Test 2: Handoffs
- Forward handoff (router → worker): **YES** — "Go to Worker" 按钮出现
- Reverse handoff (worker → router): BLOCKED（Copilot 账户认证问题，无法完成 LLM 响应）
- Prompt pre-fill works: 按钮出现并显示 "Proceed from test-router"，pre-fill 机制可见
- Notes: Handoff 按钮渲染和 agent 切换机制已确认工作。完整交互测试因账户问题暂缓。

## Test 3: askQuestions
- Carousel appears: BLOCKED（账户问题）
- Single select works: BLOCKED
- Multi select works: BLOCKED
- Free text works: BLOCKED
- Sub-agent can ask questions: BLOCKED
- Notes: 需要 Copilot 账户恢复后测试。

## Test 4: Agent Hooks
- hooks.json detected: NOT TESTED
- session-start fires: NOT TESTED
- Notes: 未创建 hooks 测试文件，优先级低于 agent 注册和 handoff 验证。

## Test 5: Pure Prompt Routing
- Classification accuracy: BLOCKED（账户问题）
- Ambiguous cases handled: BLOCKED
- Handoff suggestion correct: BLOCKED
- Notes: 需要 Copilot 账户恢复后测试。

---

## 已确认的关键能力

| 能力 | 状态 | 证据 |
|------|------|------|
| .github/agents/ 自动发现 | **CONFIRMED** | test-router 和 test-worker 出现在下拉框 |
| .agent.md YAML frontmatter | **CONFIRMED** | agent name、description、tools 正确解析 |
| handoffs 按钮渲染 | **CONFIRMED** | "Go to Worker" 按钮出现，显示 "Proceed from test-router" |
| 零 TypeScript 代码 | **CONFIRMED** | 纯 .agent.md 文件即可注册 agent，无需 extension |

## 待验证（需 Copilot 账户恢复）

| 能力 | 说明 |
|------|------|
| handoff 完整交互 | 点击按钮后是否正确切换 agent + pre-fill prompt |
| askQuestions | carousel 能力边界 |
| 纯 prompt 路由 | 意图分类准确率 |
| Agent Hooks | session-start 触发 |
| Plugin mode | plugin.json 安装方式 |

---

## Phase 1 Technical Decisions（基于已有证据的初步决策）

### Plugin Mode
- [ ] Agent Plugin 可用 → Phase 1 使用 plugin.json 模式
- [x] **.github/agents/ 模式已确认可用** → Phase 1 可以先用此模式，Plugin 模式后续验证

### User Interaction
- [ ] askQuestions 能力足够 → 移除 sidebar webview
- [ ] askQuestions 能力不足 → 保留轻量 webview 或接受纯文本交互
- **决策延迟**：需账户恢复后验证

### Routing
- [ ] 纯 prompt 路由准确率 ≥ 80% → 不需要 TypeScript router
- [ ] 纯 prompt 路由准确率 < 80% → 保留 participant/router.ts
- **决策延迟**：需账户恢复后验证

### Hooks
- [ ] Hooks 可用 → 使用 hooks.json 注入 BitFrog 上下文
- [ ] Hooks 不可用 → 在每个 agent prompt 中内嵌上下文
- **决策延迟**：优先级低
