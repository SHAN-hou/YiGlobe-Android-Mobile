# 八卦星云图 (YiGlobe) - 手机 APP 开发资料包

**版本**：Mobile V1.0（2026-04-24）
**基于**：deploy_mobile 最新版，已在线运行验证

---

## 一、项目概述

基于 Three.js 的 3D 易经六十四卦可视化项目，需**完全复刻**为手机 APP（iOS/Android）。
本资料包包含完整源码、图片资源、离线依赖库，可直接运行。

### 在线预览
- GitHub Pages: https://shan-hou.github.io/yiglobe-mobile/
- Surge: https://yiglobe-mobile.surge.sh

---

## 二、文件结构

```
APP开发资料包_Mobile/
├── README.md                      # 本说明文件
├── index.html                     # 主文件（在线版，CDN 加载 Three.js）
├── index_offline.html             # 离线版（本地加载 Three.js）
├── hexdetail.js                   # 卦象详情面板（64卦爻辞、变卦逻辑）
├── lib/                           # 离线依赖库
│   ├── es-module-shims.js         # ES Module polyfill
│   └── three/                     # Three.js r160
│       ├── build/three.module.js
│       └── examples/jsm/
│           ├── controls/OrbitControls.js
│           ├── renderers/CSS2DRenderer.js
│           └── lines/（Line2, LineMaterial, LineGeometry, ...）
├── AI命名卦图/                     # 59张卦象缩略图（jpg，约50-200KB/张）
│   ├── 02_坤为地.jpg
│   └── ...
└── hexagram_explanation_page_images_standard_margin/  # 59张释义大图（jpg）
    ├── 02_坤为地.jpg
    └── ...
```

> **说明**：两个图片目录文件名一致。缩略图用于浮窗预览，大图用于灯箱全屏放大查看（含详细释义）。
> 目前有 59 张图（64卦中 5 张缺失，代码已处理缺失情况显示"暂无"提示）。

---

## 三、完整功能清单（APP 需 100% 复刻）

### 1. 3D 球面卦象展示
- 64 个卦象按 **Drasny 构造法** 分布在球面上（乾=北极，坤=南极）
- 每个卦象是一个发光点精灵（Sprite），颜色由六爻阴阳决定
- 支持**触摸拖拽旋转**、**双指缩放**（OrbitControls）

### 2. 卦象标签（DOM 标签叠加在 3D 上）
- 每个卦象标签三行：
  - 第1行：`卦名·序号`（如"涣·59"），21px
  - 第2+3行：上卦名(象) + 下卦名(象) + 六十四卦 Unicode 符号（32px）
- **景深效果**：前半球清晰，后半球模糊+半透明
- 可通过"易"面板复选框切换显隐

### 3. 变爻连线
- 每个卦象与翻转一爻后得到的 6 个卦象相连（共 192 条线）
- 线条颜色为两端颜色混合，半透明叠加混合
- **1→43 和 1→44** 两条线为向外拱起的贝塞尔弧线（太极鱼形）

### 4. 月令循环模式（Lunar Cycle）
- 12 个月令卦按序：`坤2→复24→临19→泰11→大壮34→夬43→乾1→姤44→遁33→否12→观20→剥23`
- 月令环线：Fourier 低通平滑的闭合球面曲线（S 形太极线）
- 前半球金色实线，后半球灰色虚线
- 激活时：**只显示 12 个月令卦**的标签和精灵，其他隐藏
- 流光球沿月令线运动，3 秒/卦
- 标签透明度随距离变化（近=1，远=0.01）

### 5. 运动模式（共 7 种）
| 模式 | 说明 |
|------|------|
| 静止 | 默认，手动旋转浏览 |
| 年轮(月令循环) | 流光沿月令线运行 |
| 爻变脉冲 | 脉冲沿变爻连线扩散 |
| 层级公转 | 按纬度层分组公转 |
| 登天螺旋(54卦) | 从坤到乾螺旋上升 |
| 升龙螺旋(均匀) | 均匀螺旋线 |
| 爻变总流(全局) | 全局流线动画 |

