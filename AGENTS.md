# 减肥工具网页应用 - 需求拆解文档

## 产品概述

- **产品类型**: Web 工具应用（健康减肥类）
- **场景类型**: <scene_type>prototype-app</scene_type>
- **目标用户**: 有减肥/健康管理需求的普通用户，以及查看个人作品的访客
- **核心价值**: 提供一站式轻量化减肥工具（BMI/TDEE 计算、体重追踪、食物热量查询、运动推荐），数据本地存储无需注册，作为个人作品展示技术能力
- **界面语言**: 中文（zh-CN）
- **主题偏好**: 浅色主题（默认清爽健康风格）
- **导航模式**: 锚点导航（单页应用，顶部导航滚动定位）
- **导航布局**: Topbar（顶部固定导航栏）

---

## 页面结构总览

> **说明**：单页应用，所有功能模块以区块形式组织在首页中，通过顶部锚点导航跳转

**页面文件**: `HomePage.tsx`

| 区块名称 | 锚点 | 区块说明 |
|---------|------|---------|
| Hero 首屏 | `#hero` | 网站标题、副标题、简介，展示作品定位 |
| BMI 计算器 | `#bmi` | 身高体重输入、BMI 实时计算、等级判定、进度条可视化 |
| TDEE 热量计算器 | `#tdee` | 性别/年龄/身高/体重/活动量输入、基础代谢计算、减脂期热量建议、热量缺口提示 |
| 体重记录与趋势 | `#weight` | 添加体重记录、历史列表、折线图趋势、累计减重统计、平均速度计算 |
| 食物热量速查 | `#foods` | 分类 tab（主食/肉类/蔬果/零食）、搜索框、食物热量卡片列表、每份重量标注 |
| 居家运动推荐 | `#exercise` | 强度分级 tab（低/中/高）、运动卡片列表（名称/时长/消耗热量/动作说明） |
| 底部署名 | `#footer` | 作品署名、个人信息、制作时间，体现个人作品属性 |

---

## 页面布局建议

- **布局模式**: 单栏居中布局（最大宽度约 1200px），各功能区块上下排列，每个计算器模块采用卡片式包裹
- **视觉重心**: 输入与结果并重，每个计算器模块内部采用"左侧输入 + 右侧结果展示"的左右分栏结构（桌面端），移动端自动堆叠为上下结构
- **结果承载区**: 每个计算类模块（BMI/TDEE）都有独立的结果面板，初始态为引导文案（如"输入数据后自动计算"）；数据展示类模块（体重趋势/食物/运动）初始加载即展示内容
- **交互过渡**: 区块切换有平滑滚动动画，结果数值变化有过渡效果，卡片 hover 有微交互

---

## 导航配置

- **导航布局**: Topbar（顶部固定，背景半透明毛玻璃效果）
- **导航项**:

| 导航文字 | 锚点 |
|---------|------|
| BMI 计算 | `#bmi` |
| 热量计算 | `#tdee` |
| 体重记录 | `#weight` |
| 食物热量 | `#foods` |
| 运动推荐 | `#exercise` |

---

## 数据来源声明

| 数据/操作 | 来源类型 | 实现要求 | mock 兜底 |
|---|---|---|---|
| BMI 计算结果 | demo-mock | 前端公式实时计算 `BMI = 体重(kg) / 身高(m)²`，等级按 WHO 标准判定 | 本身即前端计算，无需 mock |
| TDEE 计算结果 | demo-mock | 前端 Mifflin-St Jeor 公式计算 BMR，再乘以活动系数得 TDEE，减脂范围按 TDEE 的 80%-90% 估算 | 本身即前端计算，无需 mock |
| 体重记录数据 | local-persist | localStorage key=`__app_fit_track_weightRecords`，JSON 数组存储，含日期和体重字段，页面初始化时读取 | 初始为空数组，无 mock 数据 |
| 食物热量数据 | demo-mock | `src/data/foods.ts` 中定义常量数组，按分类组织，包含名称、分类、每100g热量、常见每份重量 | ✅ 本身就是 mock 静态数据 |
| 居家运动数据 | demo-mock | `src/data/exercises.ts` 中定义常量数组，按强度分级，包含名称、强度、时长、消耗热量、动作说明 | ✅ 本身就是 mock 静态数据 |

> **说明**：用户明确要求"数据使用本地存储，刷新页面后记录不丢失"，体重记录为核心持久化数据；食物热量和运动推荐为参考数据，以静态常量方式内置。所有计算均为前端公式，无需后端。

---

## 功能列表

> **说明**：按页面区块组织功能点，不含导航（导航为全局配置）

