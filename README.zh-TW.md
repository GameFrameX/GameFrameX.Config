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

### 目錄說明

| 目錄 | 作用 |
|------|------|
| `Defines/` | 跨端型別定義。`builtin.xml` 定義 `vec2`/`vec3`/`vec4` 等 bean，依目標端對映——客戶端為 `UnityEngine.Vector*`，伺服器為 `System.Numerics.Vector*`。 |
| `Excels/` | Excel 來源資料根目錄，被 `gameframex` 匯入器遞迴掃描（見下方「Excel 來源佈局」）。 |
| `Tools/` | Luban 執行階段：`Luban.dll` 及其相依組件與 `Templates/` 程式碼範本。gen 指令碼透過 `dotnet Tools/Luban.dll` 呼叫。 |

### Excel 來源佈局

| 路徑 | 用途 |
|------|------|
| `Excels/__tables__.xlsx` | 資料表登錄檔。在 `gameframex` 匯入器下，表由檔名自動發現，因此該檔案可為空。 |
| `Excels/__beans__.xlsx` | 各表欄位共用的 bean（結構體）定義（如 `Property`、`PropItem`）。 |
| `Excels/__enums__.xlsx` | 列舉定義，可按分類拆分到多個 sheet。 |
| `Excels/Tables/` | 遊戲配置資料表——每個匯出的 `Tb*` 類對應一張邏輯表。 |
| `Excels/Local/` | 由 `gameframex` L10N provider 消費的在地化文字表。 |

`Excels/` 下的子目錄名參與命名空間推导：匯入器按 `_`/`-` 切分目錄名，取首段作為表命名空間，這也是 `Tables/` 和 `Local/` 保持獨立目錄的原因。

## 檔案命名規範

在 `gameframex` 表匯入器下，每個 Excel 資料檔案都依檔名自動發現。檔名按 `-` 或 `_` 切分，需遵循：

```
{排序編號}-{匯出表名}-{中文標識}.xlsx
{排序編號}-{匯出表名}-{分組}-{中文標識}.xlsx      ← 指定匯出分組時
```

| 段位 | 含義 | 規則 |
|------|------|------|
| `{排序編號}` | 便於策劃在檔案瀏覽器中快速定位的單字母/數字，無匯出語義。 | 字母或數字，不含中文，如 `C`、`D`、`S`、`L`。 |
| `{匯出表名}` | 產生的表類名為 `Tb{匯出表名}`。 | PascalCase，**禁止中文**。如 `AchievementConfig` → `TbAchievementConfig`。 |
| `{分組}`（可選） | 限制檔案只在某端匯出。 | 必須是已配置的分組：`c`（客戶端）或 `s`（伺服器）。若該段為中文或其他值，則忽略分組過濾，雙端都匯出。 |
| `{中文標識}` | 人類可讀的說明，僅作文檔用途。 | 中文文字，可含多個 `-` 分段。 |

> 匯入器會拒絕 `{匯出表名}` 含中文的檔案：*"不支持中文表名 ... 表名称定义规范为: 排序编号-导出表名-中文标识名称"*。

### 分表

一張邏輯表的資料可以分散到多個 Excel 檔案——當表資料量很大，或想按模組組織行資料時很有用。匯入器會把 `{匯出表名}` 相同的所有檔案合併為同一張表，匯出時它們的行會被拼接在一起。

命名：各分片保持 `{排序編號}` 和 `{匯出表名}` 一致，只透過 `{中文標識}` 加以區分。中文標識（含其中的編號或分類）僅作文檔用途，匯入器不會解析它。

| 分表檔案 | 合併後的表 | 典型用途 |
|----------|------------|----------|
| `L-Localization-成就.xlsx`、`L-Localization-文本.xlsx`、`L-Localization-UI.xlsx` | `TbLocalization` | 按模組組織在地化文字 |
| `D-ItemConfig-道具表-道具-1001.xlsx`、`D-ItemConfig-道具表-道具-1002.xlsx`、… | `TbItemConfig` | 將大表拆分為易於管理的多個檔案 |

### 範例

| 檔名 | 表類名 | 分組 | 說明 |
|------|--------|------|------|
| `C-AchievementConfig-成就表.xlsx` | `TbAchievementConfig` | 雙端 | 成就表 |
| `D-ItemConfig-道具表-道具-1001.xlsx` | `TbItemConfig` | 雙端 | 道具表（分片）|
| `S-SoundsConfig-声音表.xlsx` | `TbSoundsConfig` | 雙端 | 聲音表 |
| `C-AchievementConfig-c-成就表.xlsx`（範例） | `TbAchievementConfig` | 僅客戶端 | 將只在客戶端匯出 |

## 表頭規範

每張資料表 sheet 用固定的表頭區塊定義欄位。`gameframex` 匯入器直接從這些行讀取結構（`read_schema_from_file = true`）：

| 行 | 標記 | 用途 |
|----|------|------|
| 1 | `##var` | 欄位名 |
| 2 | `##type` | 欄位型別：`int`、`string`、`text`、`bool`、列舉/bean 名、`list,...` 等 |
| 3 | `##group` | 欄位級分組過濾（`c`/`s`），留空表示對所有目標匯出 |
| 4 | `##` | 中文註解/說明，供策劃閱讀 |

- `text` 欄位保存的是一個**在地化 key**，在客戶端匯出時對照 `Local/` 表解析。
- 列舉/bean 型別必須在 `__enums__.xlsx` / `__beans__.xlsx` 中定義。

範例（成就表節選）：

```
##var    | id | image | name | achievement_content | LockText | achievement_unlock_condition
##type   | int| int   | text | text                | text     | (list#sep=|),int
##group  |    |       |      |                     |          |
##       | ID | 图标id | 成就Key | 成就内容Key          | 未解锁文字key | 成就解锁条件
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
