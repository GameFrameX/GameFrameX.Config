<div align="center">

![GameFrameX Logo](https://download.alianblank.com/gameframex/gameframex_logo_320.png)

# GameFrameX.Config

[![Version](https://img.shields.io/github/v/release/GameFrameX/GameFrameX.Config?label=version&color=green)](https://github.com/GameFrameX/GameFrameX.Config/releases)
[![License](https://img.shields.io/badge/license-Apache%202.0-orange.svg)](LICENSE.md)
[![Documentation](https://img.shields.io/badge/docs-gameframex-brightgreen.svg)](https://gameframex.doc.alianblank.com)

**インディゲーム開発者向けオールインワンソリューション · インディ開発者の夢を支援**

[📖 ドキュメント](https://gameframex.doc.alianblank.com/ja) • [🚀 クイックスタート](#クイックスタート) • [💬 QQグループ: 870596322](https://qm.qq.com/q/IrE4RSmqgY)

---

🌐 **言語**: [English](README.md) | [简体中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | **日本語** | [한국어](README.ko.md)

---

</div>

## プロジェクト概要

**GameFrameX.Config** は、GameFrameX フレームワークの統合ゲーム設定ハブであり、[Luban](https://github.com/GameFrameX/luban) の上に構築されています。すべてのゲーム設定とローカライズテキストを 1 セットの Excel ソースファイルで管理し、**クライアントとサーバーの両方**に対してコードとデータを一括生成できます。

- **単一ソース・両端出力** —— 同じ Excel 定義からクライアント（Unity）とサーバー（.NET）の出力を生成します。
- **ローカライズ内蔵（L10N）** —— `gameframex` L10N provider により多言語テキストを設定と一元管理し、クライアントデータと一緒にエクスポートします。
- **クロスプラットフォーム型** —— `vec2` / `vec3` / `vec4` はクライアントでは `UnityEngine.Vector2/3/4`、サーバーでは `System.Numerics.Vector2/3/4` に対応付けされます。
- **JSON と Bin の両形式** —— ターゲットごとに選択可能。Windows（`.bat`）と macOS/Linux（`.sh`）の両方のスクリプトを同梱しています。

## 機能概要

| 機能 | 説明 |
|------|------|
| 両端生成 | 1 つの Excel ソース → クライアント（`cs-simple-json`）+ サーバー（`cs-dotnet-json` / `cs-bin`） |
| ローカライズ | `gameframex` L10N provider。`Excels/Local/` 配下の key ベースのテキストファイルをクライアントと一緒にエクスポート |
| 型ブリッジ | `vec2/3/4` を各プラットフォームの適切なベクトル型に自動マッピング |
| 複数フォーマット | JSON（可読）と Bin（コンパクト・検証付き） |
| クロスプラットフォームスクリプト | Windows は `.bat`、macOS/Linux は `.sh` |

## ディレクトリ構成

```
Config/
├── Defines/                  # 型定義（組み込み bean、例: vec2/vec3/vec4）
│   └── builtin.xml
├── Excels/                   # Excel ソースファイル
│   ├── __tables__.xlsx       # テーブル登録表
│   ├── __beans__.xlsx        # bean（構造体）定義
│   ├── __enums__.xlsx        # 列挙型定義
│   ├── Tables/               # ゲーム設定テーブル（アイテム、実績など）
│   └── Local/                # ローカライズテキスト（key → 翻訳）
├── Tools/                    # Luban ランタイム（Luban.dll + 依存関係 + テンプレート）
├── luban.conf                # Luban 設定（ターゲット、グループ、コマンド）
├── gen-client-json.sh/.bat   # クライアント JSON 出力を生成
├── gen-client-bin.sh/.bat    # クライアント Bin 出力を生成
├── gen-server-json.sh/.bat   # サーバー JSON 出力を生成
└── gen-server-bin.sh/.bat    # サーバー Bin 出力を生成
```

### Excel ソースレイアウト

| パス | 用途 |
|------|------|
| `Excels/__tables__.xlsx` | テーブル登録表。`gameframex` インポーターではテーブルがファイル名から自動発見されるため、空のままで構いません。 |
| `Excels/__beans__.xlsx` | テーブルフィールドが参照する共通 bean（構造体）定義（例: `Property`、`PropItem`）。 |
| `Excels/__enums__.xlsx` | 列挙型定義。分類ごとに複数シートへ分割可能。 |
| `Excels/Tables/` | ゲーム設定データテーブル — エクスポートされる `Tb*` クラス 1 つにつき 1 論理テーブル。 |
| `Excels/Local/` | `gameframex` L10N provider が消費するローカライズテキストテーブル。 |

`Excels/` 配下のサブディレクトリ名は名前空間の導出に使われます。インポーターはディレクトリ名を `_`/`-` で分割し、最初のセグメントをテーブル名前空間とします。これが `Tables/` と `Local/` を分ける理由です。

## ファイル命名規則

`gameframex` テーブルインポーターでは、すべての Excel データファイルがファイル名で自動発見されます。ファイル名は `-` または `_` で分割され、以下の形式に従う必要があります：

```
{ソート番号}-{エクスポートテーブル名}-{中国語ラベル}.xlsx
{ソート番号}-{エクスポートテーブル名}-{グループ}-{中国語ラベル}.xlsx      ← ターゲットグループを制限するとき
```

| セグメント | 意味 | ルール |
|------------|------|--------|
| `{ソート番号}` | プランナーがファイルブラウザで素早く見つけられるようにする単一の英数字。エクスポート上の意味は持たない。 | 英数字、中国語不可。例: `C`、`D`、`S`、`L`。 |
| `{エクスポートテーブル名}` | エクスポートされるテーブルクラスは `Tb{エクスポートテーブル名}` として生成される。 | PascalCase、**中国語は不可**。例: `AchievementConfig` → `TbAchievementConfig`。 |
| `{グループ}`（任意） | ファイルを特定ターゲットに制限する。 | 設定済みグループ `c`（クライアント）または `s`（サーバー）のみ。中国語など他の値ならグループ絞り込みはスキップされ、両端にエクスポートされる。 |
| `{中国語ラベル}` | 人間が読むための説明。ドキュメント用途のみ。 | 中国語テキスト。複数の `-` セグメントを含んでもよい。 |

> インポーターは `{エクスポートテーブル名}` に中国語を含むファイルを拒否します: *"不支持中文表名 ... 表名称定义规范为: 排序编号-导出表名-中文标识名称"*。

### 同名マージ

同じ `{エクスポートテーブル名}` を持つファイルは 1 つの論理テーブル（複数入力ファイル）にマージされます。これが大きなテーブルを複数ファイルへ分割する方法です：

| ファイル | マージ後のテーブル |
|----------|--------------------|
| `L-Localization-成就.xlsx` / `L-Localization-文本.xlsx` / `L-Localization-UI.xlsx` | `TbLocalization` |
| `D-ItemConfig-道具表-道具-1001.xlsx`（`1002`、`1003`… と続けて可） | `TbItemConfig` |

### 例

| ファイル名 | テーブルクラス | グループ | 備考 |
|------------|----------------|----------|------|
| `C-AchievementConfig-成就表.xlsx` | `TbAchievementConfig` | 両端 | 実績テーブル |
| `D-ItemConfig-道具表-道具-1001.xlsx` | `TbItemConfig` | 両端 | アイテムテーブル（分割）|
| `S-SoundsConfig-声音表.xlsx` | `TbSoundsConfig` | 両端 | サウンドテーブル |
| `C-AchievementConfig-c-成就表.xlsx`（例） | `TbAchievementConfig` | クライアントのみ | クライアントにのみエクスポート |

## テーブルヘッダー仕様

各データシートは固定のヘッダーブロックでフィールドを定義します。`gameframex` インポーターはこれらの行から直接スキーマを読み取ります（`read_schema_from_file = true`）：

| 行 | マーカー | 用途 |
|----|----------|------|
| 1 | `##var` | フィールド名 |
| 2 | `##type` | フィールド型: `int`、`string`、`text`、`bool`、列挙/bean 名、`list,...` など |
| 3 | `##group` | フィールド単位のグループ絞り込み（`c`/`s`）。空欄は全ターゲットへエクスポート |
| 4 | `##` | プランナー向けの中国語コメント/説明 |

- `text` フィールドは**ローカライズキー**を保持し、クライアントのエクスポート時に `Local/` テーブルで解決されます。
- 列挙/bean 型は `__enums__.xlsx` / `__beans__.xlsx` で定義されている必要があります。

例（実績テーブルより抜粋）:

```
##var    | id | image | name | achievement_content | LockText | achievement_unlock_condition
##type   | int| int   | text | text                | text     | (list#sep=|),int
##group  |    |       |      |                     |          |
##       | ID | 图标id | 成就Key | 成就内容Key          | 未解锁文字key | 成就解锁条件
```

## クイックスタート

### 前提条件

- 比較的新しい **.NET SDK** —— Luban は `dotnet` ツールとして実行されます（`dotnet Tools/Luban.dll`）。
- `Config` と同階層に `Unity` と `Server` リポジトリをチェックアウトしておくこと。生成スクリプトは `../Unity/Assets/...` と `../Server/GameFrameX.Config/...` に出力します。

### クライアント設定の生成（JSON）

`Config` ディレクトリで実行：

```bash
# macOS / Linux
sh gen-client-json.sh

# Windows
gen-client-json.bat
```

出力先：

- **データ** → `../Unity/Assets/Bundles/Config`
- **コード** → `../Unity/Assets/Hotfix/Config/Generate`

### サーバー設定の生成（Bin）

```bash
# macOS / Linux
sh gen-server-bin.sh

# Windows
gen-server-bin.bat
```

出力先：

- **データ** → `../Server/GameFrameX.Config/Json`
- **コード** → `../Server/GameFrameX.Config/Config`

> 実際の出力パスとコマンドは `luban.conf`（`UNITY_ASSETS_PATH`、`SERVER_PATH`、`commands`）で定義されています。チェックアウトの構成に合わせて調整してください。

## 設定テーブル

リポジトリにはワークフローを示すサンプルテーブルが同梱されています：

| テーブル | ファイル | 説明 |
|----------|----------|------|
| 実績 | `Excels/Tables/C-AchievementConfig-成就表.xlsx` | 実績定義 |
| アイテム | `Excels/Tables/D-ItemConfig-道具表-道具-1001.xlsx` | アイテム定義 |
| サウンド | `Excels/Tables/S-SoundsConfig-声音表.xlsx` | サウンド定義 |
| ローカライズ — 実績 | `Excels/Local/L-Localization-成就.xlsx` | 実績テキストの翻訳 |
| ローカライズ — テキスト | `Excels/Local/L-Localization-文本.xlsx` | 一般テキストの翻訳 |
| ローカライズ — UI | `Excels/Local/L-Localization-UI.xlsx` | UI テキストの翻訳 |

新しいテーブルを追加する場合は、`Excels/__tables__.xlsx` に登録し、`Excels/__beans__.xlsx` で bean を定義してください。

## エクスポートターゲット

`luban.conf` には 3 つのターゲットが設定されています：

| ターゲット | グループ | 最上位モジュール | コードターゲット | 用途 |
|------------|----------|------------------|------------------|------|
| `server` | `s` | `GameFrameX.Config` | `cs-dotnet-json` / `cs-bin` | サーバー（.NET） |
| `client` | `c` | `Hotfix.Config` | `cs-simple-json` / `cs-bin` | クライアント（Unity） |
| `all` | `c`, `s` | `cfg` | `luban`（デフォルト） | 両端を同時に |

## システム要件

- **.NET SDK** —— `Luban.dll` の実行に必要。
- **Excel**（または互換エディタ）—— `.xlsx` ソースファイルの編集に必要。
- **OS** —— Windows、macOS、Linux。

## ライセンス

本プロジェクトは [Apache License 2.0](LICENSE.md) の下でライセンスされています。

## 関連リンク

- [ドキュメント](https://gameframex.doc.alianblank.com)
- [GitHub リポジトリ](https://github.com/GameFrameX/GameFrameX.Config)
- [Issue トラッカー](https://github.com/GameFrameX/GameFrameX.Config/issues)
- [Luban（GameFrameX フォーク）](https://github.com/GameFrameX/luban)
- [Luban（アップストリーム）](https://github.com/focus-creative-games/luban)
