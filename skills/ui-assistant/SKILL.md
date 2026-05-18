---
name: ui-assistant
description: >
  将一句话产品想法转化为可直接运行的高保真前端 Demo，使用 HTML + DaisyUI + Alpine.js + Chart.js + Iconify 技术栈（全 CDN，零构建）。
  当用户说以下内容时必须使用此技能：
  "帮我做个demo"、"生成XX的UI"、"做个前端原型"、"一句话生成demo"、"做一个XX的界面"、
  、
---

# 前端 Demo 助手

将一句话产品想法转化为**完整可运行的多页面前端 Demo**。
技术栈：HTML + DaisyUI v5 + Alpine.js + Chart.js + Iconify（全部 CDN 引入，零构建）。

## 工作流程

### 第一步 — 解析输入

从用户消息中提取：

- **产品类型**：是什么？（仪表盘、管理系统、大屏监控、应用、工具等）
- **风格提示**：颜色、主题、设计参考（如"蓝色科技风"、"dark"、"极简"、"红色运营风"）
- **语言**：检测中文还是英文，输出语言与之匹配

### 第二步 — 风格确认

- **已指定颜色/风格** → 征得用户确认后再进行下一步
- **未指定风格** → 根据产品类型生成简短风格建议，征得用户确认后再进行下一步
  示例：
  > 根据您的需求，我建议使用「深蓝科技风 + 面板式布局」，主色调 `#0d9ff5`。是否确认这个风格，还是有其他偏好？

### 第三步 — 生成 UI 描述提示词

将一句话想法展开为结构化规格，使用以下模板：

```
创建完整的[风格/类型][产品类型]。
全局风格：[3～6 个关键词：布局、颜色、效果、字体]
需要以下页面：
【页面名】功能点1、功能点2、功能点3
【页面名】功能点1、功能点2
…
需要以下弹窗/交互：
- 弹窗1说明
- 弹窗2说明
```

根据产品类型推断 3 ～ 6 个页面 + 每页 2 ～ 4 个弹窗。

#### 用户确认

- **需求确认** → 生成 UI 描述完毕后，征得用户确认后再进行下一步
  示例：
  > 根据您的需求，我规划了以下页面 xxxxx,是否确认这个风格，还是有其他偏好？

### 第四步 — 构建 Demo

#### 文件结构（多文件）

```
./outputs/{项目名}/
├── index.html          ← 主入口
├── theme.css           ← 【必须】CSS 变量
├── components.css      ← 【必须】可复用 UI 类（按钮/卡片/标签/表格等）
├── common.js           ← 【必须】工具函数 + 模拟数据 + 通用交互
├── [page1].html
├── [page2].html
└── ...
```

#### 输出文件夹命名规则

- 文件夹名 = 产品名称的简短英文/拼音（如 `todo-app`）
- 输出路径：`./outputs/{项目名}/`，注意项目路径，非 skill 路径
- 若该文件夹**已存在**，则改为 `./outputs/{项目名}-v1/`，依次递增 v2、v3…

#### CDN 引入（固定，每个 HTML 文件均包含）

```html
<!-- Tailwind CSS v4 + DaisyUI v5 -->
<script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
<link href="https://cdn.jsdelivr.net/npm/daisyui@5" rel="stylesheet" />

<!-- Chart.js（按需引入，仅图表页面需要） -->
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.7/dist/chart.umd.min.js"></script>
<!-- Iconify -->
<script src="https://cdn.jsdelivr.net/npm/iconify-icon@2.3.0/dist/iconify-icon.min.js"></script>
<script defer src="./common.js"></script>
<!-- 先引入common.js 再引入 alpinejs -->
<script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
```

#### index.html 职责

先判断页面数量，再觉得是否要使用iframe模式 
如果指定生成一页的页面，就直接生成。
---
生成多页参考：
- 左侧边栏导航，使用 Iconify 图标
- 激活状态高亮显示
- 主内容区通过 `<iframe>` 加载子页面
- 如适用，提供亮色/暗色主题切换
- **不直接包含页面内容** — 始终通过 iframe 加载

#### 子页面规则

- 每个 `.html` 独立完整，可在浏览器中单独打开
- 使用模拟/虚拟数据 — **不调用真实 API**
- **视觉上完全可用**（不允许出现 `TODO`、`...`、仅有占位符的内容）
- 使用语义化 HTML，避免过度嵌套
- 使用 tailwindss grid+gap 进行响应式布局,竖向布局采用 flex

#### theme.css 职责

不需要重置样式，禁止在这里重置样式：`* { margin: 0; padding: 0;  }`

