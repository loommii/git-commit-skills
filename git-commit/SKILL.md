---
name: "git-commit"
description: "Analyzes staged git changes and generates professional commit messages following Conventional Commits, Linux kernel, and Go styles, with optional Chinese and Emoji variants. Invoke when user asks to write a commit message, commit changes, or needs help with git commit."
---

# Git Commit Message Generator

This skill analyzes staged code changes and generates professional, standards-compliant commit messages. It supports three industry-standard styles, auto-detects which to use based on the project context, and can optionally output Chinese-friendly and Emoji-enhanced variants.

## Workflow

1. Run `git diff --staged` to inspect all staged changes
2. If no changes are staged, run `git status` to check for unstaged changes and remind the user to stage them first
3. Analyze the diff output to understand:
   - What files were changed
   - What types of changes were made (additions, deletions, modifications)
   - The intent and scope of the changes
4. Detect the project's commit style (see Style Detection below)
5. Generate commit messages in the following variants:
   - **Standard version** — following the detected style (Conventional Commits / Linux Kernel / Go)
   - **Chinese version** — same structure, written in Chinese (中文友好版)
   - **Emoji version** — standard version enhanced with type-indicating emojis (Emoji 活力版)
6. Present all variants to the user for review, clearly labeled

## Style Detection

Check the project's existing commit history to determine the prevailing style:

```bash
git log --oneline -20
```

- If existing messages use `type(scope): subject` format (e.g., `feat(auth): add login`) → use **Conventional Commits**
- If existing messages use `subsystem: Capitalized summary` format (e.g., `mm: Fix use-after-free in folio migration`) → use **Linux Kernel Style**
- If existing messages use `package: lowercase summary` format (e.g., `net/http: handle foo when bar`) → use **Go Style**
- If no clear pattern exists, default to **Conventional Commits** unless:
  - The project is a kernel, driver, or low-level systems project → **Linux Kernel Style**
  - The project is written in Go or is a Go ecosystem project → **Go Style**

Quick disambiguation between Linux Kernel and Go styles:
- Linux Kernel: capital after colon (`mm: Fix crash`)
- Go: lowercase after colon (`net/http: fix crash`)

---

## Style 1: Conventional Commits

Best for: web applications, libraries, tools, most open-source projects.

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Type (Required)

| Type | Description |
|------|-------------|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation only changes |
| `style` | Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc.) |
| `refactor` | A code change that neither fixes a bug nor adds a feature |
| `perf` | A code change that improves performance |
| `test` | Adding missing tests or correcting existing tests |
| `build` | Changes that affect the build system or external dependencies |
| `ci` | Changes to CI configuration files and scripts |
| `chore` | Other changes that don't modify src or test files |
| `revert` | Reverts a previous commit |

### Scope (Optional)

- Module, component, or package name
- Use lowercase hyphenated names: `auth`, `api`, `ui-button`, `user-service`
- If multiple scopes are affected equally, omit the scope

### Subject (Required)

- Use the imperative mood: "add feature" not "added feature" or "adds feature"
- Do not capitalize the first letter
- No period at the end
- Keep under 72 characters

### Body (Optional)

- Use the imperative mood
- Separate from subject with a blank line
- Explain the **what** and **why**, not the **how**
- Wrap at 72 characters

### Footer (Optional)

- Reference breaking changes with `BREAKING CHANGE:` followed by a description
- Reference issues or PRs: `Closes #123`, `Refs #456`
- Separate from body with a blank line

### Conventional Commits Examples

```
feat(auth): add jwt token refresh mechanism
```

```
fix(api): handle null response from user endpoint

The user profile endpoint was returning a 500 error when the user
had no profile data. Added null check before accessing profile fields.
```

```
feat(db): migrate user schema to support multi-tenant

BREAKING CHANGE: User model now requires tenant_id field. Existing
queries must be updated to include tenant context.
```

