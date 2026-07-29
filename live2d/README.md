# Live2D 看板娘配置文档

本配置位于 `config/_default/params.yml` 的 `injector.body_end` 字段中，用于在网站左下角加载 Live2D 角色模型。

---

## 整体架构

```
injector.body_end:
  ├── <style>          → CSS 控制画布位置/大小/响应式
  ├── <canvas>         → PixiJS 渲染画布
  ├── <script> CDN 加载 → pixi.js + Cubism Core + pixi-live2d-display
  └── <script> 逻辑    → 加载模型、播放动作、交互
```

依赖三个 CDN 库：
- **pixi.js v7**：2D WebGL 渲染引擎
- **Cubism Core**：Live2D 官方运行时
- **pixi-live2d-display**：将 Cubism 模型接入 PixiJS 的桥接库

---

## 一、CSS 样式 (`<style>`)

```css
#live2dcanvas {
  position: fixed;    /* 固定定位，不随滚动移动 */
  left: 0;            /* 距左边 0px（0 = 贴左边缘，auto = 取消） */
  right: auto;        /* 距右边自动（auto = 不生效，设数值则定右） */
  bottom: 0;          /* 距底部 0px（0 = 贴底） */
  width: 250px;       /* 画布显示宽度 */
  height: 350px;      /* 画布显示高度 */
  z-index: 99999;     /* 层级，确保在最上层 */
  pointer-events: auto;  /* auto=可点击互动, none=穿透不拦截点击 */
  cursor: pointer;    /* 鼠标悬停样式（pointer=手型） */
}
@media screen and (max-width: 959px) {
  #live2dcanvas { display: none !important; }  /* 手机/平板隐藏 */
}
```

### 常用调整

| 想要的效果 | 改什么 |
|-----------|--------|
| 放右下角 | `left: auto; right: 0;` |
| 放左上角 | `left: 0; bottom: auto; top: 0;` |
| 模型太大 | 加大 `width` / `height` |
| 模型太小 | 减小 `width` / `height` |
| 不想被点击 | `pointer-events: none;` |
| 手机也显示 | 删掉 `@media` 整段 |
| 改隐藏断点 | 修改 `959px` 为其他值 |
| 降低层级（不挡导航） | 减小 `z-index`，比如 `100` |

---

## 二、Canvas 画布 (`<canvas>`)

```html
<canvas id="live2dcanvas" width="600" height="800"></canvas>
```

- `width` / `height` 是**内部渲染分辨率**，跟 CSS 显示尺寸独立
- 比值应与 CSS 的 `width/height` 接近，避免模型变形
- 当前：600/800 = 0.75，CSS 250/350 ≈ 0.71，比例基本一致
- 改大 → 模型更清晰但更耗性能；改小则反之

---

## 三、PixiJS 初始化

```js
const app = new PIXI.Application({
  view: canvas,           // 绑定到上面的 canvas
  width: 600,             // 与 canvas width 一致
  height: 800,            // 与 canvas height 一致
  backgroundAlpha: 0,     // 背景透明（0=全透明，1=不透明）
  premultipliedAlpha: true  // 预乘 Alpha，修复贴图混合问题
});
```

通常不需要改动。

---

## 四、模型加载

```js
const model = await PIXI.live2d.Live2DModel.from(
  '/live2d/37_skin4/v2a2_306604_37.model3.json'
);
```

**切换皮肤**：改路径里的 `37_skin4` 为其他皮肤目录名，同时修改 `allMotions` 动作列表。

当前可用的皮肤（位于 `static/live2d/`）：

| 目录 | 皮肤名 | 说明 |
|------|--------|------|
| `37_skin4/` | 泉眼深处 | 当前使用，50+ 贴图（已修复白块） |
| `37_skin5/` | 完美的流体 | 备选，6 贴图，干净无白块 |

---

## 五、模型位置和缩放

```js
model.scale.set(0.14);   // 缩放比例：越大模型越大
model.x = -50;           // X 轴偏移：负数=左移，正数=右移
model.y = 420;           // Y 轴偏移：越小越往上，越大越往下
```

| 参数 | 作用 | 调整方向 |
|------|------|----------|
| `scale.set(x)` | 整体缩放 | 0.08≈很小，0.2≈比较大 |
| `model.x` | 水平位置 | -100≈很左，0≈居中，200≈很右 |
| `model.y` | 垂直位置 | 0≈模型跑到顶部去了，800≈模型沉到底部以下 |

> 注意：Y 轴 0 在**顶部**，数值增大向下移动。

**调整技巧**：改完后 `hugo server` 自动热重载，F5 刷新立刻看到效果，不需要重启。

---

## 六、动作系统

### 动作列表

```js
const allMotions = [
  'b_diantou', 'b_feizou', 'b_fumo', 'b_gongji', 'b_idle',
  'b_jiangluo', 'b_jiangluo2', 'b_naxia', ...
  't_idle', 't_kaixin', ...
];
```

- 命名规则：`b_` = 身体动作，`t_` = 头部/表情动作
- 这些都是从对应皮肤的 `model3.json` 里提取的
- **换皮肤后必须同步更新此列表**

### 动作循环

```js
let loopTimer = null;

function playRandomMotion() {
  clearTimeout(loopTimer);
  const name = allMotions[Math.floor(Math.random() * allMotions.length)];
  model.internalModel.motionManager.startMotion(name, 0);
  loopTimer = setTimeout(playRandomMotion, 5000);  // 5秒换一次
}
```

| 参数 | 作用 |
|------|------|
| `5000`（毫秒） | 动作切换间隔，改大=动作播更久才换 |

### 触发方式

```js
setTimeout(playRandomMotion, 500);  // 页面加载后 500ms 开始
canvas.addEventListener('click', playRandomMotion);    // 点击切换
canvas.addEventListener('touchstart', ...);              // 手机触摸切换
```

- 想去掉点击切换：删掉 `canvas.addEventListener('click', ...)` 两行
- 想固定某个动作：把 `playRandomMotion` 里的随机逻辑改成固定索引

---

## 七、表情系统

当前未启用表情随机切换。如需添加，在 `playRandomMotion()` 里插入：

```js
// 随机表情（需要先在 model3.json 的 Expressions 里确认有哪些）
const expressions = ['e_idle', 'e_kaixin', 'e_nanguo', 'e_shengqi', 'e_yansu', 'e_chijing'];
const exp = expressions[Math.floor(Math.random() * expressions.length)];
model.expression(exp);
```

---

## 八、如何添加新皮肤

1. 下载皮肤到 `static/live2d/新目录名/`
2. 修改模型路径：`'/live2d/新目录名/xxx.model3.json'`
3. 查看该皮肤的 `.model3.json`，提取所有动作名
4. 更新 `allMotions` 数组
5. 刷新测试

---

## 九、故障排查

| 现象 | 可能原因 |
|------|---------|
| 模型不显示 | 检查浏览器 Console 有无报错；模型路径是否正确 |
| 只显示一半 | `model.y` 位置不对，模型被裁切 |
| 模型拉伸变形 | CSS 宽高比和 Canvas 宽高比不匹配 |
| 白色方块 | 特效贴图兼容问题，需手动修复对应 PNG |
| 点击没反应 | `pointer-events: auto` 是否被改成了 `none` |
| 动作不播放 | CDN 是否加载成功（看 Console 有无 CDN 404） |
| 手机端还显示 | `@media` 断点是否正确，清浏览器缓存 |
