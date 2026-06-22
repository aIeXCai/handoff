<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://img.shields.io/badge/Claude%20Code-Skill-5C4EE5?logo=anthropic&logoColor=white&labelColor=1a1a2e">
    <img src="https://img.shields.io/badge/Claude%20Code-Skill-5C4EE5?logo=anthropic&logoColor=white&labelColor=fafafa">
  </picture>
  <img src="https://img.shields.io/badge/license-MIT-green" alt="License: MIT">
  <img src="https://img.shields.io/badge/version-0.1.0-blue" alt="Version 0.1.0">
</p>

<h1 align="center">handoff</h1>

<p align="center">
  <strong>从"这个项目到底怎么回事"到"开干"<br/>——2 分钟内完成上下文交接。</strong>
</p>

<p align="center">
  <a href="README.md">English Docs</a>
  &nbsp;·&nbsp;
  <a href="#安装">安装</a>
  &nbsp;·&nbsp;
  <a href="#快速开始">快速开始</a>
  &nbsp;·&nbsp;
  <a href="#工作流">工作流</a>
</p>

---

## 这是什么？

`handoff` 是一个 **Claude Code Skill**，为任意项目自动生成一份结构化的 `HANDOFF.md` 交接文档。每次新 session 打开项目时，Claude 读取这份文档即可瞬间对齐上下文——不用重新解释，不浪费 token。

> 痛点：每次开新的 Claude Code session，上下文都是空的。你要花时间和 token 从头解释项目。
>
> 解决：一份 `HANDOFF.md` 承载所有核心事实——机器保证结构，人工确认内容。

---

## 为什么需要它？

<table>
<tr><th>痛点</th><th>handoff 怎么解决</th></tr>
<tr><td>每次开新 session 都要重新解释项目背景</td><td>7 节硬结构，<strong>2 分钟读完</strong></td></tr>
<tr><td>口头解释太占上下文窗口</td><td>一份文件承载核心事实，<strong>零 token 浪费</strong></td></tr>
<tr><td>团队信息散落在不同人的脑子里</td><td>客观事实写到文件里，谁打开<strong>都能立即对齐</strong></td></tr>
<tr><td>项目演进后旧信息过时</td><td>一键重跑，自动扫描最新状态，<strong>保留用户判断</strong></td></tr>
</table>

---

## 核心设计

### 3 个原则

| 原则 | 含义 |
|---|---|
| **短** | 7 个固定章节，每节 ≤ 5 行，整份文档 2 分钟读完 |
| **准** | 每条事实可追溯到 commit / issue / `file:line`，不写猜测 |
| **可重跑** | 多次执行结果稳定。扫描代码 + 询问用户，不靠 AI 编造 |

### 7 个固定章节

每份 `HANDOFF.md` 都有相同的结构：

| # | 章节 | 内容 |
|:-:|---|---|
| 1 | **定位** | 项目是什么、给谁用、核心价值 |
| 2 | **技术栈** | 每层选型及理由（表格） |
| 3 | **结构速览** | 目录树，深度 ≤ 2 |
| 4 | **数据模型** | ER 级粒度——实体 + 关系 |
| 5 | **关键决策** | 每条带 commit hash / issue 链接 / 代码坐标 |
| 6 | **启动 & 验证** | 启动命令 + 人工确认的跑通标志 |
| 7 | **陷阱清单** | 「不要做 X，因为 Y」格式 |

---

## 工作流

```mermaid
flowchart LR
    A[🔍 扫描<br/>项目] --> B[💬 询问<br/>第1轮：事实]
    B --> C[💬 询问<br/>第2轮：审视]
    C --> D[💬 询问<br/>第3轮：备注]
    D --> E[📝 生成<br/>HANDOFF.md]
    E --> F[👀 Review<br/>Diff]
    F --> G[✅ 写入<br/>+ Commit]

    style A fill:#5C4EE5,color:#fff
    style B fill:#7C3AED,color:#fff
    style C fill:#8B5CF6,color:#fff
    style D fill:#A78BFA,color:#fff
    style E fill:#5C4EE5,color:#fff
    style F fill:#7C3AED,color:#fff
    style G fill:#5C4EE5,color:#fff
```

