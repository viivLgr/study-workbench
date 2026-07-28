# 学习工作台 · 部署与维护指南

> 本文档记录工作台项目的完整架构和运维流程，确保项目长期可维护、可复现。

---

## 📌 项目概览

**目标**：每日接收学习文档（中英文均可），生成结构化总结，归档原始文档，通过可视化工作台网页在手机和 Mac 上同步查看。

**线上地址**：
- 工作台：https://viivlgr.github.io/study-workbench
- 仓库：https://github.com/viivLgr/study-workbench

---

## 📂 项目结构

```
study-workbench/
├── index.html              ← GitHub Pages 首页，自动跳转到工作台
├── 工作台.html             ← 【核心】可视化工作台（单文件，内嵌 JSON 数据）
├── README.md               ← 用户使用说明
├── DEPLOYMENT.md           ← 本文件，部署与维护指南
├── 索引.md                 ← 文本备份索引（日期 / 标题 / 状态）
├── 归档/                   ← 原始文档 Markdown 备份
│   └── YYYY-MM-DD_标题/
│       └── 原文.md
└── 总结/                   ← 每日总结 Markdown 备份
    └── YYYY-MM-DD_标题_总结.md
```

---

## 🔄 日常更新流程（每次收到新文档时）

收到用户发来的学习文档后，按以下步骤处理：

### 步骤 1：分析文档内容
- 阅读理解文档全文（中英文均可）
- 提炼结构化总结，包含 5 个模块：
  - 📌 一句话概览
  - 🔑 核心要点（3-7 条）
  - 📖 关键概念解释
  - 🛠 实践要点
  - 💡 我的点评

### 步骤 2：更新工作台 HTML
在 `工作台.html` 的 `DOCUMENTS` 数组**头部**插入新记录：

```javascript
{
  id: "YYYY-MM-DD_文档标题",
  date: "YYYY-MM-DD",
  title: "文档标题",
  language: "zh" | "en",
  source: "来源（可选）",
  overview: "一句话概览",
  keyPoints: ["要点1", "要点2", ...],
  concepts: [{term: "术语", explanation: "解释"}, ...],
  practice: ["实践要点1", ...],
  review: "点评内容",
  originalText: "原始文档完整内容"
}
```

> ⚠️ 注意：`id` 必须唯一；`date` 用 `YYYY-MM-DD` 格式；今日文档会自动置顶首页。

### 步骤 3：保存 Markdown 备份
- 原文：`归档/YYYY-MM-DD_标题/原文.md`
- 总结：`总结/YYYY-MM-DD_标题_总结.md`

### 步骤 4：更新索引
在 `索引.md` 的表格最上方插入一行新记录。

### 步骤 5：推送到 GitHub（触发自动部署）
```bash
cd /workspace/每日学习笔记
git add -A
git commit -m "新增 YYYY-MM-DD 文档标题"
git push
```

推送后 GitHub Pages 会在几十秒内自动重新部署，用户刷新网页即可看到最新内容。

---

## 🚀 首次部署流程（已完成，备查）

### 1. 创建仓库
```bash
curl -X POST -H "Authorization: token <TOKEN>" \
  https://api.github.com/user/repos \
  -d '{"name":"study-workbench","public":true}'
```

### 2. 初始化并推送
```bash
cd /workspace/每日学习笔记
git init
git config user.name "viivLgr"
git config user.email "viivLgr@users.noreply.github.com"
git remote add origin https://viivLgr:<TOKEN>@github.com/viivLgr/study-workbench.git
git add -A && git commit -m "初始化学习工作台"
git branch -M main
git push -u origin main
```

### 3. 开启 GitHub Pages
```bash
curl -X POST -H "Authorization: token <TOKEN>" \
  https://api.github.com/repos/viivLgr/study-workbench/pages \
  -d '{"source":{"branch":"main","path":"/"}}'
```

部署地址：`https://viivlgr.github.io/study-workbench/`

---

## 🔑 凭据管理

- **GitHub 账号**：`viivLgr`
- **Token**：用户提供的 Personal Access Token（权限：`repo`）
- **Token 失效处理**：推送失败时提示用户重新生成 token，更新 remote URL：
  ```bash
  git remote set-url origin https://viivLgr:<NEW_TOKEN>@github.com/viivLgr/study-workbench.git
  ```

> ⚠️ Token 等同于账号密码，仅在本地 git remote 中使用，不会写入任何文件。

---

## 🛠 故障排查

| 问题 | 解决方案 |
|------|---------|
| 工作台网页打不开 | 检查 https://viivlgr.github.io/study-workbench 是否能访问；确认仓库 Settings → Pages 已开启 |
| 推送失败（403） | Token 失效，需要用户重新生成并提供新 token |
| 推送后网页未更新 | GitHub Pages 有缓存，等 1-2 分钟或强制刷新（Ctrl+Shift+R） |
| 今日文档没置顶 | 检查 `date` 字段是否是当天日期，格式 `YYYY-MM-DD` |
| 工作台数据显示异常 | 检查 `DOCUMENTS` 数组的 JSON 语法（逗号、引号是否完整） |

---

## 📱 用户使用指南

### 访问工作台
- **Mac / 电脑**：浏览器打开 https://viivlgr.github.io/study-workbench
- **iPhone**：Safari 打开 → 分享 → 添加到主屏幕
- **安卓**：Chrome 打开 → 菜单 → 添加到主屏幕

### 提交新文档
在对话界面（非工作台网页）中，通过以下方式发给 AI：
1. 直接粘贴文本
2. 上传文件（PDF / Word / TXT 等）
3. 发送网页链接

---

_最后更新：2026-07-28_
