# skills

Chulan 的个人 Agent Skills，收录 Git 等日常开发工作流，支持按需安装与复用。

## 技能列表

### Git

- [git-commit-zh](skills/git-commit-zh/SKILL.md)：根据 Git 变更生成中文 Conventional Commit 消息。有暂存内容时仅覆盖暂存变更；默认只生成消息，明确要求后才执行提交。

## 安装

使用 [skills CLI](https://github.com/vercel-labs/skills)，需要 Node.js 22.20 或更高版本。

查看本仓库可用的技能：

```bash
npx skills add Chulan-824/skills --list
```

将 `git-commit-zh` 安装到 Codex，跨项目使用：

```bash
npx skills add Chulan-824/skills --skill git-commit-zh --agent codex --global
```

仅安装到当前项目时，去掉 `--global`。为其他 Agent 安装时，修改 `--agent`；省略该参数可交互选择。

## 使用

在 Git 仓库中对 Codex 输入：

```text
使用 $git-commit-zh 根据当前变更生成提交信息
```

默认只生成可复制的消息，不暂存或提交文件。

## 更新

```bash
npx skills check
npx skills update git-commit-zh --global
```

在本仓库中维护技能源码，再更新安装。首次从现有本机技能迁入时，先保留原安装，检查同名目录并备份本地修改后再切换来源。

## 目录约定

```text
skills/
├── AGENTS.md
├── LICENSE
├── README.md
└── skills/
    └── git-commit-zh/
        └── SKILL.md
```

每个技能独立存放在 `skills/<name>/`，目录名与 `SKILL.md` 中的 `name` 一致。使用名称前缀和本页分类组织领域；需要脚本、参考资料或模板时，再在对应技能内添加。

## 许可证

[MIT](LICENSE)