```
refactor(parsers): extract shared validation logic into base class

Move common validation methods from CSVParser and JSONParser into
a new ParserBase class to reduce duplication and simplify testing.
```

---

## Style 2: Linux Kernel Style

Best for: kernels, drivers, low-level systems, firmware, infrastructure projects.

The Linux kernel commit style is derived from decades of kernel development practice and documented in `Documentation/process/submitting-patches.rst`. It emphasizes **subsystem identification**, **user-visible impact**, and **structured trailers**.

```
subsystem: Capitalized short summary

Detailed explanation of the problem, the fix, and why this
approach was chosen. Wrap at 72 characters.

Signed-off-by: Author Name <email@example.com>
```

### Subsystem Prefix (Required)

The subject line MUST begin with a subsystem prefix followed by a colon and a space. The subsystem identifies the area of the codebase affected — it is an informal name, not strictly tied to directory layout.

Common kernel subsystem prefixes:

| Prefix | Scope |
|--------|-------|
| `mm` | Memory management |
| `net` | Networking stack |
| `fs` | Filesystems |
| `io_uring` | io_uring subsystem |
| `sched` | Scheduler |
| `block` | Block layer |
| `scsi` | SCSI subsystem |
| `usb` | USB subsystem |
| `pci` | PCI subsystem |
| `drm` | GPU/DRM subsystem |
| `x86` | x86 architecture |
| `arm64` | ARM64 architecture |
| `security` | Security subsystem |
| `selinux` | SELinux |
| `bpf` | BPF subsystem |
| `cgroup` | Control groups |
| `kvm` | KVM hypervisor |
| `tracing` | Tracing/ftrace |
| `printk` | Kernel logging |
| `dma` | DMA subsystem |

For non-kernel projects, derive the subsystem from the primary directory or component affected:
- `core` → core module
- `driver` → hardware driver
- `hal` → hardware abstraction layer
- `proto` → protocol implementation
- `platform` → platform-specific code

Nested subsystems are allowed: `x86/defconfigs`, `net/sched`, `fs/ext4`

### Subject (Required)

- Format: `subsystem: Capitalized imperative summary`
- **Capitalize** the first letter after the colon
- Use the **imperative mood**: "Add feature" not "Added feature" or "Adds feature"
- No period at the end
- Keep under 75 characters (including the subsystem prefix)
- Describe **what** the patch changes, not just the effect
- Good: `mm: Fix use-after-free in folio migration` Bad: `mm: Fix crash`

### Body (Required for non-trivial changes)

The body is the most important part of a kernel-style commit. Follow these principles:

1. **Describe the problem first** — Convince the reviewer that there is a problem worth fixing. Even if the bug seems obvious, explain it.
2. **Describe user-visible impact** — Crashes, lockups, data corruption, performance regressions, latency spikes. Quote dmesg output or error logs when applicable.
3. **Quantify optimizations** — If claiming performance improvement, include benchmark numbers. Also describe trade-offs (CPU vs memory vs readability).
4. **Explain the fix** — Describe in plain English what the change does and why this approach was chosen over alternatives.
5. **One problem per commit** — If the body gets long, the patch should probably be split.

Formatting rules:
- Separate from subject with a blank line
- Wrap at 72 characters
- Use imperative mood: "make xyzzy do frotz" not "[This patch] makes xyzzy do frotz"
- When referencing a prior commit, include both the 12+ char SHA and the oneline summary:
  `Commit e21d2170f36602ae ("video: remove unnecessary platform_set_drvdata()") removed...`
- Code examples: indent with 4 spaces, surround with blank lines, may exceed 72 chars

### Trailers (Structured Footer)

Trailers are key-value pairs at the end of the commit message, separated from the body by a blank line. Each trailer on its own line.

