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

## VS Codeの設定 (`settings.json`) 詳細ガイド

`settings.json`に必要な設定を追加することで、Ruffの強力な機能を最大限に活用できます。ここでは、設定項目を一つずつ詳しく解説します。

---

### 1. 保存時の自動実行（推奨設定）

以下の設定が最も一般的で強力です。ファイルを保存するたびに、**フォーマット → Lint修正 → インポート整理**が自動で実行されます。

```json
{
  "[python]": {
    // 1. 保存時にフォーマッターを実行する
    "editor.formatOnSave": true,

    // 2. 保存時にコードアクション（修正や整理）を実行する
    "editor.codeActionsOnSave": {
      // "Fix All" を実行し、Ruffのルールに基づいて問題を自動修正する
      "source.fixAll": "explicit",
      // インポート文を自動で並べ替え、整理する
      "source.organizeImports": "explicit"
    },

    // 3. PythonファイルのデフォルトフォーマッターとしてRuffを指定する
    "editor.defaultFormatter": "charliermarsh.ruff"
  }
}
```

#### 設定項目の解説

- `"[python]"`: この設定をPythonファイル（`.py`）にのみ適用します。
- `"editor.formatOnSave": true`: ファイル保存時に、`defaultFormatter`として指定されたフォーマッターを実行します。
- `"editor.defaultFormatter": "charliermarsh.ruff"`: VS CodeのデフォルトフォーマッターとしてRuffを指定します。これにより、`formatOnSave`がRuffをトリガーします。
- `"editor.codeActionsOnSave"`: ファイル保存時に実行する「コードアクション」を指定します。
  - `"source.fixAll": "explicit"`: Ruffが検出した、自動修正可能な問題をすべて修正します（未使用の変数削除など）。
  - `"source.organizeImports": "explicit"`: `isort`と同様の機能で、インポート文をグループ化し、アルファベット順に並べ替えます。
- `"explicit"`: この値は、VS Codeがアクションを明示的に実行することを示します。`true`でも動作しますが、`explicit`が公式に推奨されています。

---

### 2. 個別機能の設定例

特定の機能だけを使いたい場合の設定例です。

#### A. フォーマット機能だけを使いたい場合

```json
{
  "[python]": {
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "charliermarsh.ruff"
  }
}
```
*`codeActionsOnSave`を設定しないことで、フォーマットのみが実行されます。*

#### B. Lintの自動修正だけを使いたい場合

```json
{
  "[python]": {
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit"
    },
    // フォーマットは行わないので、defaultFormatterをnullに設定
    "editor.defaultFormatter": null
  }
}
```

#### C. インポート整理だけを使いたい場合

```json
{
  "[python]": {
    "editor.codeActionsOnSave": {
      "source.organizeImports": "explicit"
    }
  }
}
```

---

### 3. Jupyter Notebookでの設定

Jupyter Notebook (`.ipynb`) ファイルに対しても同様の設定が可能です。設定のキーが少し異なります。

```json
{
  // Notebookで保存時の自動フォーマットを有効化
  "notebook.formatOnSave.enabled": true,

  // Notebookでの保存時コードアクション
  "notebook.codeActionsOnSave": {
    "notebook.source.fixAll": "explicit",
    "notebook.source.organizeImports": "explicit"
  },

  // Notebook内のPythonセルでもRuffをフォーマッターとして指定
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff"
  }
}
```

---

### 4. 他のツールと組み合わせる場合

#### Ruffで修正し、Blackでフォーマットする

Ruffの高速な自動修正機能を利用しつつ、フォーマットは実績のある`Black`に任せる構成です。

```json
{
  "[python]": {
    // 保存時にBlackフォーマッターを実行
    "editor.formatOnSave": true,
    // 保存時にRuffで自動修正を実行
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit"
    },
    // デフォルトのフォーマッターをBlackに指定
    "editor.defaultFormatter": "ms-python.black-formatter"
  }
}
```
*この場合、Ruffは修正のみを行い、コードの整形はBlackが行います。*

#### Ruffをリンターとして使い、isortでインポート整理する

インポート整理をRuffではなく、従来の`isort`拡張機能に任せたい場合の設定です。

```json
{
  "[python]": {
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit", // Ruffの修正は有効
      "source.organizeImports": "explicit" // isort拡張機能がこれを実行
    }
  },
  // Ruff自身のインポート整理機能を無効化
  "ruff.organizeImports": false
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