### 6. 太极阴阳鱼
- 球面 WebGL shader 绘制的阴阳鱼图案
- 分界线与月令环线对齐

### 7. 粒子系统
- 球面内外的星尘粒子，可切换显隐

### 8. 经纬网格
- 可切换显隐的球面经纬线

### 9. 搜索功能
- 顶部搜索栏，支持按卦名、序号、上下卦名搜索
- 输入即时过滤，显示匹配列表
- 点击搜索结果：高亮 + 镜头飞向该卦

### 10. 图片预加载
- 页面加载 1.5 秒后后台预加载所有 64 卦缩略图和大图
- 双击弹出时从缓存直接显示，无需等待加载

---

## 四、UI 浮窗系统（4 个可拖动浮窗）

所有浮窗均为**可拖动浮动窗口**，不是底部抽屉。

### 图标布局

**竖屏模式**：
```
[易] [搜索栏............] [卦][详][ⓘ]   ← 全部在顶行
```

**横屏模式**：
```
[易]                           [卦][详][ⓘ]   ← 全部在顶行(top:12px)
         [搜索栏]
```

### 4 个浮窗详细规格

| 图标 | ID | 功能 | 关闭按钮 |
|------|-----|------|---------|
| **易** | #hud / #hudToggle | 控制面板（复选框、运动模式切换） | ✕ |
| **卦** | #hexImg / #hexImgToggle | 卦象古图缩略图 + 灯箱放大 | ✕ |
| **详** | #hexDetail / #hexDetailToggle | 卦象详情（卦辞、爻辞、变卦） | ✕ |
| **ⓘ** | #modeInfo / .mi-toggle | 运动模式说明 | × |

### 浮窗通用特性
- **图标样式**：圆形按钮，半透明深色背景，金色/青色边框
  - 竖屏：32×32px
  - 横屏：36×36px（默认44×44px）
- **展开动画**：淡入缩放 0.2s ease
- **拖拽**：标题栏 pointer event 拖拽（移动+桌面通用）
  - 边界约束：不超出视窗
- **关闭**：点击 ✕ 按钮收起回图标
- **联动**：打开卦图浮窗时自动收起模式说明浮窗

### 竖屏特殊适配
- "易"面板：宽度 140px，字号 9px
- 模式说明面板：宽度 140px，匹配"易"面板大小
- 搜索栏：宽度自适应（去除图标占用空间后的剩余宽度）

### 卦象古图浮窗（#hexImg）
- 默认宽度 260px（竖屏 200px）
- 显示缩略图（`AI命名卦图/` 目录）
- 点击图片 → 打开**灯箱**全屏显示释义大图（`hexagram_explanation_page_images_standard_margin/`）

### 灯箱（#hexLightbox）
- 全屏遮罩，支持：
  - **双指缩放**（pinch zoom）
  - **鼠标滚轮缩放**
  - **拖动平移**
  - **双击重置**（缩放回原始大小）
- 右上角 ✕ 关闭按钮
- 底部显示卦名标题

### 卦象详情面板（#hexDetail）
由 `hexdetail.js` 驱动，内容包括：
- 卦符号（Unicode ䷀~䷿，56px 大字）
- 上下卦：`天乾(上) 地坤(下)`
- **卦辞**：如"元亨利贞"
- **爻辞**：六爻 + 用九/用六（初九/九二/...上九）
- **变卦**：该卦六爻分别变化后得到的 6 个卦名标签

---

## 五、交互规则（★ 移动端关键 ★）

### 触摸交互
| 操作 | 效果 |
|------|------|
| 单指拖动 | 旋转 3D 球体 |
| 双指缩放 | 缩放球体远近 |
| **单击**卦象 | 仅高亮该卦（视觉反馈） |
| **双击**卦象（280ms内） | 高亮 + 弹出卦图浮窗 + 弹出详情浮窗 |
| 拖动超过 8px | 视为旋转操作，不触发点击 |