| Trailer | Description |
|---------|-------------|
| `Signed-off-by:` | Developer certifies the origin of the patch (DCO) |
| `Reviewed-by:` | Code reviewer attestation |
| `Acked-by:` | Subsystem maintainer approval |
| `Tested-by:` | Tester verification |
| `Reported-by:` | Person who reported the issue |
| `Suggested-by:` | Person who suggested the approach |
| `Cc:` | Person who should be informed |
| `Fixes:` | Commit hash + subject of the bug being fixed |
| `Link:` | URL to discussion, bug report, or mailing list post |

Trailer format rules:
- Each trailer on its own line
- Title-Case key, colon, single space, then value
- For `Fixes:`, use 12+ char SHA with subject: `Fixes: e21d2170f36602ae ("video: remove unnecessary platform_set_drvdata()")`
- For `Signed-off-by:`, use: `Signed-off-by: Name <email@example.com>`

### Linux Kernel Style Examples

Simple fix:
```
cifs: Remove unused device type and characteristics defines

These constants are not used anywhere in the cifs codebase.
Remove them to reduce clutter.
```

Bug fix with detailed explanation:
```
mm/khugepaged: Skip folio with hwpoisoned page

When a transparent huge page contains a hardware poisoned (hwpoison)
sub-page, khugepaged should not attempt to collapse or remap it.

Currently, try_to_map_unused_to_zeropage() does not check for
hwpoisoned pages, which can lead to accessing poisoned memory during
the zero-page mapping optimization. Similarly, thp_underused() does
not account for hwpoisoned pages when deciding if a THP is a good
collapse candidate.

Add PageHWPoison() and folio_contain_hwpoisoned_page() checks to
both paths so that hwpoisoned folios are safely skipped.

Fixes: 123456789abc ("mm/khugepaged: add self-contained...")
Signed-off-by: Jane Doe <jane@example.com>
```

Feature addition:
```
io_uring: Add multishot uring_cmd buffer selection support

Allow multishot uring_cmd requests to use provided buffer selection,
enabling zero-copy multishot operation for drivers that support it.

Previously, IORING_URING_CMD_MULTISHOT and IORING_URING_CMD_FIXED
were mutually exclusive with buffer selection, which limited the
usefulness of multishot for data-producing commands.

Add io_uring_cmd_buffer_select() and io_uring_mshot_cmd_post_cqe()
helpers that drivers can use to select and commit buffers in a
multishot submission context.

Signed-off-by: John Smith <john@example.com>
```

---

## Style 3: Go Style

Best for: Go projects, Go ecosystem libraries (golang.org/x/...), projects using Gerrit code review.

The Go project commit style is documented at `go.dev/doc/contribute` and `go.dev/wiki/CommitMessage`. It uses **package path prefixes**, **lowercase summaries**, and **no Markdown**. Go uses Gerrit for code review and refers to commits as CLs (changelists).

```
package/path: lowercase imperative summary

Longer description in complete sentences with correct punctuation.
Wrap at ~72 characters.

Fixes #12345
```

### Package Prefix (Required)

The subject line MUST begin with the package path affected by the change, followed by a colon and a space. Unlike Linux kernel's informal subsystem names, Go uses the actual import path or directory path of the package.

Common Go package prefixes:

| Prefix | Scope |
|--------|-------|
| `net/http` | HTTP server/client package |
| `cmd/go` | Go command toolchain |
| `cmd/compile` | Go compiler |
| `cmd/link` | Go linker |
| `runtime` | Go runtime (scheduler, GC, goroutines) |
| `runtime/pprof` | Profiling support |
| `crypto/tls` | TLS implementation |
| `crypto/x509` | X.509 certificate handling |
| `database/sql` | SQL database interface |
| `go/types` | Go type system |
| `go/parser` | Go parser |
| `go/printer` | Go pretty-printer |
| `testing` | Testing framework |
| `sync` | Synchronization primitives |
| `os` | Operating system functionality |
| `doc` | Documentation changes |
| `all` | Cross-cutting changes |

