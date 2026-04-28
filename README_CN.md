# git-commit Skill

[English](./README.md) | 中文

一个 Agent Skill，用于分析 Git 暂存区的代码变更，自动生成专业、规范的 commit message。

## 特性

- **自动检测项目 commit 风格** — 通过分析现有 commit 历史判断应使用的风格
- **三种业界标准风格**：
  - **Conventional Commits** — `feat(scope): subject` 格式，适用于 Web 应用、库和大多数开源项目
  - **Linux Kernel 风格** — `subsystem: Capitalized summary` 格式，适用于内核、驱动和底层系统
  - **Go 风格** — `package/path: lowercase summary` 格式，适用于 Go 项目和基于 Gerrit 的工作流
- **三种输出变体**：
  - **标准版 (Standard Version)** — 英文，遵循检测到的风格
  - **中文友好版** — 相同结构，使用中文书写，遵循 CJK 排版规范
  - **Emoji 活力版** — 在标准版基础上添加 gitmoji 表情，让 commit 历史更直观

## 安装

### 快速安装（推荐）

使用 [skills CLI](https://www.npmjs.com/package/@anthropic/skills) 直接从 GitHub 安装：

```bash
npx skills add <your-username>/git-commit-skill
```

或使用 [give-skill](https://www.npmjs.com/package/give-skill)：

```bash
npx give-skill <your-username>/git-commit-skill
```

两个工具都支持安装到指定的智能体：

```bash
# 仅安装到 Claude Code
npx skills add <your-username>/git-commit-skill -a claude-code

# 仅安装到 Cursor
npx skills add <your-username>/git-commit-skill -a cursor

# 全局安装（所有项目可用）
npx skills add <your-username>/git-commit-skill -g
```

支持的智能体包括：Claude Code、Cursor、GitHub Copilot、Gemini CLI、Windsurf、Trae、Codex、OpenCode 等。

### 手动安装

克隆此仓库，然后将 skill 文件夹复制到对应智能体的 skills 目录：

```bash
git clone https://github.com/<your-username>/git-commit-skill.git
```

| 智能体 | 项目路径 | 全局路径 |
|-------|---------|---------|
| Claude Code | `.claude/skills/git-commit/` | `~/.claude/skills/git-commit/` |
| Cursor | `.cursor/skills/git-commit/` | `~/.cursor/skills/git-commit/` |
| Copilot | `.github/skills/git-commit/` | `~/.copilot/skills/git-commit/` |
| Windsurf | `.windsurf/skills/git-commit/` | `~/.codeium/windsurf/skills/git-commit/` |
| Trae | `.trae/skills/git-commit/` | 仅项目级别 |

## 使用方法

安装后，直接让 AI 智能体帮你写 commit message 或提交变更：

- "帮我写一个 commit message"
- "帮我提交这些变更"
- "生成一个 commit message"

Skill 会自动执行以下步骤：

1. 运行 `git diff --staged` 检查暂存区变更
2. 从现有 commit 历史中检测项目的 commit 风格
3. 生成三种变体的 commit message（标准版、中文友好版、Emoji 活力版）
4. 展示所有变体供你审阅，确认后再执行提交

## 风格检测

Skill 会根据项目现有的 commit 历史自动判断应使用的风格：

| 检测到的模式 | 使用的风格 |
|-------------|-----------|
| `feat(auth): add login` | Conventional Commits |
| `mm: Fix use-after-free` | Linux Kernel |
| `net/http: fix crash` | Go |
| 无模式 + 内核项目 | Linux Kernel |
| 无模式 + Go 项目 | Go |
| 无模式 + 其他项目 | Conventional Commits |

快速区分技巧：**冒号后大写 = Linux Kernel**，**冒号后小写 = Go**。

## 风格对比

| 特性 | Conventional Commits | Linux Kernel | Go |
|------|---------------------|-------------|-----|
| 主题格式 | `type(scope): subject` | `subsystem: Subject` | `package: subject` |
| 大小写 | 小写 | 首字母大写 | 小写 |
| 前缀含义 | 类型 `feat`、`fix` 等 | 子系统名 | 包路径 |
| Body 风格 | 祈使语气，简洁 | 问题 → 影响 → 修复 | 完整句子 |
| Markdown | 允许 | 不使用 | 禁止 |
| Signed-off-by | 可选 | 必须 | 禁止 |
| Issue 引用 | `Closes #123` | `Fixes: SHA ("subj")` | `Fixes #123` / `For #123` |

## Emoji 映射

Emoji 活力版遵循 gitmoji 规范：

| Emoji | 类型 | 示例 |
|-------|------|------|
| ✨ | 新功能 | `✨ feat(auth): add jwt refresh` |
| 🐛 | 修复 | `🐛 fix(api): handle null response` |
| 📝 | 文档 | `📝 docs: update API reference` |
| ♻️ | 重构 | `♻️ refactor(core): simplify config` |
| ⚡ | 性能 | `⚡ perf(db): optimize query` |
| 💥 | 破坏性变更 | `💥 feat(api): rename endpoint` |
| ✅ | 测试 | `✅ test(auth): add login tests` |
| 🔧 | 杂务 | `🔧 chore: update dependencies` |

## 示例

### Conventional Commits

```
fix(auth): handle race condition in JWT token refresh

The token refresh mechanism had a race condition when multiple
requests triggered a refresh simultaneously. Add mutex lock to
ensure only one refresh operation runs at a time.

Closes #123
```

### Linux Kernel 风格

```
mm: Fix use-after-free in folio migration

When a folio is migrated but the destination page has already
been freed, subsequent access triggers a use-after-free. Add
page validity check before migration to prevent this.

Fixes: e21d2170f36602ae ("mm: add folio migration support")
Signed-off-by: Jane Doe <jane@example.com>
```

### Go 风格

```
net/http: fix TLS connection detection in NewClient

NewClient does not properly detect existing TLS connections in
some cases, causing unnecessary TLS handshake retries. Add a
check for the underlying connection's TLS state.

Fixes #456
```

### 中文友好版

```
fix(auth): 修复 JWT token 刷新时的竞态条件

当多个请求同时触发 token 刷新时，存在竞态条件导致
部分请求使用了已过期的 token。添加互斥锁确保同一
时间只有一个刷新操作在进行。

Closes #123
```

### Emoji 活力版

```
🐛 fix(auth): handle race condition in JWT token refresh

The token refresh mechanism had a race condition when multiple
requests triggered a refresh simultaneously. Add mutex lock to
ensure only one refresh operation runs at a time.

Closes #123
```

## 项目结构

```
.
├── LICENSE
├── README.md
├── README_CN.md
└── git-commit/
    └── SKILL.md
```

## 参考资料

- [Agent Skills 标准](https://agentskills.io)
- [Conventional Commits 规范](https://www.conventionalcommits.org/)
- [Linux Kernel 提交补丁指南](https://www.kernel.org/doc/html/latest/process/submitting-patches.html)
- [Go Commit Message 规范](https://go.dev/wiki/CommitMessage)
- [gitmoji](https://gitmoji.dev/)
- [如何写好 Git Commit Message](https://chris.beams.io/posts/git-commit/)

## 许可证

Apache-2.0
