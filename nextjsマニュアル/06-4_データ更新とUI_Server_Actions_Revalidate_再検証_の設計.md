# 6-4. データ更新とUI（Revalidate (再検証) の設計）

この章では、Next.jsアプリケーションでキャッシュを再検証する方法について学びます。`revalidatePath`と`revalidateTag`を理解することで、データ更新後に適切にキャッシュを更新できるようになります。

---

## 目次

- [キャッシュパージの戦略 (revalidatePath, revalidateTag)](#キャッシュパージの戦略-revalidatepath-revalidatetag)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## キャッシュパージの戦略 (revalidatePath, revalidateTag)

### 再検証とは
再検証は、**キャッシュされたデータを無効化し、最新のデータを取得する**仕組みです。データが更新された後、キャッシュを更新するために使用します。

### 日常生活での例
- **冷蔵庫の整理**: 古い食材（キャッシュ）を捨てて、新しい食材（最新データ）を入れる
- **図書館の本**: 古い本（キャッシュ）を返却して、新しい本（最新データ）を借りる
- **倉庫の在庫**: 古い在庫（キャッシュ）を出荷して、新しい在庫（最新データ）を入荷する

### 再検証の種類
1. **revalidatePath**: 特定のパス（ページ）のキャッシュを再検証
2. **revalidateTag**: 特定のタグが付いたデータのキャッシュを再検証

### revalidatePathとは
`revalidatePath`は、**特定のパス（ページ）のキャッシュを再検証する**関数です。ページ全体のキャッシュを無効化し、次回アクセス時に最新のデータを取得します。

### 実践例: 基本的なrevalidatePath

```tsx
// app/actions/posts.ts
'use server';

import { revalidatePath } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createPost(title: string, content: string) {
    const post = await prisma.post.create({
        data: {
            title,
            content,
        },
    });

    // 投稿一覧ページのキャッシュを再検証
    revalidatePath('/posts');

    return post;
}

export async function updatePost(id: string, title: string, content: string) {
    const post = await prisma.post.update({
        where: { id },
        data: {
            title,
            content,
        },
    });

    // 投稿一覧ページと投稿詳細ページのキャッシュを再検証
    revalidatePath('/posts');
    revalidatePath(`/posts/${id}`);

    return post;
}

export async function deletePost(id: string) {
    await prisma.post.delete({
        where: { id },
    });

    // 投稿一覧ページのキャッシュを再検証
    revalidatePath('/posts');

    return { success: true };
}
```

### 実践例: 動的ルートでのrevalidatePath

```tsx
// app/actions/posts.ts
'use server';

import { revalidatePath } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function updatePost(id: string, title: string, content: string) {
    const post = await prisma.post.update({
        where: { id },
        data: {
            title,
            content,
        },
    });

    // 動的ルートの再検証
    revalidatePath('/posts/[id]', 'page'); // 特定のページタイプ
    revalidatePath('/posts', 'layout'); // レイアウトの再検証

    return post;
}
```

### 実践例: 複数のパスの再検証

```tsx
// app/actions/posts.ts
'use server';

import { revalidatePath } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createPost(title: string, content: string) {
    const post = await prisma.post.create({
        data: {
            title,
            content,
        },
    });

    // 複数のパスを再検証
    revalidatePath('/posts'); // 投稿一覧
    revalidatePath('/'); // ホームページ（最新の投稿を表示する場合）
    revalidatePath('/dashboard'); // ダッシュボード（投稿数を表示する場合）

    return post;
}
```

### 実践例: Server ActionでのrevalidatePath

```tsx
// app/components/CreatePostForm.tsx
'use client';

import { createPost } from '@/app/actions/posts';
import { useTransition } from 'react';

export default function CreatePostForm() {
    const [isPending, startTransition] = useTransition();

    const handleSubmit = async (formData: FormData) => {
        const title = formData.get('title') as string;
        const content = formData.get('content') as string;

        startTransition(async () => {
            await createPost(title, content);
            // createPost内でrevalidatePathが実行される
        });
    };

    return (
        <form action={handleSubmit}>
            <input name="title" placeholder="タイトル" />
            <textarea name="content" placeholder="内容" />
            <button type="submit" disabled={isPending}>
                {isPending ? '作成中...' : '作成'}
            </button>
        </form>
    );
}
```

### revalidateTagとは
`revalidateTag`は、**特定のタグが付いたデータのキャッシュを再検証する**関数です。複数のページで同じデータを使用している場合に便利です。

### 実践例: 基本的なrevalidateTag

```tsx
// app/posts/page.tsx
import { fetch } from 'next/cache';

async function getPosts() {
    // タグを設定してデータを取得
    const res = await fetch('https://api.example.com/posts', {
        next: { tags: ['posts'] }, // タグを設定
    });
    return res.json();
}

export default async function PostsPage() {
    const posts = await getPosts();

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

// app/actions/posts.ts
'use server';

import { revalidateTag } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createPost(title: string, content: string) {
    const post = await prisma.post.create({
        data: {
            title,
            content,
        },
    });

    // タグを指定して再検証
    revalidateTag('posts'); // 'posts'タグが付いたすべてのデータを再検証

    return post;
}
```

### 実践例: 複数のタグの使用

```tsx
// app/posts/page.tsx
async function getPosts() {
    const res = await fetch('https://api.example.com/posts', {
        next: { tags: ['posts', 'all-posts'] }, // 複数のタグを設定
    });
    return res.json();
}

// app/dashboard/page.tsx
async function getPostCount() {
    const res = await fetch('https://api.example.com/posts/count', {
        next: { tags: ['posts', 'stats'] }, // 複数のタグを設定
    });
    return res.json();
}

// app/actions/posts.ts
'use server';

import { revalidateTag } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createPost(title: string, content: string) {
    const post = await prisma.post.create({
        data: {
            title,
            content,
        },
    });

    // 複数のタグを再検証
    revalidateTag('posts'); // 投稿関連のデータを再検証
    revalidateTag('stats'); // 統計データを再検証

    return post;
}
```

### 実践例: タグとパスの組み合わせ

```tsx
// app/posts/page.tsx
async function getPosts() {
    const res = await fetch('https://api.example.com/posts', {
        next: { tags: ['posts'] },
    });
    return res.json();
}

// app/posts/[id]/page.tsx
async function getPost(id: string) {
    const res = await fetch(`https://api.example.com/posts/${id}`, {
        next: { tags: ['posts', `post-${id}`] }, // 特定の投稿にもタグを設定
    });
    return res.json();
}

