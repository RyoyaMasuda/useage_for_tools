# uvの使い方詳細ガイド

## 目次
1. [uvとは](#uvとは)
2. [インストール](#インストール)
3. [プロジェクト管理](#プロジェクト管理)
4. [パッケージ管理](#パッケージ管理)
5. [仮想環境管理](#仮想環境管理)
6. [ツール管理](#ツール管理)
7. [Pythonバージョン管理](#pythonバージョン管理)
8. [よく使うコマンド一覧](#よく使うコマンド一覧)

---

## uvとは

`uv`は、Astral社が開発したRust製の超高速Pythonパッケージマネージャーです。

### 主な特徴
- **高速**: pipの10-100倍の速度
- **オールインワン**: パッケージ管理、仮想環境、Pythonバージョン管理を統合
- **pip互換**: 既存のpipコマンドと互換性あり
- **モダンな依存関係解決**: `pyproject.toml`と`uv.lock`を使用

---

## インストール

### Linux/macOS
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Windows
```powershell
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### pipを使用（推奨されない）
```bash
pip install uv
```

### インストール確認
```bash
uv --version
```

---

## プロジェクト管理

### 新規プロジェクトの作成
```bash
# 空のプロジェクトを作成
uv init my-project

# アプリケーションプロジェクトを作成
uv init my-project --app

# ライブラリプロジェクトを作成
uv init my-project --lib
```

生成されるファイル構造:
```
my-project/
├── pyproject.toml      # プロジェクト設定
├── README.md
├── .python-version     # Pythonバージョン指定
└── src/
    └── my_project/
        └── __init__.py
```

### プロジェクトへの依存パッケージ追加
```bash
# 通常の依存パッケージを追加
uv add requests

# 開発用依存パッケージを追加
uv add --dev pytest black ruff

# バージョン指定して追加
uv add "django>=4.2,<5.0"

# 複数パッケージを一度に追加
uv add numpy pandas matplotlib
```

### パッケージの削除
```bash
uv remove requests

# 開発依存から削除
uv remove --dev pytest
```

### 依存関係のロック
```bash
# uv.lockファイルを生成・更新
uv lock

# ロックファイルを使ってインストール
uv sync
```

### スクリプト実行
```bash
# プロジェクト環境でPythonスクリプトを実行
uv run python script.py

# プロジェクト環境でコマンドを実行
uv run pytest

# 一時的にパッケージを追加して実行
uv run --with requests python script.py
```

---

## パッケージ管理

### パッケージのインストール
```bash
# 単一パッケージをインストール
uv pip install requests

# バージョン指定してインストール
uv pip install "django==4.2.0"
uv pip install "numpy>=1.20,<2.0"

# requirements.txtからインストール
uv pip install -r requirements.txt

# 編集可能モードでローカルパッケージをインストール
uv pip install -e .
uv pip install -e ./path/to/package
```

### パッケージのアップグレード
```bash
# 特定パッケージをアップグレード
uv pip install --upgrade requests

# すべてのパッケージをアップグレード
uv pip install --upgrade-package requests
```

### パッケージのアンインストール
```bash
uv pip uninstall requests

# 複数パッケージを一度にアンインストール
uv pip uninstall requests numpy pandas
```

### インストール済みパッケージの確認
```bash
# パッケージ一覧を表示
uv pip list

# フリーズ形式で出力（requirements.txt用）
uv pip freeze

# requirements.txtに保存
uv pip freeze > requirements.txt

# 特定パッケージの情報を表示
uv pip show requests
```

### パッケージの検索
```bash
# PyPIでパッケージを検索
uv pip search django
```

---

## 仮想環境管理

### 仮想環境の作成
```bash
# デフォルトの仮想環境を作成（.venv）
uv venv

# 名前を指定して作成
uv venv myenv

# 特定のPythonバージョンで作成
uv venv --python 3.11
uv venv --python 3.12.0

# システムのパッケージにアクセスできる環境を作成
uv venv --system-site-packages
```

### 仮想環境の有効化
```bash
# Linux/macOS
source .venv/bin/activate

# Windows（PowerShell）
.venv\Scripts\Activate.ps1

# Windows（cmd）
.venv\Scripts\activate.bat
```

### 仮想環境の無効化
```bash
deactivate
```

### 仮想環境の削除
```bash
# 仮想環境ディレクトリを削除
rm -rf .venv
```

---

## ツール管理

uvは、CLIツール（black、ruff、pytestなど）を隔離された環境で管理できます。

### ツールのインストール
```bash
# ツールをグローバルにインストール
uv tool install ruff

# バージョン指定してインストール
uv tool install black==23.12.0

# 追加パッケージとともにインストール
uv tool install jupyter --with numpy --with pandas
```

### ツールの実行
```bash
# インストール済みツールを実行
ruff check .
black .

# インストールせずに一時的に実行（uvx）
uvx ruff check .
uvx --with numpy python -c "import numpy; print(numpy.__version__)"
```

### ツールのアップグレード
```bash
# 特定のツールをアップグレード
uv tool upgrade ruff

# すべてのツールをアップグレード
uv tool upgrade --all

# 依存パッケージも含めて再インストール
uv tool upgrade ruff --reinstall

# 特定の依存パッケージのみアップグレード
uv tool upgrade jupyter --upgrade-package numpy
```

### ツールのアンインストール
```bash
uv tool uninstall ruff
```

### インストール済みツールの一覧
```bash
# ツール一覧を表示
uv tool list

# ツールのインストールディレクトリを表示
uv tool dir

# 実行ファイルのディレクトリを表示
uv tool dir --bin
```

---

## Pythonバージョン管理

uvは、Pythonのバージョン管理も統合しています（pyenvの代替）。

### Pythonバージョンのインストール
```bash
# 最新バージョンをインストール
uv python install

# 特定のバージョンをインストール
uv python install 3.12
uv python install 3.11.5

# 複数バージョンをインストール
uv python install 3.11 3.12
```

### インストール済みPythonの一覧
```bash
uv python list
```

### プロジェクトで使用するPythonバージョンの指定
```bash
# .python-versionファイルを作成
uv python pin 3.12

# または手動で作成
echo "3.12" > .python-version
```

### Pythonバージョンの確認
```bash
# 現在のPythonバージョンを確認
uv python find
```

---

## よく使うコマンド一覧

### プロジェクトのセットアップ（新規）
```bash
# プロジェクト作成から実行まで
uv init my-project
cd my-project
uv add requests pytest
uv run python -c "import requests; print(requests.__version__)"
```

### プロジェクトのセットアップ（既存）
```bash
# クローン後の環境構築
git clone <repository>
cd <project>
uv sync                    # 依存関係をインストール
uv run pytest              # テストを実行
```

### 日常的な開発フロー
```bash
# パッケージを追加
uv add numpy

# スクリプトを実行
uv run python main.py

# テストを実行
uv run pytest

# リンターを実行
uv run ruff check .

# フォーマッターを実行
uv run black .
```

### 依存関係の更新
```bash
# ロックファイルを更新
uv lock --upgrade

# 特定パッケージのみ更新
uv lock --upgrade-package requests

# インストールまで一括実行
uv sync --upgrade
```

---

## pyproject.toml の例

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "プロジェクトの説明"
readme = "README.md"
requires-python = ">=3.11"
dependencies = [
    "requests>=2.31.0",
    "numpy>=1.24.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.4.0",
    "black>=23.0.0",
    "ruff>=0.1.0",
]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.uv]
dev-dependencies = [
    "pytest>=7.4.0",
    "black>=23.0.0",
]
```

---

## pipとの対応表

| pip | uv |
|-----|-----|
| `pip install package` | `uv pip install package` |
| `pip install -r requirements.txt` | `uv pip install -r requirements.txt` |
| `pip uninstall package` | `uv pip uninstall package` |
| `pip list` | `uv pip list` |
| `pip freeze` | `uv pip freeze` |
| `pip show package` | `uv pip show package` |
| `python -m venv .venv` | `uv venv` |
| `pip install -e .` | `uv pip install -e .` |

---

## 環境変数

### よく使う環境変数
```bash
# uvのデータディレクトリを変更
export UV_CACHE_DIR=~/.cache/uv

# ツールのインストールディレクトリを変更
export UV_TOOL_DIR=~/.local/share/uv/tools

# Pythonバージョンのインストール先を変更
export UV_PYTHON_INSTALL_DIR=~/.local/share/uv/python

# 並列ダウンロード数を制限
export UV_CONCURRENT_DOWNLOADS=4
```

---

## トラブルシューティング

### キャッシュのクリア
```bash
# キャッシュをクリア
uv cache clean

# 特定パッケージのキャッシュをクリア
uv cache clean requests
```

### 詳細なログを表示
```bash
# 詳細モードで実行
uv pip install requests -v

# さらに詳細なログ
uv pip install requests -vv
```

### 設定の確認
```bash
# uvの設定を表示
uv config

# システム情報を表示
uv version --verbose
```

---

## 参考リンク

- [公式ドキュメント](https://docs.astral.sh/uv/)
- [GitHub リポジトリ](https://github.com/astral-sh/uv)
- [変更履歴](https://github.com/astral-sh/uv/releases)

---

## まとめ

`uv`は、Pythonの開発環境を統一的かつ高速に管理できる強力なツールです。

**主な利点:**
- ⚡ pipより圧倒的に高速
- 🔧 パッケージ管理、仮想環境、Pythonバージョン管理を統合
- 📦 モダンな`pyproject.toml`ベースの管理
- 🔒 `uv.lock`による再現可能な環境
- 🛠️ CLIツールの隔離管理

**推奨される使い方:**
1. 新規プロジェクト: `uv init`でスタート
2. 既存プロジェクト: `uv sync`で環境構築
3. 日常開発: `uv run`でコマンド実行
4. CLIツール: `uv tool install`または`uvx`で管理