```css
@import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap');
:root {
  --color-base-content: var(--text-main);
  --color-primary: var(--primary);
  --color-success: var(--success);
  --color-warning: var(--warning);
  --color-error: var(--danger);
}
:root,
[data-theme='light'] {
  --primary: #2563eb;

}
<!-- 如果有切换需求，需要配置一套dark 颜色 -->
:root,
[data-theme='dark'] {
  --primary: #2563eb;

  
}
```

#### 可定制的属性一览

**🎨 颜色变量（Color Tokens）**

- `--color-base-100`
- `--color-base-200`
- `--color-base-300`
- `--color-base-content`
- `--color-primary` / `--color-primary-content`
- `--color-secondary` / `--color-secondary-content`
- `--color-accent` / `--color-accent-content`
- `--color-neutral` / `--color-neutral-content`
- `--color-info` / `--color-info-content`
- `--color-success` / `--color-success-content`
- `--color-warning` / `--color-warning-content`
- `--color-error` / `--color-error-content`

**📐 圆角与尺寸**

- `--radius-selector`
- `--radius-field`
- `--radius-box`
- `--size-selector`
- `--size-field`

#### components.css 职责

必须包含以下可复用类（按需扩展，**不写死颜色，全部使用 var()**）：

```css
/* 按钮 — 基于 daisyUI btn 扩展 */
.btn-glow {
  box-shadow: 0 0 20px color-mix(in srgb, var(--primary) 30%, transparent);
}
/* 卡片 */
.card-base {
  background: var(--bg-card);
  border: 1px solid var(--border);
  border-radius: var(--radius);
  box-shadow: var(--shadow-sm);
  transition: box-shadow 0.2s, transform 0.2s;
}
.card-base:hover {
  box-shadow: var(--shadow-md);
  transform: translateY(-1px);
}

/* 表格 — 基于 daisyUI table 扩展 */
.table-custom thead th {
  background: color-mix(in srgb, var(--primary) 6%, transparent);
  color: var(--primary);
  font-size: 12px;
  font-weight: 600;
}
.table-custom tbody tr {
  transition: background 0.15s;
  cursor: pointer;
}
.table-custom tbody tr:hover {
  background: color-mix(in srgb, var(--primary) 5%, transparent);
}
/* 滚动条 */
.custom-scroll::-webkit-scrollbar {
  width: 4px;
}
.custom-scroll::-webkit-scrollbar-thumb {
  background: color-mix(in srgb, var(--primary) 30%, transparent);
  border-radius: 4px;
}
/* 弹窗增强 */
.modal-enhanced .modal-box {
  border: 1px solid var(--border);
  border-radius: 12px;
  max-width: 640px;
}
.modal-enhanced .modal-backdrop {
  backdrop-filter: blur(4px);
}
```

#### common.js 职责

必须包含：

```javascript
// —— 工具函数 ——
function formatDate(date) {
  /* 返回 YYYY-MM-DD HH:mm */
}
function formatNumber(n) {
  /* 千分位格式化 */
}
function randomItem(arr) {
  /* 随机取值 */
}
function getStatusClass(status) {
  /* 根据状态返回 daisyUI badge 类名 */
}
// theme单独列出来
const theme = {
  theme: 'dark',
  init() {
    this.theme = localStorage.getItem('theme') || 'dark'
    document.documentElement.setAttribute('data-theme', this.theme)
    window.addEventListener('storage', e => {
      if (e.key === 'theme') {
        this.theme = e.newValue
        document.documentElement.setAttribute('data-theme', this.theme)
      }
    })
  },
  toggle() {
    this.theme = this.theme === 'dark' ? 'light' : 'dark'
    document.documentElement.setAttribute('data-theme', this.theme)
    localStorage.setItem('theme', this.theme)
  },
}
// —— 通用交互组件（Alpine.js 全局注册） ——
document.addEventListener('alpine:init', () => {
  Alpine.data('clock', () => ({
    time: '',
    init() {
      this.tick()
      setInterval(() => this.tick(), 1000)
    },
    tick() {
      const now = new Date()
      this.time = now.toLocaleString('zh-CN', { hour12: false })
    },
  }))
  Alpine.data('themeToggle', () => theme)

  Alpine.data('tabs', defaultTab => ({
    active: defaultTab,
    switch(tab) { this.active = tab },
  }))

  Alpine.data('dropdown', () => ({
    open: false,
    toggle() { this.open = !this.open },
    close() { this.open = false },
  }))
})
// 初始化全局主题
window.onload = () => {
  theme.init()
}
```

#### 页面 HTML 模板

```html
<!DOCTYPE html>
<html lang="zh-CN" data-theme="light">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>页面标题</title>
    <!-- 通用cdn ... -->
  </head>
  <body x-data="pageData()" x-init="init()">
    <!-- 页面内容 -->
    <script>
      function pageData() {
        return {
          // 状态、数据、方法
          init() {},
        }
      }
    </script>
  </body>
</html>
```

