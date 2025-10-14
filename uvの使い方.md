# uvの使い方ガイド

## 目次
1. [uvとは](#uvとは)
2. [インストール](#インストール)
3. [基本的な使い方](#基本的な使い方)
4. [プロジェクト管理](#プロジェクト管理)
5. [ツール管理](#ツール管理)
6. [Jupyter Notebookとの連携](#jupyter-notebookとの連携)
7. [チーム開発での運用](#チーム開発での運用)
8. [pipとの対応表](#pipとの対応表)
9. [トラブルシューティング](#トラブルシューティング)

---

## uvとは

`uv`は、Rustで書かれた超高速Python パッケージマネージャーです。

### メリット
- ⚡ **速度**: pip/poetryより10〜100倍速い
- 🎯 **オールインワン**: Pythonバージョン管理、仮想環境、パッケージ管理が1つのツールで完結
- 🔒 **再現性**: `uv.lock`で完全な環境再現
- 📦 **pip互換**: 既存のpipコマンドがそのまま使える
- 💾 **キャッシュ**: 一度DLしたパッケージは再利用

### 置き換えるツール
```
pyenv + venv + pip + pip-tools + poetry
        ↓
       uv だけ！
```

---

## インストール

### Linux/macOS
```bash
# 公式インストーラー（推奨）
curl -LsSf https://astral.sh/uv/install.sh | sh

# または pip経由（推奨されない）
pip install uv
```

### Windows
```powershell
# PowerShellで実行
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"

# または pip経由（推奨されない）
pip install uv
```

### バージョン確認
```bash
uv --version
```

### アンインストール

```bash
# Linux/macOS
rm ~/.cargo/bin/uv

# Windows
# インストーラーでインストールした場合は、PATHから削除
```

---

## 基本的な使い方

### 1. Pythonのインストール

```bash
# 利用可能なPythonバージョンを確認
uv python list

# Python 3.11.0をインストール
uv python install 3.11.0

# 複数バージョンをインストール
uv python install 3.11.0 3.12.0

# インストール済みPythonを確認
uv python list --only-installed
```

### 2. 仮想環境の作成

```bash
# デフォルトのPythonで仮想環境を作成
uv venv

# Pythonバージョンを指定
uv venv --python 3.11.0

# 別の場所に作成
uv venv --python 3.11.0 my_env

# 仮想環境を有効化
source .venv/bin/activate

# 無効化
deactivate
```

### 3. パッケージのインストール

```bash
# 単一パッケージをインストール
uv pip install pandas

# 複数パッケージ
uv pip install pandas numpy requests

# バージョン指定
uv pip install pandas==2.3.2
uv pip install "pandas>=2.0,<3.0"

# requirements.txtから
uv pip install -r requirements.txt

# アップグレード
uv pip install --upgrade pandas

# アンインストール
uv pip uninstall pandas
```

### 4. パッケージ一覧

```bash
# インストール済みパッケージを表示
uv pip list

# requirements.txt形式で出力
uv pip freeze > requirements.txt

# 特定パッケージの情報
uv pip show pandas
```

---

## プロジェクト管理

### 新規プロジェクトの作成

```bash
# プロジェクト初期化
uv init my-project
cd my-project

# または既存ディレクトリで
cd existing-project
uv init

# これで pyproject.toml が作成される
```

### pyproject.toml を使った管理

#### パッケージの追加

```bash
# 本番用パッケージを追加
uv add pandas numpy

# 開発用パッケージを追加
uv add --dev pytest black ruff

# オプショナルグループを指定
uv add --group docs sphinx

# pyproject.tomlとuv.lockが自動更新される
```

#### パッケージの削除

```bash
# パッケージを削除
uv remove pandas

# 開発用パッケージを削除
uv remove --dev pytest
```

#### インストール（同期）

```bash
# pyproject.tomlとuv.lockに基づいてインストール
uv sync

# 開発用依存関係も含む
uv sync --all-extras

# 本番用のみ（開発用を除外）
uv sync --no-dev
```

### requirements.txt からの移行

```bash
# 既存のrequirements.txtがある場合

# 方法1: requirements.txtからpyproject.tomlに変換
uv init
uv add --requirements requirements.txt

# 方法2: 直接インストール（pyproject.tomlなし）
uv venv
uv pip install -r requirements.txt
```

### ロックファイル

```bash
# 依存関係を解決してuv.lockを生成/更新
uv lock

# ロックファイルを更新（最新版に）
uv lock --upgrade

# 特定パッケージのみ更新
uv lock --upgrade-package pandas
```

---

## プロジェクトの実行

### スクリプト実行

```bash
# uv run を使うと仮想環境を自動認識
uv run python script.py

# モジュールとして実行
uv run python -m mymodule

# アクティベートしてから実行（従来の方法）
source .venv/bin/activate
python script.py
```

---

## ツール管理

uvは、CLIツール（black、ruff、pytestなど）を隔離された環境で管理できます。
プロジェクトの依存関係と分離してグローバルに使えるツールを管理できる強力な機能です。

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
uvx black .

# パッケージを一時的に追加して実行
uvx --with numpy python -c "import numpy; print(numpy.__version__)"
```

**`uvx`の利点**:
- インストール不要で即実行
- 一度だけ使いたいツールに最適
- 実行後は環境を汚さない

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

# Pythonバージョンを指定してアップグレード
uv tool upgrade ruff --python 3.12
```

**主なオプション**:
- `--all`: インストール済みすべてのツールをアップグレード
- `--reinstall`: ツール環境にインストールされている全パッケージを再インストール
- `--upgrade-package <パッケージ名>`: 特定の依存パッケージをアップグレード
- `--python <バージョン>`: 使用するPythonバージョンを指定

### ツールのアンインストール

```bash
# ツールをアンインストール
uv tool uninstall ruff

# 複数のツールを一度にアンインストール
uv tool uninstall ruff black pytest
```

### インストール済みツールの確認

```bash
# ツール一覧を表示
uv tool list

# ツールのインストールディレクトリを表示
uv tool dir

# 実行ファイルのディレクトリを表示
uv tool dir --bin
```

**ツールのインストール先**:
- Linux/macOS: `~/.local/share/uv/tools`
- Windows: `~\AppData\Roaming\uv\data\tools`
- 環境変数`UV_TOOL_DIR`で変更可能

### よく使うツールの例

```bash
# Linter/Formatter
uv tool install ruff
uv tool install black
uv tool install isort

# テスト
uv tool install pytest
uv tool install tox

# Jupyter
uv tool install jupyter

# その他
uv tool install pipx
uv tool install cookiecutter
```

---

## Jupyter Notebookとの連携

### セットアップ

```bash
# Jupyterをインストール（開発用依存関係として）
uv add --dev ipykernel jupyter

# または既にrequirements.txtに含まれている場合
uv pip install -r requirements.txt

# Jupyter Kernelとして登録
source .venv/bin/activate
python -m ipykernel install --user --name=my-project --display-name "Python (my-project)"
```

### Jupyter起動

```bash
# 方法1: アクティベートしてから
source .venv/bin/activate
jupyter notebook

# 方法2: uv runを使う
uv run jupyter notebook
```

### Notebookでの使用

1. Jupyter Notebookを開く
2. **Kernel** → **Change kernel**
3. 登録したkernel（例: "Python (my-project)"）を選択

### カーネルの確認・削除

```bash
# インストール済みkernelを確認
jupyter kernelspec list

# kernelを削除
jupyter kernelspec remove my-project
```

---

## チーム開発での運用

### 初回セットアップ（新規メンバー）

```bash
# 1. リポジトリをクローン
git clone <repository-url>
cd project

# 2. 環境を完全再現
uv sync

# これだけ！
# - Pythonがなければ自動ダウンロード
# - .venv/が作成される
# - uv.lockに基づいて完全一致する環境が構築される
```

### 開発フロー

#### パッケージを追加する場合

```bash
# 1. パッケージを追加
uv add requests

# 2. 自動で更新されるファイル
# - pyproject.toml
# - uv.lock

# 3. コミット
git add pyproject.toml uv.lock
git commit -m "Add requests package"
git push
```

#### 他のメンバーが追加したパッケージを取得

```bash
# 1. 最新を取得
git pull

# 2. 環境を同期
uv sync

# これで新しいパッケージが自動でインストールされる
```

### .gitignore に追加すべきもの

```gitignore
# Python仮想環境
.venv/
venv/
env/

# uvのキャッシュ（オプション、通常は共有しない）
.uv_cache/

# pyproject.tomlとuv.lockは必ずコミットする（除外しない）
```

---

## pipとの対応表

uvは、pipのコマンドとほぼ互換性があります。既存のpipコマンドに`uv`を追加するだけで使えます。

| pip | uv | 説明 |
|-----|-----|------|
| `pip install package` | `uv pip install package` | パッケージをインストール |
| `pip install -r requirements.txt` | `uv pip install -r requirements.txt` | requirements.txtからインストール |
| `pip install -e .` | `uv pip install -e .` | 編集可能モードでインストール |
| `pip install --upgrade package` | `uv pip install --upgrade package` | パッケージをアップグレード |
| `pip uninstall package` | `uv pip uninstall package` | パッケージをアンインストール |
| `pip list` | `uv pip list` | インストール済みパッケージ一覧 |
| `pip freeze` | `uv pip freeze` | requirements.txt形式で出力 |
| `pip show package` | `uv pip show package` | パッケージ情報を表示 |
| `python -m venv .venv` | `uv venv` | 仮想環境を作成 |
| `pip search package` | `uv pip search package` | パッケージを検索 |

### uvにしかない便利なコマンド

| コマンド | 説明 |
|---------|------|
| `uv init` | プロジェクトを初期化 |
| `uv add package` | pyproject.tomlにパッケージを追加 |
| `uv remove package` | pyproject.tomlからパッケージを削除 |
| `uv sync` | pyproject.toml/uv.lockから環境を同期 |
| `uv lock` | 依存関係をロック |
| `uv run command` | プロジェクト環境でコマンドを実行 |
| `uv tool install tool` | グローバルツールをインストール |
| `uvx tool` | ツールを一時的に実行 |
| `uv python install 3.12` | Pythonバージョンをインストール |

---

## pyproject.toml の構造

### 基本構造

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "プロジェクトの説明"
readme = "README.md"
requires-python = ">=3.11"

# 本番環境で必要なパッケージ
dependencies = [
    "pandas>=2.3.0",
    "numpy>=1.26.0",
    "requests>=2.31.0",
]

# 開発環境でのみ必要なパッケージ
[project.optional-dependencies]
dev = [
    "pytest>=7.4.0",
    "black>=23.0.0",
    "ruff>=0.1.0",
    "ipykernel>=6.25.0",
    "jupyter>=1.0.0",
]

test = [
    "pytest>=7.4.0",
    "pytest-cov>=4.1.0",
]

docs = [
    "sphinx>=7.0.0",
]

# ビルドシステム
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

# ツール設定（オプション）
[tool.black]
line-length = 88

[tool.ruff]
select = ["E", "F"]
line-length = 88
```

### インストール方法の違い

```bash
# 本番用のみ
uv pip install .

# 本番用 + 開発用
uv pip install -e ".[dev]"

# 本番用 + テスト用
uv pip install -e ".[test]"

# 全て
uv pip install -e ".[dev,test,docs]"

# または uv sync
uv sync                  # 本番用のみ
uv sync --all-extras     # 全てのオプション含む
```

---

## 便利なコマンド集

### 環境情報

```bash
# uvのバージョン
uv --version

# システム情報を表示（詳細）
uv version --verbose

# Python環境の確認
uv python list --only-installed

# 利用可能なすべてのPythonバージョン
uv python list

# 現在のPythonバージョンを確認
uv python find

# 現在のPythonパス
uv run python -c "import sys; print(sys.executable)"

# uvの設定を表示
uv config
```

### パッケージ管理

```bash
# 依存関係のツリー表示
uv pip list --format=tree

# 古いパッケージを確認
uv pip list --outdated

# 依存関係の検証
uv lock --check

# キャッシュをクリア
uv cache clean

# 特定パッケージのキャッシュをクリア
uv cache clean requests

# キャッシュサイズを確認
du -sh ~/.cache/uv
```

---

## よくある使用パターン

### パターン1: 既存プロジェクト（requirements.txtのみ）

```bash
# プロジェクトをクローン
git clone <repo>
cd project

# uvで仮想環境を作成してインストール
uv venv --python 3.11.0
source .venv/bin/activate
uv pip install -r requirements.txt

# 必要に応じてJupyter kernel登録
python -m ipykernel install --user --name=project
```

### パターン2: pyproject.toml + uv.lock があるプロジェクト

```bash
# プロジェクトをクローン
git clone <repo>
cd project

# これだけで完全再現
uv sync
```

### パターン3: 新規プロジェクト作成

```bash
# プロジェクト作成
uv init my-project
cd my-project

# パッケージを追加
uv add pandas numpy
uv add --dev pytest jupyter

# 開発開始
uv run python main.py
```

---

## requirements.txt vs pyproject.toml

### requirements.txt のみ使う場合

**メリット**:
- シンプル
- 既存のワークフローと互換

**デメリット**:
- 本番/開発環境を分離できない
- 厳密なロックファイルがない

```bash
# 使い方
uv venv
uv pip install -r requirements.txt
uv pip freeze > requirements.txt  # 更新
```

### pyproject.toml を使う場合

**メリット**:
- 本番/開発環境を分離
- `uv.lock`で完全な再現性
- メタデータも管理

**デメリット**:
- 初期セットアップに少し手間

```bash
# 使い方
uv add pandas           # 自動でpyproject.tomlとuv.lock更新
uv sync                 # 環境を同期
```

### 移行方法

```bash
# requirements.txt → pyproject.toml
uv init
uv add --requirements requirements.txt

# これでpyproject.tomlとuv.lockが生成される
```

---

## Docker環境での使用

### Dockerfile例（本番環境）

```dockerfile
FROM python:3.11-slim

# uvをインストール
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

WORKDIR /app

# 依存関係ファイルをコピー
COPY pyproject.toml uv.lock ./

# 本番用依存関係のみインストール
RUN uv sync --no-dev --frozen

# アプリケーションコードをコピー
COPY . .

CMD ["uv", "run", "python", "main.py"]
```

### docker-compose.yml例（開発環境）

```yaml
version: '3.8'

services:
  app:
    image: python:3.11
    volumes:
      - .:/app
    working_dir: /app
    command: bash -c "pip install uv && uv sync && uv run python main.py"
```

---

## 環境変数

uvの動作は環境変数で細かくカスタマイズできます。

### 主要な環境変数

```bash
# uvのデータディレクトリを変更
export UV_CACHE_DIR=~/.cache/uv

# ツールのインストールディレクトリを変更
export UV_TOOL_DIR=~/.local/share/uv/tools

# Pythonバージョンのインストール先を変更
export UV_PYTHON_INSTALL_DIR=~/.local/share/uv/python

# 並列ダウンロード数を制限
export UV_CONCURRENT_DOWNLOADS=4

# オフラインモード（キャッシュのみ使用）
export UV_OFFLINE=1

# インデックスURLを変更（プライベートリポジトリなど）
export UV_INDEX_URL=https://pypi.example.com/simple

# 追加のインデックスURL
export UV_EXTRA_INDEX_URL=https://pypi.example.com/simple

# システムのPythonを使用
export UV_SYSTEM_PYTHON=1

# 詳細ログを有効化
export UV_VERBOSE=1
```

### デフォルトのディレクトリ

**Linux/macOS**:
- キャッシュ: `~/.cache/uv`
- データ: `~/.local/share/uv`
- ツール: `~/.local/share/uv/tools`
- Python: `~/.local/share/uv/python`

**Windows**:
- キャッシュ: `%LOCALAPPDATA%\uv\cache`
- データ: `%APPDATA%\uv\data`
- ツール: `%APPDATA%\uv\data\tools`
- Python: `%APPDATA%\uv\data\python`

---

## トラブルシューティング

### 問題: パッケージがインストールできない

```bash
# キャッシュをクリア
uv cache clean

# 再インストール
uv sync --reinstall
```

### 問題: Pythonバージョンが見つからない

```bash
# 明示的にインストール
uv python install 3.11.0

# インストール済みを確認
uv python list --only-installed
```

### 問題: 依存関係の競合

```bash
# 詳細情報を表示
uv lock --verbose

# 特定パッケージのバージョンを固定
uv add "package==1.2.3"
```

### 問題: Jupyter Kernelが認識されない

```bash
# 仮想環境をアクティベートしてから登録
source .venv/bin/activate
python -m ipykernel install --user --name=project --display-name "Python (project)"

# 確認
jupyter kernelspec list
```

### 問題: .venvが古くなった

```bash
# 仮想環境を削除して再作成
rm -rf .venv
uv sync
```

---

## 参考リンク

- 公式ドキュメント: https://docs.astral.sh/uv/
- GitHub: https://github.com/astral-sh/uv
- PyPI: https://pypi.org/project/uv/

---

## まとめ

### uvを使うべき理由

1. **圧倒的な速度**: pipの10〜100倍高速
2. **オールインワン**: pyenv + venv + pip + poetry の機能を統合
3. **完全な再現性**: `uv.lock`で依存関係を完全にロック
4. **pip互換**: 既存のコマンドがそのまま使える
5. **モダンな開発体験**: `uv run`、`uv add`などの便利コマンド
6. **ツール管理**: グローバルCLIツールを隔離して管理
7. **Pythonバージョン管理**: pyenvの代替として使用可能

### 移行のススメ

**従来のツールチェーンから移行**:
```bash
# 従来
pyenv install 3.12.0
pyenv local 3.12.0
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# uv
uv venv --python 3.12.0
uv pip install -r requirements.txt
# または
uv init && uv sync
```

たった1〜2コマンドで完結！

### クイックリファレンス

| カテゴリ | コマンド | 説明 |
|---------|---------|------|
| **プロジェクト** | `uv init` | プロジェクト初期化 |
| | `uv add <pkg>` | パッケージ追加 |
| | `uv sync` | 環境同期 |
| | `uv run <cmd>` | コマンド実行 |
| **仮想環境** | `uv venv` | 仮想環境作成 |
| **パッケージ** | `uv pip install <pkg>` | インストール |
| | `uv pip list` | 一覧表示 |
| **ツール** | `uv tool install <tool>` | グローバルインストール |
| | `uvx <tool>` | 一時実行 |
| **Python** | `uv python install 3.12` | Pythonインストール |
| | `uv python pin 3.12` | バージョン固定 |

---

**最終更新**: 2025-10-14

