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

**GameFrameX.Config** is the unified game configuration hub of the GameFrameX framework, built on top of [Luban](https://github.com/GameFrameX/luban). You author all game configuration and localization text in a single set of Excel source files, then generate code and data for **both client and server** in one pass.

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

### Excel source layout

| Path | Purpose |
|------|---------|
| `Excels/__tables__.xlsx` | Table registry. Under the `gameframex` importer, tables are auto-discovered from filenames, so this file may stay empty. |
| `Excels/__beans__.xlsx` | Shared bean (struct) definitions referenced by table fields (e.g. `Property`, `PropItem`). |
| `Excels/__enums__.xlsx` | Enum definitions; can be split across multiple sheets for grouping. |
| `Excels/Tables/` | Game config data tables — one logical table per exported `Tb*` class. |
| `Excels/Local/` | Localization text tables consumed by the `gameframex` L10N provider. |

Sub-directory names under `Excels/` take part in namespace derivation: the importer splits the directory name on `_`/`-` and uses the first segment as the table namespace, which is why `Tables/` and `Local/` are kept as separate folders.

## File Naming Convention

Under the `gameframex` table importer, every Excel data file is auto-discovered by its filename. The filename is split on `-` or `_` and must follow:

```
{sort}-{TableName}-{Chinese label}.xlsx
{sort}-{TableName}-{group}-{Chinese label}.xlsx      ← restrict to a target group
```

| Segment | Meaning | Rules |
|---------|---------|-------|
| `{sort}` | A single letter/digit for designers to quickly locate the file in a file browser. No export semantics. | Alphanumeric, no Chinese. e.g. `C`, `D`, `S`, `L`. |
| `{TableName}` | The exported table class is generated as `Tb{TableName}`. | PascalCase, **no Chinese allowed**. e.g. `AchievementConfig` → `TbAchievementConfig`. |
| `{group}` (optional) | Restricts the file to a target group. | Must be a configured group: `c` (client) or `s` (server). If the segment is Chinese or anything else, group filtering is skipped and the file is exported to both ends. |
| `{Chinese label}` | Human-readable description, documentation only. | Chinese text; may contain multiple `-` segments. |

> The importer rejects Chinese in `{TableName}`: *"不支持中文表名 ... 表名称定义规范为: 排序编号-导出表名-中文标识名称"*.

### Same-name merging

Files sharing the same `{TableName}` merge into one logical table (multiple input files). This is how a large table is split across several files:

| Files | Merged table |
|-------|--------------|
| `L-Localization-成就.xlsx` / `L-Localization-文本.xlsx` / `L-Localization-UI.xlsx` | `TbLocalization` |
| `D-ItemConfig-道具表-道具-1001.xlsx` (+ `1002`, `1003` …) | `TbItemConfig` |

### Examples

| Filename | Table class | Group | Notes |
|----------|-------------|-------|-------|
| `C-AchievementConfig-成就表.xlsx` | `TbAchievementConfig` | both | Achievement table |
| `D-ItemConfig-道具表-道具-1001.xlsx` | `TbItemConfig` | both | Item table, sharded |
| `S-SoundsConfig-声音表.xlsx` | `TbSoundsConfig` | both | Sound table |
| `C-AchievementConfig-c-成就表.xlsx` (example) | `TbAchievementConfig` | client only | would export to client only |

## Table Header Schema

Each data sheet defines its fields with a fixed header block. The `gameframex` importer reads the schema straight from these rows (`read_schema_from_file = true`):

| Row | Marker | Purpose |
|-----|--------|---------|
| 1 | `##var` | Field names |
| 2 | `##type` | Field types: `int`, `string`, `text`, `bool`, an enum/bean name, `list,...`, etc. |
| 3 | `##group` | Per-field group filter (`c`/`s`); blank = exported to all targets |
| 4 | `##` | Chinese comment / description for designers |

- `text` fields hold a **localization key** resolved against the `Local/` tables at client export time.
- Enum/bean types must be defined in `__enums__.xlsx` / `__beans__.xlsx`.

Example (excerpt from the achievement table):

```
##var    | id | image | name | achievement_content | LockText | achievement_unlock_condition
##type   | int| int   | text | text                | text     | (list#sep=|),int
##group  |    |       |      |                     |          |
##       | ID | 图标id | 成就Key | 成就内容Key          | 未解锁文字key | 成就解锁条件
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
- [Luban (GameFrameX fork)](https://github.com/GameFrameX/luban)
- [Luban (upstream)](https://github.com/focus-creative-games/luban)
