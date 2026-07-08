# 项目上下文 · PTE 测试工程会议报告

> 本文件用于记录项目关键上下文，便于后续协作与上下文管理。
> 最后更新：2026-07-08

## 1. 项目概述

这是一组 **CXMT（长鑫存储）PTE 半导体测试工程会议进展报告**的可视化页面。
内容为 DRAM 芯片测试（CP / FT / RDBI / SLT）的良率追踪与失效分析，用于日常会议汇报。

- **形态**：纯静态前端，无构建系统、无第三方依赖，HTML 内联 `<style>` 与 `<script>`
- **语言**：中文界面（`lang="zh-CN"`），含大量 emoji 作视觉标识
- **展示方式**：浏览器直接打开 HTML 即可

## 2. 文件清单

| 文件 | 类型 | 说明 |
|---|---|---|
| `Daily Report.html` | 单项目日报 | DRGQA 验证 Daily Meeting（7/6），13 个议题；CSS `column-count` 瀑布流布局 |
| `PTE Report.html` | 多项目综合报告 | 覆盖 5 个项目，`nav-tabs` + JS 标签页切换 |
| `DESIGN-cal.md` | 设计规范 | Cal.com 风格设计系统 token（颜色 / 字体 / 间距 / 组件），**当前未应用于两个报告** |
| `PROJECT-CONTEXT.md` | 本文件 | 项目上下文记录 |

## 3. 业务背景（术语速查）

报告内容为半导体后段测试工程，常见术语：

- **测试站点**：CP（晶圆测试）、FT（成品测试，FT12/FT34）、RDBI（Redundancy Burn-In）、SLT（System Level Test）、BI（Burn-In）
- **指标**：Yield（良率）、PPM、True Fail、Bin（失效分类 Bin5/6/7）
- **分析手段**：EFA / PFA / FIB / FSA / DOE、DE（设计）、DC、PE / TE（工艺 / 测试工程）
- **风险分级**：🔴 高风险 / 🟡 中风险 / 🔄 进行中 / ✅ 已解决 / ⏳ 待更新
- **Action 记法**：`@负责人 + 日期（如 7/9）`

## 4. 覆盖项目（PTE Report）

| 项目 | 图标 | 状态要点 |
|---|---|---|
| DRGQA | 🔬 | 3 项高风险：RDBI TRCD Degrade（Vdibias 主因）、SB/DB Fail（Array 问题）、TPsensor LT 读数异常 |
| DEJOA | 📊 | CP Yield 95.14%↑，FT2 Bin5 PCOSIM ROW Fail 高风险 |
| DDJOC | 🆕 | 新项目建线，563B 新平台评估，各站点 Checklist 准备中 |
| DDJOB | 🏭 | FT34 315B QDP 97.39%（killer RZQ），Corner lot 已 rls |
| DDCPB | 🧪 | SLT fail debug（multi row / burst0），2 项高风险 |

## 5. 页面结构与样式约定

### Daily Report.html
- `.header` 头部 + `.kpi-row` 指标卡片 + `.grid-masonry`（3 列瀑布流）
- 议题卡 `.topic`：`.topic-hd`（标题 + 状态标签 `.st`）+ `.topic-bd`（`.prog` / `.risk-callout` / `.act-box`）
- 状态标签类：`.st-go`（进行中）`.st-done`（已解决）`.st-risk`（风险）`.st-wait`（待处理）
- 响应式断点：`1100px` → 2 列，`700px` → 1 列

### PTE Report.html
- `.nav-tabs`（sticky 顶部标签栏）→ JS 切换 `.project-section.active`
- 主色 `#165dff`（蓝），卡片网格 `.grid-2col` / `.grid-3col`
- 卡片风险类：`.card.high-risk` / `.ongoing` / `.resolved` / `.pending` / `.medium-risk`
- 深色渐变 `.header`，跨项目 Action 汇总表 `.action-table`

## 6. 注意事项 / 已知情况

- `DESIGN-cal.md`（Cal.com 白底黑 CTA 风格）与两份报告的实际风格（蓝灰 SaaS Dashboard）**不一致**，为独立的可复用设计参考，非当前样式来源。
- 两个报告各自内联样式，**样式未共享**，修改需分别处理。
- 文件夹路径含尾部空格：`/Users/katherineshen/Desktop/Meeting `。
- 报告数据为特定日期快照（Daily 7/6、PTE 7/7），更新即替换正文内容。

## 7. 待办 / 可能的后续任务

- [ ] （可选）统一两份报告的设计风格 / 抽取共享样式
- [ ] （可选）将 Daily 与 PTE 合并为单一入口页面
- [ ] （可选）按 `DESIGN-cal.md` 规范重构视觉