For `golang.org/x/...` sub-repositories, the prefix is still the package name without the repository path:
- In `x/crypto`: use `cipher/rot13:` not `x/crypto/cipher/rot13:`
- In `x/net`: use `http2:` not `x/net/http2:`
- In `x/tools`: use `go/analysis:` not `x/tools/go/analysis:`

### Subject (Required)

- Format: `package/path: lowercase imperative summary`
- **Lowercase** the first letter after the colon (this is the key difference from Linux kernel style)
- The summary should complete the sentence: "This change modifies Go to **___**"
- Use the **imperative mood**: "add feature" not "added feature" or "adds feature"
- No period at the end
- Keep under 72 characters (ideally as short as possible)
- No Markdown formatting

### Body (Required for non-trivial changes)

The body explains the change in detail. Follow these principles:

1. **Write in complete sentences** with correct punctuation — unlike Linux kernel style, Go style uses full sentences in the body
2. **Explain what and why** — The diff shows how; the body should explain what the change accomplishes and why it is needed
3. **Provide context** — Don't assume the reader understands the original problem
4. **Be specific** — Include relevant function names, types, or error messages

Formatting rules:
- Separate from subject with a blank line
- Wrap at ~72 characters (not strictly enforced, but preferred)
- Write in complete sentences with correct punctuation
- **No Markdown** — no backticks, no bold, no headers, no code blocks with ```
- Code examples: indent with spaces or tabs, but do not use Markdown fencing
- When referencing CLs (Gerrit changelists), use `CL nnnnn` or `go.dev/cl/nnnnnn` shortlinks, not direct Gerrit URLs or git hashes
- When moving code between repos, include the CL number, repository name, and git hash

### Issue References (Footer)

Go has specific conventions for referencing issues:

| Reference | Meaning |
|-----------|---------|
| `Fixes #12345` | This commit fully fixes the issue (auto-closes on merge) |
| `For #12345` | This commit is related to but does not fully fix the issue |
| `Updates #12345` | Acceptable alternative to `For` (though less precise) |

Rules:
- Place issue references at the bottom, separated from the body by a blank line
- Use `Fixes` only when the commit completely resolves the issue
- Use `For` when the commit is working toward a fix but is not complete
- Do NOT use other GitHub aliases like `Close` or `Resolves`
- For `golang.org/x/...` repos, fully-qualify: `Fixes golang/go#1234`
- A trailing period is acceptable but not required: `Fixes #12345.`

### What Go Style Does NOT Use

- **No Markdown** — no backticks, bold, headers, or fenced code blocks
- **No Signed-off-by** — Go uses Gerrit bots for CLA compliance instead
- **No type prefixes** — no `feat:`, `fix:`, `chore:` etc. The package path serves as the scope
- **No Conventional Commits** — Go predates and does not follow this convention

### Go Style Examples

Simple fix:
```
net/smtp: let NewClient detect TLS connections

NewClient now checks whether the underlying connection is already
using TLS, so that callers don't need to check this separately.
```

Feature with issue reference:
```
math: improve Sin, Cos and Tan precision for very large arguments

The existing implementation has poor numerical properties for
large arguments, so use the McGillicutty algorithm to improve
accuracy above 1e10.

The algorithm is described in detail at https://example.com/paper.

Fixes #12345
```

Bug fix with CL reference:
```
cmd/go: fix non-Git repository failures

The go command would crash when encountering a directory that
looked like a Git repository but was missing essential files.
Add a check for the .git/HEAD file before attempting to run
Git commands.

CL 69670 originally introduced this regression by removing
the error check for missing repository files.

For #22272
```

Cross-repo change:
```
cipher/rot13: add new super secure cipher

Implement the ROT13 cipher as a new package in x/crypto.
This is a commonly requested cipher for educational purposes.

Fixes golang/go#1234
```

---

## Analysis Guidelines

When analyzing the staged diff, follow these principles:

### Determine the Type / Subsystem / Package