### 双击判定实现
```kotlin
// Android GestureDetector 示例
private var lastTap = 0L
private var lastNode: HexNode? = null

gestureDetector.onSingleTapUp { e ->
    val node = pickNode(e.x, e.y) ?: return
    val now = System.currentTimeMillis()
    if (now - lastTap < 280 && lastNode?.num == node.num) {
        // 双击：弹出所有面板
        highlight(node)
        showHexImage(node)
        showHexDetail(node)
        lastTap = 0; lastNode = null
    } else {
        // 单击：仅高亮
        highlight(node)
        lastTap = now; lastNode = node
    }
}
```

### 浮窗拖拽实现
```javascript
// 标题栏拖拽核心逻辑（已实现）
head.addEventListener('pointerdown', e => {
    dragging = true;
    sx = e.clientX; sy = e.clientY;
    const r = panel.getBoundingClientRect();
    ox = r.left; oy = r.top;
    head.setPointerCapture(e.pointerId);
});
head.addEventListener('pointermove', e => {
    if (!dragging) return;
    let nx = ox + e.clientX - sx, ny = oy + e.clientY - sy;
    // 边界约束
    nx = Math.max(4, Math.min(window.innerWidth - panel.offsetWidth - 4, nx));
    ny = Math.max(4, Math.min(window.innerHeight - panel.offsetHeight - 4, ny));
    panel.style.left = nx + 'px'; panel.style.top = ny + 'px';
});
```

### 重要：touch-action
- Canvas 元素设置 `touch-action: manipulation` 消除浏览器 300ms 点击延迟
- 灯箱设置 `touch-action: none` 允许自定义手势处理

---

## 六、技术栈建议

### 方案 A（推荐，最快）：WebView 嵌入
```
Android: Activity → WebView → assets/index_offline.html
iOS:     ViewController → WKWebView → Bundle/index_offline.html
```
**优点**：100% 复刻，工作量最小
**步骤**：
1. 将本资料包所有文件放入 `assets/`（Android）或 `Bundle`（iOS）
2. 使用 `index_offline.html`（已本地化所有依赖）
3. WebView 配置：
   - 启用 JavaScript
   - 允许本地文件访问
   - 设置 `touch-action` 不干扰手势
   - `viewport-fit=cover` 适配刘海屏

### 方案 B：React Native + three.js（expo-three）
- 用 expo-gl 渲染 Three.js
- UI 面板用原生组件
- 工作量中等

### 方案 C：Flutter + flutter_3d / Unity as Library
- 跨平台兼容好，但生态不如 Three.js 成熟

---

## 七、数据结构速查

### HEX（64 卦主数据）
```javascript
const HEX = [
  [1,"乾","111111"], [2,"坤","000000"], [3,"屯","010001"], ...
];
// [序号, 卦名, 六爻二进制(顶爻在左)]
```

### TRIGRAM（八卦映射）
```javascript
{ '111':['乾','天'], '000':['坤','地'], '001':['震','雷'],
  '010':['坎','水'], '100':['艮','山'], '110':['巽','风'],
  '011':['兑','泽'], '101':['离','火'] }
```

### MONTH_SEQ（月令卦序）
```javascript
[2, 24, 19, 11, 34, 43, 1, 44, 33, 12, 20, 23]
```

### HEX_SYMBOL（Unicode 卦符号）
```javascript
{1:'䷀', 2:'䷁', 3:'䷂', ... 64:'䷿'}
```

### GUA_CI（卦辞）
```javascript
{1:'元亨利贞', 2:'元亨,利牝马之贞', ...}
```

### YAO（爻辞，hexdetail.js 中，共 64×6~7 条）
```javascript
YAO[1] = ['初九：潜龙勿用', '九二：见龙在田...', ...]
```

### IMG_MAP（图片文件名映射）
```javascript
{ 1:'01_乾为天.jpg', 2:'02_坤为地.jpg', 3:'03_水雷屯.jpg', ... }
// 缩略图目录: AI命名卦图/
// 大图目录: hexagram_explanation_page_images_standard_margin/
```

---

## 八、响应式布局规格

