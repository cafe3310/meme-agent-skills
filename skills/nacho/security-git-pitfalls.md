# 安全与 Git 陷阱知识库 (Security & Git Pitfalls KB)

本文件汇总了数据安全、信息安全以及 Git 版本控制中常见的风险点与规避建议。

## 1. 凭据泄露与敏感信息 (Secrets & Credentials)
- **硬编码密钥**：将 API Key、数据库密码、Token 直接写在源码中。
  - **防呆**：使用环境变量或 Secret Manager (如 AWS Secrets Manager, HashiCorp Vault)。
- **误提交敏感文件**：将 `.env`, `id_rsa`, `config.json` 等包含私密信息的文件推送到仓库。
  - **防呆**：严格配置 `.gitignore`，并使用 `pre-commit` 钩子（如 `gitleaks`）进行扫描。
- **Git 历史残留**：在 commit 后删除密钥是不够的，历史记录中依然存在。
  - **防呆**：一旦泄露，必须**立即吊销密钥**，并使用 `git-filter-repo` 或 BFG 清理历史。

## 2. Git 操作与仓库管理 (Git & Repo Management)
- **“通配符”添加**：习惯性使用 `git add .` 或 `git add *` 而不进行 `git status` 审查。
- **强推覆盖 (Force Push)**：在多人协作的分支上使用 `git push --force`，破坏他人工作及审计轨迹。
- **缺乏分支保护**：允许直接向 `main` 分支推送，绕过 Code Review 和 CI 检查。
- **未签名的 Commit**：不使用 GPG/SSH 签名，导致他人可以轻易伪造你的提交身份。

## 3. 信息安全风险 (Information Security)
- **暴露 `.git` 目录**：Web 服务器配置不当，允许外部访问 `.git` 文件夹，导致源码全泄露。
- **权限过度放权**：给太多人分配“管理员”或“写”权限，违反最小特权原则 (PoLP)。
- **CI/CD 注入**：流水线配置不当，允许来自 Fork 仓库的 PR 访问生产环境的 Secrets。
- **影子访问**：不维护 SSH Key 或个人访问令牌 (PAT) 的生命周期，离职人员仍持有权限。

## 4. 安全防护 Checkbox
- [ ] 是否所有敏感配置都已移出源码？
- [ ] `.gitignore` 是否覆盖了所有本地开发环境文件？
- [ ] 是否开启了主分支保护规则？
- [ ] 是否配置了预提交扫描钩子？
- [ ] 关键提交是否使用了签名？
