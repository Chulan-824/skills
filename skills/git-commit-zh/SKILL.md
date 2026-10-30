---
name: git-commit-zh
description: >
  根据当前 git 变更生成中文 Conventional Commit message（type/scope/subject/body）。
  Use when the user wants a commit message, 生成提交信息, 写 commit, conventional
  commit 中文, or /git-commit-zh. Generate only unless the user explicitly asks to commit.
---

# Git Commit 中文消息

根据当前 git 变更，产出一条符合 **Conventional Commits** 的中文 commit message。默认**只生成、不提交**。

## Steps

### 1. 收集变更

在仓库根目录一次取齐上下文：

```bash
printf '=== STATUS ===\n'; git status -sb
printf '\n=== STAGED ===\n'; git diff --cached
printf '\n=== UNSTAGED ===\n'; git diff
printf '\n=== UNTRACKED (names) ===\n'; git ls-files --others --exclude-standard
printf '\n=== LOG ===\n'; git log --oneline -10 2>/dev/null || true
```

- 有 **staged** 内容：以 staged 为主写 message；unstaged/untracked 仅作边界提示。
- 无 staged、有 unstaged/untracked：以全部未提交变更为准。
- 工作区干净：说明无可提交变更并停止。
- Diff 过大（约 >500 行）：先按 `git diff --stat`（或 staged 的 `--cached --stat`）分文件归类意图，再抽读关键 hunk，避免只根据文件名瞎猜。

**完成标准**：已拿到 status + 有效 diff（或确认干净）；清楚 message 覆盖的是 staged 还是全部未提交变更。

### 2. 定 type 与 scope

对变更做一次意图归类，选出**唯一主 type**（见下方 Types）。多意图并存时：

- 能分清主次 → 用主意图的 type，次要点放 body；
- 明显无关的多块变更 → 为每块各写一条 candidate message，并提示应拆成多次 commit。

`scope` 取受影响的模块/目录/包名（简短英文或项目惯用标识）；影响面宽或说不清时**省略 scope**。

`git log` 只用于对齐本仓库既有 type/scope 用词习惯；**subject/body 仍用中文**，格式仍用 Conventional Commits。

**完成标准**：写出 `type`、可选 `scope`，以及一句归类理由（内部用，可不必展示给用户）。

### 3. 起草 message

按下方**格式**与**中文写法**生成完整 message。Subject 写 **why not what**：说明意图/价值，而不是罗列改了哪些文件。

**完成标准**：得到一条可直接粘贴进 `git commit` 的完整文本；subject ≤ 50 个汉字或约 72 字符；需要时有空行后的 body。

### 4. 输出

仅输出候选 message（可附一行 type/scope 选择说明）。使用可复制代码块：

```text
type(scope): 中文主题

可选正文……
```

未经用户**明确**要求（如「帮我 commit」「提交」），不执行 `git add` / `git commit`。用户随后要求提交时，用 HEREDOC 保留换行：

```bash
git commit -m "$(cat <<'EOF'
type(scope): 中文主题

可选正文
EOF
)"
```

**完成标准**：用户能复制 message；是否提交的决定权在用户。

---

## Reference

### 格式

```
<type>[optional scope][optional !]: <中文 subject>

[optional body]

[optional footer]
```

| 部件 | 规则 |
|------|------|
| `type` | 英文小写，见 Types |
| `scope` | 可选；括号包裹，如 `(auth)`、`(api)` |
| `!` | 破坏性变更时写在 `:` 前，如 `feat(api)!: ...`；footer 可补 `BREAKING CHANGE: ...` |
| subject | 中文；一句话；句末不加句号 |
| body | 可选；说明动机、取舍、影响面；中文；与 subject 空一行 |
| footer | 可选；`Fixes #123`、`BREAKING CHANGE:` 等 |

### Types

| type | 何时用 |
|------|--------|
| `feat` | 用户可感知的新能力 |
| `fix` | 修复缺陷或错误行为 |
| `refactor` | 重构；行为不变 |
| `perf` | 性能优化 |
| `docs` | 仅文档 |
| `test` | 仅测试 |
| `build` | 构建系统或外部依赖 |
| `ci` | CI 配置/脚本 |
| `chore` | 杂项维护（工具、脚手架等） |
| `style` | 纯格式（空格、分号等），不影响语义 |
| `revert` | 回滚某次提交 |

`fix` vs `feat`：补齐本应可用的行为 → `fix`；新增此前不存在的能力 → `feat`。

### 中文写法

- Subject：**why not what**——「避免重复提交订单」优于「修改 OrderService」。
- 语气：陈述结果/意图，简短直接；不写「本次提交」「我修改了」。
- Body：非显而易见时再写——动机、兼容性、迁移注意；列表用 `- `。
- 专有名词、API、类名、路径可保留英文原样。
- 一条 message 只表达一个主意图；其余放 body 或拆成多条 candidate。

### 示例

```text
feat(auth): 支持手机号验证码登录

新增短信校验与登录接口字段；旧密码登录保持兼容。
```

```text
fix(cart): 结算时正确扣减优惠券库存

并发下单可能超扣；改为库存预占后再确认。
```

```text
refactor(parser): 拆分词法与语法阶段

行为不变，便于单独为 lexer 补测试。
```