### CSS Media Query 断点
| 条件 | 应用场景 |
|------|---------|
| `max-width: 768px` | 手机通用（图标缩小、浮窗尺寸调整） |
| `max-width: 480px` | 小屏手机（HUD 字号更小） |
| `orientation: landscape` + `max-height: 600px` | 手机横屏（图标全部移到顶行） |
| `orientation: portrait` + `max-width: 768px` | 手机竖屏（五元素同顶行布局） |

### 视觉规格参数
| 项目 | 参数 |
|------|------|
| 背景色 | `#05060a`（近黑） |
| 浮窗背景 | `rgba(10,10,20,0.85)` + `backdrop-filter: blur(8px)` |
| 浮窗边框 | 青色 `#33ccff` 或 金色 `rgba(255,225,122,0.45)` |
| 标题色 | `#ffe17a`（金色） |
| 正文色 | `#ccddff`（浅青）/ `#dcdce5`（浅灰） |
| 辅助色 | `#7fd8ff`（亮蓝）/ `#9aa0b4`（灰） |
| 圆形图标 | 半透明深色背景 + 模糊 |
| 动画时长 | 0.2s ease |
| 字体 | "Microsoft YaHei", "PingFang SC", sans-serif |
| Tooltip 大卦符号 | 80px |
| 详情面板卦符号 | 56px |

---

## 九、验收清单

复刻完成标准（APP 需逐项通过）：

### 3D 渲染
- [ ] 3D 球面可触摸拖拽旋转，双指缩放
- [ ] 64 卦 Sprite 位置、颜色、标签完全一致
- [ ] 景深模糊效果（前清后模糊）
- [ ] 192 条变爻连线，1→43 / 1→44 弧线正确
- [ ] 阴阳鱼 shader 球面图案
- [ ] 星尘粒子
- [ ] 经纬网格切换

### 运动模式
- [ ] 7 种运动模式全部正常
- [ ] 月令模式流光球沿 S 形线运动
- [ ] 模式说明面板内容随模式切换更新

### 交互
- [ ] **双击卦象**（280ms）触发古图浮窗 + 详情浮窗
- [ ] 单击仅高亮
- [ ] 拖动 > 8px 视为旋转，不触发点击
- [ ] canvas 无 300ms 延迟（touch-action: manipulation）

### 浮窗系统
- [ ] 4 个浮窗图标（易/卦/详/ⓘ）
- [ ] 横屏：全部在顶行同一行
- [ ] 竖屏：全部在顶行同一行（缩小图标）
- [ ] 浮窗可拖动（标题栏拖拽）
- [ ] 关闭按钮 ✕ 收起回图标
- [ ] 浮窗边界约束（不超出屏幕）

### 卦象图片
- [ ] 缩略图显示正确
- [ ] 灯箱放大：双指缩放 + 拖动 + 双击重置
- [ ] 图片预加载（后台加载，双击即显）

### 详情面板
- [ ] 卦符号、上下卦、卦辞正确
- [ ] 爻辞（6~7 条）正确
- [ ] 变卦标签正确（6 个）

### 搜索
- [ ] 搜索卦名、序号、上下卦名
- [ ] 点击结果高亮 + 飞向该卦

---

## 十、开发者注意事项

1. **完整复刻** — 所有视觉、动画、数据不可删减
2. **移动端交互差异** — 单击≠双击，**只有双击才弹出面板**
3. **离线使用** — 使用 `index_offline.html` + `lib/` 目录，无需联网
4. **图片优化** — 当前图片已压缩，APP 可按需进一步优化
5. **刘海屏适配** — 使用 `viewport-fit=cover` + `safe-area-inset-*` 环境变量
6. **WebView 方案** — 最省工期，直接加载 `index_offline.html` 即可运行
7. **测试设备** — 建议在 iPhone SE（小屏）、iPhone 14 Pro（刘海）、安卓折叠屏上测试

如有疑问，直接在电脑浏览器打开 `index_offline.html` 对照效果。
在线版地址：https://shan-hou.github.io/yiglobe-mobile/
