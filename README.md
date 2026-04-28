# git-commit Skill

English | [中文](./README_CN.md)

An Agent Skill that analyzes staged git changes and generates professional, standards-compliant commit messages.

## Features

- **Auto-detects project commit style** by inspecting existing commit history
- **Three industry-standard styles**:
  - **Conventional Commits** — `feat(scope): subject` format, best for web apps, libraries, and most open-source projects
  - **Linux Kernel Style** — `subsystem: Capitalized summary` format, best for kernels, drivers, and low-level systems
  - **Go Style** — `package/path: lowercase summary` format, best for Go projects and Gerrit-based workflows
- **Three output variants**:
  - **Standard Version** — English, following the detected style
  - **中文友好版 (Chinese Version)** — Same structure, written in Chinese with proper CJK typography rules
  - **Emoji 活力版 (Emoji Version)** — Standard version with gitmoji-enhanced subject lines

## Installation

### Quick Install (Recommended)

Install directly from GitHub using the [skills CLI](https://www.npmjs.com/package/@anthropic/skills):

```bash
npx skills add loommii/git-commit-skills
```

Or with [give-skill](https://www.npmjs.com/package/give-skill):

```bash
npx give-skill loommii/git-commit-skills
```

Or with Gemini CLI:

```bash
gemini skills install https://github.com/loommii/git-commit-skills.git
```

Both `skills` and `give-skill` support installing to specific agents:

```bash
# Install to Claude Code only
npx skills add loommii/git-commit-skills -a claude-code

# Install to Cursor only
npx skills add loommii/git-commit-skills -a cursor

# Install globally (available across all projects)
npx skills add loommii/git-commit-skills -g
```

Supported agents include: Claude Code, Cursor, GitHub Copilot, Gemini CLI, Windsurf, Trae, Codex, OpenCode, and more.

### Manual Install

Clone this repository and copy the skill folder to your agent's skills directory:

```bash
git clone https://github.com/loommii/git-commit-skills.git
```

| Agent | Project Path | Global Path |
|-------|-------------|-------------|
| Claude Code | `.claude/skills/git-commit/` | `~/.claude/skills/git-commit/` |
| Cursor | `.cursor/skills/git-commit/` | `~/.cursor/skills/git-commit/` |
| Copilot | `.github/skills/git-commit/` | `~/.copilot/skills/git-commit/` |
| Windsurf | `.windsurf/skills/git-commit/` | `~/.codeium/windsurf/skills/git-commit/` |
| Trae | `.trae/skills/git-commit/` | Project-level only |

## Usage

Once installed, simply ask the AI agent to write a commit message or commit your changes:

- "Write a commit message for my staged changes"
- "Help me commit these changes"
- "Generate a commit message"

The skill will:

1. Run `git diff --staged` to inspect your changes
2. Detect the project's commit style from existing history
3. Generate commit messages in all three variants (Standard, 中文友好版, Emoji 活力版)
4. Present them for your review before committing

## Style Detection

The skill automatically detects which commit style to use based on the project's existing commit history:

| Pattern Detected | Style Used |
|-----------------|------------|
| `feat(auth): add login` | Conventional Commits |
| `mm: Fix use-after-free` | Linux Kernel |
| `net/http: fix crash` | Go |
| No pattern + kernel project | Linux Kernel |
| No pattern + Go project | Go |
| No pattern + other | Conventional Commits |

Quick tip to distinguish Linux Kernel vs Go: **capital after colon = Linux Kernel**, **lowercase after colon = Go**.

## Style Comparison

| Feature | Conventional Commits | Linux Kernel | Go |
|---------|---------------------|--------------|-----|
| Subject format | `type(scope): subject` | `subsystem: Subject` | `package: subject` |
| Capitalization | lowercase | Capitalized | lowercase |
| Type prefix | `feat`, `fix`, etc. | Subsystem name | Package path |
| Body style | Imperative, concise | Problem → impact → fix | Complete sentences |
| Markdown | Allowed | Not used | Prohibited |
| Signed-off-by | Optional | Required | Prohibited |
| Issue refs | `Closes #123` | `Fixes: SHA ("subj")` | `Fixes #123` / `For #123` |

## Emoji Mapping

The Emoji 活力版 uses gitmoji conventions:

| Emoji | Type | Example |
|-------|------|---------|
| ✨ | New feature | `✨ feat(auth): add jwt refresh` |
| 🐛 | Bug fix | `🐛 fix(api): handle null response` |
| 📝 | Documentation | `📝 docs: update API reference` |
| ♻️ | Refactor | `♻️ refactor(core): simplify config` |
| ⚡ | Performance | `⚡ perf(db): optimize query` |
| 💥 | Breaking change | `💥 feat(api): rename endpoint` |
| ✅ | Test | `✅ test(auth): add login tests` |
| 🔧 | Chore | `🔧 chore: update dependencies` |

## Examples

### Conventional Commits

```
fix(auth): handle race condition in JWT token refresh

The token refresh mechanism had a race condition when multiple
requests triggered a refresh simultaneously. Add mutex lock to
ensure only one refresh operation runs at a time.

Closes #123
```

### Linux Kernel Style

```
mm: Fix use-after-free in folio migration

When a folio is migrated but the destination page has already
been freed, subsequent access triggers a use-after-free. Add
page validity check before migration to prevent this.

Fixes: e21d2170f36602ae ("mm: add folio migration support")
Signed-off-by: Jane Doe <jane@example.com>
```

### Go Style

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

## Project Structure

```
.
├── LICENSE
├── README.md
├── README_CN.md
└── git-commit/
    └── SKILL.md
```

## References

- [Agent Skills Standard](https://agentskills.io)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Linux Kernel Submitting Patches](https://www.kernel.org/doc/html/latest/process/submitting-patches.html)
- [Go Commit Messages](https://go.dev/wiki/CommitMessage)
- [gitmoji](https://gitmoji.dev/)
- [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)

## License

Apache-2.0
