# 3-2. Server Componentのデータ取得

この章では、Next.jsのServer Componentでのデータ取得方法について学びます。非同期コンポーネント、動的データ取得、静的データ取得を理解することで、効率的にデータを取得し、パフォーマンスを最適化できるようになります。

---

## 目次

- [非同期コンポーネント (`async/await`)](#非同期コンポーネント-asyncawait)
- [動的データ取得 (Dynamic Rendering)](#動的データ取得-dynamic-rendering)
- [静的データ取得 (Static Rendering)](#静的データ取得-static-rendering)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## 非同期コンポーネント (`async/await`)

### 非同期コンポーネントとは
非同期コンポーネントは、**`async/await`を使ってデータを取得するServer Component**です。Next.jsのApp Routerでは、Server Componentを非同期関数として定義できます。

### 日常生活での例
- **レストランの注文**: 注文（リクエスト）をして、料理が来るまで待つ（await）
- **図書館での本の検索**: 本を探す（リクエスト）をして、見つかるまで待つ（await）
- **宅配便の受け取り**: 荷物が届くまで待つ（await）

### 基本的な非同期コンポーネント

```tsx
// app/users/page.tsx
async function getUsers() {
    const res = await fetch('https://api.example.com/users');
    return res.json();
}

export default async function UsersPage() {
    const users = await getUsers();
    
    return (
        <div>
            <h1>ユーザー一覧</h1>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </div>
    );
}
```

### 実践例: 非同期コンポーネント

```tsx
// app/posts/page.tsx
interface Post {
    id: number;
    title: string;
    body: string;
    userId: number;
}

async function getPosts(): Promise<Post[]> {
    const res = await fetch('https://jsonplaceholder.typicode.com/posts');
    if (!res.ok) {
        throw new Error('投稿の取得に失敗しました');
    }
    return res.json();
}

export default async function PostsPage() {
    const posts = await getPosts();
    
    return (
        <div>
            <h1>投稿一覧</h1>
            <ul>
                {posts.map(post => (
                    <li key={post.id}>
                        <h2>{post.title}</h2>
                        <p>{post.body}</p>
                    </li>
                ))}
            </ul>
        </div>
    );
}

// app/posts/[id]/page.tsx
interface PostPageProps {
    params: {
        id: string;
    };
}

async function getPost(id: string): Promise<Post> {
    const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
    if (!res.ok) {
        throw new Error('投稿の取得に失敗しました');
    }
    return res.json();
}

export default async function PostPage({ params }: PostPageProps) {
    const { id } = params;
    const post = await getPost(id);
    
    return (
        <article>
            <h1>{post.title}</h1>
            <p>{post.body}</p>
        </article>
    );
}

// app/users/[id]/page.tsx
interface User {
    id: number;
    name: string;
    email: string;
    phone: string;
}

interface UserPageProps {
    params: {
        id: string;
    };
}

async function getUser(id: string): Promise<User> {
    const res = await fetch(`https://jsonplaceholder.typicode.com/users/${id}`);
    if (!res.ok) {
        throw new Error('ユーザーの取得に失敗しました');
    }
    return res.json();
}

export default async function UserPage({ params }: UserPageProps) {
    const { id } = params;
    const user = await await getUser(id);
    
    return (
        <div>
            <h1>{user.name}</h1>
            <p>メール: {user.email}</p>
            <p>電話: {user.phone}</p>
        </div>
    );
}

// app/blog/page.tsx
interface BlogPost {
    id: string;
    title: string;
    content: string;
    publishedAt: string;
}

async function getBlogPosts(): Promise<BlogPost[]> {
    // 実際の実装ではデータベースやAPIから取得
    await new Promise(resolve => setTimeout(resolve, 1000)); // 遅延をシミュレート
    
    return [
        { id: '1', title: '投稿1', content: '内容1', publishedAt: '2024-01-01' },
        { id: '2', title: '投稿2', content: '内容2', publishedAt: '2024-01-02' }
    ];
}

export default async function BlogPage() {
    const posts = await getBlogPosts();
    
    return (
        <div>
            <h1>ブログ</h1>
            {posts.map(post => (
                <article key={post.id}>
                    <h2>{post.title}</h2>
                    <p>{post.content}</p>
                    <time>{post.publishedAt}</time>
                </article>
            ))}
        </div>
    );
}
```

### エラーハンドリング

```tsx
// app/posts/page.tsx
async function getPosts() {
    try {
        const res = await fetch('https://api.example.com/posts');
        if (!res.ok) {
            throw new Error('投稿の取得に失敗しました');
        }
        return await res.json();
    } catch (error) {
        console.error('エラー:', error);
        throw error; // error.tsxに渡される
    }
}

export default async function PostsPage() {
    const posts = await getPosts();
    
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

---

## 動的データ取得 (Dynamic Rendering)

### 動的レンダリングとは
動的レンダリングは、**リクエストごとにデータを取得して、その都度HTMLを生成する**レンダリング方式です。常に最新のデータを表示したい場合に使用します。

### 動的レンダリングの特徴
- **常に最新**: リクエストごとに最新のデータを取得
- **パーソナライズ**: ユーザーごとに異なる内容を表示
- **リアルタイム**: リアルタイムで更新されるデータに対応

### 動的レンダリングの指定方法

#### 1. `dynamic`設定を使用

```tsx
// app/posts/page.tsx
export const dynamic = 'force-dynamic'; // 動的レンダリングを強制

export default async function PostsPage() {
    const posts = await getPosts();
    return <div>{/* ... */}</div>;
}
```

#### 2. 動的な関数を使用

```tsx
// app/posts/page.tsx
export default async function PostsPage() {
    // cookies()やheaders()などの動的な関数を使用すると自動的に動的レンダリングになる
    const cookies = await import('next/headers').then(m => m.cookies);
    const theme = cookies().get('theme');
    
    const posts = await getPosts();
    return <div>{/* ... */}</div>;
}
```

### 実践例: 動的データ取得

```tsx
// app/dashboard/page.tsx
export const dynamic = 'force-dynamic';

interface DashboardData {
    totalUsers: number;
    totalPosts: number;
    recentActivity: string[];
}

async function getDashboardData(): Promise<DashboardData> {
    // 実際の実装ではデータベースから取得
    await new Promise(resolve => setTimeout(resolve, 500));
    
    return {
        totalUsers: 100,
        totalPosts: 250,
        recentActivity: ['ユーザーAがログイン', '投稿Bが作成されました']
    };
}

export default async function DashboardPage() {
    const data = await getDashboardData();
    
    return (
        <div>
            <h1>ダッシュボード</h1>
            <div>
                <h2>統計</h2>
                <p>総ユーザー数: {data.totalUsers}</p>
                <p>総投稿数: {data.totalPosts}</p>
            </div>
            <div>
                <h2>最近のアクティビティ</h2>
                <ul>
                    {data.recentActivity.map((activity, index) => (
                        <li key={index}>{activity}</li>
                    ))}
                </ul>
            </div>
        </div>
    );
}

// app/profile/page.tsx
import { cookies } from 'next/headers';

interface User {
    id: string;
    name: string;
    email: string;
}

async function getUserProfile(): Promise<User> {
    // cookies()を使用すると自動的に動的レンダリングになる
    const cookieStore = await cookies();
    const userId = cookieStore.get('userId')?.value;
    
    if (!userId) {
        throw new Error('ユーザーIDがありません');
    }
    
    // 実際の実装ではデータベースから取得
    return {
        id: userId,
        name: '太郎',
        email: 'taro@example.com'
    };
}

export default async function ProfilePage() {
    const user = await getUserProfile();
    
    return (
        <div>
            <h1>プロフィール</h1>
            <p>名前: {user.name}</p>
            <p>メール: {user.email}</p>
        </div>
    );
}

// app/posts/[id]/page.tsx
import { headers } from 'next/headers';

interface PostPageProps {
    params: {
        id: string;
    };
}

async function getPost(id: string) {
    // headers()を使用すると自動的に動的レンダリングになる
    const headersList = await headers();
    const userAgent = headersList.get('user-agent');
    
    // 実際の実装ではデータベースから取得
    return {
        id,
        title: '投稿タイトル',
        content: '投稿内容',
        views: Math.floor(Math.random() * 1000) // 動的な値
    };
}

export default async function PostPage({ params }: PostPageProps) {
    const { id } = params;
    const post = await getPost(id);
    
    return (
        <article>
            <h1>{post.title}</h1>
            <p>{post.content}</p>
            <p>閲覧数: {post.views}</p>
        </article>
    );
}
```

---

## 静的データ取得 (Static Rendering)

### 静的レンダリングとは
静的レンダリングは、**ビルド時にデータを取得して、HTMLを事前に生成する**レンダリング方式です。同じ内容を表示するページに適しています。

### 静的レンダリングの特徴
- **高速**: 事前に生成されたHTMLを返すため高速
- **キャッシュ可能**: CDNでキャッシュできる
- **SEO**: 検索エンジンがインデックスしやすい

### 静的レンダリングの指定方法

#### 1. `dynamic`設定を使用

```tsx
// app/posts/page.tsx
export const dynamic = 'force-static'; // 静的レンダリングを強制

export default async function PostsPage() {
    const posts = await getPosts();
    return <div>{/* ... */}</div>;
}
```

#### 2. `revalidate`を使用

```tsx
// app/posts/page.tsx
export const revalidate = 3600; // 1時間ごとに再生成

export default async function PostsPage() {
    const posts = await getPosts();
    return <div>{/* ... */}</div>;
}
```

### 実践例: 静的データ取得

```tsx
// app/about/page.tsx
export const dynamic = 'force-static';

interface AboutData {
    title: string;
    description: string;
    team: { name: string; role: string }[];
}

async function getAboutData(): Promise<AboutData> {
    // 実際の実装ではデータベースやCMSから取得
    return {
        title: 'About Us',
        description: '私たちについて',
        team: [
            { name: '太郎', role: 'CEO' },
            { name: '花子', role: 'CTO' }
        ]
    };
}

export default async function AboutPage() {
    const data = await getAboutData();
    
    return (
        <div>
            <h1>{data.title}</h1>
            <p>{data.description}</p>
            <h2>チーム</h2>
            <ul>
                {data.team.map((member, index) => (
                    <li key={index}>
                        {member.name} - {member.role}
                    </li>
                ))}
            </ul>
        </div>
    );
}

// app/products/page.tsx
export const revalidate = 3600; // 1時間ごとに再生成

interface Product {
    id: string;
    name: string;
    price: number;
    description: string;
}

async function getProducts(): Promise<Product[]> {
    // 実際の実装ではデータベースから取得
    return [
        { id: '1', name: '商品A', price: 1000, description: '説明A' },
        { id: '2', name: '商品B', price: 2000, description: '説明B' }
    ];
}

export default async function ProductsPage() {
    const products = await getProducts();
    
    return (
        <div>
            <h1>商品一覧</h1>
            <ul>
                {products.map(product => (
                    <li key={product.id}>
                        <h2>{product.name}</h2>
                        <p>価格: ¥{product.price}</p>
                        <p>{product.description}</p>
                    </li>
                ))}
            </ul>
        </div>
    );
}

// app/blog/[slug]/page.tsx
interface BlogPost {
    slug: string;
    title: string;
    content: string;
    publishedAt: string;
}

// 静的生成するパスを指定
export async function generateStaticParams() {
    // ビルド時に生成するパスを返す
    return [
        { slug: 'post-1' },
        { slug: 'post-2' },
        { slug: 'post-3' }
    ];
}

async function getPost(slug: string): Promise<BlogPost> {
    // 実際の実装ではデータベースから取得
    return {
        slug,
        title: `投稿: ${slug}`,
        content: '投稿内容',
        publishedAt: '2024-01-01'
    };
}

export default async function BlogPostPage({
    params,
}: {
    params: { slug: string };
}) {
    const { slug } = params;
    const post = await getPost(slug);
    
    return (
        <article>
            <h1>{post.title}</h1>
            <time>{post.publishedAt}</time>
            <div>{post.content}</div>
        </article>
    );
}

// app/faq/page.tsx
export const dynamic = 'force-static';

interface FAQ {
    question: string;
    answer: string;
}

async function getFAQs(): Promise<FAQ[]> {
    // 実際の実装ではデータベースやCMSから取得
    return [
        { question: 'Q1', answer: 'A1' },
        { question: 'Q2', answer: 'A2' }
    ];
}

export default async function FAQPage() {
    const faqs = await getFAQs();
    
    return (
        <div>
            <h1>よくある質問</h1>
            <dl>
                {faqs.map((faq, index) => (
                    <div key={index}>
                        <dt>{faq.question}</dt>
                        <dd>{faq.answer}</dd>
                    </div>
                ))}
            </dl>
        </div>
    );
}
```

### generateStaticParams

`generateStaticParams`は、**動的ルートで静的生成するパスを指定する**関数です。

```tsx
// app/products/[id]/page.tsx
interface ProductPageProps {
    params: {
        id: string;
    };
}

export async function generateStaticParams() {
    // ビルド時に生成するパスを返す
    const products = await getProducts();
    return products.map(product => ({
        id: product.id.toString()
    }));
}

async function getProduct(id: string) {
    const res = await fetch(`https://api.example.com/products/${id}`);
    return res.json();
}

export default async function ProductPage({ params }: ProductPageProps) {
    const { id } = params;
    const product = await getProduct(id);
    
    return (
        <div>
            <h1>{product.name}</h1>
            <p>{product.description}</p>
        </div>
    );
}
```

---

## まとめ

この章では、Server Componentでのデータ取得方法について学びました。

### 学んだこと
- **非同期コンポーネント**: `async/await`を使ってデータを取得
- **動的データ取得**: リクエストごとに最新のデータを取得
- **静的データ取得**: ビルド時にデータを取得してHTMLを事前生成
- **generateStaticParams**: 動的ルートで静的生成するパスを指定

### 重要なポイント
1. **非同期コンポーネント**: Server Componentを`async`関数として定義
2. **動的レンダリング**: `dynamic = 'force-dynamic'`または動的な関数を使用
3. **静的レンダリング**: `dynamic = 'force-static'`または`revalidate`を使用
4. **generateStaticParams**: 動的ルートで静的生成するパスを指定
5. **エラーハンドリング**: 適切にエラーハンドリングを行う

### 使い分けの目安
- **動的レンダリング**: 常に最新のデータが必要な場合、ユーザーごとに異なる内容
- **静的レンダリング**: 同じ内容を表示するページ、パフォーマンスを重視する場合

### 次のステップ
Server Componentでのデータ取得を理解することで、効率的にデータを取得し、パフォーマンスを最適化できるようになります。次の章では、Routeのメタデータについて詳しく学びます。

---

## 演習問題

### 問題1: 基本的な非同期コンポーネント
非同期コンポーネントを作成し、APIからデータを取得して表示してください。

<details>
<summary>解答例</summary>

```tsx
// app/posts/page.tsx
interface Post {
    id: number;
    title: string;
}

async function getPosts(): Promise<Post[]> {
    const res = await fetch('https://jsonplaceholder.typicode.com/posts');
    return res.json();
}

export default async function PostsPage() {
    const posts = await getPosts();
    
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
</details>

### 問題2: 動的レンダリングの指定
動的レンダリングを指定して、リクエストごとに最新のデータを取得してください。

<details>
<summary>解答例</summary>

```tsx
// app/dashboard/page.tsx
export const dynamic = 'force-dynamic';

async function getDashboardData() {
    // データ取得処理
    return { count: Math.random() * 100 };
}

export default async function DashboardPage() {
    const data = await getDashboardData();
    
    return (
        <div>
            <h1>ダッシュボード</h1>
            <p>カウント: {data.count}</p>
        </div>
    );
}
```
</details>

### 問題3: 静的レンダリングの指定
静的レンダリングを指定して、ビルド時にデータを取得してください。

<details>
<summary>解答例</summary>

```tsx
// app/about/page.tsx
export const dynamic = 'force-static';

async function getAboutData() {
    return {
        title: 'About',
        description: '私たちについて'
    };
}

export default async function AboutPage() {
    const data = await getAboutData();
    
    return (
        <div>
            <h1>{data.title}</h1>
            <p>{data.description}</p>
        </div>
    );
}
```
</details>

### 問題4: revalidateの使用
`revalidate`を使って、一定時間ごとに再生成するように設定してください。

<details>
<summary>解答例</summary>

```tsx
// app/products/page.tsx
export const revalidate = 3600; // 1時間ごとに再生成

async function getProducts() {
    // データ取得処理
    return [];
}

export default async function ProductsPage() {
    const products = await getProducts();
    
    return (
        <div>
            <h1>商品一覧</h1>
            <ul>
                {products.map(product => (
                    <li key={product.id}>{product.name}</li>
                ))}
            </ul>
        </div>
    );
}
```
</details>

### 問題5: generateStaticParamsの使用
`generateStaticParams`を使って、動的ルートで静的生成するパスを指定してください。

<details>
<summary>解答例</summary>

```tsx
// app/posts/[id]/page.tsx
export async function generateStaticParams() {
    return [
        { id: '1' },
        { id: '2' },
        { id: '3' }
    ];
}

async function getPost(id: string) {
    const res = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
    return res.json();
}

export default async function PostPage({
    params,
}: {
    params: { id: string };
}) {
    const { id } = params;
    const post = await getPost(id);
    
    return (
        <article>
            <h1>{post.title}</h1>
            <p>{post.body}</p>
        </article>
    );
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

