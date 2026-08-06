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
