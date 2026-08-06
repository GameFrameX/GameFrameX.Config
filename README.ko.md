<div align="center">

![GameFrameX Logo](https://download.alianblank.com/gameframex/gameframex_logo_320.png)

# GameFrameX.Config

[![Version](https://img.shields.io/github/v/release/GameFrameX/GameFrameX.Config?label=version&color=green)](https://github.com/GameFrameX/GameFrameX.Config/releases)
[![License](https://img.shields.io/badge/license-Apache%202.0-orange.svg)](LICENSE.md)
[![Documentation](https://img.shields.io/badge/docs-gameframex-brightgreen.svg)](https://gameframex.doc.alianblank.com)

**인디 게임 개발자를 위한 올인원 솔루션 · 인디 개발자의 꿈을 실현**

[📖 문서](https://gameframex.doc.alianblank.com/ko) • [🚀 빠른 시작](#빠른-시작) • [💬 QQ 그룹: 870596322](https://qm.qq.com/q/IrE4RSmqgY)

---

🌐 **언어**: [English](README.md) | [简体中文](README.zh-CN.md) | [繁體中文](README.zh-TW.md) | [日本語](README.ja.md) | **한국어**

---

</div>

## 프로젝트 개요

**GameFrameX.Config**는 GameFrameX 프레임워크의 통합 게임 설정 허브로, [Luban](https://github.com/GameFrameX/luban) 위에 구축되었습니다. 모든 게임 설정과 로컬라이제이션 텍스트를 하나의 Excel 소스 파일 세트로 관리하고, **클라이언트와 서버 양쪽** 모두에 대한 코드와 데이터를 한 번에 생성할 수 있습니다.

- **단일 소스, 양단 내보내기** — 동일한 Excel 정의에서 클라이언트(Unity)와 서버(.NET) 출력을 생성합니다.
- **로컬라이제이션 내장(L10N)** — `gameframex` L10N provider로 다국어 텍스트를 설정과 함께 통합 관리하며, 클라이언트 데이터와 함께 내보냅니다.
- **크로스 플랫폼 타입** — `vec2` / `vec3` / `vec4`는 클라이언트에서 `UnityEngine.Vector2/3/4`, 서버에서 `System.Numerics.Vector2/3/4`로 매핑됩니다.
- **JSON 및 Bin 형식** — 타깃별로 선택 가능. Windows(`.bat`)와 macOS/Linux(`.sh`) 스크립트를 모두 제공합니다.

## 기능

| 기능 | 설명 |
|------|------|
| 양단 생성 | 하나의 Excel 소스 → 클라이언트(`cs-simple-json`) + 서버(`cs-dotnet-json` / `cs-bin`) |
| 로컬라이제이션 | `gameframex` L10N provider. `Excels/Local/`의 key 기반 텍스트 파일을 클라이언트와 함께 내보냄 |
| 타입 브리징 | `vec2/3/4`를 각 플랫폼에 맞는 벡터 타입으로 자동 매핑 |
| 다중 포맷 | JSON(가독성)과 Bin(컴팩트, 검증 포함) |
| 크로스 플랫폼 스크립트 | Windows는 `.bat`, macOS/Linux는 `.sh` |

## 디렉터리 구조

```
Config/
├── Defines/                  # 타입 정의(내장 bean, 예: vec2/vec3/vec4)
│   └── builtin.xml
├── Excels/                   # Excel 소스 파일
│   ├── __tables__.xlsx       # 테이블 레지스트리
│   ├── __beans__.xlsx        # bean(구조체) 정의
│   ├── __enums__.xlsx        # 열거형 정의
│   ├── Tables/               # 게임 설정 테이블(아이템, 업적 등)
│   └── Local/                # 로컬라이제이션 텍스트(key → 번역)
├── Tools/                    # Luban 런타임(Luban.dll + 의존성 + 템플릿)
├── luban.conf                # Luban 설정(타깃, 그룹, 명령)
├── gen-client-json.sh/.bat   # 클라이언트 JSON 출력 생성
├── gen-client-bin.sh/.bat    # 클라이언트 Bin 출력 생성
├── gen-server-json.sh/.bat   # 서버 JSON 출력 생성
└── gen-server-bin.sh/.bat    # 서버 Bin 출력 생성
```

### Excel 소스 레이아웃

| 경로 | 용도 |
|------|------|
| `Excels/__tables__.xlsx` | 테이블 레지스트리. `gameframex` 임포터에서는 파일명으로 테이블이 자동 발견되므로 이 파일은 비어 있어도 됩니다. |
| `Excels/__beans__.xlsx` | 테이블 필드가 참조하는 공유 bean(구조체) 정의(예: `Property`, `PropItem`). |
| `Excels/__enums__.xlsx` | 열거형 정의. 분류별로 여러 시트로 나눌 수 있습니다. |
| `Excels/Tables/` | 게임 설정 데이터 테이블 — 내보내지는 `Tb*` 클래스 하나당 논리 테이블 한 개. |
| `Excels/Local/` | `gameframex` L10N provider가 소비하는 로컬라이제이션 텍스트 테이블. |

`Excels/` 하위 디렉터리명은 네임스페이스 도출에 사용됩니다. 임포터는 디렉터리명을 `_`/`-`로 분할하여 첫 세그먼트를 테이블 네임스페이스로 씁니다. `Tables/`와 `Local/`을 분리하는 이유입니다.

## 파일 명명 규칙

`gameframex` 테이블 임포터에서는 모든 Excel 데이터 파일이 파일명으로 자동 발견됩니다. 파일명은 `-` 또는 `_`로 분할되며 다음 형식을 따라야 합니다:

```
{정렬 번호}-{내보내기 테이블명}-{중국어 라벨}.xlsx
{정렬 번호}-{내보내기 테이블명}-{그룹}-{중국어 라벨}.xlsx      ← 대상 그룹을 제한할 때
```

| 세그먼트 | 의미 | 규칙 |
|----------|------|------|
| `{정렬 번호}` | 기획자가 파일 브라우저에서 빠르게 찾기 위한 단일 문자/숫자. 내보내기 의미는 없음. | 영숫자, 중국어 불가. 예: `C`, `D`, `S`, `L`. |
| `{내보내기 테이블명}` | 내보내지는 테이블 클래스는 `Tb{내보내기 테이블명}`로 생성됨. | PascalCase, **중국어 불가**. 예: `AchievementConfig` → `TbAchievementConfig`. |
| `{그룹}` (선택) | 파일을 특정 대상으로 제한. | 설정된 그룹 `c`(클라이언트) 또는 `s`(서버)만 가능. 중국어 등 다른 값이면 그룹 필터링을 건너뛰고 양단에 모두 내보냄. |
| `{중국어 라벨}` | 사람이 읽기 위한 설명. 문서 용도만. | 중국어 텍스트. 여러 `-` 세그먼트를 포함해도 됨. |

> 임포터는 `{내보내기 테이블명}`에 중국어가 포함된 파일을 거부합니다: *"不支持中文表名 ... 表名称定义规范为: 排序编号-导出表名-中文标识名称"*.

### 동명 병합

동일한 `{내보내기 테이블명}`을 가진 파일들은 하나의 논리 테이블(여러 입력 파일)로 병합됩니다. 큰 테이블을 여러 파일로 나누는 방법입니다:

| 파일 | 병합된 테이블 |
|------|----------------|
| `L-Localization-成就.xlsx` / `L-Localization-文本.xlsx` / `L-Localization-UI.xlsx` | `TbLocalization` |
| `D-ItemConfig-道具表-道具-1001.xlsx`(`1002`, `1003`… 계속 가능) | `TbItemConfig` |

### 예시

| 파일명 | 테이블 클래스 | 그룹 | 비고 |
|--------|----------------|------|------|
| `C-AchievementConfig-成就表.xlsx` | `TbAchievementConfig` | 양단 | 업적 테이블 |
| `D-ItemConfig-道具表-道具-1001.xlsx` | `TbItemConfig` | 양단 | 아이템 테이블(분할)|
| `S-SoundsConfig-声音表.xlsx` | `TbSoundsConfig` | 양단 | 사운드 테이블 |
| `C-AchievementConfig-c-成就表.xlsx`(예시) | `TbAchievementConfig` | 클라이언트만 | 클라이언트에만 내보냄 |

## 테이블 헤더 스키마

각 데이터 시트는 고정된 헤더 블록으로 필드를 정의합니다. `gameframex` 임포터는 이 행들에서 직접 스키마를 읽습니다(`read_schema_from_file = true`):

| 행 | 마커 | 용도 |
|----|------|------|
| 1 | `##var` | 필드명 |
| 2 | `##type` | 필드 타입: `int`, `string`, `text`, `bool`, 열거형/bean명, `list,...` 등 |
| 3 | `##group` | 필드 단위 그룹 필터링(`c`/`s`). 비어 있으면 모든 대상으로 내보냄 |
| 4 | `##` | 기획자용 중국어 주석/설명 |

- `text` 필드는**로컬라이제이션 키**를 저장하며, 클라이언트 내보내기 시 `Local/` 테이블에서 해석됩니다.
- 열거형/bean 타입은 `__enums__.xlsx` / `__beans__.xlsx`에 정의되어 있어야 합니다.

예시(업적 테이블 발췌):

```
##var    | id | image | name | achievement_content | LockText | achievement_unlock_condition
##type   | int| int   | text | text                | text     | (list#sep=|),int
##group  |    |       |      |                     |          |
##       | ID | 图标id | 成就Key | 成就内容Key          | 未解锁文字key | 成就解锁条件
```

## 빠른 시작

### 사전 요구 사항

- 비교적 최신의 **.NET SDK** — Luban은 `dotnet` 도구로 실행됩니다(`dotnet Tools/Luban.dll`).
- `Config`와 같은 계층에 `Unity`와 `Server` 저장소를 체크아웃해 둘 것. 생성 스크립트는 `../Unity/Assets/...`와 `../Server/GameFrameX.Config/...`에 출력합니다.

### 클라이언트 설정 생성(JSON)

`Config` 디렉터리에서 실행:

```bash
# macOS / Linux
sh gen-client-json.sh

# Windows
gen-client-json.bat
```

출력 위치:

- **데이터** → `../Unity/Assets/Bundles/Config`
- **코드** → `../Unity/Assets/Hotfix/Config/Generate`

### 서버 설정 생성(Bin)

```bash
# macOS / Linux
sh gen-server-bin.sh

# Windows
gen-server-bin.bat
```

출력 위치:

- **데이터** → `../Server/GameFrameX.Config/Json`
- **코드** → `../Server/GameFrameX.Config/Config`

> 실제 출력 경로와 명령은 `luban.conf`(`UNITY_ASSETS_PATH`, `SERVER_PATH`, `commands`)에 정의되어 있습니다. 체크아웃 레이아웃에 맞게 조정하세요.

## 구성 테이블

저장소에는 워크플로를 보여주는 샘플 테이블이 포함되어 있습니다:

| 테이블 | 파일 | 설명 |
|--------|------|------|
| 업적 | `Excels/Tables/C-AchievementConfig-成就表.xlsx` | 업적 정의 |
| 아이템 | `Excels/Tables/D-ItemConfig-道具表-道具-1001.xlsx` | 아이템 정의 |
| 사운드 | `Excels/Tables/S-SoundsConfig-声音表.xlsx` | 사운드 정의 |
| 로컬라이제이션 — 업적 | `Excels/Local/L-Localization-成就.xlsx` | 업적 텍스트 번역 |
| 로컬라이제이션 — 텍스트 | `Excels/Local/L-Localization-文本.xlsx` | 일반 텍스트 번역 |
| 로컬라이제이션 — UI | `Excels/Local/L-Localization-UI.xlsx` | UI 텍스트 번역 |

새 테이블을 추가하려면 `Excels/__tables__.xlsx`에 등록하고, `Excels/__beans__.xlsx`에 bean을 정의하세요.

## 내보내기 대상

`luban.conf`에는 세 개의 타깃이 설정되어 있습니다:

| 타깃 | 그룹 | 최상위 모듈 | 코드 타깃 | 용도 |
|------|------|-------------|-----------|------|
| `server` | `s` | `GameFrameX.Config` | `cs-dotnet-json` / `cs-bin` | 서버(.NET) |
| `client` | `c` | `Hotfix.Config` | `cs-simple-json` / `cs-bin` | 클라이언트(Unity) |
| `all` | `c`, `s` | `cfg` | `luban`(기본) | 양단 동시 |

## 시스템 요구사항

- **.NET SDK** — `Luban.dll` 실행에 필요.
- **Excel**(또는 호환 에디터) — `.xlsx` 소스 파일 작성에 필요.
- **OS** — Windows, macOS, Linux.

## 라이선스

이 프로젝트는 [Apache License 2.0](LICENSE.md)에 따라 라이선스됩니다.

## 관련 링크

- [문서](https://gameframex.doc.alianblank.com)
- [GitHub 저장소](https://github.com/GameFrameX/GameFrameX.Config)
- [이슈 트래커](https://github.com/GameFrameX/GameFrameX.Config/issues)
- [Luban(GameFrameX 포크)](https://github.com/GameFrameX/luban)
- [Luban(업스트림)](https://github.com/focus-creative-games/luban)
