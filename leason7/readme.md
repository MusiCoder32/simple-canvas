# Leason 7：综合案例（相机/缩放/命中/动画/导出）

对应示例：index.html

## 1. 本课目标
把前面课程的关键点串起来，形成一个“可交互场景”的基础架构：
- 世界坐标（World）与屏幕坐标（Screen）分离
- 相机（Camera）：平移 + 缩放
- 命中测试：根据世界坐标判断点击对象
- 动画：fixed timestep 推进运动
- 导出：toDataURL 生成 PNG（注意 tainted canvas）

## 2. 坐标系统：Screen vs World
- Screen（屏幕坐标）：鼠标事件坐标，单位 CSS 像素，范围大致是 (0..W, 0..H)
- World（世界坐标）：你要管理对象的位置与运动（可大可小，独立于屏幕）

核心函数：
- `screenToWorld(p)`：把鼠标点从屏幕映射到世界

## 3. 相机变换（Camera Transform）
渲染世界时通常做：
1) 平移到屏幕中心
2) 乘以 zoom
3) 平移 -camera（把相机理解为“世界反向移动”）

注意点：
- 变换影响命中测试：你必须在世界坐标里做 hit test。
- 变换影响线宽：如果想让描边“视觉恒定”，需要在世界坐标里用 `lineWidth / zoom`。

## 4. 缩放以鼠标为中心（Zoom at Cursor）
目标：滚轮缩放时，鼠标指着的世界点保持不动。

做法：
- 缩放前：`before = screenToWorld(mouse)`
- 更新 zoom
- 缩放后：`after = screenToWorld(mouse)`
- 用差值修正相机：
  - `camera.x += (before.x - after.x)`
  - `camera.y += (before.y - after.y)`

## 5. 平移（Pan）与拖拽对象（Drag）
- 拖拽对象：
  - mousedown 命中对象 → 记录起始世界点与对象位置
  - mousemove → 更新对象位置
- 平移相机：
  - 记录起始屏幕点与相机位置
  - 位移需要除以 zoom：屏幕移动 dx 对应世界移动 dx/zoom

## 6. 动画时间步（fixed timestep）
- 使用 accumulator 追赶真实时间。
- 帧率低时单帧多次 step，保持物理稳定。

## 7. 性能优化点（本案例里用到的）
- 网格使用 pattern（由离屏 tile 生成），避免每帧大量 lineTo。

## 8. 导出与 tainted canvas
- `canvas.toDataURL()` 仅在画布未被跨域资源污染时可用。
- 如果你往这个案例里加入跨域图片纹理，导出可能会失败（参考 Leason 5）。

## 9. 本课扩展练习
- 练习 1：加入矩形/多边形对象，并实现命中测试。
- 练习 2：实现多选与框选（需要 world-space 的 selection box）。
- 练习 3：加入“相机惯性滚动/平滑缩放”（用 rAF + 插值）。
- 练习 4：加入对象的旋转/缩放，并处理命中测试（可用反变换或 Path2D+变换）。