**For Conventional Commits:**
- New functions, classes, or endpoints → `feat`
- Bug fixes, error handling additions → `fix`
- Only comments, README, or doc changes → `docs`
- Whitespace, formatting, lint fixes → `style`
- Code restructuring without behavior change → `refactor`
- Performance optimizations → `perf`
- Test file additions or modifications → `test`
- Build config, dependency changes → `build`
- CI/CD pipeline changes → `ci`
- Other non-src changes → `chore`

**For Linux Kernel Style:**
- Identify the subsystem from the file path and affected code area
- Use the most specific reasonable subsystem prefix
- If multiple subsystems are affected, use the primary one or a combined prefix like `net/sched`
- Common mapping from paths: `mm/` → `mm`, `net/` → `net`, `fs/` → `fs`, `drivers/gpu/drm/` → `drm`, `arch/x86/` → `x86`

**For Go Style:**
- Identify the package from the directory path of the changed files
- Use the Go import path as the prefix: `net/http`, `cmd/go`, `runtime`
- For standard library: use the package directory name
- For `golang.org/x/...` repos: use the package path without the repo prefix
- If multiple packages are affected, use the primary one or `all` for cross-cutting changes
- Common mapping: `src/net/http/` → `net/http`, `src/cmd/go/` → `cmd/go`, `src/runtime/` → `runtime`

### Write the Subject
- Summarize the most significant change
- If multiple unrelated changes exist, suggest the user split the commit
- Be specific and factual
- **Conventional Commits**: lowercase, no period, under 72 chars
- **Linux Kernel**: Capitalized after colon, no period, under 75 chars
- **Go**: lowercase after colon, no period, under 72 chars, completes "This change modifies Go to ___"

### Write the Body
- **Conventional Commits**: Explain the what and why; keep it concise; imperative mood
- **Linux Kernel Style**: Follow the problem → impact → fix structure
  - Describe the problem and why it matters
  - Describe user-visible impact (crashes, regressions, etc.)
  - Quantify performance claims with numbers
  - Explain the fix and why this approach was chosen
  - Reference prior commits with 12+ char SHA + summary
- **Go Style**: Write in complete sentences with correct punctuation
  - Explain what the change accomplishes and why
  - Provide context for the change
  - No Markdown formatting
  - Reference CLs with `CL nnnnn` or `go.dev/cl/nnnnnn`

### Handle Breaking Changes
- **Conventional Commits**: Use `BREAKING CHANGE:` in the footer
- **Linux Kernel Style**: Describe the impact in the body and note what users need to change
- **Go Style**: Describe the impact in the body; Go proposals handle API changes separately

---

## Variant A: Chinese Version (中文友好版)

The Chinese version follows the same structural format as the detected base style, but writes the content in Chinese. This is useful for teams that communicate primarily in Chinese.

### Format Mapping

| Base Style | Chinese Format |
|-----------|----------------|
| Conventional Commits | `类型(范围): 中文描述` |
| Linux Kernel | `子系统: 中文描述` |
| Go | `包路径: 中文描述` |

### Type Translation (Conventional Commits)

| English Type | Chinese Type |
|-------------|-------------|
| `feat` | `新增` |
| `fix` | `修复` |
| `docs` | `文档` |
| `style` | `样式` |
| `refactor` | `重构` |
| `perf` | `性能` |
| `test` | `测试` |
| `build` | `构建` |
| `ci` | `持续集成` |
| `chore` | `杂务` |
| `revert` | `回退` |

### Chinese Version Rules

