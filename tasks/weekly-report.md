---
description: 每周总结 — 汇总本周 git 提交与 Obsidian 笔记，生成结构化周报
schedule: "0 9 * * 0"
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
    "<YOUR_VAULT_PATH>/3-输出/周报_*": "allow"              # 👈 change to your vault path
    "<YOUR_VAULT_PATH>/2-技能/定时任务看板.md": "allow"      # 👈 change to your vault path
---

# 任务：生成本周周报

请帮我生成本周的周报总结。

## 执行步骤

### 1. 扫描本周 Git 提交
扫描 `~/projects/` 下所有 git 仓库本周（过去7天）的提交：  # 👈 change to your projects directory
- 对每个仓库运行 `git log --since="7 days ago" --oneline --all`
- 按项目分组整理

### 2. 扫描本周 Obsidian 笔记
- 使用 Obsidian 工具检查 vault 中本周创建或修改的笔记
- 按分类整理（系统运维 / 项目开发 / 技术研究 / 其他）
- 提取每篇笔记的核心要点（1-2句话）

### 3. 生成周报
生成如下格式的周报：

```markdown
# 周报 {本周日期范围，如 2026-06-09 ~ 2026-06-15}

## 📊 本周概览
- 新增笔记：X 篇
- 活跃项目：X 个
- 提交总数：X 次
- 活跃主题：{关键词}

## 🔧 系统运维
| 日期 | 内容 | 要点 |
|------|------|------|

## 💻 项目开发
| 项目 | 提交数 | 关键变更 |
|------|--------|----------|

## 📝 知识沉淀
| 日期 | 笔记 | 核心要点 |
|------|------|----------|

## 💡 本周收获
- 关键洞察 1
- 关键洞察 2

## 📅 下周计划
- 待跟进事项
```

### 4. 保存周报
- 将周报保存到 Obsidian vault 的 `3-输出/周报_{结束日期}.md`
- 同时在今天的每日笔记中追加一条链接指向周报

## 参考
- Obsidian vault 中的 `2-技能/周报总结.md` 定义了周报的详细格式规范
- vault 中的 `1-笔记/项目索引.md` 包含所有项目的索引信息

### 5. 追加执行记录到看板

周报保存完成后，在 Obsidian vault 的 `2-技能/定时任务看板.md` 中追加一条执行记录。

执行记录格式：
```
- ⏱️ YYYY-MM-DD HH:MM | ✅ weekly-report | ⏱️ ~Xs | 周报已保存至 3-输出/周报_YYYY-MM-DD.md
```

操作方式：
- 使用 `obsidian_append_to_note`（优先）或 `read` + `write`（降级）追加到笔记末尾
- 不要覆盖看板已有内容

## 注意事项
- 周报要突出重点，不要流水账
- 项目索引中的项目应该作为扫描目标
- 如果本周活动很少，如实反映，不要编造内容