#### DaisyUI 组件使用规范

**原则：优先使用 daisyUI 语义类，不手写 UI 组件。**

```html
<!-- 按钮 -->
<button class="btn btn-primary">确认</button>
<button class="btn btn-ghost">取消</button>
<button class="btn btn-error btn-sm">删除</button>
<!-- 弹窗 -->
<button class="btn" onclick="my_modal_1.showModal()">open modal</button>
<!-- 这里id要唯一 通过id.showModal 打开 -->
<dialog id="my_modal_1" class="modal">
  <div class="modal-box">
    <form method="dialog">
      <button class="btn btn-sm btn-circle btn-ghost absolute right-2 top-2">✕</button>
    </form>
    <h3 class="text-lg font-bold">Hello!</h3>
    <p class="py-4">Press ESC key or click on ✕ button to close</p>
    <div class="modal-action">
      <form method="dialog">
        <!-- if there is a button in form, it will close the modal -->
        <button class="btn">Close</button>
      </form>
    </div>
  </div>
</dialog>
<!-- 表格 -->
<table class="table table-custom">
  <thead>
    <tr> <th>列</th> </tr>
  </thead>
  <tbody>
    <tr> <td>数据</td> </tr>
  </tbody>
</table>
<!-- 徽标 -->
<span class="badge badge-success">成功</span>
<!-- 表单 -->
<select class="select select-sm w-full">
  ...
</select>
<input type="text" class="input input-bordered input-sm w-full" />
<textarea class="textarea textarea-bordered w-full"></textarea>
<input type="checkbox" class="toggle toggle-sm" />
<input type="checkbox" class="checkbox checkbox-sm" />
<!-- 带图标和 快捷键 -->
<label class="input">
  <iconify-icon icon="lucide:search"></iconify-icon> <input type="search" class="grow" placeholder="Search" />
  <kbd class="kbd kbd-sm">⌘</kbd> <kbd class="kbd kbd-sm">S</kbd>
</label>
<!-- 标签页 -->
<div x-data="tabs('a')" role="tablist" class="tabs tabs-bordered">
  <a role="tab" class="tab" :class="{ 'tab-active': active === 'a' }" @click="active = 'a'">Tab A</a>
  <a role="tab" class="tab" :class="{ 'tab-active': active === 'b' }" @click="active = 'b'">Tab B</a>
</div>
/* 进度条 */
<progress class="progress progress-primary w-full" value="70" max="100"></progress>
<!-- 切换主题 参考 -->
<div x-data="themeToggle">
  <button @click="toggle()" class="btn"></button>
  <iconify-icon :icon="theme === 'dark' ? 'lucide:moon' : 'lucide:sun'"></iconify-icon>
</div>
```

#### Chart.js 图表规范

仅图表页面引入 Chart.js CDN。初始化时机：

```javascript
init() {
  this.$nextTick(() => setTimeout(() => this.initCharts(), 100));
},
initCharts() {
  const ctx = document.getElementById('myChart');
  if (!ctx) return;
  new Chart(ctx, { /* ... */ });
}
```

图表配色从 CSS 变量读取：

```javascript
const style = getComputedStyle(document.documentElement)
const primary = style.getPropertyValue('--primary').trim()
const success = style.getPropertyValue('--success').trim()
const warning = style.getPropertyValue('--warning').trim()
const danger = style.getPropertyValue('--danger').trim()
```

Tooltip 统一风格（深色背景 + 圆角）：

```javascript
tooltip: {
  backgroundColor: 'rgba(15, 23, 42, 0.92)',
  borderColor: 'rgba(255,255,255,0.1)',
  borderWidth: 1,
  titleColor: '#f1f5f9',
  bodyColor: '#94a3b8',
  cornerRadius: 8,
  padding: 10,
}
```

#### Iconify 使用规范

```html
<iconify-icon icon="lucide:home" style="font-size: 20px;"></iconify-icon>
```

推荐图标集：`lucide:*`（统一线性风格）。复杂场景可补充 `mdi:*` 或 `carbon:*`。

## 默认风格预设