- **区块**: Hero 首屏
  - **页面目标**: 展示网站定位和作品介绍，引导用户向下浏览
  - **功能点**:
    - 展示主标题"减肥工具箱"和副标题"你的轻量化健康管理助手"
    - 展示简短的功能简介和作品说明
    - 主 CTA 按钮"开始计算"，点击平滑滚动到 BMI 区块

- **区块**: BMI 身体质量指数计算器
  - **页面目标**: 用户输入身高体重，快速了解自己的 BMI 水平和体重等级
  - **功能点**:
    - 身高（cm）和体重（kg）输入框，支持数字输入实时响应
    - 自动计算 BMI 值，结果大字展示，保留1位小数
    - 体重等级判定（偏瘦/正常/超重/肥胖），对应不同颜色标签
    - BMI 区间可视化进度条，标注当前值所在位置及各区间分界点
    - 给出简短的健康建议文案（根据等级动态变化）

- **区块**: 每日热量需求计算器（TDEE）
  - **页面目标**: 计算每日总消耗热量，给出减脂期建议摄入范围
  - **功能点**:
    - 表单输入：性别（男/女单选）、年龄、身高、体重、活动量（5级选择：久坐/轻度/中度/高强度/极高强度）
    - 使用 Mifflin-St Jeor 公式计算基础代谢率（BMR）
    - 乘以活动系数得出每日总能量消耗（TDEE），大卡单位展示
    - 给出减脂期建议摄入热量范围（如 TDEE 的 80%-90%）
    - 显示每日热量缺口参考值及预计减重速度说明

- **区块**: 体重记录与趋势图
  - **页面目标**: 记录每日体重，追踪变化趋势，数据本地持久化
  - **功能点**:
    - **添加体重记录**: 日期选择器（默认今天）+ 体重输入 + "添加"按钮，提交后写入 localStorage 并刷新列表
    - 体重记录列表，按日期倒序展示，支持单条删除操作
    - 折线图展示体重变化趋势（横轴日期，纵轴体重）
    - 统计面板：累计减重斤数（最新-最早）、平均减重速度（斤/周）、记录天数
    - 页面加载时从 localStorage 读取历史数据，空状态时显示引导提示
    - 数据契约：`IWeightRecord { id: string; date: string; weight: number }`

- **区块**: 常见食物热量速查表
  - **页面目标**: 快速查询常见食物的热量，辅助饮食控制
  - **功能点**:
    - 分类 Tab 切换：主食 / 肉类 / 蔬果 / 零食
    - 搜索框：支持按食物名称模糊筛选，与分类筛选联动
    - 食物卡片网格列表，展示食物名称、每100g热量、常见每份重量及对应热量
    - 卡片按热量从低到高排序，高低热量有视觉区分（颜色标签）

- **区块**: 居家运动推荐
  - **页面目标**: 提供不同强度的居家运动参考，帮助用户选择合适的锻炼方式
  - **功能点**:
    - 强度分级 Tab 切换：低强度 / 中强度 / 高强度
    - 运动卡片列表，展示动作名称、建议时长、消耗热量估算
    - 每张卡片包含简单的动作说明和注意事项
    - 卡片展开/收起详情（可选交互，默认展示摘要，点击展开完整说明）

- **区块**: 底部署名
  - **页面目标**: 体现个人作品属性，展示创作者信息
  - **功能点**:
    - 展示作品名称"减肥工具箱 - 个人作品"
    - 展示创作者署名和制作时间
    - 简短的技术栈说明（可选，体现作品属性）
    - 回到顶部按钮

---

## 数据共享配置

> **说明**：BMI 和 TDEE 计算器都需要身高和体重输入，可共享数据减少重复输入

| 存储键名 | 数据说明 | 使用区块 |
|---------|---------|---------|
| `__app_fit_track_profile` | 用户基础信息，类型为 `IUserProfile`，含身高、体重、性别、年龄 | BMI 计算器、TDEE 计算器 |

```ts
interface IUserProfile {
  /** 身高，单位 cm */
  height?: number;
  /** 体重，单位 kg */
  weight?: number;
  /** 性别 */
  gender?: 'male' | 'female';
  /** 年龄 */
  age?: number;
}
```

**共享逻辑说明**：用户在 BMI 模块输入身高体重后，TDEE 模块自动填充对应字段；反之亦然。数据同时写入 localStorage（可选持久化用户资料），刷新页面后保留。

---

## 技术选型建议

- **前端框架**: React + TypeScript
- **样式方案**: Tailwind CSS
- **图表库**: Recharts（体重趋势折线图，轻量且与 React 集成良好）
- **图标库**: Lucide React
- **状态管理**: React useState + useContext（简单场景，无需 Redux）
- **数据持久化**: localStorage
- **动画**: CSS transition + 自定义 scroll-behavior（平滑滚动）

-------

<scene_type>prototype-app</scene_type>

# UI 设计指南

