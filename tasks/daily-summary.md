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

## ⚠️ 代理委派策略

本任务的 build agent 需要将具体工作委托给子代理。**务必按以下规则分配**：

| 工作类型 | 使用子代理 | 原因 |
| :--: | :--: | :--: |
| 扫描 git 仓库 / 本地文件 | `explore` | explore 具备完整的 find/ls/git log 权限 |
| 外部资料查询 | `researcher` | researcher 可访问 GitHub/npm 等外部源 |
| 兜底 | build agent 自己跑 bash | 如果子代理全部失败，不要放弃——build agent 已被授予 find/ls/git 权限，可直接执行命令 |

> ⚡ 关键：子代理失败一次不代表任务失败。先换策略，再降级到直接执行，最后才放弃。

## 📂 参数配置

| 参数 | 值 | 说明 |
| :--: | :--: | :--: |
| Git 扫描根目录 | `~/projects/` | 递归扫描所有子目录中的 git 仓库 |
| Obsidian Vault | `<YOUR_VAULT_PATH>/` | 每日笔记在 vault 根目录 |
| 看板笔记 | `2-技能/定时任务看板.md` | 执行日志写入目标 |
| 参考示例 | `2026-06-11.md` | 复盘格式参考 |

> 💡 迁移到其他机器时，只需修改上表中的路径。

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

### 3.5. 检查今日笔记质量

在写入复盘内容之前，先检查今天的每日笔记是否规范。使用 Obsidian 工具或 `read` 读取今日笔记，检查：

| 检查项 | 判断标准 | 不达标时 |
| :--: | :--: | :--: |
| Tags 丰富度 | frontmatter tags 至少包含 2 个主题标签（非 daily） | 在复盘建议中添加一条「📋 建议补充 tags」 |
| 内部链接数 | wiki-links ≥ 3 个 | 在复盘建议中添加一条「📋 建议增加内部链接」 |
| 评分是否填写 | score.total > 0 | 在复盘建议中添加一条「📋 今日评分尚未填写」 |

将检查结果以「📋 笔记健康提示」格式嵌入复盘内容的 AI 提升建议区块中（放在第 1 条建议之前），格式如下：

```
### 📋 笔记健康提示
- ⚠️ Tags 不足（当前仅 `daily`），建议添加 `#主题1 #主题2`
- ⚠️ 内部链接过少（当前仅 X 个），建议链接相关笔记
- ⚠️ 今日评分未填写，建议根据实际产出自评
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

### 5. 追加执行记录到看板（含失败记录）

无论任务成功或失败，都必须在 Obsidian vault 的 `2-技能/定时任务看板.md` 中追加一条执行记录。

**成功时**（复盘内容已写入每日笔记）：
```
- ⏱️ YYYY-MM-DD HH:MM | ✅ daily-summary | ⏱️ ~Xs | 发现 X 提交, X 笔记变更
```

**部分成功时**（扫描完成但写入失败）：
```
- ⏱️ YYYY-MM-DD HH:MM | ⚠️ daily-summary | ⏱️ ~Xs | 扫描成功但写入失败：{原因}
```

**完全失败时**（无法完成扫描）：
```
- ⏱️ YYYY-MM-DD HH:MM | ❌ daily-summary | ⏱️ ~Xs | 失败：{具体原因}
```

操作方式：
- 使用 `obsidian_append_to_note`（优先）或 `read` + `write`（降级）追加到看板笔记末尾
- 不要覆盖看板已有内容
- 执行记录是唯一的任务可观测性出口——**必须写入**，即使前面全部失败