- The subject line uses Chinese, keeping the same prefix format as the base style
- The body is written in natural Chinese, using complete sentences
- Technical terms (function names, variable names, error messages, package names) remain in English
- Issue references remain in English: `Fixes #123`, `Closes #456`
- For Conventional Commits, the type keyword can be either English or Chinese — use whichever matches the project's convention. If the project already uses Chinese types, follow that pattern; otherwise, keep the type in English and only translate the description
- Keep the subject concise — Chinese characters convey more meaning per character, so aim for under 40 Chinese characters for the description part
- No period at the end of the subject line (Chinese period "。" is also omitted)
- Use Chinese punctuation in the body: "，" "。" "；" instead of "," "." ";"
- When mixing Chinese and English, add a space between them: "修复 Auth 模块的空指针问题" not "修复Auth模块的空指针问题"

### Chinese Version Examples

Conventional Commits base:
```
fix(auth): 修复 JWT token 刷新时的竞态条件

当多个请求同时触发 token 刷新时，存在竞态条件导致
部分请求使用了已过期的 token。添加互斥锁确保同一
时间只有一个刷新操作在进行。

Closes #123
```

Linux Kernel base:
```
mm: 修复 folio 迁移中的 use-after-free 问题

在 folio 迁移过程中，如果目标页面已被释放，后续
对该 folio 的访问会触发 use-after-free。在迁移
前增加页面有效性检查，避免对已释放页面的访问。

Fixes: abc123456789 ("mm: add folio migration support")
Signed-off-by: 张三 <zhangsan@example.com>
```

Go base:
```
net/http: 修复 TLS 连接检测失败的问题

NewClient 在某些情况下无法正确检测已有的 TLS 连接，
导致不必要的 TLS 握手重试。增加对底层连接 TLS 状态
的检查，避免重复握手。

Fixes #456
```

---

## Variant B: Emoji Version (Emoji 活力版)

The Emoji version adds a type-indicating emoji at the beginning of the subject line, making the commit history more visually scannable and expressive. It follows the gitmoji convention.

### Format

```
<emoji> <base style subject>

<body>
```

### Emoji Mapping

| Emoji | Type | Conventional Commits | Linux Kernel / Go Context |
|-------|------|---------------------|--------------------------|
| ✨ | New feature | `feat` | New functionality added |
| 🐛 | Bug fix | `fix` | Bug fix |
| 📝 | Documentation | `docs` | Documentation changes |
| 💄 | Style/UI | `style` | Code style, formatting |
| ♻️ | Refactor | `refactor` | Code restructuring |
| ⚡ | Performance | `perf` | Performance improvement |
| ✅ | Test | `test` | Adding/updating tests |
| 👷 | Build/CI | `build` / `ci` | Build or CI changes |
| 🔧 | Chore | `chore` | Maintenance tasks |
| ⏪ | Revert | `revert` | Reverting a change |
| 🔒 | Security | `fix` (security) | Security fix |
| 🚀 | Release | — | Release/deployment |
| 🚑 | Hotfix | `fix` (critical) | Critical hotfix |
| 💥 | Breaking change | `feat`/`fix` + BREAKING | Breaking change |
| 🔀 | Merge | — | Merge branch |
| 🗑️ | Removal | `refactor` / `chore` | Removing code/files |
| 🌐 | Internationalization | `feat` (i18n) | i18n/l10n changes |
| 📦 | Dependencies | `build` | Dependency updates |
| 🔥 | Remove code/files | `chore` | Removing unused code |

### Emoji Version Rules

- Place exactly one emoji at the very beginning of the subject line, followed by a space
- Choose the emoji based on the **primary intent** of the change, not every aspect
- The rest of the subject line follows the base style format exactly
- The body and footer remain unchanged from the standard version — no emojis in the body
- If the change has multiple aspects, pick the emoji for the most significant one
- For breaking changes, use 💥 as the emoji AND include the standard `BREAKING CHANGE:` footer
- Do not use multiple emojis in the subject line
- The emoji is purely decorative and does not replace the type prefix in Conventional Commits

### Emoji Version Examples

Conventional Commits base:
```
✨ feat(auth): add jwt token refresh mechanism
```

