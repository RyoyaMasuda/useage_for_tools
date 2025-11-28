# 1-1. Next.jsプロジェクトの始め方

この章では、Next.jsプロジェクトの作成方法について学びます。Next.jsを理解することで、Reactアプリケーションをより効率的に開発できるようになります。

---

## 目次

- [フレームワークとライブラリの違い](#フレームワークとライブラリの違い)
- [create-next-app によるセットアップ](#create-next-app-によるセットアップ)
- [プロジェクト構造の理解 (appディレクトリ, public, next.config.js)](#プロジェクト構造の理解-appディレクトリ-public-nextconfigjs)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## フレームワークとライブラリの違い

### フレームワークとは
フレームワークは、**アプリケーションの構造や流れを提供する**ものです。開発者は、フレームワークが定めたルールに従って開発します。

### ライブラリとは
ライブラリは、**必要な機能を選択して使用する**ものです。開発者が主導権を持ち、必要な部分だけを使用します。

### ReactとNext.jsの関係

| 特徴 | React | Next.js |
|------|-------|---------|
| **種類** | ライブラリ | フレームワーク |
| **役割** | UI構築 | フルスタックフレームワーク |
| **ルーティング** | 自分で実装 | 自動生成 |
| **SSR** | 自分で実装 | 標準サポート |
| **ビルド** | 自分で設定 | 自動設定 |

### React（ライブラリ）の特徴
- **柔軟性**: 必要な機能だけを使用できる
- **選択の自由**: ルーティング、状態管理などを自由に選択
- **設定が必要**: ビルドツールやルーティングを自分で設定

### Next.js（フレームワーク）の特徴
- **規約**: ファイルベースのルーティングなど、規約に従う
- **統合**: ルーティング、SSR、ビルドなどが統合されている
- **設定が少ない**: 初期設定が最小限

### 実践例: ReactとNext.jsの違い

#### React（ライブラリ）の場合
```jsx
// React Routerを自分で設定
import { BrowserRouter, Routes, Route } from 'react-router-dom'

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  )
}
```

#### Next.js（フレームワーク）の場合
```jsx
// ファイル構造で自動的にルーティング
// app/page.jsx → /
// app/about/page.jsx → /about

// 設定不要で自動的にルーティングが生成される
```

---

## create-next-app によるセットアップ

### create-next-appとは
`create-next-app`は、**Next.jsプロジェクトを簡単に作成するツール**です。必要な設定が自動的に行われます。

### インストール手順

#### 1. Node.jsの確認

まず、Node.jsがインストールされているか確認します。

```bash
node --version
npm --version
```

#### 2. Next.jsプロジェクトの作成

```bash
npx create-next-app@latest my-next-app
```

または、TypeScriptを使用する場合：

```bash
npx create-next-app@latest my-next-app --typescript
```

#### 3. セットアップオプション

対話形式で以下のオプションが表示されます：

- **TypeScript**: TypeScriptを使用するか
- **ESLint**: ESLintを使用するか
- **Tailwind CSS**: Tailwind CSSを使用するか
- **src/ directory**: srcディレクトリを使用するか
- **App Router**: App Routerを使用するか（推奨）
- **Import alias**: インポートエイリアスを設定するか

#### 4. プロジェクトディレクトリに移動

```bash
cd my-next-app
```

#### 5. 開発サーバーの起動

```bash
npm run dev
```

ブラウザで `http://localhost:3000` にアクセスすると、Next.jsアプリケーションが表示されます。

### 実践例: プロジェクトの作成

```bash
# 基本的なNext.jsプロジェクトの作成
npx create-next-app@latest my-first-next-app

# 対話形式で設定を選択
# ✓ Would you like to use TypeScript? ... Yes
# ✓ Would you like to use ESLint? ... Yes
# ✓ Would you like to use Tailwind CSS? ... Yes
# ✓ Would you like to use `src/` directory? ... No
# ✓ Would you like to use App Router? ... Yes
# ✓ Would you like to customize the default import alias? ... No

# プロジェクトディレクトリに移動
cd my-first-next-app

# 開発サーバーの起動
npm run dev
```

### よく使用されるnpmコマンド

```bash
# 開発サーバーの起動
npm run dev

# 本番環境用のビルド
npm run build

# ビルド結果のプレビュー
npm start

# リンターの実行
npm run lint
```

### 実践例: TypeScriptプロジェクトの作成

```bash
# TypeScriptを使用したNext.jsプロジェクトの作成
npx create-next-app@latest my-ts-next-app --typescript

# または、対話形式でTypeScriptを選択
npx create-next-app@latest my-ts-next-app
# TypeScriptを選択
```

---

## プロジェクト構造の理解 (appディレクトリ, public, next.config.js)

### プロジェクト構造

Next.jsプロジェクトの基本的な構造は以下の通りです：

```
my-next-app/
├── app/                    # App Router（Next.js 13+）
│   ├── layout.tsx         # ルートレイアウト
│   ├── page.tsx           # ホームページ
│   └── globals.css        # グローバルスタイル
├── public/                # 静的ファイル
│   └── next.svg          # 静的アセット
├── next.config.js         # Next.jsの設定ファイル
├── package.json           # プロジェクトの設定と依存関係
├── tsconfig.json          # TypeScriptの設定（TypeScript使用時）
└── README.md              # プロジェクトの説明
```

### appディレクトリ

`app`ディレクトリは、**App Routerを使用する場合のルートディレクトリ**です。

#### appディレクトリの特徴
- **ファイルベースルーティング**: ファイル構造でルーティングが自動生成
- **レイアウト**: `layout.tsx`で共通レイアウトを定義
- **ページ**: `page.tsx`でページコンポーネントを定義

#### 基本的な構造

```
app/
├── layout.tsx            # ルートレイアウト
├── page.tsx             # ホームページ (/)
├── about/
│   └── page.tsx         # /about ページ
└── contact/
    └── page.tsx         # /contact ページ
```

#### layout.tsx - ルートレイアウト

```tsx
// app/layout.tsx
import type { Metadata } from 'next'
import './globals.css'

export const metadata: Metadata = {
  title: 'My Next.js App',
  description: 'Next.jsアプリケーション',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="ja">
      <body>{children}</body>
    </html>
  )
}
```

#### page.tsx - ページコンポーネント

```tsx
// app/page.tsx
export default function Home() {
  return (
    <main>
      <h1>ホームページ</h1>
      <p>Next.jsアプリケーションへようこそ</p>
    </main>
  )
}
```

### publicディレクトリ

`public`ディレクトリは、**静的ファイルを配置するディレクトリ**です。

#### publicディレクトリの特徴
- **直接アクセス**: `/`から直接アクセスできる
- **静的アセット**: 画像、フォント、アイコンなどを配置

#### 使用例

```
public/
├── images/
│   └── logo.png         # /images/logo.png でアクセス
├── favicon.ico          # /favicon.ico でアクセス
└── robots.txt           # /robots.txt でアクセス
```

```tsx
// app/page.tsx
import Image from 'next/image'

export default function Home() {
  return (
    <main>
      <Image
        src="/images/logo.png"
        alt="ロゴ"
        width={200}
        height={200}
      />
    </main>
  )
}
```

### next.config.js

`next.config.js`は、**Next.jsの設定ファイル**です。

#### 基本的な設定

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  swcMinify: true,
}

module.exports = nextConfig
```

#### 実践例: よく使用される設定

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  // React Strict Modeを有効化
  reactStrictMode: true,
  
  // SWCミニファイを有効化
  swcMinify: true,
  
  // 画像の最適化設定
  images: {
    domains: ['example.com'],
  },
  
  // 環境変数の設定
  env: {
    CUSTOM_KEY: 'custom-value',
  },
  
  // リダイレクト設定
  async redirects() {
    return [
      {
        source: '/old-page',
        destination: '/new-page',
        permanent: true,
      },
    ]
  },
}

module.exports = nextConfig
```

### 実践例: プロジェクト構造の理解

```tsx
// app/layout.tsx
import type { Metadata } from 'next'
import './globals.css'

export const metadata: Metadata = {
  title: 'My Next.js App',
  description: 'Next.jsアプリケーションの説明',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="ja">
      <body>
        <header>
          <h1>ヘッダー</h1>
        </header>
        <main>{children}</main>
        <footer>
          <p>フッター</p>
        </footer>
      </body>
    </html>
  )
}
```

```tsx
// app/page.tsx
export default function Home() {
  return (
    <div>
      <h1>ホームページ</h1>
      <p>Next.jsアプリケーションへようこそ</p>
    </div>
  )
}
```

```tsx
// app/about/page.tsx
export default function About() {
  return (
    <div>
      <h1>Aboutページ</h1>
      <p>このページについて</p>
    </div>
  )
}
```

```css
/* app/globals.css */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', sans-serif;
}
```

---

## まとめ

この章では、Next.jsプロジェクトの作成方法について学びました。

### 学んだこと
- フレームワークとライブラリの違い: Reactはライブラリ、Next.jsはフレームワーク
- create-next-app: Next.jsプロジェクトを簡単に作成
- プロジェクト構造: appディレクトリ、publicディレクトリ、next.config.js

### 重要なポイント
1. **フレームワーク**: 構造や流れを提供（Next.js）
2. **ライブラリ**: 必要な機能を選択して使用（React）
3. **create-next-app**: プロジェクト作成ツール
4. **appディレクトリ**: App Routerのルートディレクトリ
5. **publicディレクトリ**: 静的ファイルを配置
6. **next.config.js**: Next.jsの設定ファイル

### 次のステップ
次の章では、ローカル開発環境の構築について詳しく学びます。

---

## 演習問題

### 問題1: Next.jsプロジェクトの作成
`create-next-app`を使用して、`my-practice-app`という名前のNext.jsプロジェクトを作成してください。

<details>
<summary>解答例</summary>

```bash
# プロジェクトの作成
npx create-next-app@latest my-practice-app

# 対話形式で設定を選択
# TypeScript: Yes
# ESLint: Yes
# Tailwind CSS: Yes
# src/ directory: No
# App Router: Yes

# プロジェクトディレクトリに移動
cd my-practice-app

# 開発サーバーの起動
npm run dev
```
</details>

### 問題2: appディレクトリの理解
`app`ディレクトリ内に`about`ページを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/about/page.tsx
export default function About() {
  return (
    <div>
      <h1>Aboutページ</h1>
      <p>このページについて</p>
    </div>
  )
}
```

`/about`にアクセスすると、このページが表示されます。
</details>

### 問題3: layout.tsxの作成
`app/layout.tsx`で共通レイアウトを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/layout.tsx
import type { Metadata } from 'next'
import './globals.css'

export const metadata: Metadata = {
  title: 'My App',
  description: 'Next.jsアプリケーション',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="ja">
      <body>
        <header>
          <h1>ヘッダー</h1>
        </header>
        <main>{children}</main>
        <footer>
          <p>フッター</p>
        </footer>
      </body>
    </html>
  )
}
```
</details>

### 問題4: publicディレクトリの使用
`public`ディレクトリに画像を配置し、`app/page.tsx`で表示してください。

<details>
<summary>解答例</summary>

```tsx
// app/page.tsx
import Image from 'next/image'

export default function Home() {
  return (
    <div>
      <h1>ホームページ</h1>
      <Image
        src="/images/logo.png"
        alt="ロゴ"
        width={200}
        height={200}
      />
    </div>
  )
}
```

`public/images/logo.png`に画像を配置します。
</details>

### 問題5: next.config.jsの設定
`next.config.js`でReact Strict Modeを有効化してください。

<details>
<summary>解答例</summary>

```javascript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
}

module.exports = nextConfig
```
</details>

---

お疲れ様でした！次の章に進みましょう。

