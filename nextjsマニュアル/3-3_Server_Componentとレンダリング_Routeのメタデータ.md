# 3-3. Server Componentとレンダリング（Routeのメタデータ）

この章では、Next.jsアプリケーションでページのメタデータ（タイトル、説明、OGPなど）を設定する方法について学びます。メタデータを適切に設定することで、SEO対策やSNSでのシェア時の表示を改善できます。

---

## 目次

- [静的メタデータと動的メタデータ (generateMetadata)](#静的メタデータと動的メタデータ-generatemetadata)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## 静的メタデータと動的メタデータ (generateMetadata)

### メタデータとは
メタデータは、**ページに関する情報（タイトル、説明、画像など）**です。検索エンジンやSNSがページを理解するために使用されます。

### 日常生活での例
- **本の表紙**: 本のタイトル（メタデータ）を見て、内容を推測する
- **商品のラベル**: 商品名（メタデータ）を見て、商品を識別する
- **看板**: 店名（メタデータ）を見て、店を識別する

### メタデータの種類
1. **タイトル**: ページのタイトル（`<title>`タグ）
2. **説明**: ページの説明（`<meta name="description">`）
3. **OGP**: Open Graph Protocol（SNSでのシェア時に表示される情報）
4. **Twitter Card**: Twitterでのシェア時に表示される情報
5. **アイコン**: ファビコンやApple Touch Icon

### 静的メタデータとは
静的メタデータは、**固定された値のメタデータ**です。ページごとに同じメタデータを設定する場合に使用します。

### 実践例: 基本的な静的メタデータ

```tsx
// app/page.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
    title: 'ホームページ',
    description: 'Next.jsアプリケーションのホームページです',
};

export default function HomePage() {
    return (
        <div>
            <h1>ホームページ</h1>
            <p>ようこそ、ホームページへ！</p>
        </div>
    );
}
```

### 実践例: 詳細な静的メタデータ

```tsx
// app/about/page.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
    title: 'About | My App',
    description: 'このサイトについての情報を掲載しています',
    keywords: ['Next.js', 'React', 'TypeScript'],
    authors: [{ name: '山田太郎' }],
    openGraph: {
        title: 'About | My App',
        description: 'このサイトについての情報を掲載しています',
        url: 'https://example.com/about',
        siteName: 'My App',
        images: [
            {
                url: 'https://example.com/og-image.jpg',
                width: 1200,
                height: 630,
                alt: 'AboutページのOG画像',
            },
        ],
        locale: 'ja_JP',
        type: 'website',
    },
    twitter: {
        card: 'summary_large_image',
        title: 'About | My App',
        description: 'このサイトについての情報を掲載しています',
        images: ['https://example.com/twitter-image.jpg'],
    },
    robots: {
        index: true,
        follow: true,
    },
};

export default function AboutPage() {
    return (
        <div>
            <h1>About</h1>
            <p>このサイトについて</p>
        </div>
    );
}
```

### 実践例: レイアウトでのメタデータ

```tsx
// app/layout.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
    title: {
        default: 'My App',
        template: '%s | My App', // 子ページのタイトルに自動的に追加
    },
    description: 'Next.jsアプリケーション',
    metadataBase: new URL('https://example.com'),
    openGraph: {
        type: 'website',
        locale: 'ja_JP',
        url: 'https://example.com',
        siteName: 'My App',
        images: [
            {
                url: '/og-image.jpg',
                width: 1200,
                height: 630,
                alt: 'My App',
            },
        ],
    },
    twitter: {
        card: 'summary_large_image',
        title: 'My App',
        description: 'Next.jsアプリケーション',
        images: ['/twitter-image.jpg'],
    },
};

export default function RootLayout({
    children,
}: {
    children: React.ReactNode;
}) {
    return (
        <html lang="ja">
            <body>{children}</body>
        </html>
    );
}
```

### 動的メタデータとは
動的メタデータは、**データに基づいて動的に生成されるメタデータ**です。`generateMetadata`関数を使用して、データに応じたメタデータを生成します。

### generateMetadataとは
`generateMetadata`は、**動的にメタデータを生成する関数**です。非同期関数として定義し、データを取得してメタデータを返します。

### 実践例: 基本的なgenerateMetadata

```tsx
// app/products/[id]/page.tsx
import type { Metadata } from 'next';

async function getProduct(id: string) {
    // 実際のアプリケーションでは、データベースから取得
    const products = {
        '1': { id: '1', name: 'ノートパソコン', description: '高性能なノートパソコン' },
        '2': { id: '2', name: 'マウス', description: '使いやすいマウス' },
    };
    return products[id as keyof typeof products];
}

export async function generateMetadata({ params }: { params: { id: string } }): Promise<Metadata> {
    const product = await getProduct(params.id);

    if (!product) {
        return {
            title: '商品が見つかりません',
        };
    }

    return {
        title: `${product.name} | My App`,
        description: product.description,
        openGraph: {
            title: product.name,
            description: product.description,
            images: [`/products/${product.id}/image.jpg`],
        },
    };
}

export default async function ProductPage({ params }: { params: { id: string } }) {
    const product = await getProduct(params.id);

    if (!product) {
        return <div>商品が見つかりません</div>;
    }

    return (
        <div>
            <h1>{product.name}</h1>
            <p>{product.description}</p>
        </div>
    );
}
```

### 実践例: 複数のパラメータを使用したgenerateMetadata

```tsx
// app/users/[userId]/posts/[postId]/page.tsx
import type { Metadata } from 'next';

async function getPost(userId: string, postId: string) {
    // 実際のアプリケーションでは、データベースから取得
    return {
        id: postId,
        title: '投稿タイトル',
        content: '投稿の内容',
        author: { name: '太郎', id: userId },
    };
}

export async function generateMetadata({ 
    params 
}: { 
    params: { userId: string; postId: string } 
}): Promise<Metadata> {
    const post = await getPost(params.userId, params.postId);

    return {
        title: `${post.title} | ${post.author.name}の投稿`,
        description: post.content.substring(0, 160),
        openGraph: {
            title: post.title,
            description: post.content.substring(0, 160),
            authors: [post.author.name],
            images: [`/users/${params.userId}/posts/${params.postId}/image.jpg`],
        },
    };
}

export default async function PostPage({ 
    params 
}: { 
    params: { userId: string; postId: string } 
}) {
    const post = await getPost(params.userId, params.postId);

    return (
        <div>
            <h1>{post.title}</h1>
            <p>投稿者: {post.author.name}</p>
            <p>{post.content}</p>
        </div>
    );
}
```

### 実践例: searchParamsを使用したgenerateMetadata

```tsx
// app/search/page.tsx
import type { Metadata } from 'next';

export async function generateMetadata({ 
    searchParams 
}: { 
    searchParams: { q?: string } 
}): Promise<Metadata> {
    const query = searchParams.q || '';

    return {
        title: query ? `"${query}"の検索結果 | My App` : '検索 | My App',
        description: query 
            ? `"${query}"の検索結果を表示しています`
            : '検索ページです',
        openGraph: {
            title: query ? `"${query}"の検索結果` : '検索',
            description: query 
                ? `"${query}"の検索結果を表示しています`
                : '検索ページです',
        },
    };
}

export default async function SearchPage({ 
    searchParams 
}: { 
    searchParams: { q?: string } 
}) {
    const query = searchParams.q || '';

    return (
        <div>
            <h1>検索</h1>
            {query && <p>検索クエリ: {query}</p>}
        </div>
    );
}
```

### 実践例: エラーハンドリングを含むgenerateMetadata

```tsx
// app/products/[id]/page.tsx
import type { Metadata } from 'next';
import { notFound } from 'next/navigation';

async function getProduct(id: string) {
    // 実際のアプリケーションでは、データベースから取得
    const products = {
        '1': { id: '1', name: 'ノートパソコン', description: '高性能なノートパソコン' },
    };
    return products[id as keyof typeof products] || null;
}

export async function generateMetadata({ 
    params 
}: { 
    params: { id: string } 
}): Promise<Metadata> {
    try {
        const product = await getProduct(params.id);

        if (!product) {
            return {
                title: '商品が見つかりません | My App',
                description: '指定された商品は存在しません',
            };
        }

        return {
            title: `${product.name} | My App`,
            description: product.description,
            openGraph: {
                title: product.name,
                description: product.description,
                images: [`/products/${product.id}/image.jpg`],
            },
        };
    } catch (error) {
        console.error('メタデータ生成エラー:', error);
        return {
            title: 'エラー | My App',
            description: 'メタデータの生成中にエラーが発生しました',
        };
    }
}

export default async function ProductPage({ params }: { params: { id: string } }) {
    const product = await getProduct(params.id);

    if (!product) {
        notFound();
    }

    return (
        <div>
            <h1>{product.name}</h1>
            <p>{product.description}</p>
        </div>
    );
}
```

### 実践例: 複数のメタデータソースの組み合わせ

```tsx
// app/blog/[slug]/page.tsx
import type { Metadata } from 'next';

async function getPost(slug: string) {
    // 実際のアプリケーションでは、データベースから取得
    return {
        slug,
        title: 'ブログ投稿のタイトル',
        excerpt: 'ブログ投稿の抜粋',
        author: { name: '太郎', avatar: '/authors/taro.jpg' },
        publishedAt: '2024-01-01',
        image: '/blog/post-image.jpg',
        tags: ['Next.js', 'React', 'TypeScript'],
    };
}

export async function generateMetadata({ 
    params 
}: { 
    params: { slug: string } 
}): Promise<Metadata> {
    const post = await getPost(params.slug);

    return {
        title: `${post.title} | My Blog`,
        description: post.excerpt,
        keywords: post.tags,
        authors: [{ name: post.author.name }],
        openGraph: {
            title: post.title,
            description: post.excerpt,
            url: `https://example.com/blog/${post.slug}`,
            siteName: 'My Blog',
            images: [
                {
                    url: post.image,
                    width: 1200,
                    height: 630,
                    alt: post.title,
                },
            ],
            type: 'article',
            publishedTime: post.publishedAt,
            authors: [post.author.name],
            tags: post.tags,
        },
        twitter: {
            card: 'summary_large_image',
            title: post.title,
            description: post.excerpt,
            images: [post.image],
            creator: `@${post.author.name}`,
        },
        alternates: {
            canonical: `https://example.com/blog/${post.slug}`,
        },
    };
}

