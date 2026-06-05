# 新东方高中英语语法课件项目

## 项目概述

为新东方面试试讲制作的高中英语语法教学网页课件，单 HTML 文件，双击即开，投屏授课用。每个语法专题对应一个 HTML 文件。

## 技术选型

- **纯 HTML/CSS/JS 单文件**，零依赖，离线可用
- **深色"黑板风"主题**：深蓝灰底 + 金色暖调点缀 + 红色标注失分点
- **幻灯片式翻页**：键盘 ↑↓ / 滚轮 / 点击左右半屏 / Home 键回导航
- **全响应式布局**：所有尺寸用 `clamp()` + `vh/vw`，不设 px 上限

## 配色变量

| 变量 | 色值 | 用途 |
|------|------|------|
| `--gold` | `#d4a355` | 主强调色、按钮、边框 |
| `--gold-light` | `#f0c97a` | 高亮文字、先行词标注、翻译中从句高亮 |
| `--red` / `--red-light` | `#e05550` / `#ff6b63` | 高频失分点、错误提示、只能用that卡片 |
| `--blue` / `--blue-light` | `#5a8ec9` / `#7aafe8` | 定语从句标注、口诀卡、that标识色 |
| `--green` | `#4a9e6b` | 正确反馈、基础层卡片 |
| `--bg` / `--surface` / `--surface-light` | `#1a1d28` / `#232738` / `#2c3042` | 背景三层 |
| `--border` | `#383c4e` | 所有边框 |
| `--text` / `--text-dim` | `#e8e6e1` / `#a09d95` | 主文字 / 次要文字 |

## 通用组件

所有课件统一使用以下 CSS 类：

- `.slide` — 幻灯片容器，`96vw × 94vh`，`padding` 用 `clamp()` 自适应
- `.slide-header` — 顶部标题栏（编号 + 标题 + 标签）
- `.example-box` — 例句展示框，左侧金色边框。英文句子内用 `.highlight`（金色）或 `.emphasis`（红色）标注关键词
- `.card` / `.card-grid` — 知识点卡片和网格布局。卡片用 `.red-border`（that规则）、`.gold-border`（which规则）、`.blue-border`（概念）、`.green-border`（目标）区分
- `.quiz-item` — 选择题，支持改选、双击重置、口诀反馈
- `.tip-box` — 金色提示框，放话术/思路引导
- `.mnemonic-card` — 蓝紫渐变口诀卡，放口诀文字
- `.compare-table` — 对比表格，`.that-col` 蓝色、`.which-col` 金色、`.trap` 红色标注易错
- `.trans-toggle` — "中"按钮，点击展开中文翻译
- `.show-more-btn` — "📋 更多例子"展开按钮
- `.extra-examples` — 隐藏的额外例句区，点 `.show-more-btn` 展开
- `.trans-text` — 中文翻译文字，默认隐藏，用 `<strong>` 标注从句对应部分（CSS 已设为金色）
- `.drag-word` / `.drop-sentence` — 拖拽选词游戏
- `.mindmap` — 思维导图小结
- `.hw-cards` — 分层作业卡片

## 导航页

- 第一张幻灯片固定为导航页（`.nav-page`）
- 用 `.course-grid`（3列网格）排列课程卡片（`.course-card`）
- 当前已就绪课程用 `.active-course`（金色边框），不可用课程用 `.upcoming`（灰色半透明 + `pointer-events:none`）
- 如一个课程分多课时，卡片内用 `.c-btns` 放子按钮：`.c-btn.active`（金色可点）/ `.c-btn.locked`（灰色锁定）
- 导航页底部标注操作提示（Home 键返回等）
- 右上角固定 ⌂ 按钮（`.home-btn`），点击 `goToSlide(0)` 随时回导航

## 交互逻辑

