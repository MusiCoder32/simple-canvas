# Leason 1：Canvas API 基础（路径 / 文本 / 图片 / 变换 / save-restore）

对应示例：index.html

## 1. Canvas 的核心对象与心智模型
- `canvas.getContext('2d')` 获取 2D 上下文（ctx）。
- Canvas 是“立即模式（Immediate Mode）”：
  - 你画完之后，画布里只剩像素，不再保留“对象”。
  - 交互/命中测试/选择等能力需要你自己维护数据结构（后续课程会讲）。
- 绝大多数 API 本质上是：
  - 改 ctx 状态（颜色、线宽、字体、变换……）
  - 或者改“当前路径”（Path）
  - 最终用 `fill()` / `stroke()` / `drawImage()` 把像素写进画布。

## 2. 路径（Path）基础
### beginPath / moveTo / lineTo / closePath
- `beginPath()`：开始一个新的路径（清空“当前路径”）。
- `moveTo(x,y)`：移动画笔，不画线。
- `lineTo(x,y)`：从上一个点画线到当前点。
- `closePath()`：把当前点连接回起点。

### fill / stroke 的区别
- `fill()` 填充路径内部区域（按填充规则，默认 non-zero）。
- `stroke()` 描边路径轮廓（线宽、线帽、线连接都受状态影响）。
- 常见顺序：先 `fill()` 再 `stroke()`，视觉更自然。

### rect / fillRect / strokeRect
- `rect()` 会把矩形加入“当前路径”，需要 `beginPath()`。
- `fillRect/strokeRect` 是便捷的“立即绘制”API，不走路径。

### arc / 曲线
- `arc(x,y,r,start,end)` 画圆弧。
- `quadraticCurveTo` 二次贝塞尔（1 个控制点）。
- `bezierCurveTo` 三次贝塞尔（2 个控制点）。

## 3. 文本绘制
- `ctx.font`：语法与 CSS 类似（可设置粗细、字号、字体族）。
- `textAlign`：left/center/right（控制 x 对齐）。
- `textBaseline`：alphabetic/top/middle/bottom（控制 y 基线）。
- `measureText()`：获取文本宽度等指标。

常见坑：
- font 没设置时使用默认字体；字号变化会影响布局。
- textBaseline 默认是 `alphabetic`，对齐时容易产生“y 不准”的错觉。

## 4. 图片绘制与加载
- `drawImage()` 有三种常见重载：
  - `drawImage(img, dx, dy)`
  - `drawImage(img, dx, dy, dw, dh)`（缩放）
  - `drawImage(img, sx, sy, sw, sh, dx, dy, dw, dh)`（裁剪 + 缩放）

加载建议：
- `await img.decode()`（现代浏览器）或 `img.onload`。
- 运行建议使用本地静态服务器（避免 file:// 导致资源/安全策略问题）。

## 5. 变换（Transform）与状态栈
### 变换是什么
- ctx 内部维护“当前变换矩阵（CTM）”。
- 调用 `translate/rotate/scale` 会影响后续所有绘制（含线宽、文字、命中测试）。

### save / restore
- `save()`：把当前状态（含样式 + 变换 + 裁剪等）压栈。
- `restore()`：弹栈，恢复到之前状态。

实战建议：
- 以“对象绘制函数”为单位：
  - 进入对象 `save()` → 设置 transform/样式 → 绘制 → `restore()`
  - 避免状态泄漏导致难排查的视觉 bug。

## 6. 本课练习建议
- 练习 1：把示例里的三角形改成五角星（路径 + closePath）。
- 练习 2：尝试不同 `lineJoin/lineCap` 的视觉差异。
- 练习 3：把图片绘制改成裁剪模式（第三种 drawImage 重载）。

## 7. 下一课预告
Leason 2 会引入动画：requestAnimationFrame + deltaTime + fixed timestep，让动画在不同刷新率下表现一致。