export default async function BlogPostPage({ 
    params 
}: { 
    params: { slug: string } 
}) {
    const post = await getPost(params.slug);

    return (
        <article>
            <h1>{post.title}</h1>
            <p>著者: {post.author.name}</p>
            <p>公開日: {post.publishedAt}</p>
            <p>{post.excerpt}</p>
        </article>
    );
}
```

### 実践例: アイコンの設定

```tsx
// app/layout.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
    title: 'My App',
    description: 'Next.jsアプリケーション',
    icons: {
        icon: [
            { url: '/favicon.ico' },
            { url: '/icon-16x16.png', sizes: '16x16', type: 'image/png' },
            { url: '/icon-32x32.png', sizes: '32x32', type: 'image/png' },
        ],
        apple: [
            { url: '/apple-touch-icon.png', sizes: '180x180', type: 'image/png' },
        ],
    },
};

export default function RootLayout({
    children,
}: {
    children: React.ReactNode;
}) {
    return (
        <html lang="ja">
            <body>{children}</body>
        </html>
    );
}
```

### 実践例: メタデータの継承

```tsx
// app/layout.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
    title: {
        default: 'My App',
        template: '%s | My App',
    },
    description: 'Next.jsアプリケーション',
    openGraph: {
        type: 'website',
        locale: 'ja_JP',
        siteName: 'My App',
    },
};