// app/actions/posts.ts
'use server';

import { revalidateTag, revalidatePath } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function updatePost(id: string, title: string, content: string) {
    const post = await prisma.post.update({
        where: { id },
        data: {
            title,
            content,
        },
    });

    // タグとパスの両方を再検証
    revalidateTag('posts'); // すべての投稿データを再検証
    revalidateTag(`post-${id}`); // 特定の投稿を再検証
    revalidatePath('/posts'); // 投稿一覧ページを再検証
    revalidatePath(`/posts/${id}`); // 投稿詳細ページを再検証

    return post;
}
```

### 実践例: Route Handlerでの再検証

```tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server';
import { revalidatePath, revalidateTag } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function POST(request: Request) {
    const { title, content } = await request.json();

    const post = await prisma.post.create({
        data: {
            title,
            content,
        },
    });

    // Route Handlerでも再検証可能
    revalidatePath('/posts');
    revalidateTag('posts');

    return NextResponse.json(post);
}
```

### 実践例: Webhookでの再検証

```tsx
// app/api/webhook/revalidate/route.ts
import { NextResponse } from 'next/server';
import { revalidatePath, revalidateTag } from 'next/cache';

export async function POST(request: Request) {
    try {
        const { path, tag } = await request.json();

        // パスの再検証
        if (path) {
            revalidatePath(path);
        }

        // タグの再検証
        if (tag) {
            revalidateTag(tag);
        }

        return NextResponse.json({ revalidated: true });
    } catch (error) {
        return NextResponse.json(
            { error: '再検証に失敗しました' },
            { status: 500 }
        );
    }
}

// 外部サービス（例: CMS）からのWebhook
// POST /api/webhook/revalidate
// Body: { "path": "/posts", "tag": "posts" }
```

### 実践例: 条件付き再検証

```tsx
// app/actions/posts.ts
'use server';

import { revalidatePath, revalidateTag } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function updatePost(
    id: string,
    title: string,
    content: string,
    options?: { revalidateHome?: boolean }
) {
    const post = await prisma.post.update({
        where: { id },
        data: {
            title,
            content,
        },
    });

    // 常に再検証
    revalidatePath('/posts');
    revalidateTag('posts');

    // 条件付きで再検証
    if (options?.revalidateHome) {
        revalidatePath('/');
    }

    return post;
}
```

### 実践例: バッチ再検証

```tsx
// app/actions/posts.ts
'use server';

import { revalidatePath, revalidateTag } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function batchUpdatePosts(updates: Array<{ id: string; title: string }>) {
    // 複数の投稿を一括更新
    const results = await Promise.all(
        updates.map(update =>
            prisma.post.update({
                where: { id: update.id },
                data: { title: update.title },
            })
        )
    );

    // 一度だけ再検証（効率的）
    revalidatePath('/posts');
    revalidateTag('posts');

    return results;
}
```

### 実践例: エラーハンドリングを含む再検証

```tsx
// app/actions/posts.ts
'use server';

import { revalidatePath, revalidateTag } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createPost(title: string, content: string) {
    try {
        const post = await prisma.post.create({
            data: {
                title,
                content,
            },
        });

        // 成功時に再検証
        revalidatePath('/posts');
        revalidateTag('posts');

        return { success: true, post };
    } catch (error) {
        console.error('投稿作成エラー:', error);
        // エラー時は再検証しない
        return { success: false, error: '投稿の作成に失敗しました' };
    }
}
```

### 実践例: 再検証のタイミング制御

```tsx
// app/actions/posts.ts
'use server';

