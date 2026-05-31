# 团队协作SOP

> 团队 Git 协作标准化流程

## 一、分支管理策略

### 分支结构

```
main (生产环境)
├── develop (开发环境)
├── feature/xxx (功能开发)
├── fix/xxx (bug修复)
├── release/x.x.x (发布版本)
└── hotfix/xxx (紧急修复)
```

### 分支说明

| 分支 | 用途 | 来源 | 合并目标 |
|------|------|------|----------|
| main | 生产环境代码 | - | - |
| develop | 日常开发 | main | main |
| feature/xxx | 新功能 | develop | develop |
| fix/xxx | 修复bug | develop | develop |
| release/x.x.x | 发布准备 | develop | main, develop |
| hotfix/xxx | 紧急修复 | main | main, develop |

## 二、开发流程

### 新功能开发流程

```bash
# 1. 从 develop 创建功能分支
git checkout develop
git pull --rebase
git checkout -b feature/user-login

# 2. 开发并提交
git add .
git commit -m "feat(login): 添加用户登录功能"

# 3. 同步 develop 最新代码
git fetch origin
git rebase origin/develop

# 4. 推送到远程
git push origin feature/user-login

# 5. 创建 Pull Request
# 在 GitHub/GitLab 上创建 PR，合并到 develop
```

### Bug 修复流程

```bash
# 1. 从 develop 创建修复分支
git checkout develop
git pull --rebase
git checkout -b fix/login-error

# 2. 修复并提交
git add .
git commit -m "fix(login): 修复登录验证失败问题"

# 3. 创建 PR 合并到 develop
```

### 紧急修复流程

```bash
# 1. 从 main 创建 hotfix 分支
git checkout main
git pull --rebase
git checkout -b hotfix/critical-bug

# 2. 修复并提交
git add .
git commit -m "hotfix: 修复生产环境关键bug"

# 3. 创建 PR 合并到 main 和 develop
# 需要两个 PR：一个到 main，一个到 develop
```

## 三、提交规范

### 提交信息格式

```
<type>(<scope>): <subject>

<body>

<footer>
```

### type 说明

```
feat:     新功能
fix:      修复bug
docs:     文档修改
style:    代码格式修改
refactor: 代码重构
test:     测试相关
chore:    构建/工具
perf:     性能优化
```

### 示例

```
feat(login): 添加手机号登录功能

实现手机号+验证码登录，支持60秒重新发送。
添加登录状态持久化，7天免登录。

Closes #123, #456
```

## 四、Code Review 流程

### 创建 PR 要求

- [ ] 提供清晰的 PR 标题和描述
- [ ] 关联相关的 Issue 或需求
- [ ] 确保 CI 测试通过
- [ ] 确保代码风格符合规范
- [ ] 确保没有冲突

### Review 检查清单

**功能检查**：
- [ ] 代码实现了需求中的功能
- [ ] 处理了异常情况
- [ ] 边界条件已考虑

**代码质量**：
- [ ] 代码逻辑清晰
- [ ] 命名规范合理
- [ ] 没有重复代码
- [ ] 注释合理

**安全性**：
- [ ] 没有敏感信息泄露
- [ ] 输入验证完整
- [ ] 权限控制正确

**性能**：
- [ ] 没有性能隐患
- [ ] 数据库查询合理
- [ ] 缓存策略合适

### Review 原则

1. **及时原则**：PR 创建后 24 小时内完成 Review
2. **尊重原则**：评论具体代码而非个人
3. **学习原则**：Review 是双向学习的过程
4. **合理原则**：遵循项目规范，不过度追求完美

## 五、发布管理流程

### 版本号规范

采用语义化版本号（Semantic Versioning）：

```
主版本号.次版本号.修订号
```

| 版本变化 | 规则 | 示例 |
|----------|------|------|
| 不兼容的API修改 | 主版本号 | 1.0.0 → 2.0.0 |
| 向下兼容的功能新增 | 次版本号 | 1.0.0 → 1.1.0 |
| 向下兼容的问题修复 | 修订号 | 1.0.0 → 1.0.1 |

### 发布流程

```bash
# 1. 从 develop 创建 release 分支
git checkout develop
git checkout -b release/v1.2.0

# 2. 修改版本号、更新 CHANGELOG
# 3. 修复测试中发现的问题
# 4. 合并到 main
git checkout main
git merge --no-ff release/v1.2.0
git tag -a v1.2.0 -m "v1.2.0"
git push origin main --tags

# 5. 合并回 develop
git checkout develop
git merge --no-ff release/v1.2.0

# 6. 删除 release 分支
git branch -d release/v1.2.0
```

## 六、冲突解决流程

### 预防冲突

1. **经常同步**：定期拉取最新代码
2. **小提交**：每次提交改动量小
3. **单一职责**：每次提交只做一件事
4. **及时更新**：PR 合并前 rebase 最新代码

### 解决冲突

```bash
# 1. 拉取最新代码
git pull --rebase origin develop

# 2. 解决所有冲突文件
# 打开冲突文件，选择保留的内容

# 3. 标记冲突已解决
git add 已解决的文件

# 4. 继续 rebase
git rebase --continue

# 如果搞乱了
git rebase --abort
```

## 七、Git 配置规范

### 必须配置项

```bash
git config --global user.name "真实姓名"
git config --global user.email "公司邮箱"
git config --global core.autocrlf input
git config --global pull.rebase true
```

### 推荐配置项

```bash
git config --global init.defaultBranch main
git config --global commit.gpgsign false
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.df diff
git config --global alias.unstage 'reset HEAD --'
```