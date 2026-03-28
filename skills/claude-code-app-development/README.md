# Claude Code App Development Skill

使用 Claude Code 進行應用程式開發的 Skill，基於 Spec-Driven Development (SDD) 方法論。

## 功能

- **Spec-Driven Development** - 使用 Spec Kit 進行結構化開發
- **TypeScript + Bun Stack** - React, TailwindCSS, Elysia.js, Prisma
- **Anysystem Design** - 使用統一的 UI 組件庫
- **AWS CDK** - 部署 Serverless 測試環境
- **Playwright MCP** - 自動化 UI 測試
- **AWS CodeCommit** - Git 版本控制

## 安裝方式

### 方法一：作為 Command 安裝（推薦）

將 `commands/app-development.md` 複製到 commands 目錄：

```bash
# 複製命令文件
cp /Users/davidchu/Sites/localhost/dev_agent/skills/claude-code-app-development/commands/app-development.md \
   /Users/davidchu/www/dev_env/claude/commands/app-development.md
```

或使用符號鏈接（推薦，便於更新）：

```bash
ln -s /Users/davidchu/Sites/localhost/dev_agent/skills/claude-code-app-development/commands/app-development.md \
      /Users/davidchu/www/dev_env/claude/commands/app-development.md
```

### 方法二：作為 Plugin 安裝

```bash
# 複製整個 skill 目錄到 plugins
cp -r /Users/davidchu/Sites/localhost/dev_agent/skills/claude-code-app-development \
      ~/.claude/plugins/cache/claude-code-app-development
```

## 使用方法

在 Claude Code 中使用：

```
/app-development <項目需求描述>
```

例如：
```
/app-development 建立一個任務管理系統，用戶可以建立專案、分配任務、追蹤進度
```

## 開發流程

```
0. Setup        →  建立 CodeCommit Repo + Git Init
1. Initialize   →  specify init --ai claude
2. Constitution →  /speckit.constitution
3. Specify      →  /speckit.specify
4. Clarify      →  /speckit.clarify (REQUIRED)
5. Plan         →  /speckit.plan
6. Tasks        →  /speckit.tasks
7. Implement     →  /speckit.implement
8. Test         →  Playwright MCP Tools
9. Fix (if needed) → Claude Code Edit/Read
```

## 交付物

- **測試環境 URL** - 可訪問的測試環境
- **測試帳號** - 如果需要登入認證
- **快速測試指南** - 關鍵用戶流程

## 技術棧

| 層 | 技術 |
|---|------|
| Runtime | Bun |
| 語言 | TypeScript |
| 前端 | React + TailwindCSS + Anysystem Design |
| 後端 | Elysia.js |
| ORM | Prisma |
| 資料庫 | PostgreSQL / SQLite |
| 雲端 | AWS CDK (Lambda, API Gateway, DynamoDB) |

## 前置要求

- [ ] Bun 已安裝
- [ ] Node.js 18+
- [ ] AWS CLI 已配置
- [ ] AWS CodeCommit 權限
- [ ] specify CLI (`uv tool install specify-cli`)
- [ ] Playwright MCP 已啟用

## 詳細文檔

- [SKILL.md](./SKILL.md) - 主要 Skill 文件
- [references/tech-stack.md](./references/tech-stack.md) - 技術棧詳解
- [references/integration.md](./references/integration.md) - 前後端整合
- [references/aws-cdk.md](./references/aws-cdk.md) - AWS CDK 部署
- [references/testing.md](./references/testing.md) - Playwright 測試
- [references/codecommit.md](./references/codecommit.md) - CodeCommit 操作

## 檔案結構

```
claude-code-app-development/
├── README.md                    # 本文件
├── SKILL.md                     # 主要 Skill 定義
├── commands/
│   └── app-development.md       # OpenClaw Command 文件
└── references/
    ├── tech-stack.md           # 技術棧詳解
    ├── integration.md          # 前後端整合
    ├── aws-cdk.md              # AWS CDK 部署
    ├── testing.md              # Playwright 測試
    ├── spec-kit-workflow.md    # SDD 工作流
    ├── claude-capabilities.md  # Claude Code 能力
    ├── project-structure.md     # 專案結構
    └── codecommit.md           # CodeCommit 操作
```