### 翻页
- 键盘 ↑↓ / PageUp/PageDown 翻页，Home 回导航，End 到末尾
- 鼠标滚轮（600ms 防抖，`deltaY > 30` 触发）
- 点击左半屏上一页，右半屏下一页
- **跳过规则**：`button`、`kbd`、`.q-opt`、`.drag-word`、`.drop-sentence`、`.drag-reset`、`.trans-toggle` 不触发翻页
- 底部提示栏（`.nav-hint`）显示操作说明
- 顶部进度条（`.progress-bar`）显示当前位置百分比

### 选择题（quiz-item）
- 点选项即时判对错，显示对应反馈文字
- **正确反馈**：绿色边框 + 正确解释 + 口诀对应说明
- **错误反馈**：红色边框 + 抖动动画 + 错误原因 + 口诀纠正
- **可改选**：选错后可点其他选项，清除旧状态重新判断
- **选对后也可点错误选项**：正确答案保持绿色高亮，同时显示错误反馈，方便正反对照
- **双击已选选项**：恢复初始状态，什么都没选
- 选对后错误选项不会锁定（与旧版行为不同，新版本刻意保留此功能供演示用）
- 反馈文字必须以 **✅/❌ + 考点判断 + 口诀对应** 的格式书写，见下方"反馈文案规范"

### 中英翻译
- 英文例句内嵌 `.trans-toggle`（"中"按钮）
- 点击展开紧邻的 `.trans-text`
- **DOM 结构**：按钮在父元素（`.sentence` 或 `p`）内部，翻译文字是父元素的兄弟元素
- `toggleTrans()` 需先找 `btn.nextElementSibling`，找不到再找 `btn.parentElement.nextElementSibling`
- 翻译中从句部分用 `<strong>` 标注，CSS 已设为金色

### 更多例子
- 语法规则卡片底部放 `.show-more-btn`（虚线边框小按钮）
- 点击展开紧邻的 `.extra-examples`
- `.extra-examples` 内每条例句用 `.ex` 包裹，关键词用 `.en`（蓝色）、中文用 `.cn`（金色）

### 拖拽选词游戏
- 支持鼠标拖拽和触屏点击两种方式
- 拖拽 `.drag-word`（that/which）到 `.drop-sentence` 的空格（`.blank`）
- 判对显示绿色 ✓，判错显示红色 ✗
- 每填完一个显示得分
- "重置游戏"按钮清空所有答案

## 反馈文案规范（核心要求）

**每道选择题的正确和错误反馈都必须包含三要素：**

1. **对错标记**：✅ 或 ❌
2. **考点判断**：这句话为什么用 that/which
3. **口诀对应**：明确指出对应哪条口诀的哪个字

**标准格式：**

```
that 类：
✅ 最高级 most interesting → that！口诀"高序唯同不人物"的"高"！
❌ 最高级修饰 → 只能用 that！"高序唯同不人物"的"高"！

✅ 序数词 the first → that！口诀"高序唯同不人物"的"序"！
✅ the only 修饰 → that！口诀"高序唯同不人物"的"唯"！
✅ all 不定代词 → that！口诀"高序唯同不人物"的"不"！
✅ 人+物 → that！口诀"高序唯同不人物"的"人物"！

which 类：
✅ 介词 in 后面 → which！"介词逗号不用 that"的"介词"！
✅ 逗号 → 非限制性 → which！"介词逗号不用 that"的"逗号"！
```

## 口诀系统（核心教学内容）

两条口诀必须同时出现在以下所有页面底部：
- 新知精讲（只能用that页、只用which页）
- 随堂练（that专项练、which专项练）
- 基础练习①②
- 易错题
- 拖拽游戏
- 高考真题
- 对比表格

口诀条格式（紧凑版，用于练习页）：
```html
<div class="mnemonic-card" style="margin-top:10px;padding:8px 20px;display:flex;gap:24px;justify-content:center;align-items:center;">
  <span style="font-size:clamp(14px,1.3vh,18px);font-weight:800;color:var(--blue-light);">"高序唯同不人物" → that</span>
  <span style="color:var(--border);">|</span>
  <span style="font-size:clamp(14px,1.3vh,18px);font-weight:800;color:var(--gold-light);">"介词逗号不用 that" → which</span>
</div>
```

