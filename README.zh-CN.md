# Road Config Tool

语言：[English](README.md) | 中文

一个纯前端、零依赖的 **road config 可视化工具**，用于在浏览器中直观查看和检查
[OpenVTER](https://github.com/XinkaiJi/OpenVTER) 项目所使用的 road config 标注文件。

载入 road config（`.json`）后，工具会在底图上渲染出车道编号、车道线、行车线里程、
施工区、坐标轴等信息，方便核对标注是否正确，并可导出为 PNG 图。

项目包含两个页面：

- **Viewer**（`index.html`）- 载入并可视化已有的 road config
- **Editor**（`editor.html`）- 在底图上直接绘制 / 编辑标注，并导出符合规范的 road config JSON

## 在线访问

- Viewer: https://xinkaiji.github.io/road_config_website/
- Editor: https://xinkaiji.github.io/road_config_website/editor.html

无需安装、无需后端，所有解析与渲染都在本地浏览器中完成，标注文件不会上传到任何服务器。

## 这是什么

[OpenVTER](https://github.com/XinkaiJi/OpenVTER) 使用一份 road config JSON 来描述道路的
几何与语义信息（车道、车道线、行车里程线、坐标轴、施工区等）。该 JSON 通常由标注工具
（labelme 风格的 `shapes` 结构）生成。本工具复刻了 OpenVTER 中 `visualize_config` 的绘制逻辑，
让你不必运行 Python 脚本即可快速预览标注结果。

## Viewer 功能

- **载入方式**
  - 点击“打开 road config”选择 `.json`
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
  - 一键“适应窗口”/“重置视图”
- **参数调节**：底图亮度、车道填充透明度、字号、线宽、里程间隔
- **导出**：将当前渲染结果保存为 PNG

## Editor 功能

`editor.html` 用于从零创建或修改 road config，导出的 JSON 为 labelme 风格（含
`shape_type`、`group_id` 等字段，并内嵌底图），可直接在 Viewer 或 OpenVTER 中使用。

- **载入**：打开底图图片；或载入已有 `.json` 继续编辑（图片 / JSON 均支持拖拽）
- **按规范标注**：从下拉菜单选择标注类型，编辑器自动生成正确的 `label` 与几何类型：
  `road` / `fp`（多边形或矩形）/ `length_X` / `x` / `y` / `lane_N` / `laneline_N` /
  `drivingline_{name}_{pos}_region|line|point_{base}|connect_{id1}_{id2}` / `workzone`；
  `lane_N`、`laneline_N` 编号会在每次完成后自动递增
- **自定义标注**：选“自定义”后自选形状（点 / 线 / 矩形 / 多边形 / 折线），
  绘制完成后输入任意 `label`，类似 labelme 的自由标注
- **绘制模式**（B）：左键打点，双击 / Enter 完成多边形、折线，Backspace 撤销上一点，
  Esc 取消；线段点两下自动完成；矩形按住拖动；点单击放置
- **选择 / 编辑模式**（V）：点击选中、拖动顶点微调、拖动整体移动、`Delete` 删除、
  双击列表项重命名 label
- **视图**：滚轮缩放、右键 / 中键平移、底图亮度调节
- **导出**：road config `.json` 或标注后的 PNG

## road config 格式约定

JSON 顶层包含 `imageWidth`、`imageHeight`、可选的 `imageData`（base64 底图），
以及一个 `shapes` 数组。每个 shape 含 `label` 与 `points`，按 `label` 前缀解析：

| label 前缀         | 含义                                  |
| ------------------ | ------------------------------------- |
| `lane_N`           | 第 N 条车道的闭合区域                 |
| `laneline_N`       | 第 N 条车道线（相邻编号配对构成车道） |
| `drivingline*`     | 行车线（用于计算里程）                |
| `length_<米数>`    | 标定尺度：该线段对应的真实长度（米）  |
| `x` / `y`          | 坐标轴方向                            |
| `workzone*`        | 施工区                                |
| `fp`               | 稳定 / 检测区域                       |
| `road`             | 路面区域                              |
| `int*`             | 路口区域                              |

尺度（米/像素）由 `length_*` 标注自动推算，用于里程与坐标轴的真实单位换算。

## 本地使用

纯静态页面，无构建步骤。直接用浏览器打开 `index.html` 即可：

```bash
git clone https://github.com/XinkaiJi/road_config_website.git
cd road_config_website
open index.html        # macOS；或在浏览器中直接打开该文件
```

## 部署

整站通过 GitHub Pages 托管。在仓库 **Settings -> Pages** 中将来源设为 `main` 分支根目录即可。

## 相关项目

- [OpenVTER](https://github.com/XinkaiJi/OpenVTER) - 本工具所服务的车辆轨迹提取项目
