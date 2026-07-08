# 产品规划 · MeetFlow 会议全流程管理

> 一个 Web 端产品，覆盖「会前材料 → 会议纪要 → 会后 AI 报告」的会议全流程。
> 视觉风格遵循 `DESIGN-cal.md`（Cal.com 设计系统）。
> 制定日期：2026-07-08

---

## 1. 产品定位

**一句话**：把散落在文档、聊天、会议里的信息，收敛成「一个会议 = 材料 + 纪要 + 报告」的结构化闭环，并用 AI 自动产出像样的会后报告。

**目标用户**：团队内部（需登录 / 权限）。典型如你给的例子——工程团队的日报 / 周会 / 评审会。

**核心价值**：
- 会前：材料集中、议程清晰，参会人提前对齐
- 会中：结构化纪要（议题 / 决议 / 风险 / Action）
- 会后：一键 AI 生成可视化报告（复用你现有 HTML 报告的排版风格）

---

## 2. 核心功能模块

### 2.1 会议管理（Meetings）
- 会议列表（按项目 / 时间 / 状态筛选）
- 会议基本信息：标题、时间、项目归属、参会人、状态（计划中 / 进行中 / 已结束 / 已归档）
- 会议详情页 = 三个标签页：**会前材料** · **会议纪要** · **会后报告**

### 2.2 会前材料（Pre-meeting）
- 议程（Agenda）：条目化，可排序、指派负责人
- 材料附件：上传文档 / 链接（MVP 阶段存本地引用）
- 参会人清单 + 出席状态

### 2.3 会议纪要（Minutes）
- 结构化编辑：议题（Topic）、决议（Decision）、风险（Risk 分级）、行动项（Action：负责人 + 截止日）
- 行动项汇总视图（跨议题）
- 状态标签：🔄 进行中 / ✅ 已解决 / 🔴 高风险 / 🟡 中风险 / ⏳ 待更新

### 2.4 会后报告（AI Report）★核心亮点
- 输入：该会议的纪要结构化数据
- AI 生成：概要、KPI 提炼、议题卡片、Action 优先级表
- 输出：Cal.com 风格的可视化报告页（对标你现有的 `Daily Report.html` / `PTE Report.html`）
- 支持：人工二次编辑、导出 HTML / 打印 PDF、分享链接

### 2.5 协作与权限（第二期）
- 登录 / 组织 / 成员角色（Owner / Editor / Viewer）
- 多人协同编辑纪要、评论

---

## 3. 分期路线图

> 你选择了「团队协作 + 本地存储」，二者分属不同阶段。建议如下分期，先跑通单机闭环，再上协作。

### Phase 0 · 原型（当前）
- 交互原型页面（单文件，Cal.com 风格），验证视觉 + 核心流程
- 数据用 mock + `localStorage`

### Phase 1 · MVP（纯前端 SPA + 本地存储）
- React + Vite + TypeScript
- 路由：会议列表 / 会议详情（三标签）/ 报告预览
- 数据层：`localStorage` 或 `IndexedDB`（`Dexie`）
- AI 报告：接入 LLM API（前端直连，key 由用户配置），根据纪要生成报告结构
- 导出：HTML 下载 + 浏览器打印 PDF

### Phase 2 · 协作（引入轻后端）
- 登录 / 组织 / 角色权限
- 后端：Supabase 或 轻量 Node 服务（数据库 + 鉴权 + 文件存储）
- 实时协同（可选：Yjs / Liveblocks）
- AI key 由服务端代理（避免前端暴露）

### Phase 3 · 增强
- 模板市场（不同会议类型的报告模板）
- 语音转纪要、会议录音摘要
- 数据看板（跨会议的 Action 完成率、风险趋势）

---

## 4. 技术方案（Phase 1 MVP）

| 层 | 选型 | 说明 |
|---|---|---|
| 框架 | React 18 + Vite + TypeScript | 你倾向纯前端 SPA |
| 路由 | React Router | 客户端路由 |
| 状态 | Zustand（轻量） | 全局会议数据 |
| 存储 | IndexedDB（Dexie）/ localStorage | 本地持久化，无需后端 |
| 样式 | CSS 变量 + 设计 token | 直接映射 `DESIGN-cal.md` |
| 字体 | Inter + Cal Sans（回退 Inter 600 / -0.04em） | 见设计规范 |
| AI | 可插拔 LLM Provider（OpenAI 兼容接口） | key 前端配置，Phase 2 移到服务端 |
| 导出 | 内置 HTML 模板 + `window.print()` | 报告导出 |

**设计 token 落地**（来自 `DESIGN-cal.md`）：
- 主色 `#111111`（黑色主 CTA）· 画布 `#ffffff` · 卡片 `#f5f5f5` · 深色 footer `#101010`
- 圆角：按钮/输入 8px · 卡片 12px · hero 16px · 头像/pill 全圆
- 间距节奏：区块 96px · 卡片内边距 32px
- 严格双字体：Cal Sans 只用于标题，Inter 用于正文/UI

---

## 5. 信息架构（页面地图）

```
/                       登录后落地 → 会议列表（Dashboard）
/meetings               会议列表（筛选 / 搜索 / 新建）
/meetings/:id           会议详情
  ├─ /pre               会前材料（议程 + 附件 + 参会人）
  ├─ /minutes           会议纪要（议题 / 决议 / 风险 / Action）
  └─ /report            会后报告（AI 生成 + 编辑 + 导出）
/settings               个人设置（AI key、偏好）
```

---

## 6. 核心数据模型（草案）

```ts
Meeting {
  id, title, project, datetime, status, attendees[], createdBy
}
AgendaItem { id, meetingId, title, owner, order }
Attachment { id, meetingId, name, type, url }
Topic {
  id, meetingId, title, owners[], status,   // 进行中/已解决/高风险...
  progress, risk?: { level, note }, actions[]
}
Action { id, topicId, desc, owner, dueDate, priority }
Report {
  id, meetingId, generatedAt, summary, kpis[], html, edited
}
```

---

## 7. 关键设计原则（对齐 Cal.com 规范）

- 动作层保持**单色**：主按钮近黑 `#111111`，不用蓝色/彩色做 CTA
- 卡片语义化：浅灰卡 = 抽象信息，白卡带边框 = 展示真实内容/数据
- 每页以**深色 footer 收尾**
- 风险 emoji 与状态标签沿用你现有报告的语言（🔴🟡✅🔄⏳）
- 报告页排版直接复用你两个 HTML 例子的信息密度（KPI 行 + 卡片网格 + Action 表）

---

## 8. 待确认 / 决策点

- [ ] AI Provider 选型（OpenAI / Azure / 国内模型？）
- [ ] Phase 2 后端选型（Supabase vs 自建 Node）
- [ ] 报告是否需要多套模板（Daily / Weekly / 多项目综合）
- [ ] 附件存储上限与格式白名单
- [ ] 是否需要移动端适配优先级（当前只 Web）

---

## 9. 下一步

1. ✅ 本规划文档
2. ▶ 交互原型页面（Cal.com 风格，含会议列表 / 详情三标签 / 报告生成）
3. ⏭ 评审原型 → 确定 Phase 1 技术细节 → 搭建 Vite 项目骨架
