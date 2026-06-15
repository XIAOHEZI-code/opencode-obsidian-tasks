---
description: 每日复盘 — 扫描今日 git 提交与 Obsidian 笔记变更，生成复盘总结
schedule: "0 23 * * *"
cwd: ~
agent: build
permission:
  obsidian_*: "allow"
  read: "allow"
  glob: "allow"
  grep: "allow"
  bash:
    "*": "deny"
    "git log*": "allow"
    "git diff*": "allow"
    "git status*": "allow"
    "git branch*": "allow"
    "find *": "allow"
    "ls *": "allow"
    "date *": "allow"
    "whoami": "allow"
    "pwd": "allow"
  edit: "deny"
  write:
    "*": "deny"
    "<YOUR_VAULT_PATH>/*": "allow"                           # 👈 change to your vault path
    "<YOUR_VAULT_PATH>/2-技能/定时任务看板.md": "allow"       # 👈 change to your vault path
---

# 任务：生成今日每日复盘

请帮我生成今日的每日复盘笔记。

## 执行步骤

### 1. 扫描今日 Git 提交
扫描 `~/projects/` 下所有 git 仓库今日的提交记录：  # 👈 change to your projects directory
- 对于每个仓库，运行 `git log --since="2026-06-15" --oneline --all`（日期替换为今天）
- 记录项目名、commit hash、提交信息、日期

### 2. 扫描今日 Obsidian 笔记变更
- 使用 Obsidian 工具检查 vault 中今天创建或修改的笔记
- 按分类整理（系统运维 / 项目开发 / 技术研究 / 其他）

### 3. 生成复盘内容
将以上信息组织成如下格式，写入今天的 Obsidian 每日笔记（如 `2026-06-15.md`）：

```markdown
## 每日复盘

### 📊 活动汇总 (今日数据)

#### 💻 Git 提交
- **项目名**
  - `commit_hash` - 提交信息 (日期 时间)
  - ...

#### 📝 Obsidian 笔记变更
- **分类名**：笔记1、笔记2...

---

### 💡 AI 提升建议

1. **建议标题**
   - **观察**：...
   - **建议**：...
```

### 4. 写入 Obsidian（含降级）

首先尝试使用 Obsidian 工具将复盘内容**追加**到今天的每日笔记中（不要覆盖已有内容，使用 append/patch 方式）。

如果 Obsidian API 不可用（连接被拒绝、404、超时等），则降级为直接文件写入：
- 使用 `read` 检查 `<YOUR_VAULT_PATH>/YYYY-MM-DD.md`（今天的每日笔记，替换为你的 vault 路径）是否已存在
- 如果不存在，使用 `write` 创建文件并写入 frontmatter + 复盘内容
- 如果已存在，使用 `write` 将复盘内容追加到文件末尾（注意：先 `read` 读取已有内容，拼接后再写入）
- 确保不覆盖已有内容，始终保留原有笔记数据

## 参考格式
参考已有的复盘示例：Obsidian vault 中的 `2026-06-11.md` 笔记，它的「每日复盘」章节格式就是目标格式。

## 注意事项
- 如果今天没有任何 git 提交或笔记变更，如实说明
- 复盘内容要简洁、有条理
- AI 建议要具体、可操作
- 如果今天的每日笔记还不存在，先创建它
- 降级文件写入时，确保不覆盖已有内容，使用追加模式（先 `read` 读取已有内容，拼接后再 `write`）

### 5. 追加执行记录到看板

复盘写入完成后，在 Obsidian vault 的 `2-技能/定时任务看板.md` 中追加一条执行记录。

执行记录格式：
```
- ⏱️ YYYY-MM-DD HH:MM | ✅ daily-summary | ⏱️ ~Xs | 发现 X 个提交, X 个笔记变更
```

操作方式：
- 使用 `obsidian_append_to_note`（优先）或 `read` + `write`（降级）追加到笔记末尾
- 不要覆盖看板已有内容
- 如果今天有无 git 提交或笔记变更，如实记录（如 "发现 0 提交, 3 笔记变更"）