## 1. 设计推导依据

- **参考意图**: Free Direction —— 无参考材料，完全从减肥工具产品语义出发自主设计
- **核心情绪 / 应用类型**: 轻量健康工具，传递清爽、鼓励、可达成的成长感，而非焦虑的数字压力
- **独特记忆点**: BMI 进度条与体重趋势图共用同一套"渐变绿阶"视觉语言，每一个数据点都像成长路上的一枚小绿叶标记

## 2. Art Direction

- **方向名**: 清新薄荷 · 数据轻工具
- **Design Style**: Soft Blocks 柔色块 + Swiss Minimalist 瑞士极简 —— 柔色块营造健康友好氛围，瑞士极简保证计算器与数据表格的清晰度
- **DNA 参数**: 圆角 soft（rounded-xl）/ 阴影 subtle（shadow-sm）/ 间距 standard（gap-4 / p-6）/ 字体方向 圆润无衬线 + 几何感数字 / 装饰手法 极细描边卡片 + 淡彩块面点缀
- **应用类型**: Tool —— 单页锚点导航，功能模块卡片化分布，信息密度中等

## 3. Color System

**色彩关系**: 薄荷绿主色 + 同色系极浅反馈底 + 米白暖调背景 + 深灰绿正文
**配色设计理由**: 薄荷绿传达健康、清新、成长的语义，避免传统减肥产品的焦虑红或医疗冷蓝；暖白背景降低长时间使用的视觉疲劳；深灰绿正文保证阅读舒适度并与主色呼应。
**主色推导**: 从"健康成长、自然轻盈"的产品语义出发，选择绿色系中饱和度适中、偏冷调的薄荷绿（hsl 152°），既有健康辨识度又不过于草药感；通过降低饱和度和提高明度衍生 accent 与状态色。
**使用比例**: 60% 中性（bg + card + border）/ 30% 辅助（accent + muted）/ 10% primary；primary 仅用于主按钮、关键数值高亮、进度条激活段、当前导航项。

| 角色 | CSS 变量 | Tailwind Class | HSL 值 | 设计说明 |
|---|---|---|---|---|
| bg | `--background` | `bg-background` | hsl(150 20% 97%) | 页面背景，暖调浅薄荷白 |
| card | `--card` | `bg-card` | hsl(0 0% 100%) | 卡片、表单、弹层，纯白承载内容 |
| text | `--foreground` | `text-foreground` | hsl(155 25% 15%) | 标题和正文，深灰绿 |
| textMuted | `--muted-foreground` | `text-muted-foreground` | hsl(152 10% 45%) | 占位符、说明、辅助元信息 |
| primary | `--primary` | `bg-primary` / `text-primary` | hsl(152 60% 45%) | 主交互、CTA、激活态、品牌识别，薄荷绿 |
| primaryForeground | `--primary-foreground` | `text-primary-foreground` | hsl(0 0% 100%) | primary 上的文字和图标 |
| accent | `--accent` | `bg-accent` | hsl(150 35% 92%) | hover/focus 浅底、选中浅底、菜单项状态，浅薄荷 |
| accentForeground | `--accent-foreground` | `text-accent-foreground` | hsl(155 25% 20%) | accent 上的文字和图标 |
| border | `--border` | `border-border` | hsl(150 15% 88%) | 输入框、卡片、菜单边界，淡绿灰 |

**语义色提示**:
- 成功（达标/正常范围）：hsl(152 60% 45%) — bg: hsl(150 50% 93%) / border: hsl(152 50% 75%) / text: hsl(155 50% 28%)；与 primary 同色温
- 警告（超重/偏高）：hsl(35 85% 55%) — bg: hsl(38 90% 94%) / border: hsl(36 80% 78%) / text: hsl(30 70% 30%)；饱和度与 primary 对齐 ±10%
- 错误（肥胖/超标）：hsl(0 70% 60%) — bg: hsl(0 70% 95%) / border: hsl(0 65% 82%) / text: hsl(0 60% 30%)；饱和度略低于 primary，避免刺眼
- BMI 区间渐变：从偏瘦（浅蓝 hsl 200 70% 85%）→ 正常（薄荷绿 hsl 152 60% 55%）→ 超重（橙 hsl 35 85% 65%）→ 肥胖（红 hsl 0 70% 65%），用在进度条背景

## 4. 字体与节奏

- **font-display**: Noto Sans SC —— 圆润现代的中文无衬线，数字形态几何感好，适合计算器类工具
- **font-body**: Noto Sans SC —— 正文与标题统一字体族，通过字重区分层级，保持轻盈干净的阅读节奏
- **字号**: H1 text-4xl（移动端 text-3xl）；H2 text-2xl；body text-base；muted text-sm；关键数值（BMI结果/热量结果）使用 text-3xl font-semibold
- **圆角**: 大 —— rounded-xl 卡片与按钮，rounded-full 进度条与胶囊按钮，营造柔和友好感