### 3 轮询问是核心

这正是 handoff 和单纯「dump 一段 prompt」的本质区别：

| 轮次 | 做什么 | 为什么重要 |
|:---:|---|---|
| **1** | 确认 AI 扫描到的事实，补充定位、陷阱和跑通标志 | 机器能扫代码，但不知道*你为什么这么写* |
| **2** | 审视旧文档中的决策和陷阱，批量保留/修改/删除 | 项目演进后部分条目可能过时 |
| **3** | 确认用户备注区内容 | 用户手动记录的内容**永不自动覆盖** |

---

## 安装

```bash
npx skills add aIeXCai/handoff
```

---

## 快速开始

```bash
cd your-project
/handoff
```

就这么简单。Skill 会自动扫描你的仓库，问你 3 轮问题，然后在项目根目录写入 `HANDOFF.md`。

或者在对话中说：*「写个 handoff」*/*「交接文档」*/*「更新 HANDOFF」*

### 什么时候用

- **长 session 快到上下文上限** — 在上下文被压缩前「打包带走」项目状态
- **新 session 开始** — 读取已有 `HANDOFF.md`，瞬间对齐
- **项目结构大幅变动** — 重扫，反映最新现实
- **新人加入项目** — 2 分钟看懂项目全貌

---

## 示例输出

以下是一份真实生成的 `HANDOFF.md`（微信小程序项目）：

```markdown
## 1. 定位
Grit 小程序 — 健身计划打卡工具，面向 CrossFit/Hyrox 爱好者。
核心价值：用结构化计划 + 主观感受记录替代零散的健身日志。

## 2. 技术栈
| 层     | 选型                       | 理由                           |
|--------|----------------------------|--------------------------------|
| 前端   | 微信原生 + WXML             | 小程序生态首选                  |
| 后端   | 微信云开发                  | 免运维，CloudBase 集成           |
| 数据   | CloudBase 文档数据库        | 与云函数天然集成                |

## 3. 结构速览
src/
├── pages/          # 页面
├── components/     # 公共组件
├── utils/          # 工具函数
└── cloudfunctions/ # 云函数

## 4. 数据模型
- User → Checkin: 1 对多（用户有多次打卡记录）
- Plan → Exercise: 1 对多（计划包含多个动作）

## 5. 关键决策
- 嵌入单文档而非子集合存储 checkin → commit a3f2b1c
- 懒创建 checkin 记录 → src/utils/checkin.js:42
- 主观感受与训练数据分离存储 → issue #47

## 6. 启动 & 验证
- `npm run dev` → 微信开发者工具中看到首页计划列表即为跑通

## 7. 陷阱清单
- 不要手动改 checkin 的 date 字段 → CloudBase 触发器依赖该字段索引
- 不要在 onShow 里做重查询 → 小程序页面切换频繁触发
- 不要用 `wx.navigateTo` 深层跳转 → 超过 5 层用 `wx.redirectTo`

## 用户备注
<!-- handoff:manual-zone -->
auth 流程依赖微信 `wx.login()` 返回的临时 code。
在开发者工具中调试时可打开「跳过鉴权」开关。
<!-- /handoff:manual-zone -->
```

---

## 与其他工具的区别

| | handoff | README.md | CLAUDE.md | 口述 |
|---|---|---|---|---|
| **谁维护** | AI + 人工确认 | 人工 | 人工 | 人工 |
| **更新频率** | 一键重跑 | 偶尔更新 | 手动 | 每次都要说 |
| **内容类型** | 事实、决策、陷阱 | 项目介绍 | AI 行为规则 | 随意 |
| **可追溯性** | 每条可指到代码坐标 | 无要求 | 无要求 | 无 |
| **最佳场景** | AI 辅助开发的交接 | 给人看的项目介绍 | AI 行为配置 | 一次性解释 |

---

## 许可

[MIT](LICENSE)

---

<p align="center">
  <sub>为 <a href="https://claude.ai/code">Claude Code</a> 打造 —— <a href="https://anthropic.com">Anthropic</a> 出品的 AI 编程 CLI。</sub>
</p>
