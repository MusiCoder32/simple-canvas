# simple-canvas

一个循序渐进的 Canvas 2D 教学项目（按 leason1~leason7 组织）。每一课都有：
- `index.html`：可直接跑的示例代码（大量注释）
- `readme.md`：本课知识点总结与练习建议

## 运行方式（推荐）

在仓库根目录启动一个静态服务器，然后用浏览器打开对应课的页面：

```bash
python3 -m http.server 5173
```

然后访问：
- http://localhost:5173/leason1/
- http://localhost:5173/leason2/
- ...

说明：使用本地静态服务器可以避免 `file://` 带来的资源加载与安全策略差异（尤其是图片与导出）。

## 课程目录

| 课时 | 主题 | 入口 |
|---|---|---|
| Leason 1 | API 基础：路径/文本/图片/变换/save-restore | `./leason1/index.html` / `./leason1/readme.md` |
| Leason 2 | 动画：rAF + 时间步进（fixed timestep） | `./leason2/index.html` / `./leason2/readme.md` |
| Leason 3 | DPR 适配：高清画布与坐标映射 | `./leason3/index.html` / `./leason3/readme.md` |
| Leason 4 | 命中测试：Path2D + isPointInPath | `./leason4/index.html` / `./leason4/readme.md` |
| Leason 5 | tainted canvas：跨域与 CORS | `./leason5/index.html` / `./leason5/readme.md` |
| Leason 6 | 性能优化：离屏缓存与 OffscreenCanvas 思路 | `./leason6/index.html` / `./leason6/readme.md` |
| Leason 7 | 综合案例：相机/缩放/拖拽/动画/导出 | `./leason7/index.html` / `./leason7/readme.md` |