## 5. 全局布局契约

- **Reference Layout Use**: 按需求结构推导，5 个功能模块以卡片形式纵向排列，顶部锚点导航
- **Page / Section Order**: Hero（标题+简介）→ BMI 计算器 → TDEE 计算器 → 体重记录与趋势 → 食物热量速查 → 居家运动推荐 → Footer（作品署名）
- **Standard Content Zone**: Tool max-w-4xl + `mx-auto`，适合计算器与卡片式工具的阅读宽度
- **Shell / Frame Alignment**: 同宽 —— 顶部导航内容区与页面内容区同宽对齐，footer 同宽
- **Padding & Rhythm**: `px-4 md:px-6 py-10 md:py-14`，section 间距 `space-y-10 md:space-y-14`，保持 8px 倍数
- **Full-bleed Zones**: 无全宽需求；所有内容受 Standard Content Zone 约束
- **Local Narrowing**: BMI 与 TDEE 计算器表单在卡片内收窄，`max-w-md mx-auto` 居中
- **Overflow Strategy**: 食物热量表格移动端使用 `overflow-x-auto`，体重趋势图容器同理
- **Flexibility Boundary**: 允许移动端卡片内边距从 p-6 调整为 p-4，允许食物列表移动端改为单列；不允许改变主色、圆角、阴影和全局 max-w

## 6. 视觉与动效

- **装饰**: 极细描边 + 淡彩柔色块点缀 + 微小绿叶图形装饰
- **阴影/边界**: 轻 —— 卡片使用 `shadow-sm` + `border` 双层边界感，hover 时阴影加深至 `shadow-md`
- **动效**: 精致 —— 卡片 hover 有 4px 上浮 + 阴影过渡（duration-200 ease-out）；计算结果数字有 200ms 淡入上滑；进度条填充有 600ms ease-out 动画；页面内锚点跳转 smooth scroll

## 7. 组件原则

- 按钮、输入框、单选组、选项卡必须具备 Default / Hover / Active / Focus-visible / Disabled 状态
- Primary 按钮承担计算、添加记录等主行动；Outline / Ghost 用于次级操作和筛选切换
- BMI 进度条和体重趋势图使用统一的薄荷绿主色 + 渐变区间色彩系统
- 空状态（无体重记录时）使用浅薄荷底 + 绿叶图标 + 鼓励文案，延续产品语言
- 所有可交互元素 focus-visible 状态使用 `ring-2 ring-primary ring-offset-2`，保证键盘可达

## 8. Image Direction

- **Image Role**: 装饰插画（Hero 区点缀 + 各模块空状态/分隔小图）
- **Image Art Direction**: 极简扁平风格，柔和薄荷绿与米白为主色，局部橙黄点缀；主体为简约线条勾勒的健康生活元素（卷尺、苹果、运动鞋、水杯），构图留白充足，光线均匀无阴影，材质为扁平化色块+极细描边，情绪轻松愉悦有活力
- **Image Prompt Keywords**: flat illustration, mint green and cream white, minimal health icons, tape measure apple sneaker water bottle, soft pastel colors, thin outline, clean white background, playful healthy lifestyle, simple geometric shapes, gentle and uplifting mood
- **Image Avoidance**: 真实人物减肥前后对比图、医用人体解剖图、高饱和霓虹色、3D 塑料质感图标、浓重阴影和透视、焦虑紧张的情绪表达

## 9. Anti-patterns

- **Split personality**: 每个功能模块各自为政用不同色和圆角；全站共享薄荷绿主色、rounded-xl 圆角和 shadow-sm 边界语言
- **Phantom tokens**: 随意命名 `--success` / `--warning` 却不在主题中定义；所有语义色必须写入 CSS 变量系统
- **Default SaaS drift**: 回到默认蓝按钮、紫色渐变、通用卡片堆叠；用薄荷绿 + 柔色块 + 绿叶视觉锚点塑造减肥工具专属气质
- **Invisible interaction**: 只做 hover 不做 focus-visible；每个输入框、按钮、选项卡都要有清晰的键盘聚焦态
- **Mono-hue tyranny**: 主按钮、tab、icon、边框、链接、图表全用 primary 绿；按 60-30-10 比例，primary 只出现在 CTA 和关键高亮
- **Status color drift**: 警告红和错误红饱和度过高，与克制的薄荷绿主色格格不入；语义色饱和度与 primary 对齐 ±15%，色温偏暖以保持和谐
- **Anxiety-driven design**: 用大红色、警感叹号、"你超重了"等刺激性文案传达结果；使用鼓励式文案和柔和色彩，强调成长而非批判