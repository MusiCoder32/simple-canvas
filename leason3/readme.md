# Leason 3：DPR 适配（高清 Canvas）

对应示例：index.html

## 1. 为什么会“糊”
- Canvas 有两套尺寸：
  - CSS 尺寸：`canvas.style.width/height`（页面布局尺寸，单位 CSS 像素）
  - 像素缓冲区尺寸：`canvas.width/height`（实际位图像素数量）
- 高分屏：`devicePixelRatio (dpr) > 1`
  - 如果你只设置了 CSS 尺寸，但 backing store 没放大，那么就是“低分辨率位图被拉伸显示”。

## 2. 经典 HiDPI 适配写法
步骤：
1) 设定 CSS 尺寸：
- `canvas.style.width = cssW + 'px'`
- `canvas.style.height = cssH + 'px'`

2) 放大像素缓冲区：
- `canvas.width = Math.round(cssW * dpr)`
- `canvas.height = Math.round(cssH * dpr)`

3) 把绘制坐标系缩放回 CSS 像素：
- 推荐：`ctx.setTransform(dpr, 0, 0, dpr, 0, 0)`

为什么推荐 setTransform：
- `ctx.scale(dpr,dpr)` 会叠加缩放，重复调用容易乘多次。
- 每次修改 `canvas.width/height` 都会重置 ctx 状态（包括 transform），因此需要重新设置。

## 3. 1px 线条对齐（0.5 偏移）
- 在 1px 线条场景里，线落在像素边界可能出现锯齿/模糊。
- 常见技巧：坐标 + 0.5 让线落在像素中心：
  - `moveTo(x + 0.5, 0)` / `lineTo(x + 0.5, h)`

注意：
- 在做了 dpr transform 后，这里的“1px”指的是 1 个 CSS 像素视觉宽度。

## 4. 鼠标坐标映射
事件坐标：
- `clientX/clientY` 是 CSS 像素（相对视口）。
- `getBoundingClientRect()` 返回元素在视口的 CSS 尺寸与位置。

在“已 setTransform(dpr...)”的情况下：
- 你的绘制坐标单位就是 CSS 像素，所以：
  - `x = clientX - rect.left`
  - `y = clientY - rect.top`
  - 就能直接用于绘制/命中测试。

如果你没有把坐标系缩放回 CSS 单位：
- 那你需要将事件点再乘 dpr 映射到 backing store。

## 5. resize 与 dpr 变化
- 窗口大小变化、浏览器缩放、拖到另一个屏幕都可能导致 dpr 变化。
- 应对：重新 setup canvas（会清屏）。

## 6. 本课练习建议
- 练习 1：给画布加一个“缩放比显示 + 切换”按钮（模拟不同 dpr）。
- 练习 2：画一组 1px 线和 2px 线，对比 0.5 偏移的差异。

## 7. 下一课预告
Leason 4 进入交互：命中测试与坐标映射，使用 Path2D + isPointInPath/isPointInStroke。
