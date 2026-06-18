# Road Config Viewer

一个纯前端、零依赖的 **road config 可视化工具**，用于在浏览器中直观查看和检查
[OpenVTER](https://github.com/XinkaiJi/OpenVTER) 项目所使用的 road config 标注文件。

载入 road config（`.json`）后，工具会在底图上渲染出车道编号、车道线、行车线里程、
施工区、坐标轴等信息，方便核对标注是否正确，并可导出为 PNG 图。

## 在线访问

👉 https://xinkaiji.github.io/road_config_website/

无需安装、无需后端，所有解析与渲染都在本地浏览器中完成，标注文件不会上传到任何服务器。

## 这是什么

[OpenVTER](https://github.com/XinkaiJi/OpenVTER) 使用一份 road config JSON 来描述道路的
几何与语义信息（车道、车道线、行车里程线、坐标轴、施工区等）。该 JSON 通常由标注工具
（labelme 风格的 `shapes` 结构）生成。本工具复刻了 OpenVTER 中 `visualize_config` 的绘制逻辑，
让你不必运行 Python 脚本即可快速预览标注结果。

## 功能

- **载入方式**
  - 点击「打开 road config」选择 `.json`
  - 直接把 `.json` 文件拖拽到画布区
  - 若 JSON 内含 `imageData`（base64 内嵌底图）则自动使用；否则可单独选择底图图片
- **可视化图层**（均可单独开关，并显示数量统计）
  - 底图（亮度可调）
  - 车道：由相邻 `laneline` 配对构造车道多边形 + `lane_N` 闭合区域，自动标注车道编号
  - 车道线（`laneline`）
  - 行车线 + 里程：沿 `drivingline` 按设定间隔标注里程（米）
  - 坐标轴（`x` / `y`），带刻度与米标注
  - 施工区（`workzone`），橙色虚线高亮
  - 检测 / 稳定 / 路口区域（`fp` / `road` / `int`）
  - 文字标签开关
- **交互**
  - 滚轮缩放（以光标为锚点）
  - 按住右键 / 中键拖动平移
  - 一键「适应窗口」/「重置视图」
- **参数调节**：底图亮度、车道填充透明度、字号、线宽、里程间隔
- **导出**：将当前渲染结果保存为 PNG

## road config 格式约定

JSON 顶层包含 `imageWidth`、`imageHeight`、可选的 `imageData`（base64 底图），
以及一个 `shapes` 数组。每个 shape 含 `label` 与 `points`，按 `label` 前缀解析：

| label 前缀          | 含义                                    |
| ------------------- | --------------------------------------- |
| `lane_N`            | 第 N 条车道的闭合区域                    |
| `laneline_N`        | 第 N 条车道线（相邻编号配对构成车道）    |
| `drivingline*`      | 行车线（用于计算里程）                   |
| `length_<米数>`     | 标定尺度：该线段对应的真实长度（米）     |
| `x` / `y`           | 坐标轴方向                              |
| `workzone*`         | 施工区                                  |
| `fp`                | 稳定 / 检测区域                         |
| `road`              | 路面区域                                |
| `int*`              | 路口区域                                |

尺度（米/像素）由 `length_*` 标注自动推算，用于里程与坐标轴的真实单位换算。

## 本地使用

纯静态页面，无构建步骤。直接用浏览器打开 `index.html` 即可：

```bash
git clone https://github.com/XinkaiJi/road_config_website.git
cd road_config_website
open index.html        # macOS；或在浏览器中直接打开该文件
```

## 部署

整站为单个 `index.html`，已通过 GitHub Pages 托管。在仓库 **Settings → Pages**
中将来源设为 `main` 分支根目录即可。

## 相关项目

- [OpenVTER](https://github.com/XinkaiJi/OpenVTER) — 本工具所服务的车辆轨迹提取项目
