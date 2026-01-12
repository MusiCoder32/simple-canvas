# Leason 6：性能优化与离屏渲染（缓存 / OffscreenCanvas）

对应示例：index.html

## 1. Canvas 性能主要花在哪
常见瓶颈：
- 每帧 draw 调用次数太多（大量 beginPath/arc/fill）
- 频繁修改 ctx 状态（fillStyle/font/globalAlpha/composite 等）
- 复杂路径/渐变每帧重复计算
- 分配大量临时对象导致 GC 抖动

建议习惯：
- 用 DevTools 的 Performance/Rendering 面板定位“慢在哪”。

## 2. “离屏缓存”的基本思路
把“昂贵但可复用”的绘制结果预先画到离屏 canvas：
- 主线程每帧只做 `drawImage`（更像贴图/精灵渲染）

典型适用：
- 粒子/火花/光晕
- 重复纹理（pattern）
- 复杂矢量形状（logo、图标）

## 3. OffscreenCanvas 与降级
- `OffscreenCanvas`：可以在 Worker 里渲染，减少主线程卡顿。
- 兼容性不一：需要降级到普通 `document.createElement('canvas')`。

两种常见策略：
- 主线程离屏：只做缓存（本课示例）
- Worker 离屏：主线程只负责显示与交互（更高级的架构）

## 4. 减少 draw 调用 & 状态切换
实战 checklist：
- 尽量批处理：同样样式的图形放在一起画
- 少改 ctx 属性：尤其是 font、shadow、composite
- 能用 pattern / sprite 就不要每帧重建路径

## 5. 内存与 GC
- 大量粒子如果用对象数组：每帧创建/销毁对象会触发 GC。
- 优化方向：
  - 对象池（复用对象）
  - TypedArray（结构化数据，减少分配）

## 6. 本课练习建议
- 练习 1：把粒子数据改成 TypedArray（x,y,vx,vy,r 分离存储）。
- 练习 2：增加一个“颜色渐变精灵”与“纯色圆”对比 FPS。
- 练习 3：实现“脏矩形”策略：只清除/重绘发生变化的区域（需要更复杂的管理）。

## 7. 下一课预告
Leason 7 综合案例会把：相机变换（平移/缩放）、命中测试、动画时间步、导出截图等串起来做一个小型场景。
