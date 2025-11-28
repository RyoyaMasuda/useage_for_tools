# 2-2. アプリケーションのルーティング（SPAならではのナビゲーション）

この章では、Next.jsアプリケーションでページ遷移を実現する「ナビゲーション」について学びます。Next.jsの`<Link>`コンポーネントと`useRouter`フックを理解することで、シングルページアプリケーション（SPA）として効率的にページ遷移を実装できるようになります。

---

## 目次

- [<Link> コンポーネント](#link-コンポーネント)
- [useRouter フック](#userouter-フック)
- [サーバーサイドでのリダイレクト](#サーバーサイドでのリダイレクト)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## <Link> コンポーネント

### Linkとは
`Link`は、**Next.jsでページ遷移を行うためのコンポーネント**です。通常の`<a>`タグと似ていますが、ページ全体をリロードせずに遷移できます（クライアントサイドナビゲーション）。

### 日常生活での例
- **本の目次**: 目次（Link）を見て、該当するページ（ルート）に移動する
- **駅の案内板**: 案内板（Link）を見て、目的のホーム（ルート）に向かう
- **メニュー**: レストランのメニュー（Link）を見て、各料理（ルート）を選ぶ

### Linkの基本構文

```tsx
import Link from 'next/link';

<Link href="/パス">リンクテキスト</Link>
```

### Linkの特徴
1. **クライアントサイドナビゲーション**: ページ全体をリロードせずに遷移
2. **プリフェッチ**: リンクが表示された時点で、リンク先のページを事前に読み込む
3. **最適化**: Next.jsが自動的に最適化を行う
4. **型安全性**: TypeScriptを使用している場合、型チェックが可能

### 実践例: 基本的なLink

```tsx
// app/components/Navigation.tsx
import Link from 'next/link';

export default function Navigation() {
    return (
        <nav style={{
            backgroundColor: '#333',
            padding: '16px',
            display: 'flex',
            gap: '16px'
        }}>
            <Link 
                href="/" 
                style={{ color: '#fff', textDecoration: 'none' }}
            >
                ホーム
            </Link>
            <Link 
                href="/about" 
                style={{ color: '#fff', textDecoration: 'none' }}
            >
                About
            </Link>
            <Link 
                href="/products" 
                style={{ color: '#fff', textDecoration: 'none' }}
            >
                商品一覧
            </Link>
            <Link 
                href="/contact" 
                style={{ color: '#fff', textDecoration: 'none' }}
            >
                お問い合わせ
            </Link>
        </nav>
    );
}
```

### 実践例: 動的ルートへのLink

```tsx
// app/components/ProductList.tsx
import Link from 'next/link';

interface Product {
    id: number;
    name: string;
    price: number;
}

export default function ProductList({ products }: { products: Product[] }) {
    return (
        <div>
            <h2>商品一覧</h2>
            <ul>
                {products.map(product => (
                    <li key={product.id}>
                        <Link href={`/products/${product.id}`}>
                            {product.name} - ¥{product.price.toLocaleString()}
                        </Link>
                    </li>
                ))}
            </ul>
        </div>
    );
}
```

### 実践例: Linkのスタイリング

```tsx
// app/components/StyledLink.tsx
import Link from 'next/link';

export default function StyledLink() {
    return (
        <div>
            {/* 基本的なLink */}
            <Link href="/about">About</Link>

            {/* スタイルを適用したLink */}
            <Link 
                href="/about"
                style={{
                    color: '#007bff',
                    textDecoration: 'none',
                    padding: '8px 16px',
                    border: '1px solid #007bff',
                    borderRadius: '4px'
                }}
            >
                About
            </Link>

            {/* classNameを使用（CSS ModulesやTailwind CSSなど） */}
            <Link 
                href="/about"
                className="btn-primary"
            >
                About
            </Link>
        </div>
    );
}
```

### 実践例: 外部リンク

```tsx
// app/components/ExternalLinks.tsx
import Link from 'next/link';

export default function ExternalLinks() {
    return (
        <div>
            {/* 外部リンク: target="_blank"とrel="noopener noreferrer"を追加 */}
            <Link 
                href="https://nextjs.org"
                target="_blank"
                rel="noopener noreferrer"
            >
                Next.js公式サイト
            </Link>

            {/* 内部リンク: targetやrelは不要 */}
            <Link href="/about">About</Link>
        </div>
    );
}
```

### 実践例: Linkのプレフェッチ制御

```tsx
// app/components/PrefetchControl.tsx
import Link from 'next/link';

export default function PrefetchControl() {
    return (
        <div>
            {/* デフォルト: プレフェッチ有効 */}
            <Link href="/about">About（プレフェッチ有効）</Link>

            {/* プレフェッチ無効 */}
            <Link href="/about" prefetch={false}>
                About（プレフェッチ無効）
            </Link>
        </div>
    );
}
```

### 実践例: アクティブなリンクの表示

```tsx
// app/components/ActiveLink.tsx
'use client';

import Link from 'next/link';
import { usePathname } from 'next/navigation';

export default function ActiveLink({ href, children }: { href: string; children: React.ReactNode }) {
    const pathname = usePathname();
    const isActive = pathname === href;

    return (
        <Link 
            href={href}
            style={{
                color: isActive ? '#007bff' : '#333',
                fontWeight: isActive ? 'bold' : 'normal',
                textDecoration: 'none'
            }}
        >
            {children}
        </Link>
    );
}

// app/components/Navigation.tsx
import ActiveLink from './ActiveLink';

export default function Navigation() {
    return (
        <nav>
            <ActiveLink href="/">ホーム</ActiveLink>
            <ActiveLink href="/about">About</ActiveLink>
            <ActiveLink href="/products">商品一覧</ActiveLink>
        </nav>
    );
}
```

---

## useRouter フック

### useRouterとは
`useRouter`は、**プログラムからページ遷移を行うためのフック**です。ボタンのクリックやフォーム送信後など、条件に応じて遷移する際に使用します。

### 日常生活での例
- **自動ドア**: センサーが反応した時（条件）に、ドアが開く（遷移）
- **エレベーター**: ボタンを押した時（条件）に、目的の階に移動する（遷移）
- **GPSナビ**: 目的地を設定した時（条件）に、ルートを表示する（遷移）

### useRouterの基本構文

```tsx
'use client';

import { useRouter } from 'next/navigation';

const router = useRouter();

// ページ遷移
router.push('/パス');
router.replace('/パス');
router.back();
router.forward();
router.refresh();
```

### useRouterの特徴
1. **クライアントサイドナビゲーション**: ページ全体をリロードせずに遷移
2. **プログラムからの制御**: 条件に応じて遷移を制御できる
3. **履歴の操作**: ブラウザの履歴を操作できる
4. **Client Componentでのみ使用可能**: `'use client'`ディレクティブが必要

### 実践例: 基本的なuseRouter

```tsx
// app/components/LoginForm.tsx
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';

export default function LoginForm() {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const router = useRouter();

    const handleSubmit = async (e: React.FormEvent) => {
        e.preventDefault();
        
        // ログイン処理（模擬）
        if (email && password) {
            console.log('ログイン成功');
            // ログイン成功後、ダッシュボードに遷移
            router.push('/dashboard');
        } else {
            alert('メールアドレスとパスワードを入力してください');
        }
    };

    return (
        <form onSubmit={handleSubmit} style={{ maxWidth: '400px', margin: '0 auto', padding: '32px' }}>
            <h1>ログイン</h1>
            <div style={{ marginBottom: '16px' }}>
                <label>メールアドレス</label>
                <input
                    type="email"
                    value={email}
                    onChange={(e) => setEmail(e.target.value)}
                    style={{ width: '100%', padding: '8px' }}
                />
            </div>
            <div style={{ marginBottom: '16px' }}>
                <label>パスワード</label>
                <input
                    type="password"
                    value={password}
                    onChange={(e) => setPassword(e.target.value)}
                    style={{ width: '100%', padding: '8px' }}
                />
            </div>
            <button type="submit" style={{ width: '100%', padding: '8px' }}>
                ログイン
            </button>
            <button 
                type="button"
                onClick={() => router.push('/register')}
                style={{ marginTop: '16px', width: '100%', padding: '8px' }}
            >
                新規登録
            </button>
        </form>
    );
}
```

### 実践例: router.pushとrouter.replace

```tsx
// app/components/NavigationButtons.tsx
'use client';

import { useRouter } from 'next/navigation';

export default function NavigationButtons() {
    const router = useRouter();

    const handlePush = () => {
        // push: 履歴に追加（戻るボタンで戻れる）
        router.push('/about');
    };

    const handleReplace = () => {
        // replace: 履歴を置き換え（戻るボタンで戻れない）
        router.replace('/about');
    };

    const handleBack = () => {
        // 前のページに戻る
        router.back();
    };

    const handleForward = () => {
        // 次のページに進む
        router.forward();
    };

    const handleRefresh = () => {
        // 現在のページを再読み込み
        router.refresh();
    };

    return (
        <div style={{ display: 'flex', gap: '8px' }}>
            <button onClick={handlePush}>push（履歴に追加）</button>
            <button onClick={handleReplace}>replace（履歴を置き換え）</button>
            <button onClick={handleBack}>戻る</button>
            <button onClick={handleForward}>進む</button>
            <button onClick={handleRefresh}>再読み込み</button>
        </div>
    );
}
```

### 実践例: 条件付き遷移

```tsx
// app/components/ProductDetail.tsx
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';

export default function ProductDetail({ productId }: { productId: string }) {
    const [quantity, setQuantity] = useState(1);
    const router = useRouter();

    const handleAddToCart = () => {
        // カートに追加する処理
        console.log(`商品${productId}を${quantity}個カートに追加`);
        
        // カートページに遷移
        router.push('/cart');
    };

    const handleBack = () => {
        // 前のページに戻る
        router.back();
    };

    const handleGoToProducts = () => {
        // 商品一覧ページに遷移
        router.push('/products');
    };

    return (
        <div style={{ padding: '32px' }}>
            <button onClick={handleBack}>戻る</button>
            <button onClick={handleGoToProducts}>商品一覧</button>
            
            <h1>商品詳細 {productId}</h1>
            <div>
                <label>数量: </label>
                <input
                    type="number"
                    value={quantity}
                    onChange={(e) => setQuantity(Number(e.target.value))}
                    min="1"
                />
            </div>
            <button onClick={handleAddToCart}>カートに追加</button>
        </div>
    );
}
```

### 実践例: クエリパラメータ付きの遷移

```tsx
// app/components/SearchForm.tsx
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';

export default function SearchForm() {
    const [query, setQuery] = useState('');
    const router = useRouter();

    const handleSearch = (e: React.FormEvent) => {
        e.preventDefault();
        
        if (query) {
            // クエリパラメータ付きで検索ページに遷移
            router.push(`/search?q=${encodeURIComponent(query)}`);
        }
    };

    return (
        <form onSubmit={handleSearch}>
            <input
                type="text"
                value={query}
                onChange={(e) => setQuery(e.target.value)}
                placeholder="検索..."
                style={{ padding: '8px', width: '300px' }}
            />
            <button type="submit" style={{ padding: '8px', marginLeft: '8px' }}>
                検索
            </button>
        </form>
    );
}
```

### 実践例: 遷移前の確認

```tsx
// app/components/ConfirmNavigation.tsx
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';

export default function ConfirmNavigation() {
    const [hasUnsavedChanges, setHasUnsavedChanges] = useState(false);
    const router = useRouter();

    const handleNavigation = (path: string) => {
        if (hasUnsavedChanges) {
            const confirmed = confirm('保存されていない変更があります。移動しますか？');
            if (confirmed) {
                router.push(path);
            }
        } else {
            router.push(path);
        }
    };

    return (
        <div>
            <button onClick={() => setHasUnsavedChanges(!hasUnsavedChanges)}>
                {hasUnsavedChanges ? '変更を保存' : '変更を加える'}
            </button>
            <button onClick={() => handleNavigation('/about')}>
                Aboutに移動
            </button>
        </div>
    );
}
```

---

## サーバーサイドでのリダイレクト

### サーバーサイドリダイレクトとは
サーバーサイドリダイレクトは、**サーバーコンポーネントやRoute Handlerでページをリダイレクトする**機能です。認証チェックや条件に応じて、適切なページにリダイレクトできます。

### 日常生活での例
- **入場管理**: チケットがない場合（条件）に、入場口（リダイレクト先）に案内する
- **セキュリティ**: 認証されていない場合（条件）に、ログインページ（リダイレクト先）に誘導する
- **案内**: 閉店中の場合（条件）に、営業時間の案内（リダイレクト先）に誘導する

### redirect関数とは
`redirect`は、**サーバーコンポーネントやRoute Handlerでリダイレクトを行う**関数です。Next.js 13以降のApp Routerで使用できます。

### 実践例: サーバーコンポーネントでのリダイレクト

```tsx
// app/dashboard/page.tsx
import { redirect } from 'next/navigation';
import { getSession } from '@/lib/auth';

export default async function DashboardPage() {
    // セッションを確認
    const session = await getSession();

    // 認証されていない場合、ログインページにリダイレクト
    if (!session) {
        redirect('/login');
    }

    return (
        <div>
            <h1>ダッシュボード</h1>
            <p>ようこそ、{session.user.name}さん</p>
        </div>
    );
}
```

### 実践例: 条件付きリダイレクト

```tsx
// app/products/[id]/page.tsx
import { redirect } from 'next/navigation';

async function getProduct(id: string) {
    // 実際のアプリケーションでは、データベースから取得
    const products = {
        '1': { id: '1', name: 'ノートパソコン', available: true },
        '2': { id: '2', name: 'マウス', available: false },
    };
    return products[id as keyof typeof products];
}

export default async function ProductPage({ params }: { params: { id: string } }) {
    const product = await getProduct(params.id);

    // 商品が存在しない場合、404ページにリダイレクト
    if (!product) {
        redirect('/404');
    }

    // 商品が利用不可能な場合、商品一覧ページにリダイレクト
    if (!product.available) {
        redirect('/products?unavailable=true');
    }

    return (
        <div>
            <h1>{product.name}</h1>
            <p>商品ID: {product.id}</p>
        </div>
    );
}
```

### 実践例: Route Handlerでのリダイレクト

```tsx
// app/api/auth/login/route.ts
import { NextResponse } from 'next/server';
import { redirect } from 'next/navigation';

export async function POST(request: Request) {
    const body = await request.json();
    const { email, password } = body;

    // 認証処理（模擬）
    if (email === 'admin@example.com' && password === 'password') {
        // 認証成功: ダッシュボードにリダイレクト
        return NextResponse.redirect(new URL('/dashboard', request.url));
    } else {
        // 認証失敗: ログインページにリダイレクト（エラーメッセージ付き）
        return NextResponse.redirect(new URL('/login?error=invalid', request.url));
    }
}
```

### 実践例: 永続的なリダイレクト（301/302）

```tsx
// app/api/old-page/route.ts
import { NextResponse } from 'next/server';

export async function GET() {
    // 301リダイレクト（永続的）: 古いURLから新しいURLに永続的にリダイレクト
    return NextResponse.redirect(new URL('/new-page', 'http://localhost:3000'), {
        status: 301,
    });
}

// app/api/temporary/route.ts
import { NextResponse } from 'next/server';

export async function GET() {
    // 302リダイレクト（一時的）: 一時的に別のURLにリダイレクト
    return NextResponse.redirect(new URL('/temporary-page', 'http://localhost:3000'), {
        status: 302,
    });
}
```

### 実践例: 認証チェックとリダイレクト

```tsx
// app/lib/auth.ts
export async function getSession() {
    // 実際のアプリケーションでは、セッションを取得
    // ここでは模擬データ
    return null; // または { user: { name: '太郎' } }
}

// app/middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
    // 認証が必要なページ
    const protectedPaths = ['/dashboard', '/admin', '/profile'];
    const isProtectedPath = protectedPaths.some(path => 
        request.nextUrl.pathname.startsWith(path)
    );

    // 認証チェック（模擬）
    const token = request.cookies.get('token');
    
    if (isProtectedPath && !token) {
        // 認証されていない場合、ログインページにリダイレクト
        const loginUrl = new URL('/login', request.url);
        loginUrl.searchParams.set('from', request.nextUrl.pathname);
        return NextResponse.redirect(loginUrl);
    }

    return NextResponse.next();
}

export const config = {
    matcher: ['/dashboard/:path*', '/admin/:path*', '/profile/:path*'],
};
```

### 実践例: 外部URLへのリダイレクト

```tsx
// app/api/external/route.ts
import { NextResponse } from 'next/server';

export async function GET() {
    // 外部URLにリダイレクト
    return NextResponse.redirect('https://example.com', {
        status: 302,
    });
}
```

### 実践例: リダイレクトとメッセージの表示

```tsx
// app/login/page.tsx
import { redirect } from 'next/navigation';
import LoginForm from '@/components/LoginForm';

export default function LoginPage({ searchParams }: { searchParams: { from?: string; error?: string } }) {
    // エラーメッセージの表示
    const errorMessage = searchParams.error === 'invalid' 
        ? 'メールアドレスまたはパスワードが正しくありません'
        : null;

    return (
        <div>
            <h1>ログイン</h1>
            {errorMessage && (
                <div style={{ color: 'red', marginBottom: '16px' }}>
                    {errorMessage}
                </div>
            )}
            {searchParams.from && (
                <p style={{ color: 'blue', marginBottom: '16px' }}>
                    このページを閲覧するにはログインが必要です
                </p>
            )}
            <LoginForm />
        </div>
    );
}

// app/components/LoginForm.tsx
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';

export default function LoginForm() {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const router = useRouter();

    const handleSubmit = async (e: React.FormEvent) => {
        e.preventDefault();
        
        try {
            const response = await fetch('/api/auth/login', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ email, password }),
            });

            if (response.ok) {
                // リダイレクトはサーバー側で処理される
                router.push('/dashboard');
            } else {
                router.push('/login?error=invalid');
            }
        } catch (error) {
            console.error('ログインエラー:', error);
            router.push('/login?error=invalid');
        }
    };

    return (
        <form onSubmit={handleSubmit}>
            <div>
                <label>メールアドレス</label>
                <input
                    type="email"
                    value={email}
                    onChange={(e) => setEmail(e.target.value)}
                />
            </div>
            <div>
                <label>パスワード</label>
                <input
                    type="password"
                    value={password}
                    onChange={(e) => setPassword(e.target.value)}
                />
            </div>
            <button type="submit">ログイン</button>
        </form>
    );
}
```

---

## まとめ

この章では、Next.jsのナビゲーションについて以下のことを学びました。

### <Link> コンポーネント
- `Link`は、Next.jsでページ遷移を行うためのコンポーネント
- クライアントサイドナビゲーションで、ページ全体をリロードせずに遷移できる
- プリフェッチ機能により、リンク先のページを事前に読み込む
- 動的ルート、外部リンク、スタイリングなどに対応

### useRouter フック
- `useRouter`は、プログラムからページ遷移を行うためのフック
- `router.push`、`router.replace`、`router.back`、`router.forward`、`router.refresh`などのメソッドが使用できる
- Client Componentでのみ使用可能（`'use client'`ディレクティブが必要）
- 条件付き遷移やクエリパラメータ付きの遷移が可能

### サーバーサイドでのリダイレクト
- `redirect`関数は、サーバーコンポーネントやRoute Handlerでリダイレクトを行う
- 認証チェックや条件に応じて、適切なページにリダイレクトできる
- `NextResponse.redirect`を使用して、Route Handlerでリダイレクトできる
- 301（永続的）や302（一時的）のリダイレクトを指定できる

### Next.jsのナビゲーションのメリット
- **パフォーマンス**: クライアントサイドナビゲーションにより、高速なページ遷移
- **プリフェッチ**: リンク先のページを事前に読み込むことで、ユーザー体験が向上
- **最適化**: Next.jsが自動的に最適化を行う
- **型安全性**: TypeScriptを使用している場合、型チェックが可能

### 次のステップ
ナビゲーションを理解したら、次のトピックに進みましょう：
- ネスト可能なレイアウト（2-3）
- Server Componentとレンダリング（3章）
- データ取得とキャッシュ（4章）

---

## 演習問題

以下の問題を解いて、Next.jsのナビゲーションの理解を深めましょう。

### 問題1: 基本的なLink
`Home`、`About`、`Contact`の3つのページへのリンクを含むナビゲーションコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/components/Navigation.tsx
import Link from 'next/link';

export default function Navigation() {
    return (
        <nav style={{ display: 'flex', gap: '16px' }}>
            <Link href="/">ホーム</Link>
            <Link href="/about">About</Link>
            <Link href="/contact">Contact</Link>
        </nav>
    );
}
```
</details>

### 問題2: useRouterを使った遷移
ログインフォームを作成し、ログイン成功後に`/dashboard`に遷移する機能を実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/components/LoginForm.tsx
'use client';

import { useState } from 'react';
import { useRouter } from 'next/navigation';

export default function LoginForm() {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const router = useRouter();

    const handleSubmit = (e: React.FormEvent) => {
        e.preventDefault();
        if (email && password) {
            router.push('/dashboard');
        }
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="email"
                value={email}
                onChange={(e) => setEmail(e.target.value)}
            />
            <input
                type="password"
                value={password}
                onChange={(e) => setPassword(e.target.value)}
            />
            <button type="submit">ログイン</button>
        </form>
    );
}
```
</details>

### 問題3: サーバーサイドでのリダイレクト
認証が必要なページを作成し、認証されていない場合に`/login`にリダイレクトする機能を実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/dashboard/page.tsx
import { redirect } from 'next/navigation';

async function checkAuth() {
    // 実際のアプリケーションでは、セッションを確認
    return false; // 認証されていない
}

export default async function DashboardPage() {
    const isAuthenticated = await checkAuth();
    
    if (!isAuthenticated) {
        redirect('/login');
    }

    return <div>ダッシュボード</div>;
}
```
</details>

### 問題4: 動的ルートへのLink
商品一覧を表示し、各商品をクリックすると商品詳細ページに遷移する機能を実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/components/ProductList.tsx
import Link from 'next/link';

interface Product {
    id: number;
    name: string;
}

export default function ProductList({ products }: { products: Product[] }) {
    return (
        <ul>
            {products.map(product => (
                <li key={product.id}>
                    <Link href={`/products/${product.id}`}>
                        {product.name}
                    </Link>
                </li>
            ))}
        </ul>
    );
}
```
</details>

### 問題5: 条件付きリダイレクト
商品詳細ページを作成し、商品が存在しない場合に`/404`にリダイレクトする機能を実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/products/[id]/page.tsx
import { redirect } from 'next/navigation';

async function getProduct(id: string) {
    // 実際のアプリケーションでは、データベースから取得
    return null; // 商品が存在しない
}

export default async function ProductPage({ params }: { params: { id: string } }) {
    const product = await getProduct(params.id);

    if (!product) {
        redirect('/404');
    }

    return <div>{product.name}</div>;
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

