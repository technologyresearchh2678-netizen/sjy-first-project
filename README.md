# commit-message-generator

> 基于 Conventional Commits 规范的智能提交信息生成器

自动分析 git diff 内容，生成符合 [Conventional Commits](https://www.conventionalcommits.org/) 规范的提交信息。支持三档详细程度候选，让每次提交都清晰专业。

## 特性

- **自动分类** — 识别 feat / fix / docs / refactor 等 10+ 提交类型
- **三档候选** — 简短版 / 标准版 / 详细版，按需选择
- **零配置** — 开箱即用，无需额外依赖
- **可扩展** — 支持自定义 agent 配置和提示词

## 项目结构

```
commit-message-generator/
├── SKILL.md              # Skill 定义（Agent Skills 规范）
├── agents/
│   └── openai.yami       # OpenAI 格式 Agent 配置
├── LICENSE               # MIT 许可证
└── README.md             # 本文件
```

## 使用方法

### 方式一：作为 Agent Skill 加载

将技能目录放入 `.agents/skills/` 中，智能体运行时会自动发现并加载：

```bash
# 复制到项目级技能目录
cp -r commit-message-generator .agents/skills/

# 或用户级目录
cp -r commit-message-generator ~/.agents/skills/
```

### 方式二：使用 skills CLI

```bash
# 从本地目录安装
npx skills add ./commit-message-generator

# 从 GitHub 安装（发布后）
npx skills add your-username/commit-message-generator
```

### 方式三：直接使用 Agent 配置

通过 `agents/openai.yami` 直接加载 agent：

```bash
# 示例：使用支持 yami 格式的 agent 运行时
agent run --config agents/openai.yami
```

## 输入输出示例

### 输入

在已修改的 git 仓库中，向智能体发送：

```
帮我生成提交信息
```

技能会自动读取 `git diff` 结果，例如：

```diff
diff --git a/src/auth/login.ts b/src/auth/login.ts
index abc123..def456 100644
--- a/src/auth/login.ts
+++ b/src/auth/login.ts
@@ -10,6 +10,8 @@ export async function login(username, password) {
+  if (!password || password.length < 8) {
+    throw new Error("Password must be at least 8 characters");
+  }
   const user = await findUser(username);
```

### 输出

生成 3 个候选提交信息：

**候选 1 — 简短版**
```
fix(auth): add password length validation
```

**候选 2 — 标准版**
```
fix(auth): add password length validation

- Enforce minimum 8-character password requirement
- Throw descriptive error when validation fails
- Prevents weak passwords from being accepted
```

**候选 3 — 详细版**
```
fix(auth): add password length validation

- Add validation check for password length >= 8 characters
- Throw Error with clear message when validation fails
- Position check before database lookup for early failure
- Improves security by rejecting weak passwords upfront

Closes #42
```

## 许可证

MIT License