**默认（未指定时）：蓝色浅色系**
| 属性 | 值 |
| ---------------- | -------------------------------- |
| 主色 `--primary` | `#2563EB` |
| 页面背景 | `#f8fafc` |
| 卡片/面板 | `#ffffff` |
| 侧边栏 | `#ffffff`（白色 + 右侧边框） |
| 风格关键词 | 浅色、卡片式、圆角、干净、科技感 |
**其他颜色预设：**
| 关键词 | `--primary` | 适用场景 |
| ------------ | ----------- | --------------------------------------- |
| 蓝色（默认） | `#2563EB` | 通用管理系统、SaaS |
| 绿色 | `#10b981` | 运营看板、增长工具 |
| 橙色 | `#f97316` | 电商、营销、温暖感 |
| 紫色 | `#7c3aed` | AI 应用、数据平台、创意工具 |
| 红色 | `#ef4444` | 安全监控、告警中心 |
| 金色 | `#f59e0b` | 金融、企业管理 |
| 极简 | `#334155` | 大留白、文档工具 |
| 暗色 | `#6366f1` | **仅用户明确要求"暗色/dark"时使用**，同时切换深色背景变量 |

**暗色切换规则**（用户要求暗色时）：

`<html>` 标签添加 `data-theme="dark"`。

## 布局模板

### 管理系统（默认）

```
┌────────┬─────────────────────────────────────────┐
│        │ 面包屑 + 搜索 + 用户头像                   │
│ 侧边栏  ├─────────────────────────────────────────┤
│ 导航    │ 统计卡片行                                │
│        ├─────────────────────────────────────────┤
│        │                                         │
│        │         主内容区                          │
│        │   （表格 / 表单 / 详情）                   │
│        │                                         │
└────────┴─────────────────────────────────────────┘
```

### 仪表盘 / 大屏

```
┌──────────────────────────────────────────────────┐
│ 标题栏：Logo + 标题 + 公告 + 时钟 + 操作按钮       │
├──────────┬──────────┬──────────┬──────────────────┤
│ 统计卡片1 │ 统计卡片2 │ 统计卡片3 │    信息面板      │
├──────────┴──────────┴──────────┤                  │
│                                │                  │
│         主内容区                │    侧面板        │
│   （图表 / 表格 / 主要模块）     │ （告警/资源/列表）│
│                                │                  │
├────────────────────────────────┴──────────────────┤
│ 底部区域：辅助图表 / 仪表 / 快捷操作               │
└──────────────────────────────────────────────────┘
```

---

## 常见模块模式

| 产品类型     | 推荐页面/模块                                               |
| ------------ | ----------------------------------------------------------- |
| 管理后台     | 仪表盘概览、数据列表（表格+筛选+操作）、新增/编辑弹窗、设置 |
| 监控大屏     | 实时概览、数据表格、趋势图表、告警面板、资源监控            |
| 数据看板     | KPI 卡片、多维图表、数据表格、筛选器、导出弹窗              |
| 聊天/AI 应用 | 对话页、历史列表、模型选择、设置                            |
| 电商         | 商品列表、商品详情、购物车、订单管理                        |
| 内容工具     | 编辑器、素材库、预览、发布设置                              |

---

## 数据规范

- 所有数据写死在 Alpine.js `return { ... }` 中，**不调用外部 API**
- 列表数据不少于 **10 条**，字段完整真实（禁止"项目 1、项目 2"）
- 告警/通知数据不少于 **5 条**，分级别展示
- 报表/统计数据不少于 **7 条**（如近 7 天）
- 使用 `setInterval` 模拟实时数据更新（可选，适用于大屏/监控类）

---

## 弹窗规范

每个页面至少包含 **1 个功能弹窗**，使用 daisyUI `modal` 组件：
| 弹窗类型 | 典型内容 |
| ------------ | ---------------------------------------------- |
| 详情弹窗 | 数据行点击展开完整信息 |
| 表单弹窗 | 新增/编辑，含 input / select / textarea |
| 确认弹窗 | 删除、提交等操作二次确认 |
| 设置弹窗 | toggle 开关 + select 下拉 |
| 报表弹窗 | 统计摘要 + 数据表格 + 导出按钮 |
| 通知弹窗 | 分级别列表 + 全部已读操作 |

---

## 输出前质量检查清单

- [ ] `index.html` （如有iframe,检查 侧边栏导航 + iframe 切换正常工作）
- [ ] `theme.css`、`components.css`、`common.js` 均已创建
- [ ] 所有子页面引用了共享样式/脚本文件
- [ ] 所有子页面无报错可正常渲染
- [ ] CSS 变量与所请求的风格一致
- [ ] DaisyUI 语义类优先（btn、modal、badge、table、select、toggle 等）
- [ ] Alpine.js 函数式组件组织状态和方法
- [ ] 图表在 `$nextTick` 后初始化，配色从 CSS 变量读取
- [ ] 模拟数据真实可信，数量达标
- [ ] 至少每个页面 1 个功能弹窗，交互完整
- [ ] 图标使用 Iconify（推荐 `lucide:*`）
- [ ] 响应式布局（flex+grid + gap，适配不同分辨率）
- [ ] 无 TODO / 占位符 / `...`
