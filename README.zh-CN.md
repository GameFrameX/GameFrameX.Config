<div align="center">

![GameFrameX Logo](https://download.alianblank.com/gameframex/gameframex_logo_320.png)

# GameFrameX.Config

[![Version](https://img.shields.io/github/v/release/GameFrameX/GameFrameX.Config?label=version&color=green)](https://github.com/GameFrameX/GameFrameX.Config/releases)
[![License](https://img.shields.io/badge/license-Apache%202.0-orange.svg)](LICENSE.md)
[![Documentation](https://img.shields.io/badge/docs-gameframex-brightgreen.svg)](https://gameframex.doc.alianblank.com)

**独立游戏前后端一体化解决方案 · 独立游戏开发者的圆梦大使**

[📖 文档](https://gameframex.doc.alianblank.com/zh-CN) • [🚀 快速开始](#快速开始) • [💬 QQ群: 870596322](https://qm.qq.com/q/IrE4RSmqgY)

---

🌐 **语言**: [English](README.md) | **简体中文** | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [한국어](README.ko.md)

---

</div>

## 项目简介

**GameFrameX.Config** 是 GameFrameX 框架的统一游戏配置中心，基于 [Luban](https://github.com/GameFrameX/luban) 构建。你只需在一套 Excel 源文件中维护全部游戏配置和多语言文本，即可一次性为**客户端和服务端**生成代码与数据。

- **一源双端** —— 同一份 Excel 定义同时产出客户端（Unity）与服务端（.NET）输出。
- **内置本地化（L10N）** —— 通过 `gameframex` L10N provider 将多语言文本与配置统一管理，并随客户端数据一并导出。
- **跨端类型映射** —— `vec2` / `vec3` / `vec4` 在客户端映射为 `UnityEngine.Vector2/3/4`，在服务端映射为 `System.Numerics.Vector2/3/4`。
- **JSON 与 Bin 双格式** —— 可按目标自由选择；同时提供 Windows（`.bat`）和 macOS/Linux（`.sh`）脚本。

## 功能特性

| 能力 | 说明 |
|------|------|
| 双端生成 | 一份 Excel 源 → 客户端（`cs-simple-json`）+ 服务端（`cs-dotnet-json` / `cs-bin`） |
| 本地化 | `gameframex` L10N provider；基于 key 的文本文件位于 `Excels/Local/`，随客户端一并导出 |
| 类型桥接 | `vec2/3/4` 自动映射到各端对应的向量类型 |
| 多种格式 | JSON（可读）与 Bin（紧凑、带校验） |
| 跨平台脚本 | Windows 使用 `.bat`，macOS/Linux 使用 `.sh` |

## 目录结构

```
Config/
├── Defines/                  # 类型定义（内置 bean，如 vec2/vec3/vec4）
│   └── builtin.xml
├── Excels/                   # Excel 源文件
│   ├── __tables__.xlsx       # 数据表注册表
│   ├── __beans__.xlsx        # bean（结构体）定义
│   ├── __enums__.xlsx        # 枚举定义
│   ├── Tables/               # 游戏配置表（道具、成就等）
│   └── Local/                # 本地化文本（key → 翻译）
├── Tools/                    # Luban 运行时（Luban.dll + 依赖 + 模板）
├── luban.conf                # Luban 配置（目标、分组、命令）
├── gen-client-json.sh/.bat   # 生成客户端 JSON 输出
├── gen-client-bin.sh/.bat    # 生成客户端 Bin 输出
├── gen-server-json.sh/.bat   # 生成服务端 JSON 输出
└── gen-server-bin.sh/.bat    # 生成服务端 Bin 输出
```

## 快速开始

### 前置要求

- 较新版本的 **.NET SDK** —— Luban 以 `dotnet` 工具运行（`dotnet Tools/Luban.dll`）。
- 在 `Config` 同级目录检出 `Unity` 和 `Server` 仓库。生成脚本会写入 `../Unity/Assets/...` 和 `../Server/GameFrameX.Config/...`。

### 生成客户端配置（JSON）

在 `Config` 目录下执行：

```bash
# macOS / Linux
sh gen-client-json.sh

# Windows
gen-client-json.bat
```

导出位置：

- **数据** → `../Unity/Assets/Bundles/Config`
- **代码** → `../Unity/Assets/Hotfix/Config/Generate`

### 生成服务端配置（Bin）

```bash
# macOS / Linux
sh gen-server-bin.sh

# Windows
gen-server-bin.bat
```

导出位置：

- **数据** → `../Server/GameFrameX.Config/Json`
- **代码** → `../Server/GameFrameX.Config/Config`

> 实际的输出路径和命令定义在 `luban.conf` 中（`UNITY_ASSETS_PATH`、`SERVER_PATH`、`commands`）。请根据你的目录布局进行调整。

## 配置表

仓库内置了用于演示工作流的示例表：

| 表 | 文件 | 说明 |
|----|------|------|
| 成就 | `Excels/Tables/C-AchievementConfig-成就表.xlsx` | 成就定义 |
| 道具 | `Excels/Tables/D-ItemConfig-道具表-道具-1001.xlsx` | 道具定义 |
| 声音 | `Excels/Tables/S-SoundsConfig-声音表.xlsx` | 声音定义 |
| 本地化 — 成就 | `Excels/Local/L-Localization-成就.xlsx` | 成就文本翻译 |
| 本地化 — 文本 | `Excels/Local/L-Localization-文本.xlsx` | 通用文本翻译 |
| 本地化 — UI | `Excels/Local/L-Localization-UI.xlsx` | UI 文本翻译 |

新增数据表时，在 `Excels/__tables__.xlsx` 中注册，并在 `Excels/__beans__.xlsx` 中定义对应的 bean。

## 导出目标

`luban.conf` 中配置了三个目标：

| 目标 | 分组 | 顶层模块 | 代码目标 | 用途 |
|------|------|----------|----------|------|
| `server` | `s` | `GameFrameX.Config` | `cs-dotnet-json` / `cs-bin` | 服务端（.NET） |
| `client` | `c` | `Hotfix.Config` | `cs-simple-json` / `cs-bin` | 客户端（Unity） |
| `all` | `c`, `s` | `cfg` | `luban`（默认） | 双端同时 |

## 系统要求

- **.NET SDK** —— 用于运行 `Luban.dll`。
- **Excel**（或兼容编辑器）—— 用于编写 `.xlsx` 源文件。
- **操作系统** —— Windows、macOS 或 Linux。

## 开源协议

本项目基于 [Apache License 2.0](LICENSE.md) 协议开源。

## 相关链接

- [文档](https://gameframex.doc.alianblank.com)
- [GitHub 仓库](https://github.com/GameFrameX/GameFrameX.Config)
- [问题追踪](https://github.com/GameFrameX/GameFrameX.Config/issues)
- [Luban（GameFrameX 分支）](https://github.com/GameFrameX/luban)
- [Luban（上游）](https://github.com/focus-creative-games/luban)