import { revalidatePath, revalidateTag } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createPost(
    title: string,
    content: string,
    options?: { immediate?: boolean }
) {
    const post = await prisma.post.create({
        data: {
            title,
            content,
        },
    });

    // 即座に再検証（デフォルト）
    if (options?.immediate !== false) {
        revalidatePath('/posts');
        revalidateTag('posts');
    }

    // または、非同期で再検証（バックグラウンド）
    if (options?.immediate === false) {
        // バックグラウンドで再検証
        setTimeout(() => {
            revalidatePath('/posts');
            revalidateTag('posts');
        }, 0);
    }

    return post;
}
```

---

## まとめ

この章では、Revalidate (再検証) の設計について以下のことを学びました。

### キャッシュパージの戦略
- **revalidatePath**: 特定のパス（ページ）のキャッシュを再検証
- **revalidateTag**: 特定のタグが付いたデータのキャッシュを再検証
- **使い分け**: パスはページ単位、タグはデータ単位で再検証

### revalidatePath
- **基本的な使用**: `revalidatePath('/posts')`で投稿一覧ページを再検証
- **動的ルート**: `revalidatePath('/posts/[id]', 'page')`で動的ルートを再検証
- **複数のパス**: 複数のパスを個別に再検証可能
- **Server Action**: Server Action内で使用して、データ更新後にキャッシュを更新

### revalidateTag
- **基本的な使用**: `revalidateTag('posts')`で'posts'タグが付いたデータを再検証
- **複数のタグ**: 複数のタグを個別に再検証可能
- **タグの設定**: `fetch`の`next.tags`でタグを設定
- **効率的**: 複数のページで同じデータを使用している場合に便利

### 再検証のベストプラクティス
- **データ更新後**: データを更新した後、必ず再検証する
- **エラーハンドリング**: エラー時は再検証しない
- **バッチ処理**: 複数の更新を一括処理する場合、一度だけ再検証する
- **条件付き再検証**: 必要に応じて条件付きで再検証する

### 再検証のメリット
- **最新データ**: データ更新後、最新のデータを表示できる
- **パフォーマンス**: キャッシュを活用しながら、最新データを保証
- **ユーザー体験**: データ更新後、即座に最新の状態を表示できる

### 次のステップ
Revalidate (再検証) の設計を理解したら、次のトピックに進みましょう：
- パフォーマンスとキャッシュの最適化（9章）
- デプロイと運用（10章）

---

## 演習問題

以下の問題を解いて、Revalidate (再検証) の設計の理解を深めましょう。

### 問題1: revalidatePathの使用
投稿を作成した後、投稿一覧ページのキャッシュを再検証するServer Actionを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/actions/posts.ts
'use server';

import { revalidatePath } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createPost(title: string, content: string) {
    const post = await prisma.post.create({
        data: {
            title,
            content,
        },
    });

    revalidatePath('/posts');

    return post;
}
```
</details>

### 問題2: revalidateTagの使用
タグを使用してデータを取得し、データ更新後にタグを再検証する機能を実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/posts/page.tsx
async function getPosts() {
    const res = await fetch('https://api.example.com/posts', {
        next: { tags: ['posts'] },
    });
    return res.json();
}

// app/actions/posts.ts
'use server';

import { revalidateTag } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createPost(title: string, content: string) {
    const post = await prisma.post.create({
        data: {
            title,
            content,
        },
    });

    revalidateTag('posts');

    return post;
}
```
</details>

### 問題3: 複数のパスの再検証
投稿を更新した後、投稿一覧ページと投稿詳細ページの両方のキャッシュを再検証してください。

<details>
<summary>解答例</summary>

```tsx
// app/actions/posts.ts
'use server';

import { revalidatePath } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function updatePost(id: string, title: string, content: string) {
    const post = await prisma.post.update({
        where: { id },
        data: {
            title,
            content,
        },
    });

    revalidatePath('/posts');
    revalidatePath(`/posts/${id}`);

    return post;
}
```
</details>

### 問題4: エラーハンドリングを含む再検証
エラーハンドリングを含む投稿作成機能を実装し、成功時のみ再検証してください。

<details>
<summary>解答例</summary>

```tsx
// app/actions/posts.ts
'use server';

import { revalidatePath } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createPost(title: string, content: string) {
    try {
        const post = await prisma.post.create({
            data: {
                title,
                content,
            },
        });

        revalidatePath('/posts');

        return { success: true, post };
    } catch (error) {
        return { success: false, error: '投稿の作成に失敗しました' };
    }
}
```
</details>

### 問題5: タグとパスの組み合わせ
タグとパスの両方を使用して、効率的に再検証する機能を実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/posts/page.tsx
async function getPosts() {
    const res = await fetch('https://api.example.com/posts', {
        next: { tags: ['posts'] },
    });
    return res.json();
}

// app/actions/posts.ts
'use server';

import { revalidatePath, revalidateTag } from 'next/cache';
import { prisma } from '@/lib/prisma';

export async function createPost(title: string, content: string) {
    const post = await prisma.post.create({
        data: {
            title,
            content,
        },
    });

    revalidateTag('posts');
    revalidatePath('/posts');

    return post;
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

