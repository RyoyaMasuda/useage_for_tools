# 9-2. パフォーマンスとキャッシュの最適化（Next.jsのキャッシュシステム (The 4 Caches)）

この章では、Next.jsの4つのキャッシュシステムについて学びます。キャッシュを理解することで、アプリケーションのパフォーマンスを最適化できるようになります。

---

## 目次

- [Request Memoization（リクエストメモ化）](#request-memoizationリクエストメモ化)
- [Data Cache（データキャッシュ）](#data-cacheデータキャッシュ)
- [Full Route Cache（フルルートキャッシュ）](#full-route-cacheフルルートキャッシュ)
- [Router Cache（ルーターキャッシュ）](#router-cacheルーターキャッシュ)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## Request Memoization（リクエストメモ化）

### Request Memoizationとは
Request Memoizationは、**同じリクエスト内で同じ関数を複数回呼び出した場合、結果をメモ化する**仕組みです。同じデータを複数回取得することを防ぎます。

### 日常生活での例
- **メモ帳**: 一度調べた情報（リクエストメモ化）をメモ帳に書いておき、同じ情報を再度調べる必要がなくなった
- **電話帳**: 一度調べた電話番号（リクエストメモ化）を電話帳に書いておき、同じ番号を再度調べる必要がなくなった
- **辞書**: 一度調べた単語の意味（リクエストメモ化）を覚えておき、同じ単語を再度調べる必要がなくなった

### Request Memoizationの特徴
1. **自動適用**: Next.jsが自動的に適用する
2. **リクエスト単位**: 同じリクエスト内でのみ有効
3. **関数の同一性**: 同じ関数と引数の組み合わせでメモ化される

### 実践例: Request Memoizationの動作

```tsx
// app/components/UserProfile.tsx
import { prisma } from '@/lib/prisma';

async function getUser(id: string) {
    console.log('getUserが呼び出されました:', id);
    return await prisma.user.findUnique({ where: { id } });
}

export default async function UserProfile({ userId }: { userId: string }) {
    // 同じリクエスト内でgetUserが複数回呼び出されても、
    // 実際のデータベースクエリは1回だけ実行される
    const user1 = await getUser(userId);
    const user2 = await getUser(userId);
    const user3 = await getUser(userId);

    // user1, user2, user3は同じオブジェクトを参照
    console.log(user1 === user2); // true
    console.log(user2 === user3); // true

    return (
        <div>
            <h1>{user1.name}</h1>
            <p>{user1.email}</p>
        </div>
    );
}
```

### 実践例: fetch APIでのRequest Memoization

```tsx
// app/components/PostList.tsx
async function fetchPosts() {
    console.log('fetchPostsが呼び出されました');
    const res = await fetch('https://api.example.com/posts', {
        cache: 'force-cache', // キャッシュを有効化
    });
    return res.json();
}

export default async function PostList() {
    // 同じリクエスト内でfetchPostsが複数回呼び出されても、
    // 実際のHTTPリクエストは1回だけ実行される
    const posts1 = await fetchPosts();
    const posts2 = await fetchPosts();
    const posts3 = await fetchPosts();

    // posts1, posts2, posts3は同じデータを参照
    console.log(posts1 === posts2); // true

    return (
        <ul>
            {posts1.map((post: any) => (
                <li key={post.id}>{post.title}</li>
            ))}
        </ul>
    );
}
```

### 実践例: Request Memoizationの無効化

```tsx
// app/components/NoMemoization.tsx
import { cache } from 'react';

// cache関数を使用して明示的にメモ化を制御
const getUser = cache(async (id: string) => {
    console.log('getUserが呼び出されました:', id);
    return await prisma.user.findUnique({ where: { id } });
});

export default async function NoMemoization({ userId }: { userId: string }) {
    const user = await getUser(userId);
    return <div>{user.name}</div>;
}
```

---

## Data Cache（データキャッシュ）

### Data Cacheとは
Data Cacheは、**fetch APIの結果を永続的にキャッシュする**仕組みです。同じデータを複数回取得することを防ぎ、パフォーマンスを向上させます。

### 日常生活での例
- **図書館の本**: 一度借りた本（データキャッシュ）を図書館に返しても、次回同じ本を借りる時はすぐに見つかる
- **冷蔵庫**: 一度買った食材（データキャッシュ）を冷蔵庫に保存しておき、次回同じ食材を買う時はすぐに使える
- **倉庫**: 一度入荷した商品（データキャッシュ）を倉庫に保管しておき、次回同じ商品を出荷する時はすぐに取り出せる

### Data Cacheの特徴
1. **永続的**: ビルド時やリクエスト時にキャッシュされる
2. **自動再検証**: 一定時間後に自動的に再検証される
3. **手動再検証**: `revalidate`を使用して手動で再検証できる

### 実践例: Data Cacheの基本使用

```tsx
// app/components/CachedData.tsx
async function fetchData() {
    // fetch APIは自動的にData Cacheに保存される
    const res = await fetch('https://api.example.com/data', {
        cache: 'force-cache', // キャッシュを有効化（デフォルト）
    });
    return res.json();
}

export default async function CachedData() {
    const data = await fetchData();
    return <div>{JSON.stringify(data)}</div>;
}
```

### 実践例: キャッシュの無効化

```tsx
// app/components/NoCache.tsx
async function fetchData() {
    // cache: 'no-store' を使用してキャッシュを無効化
    const res = await fetch('https://api.example.com/data', {
        cache: 'no-store', // キャッシュを無効化
    });
    return res.json();
}

export default async function NoCache() {
    const data = await fetchData();
    return <div>{JSON.stringify(data)}</div>;
}
```

### 実践例: 時間ベースの再検証

```tsx
// app/components/TimeBasedRevalidation.tsx
async function fetchData() {
    // next.revalidateを使用して、60秒後に自動的に再検証
    const res = await fetch('https://api.example.com/data', {
        next: { revalidate: 60 }, // 60秒後に再検証
    });
    return res.json();
}

export default async function TimeBasedRevalidation() {
    const data = await fetchData();
    return <div>{JSON.stringify(data)}</div>;
}
```

### 実践例: オンデマンド再検証

```tsx
// app/api/revalidate/route.ts
import { revalidatePath } from 'next/cache';
import { NextResponse } from 'next/server';

export async function POST(request: Request) {
    const { path } = await request.json();

    // 特定のパスを再検証
    revalidatePath(path);

    return NextResponse.json({ revalidated: true });
}

// app/components/OnDemandRevalidation.tsx
'use client';

export default function OnDemandRevalidation() {
    const handleRevalidate = async () => {
        await fetch('/api/revalidate', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ path: '/posts' }),
        });
    };

    return (
        <button onClick={handleRevalidate}>
            データを再検証
        </button>
    );
}
```

### 実践例: タグベースの再検証

```tsx
// app/components/TagBasedRevalidation.tsx
async function fetchData() {
    const res = await fetch('https://api.example.com/data', {
        next: { tags: ['posts'] }, // タグを設定
    });
    return res.json();
}

export default async function TagBasedRevalidation() {
    const data = await fetchData();
    return <div>{JSON.stringify(data)}</div>;
}

// app/api/revalidate/route.ts
import { revalidateTag } from 'next/cache';
import { NextResponse } from 'next/server';

export async function POST(request: Request) {
    const { tag } = await request.json();

    // 特定のタグを再検証
    revalidateTag(tag);

    return NextResponse.json({ revalidated: true });
}
```

---

## Full Route Cache（フルルートキャッシュ）

### Full Route Cacheとは
Full Route Cacheは、**静的生成されたページ全体をキャッシュする**仕組みです。ビルド時やリクエスト時にページ全体を事前にレンダリングし、キャッシュします。

### 日常生活での例
- **印刷物**: 一度印刷した文書（フルルートキャッシュ）を保存しておき、次回同じ文書を印刷する時はすぐに使える
- **写真**: 一度撮影した写真（フルルートキャッシュ）を保存しておき、次回同じ写真を見る時はすぐに表示できる
- **レシピ**: 一度作った料理のレシピ（フルルートキャッシュ）を保存しておき、次回同じ料理を作る時はすぐに参照できる

### Full Route Cacheの特徴
1. **静的生成**: ビルド時やリクエスト時にページを事前にレンダリング
2. **永続的**: キャッシュされたページは永続的に保存される
3. **自動適用**: 動的関数を使用しない場合、自動的に適用される

### 実践例: 静的生成（Full Route Cacheが適用される）

```tsx
// app/posts/page.tsx
import { prisma } from '@/lib/prisma';

// 動的関数を使用していないため、Full Route Cacheが適用される
export default async function PostsPage() {
    const posts = await prisma.post.findMany();

    return (
        <div>
            <h1>投稿一覧</h1>
            <ul>
                {posts.map(post => (
                    <li key={post.id}>{post.title}</li>
                ))}
            </ul>
        </div>
    );
}
```

### 実践例: 動的レンダリング（Full Route Cacheが適用されない）

```tsx
// app/posts/[id]/page.tsx
import { cookies } from 'next/headers';

// cookies()などの動的関数を使用しているため、Full Route Cacheが適用されない
export const dynamic = 'force-dynamic'; // 動的レンダリングを強制

export default async function PostPage({ params }: { params: { id: string } }) {
    const cookieStore = cookies();
    const theme = cookieStore.get('theme');

    return (
        <div>
            <h1>投稿詳細</h1>
            <p>テーマ: {theme?.value}</p>
        </div>
    );
}
```

### 実践例: 部分的に動的レンダリング

```tsx
// app/posts/[id]/page.tsx
import { unstable_noStore as noStore } from 'next/cache';

export default async function PostPage({ params }: { params: { id: string } }) {
    // noStore()を使用して、この部分だけ動的レンダリング
    noStore();
    const post = await fetch(`https://api.example.com/posts/${params.id}`).then(res => res.json());

    return (
        <div>
            <h1>{post.title}</h1>
            <p>{post.content}</p>
        </div>
    );
}
```

### 実践例: セグメント設定での制御

```tsx
// app/posts/page.tsx
// 動的レンダリングを強制
export const dynamic = 'force-dynamic';

// または、静的生成を強制
// export const dynamic = 'force-static';

// または、自動検出（デフォルト）
// export const dynamic = 'auto';

export default async function PostsPage() {
    const posts = await fetch('https://api.example.com/posts').then(res => res.json());

    return (
        <div>
            <h1>投稿一覧</h1>
            <ul>
                {posts.map((post: any) => (
                    <li key={post.id}>{post.title}</li>
                ))}
            </ul>
        </div>
    );
}
```

---

## Router Cache（ルーターキャッシュ）

### Router Cacheとは
Router Cacheは、**クライアント側でナビゲーション時にページをキャッシュする**仕組みです。ユーザーが同じページに戻った時、即座に表示できます。

### 日常生活での例
- **ブラウザの履歴**: 一度訪れたページ（ルーターキャッシュ）を履歴に保存しておき、次回同じページに戻る時はすぐに表示できる
- **ブックマーク**: 一度保存したページ（ルーターキャッシュ）をブックマークしておき、次回同じページを開く時はすぐに表示できる
- **最近使ったファイル**: 一度開いたファイル（ルーターキャッシュ）を最近使ったファイルに保存しておき、次回同じファイルを開く時はすぐに表示できる

### Router Cacheの特徴
1. **クライアント側**: ブラウザでキャッシュされる
2. **一時的**: セッション中のみ有効
3. **自動適用**: Next.jsが自動的に適用する

### 実践例: Router Cacheの動作

```tsx
// app/components/Navigation.tsx
'use client';

import Link from 'next/link';
import { useRouter } from 'next/navigation';

export default function Navigation() {
    const router = useRouter();

    return (
        <nav>
            <Link href="/posts">投稿一覧</Link>
            <Link href="/about">About</Link>
            <button onClick={() => router.push('/contact')}>
                お問い合わせ
            </button>
        </nav>
    );
}

// ユーザーが /posts に移動すると、Router Cacheに保存される
// その後、/about に移動してから /posts に戻ると、
// Router Cacheから即座に表示される
```

### 実践例: Router Cacheの無効化

```tsx
// app/components/NoRouterCache.tsx
'use client';

import { useRouter } from 'next/navigation';

export default function NoRouterCache() {
    const router = useRouter();

    const handleNavigation = () => {
        // router.refresh()を使用して、Router Cacheを無効化
        router.refresh();
        router.push('/posts');
    };

    return (
        <button onClick={handleNavigation}>
            最新データを取得して移動
        </button>
    );
}
```

### 実践例: Router Cacheの制御

```tsx
// app/components/CacheControl.tsx
'use client';

import { useRouter } from 'next/navigation';
import { useEffect } from 'react';

export default function CacheControl() {
    const router = useRouter();

    useEffect(() => {
        // 一定時間後にRouter Cacheを無効化
        const timer = setTimeout(() => {
            router.refresh();
        }, 30000); // 30秒後

        return () => clearTimeout(timer);
    }, [router]);

    return <div>キャッシュ制御中...</div>;
}
```

### 実践例: プリフェッチとRouter Cache

```tsx
// app/components/PrefetchExample.tsx
'use client';

import Link from 'next/link';

export default function PrefetchExample() {
    return (
        <div>
            {/* prefetch={true}（デフォルト）: リンクが表示された時点でプリフェッチ */}
            <Link href="/posts" prefetch={true}>
                投稿一覧（プリフェッチ有効）
            </Link>

            {/* prefetch={false}: プリフェッチを無効化 */}
            <Link href="/about" prefetch={false}>
                About（プリフェッチ無効）
            </Link>
        </div>
    );
}
```

---

## まとめ

この章では、Next.jsのキャッシュシステム（The 4 Caches）について以下のことを学びました。

### Request Memoization（リクエストメモ化）
- **目的**: 同じリクエスト内で同じ関数を複数回呼び出した場合、結果をメモ化
- **適用範囲**: 同じリクエスト内でのみ有効
- **自動適用**: Next.jsが自動的に適用する
- **使用例**: 同じデータを複数のコンポーネントで使用する場合

### Data Cache（データキャッシュ）
- **目的**: fetch APIの結果を永続的にキャッシュ
- **適用範囲**: ビルド時やリクエスト時にキャッシュされる
- **再検証**: 時間ベース、タグベース、オンデマンドで再検証可能
- **使用例**: APIから取得したデータをキャッシュする場合

### Full Route Cache（フルルートキャッシュ）
- **目的**: 静的生成されたページ全体をキャッシュ
- **適用範囲**: 動的関数を使用しない場合、自動的に適用される
- **制御**: `dynamic`設定や`noStore()`で制御可能
- **使用例**: 静的ページを事前にレンダリングする場合

### Router Cache（ルーターキャッシュ）
- **目的**: クライアント側でナビゲーション時にページをキャッシュ
- **適用範囲**: セッション中のみ有効
- **制御**: `router.refresh()`で無効化可能
- **使用例**: ユーザーが同じページに戻った時、即座に表示する場合

### キャッシュシステムのメリット
- **パフォーマンス**: キャッシュにより、データ取得やレンダリングが高速化
- **ユーザー体験**: 即座にページが表示されるため、ユーザー体験が向上
- **サーバー負荷**: キャッシュにより、サーバーへの負荷が軽減
- **ネットワーク使用量**: キャッシュにより、ネットワーク使用量が削減

### 次のステップ
Next.jsのキャッシュシステムを理解したら、次のトピックに進みましょう：
- レンダリング戦略の最適化（9-3）
- アセットの最適化（9-4）

---

## 演習問題

以下の問題を解いて、Next.jsのキャッシュシステムの理解を深めましょう。

### 問題1: Request Memoization
同じリクエスト内で同じ関数を複数回呼び出すコンポーネントを作成し、Request Memoizationが動作することを確認してください。

<details>
<summary>解答例</summary>

```tsx
// app/components/MemoizationExample.tsx
import { prisma } from '@/lib/prisma';

async function getUser(id: string) {
    console.log('getUserが呼び出されました:', id);
    return await prisma.user.findUnique({ where: { id } });
}

export default async function MemoizationExample({ userId }: { userId: string }) {
    const user1 = await getUser(userId);
    const user2 = await getUser(userId);

    return (
        <div>
            <p>{user1.name}</p>
            <p>{user2.name}</p>
        </div>
    );
}
```
</details>

### 問題2: Data Cache
fetch APIを使用してデータを取得し、Data Cacheに保存するコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/components/DataCacheExample.tsx
async function fetchData() {
    const res = await fetch('https://api.example.com/data', {
        cache: 'force-cache',
    });
    return res.json();
}

export default async function DataCacheExample() {
    const data = await fetchData();
    return <div>{JSON.stringify(data)}</div>;
}
```
</details>

### 問題3: 時間ベースの再検証
60秒後に自動的に再検証されるデータ取得を実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/components/TimeBasedRevalidation.tsx
async function fetchData() {
    const res = await fetch('https://api.example.com/data', {
        next: { revalidate: 60 },
    });
    return res.json();
}

export default async function TimeBasedRevalidation() {
    const data = await fetchData();
    return <div>{JSON.stringify(data)}</div>;
}
```
</details>

### 問題4: 動的レンダリング
動的レンダリングを強制するページを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/posts/page.tsx
export const dynamic = 'force-dynamic';

export default async function PostsPage() {
    const posts = await fetch('https://api.example.com/posts').then(res => res.json());

    return (
        <div>
            <h1>投稿一覧</h1>
            <ul>
                {posts.map((post: any) => (
                    <li key={post.id}>{post.title}</li>
                ))}
            </ul>
        </div>
    );
}
```
</details>

### 問題5: Router Cacheの無効化
Router Cacheを無効化して、最新データを取得する機能を実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/components/RefreshExample.tsx
'use client';

import { useRouter } from 'next/navigation';

export default function RefreshExample() {
    const router = useRouter();

    const handleRefresh = () => {
        router.refresh();
    };

    return (
        <button onClick={handleRefresh}>
            最新データを取得
        </button>
    );
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

