| 目的 | nvm (Node.js) のコマンド | pyenv (Python) のコマンド | 補足 |
| :--- | :--- | :--- | :--- |
| インストール可能バージョン一覧 | `nvm ls-remote` | `pyenv install --list` | インストールできる全バージョンを表示。 |
| 最新のLTSをインストール | `nvm install --lts` | `pyenv install <最新LTSバージョン>` | nvmは`--lts`フラグあり。pyenvはLTS版のバージョン番号を明示的に指定。 |
| 特定のバージョンをインストール | `nvm install <バージョン>` | `pyenv install <バージョン>` | 指定したバージョンをインストール。 |
| インストール済みバージョン一覧 | `nvm ls` | `pyenv versions` | 現在nvm/pyenvでインストールされているバージョンを表示。 |
| 現在使用中のバージョン表示 | `nvm current` | `pyenv version` | 現在アクティブなバージョンを表示。 |
| グローバル（デフォルト）設定 | `nvm alias default <バージョン>` | `pyenv global <バージョン>` | 全てのシェルセッションでデフォルトで使用。 |
| ディレクトリ（プロジェクト）単位で使用 | `nvm use <バージョン>` | `pyenv local <バージョン>` | カレントディレクトリに設定ファイルを作成して適用。プロジェクトごとの切り替えに最適。 |
| 現在のセッションで使用 | `nvm use <バージョン>` | `pyenv shell <バージョン>` | 現在のターミナルのみで一時的に使用。 |
| アンインストール | `nvm uninstall <バージョン>` | `pyenv uninstall <バージョン>` | 指定したバージョンを環境から削除。 |
| 現在の環境パスを確認 | `which node` / `which npm` | `which python` / `which pip` | 実際にどのバージョンの実行ファイルが使われているか確認。 |
| ヘルプ表示 | `nvm help` | `pyenv help` | 各コマンドの詳細な使い方を表示。 |
