# 1-1. 開発環境 (Vite)

この章では、Reactアプリケーションを開発するための環境構築について学びます。Viteを使用することで、高速な開発環境を構築できます。

---

## 目次

- [ViteによるReactプロジェクトの作成](#viteによるreactプロジェクトの作成)
- [プロジェクト構造の理解 (main.jsx, App.jsx)](#プロジェクト構造の理解-mainjsx-appjsx)
- [Strict Modeについて](#strict-modeについて)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## ViteによるReactプロジェクトの作成

### Viteとは
Vite（ヴィート）は、**モダンなフロントエンド開発ツール**です。従来のツールと比べて、開発サーバーの起動が非常に高速です。

### Viteの特徴
- **高速な起動**: 開発サーバーが瞬時に起動する
- **高速な更新**: ファイル変更時の更新が非常に速い
- **最適化されたビルド**: 本番環境用のビルドも高速
- **TypeScript対応**: TypeScriptを標準でサポート

### Viteプロジェクトの作成手順

#### 1. Node.jsのインストール確認

まず、Node.jsがインストールされているか確認します。

```bash
node --version
npm --version
```

#### 2. Viteプロジェクトの作成

```bash
npm create vite@latest my-react-app -- --template react
```

または、TypeScriptを使用する場合：

```bash
npm create vite@latest my-react-app -- --template react-ts
```

#### 3. プロジェクトディレクトリに移動

```bash
cd my-react-app
```

#### 4. 依存関係のインストール

```bash
npm install
```

#### 5. 開発サーバーの起動

```bash
npm run dev
```

ブラウザで `http://localhost:5173` にアクセスすると、Reactアプリケーションが表示されます。

### 実践例: Viteプロジェクトの作成

```bash
# プロジェクトの作成
npm create vite@latest my-first-react-app -- --template react

# プロジェクトディレクトリに移動
cd my-first-react-app

# 依存関係のインストール
npm install

# 開発サーバーの起動
npm run dev
```

### コマンドの説明

- **`npm create vite@latest`**: 最新版のViteを使用してプロジェクトを作成
- **`my-first-react-app`**: プロジェクト名（任意の名前を指定可能）
- **`--template react`**: Reactテンプレートを使用
- **`npm install`**: package.jsonに記載された依存関係をインストール
- **`npm run dev`**: 開発サーバーを起動

### 実践例: プロジェクト作成後の確認

プロジェクトが正常に作成されたか確認するため、以下のコマンドを実行します：

```bash
# プロジェクト構造の確認
ls -la

# package.jsonの確認
cat package.json
```

### よく使用されるnpmコマンド

```bash
# 開発サーバーの起動
npm run dev

# 本番環境用のビルド
npm run build

# ビルド結果のプレビュー
npm run preview

# リンターの実行（ESLintが設定されている場合）
npm run lint
```

---

## プロジェクト構造の理解 (main.jsx, App.jsx)

### プロジェクト構造

Viteで作成されたReactプロジェクトの基本的な構造は以下の通りです：

```
my-react-app/
├── node_modules/          # 依存関係（自動生成）
├── public/                # 静的ファイル
│   └── vite.svg
├── src/                   # ソースコード
│   ├── assets/            # 画像やCSSなどのリソース
│   ├── App.jsx            # メインのAppコンポーネント
│   ├── App.css            # Appコンポーネントのスタイル
│   ├── index.css           # グローバルスタイル
│   └── main.jsx           # エントリーポイント
├── index.html             # HTMLテンプレート
├── package.json           # プロジェクトの設定と依存関係
├── vite.config.js         # Viteの設定ファイル
└── README.md              # プロジェクトの説明
```

### main.jsx - エントリーポイント

`main.jsx`は、**アプリケーションのエントリーポイント**です。ReactアプリケーションをDOMにマウントします。

#### main.jsxの基本構造

```jsx
// src/main.jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

#### main.jsxの各部分の説明

1. **`import React from 'react'`**: Reactライブラリをインポート
2. **`import ReactDOM from 'react-dom/client'`**: React DOMクライアントをインポート（React 18以降）
3. **`import App from './App.jsx'`**: Appコンポーネントをインポート
4. **`import './index.css'`**: グローバルスタイルをインポート
5. **`ReactDOM.createRoot()`**: React 18の新しいAPI。ルートを作成
6. **`document.getElementById('root')`**: HTMLの`<div id="root">`要素を取得
7. **`.render()`**: Appコンポーネントをレンダリング

### App.jsx - メインコンポーネント

`App.jsx`は、**アプリケーションのメインコンポーネント**です。ここにアプリケーションの主要な構造を記述します。

#### App.jsxの基本構造

```jsx
// src/App.jsx
import { useState } from 'react'
import './App.css'

function App() {
  const [count, setCount] = useState(0)

  return (
    <div className="App">
      <header className="App-header">
        <h1>React App</h1>
        <button onClick={() => setCount((count) => count + 1)}>
          count is {count}
        </button>
      </header>
    </div>
  )
}

export default App
```

#### App.jsxの各部分の説明

1. **`import { useState } from 'react'`**: useStateフックをインポート（状態管理用）
2. **`import './App.css'`**: Appコンポーネント専用のスタイルをインポート
3. **`function App()`**: Appコンポーネントの定義
4. **`useState(0)`**: カウントの初期値を0に設定
5. **`return (...)`**: JSXを返す（コンポーネントのUI）
6. **`export default App`**: Appコンポーネントをエクスポート

### index.html - HTMLテンプレート

`index.html`は、**アプリケーションのHTMLテンプレート**です。

#### index.htmlの基本構造

```html
<!doctype html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>React App</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

#### index.htmlの各部分の説明

1. **`<div id="root"></div>`**: Reactアプリケーションがマウントされる要素
2. **`<script type="module" src="/src/main.jsx"></script>`**: main.jsxをモジュールとして読み込む

### 実践例: プロジェクト構造の理解

```jsx
// src/main.jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './index.css'

// React 18の新しいAPIを使用
const root = ReactDOM.createRoot(document.getElementById('root'))

root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)

// 実践例: 複数のコンポーネントをインポート
// import Header from './components/Header.jsx'
// import Footer from './components/Footer.jsx'
```

```jsx
// src/App.jsx
import { useState } from 'react'
import './App.css'

function App() {
  const [message, setMessage] = useState('Hello, React!')

  return (
    <div className="App">
      <h1>{message}</h1>
      <button onClick={() => setMessage('こんにちは、React!')}>
        メッセージを変更
      </button>
    </div>
  )
}

export default App
```

```html
<!-- index.html -->
<!doctype html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>My React App</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

---

## Strict Modeについて

### Strict Modeとは
Strict Modeは、**Reactアプリケーションの潜在的な問題を検出するための開発ツール**です。本番環境では影響しません。

### Strict Modeの特徴
- **問題の検出**: コンポーネントの潜在的な問題を警告
- **開発専用**: 本番環境では無効化される
- **二重レンダリング**: 開発時にコンポーネントを2回レンダリングして問題を検出

### Strict Modeの有効化

Strict Modeは、`main.jsx`で`<React.StrictMode>`で囲むことで有効化されます。

```jsx
// src/main.jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

### Strict Modeが検出する問題

1. **安全でないライフサイクルメソッド**: 非推奨のライフサイクルメソッドの使用
2. **レガシーな文字列ref API**: 古いref APIの使用
3. **非推奨のfindDOMNode**: findDOMNodeの使用
4. **予期しない副作用**: 副作用の検出

### 実践例: Strict Modeの動作確認

```jsx
// src/App.jsx
import { useState, useEffect } from 'react'
import './App.css'

function App() {
  const [count, setCount] = useState(0)

  // Strict Modeでは、開発時にこのuseEffectが2回実行される
  useEffect(() => {
    console.log('コンポーネントがマウントされました')
    
    // クリーンアップ関数
    return () => {
      console.log('コンポーネントがアンマウントされました')
    }
  }, [])

  return (
    <div className="App">
      <h1>カウント: {count}</h1>
      <button onClick={() => setCount(count + 1)}>
        カウントを増やす
      </button>
    </div>
  )
}

export default App
```

開発環境では、コンソールに以下のように表示されます（Strict Modeが有効な場合）：

```
コンポーネントがマウントされました
コンポーネントがアンマウントされました
コンポーネントがマウントされました
```

### Strict Modeを無効化する場合

Strict Modeを無効化したい場合は、`<React.StrictMode>`を削除します：

```jsx
// Strict Modeを無効化
ReactDOM.createRoot(document.getElementById('root')).render(
  <App />
)
```

ただし、**開発中はStrict Modeを有効にしておくことを強く推奨**します。

### 実践例: Strict Modeの効果

```jsx
// src/App.jsx
import { useState, useEffect } from 'react'
import './App.css'

function App() {
  const [count, setCount] = useState(0)
  const [mounted, setMounted] = useState(false)

  useEffect(() => {
    // Strict Modeでは、開発時にこのuseEffectが2回実行される
    console.log('useEffectが実行されました')
    setMounted(true)
    
    return () => {
      console.log('クリーンアップが実行されました')
      setMounted(false)
    }
  }, [])

  return (
    <div className="App">
      <h1>Strict Modeの動作確認</h1>
      <p>マウント状態: {mounted ? 'マウント済み' : 'アンマウント済み'}</p>
      <p>カウント: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        カウントを増やす
      </button>
    </div>
  )
}

export default App
```

---

## まとめ

この章では、React開発環境の構築について学びました。

### 学んだこと
- ViteによるReactプロジェクトの作成: 高速な開発環境の構築
- プロジェクト構造の理解: main.jsx、App.jsx、index.htmlの役割
- Strict Mode: 開発時の問題検出ツール

### 重要なポイント
1. **Vite**: 高速な開発ツール
2. **main.jsx**: アプリケーションのエントリーポイント
3. **App.jsx**: メインコンポーネント
4. **index.html**: HTMLテンプレート
5. **Strict Mode**: 開発時の問題検出

### 次のステップ
次の章では、JSXのルールと記法について詳しく学びます。

---

## 演習問題

### 問題1: Viteプロジェクトの作成
Viteを使用して、`my-practice-app`という名前のReactプロジェクトを作成してください。

<details>
<summary>解答例</summary>

```bash
# プロジェクトの作成
npm create vite@latest my-practice-app -- --template react

# プロジェクトディレクトリに移動
cd my-practice-app

# 依存関係のインストール
npm install

# 開発サーバーの起動
npm run dev
```
</details>

### 問題2: main.jsxの理解
`main.jsx`で、`ReactDOM.createRoot()`を使用してAppコンポーネントをレンダリングするコードを書いてください。

<details>
<summary>解答例</summary>

```jsx
// src/main.jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.jsx'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```
</details>

### 問題3: App.jsxの作成
`App.jsx`で、「Hello, React!」というメッセージを表示するシンプルなコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```jsx
// src/App.jsx
import './App.css'

function App() {
  return (
    <div className="App">
      <h1>Hello, React!</h1>
    </div>
  )
}

export default App
```
</details>

### 問題4: Strict Modeの確認
Strict Modeを有効にした状態で、`useEffect`を使用してコンソールにログを出力するコンポーネントを作成し、開発時に2回実行されることを確認してください。

<details>
<summary>解答例</summary>

```jsx
// src/App.jsx
import { useEffect } from 'react'
import './App.css'

function App() {
  useEffect(() => {
    console.log('コンポーネントがマウントされました')
    
    return () => {
      console.log('コンポーネントがアンマウントされました')
    }
  }, [])

  return (
    <div className="App">
      <h1>Strict Modeの確認</h1>
    </div>
  )
}

export default App
```

開発環境では、コンソールに2回ログが表示されます（Strict Modeが有効な場合）。
</details>

### 問題5: プロジェクト構造の確認
作成したプロジェクトの`src`ディレクトリ内のファイルを確認し、それぞれの役割を説明してください。

<details>
<summary>解答例</summary>

プロジェクト構造の確認：

```bash
# srcディレクトリの内容を確認
ls -la src/
```

主なファイルの役割：
- **main.jsx**: アプリケーションのエントリーポイント。ReactアプリをDOMにマウント
- **App.jsx**: メインコンポーネント。アプリケーションの主要な構造を定義
- **App.css**: Appコンポーネント専用のスタイル
- **index.css**: グローバルスタイル
- **assets/**: 画像やその他のリソースを格納
</details>

---

お疲れ様でした！次の章に進みましょう。