// app/about/page.tsx
import type { Metadata } from 'next';

// 親レイアウトのメタデータを継承し、titleのみ上書き
export const metadata: Metadata = {
    title: 'About', // "About | My App" になる（templateが適用される）
    description: 'このサイトについて', // 親のdescriptionを上書き
};

export default function AboutPage() {
    return <div>About</div>;
}
```

---

## まとめ

この章では、Routeのメタデータについて以下のことを学びました。

### 静的メタデータと動的メタデータ
- **静的メタデータ**: 固定された値のメタデータ。`export const metadata`で定義
- **動的メタデータ**: データに基づいて動的に生成されるメタデータ。`generateMetadata`関数で定義
- **メタデータの種類**: タイトル、説明、OGP、Twitter Card、アイコンなど

### 静的メタデータ
- `export const metadata: Metadata`で定義
- レイアウトで定義すると、子ページに継承される
- `title.template`を使用すると、子ページのタイトルに自動的に追加できる

### 動的メタデータ（generateMetadata）
- `generateMetadata`関数は非同期関数として定義
- `params`や`searchParams`を使用して、動的にメタデータを生成
- エラーハンドリングを含めることで、安全にメタデータを生成できる

### メタデータの設定項目
- **基本情報**: title、description、keywords、authors
- **OGP**: openGraph（SNSでのシェア時に表示）
- **Twitter Card**: twitter（Twitterでのシェア時に表示）
- **アイコン**: icons（ファビコンやApple Touch Icon）
- **SEO**: robots、alternates（canonical URL）

### メタデータのメリット
- **SEO対策**: 検索エンジンがページを理解しやすくなる
- **SNSでのシェア**: OGPやTwitter Cardにより、適切に表示される
- **ユーザー体験**: ブラウザのタブやブックマークで適切に表示される

### 次のステップ
Routeのメタデータを理解したら、次のトピックに進みましょう：
- データ取得とキャッシュ（4章）
- Route Handler（5章）
- Server Actions（6章）

---

## 演習問題

以下の問題を解いて、Routeのメタデータの理解を深めましょう。

### 問題1: 基本的な静的メタデータ
`About`ページに、タイトルと説明を含む静的メタデータを設定してください。

<details>
<summary>解答例</summary>

```tsx
// app/about/page.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
    title: 'About | My App',
    description: 'このサイトについての情報を掲載しています',
};

