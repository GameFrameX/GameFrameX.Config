<div align="center">

![GameFrameX Logo](https://download.alianblank.com/gameframex/gameframex_logo_320.png)

# GameFrameX.Config

[![Version](https://img.shields.io/github/v/release/GameFrameX/GameFrameX.Config?label=version&color=green)](https://github.com/GameFrameX/GameFrameX.Config/releases)
[![License](https://img.shields.io/badge/license-Apache%202.0-orange.svg)](LICENSE.md)
[![Documentation](https://img.shields.io/badge/docs-gameframex-brightgreen.svg)](https://gameframex.doc.alianblank.com)

**All-in-One Solution for Indie Game Development · Empowering Indie Developers' Dreams**

[📖 Documentation](https://gameframex.doc.alianblank.com/) • [🚀 Quick Start](#quick-start) • [💬 QQ Group: 870596322](https://qm.qq.com/q/IrE4RSmqgY)

---

🌐 **Language**: **English** | [简体中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | [한국어](README.ko.md)

---

</div>

## Project Overview

**GameFrameX.Config** is the unified game configuration hub of the GameFrameX framework, built on top of [Luban](https://github.com/focus-creative-games/luban). You author all game configuration and localization text in a single set of Excel source files, then generate code and data for **both client and server** in one pass.

- **One source, dual export** — the same Excel definitions produce client (Unity) and server (.NET) outputs.
- **Built-in localization (L10N)** — multilingual text managed alongside config via the `gameframex` L10N provider, exported together with the client data.
- **Cross-platform types** — `vec2` / `vec3` / `vec4` map to `UnityEngine.Vector2/3/4` on the client and `System.Numerics.Vector2/3/4` on the server.
- **JSON & Bin formats** — choose per target; scripts are provided for both Windows (`.bat`) and macOS/Linux (`.sh`).

## Features

| Capability | Description |
|------------|-------------|
| Dual-end generation | One Excel source → client (`cs-simple-json`) + server (`cs-dotnet-json` / `cs-bin`) |
| Localization | `gameframex` L10N provider; key-based text files under `Excels/Local/`, exported with the client |
| Type bridging | `vec2/3/4` automatically mapped to the correct vector type per platform |
| Multiple formats | JSON (human-readable) and Bin (compact, with validation) |
| Cross-platform scripts | `.bat` for Windows and `.sh` for macOS/Linux |

## Directory Structure

```
Config/
├── Defines/                  # Type definitions (builtin beans, e.g. vec2/vec3/vec4)
│   └── builtin.xml
├── Excels/                   # Excel source files
│   ├── __tables__.xlsx       # Table registry
│   ├── __beans__.xlsx        # Bean (struct) definitions
│   ├── __enums__.xlsx        # Enum definitions
│   ├── Tables/               # Game config tables (items, achievements, ...)
│   └── Local/                # Localization text (key → translations)
├── Tools/                    # Luban runtime (Luban.dll + dependencies + templates)
├── luban.conf                # Luban configuration (targets, groups, commands)
├── gen-client-json.sh/.bat   # Generate client JSON output
├── gen-client-bin.sh/.bat    # Generate client Bin output
├── gen-server-json.sh/.bat   # Generate server JSON output
└── gen-server-bin.sh/.bat    # Generate server Bin output
```

## Quick Start

### Prerequisites

- A recent **.NET SDK** — Luban runs as a `dotnet` tool (`dotnet Tools/Luban.dll`).
- The sibling `Unity` and `Server` repositories checked out next to `Config`. The gen scripts write into `../Unity/Assets/...` and `../Server/GameFrameX.Config/...`.

### Generate client config (JSON)

From the `Config` directory:

```bash
# macOS / Linux
sh gen-client-json.sh

# Windows
gen-client-json.bat
```

This exports:

- **Data** → `../Unity/Assets/Bundles/Config`
- **Code** → `../Unity/Assets/Hotfix/Config/Generate`

### Generate server config (Bin)

```bash
# macOS / Linux
sh gen-server-bin.sh

# Windows
gen-server-bin.bat
```

This exports:

- **Data** → `../Server/GameFrameX.Config/Json`
- **Code** → `../Server/GameFrameX.Config/Config`

> The active output paths and commands are defined in `luban.conf` (`UNITY_ASSETS_PATH`, `SERVER_PATH`, `commands`). Adjust them to match your checkout layout.

## Configuration Tables

The repository ships with example tables that illustrate the workflow:

| Table | File | Description |
|-------|------|-------------|
| Achievement | `Excels/Tables/C-AchievementConfig-成就表.xlsx` | Achievement definitions |
| Item | `Excels/Tables/D-ItemConfig-道具表-道具-1001.xlsx` | Item definitions |
| Sound | `Excels/Tables/S-SoundsConfig-声音表.xlsx` | Sound definitions |
| Localization — Achievement | `Excels/Local/L-Localization-成就.xlsx` | Achievement text translations |
| Localization — Text | `Excels/Local/L-Localization-文本.xlsx` | General text translations |
| Localization — UI | `Excels/Local/L-Localization-UI.xlsx` | UI text translations |

Add new tables by registering them in `Excels/__tables__.xlsx` and defining their beans in `Excels/__beans__.xlsx`.

## Export Targets

Three targets are configured in `luban.conf`:

| Target | Group | Top Module | Code Target | Use |
|--------|-------|------------|-------------|-----|
| `server` | `s` | `GameFrameX.Config` | `cs-dotnet-json` / `cs-bin` | Server-side (.NET) |
| `client` | `c` | `Hotfix.Config` | `cs-simple-json` / `cs-bin` | Client-side (Unity) |
| `all` | `c`, `s` | `cfg` | `luban` (default) | Both ends together |

## System Requirements

- **.NET SDK** — to run `Luban.dll`.
- **Excel** (or a compatible editor) — to author the `.xlsx` source files.
- **OS** — Windows, macOS, or Linux.

## License

This project is licensed under the [Apache License 2.0](LICENSE.md).

## Related Links

- [Documentation](https://gameframex.doc.alianblank.com)
- [GitHub Repository](https://github.com/GameFrameX/GameFrameX.Config)
- [Issue Tracker](https://github.com/GameFrameX/GameFrameX.Config/issues)
- [Luban (upstream)](https://github.com/focus-creative-games/luban)