```
🐛 fix(api): handle null response from user endpoint

The user profile endpoint was returning a 500 error when the user
had no profile data. Added null check before accessing profile fields.
```

```
💥 feat(db): migrate user schema to support multi-tenant

BREAKING CHANGE: User model now requires tenant_id field. Existing
queries must be updated to include tenant context.
```

Linux Kernel base:
```
🐛 mm: Fix use-after-free in folio migration
```

```
✨ io_uring: Add multishot uring_cmd buffer selection support

Allow multishot uring_cmd requests to use provided buffer selection,
enabling zero-copy multishot operation for drivers that support it.

Signed-off-by: John Smith <john@example.com>
```

Go base:
```
🐛 net/http: fix TLS connection detection
```

```
✨ math: improve Sin, Cos and Tan precision for very large arguments

The existing implementation has poor numerical properties for
large arguments, so use the McGillicutty algorithm to improve
accuracy above 1e10.

Fixes #12345
```

Combined Chinese + Emoji:
```
🐛 fix(auth): 修复 JWT token 刷新时的竞态条件

当多个请求同时触发 token 刷新时，存在竞态条件导致
部分请求使用了已过期的 token。添加互斥锁确保同一
时间只有一个刷新操作在进行。

Closes #123
```

---

## Output Presentation

When presenting the generated commit messages to the user, use the following format:

```
## Standard Version
<standard commit message>

## 中文友好版
<chinese commit message>

## Emoji 活力版
<emoji commit message>
```

If the user only requests one variant, output only that variant. If the user does not specify, output all three variants by default.

---

## Rules

1. **Always inspect staged changes first** — never guess or assume what changed
2. **Never commit on behalf of the user** — always present the message for review first
3. **Suggest splitting commits** if staged changes span multiple unrelated concerns
4. **Use English** for standard commit messages; use Chinese for the 中文友好版 variant
5. **Do not include trivial changes** as the primary subject if more significant changes exist
6. **Keep the subject line under 72 characters** (Conventional/Go) or 75 characters (Kernel); for Chinese version, under 40 Chinese characters for the description part
7. **Do not reference temporary or local file paths** in the commit message
8. **If no changes are staged**, inform the user and suggest `git add` commands
9. **If the diff is too large**, summarize the major themes and suggest splitting
10. **Never include sensitive information** (keys, passwords, tokens) in commit messages
11. **Use imperative mood** for subjects: "add feature" / "Add feature" not "Added feature"
12. **For kernel style**: always describe the problem before the fix; always describe user-visible impact
13. **For kernel style**: quantify performance claims with numbers and describe trade-offs
14. **For kernel style**: reference prior commits with 12+ char SHA + oneline summary
15. **For kernel style**: include appropriate trailers (Fixes, Signed-off-by, etc.) when applicable
16. **For Go style**: lowercase after the colon in the subject line
17. **For Go style**: write the body in complete sentences with correct punctuation
18. **For Go style**: no Markdown formatting in the commit message
19. **For Go style**: do not add Signed-off-by lines
20. **For Go style**: use `Fixes #nnnn` for complete fixes, `For #nnnn` for partial work
21. **For Go style**: reference CLs with `CL nnnnn` or `go.dev/cl/nnnnnn`, not Gerrit URLs
22. **For Chinese version**: keep technical terms (function names, variables, errors, packages) in English
23. **For Chinese version**: use Chinese punctuation in the body (，。；) and add spaces between Chinese and English text
24. **For Chinese version**: no period at end of subject (omit both "." and "。")
25. **For Emoji version**: use exactly one emoji at the start of the subject line, followed by a space
26. **For Emoji version**: choose emoji based on primary intent; do not use multiple emojis
27. **For Emoji version**: emoji does not replace the type prefix — keep `✨ feat(scope):` not `✨ (scope):`
28. **For Emoji version**: no emojis in the body or footer
29. **Output all three variants** (Standard, 中文友好版, Emoji 活力版) by default unless the user requests only one
