# uvの使い方 簡易版

## 基本的なワークフロー

### 新規プロジェクト
```bash
# 1. プロジェクト初期化 (init(pythonバージョン指定)で作られるのは.python-versionとpyproject.toml)
uv init --python 3.11 my-project
cd my-project

# 2. パッケージ追加 (uv addの時点で.venv(仮想環境)が作られる。)
uv add pandas numpy

# 3. 環境同期（仮想環境自動作成 + 依存関係インストール）
uv sync
```

### 既存プロジェクト（pyproject.tomlあり）
```bash
# これだけで完結
uv sync
```

**注意**: 既存のpyproject.tomlがある場合、`uv init`は実行できません（エラーになる）

### 既存プロジェクト（requirements.txtのみ）
```bash
# 1. 仮想環境作成
uv venv --python 3.11

# 2. 依存関係インストール
uv pip install -r requirements.txt
```

---

## 主要コマンド

| コマンド | 用途 |
|---------|------|
| `uv init` | プロジェクト初期化（pyproject.toml作成） |
| `uv sync` | 環境同期（仮想環境作成 + 依存関係インストール） |
| `uv add <pkg>` | パッケージ追加 |
| `uv venv` | 仮想環境のみ作成 |
| `uv pip install` | requirements.txtベースでインストール |

---

## よくある質問

### Q: pyenvは必要？
**A: 不要。uvだけでPythonバージョン管理可能**
```bash
uv python install 3.11  # Pythonインストール
uv venv --python 3.11    # 指定バージョンで仮想環境作成
```

### Q: .python-versionファイルは作られる？
**A: uv initでPythonバージョン指定すると自動作成**
```bash
uv init --python 3.11  # .python-versionが作成される
```

### Q: requirements.txtから移行したい
**A: pyproject.tomlに移行推奨**
```bash
uv init  # 新規プロジェクトの場合のみ
uv add --requirements requirements.txt  # pyproject.tomlに変換
# uv add -r requirements.txt  # でもいい
uv sync
```

**注意**: 既存のpyproject.tomlがある場合は`uv init`は使えません

### Q: 既存環境からrequirements.txtを作りたい
**A: uv pip freezeを使用**
```bash
uv pip freeze > requirements.txt
```

---

## まとめ

**推奨ワークフロー:**
1. `uv init --python 3.11` - プロジェクト初期化
2. `uv add <パッケージ>` - 依存関係追加
3. `uv sync` - 環境同期

**これだけで完結！**