export default function AboutPage() {
    return (
        <div>
            <h1>About</h1>
            <p>このサイトについて</p>
        </div>
    );
}
```
</details>

### 問題2: OGPを含むメタデータ
`Products`ページに、OGPを含むメタデータを設定してください。

<details>
<summary>解答例</summary>

```tsx
// app/products/page.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
    title: '商品一覧 | My App',
    description: '商品一覧ページです',
    openGraph: {
        title: '商品一覧',
        description: '商品一覧ページです',
        images: ['/products/og-image.jpg'],
    },
};

export default function ProductsPage() {
    return <div>商品一覧</div>;
}
```
</details>

### 問題3: generateMetadataの実装
商品詳細ページで、商品データに基づいて動的にメタデータを生成してください。

<details>
<summary>解答例</summary>

```tsx
// app/products/[id]/page.tsx
import type { Metadata } from 'next';

async function getProduct(id: string) {
    return {
        id,
        name: 'ノートパソコン',
        description: '高性能なノートパソコン',
    };
}

export async function generateMetadata({ 
    params 
}: { 
    params: { id: string } 
}): Promise<Metadata> {
    const product = await getProduct(params.id);

    return {
        title: `${product.name} | My App`,
        description: product.description,
    };
}

export default async function ProductPage({ params }: { params: { id: string } }) {
    const product = await getProduct(params.id);
    return <div>{product.name}</div>;
}
```
</details>

### 問題4: レイアウトでのメタデータ
ルートレイアウトで、デフォルトのタイトルとテンプレートを設定してください。

<details>
<summary>解答例</summary>

```tsx
// app/layout.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
    title: {
        default: 'My App',
        template: '%s | My App',
    },
    description: 'Next.jsアプリケーション',
};

export default function RootLayout({ children }: { children: React.ReactNode }) {
    return (
        <html lang="ja">
            <body>{children}</body>
        </html>
    );
}
```
</details>

### 問題5: エラーハンドリングを含むgenerateMetadata
商品詳細ページで、商品が存在しない場合のエラーハンドリングを含むgenerateMetadataを実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/products/[id]/page.tsx
import type { Metadata } from 'next';

async function getProduct(id: string) {
    return null; // 商品が存在しない
}

export async function generateMetadata({ 
    params 
}: { 
    params: { id: string } 
}): Promise<Metadata> {
    const product = await getProduct(params.id);

    if (!product) {
        return {
            title: '商品が見つかりません | My App',
            description: '指定された商品は存在しません',
        };
    }

    return {
        title: `${product.name} | My App`,
        description: product.description,
    };
}

export default async function ProductPage({ params }: { params: { id: string } }) {
    const product = await getProduct(params.id);
    if (!product) {
        return <div>商品が見つかりません</div>;
    }
    return <div>{product.name}</div>;
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

