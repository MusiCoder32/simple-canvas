# Leason 4：命中测试（Hit Test）+ 坐标映射

对应示例：index.html

## 1. 为什么需要命中测试
- Canvas 画完只剩像素，没有 DOM 节点可点。
- 交互（hover/点击/拖拽/选择）必须自己实现：
  1) 将鼠标坐标映射到画布坐标
  2) 判断该点是否落在某个图形上
  3) 更新对象状态并重绘

## 2. 两类常见命中测试方案
### A. 几何计算（数学 hit test）
- 圆：`(dx^2 + dy^2 <= r^2)`
- 矩形：`x in [l,r] && y in [t,b]`
- 多边形：射线法/环绕数等

优点：快、可控。
缺点：复杂形状写起来麻烦。

### B. 利用 Canvas 自带 API：Path2D + isPointInPath/isPointInStroke
- `const path = new Path2D()` 组装形状
- `ctx.isPointInPath(path, x, y)` 判断填充区域
- `ctx.isPointInStroke(path, x, y)` 判断描边区域

优点：复用绘制路径，写起来更直观。
注意：
- `isPointInStroke` 会受 `ctx.lineWidth` 影响；实际交互中可用更粗的 lineWidth 提升可点性。

## 3. “保留模式”数据结构
- Canvas 不保存对象，你要自己保存：
  - `objects = [{id, x, y, ...}, ...]`
- 每次状态变化（hover/drag）后：清屏 → 遍历 objects 重绘。

## 4. Z 轴（层级）处理
- 通常“后画的在上面”。
- 交互上常见需求：点击某个对象后置顶。
- 示例做法：把命中的对象从数组中移除，再 push 到末尾。

## 5. 拖拽实现要点
- 命中后记录 offset：
  - `offset = mouse - object`
  - 拖拽时：`object = mouse - offset`
- 鼠标抬起时清空 dragging 状态。

## 6. DPR 与坐标映射
- 本课示例已做 HiDPI：ctx 坐标单位 = CSS 像素。
- 因此事件点映射很直接：
  - `x = clientX - rect.left`
  - `y = clientY - rect.top`

## 7. 本课练习建议
- 练习 1：给对象加“旋转”属性，并尝试命中测试是否仍准确（提示：Path2D+变换或反变换）。
- 练习 2：实现“框选”（拖拽一个矩形选择多个对象）。
- 练习 3：加入“吸附网格”（拖动时位置自动对齐）。

## 8. 下一课预告
Leason 5 讲 tainted canvas（跨域污染）：为什么画了跨域图就无法 toDataURL/getImageData，以及如何正确处理 CORS。
