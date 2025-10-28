# Ruff VS Code 拡張機能ガイド

Ruffは、Rustで書かれた非常に高速なPythonリンターおよびコードフォーマッターです。このドキュメントでは、Visual Studio Code拡張機能の使い方を解説します。

> **NOTE**
> Ruff v0.4.5以降、Rustで書かれた言語サーバー `ruff server` が組み込まれました。
> v0.5.3で安定版となり、拡張機能で自動的に使用されるようになっています。

Ruffは、Flake8（とそのプラグイン）、Black、isort、pyupgradeなどを置き換えることができ、それぞれのツールよりも数十倍から数百倍高速に動作します。

## 主な機能

- **"Quick Fix"**: 未使用のインポートなど、自動修正可能な違反をワンクリックで修正します。
- **"Fix all"**: 全ての自動修正可能な違反を一括で修正します。
- **"Format Document"**: Black互換のコードフォーマットを適用します。
- **"Organize Imports"**: isort互換のインポート並べ替えと重複削除を行います。

## 使い方

VS Codeにインストールすると、PythonファイルまたはJupyter Notebookファイルを開いたり編集したりする際に、Ruffが自動的に実行されます。

## Jupyter Notebookのサポート

Ruff v0.1.3以降、Jupyter Notebookに対応しています。診断、コードアクション、フォーマットなど、通常のPythonファイルで利用できる全ての機能がNotebookでも利用可能です。

Ruff v0.6.0でこの機能は安定版となり、デフォルトで有効になりました。それ以前のバージョンでは、設定ファイルで明示的に有効にする必要があります。

```toml
# ruff.toml or pyproject.toml
[tool.ruff]
extend-include = ["*.ipynb"]
```

## VS Codeの設定 (`settings.json`)

`settings.json`に必要な設定を追加することで、保存時の自動フォーマットや修正が有効になります。

### 保存時にフォーマット・修正・インポート整理をまとめて行う

以下の設定が最も一般的です。保存時にフォーマット、Lint修正、インポート整理がすべて自動で行われます。

**Pythonファイル用:**
```json
{
  "[python]": {
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit",
      "source.organizeImports": "explicit"
    },
    "editor.defaultFormatter": "charliermarsh.ruff"
  }
}
```

**Jupyter Notebook用:**
```json
{
  "notebook.formatOnSave.enabled": true,
  "notebook.codeActionsOnSave": {
    "notebook.source.fixAll": "explicit",
    "notebook.source.organizeImports": "explicit"
  },
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff"
  }
}
```

## Rust製言語サーバー (`ruff server`) の利用

Ruff拡張機能は、以下の条件を満たす場合に自動的にRust製の高速な言語サーバー (`ruff server`) を使用します。

1.  `ruff`の実行ファイルのバージョンが`0.5.3`以上であること。
2.  `ruff.nativeServer`設定が`auto`（デフォルト）であること。

明示的に有効/無効にすることも可能です。

```json
{
  // Rust製サーバーを強制的に有効にする
  "ruff.nativeServer": "on"
}
```

> **WARNING**
> 従来のPython製言語サーバー (`ruff-lsp`) は非推奨であり、将来のリリースで削除される予定です。Rust製サーバーへの移行を推奨します。

## Ruff自体の設定方法

プロジェクトのルールセットや設定は、`pyproject.toml`、`ruff.toml`、または`.ruff.toml`ファイルで行うことが推奨されます。これにより、チームメンバー全員が同じ設定を共有できます。

VS Codeの設定でも一部のRuffの設定を上書きできます。

```json
{
  "ruff.lineLength": 88,
  "ruff.lint.select": ["E", "F", "W", "I"]
}
```

カスタム設定ファイルのパスを指定することも可能です。

```json
{
  "ruff.configuration": "/path/to/your/ruff.toml"
}
```

## 主なコマンド

コマンドパレット (`Ctrl+Shift+P` or `Cmd+Shift+P`) から以下のコマンドを実行できます。

| コマンド | 説明 |
|---|---|
| `Ruff: Fix all auto-fixable problems` | 全ての自動修正可能な問題を修正します。 |
| `Ruff: Format Imports` | インポートを整理します。 |
| `Ruff: Format Document` | ドキュメント全体をフォーマットします。 |
| `Ruff: Restart Server` | 言語サーバーを再起動します。 |
| `Ruff: Show client logs` | Ruffのクライアントログを表示します。 |
| `Ruff: Show server logs` | Ruffの言語サーバーログを表示します。 |

## トラブルシューティング

問題が発生した場合は、まずアウトプットチャネルのログを確認してください。

1. コマンドパレットを開きます。
2. `Output: Show Output Channels` を選択します。
3. "Ruff" または "Ruff Language Server" を選択してログを確認します。

ログレベルは `settings.json` で変更できます。

```json
{
  "ruff.logLevel": "info" // "debug" などに変更可能
}
```
