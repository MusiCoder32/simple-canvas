# Leason 2：动画（requestAnimationFrame + 时间步进）

对应示例：index.html

## 1. requestAnimationFrame（rAF）基础
- `requestAnimationFrame(cb)`：浏览器在下一次重绘前调用 cb。
- 优点：
  - 更贴合显示器刷新节奏（通常 60/120/144Hz）。
  - 页面不可见时自动降频/暂停，更省电。
  - 回调会给高精度时间戳 `now`（ms）。

常见错误：
- 用 `setInterval` 驱动动画：不同标签页/后台/负载下抖动更明显。

## 2. deltaTime（可变时间步）
- 定义：`dt = (now - lastNow) / 1000`（秒）。
- 典型更新：
  - `v += a * dt`
  - `p += v * dt`

注意：
- dt 可能突然变大（切后台、断点暂停、卡顿），需要“封顶”避免穿透：
  - `dt = Math.min(dt, 0.05)` 之类。

## 3. fixed timestep（固定时间步）
为什么要固定时间步：
- 可变 dt 在帧率不稳时，物理积分误差变大。
- 碰撞检测更容易穿透。
- 同样的参数在不同机器上表现不一致。

核心做法：
- 设定固定步长 `fixedDt`（例如 1/120s）。
- 使用 accumulator 累积真实时间：
  - `acc += frameDt`
  - `while (acc >= fixedDt) { step(fixedDt); acc -= fixedDt }`

插值渲染（概念）：
- `alpha = acc / fixedDt` 可用于“上一状态/下一状态”的插值，让渲染更平滑。

## 4. 暂停/恢复的时间处理
- 从暂停恢复时要重置 `lastNow`，否则 dt 会非常大。

## 5. 本课练习建议
- 练习 1：把小球改成多个小球（数组循环），体验 fixed timestep 的价值。
- 练习 2：加入“水平加速度/风力”，观察不同 dt 策略下稳定性。
- 练习 3：加入简单碰撞（球-球），对比可变 dt 的穿透风险。

## 6. 下一课预告
Leason 3 讲 DPR 适配：让高分屏上 canvas 线条/文字更清晰，并解释 CSS 尺寸 vs 像素缓冲区尺寸。
