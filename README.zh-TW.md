<div align="center">

![GameFrameX Logo](https://download.alianblank.com/gameframex/gameframex_logo_320.png)

# GameFrameX.Config

[![Version](https://img.shields.io/github/v/release/GameFrameX/GameFrameX.Config?label=version&color=green)](https://github.com/GameFrameX/GameFrameX.Config/releases)
[![License](https://img.shields.io/badge/license-Apache%202.0-orange.svg)](LICENSE.md)
[![Documentation](https://img.shields.io/badge/docs-gameframex-brightgreen.svg)](https://gameframex.doc.alianblank.com)

**獨立遊戲前後端一體化解決方案 · 獨立遊戲開發者的圓夢大使**

[📖 文檔](https://gameframex.doc.alianblank.com/zh-TW) • [🚀 快速開始](#快速開始) • [💬 QQ群: 870596322](https://qm.qq.com/q/IrE4RSmqgY)

---

🌐 **語言**: [English](README.md) | [简体中文](README.zh-CN.md) | **繁體中文** | [日本語](README.ja.md) | [한국어](README.ko.md)

---

</div>

## 項目簡介

**GameFrameX.Config** 是 GameFrameX 框架的統一遊戲配置中心，基於 [Luban](https://github.com/GameFrameX/luban) 構建。你只需在一套 Excel 來源檔案中維護全部遊戲配置與多語言文本，即可一次性為**客戶端與伺服器**生成程式碼與資料。

- **一源雙端** —— 同一份 Excel 定義同時產出客戶端（Unity）與伺服器（.NET）輸出。
- **內建在地化（L10N）** —— 透過 `gameframex` L10N provider 將多語言文本與配置統一管理，並隨客戶端資料一併匯出。
- **跨端型別對映** —— `vec2` / `vec3` / `vec4` 在客戶端對映為 `UnityEngine.Vector2/3/4`，在伺服器對映為 `System.Numerics.Vector2/3/4`。
- **JSON 與 Bin 雙格式** —— 可依目標自由選擇；同時提供 Windows（`.bat`）與 macOS/Linux（`.sh`）指令碼。

## 功能特性

| 能力 | 說明 |
|------|------|
| 雙端生成 | 一份 Excel 來源 → 客戶端（`cs-simple-json`）+ 伺服器（`cs-dotnet-json` / `cs-bin`） |
| 在地化 | `gameframex` L10N provider；基於 key 的文字檔案位於 `Excels/Local/`，隨客戶端一併匯出 |
| 型別橋接 | `vec2/3/4` 自動對映到各端對應的向量型別 |
| 多種格式 | JSON（可讀）與 Bin（緊湊、帶校驗） |
| 跨平台指令碼 | Windows 使用 `.bat`，macOS/Linux 使用 `.sh` |

## 目錄結構

```
Config/
├── Defines/                  # 型別定義（內建 bean，如 vec2/vec3/vec4）
│   └── builtin.xml
├── Excels/                   # Excel 來源檔案
│   ├── __tables__.xlsx       # 資料表登錄檔
│   ├── __beans__.xlsx        # bean（結構體）定義
│   ├── __enums__.xlsx        # 列舉定義
│   ├── Tables/               # 遊戲配置表（道具、成就等）
│   └── Local/                # 在地化文字（key → 翻譯）
├── Tools/                    # Luban 執行階段（Luban.dll + 相依 + 範本）
├── luban.conf                # Luban 配置（目標、分組、命令）
├── gen-client-json.sh/.bat   # 生成客戶端 JSON 輸出
├── gen-client-bin.sh/.bat    # 生成客戶端 Bin 輸出
├── gen-server-json.sh/.bat   # 生成伺服器 JSON 輸出
└── gen-server-bin.sh/.bat    # 生成伺服器 Bin 輸出
```

## 快速開始

### 前置需求

- 較新版本的 **.NET SDK** —— Luban 以 `dotnet` 工具執行（`dotnet Tools/Luban.dll`）。
- 在 `Config` 同級目錄簽出 `Unity` 與 `Server` 儲存庫。生成指令碼會寫入 `../Unity/Assets/...` 與 `../Server/GameFrameX.Config/...`。

### 生成客戶端配置（JSON）

在 `Config` 目錄下執行：

```bash
# macOS / Linux
sh gen-client-json.sh

# Windows
gen-client-json.bat
```

匯出位置：

- **資料** → `../Unity/Assets/Bundles/Config`
- **程式碼** → `../Unity/Assets/Hotfix/Config/Generate`

### 生成伺服器配置（Bin）

```bash
# macOS / Linux
sh gen-server-bin.sh

# Windows
gen-server-bin.bat
```

匯出位置：

- **資料** → `../Server/GameFrameX.Config/Json`
- **程式碼** → `../Server/GameFrameX.Config/Config`

> 實際的輸出路徑與命令定義在 `luban.conf` 中（`UNITY_ASSETS_PATH`、`SERVER_PATH`、`commands`）。請依你的目錄布局進行調整。

## 配置表

倉庫內建了用於示範工作流的範例表：

| 表 | 檔案 | 說明 |
|----|------|------|
| 成就 | `Excels/Tables/C-AchievementConfig-成就表.xlsx` | 成就定義 |
| 道具 | `Excels/Tables/D-ItemConfig-道具表-道具-1001.xlsx` | 道具定義 |
| 聲音 | `Excels/Tables/S-SoundsConfig-声音表.xlsx` | 聲音定義 |
| 在地化 — 成就 | `Excels/Local/L-Localization-成就.xlsx` | 成就文字翻譯 |
| 在地化 — 文本 | `Excels/Local/L-Localization-文本.xlsx` | 通用文字翻譯 |
| 在地化 — UI | `Excels/Local/L-Localization-UI.xlsx` | UI 文字翻譯 |

新增資料表時，在 `Excels/__tables__.xlsx` 中登錄，並在 `Excels/__beans__.xlsx` 中定義對應的 bean。

## 匯出目標

`luban.conf` 中配置了三個目標：

| 目標 | 分組 | 頂層模組 | 程式碼目標 | 用途 |
|------|------|----------|------------|------|
| `server` | `s` | `GameFrameX.Config` | `cs-dotnet-json` / `cs-bin` | 伺服器（.NET） |
| `client` | `c` | `Hotfix.Config` | `cs-simple-json` / `cs-bin` | 客戶端（Unity） |
| `all` | `c`, `s` | `cfg` | `luban`（預設） | 雙端同時 |

## 系統需求

- **.NET SDK** —— 用於執行 `Luban.dll`。
- **Excel**（或相容編輯器）—— 用於編寫 `.xlsx` 來源檔案。
- **作業系統** —— Windows、macOS 或 Linux。

## 開源協議

本專案基於 [Apache License 2.0](LICENSE.md) 協議開源。

## 相關連結

- [文檔](https://gameframex.doc.alianblank.com)
- [GitHub 儲存庫](https://github.com/GameFrameX/GameFrameX.Config)
- [問題追蹤](https://github.com/GameFrameX/GameFrameX.Config/issues)
- [Luban（GameFrameX 分支）](https://github.com/GameFrameX/luban)
- [Luban（上游）](https://github.com/focus-creative-games/luban)
