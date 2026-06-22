---
name: handoff
description: 为当前项目生成/刷新 HANDOFF.md — 项目交接文档,在新 session 快速恢复上下文。仅记录客观事实,可重复运行。
version: 0.1.0
triggers:
  - handoff
  - 交接文档
  - 项目概览
  - 更新 HANDOFF
  - 写个 handoff
scope: 通用(适配所有项目)
---

# handoff

为当前项目生成或刷新根目录的 `HANDOFF.md`。**仅陈述客观事实**——不写未来规划、不写 PRD、不写设计动机猜测。

## When to use

触发场景(任一):
- 用户调用 `/handoff` 或说"更新 HANDOFF"、"交接文档"、"项目概览"、"写个 handoff"
- 长 session 接近上下文上限,需要"打包带走"项目状态
- 新 session 开始,想快速对齐项目现状

## 核心原则

- **短** — 7 节硬结构;短节(定位/启动)≤ 5 行,列表节(决策/陷阱)每项 1 行,表节(技术栈/数据模型)行数不限但每格 1 句内;整份 ≤ 2 分钟读完
- **准** — 每条事实可指到 commit / issue / `path:line`
- **可重跑** — 多次执行结果应保持稳定
- **保留判断** — 用户的"为什么这么写"是 HANDOFF 最有价值的部分,绝不丢

## Workflow

### Step 1 — 扫描(只读,自动)

并行执行:
- 读旧 `HANDOFF.md`(若存在),拆出 7 节 + "决策"段 + "陷阱"段 + "用户备注"区
- 扫描根结构(深度 ≤ 2,跳过 `.git` `node_modules` `dist` `build` `venv` `__pycache__` `.next` `target` 等)
- 识别技术栈:扫所有 manifest(`package.json` / `requirements.txt` / `pyproject.toml` / `go.mod` / `Cargo.toml` / `pom.xml` / `build.gradle` / `Podfile` / `*.csproj` / `cloudfunctions/*/index.js` 等)
- 提取 scripts 段(build / test / start / dev 命令)
- 扫数据模型:查 `schema/` `migrations/` `models/` `db.js` `database/` `prisma/` `entities/` 等关键字目录
- 读最近 20 条 `git log --oneline`,过滤关键词 `fix` `revert` `breaking` `⚠️` `切记` `不要` `坑` `deprecate` `migration`

### Step 2 — 询问(必须,3 轮)

#### 第一轮:事实确认

把 Step 1 扫描结果按 7 节组织展示:
> 扫描结果(请逐节确认):
> 1. 定位 — 我猜是 "X"  ← 对吗?
> 2. 技术栈 — "Y"  ← 对吗?
> ...
> 回复 `y` 全确认 / 逐条修正 / 补充新内容

**必须**收集到:
- 项目定位 / 目标用户
- 关键决策的"为什么"
- 陷阱清单
- **跑通标志** — "启动成功后看到什么算成功"(必问;小程序/Web/CLI/API 等项目类型差异大,机器不替用户决定)

#### 第二轮:决策审视(C 变体核心,批处理)

读旧文档第 5、7 节,一次性列出全部,让用户用**批处理格式**回复:

> 旧决策审视(共 N 条):
> 1. 嵌入单文档
> 2. 懒创建 checkin
> ...
> 
> 旧陷阱审视(共 M 条):
> 12. CloudBase 未绑 appid
> ...
> 
> **回复格式**(任选其一):
> - `1y 3d 8m` — 编号 + y(保留) / m(改写) / d(删除),缺省 = y
> - `全保留` — 一键过
> - `全删` — 一键清
> - `全保留 + 新增: <新内容>` — 批量保留并补充
> 
> `m` 项会接着追问怎么改

#### 第三轮:备注区确认

> 旧"用户备注"区有 X 段内容:
> - "..." — 保留 / 删?
> 要现在补充新备注吗?

### Step 3 — 生成

按 7 节硬结构输出 markdown,**严格每节 ≤ 5 行**:
1. **定位** — 1-2 句,项目是什么、给谁用、核心价值
2. **技术栈** — 表(层 / 选型 / 理由)
3. **结构速览** — 目录树,深度 ≤ 2
4. **数据模型** — ER 级粒度,实体 + 关系,不展字段
5. **关键决策** — 每条带 commit hash / issue 链接 / `path:line`
6. **启动 & 验证** — 启动命令 + **用户口述的跑通标志**(Step 2 第一轮已收集,不替用户决定)
7. **陷阱清单** — "不要做 X,因为 Y"格式

末尾追加:
```markdown
## 用户备注(skill 永不自动覆盖)
<!-- handoff:manual-zone -->
{原样保留的旧备注 + 用户新补充}
<!-- /handoff:manual-zone -->
```

### Step 4 — Review(不可跳过)

完整 markdown 输出到终端(不直接写文件):
> 即将写入 `./HANDOFF.md`:
> ```diff
> {完整 diff}
> ```
> `y` 覆盖 / `n` 取消 / `e` 进入编辑模式

### Step 5 — 写入 & 提示 commit

- 写入 `HANDOFF.md`
- 提示: `建议 commit: docs: refresh HANDOFF (YYYY-MM-DD),是否 commit?`

## Accuracy rules(硬性)

- 不写未验证的"决策" — 每条必须能指到 commit / issue / `path:line`
- 不写"我猜这里将来要..." — 属于 PRD/Roadmap
- 不展开字段/代码 — 给坐标,不复制
- 不写"详细描述" — 5 行内讲不清的,删
- "用户备注"区 **永不** 自动覆盖

## 边界

- 只生成/更新 `HANDOFF.md` 一个文件
- 不动其他文件
- 不执行副作用命令(`npm install` / `npm test` / 网络请求)
- 不主动 commit(写完询问)

## 错误处理

- 找不到 git 仓库 → 询问"项目根在哪?",或无 git 模式(只扫文件树)
- 找不到 manifest → 询问"项目类型/技术栈"
- `HANDOFF.md` 不存在 → 跳过第二轮"决策审视",按"全新生成"流程
- "用户备注"区标记缺失但有额外内容 → 警告"检测到非标准内容,会保留为备注"

## 输出确认格式

写入后简短确认:
```
✓ HANDOFF.md 已写入 ({行数} 行, {节数} 节)
✓ 决策审视:保留了 X 条,改了 Y 条,删了 Z 条
✓ 用户备注:保留了 N 段
→ 建议 commit: docs: refresh HANDOFF (YYYY-MM-DD)
```
