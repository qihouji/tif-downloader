# TIF 地图下载工具

一个基于 Tauri + Rust 的高性能地图瓦片下载与拼接桌面工具。

![Rust](https://img.shields.io/badge/rust-1.70+-orange.svg)
![Tauri](https://img.shields.io/badge/tauri-2.0-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

## ✨ 功能特性

### 🗳️ 地图下载
- **多图源支持**：OSM、ArcGIS 卫星/地形/街道、天地图、Carto、Google Maps、高德地图/卫星、OpenTopoMap 等
- **自定义图源**：支持添加任意 `{z}/{x}/{y}` 格式的瓦片图源
- **多任务并行下载**：支持同时创建多个下载任务，实时进度显示
- **多格式导出**：GeoTIFF（带地理坐标 + LZW 压缩）、PNG、JPEG
- **按边界裁剪**：支持按多边形边界裁剪，透明背景
- **可调并发**：支持 10-100 并发下载，快速高效
- **下载历史**：自动记录每次下载，支持快速打开文件夹

### 📍 区域选择
- **地名搜索**：输入地名快速定位
- **行政区划**：中国省/市/区县三级联动选择
- **自定义边界**：
  - 上传 GeoJSON (.json/.geojson)
  - 上传 Shapefile (.shp + .shx + .dbf)
  - 地图上手动绘制矩形或多边形

### 📦 矢量数据（高级功能）
- OSM 数据下载（道路、建筑、水系等）
- 行政区划边界下载
- 本地矢量文件加载预览

## 🚀 快速开始

### 环境要求
- [Rust](https://www.rust-lang.org/tools/install) 1.70+
- [Node.js](https://nodejs.org/) (可选，仅开发时需要)
- 系统依赖（Tauri 需要 WebView + GTK 相关开发库，见下方「本地构建完整环境配置」）

### 开发运行

```bash
cd src-tauri
cargo tauri dev
```

### 构建发布

```bash
cd src-tauri
cargo tauri build
```

构建完成后，安装包位于 `src-tauri/target/release/bundle/` 目录。

## 🧰 本地构建完整环境配置

> 如果你遇到 `glib-2.0`、`webkit2gtk`、`pkg-config` 相关报错，基本都是系统依赖未安装导致。

### 1) 安装 Rust 与 Tauri CLI（所有平台通用）

```bash
# 安装 Rust（已安装可跳过）
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source "$HOME/.cargo/env"

# 安装 tauri-cli（v2）
cargo install tauri-cli --version "^2.0.0"
```

### 2) 安装 Node.js（建议 LTS）

- 到 <https://nodejs.org/> 安装 LTS 版本，安装完成后确认：

```bash
node -v
npm -v
```

### 3) Linux 依赖（Ubuntu / Debian）

```bash
sudo apt update
sudo apt install -y \
  pkg-config \
  build-essential \
  libgtk-3-dev \
  libwebkit2gtk-4.1-dev \
  libglib2.0-dev \
  libjavascriptcoregtk-4.1-dev \
  libsoup-3.0-dev \
  libayatana-appindicator3-dev \
  librsvg2-dev \
  patchelf
```

> 如果你的发行版没有 `4.1` 包名，可尝试 `libwebkit2gtk-4.0-dev` 和 `libjavascriptcoregtk-4.0-dev`。

### 4) macOS 依赖

1. 安装 Xcode Command Line Tools：

```bash
xcode-select --install
```

2. 安装 Homebrew（如未安装）并安装基础工具：

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install pkg-config
```

### 5) Windows 依赖

1. 安装 **Microsoft Visual Studio C++ Build Tools**（勾选 “Desktop development with C++”）。
2. 安装 **WebView2 Runtime**（大多数 Windows 11 已预装）。
3. 安装 Rust（`stable-x86_64-pc-windows-msvc` 工具链）。

可在 PowerShell 中验证：

```powershell
rustc -V
cargo -V
```

### 6) 拉起开发环境并验证

```bash
# 进入 Tauri 工程目录
cd src-tauri

# 先做一次编译检查（推荐）
cargo check

# 启动开发模式
cargo tauri dev
```

### 7) 打包发布

```bash
cd src-tauri
cargo tauri build
```

产物目录：

- Linux/macOS/Windows 安装包与可执行文件：`src-tauri/target/release/bundle/`

## 🏗️ 项目结构

```
tif-downloader/
├── src-tauri/          # Rust 后端 (Tauri)
│   ├── src/
│   │   ├── lib.rs        # 应用入口
│   │   ├── commands.rs   # Tauri 命令
│   │   ├── config.rs     # 配置和内置图源
│   │   ├── tile.rs       # 瓦片坐标计算
│   │   ├── downloader.rs # 异步并发下载器
│   │   ├── merger.rs     # 瓦片拼接与裁剪
│   │   ├── exporter.rs   # 图像导出 (GeoTIFF/PNG/JPEG)
│   │   ├── admin.rs      # 行政区划数据
│   │   ├── task.rs       # 多任务管理
│   │   ├── history.rs    # 下载历史记录
│   │   └── settings.rs   # 用户设置持久化
│   └── Cargo.toml
├── static/             # 前端静态文件
│   ├── index.html
│   ├── css/style.css
│   ├── lib/            # 本地 Leaflet 库
│   └── js/
│       ├── api.js      # Tauri API 适配层
│       └── app.js      # 前端主逻辑
└── docs/               # 文档
```

## ⚙️ 配置说明

### 天地图 Token
内置了默认 Token，建议在「高级选项」中配置您自己的 Token 以获得更好的服务。

### 代理设置
访问 Google 等国外图源时，请在「高级选项」中启用代理并配置正确的代理地址。

## 📝 注意事项

- 下载的地图数据版权归原图商所有，请遵守相关使用条款
- 大范围高缩放级别下载可能需要较长时间，请耐心等待
- 建议根据网络状况调整并发数（网络不稳定时降低并发数）

## 📄 许可证

MIT License

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=gaopengbin/tif-downloader&type=Date)](https://star-history.com/#gaopengbin/tif-downloader&Date)
