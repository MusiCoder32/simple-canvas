# Leason 5：Tainted Canvas（跨域污染 / 安全限制）

对应示例：index.html

## 1. 什么是 tainted canvas
当你把“没有正确 CORS 授权的跨域资源”绘制到画布后，浏览器会把画布标记为 tainted（被污染）。

一旦 tainted：
- 仍然可以继续绘制（显示没问题）
- 但禁止读取像素/导出：
  - `canvas.toDataURL()` / `canvas.toBlob()`
  - `ctx.getImageData()`
  - 典型报错：`SecurityError: Tainted canvases may not be exported.`

核心原因：
- 防止你通过像素读取窃取跨域资源（例如需要登录的图片/验证码/用户头像等）。

## 2. 触发污染的典型场景
- `img.src = 'https://other-domain.com/a.png'`（跨域）
- 服务器未返回允许的 CORS 响应头
- 或 `crossOrigin` 属性设置不正确（或设置时机不对）

注意：
- `img.crossOrigin = 'anonymous'` 必须在 `img.src = ...` 之前设置。

## 3. 如何“既能画跨域图，又能读像素”
需要同时满足：
1) 前端：
- `img.crossOrigin = 'anonymous'`

2) 资源服务器：
- 返回 `Access-Control-Allow-Origin: *` 或者明确允许你的源
- 如果用凭证（cookie）：需要 `use-credentials` + `Access-Control-Allow-Credentials: true` 且不能用 `*`

## 4. 常见解决方案
- 方案 A：同源托管资源（最省心）
- 方案 B：服务器正确配置 CORS
- 方案 C：后端代理转发（你的后端去请求资源并以同源方式返回）
- 方案 D：放弃像素读取（只展示，不导出/不做像素级处理）

## 5. 你应该记住的“判断标准”
- 只要你要做这些事情，就必须非常关注 CORS/taint：
  - 截图导出、生成海报
  - 取样、滤镜、像素级特效
  - OCR、识别、取色器

## 6. 本课练习建议
- 练习 1：用你自己的服务器托管一张图片，对比同源 vs 跨域。
- 练习 2：把 `crossOrigin` 放到 `src` 之后设置，观察为什么无效。
- 练习 3：尝试 `toBlob()` 导出并下载（与 toDataURL 类似的限制）。

## 7. 下一课预告
Leason 6 讲性能优化与离屏：减少每帧绘制开销、用精灵缓存/离屏 canvas、以及 OffscreenCanvas 的思路。