口诀条格式（大号版，用于对比表格等）用 `clamp(18px,1.8vh,26px)`。

## 教学结构（5 段式，40 分钟）

```
导航页 → 导入(5min) → 新知精讲(18min) → 随堂分层练(12min) → 高考真题(3min) → 小结作业(5min)
```

每个阶段在课件中用 `PART N` 编号标签区分。

### 导入页规范
- 用中国学生熟悉的文化例子（哪吒、周杰伦、科比等），**不用美剧**
- 6 个例句覆盖全部关系代词（that/which/who/whom/whose）
- 紧接着用"主人+保镖"比喻页，配合"引路绳"解释关系代词
- 比喻页后接学习目标页（闯关风格，3目标箭头串联 + 中文小故事引导）

### 新知精讲规范
- 讲一个小点、立刻举 2 个例句，不堆理论
- 规则卡片必须带"📋 更多例子"按钮
- 只能用 that：5 条规则合并到一页（3+2网格）
- 只用 which：2 条规则一页
- 每条规则讲完立刻接专项练习（that 5 题 + which 4 题）

### 练习密度规范
- 基础练习：每页 6 题，两页共 12 题
- 易错题：每页 4 题
- 高考真题：至少 5 题
- 题目多时字号自动缩小（vh 驱动），保证不溢出

### 对比表格规范
- 第一列："中文规则名（英文例句小字灰色）"
- 底部放大号口诀条

### 小结作业规范
- 思维导图页：中心节点 + 左右分支
- 作业页：左右双卡（基础层绿色 + 提升层金色）

## 文件命名

- `定语从句教学.html` — 课1：that/which 专题
- 后续课2：who/whom/whose 专题（待开发）
- 其他语法专题：`名词性从句教学.html`、`状语从句教学.html` 等

## 代码约定

- **字号全部用 `clamp()` + `vh/vw`**，不用固定 px。屏幕高则大、矮则小
- **不新增外部依赖**，所有字体/CSS/JS 内联
- **标签匹配**：每个 `<div class="slide">` 必须对应一个 `</div>`，不同幻灯片之间用 `<!-- SLIDE N: xxx -->` 注释分隔
- **幻灯片 class**：首页用 `.active`，封面和导航页额外加 `.title-slide` / `.nav-page`
- **inline style 适度使用**：口诀条、特殊布局的卡片可以写 inline style，避免创建一次性 CSS 类
- **transition 统一**：`.slide` 切换用 `0.45s cubic-bezier(.4,0,.2,1)`
- **中英混合**：中文内容为主，英文例句配翻译按钮，翻译中从句用 `<strong>` 金色高亮
- **面试场景优先**：话术提示、口诀、纠错示范文字直接打在页面上，方便面试时照着讲
- **不添加 emoji 之外的图标库**，用 emoji 和 Unicode 符号（✓✗↑↓→）即可
- **CSS 变量必须用 `var(--xxx)` 引用**，不要硬编码色值

## 常见坑

- `.trans-toggle` 的 `nextElementSibling` 取不到翻译文字（因为按钮在 `.sentence` 内部），需 fallback 到 `parentElement.nextElementSibling`
- 拖拽游戏的 `.trans-toggle` 直接与 `.trans-text` 同级，`nextElementSibling` 能直接取到
- 合并幻灯片后注意更新 JS 中的注释编号，但不影响功能（goToSlide 用 index）
- 选择题 `data-answer` 值必须与选项 `textContent.trim()` 完全一致，包括空格
- wheel 事件加 `{passive: true}` 避免性能警告
- 触屏拖拽需同时处理 `touchstart/touchmove/touchend` 和 `dragstart/dragover/drop`
