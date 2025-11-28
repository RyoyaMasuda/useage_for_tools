# 4-2. Prisma Clientでのデータ取得

この章では、Prisma Clientを使用したデータ取得について学びます。Next.jsのServer ComponentでPrisma Clientを使用することで、型安全で効率的なデータベースアクセスが可能になります。

---

## 目次

- [Prismaを用いたDBアクセス](#prismaを用いたdbアクセス)
- [Prisma ClientのRequestのメモ化](#prisma-clientのrequestのメモ化)
- [Prisma Clientのキャッシュ戦略 (unstable_cache 等の活用)](#prisma-clientのキャッシュ戦略-unstable_cache-等の活用)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## Prismaを用いたDBアクセス

### Prisma Clientの基本
Prisma Clientは、**Prisma Schemaから自動生成される型安全なデータベースクライアント**です。Next.jsのServer Componentで使用することで、サーバーサイドで直接データベースにアクセスできます。

### Prisma Clientのセットアップ

```typescript
// src/lib/prisma.ts
import { PrismaClient } from '@prisma/client'

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined
}

export const prisma = globalForPrisma.prisma ?? new PrismaClient()

if (process.env.NODE_ENV !== 'production') {
  globalForPrisma.prisma = prisma
}
```

### 基本的なデータ取得

#### 全件取得

```typescript
// src/app/users/page.tsx
import prisma from '@/lib/prisma'

export default async function UsersPage() {
  const users = await prisma.user.findMany()

  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            {user.name} ({user.email})
          </li>
        ))}
      </ul>
    </div>
  )
}
```

#### 単一レコードの取得

```typescript
// src/app/users/[id]/page.tsx
import prisma from '@/lib/prisma'
import { notFound } from 'next/navigation'

interface UserPageProps {
  params: {
    id: string
  }
}

export default async function UserPage({ params }: UserPageProps) {
  const user = await prisma.user.findUnique({
    where: {
      id: parseInt(params.id),
    },
  })

  if (!user) {
    notFound()
  }

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
    </div>
  )
}
```

#### 条件に一致するレコードの取得

```typescript
// src/app/posts/page.tsx
import prisma from '@/lib/prisma'

export default async function PostsPage() {
  // 公開されている投稿のみを取得
  const posts = await prisma.post.findMany({
    where: {
      published: true,
    },
    orderBy: {
      createdAt: 'desc',
    },
  })

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

### リレーションを含むデータ取得

#### includeを使用したリレーションの取得

```typescript
// src/app/posts/page.tsx
import prisma from '@/lib/prisma'

export default async function PostsPage() {
  const posts = await prisma.post.findMany({
    include: {
      author: true,  // 投稿者情報を含める
      tags: true,    // タグ情報を含める
    },
  })

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>
            <h2>{post.title}</h2>
            <p>By {post.author.name}</p>
            <div>
              Tags: {post.tags.map((tag) => tag.name).join(', ')}
            </div>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

#### selectを使用した特定フィールドの取得

```typescript
// src/app/posts/page.tsx
import prisma from '@/lib/prisma'

export default async function PostsPage() {
  const posts = await prisma.post.findMany({
    select: {
      id: true,
      title: true,
      author: {
        select: {
          name: true,
        },
      },
    },
  })

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>
            <h2>{post.title}</h2>
            <p>By {post.author.name}</p>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

### ページネーション

```typescript
// src/app/posts/page.tsx
import prisma from '@/lib/prisma'
import Link from 'next/link'

interface PostsPageProps {
  searchParams: {
    page?: string
  }
}

export default async function PostsPage({ searchParams }: PostsPageProps) {
  const page = parseInt(searchParams.page || '1')
  const pageSize = 10
  const skip = (page - 1) * pageSize

  const [posts, total] = await Promise.all([
    prisma.post.findMany({
      skip,
      take: pageSize,
      orderBy: {
        createdAt: 'desc',
      },
    }),
    prisma.post.count(),
  ])

  const totalPages = Math.ceil(total / pageSize)

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
          </li>
        ))}
      </ul>
      <div>
        {page > 1 && (
          <Link href={`/posts?page=${page - 1}`}>Previous</Link>
        )}
        {page < totalPages && (
          <Link href={`/posts?page=${page + 1}`}>Next</Link>
        )}
      </div>
    </div>
  )
}
```

### 集計クエリ

```typescript
// src/app/dashboard/page.tsx
import prisma from '@/lib/prisma'

export default async function DashboardPage() {
  const [userCount, postCount, publishedPostCount] = await Promise.all([
    prisma.user.count(),
    prisma.post.count(),
    prisma.post.count({
      where: {
        published: true,
      },
    }),
  ])

  return (
    <div>
      <h1>Dashboard</h1>
      <div>
        <p>Total Users: {userCount}</p>
        <p>Total Posts: {postCount}</p>
        <p>Published Posts: {publishedPostCount}</p>
      </div>
    </div>
  )
}
```

### 実践例: 複雑なクエリ

```typescript
// src/app/posts/[id]/page.tsx
import prisma from '@/lib/prisma'
import { notFound } from 'next/navigation'

interface PostPageProps {
  params: {
    id: string
  }
}

export default async function PostPage({ params }: PostPageProps) {
  const post = await prisma.post.findUnique({
    where: {
      id: parseInt(params.id),
    },
    include: {
      author: {
        select: {
          id: true,
          name: true,
          email: true,
        },
      },
      tags: {
        select: {
          id: true,
          name: true,
        },
      },
      _count: {
        select: {
          comments: true,
        },
      },
    },
  })

  if (!post) {
    notFound()
  }

  return (
    <div>
      <h1>{post.title}</h1>
      <p>By {post.author.name}</p>
      <div>
        <p>{post.content}</p>
      </div>
      <div>
        Tags: {post.tags.map((tag) => tag.name).join(', ')}
      </div>
      <div>
        Comments: {post._count.comments}
      </div>
    </div>
  )
}
```

---

## Prisma ClientのRequestのメモ化

### Request Memoizationとは
Request Memoizationは、**同じリクエスト内で同じクエリを複数回実行した場合、最初の結果を再利用する仕組み**です。Next.jsでは、自動的にRequest Memoizationが適用されます。

### Request Memoizationの動作

#### 同じリクエスト内での重複クエリ

```typescript
// src/app/posts/[id]/page.tsx
import prisma from '@/lib/prisma'

async function getPost(id: number) {
  console.log('🔵 Fetching post:', id)
  return await prisma.post.findUnique({
    where: { id },
  })
}

export default async function PostPage({ params }: { params: { id: string } }) {
  const postId = parseInt(params.id)

  // 同じリクエスト内で同じクエリを複数回実行
  const [post1, post2, post3] = await Promise.all([
    getPost(postId),
    getPost(postId),
    getPost(postId),
  ])

  // Request Memoizationにより、実際のクエリは1回だけ実行される
  // コンソールには "🔵 Fetching post: 1" が1回だけ表示される

  return (
    <div>
      <h1>{post1?.title}</h1>
    </div>
  )
}
```

### Request Memoizationの条件

Request Memoizationが適用される条件：
1. **同じリクエスト内**: 同じHTTPリクエストの処理中
2. **同じクエリ**: 同じパラメータで同じPrismaクエリを実行
3. **Server Component**: Server Component内でのみ適用される

### 実践例: Request Memoizationの活用

```typescript
// src/app/posts/[id]/page.tsx
import prisma from '@/lib/prisma'
import PostContent from '@/components/PostContent'
import PostAuthor from '@/components/PostAuthor'
import PostComments from '@/components/PostComments'

async function getPost(id: number) {
  return await prisma.post.findUnique({
    where: { id },
    include: {
      author: true,
    },
  })
}

export default async function PostPage({ params }: { params: { id: string } }) {
  const postId = parseInt(params.id)

  // 同じリクエスト内で同じ関数を複数回呼び出し
  // Request Memoizationにより、実際のクエリは1回だけ実行される
  const [post, postForAuthor, postForComments] = await Promise.all([
    getPost(postId),
    getPost(postId),
    getPost(postId),
  ])

  return (
    <div>
      <PostContent post={post} />
      <PostAuthor post={postForAuthor} />
      <PostComments postId={postId} />
    </div>
  )
}
```

### 実践例: 複数のコンポーネントでの共有

```typescript
// src/lib/posts.ts
import prisma from '@/lib/prisma'

export async function getPost(id: number) {
  return await prisma.post.findUnique({
    where: { id },
    include: {
      author: true,
    },
  })
}

export async function getPosts() {
  return await prisma.post.findMany({
    orderBy: {
      createdAt: 'desc',
    },
  })
}
```

```typescript
// src/app/posts/[id]/page.tsx
import { getPost } from '@/lib/posts'
import PostContent from '@/components/PostContent'
import PostSidebar from '@/components/PostSidebar'

export default async function PostPage({ params }: { params: { id: string } }) {
  const postId = parseInt(params.id)

  // 同じリクエスト内で同じ関数を複数回呼び出し
  const [post1, post2] = await Promise.all([
    getPost(postId),
    getPost(postId),
  ])

  // Request Memoizationにより、実際のクエリは1回だけ実行される

  return (
    <div>
      <PostContent post={post1} />
      <PostSidebar post={post2} />
    </div>
  )
}
```

### Request Memoizationの注意点

#### 異なるパラメータの場合は適用されない

```typescript
// Request Memoizationは適用されない（異なるパラメータ）
const [post1, post2] = await Promise.all([
  getPost(1),
  getPost(2),
])
```

#### 異なるクエリの場合は適用されない

```typescript
// Request Memoizationは適用されない（異なるクエリ）
const [post1, post2] = await Promise.all([
  prisma.post.findUnique({ where: { id: 1 } }),
  prisma.post.findFirst({ where: { title: 'Hello' } }),
])
```

---

## Prisma Clientのキャッシュ戦略 (unstable_cache 等の活用)

### キャッシュとは
キャッシュは、**データを一時的に保存し、同じデータを取得する際のパフォーマンスを向上させる仕組み**です。

### unstable_cacheとは
`unstable_cache`は、**Next.jsが提供するキャッシュ機能**です。データをキャッシュし、一定時間内はキャッシュからデータを返します。

### unstable_cacheの基本的な使い方

```typescript
import { unstable_cache } from 'next/cache'
import prisma from '@/lib/prisma'

async function getCachedPosts() {
  return await unstable_cache(
    async () => {
      return await prisma.post.findMany({
        orderBy: {
          createdAt: 'desc',
        },
      })
    },
    ['posts'],  // キャッシュキー
    {
      revalidate: 60,  // 60秒後にキャッシュを無効化
    }
  )()
}

export default async function PostsPage() {
  const posts = await getCachedPosts()

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

### キャッシュキーの指定

```typescript
import { unstable_cache } from 'next/cache'
import prisma from '@/lib/prisma'

async function getCachedPost(id: number) {
  return await unstable_cache(
    async () => {
      return await prisma.post.findUnique({
        where: { id },
      })
    },
    ['post', id.toString()],  // キャッシュキーにIDを含める
    {
      revalidate: 300,  // 5分後にキャッシュを無効化
    }
  )()
}

export default async function PostPage({ params }: { params: { id: string } }) {
  const post = await getCachedPost(parseInt(params.id))

  return (
    <div>
      <h1>{post?.title}</h1>
      <p>{post?.content}</p>
    </div>
  )
}
```

### タグベースのキャッシュ

```typescript
import { unstable_cache } from 'next/cache'
import prisma from '@/lib/prisma'

async function getCachedPosts() {
  return await unstable_cache(
    async () => {
      return await prisma.post.findMany({
        orderBy: {
          createdAt: 'desc',
        },
      })
    },
    ['posts'],
    {
      tags: ['posts'],  // タグを指定
      revalidate: 60,
    }
  )()
}

// 別の場所でキャッシュを無効化
import { revalidateTag } from 'next/cache'

export async function createPost(data: { title: string; content: string }) {
  await prisma.post.create({ data })
  revalidateTag('posts')  // 'posts'タグのキャッシュを無効化
}
```

### 実践例: 完全なキャッシュ戦略

```typescript
// src/lib/cached-queries.ts
import { unstable_cache } from 'next/cache'
import prisma from '@/lib/prisma'

// 投稿一覧をキャッシュ
export async function getCachedPosts() {
  return await unstable_cache(
    async () => {
      return await prisma.post.findMany({
        where: {
          published: true,
        },
        orderBy: {
          createdAt: 'desc',
        },
        include: {
          author: {
            select: {
              id: true,
              name: true,
            },
          },
        },
      })
    },
    ['posts'],
    {
      tags: ['posts'],
      revalidate: 60,  // 60秒
    }
  )()
}

// 投稿をキャッシュ
export async function getCachedPost(id: number) {
  return await unstable_cache(
    async () => {
      return await prisma.post.findUnique({
        where: { id },
        include: {
          author: {
            select: {
              id: true,
              name: true,
            },
          },
        },
      })
    },
    ['post', id.toString()],
    {
      tags: ['posts', `post-${id}`],
      revalidate: 300,  // 5分
    }
  )()
}

// ユーザー統計をキャッシュ
export async function getCachedUserStats(userId: number) {
  return await unstable_cache(
    async () => {
      const [postCount, commentCount] = await Promise.all([
        prisma.post.count({
          where: { authorId: userId },
        }),
        prisma.comment.count({
          where: { authorId: userId },
        }),
      ])

      return { postCount, commentCount }
    },
    ['user-stats', userId.toString()],
    {
      tags: ['user-stats', `user-${userId}`],
      revalidate: 600,  // 10分
    }
  )()
}
```

### 実践例: キャッシュの無効化

```typescript
// src/app/api/posts/route.ts
import { NextResponse } from 'next/server'
import { revalidateTag } from 'next/cache'
import prisma from '@/lib/prisma'

// POST: 投稿を作成
export async function POST(request: Request) {
  const body = await request.json()

  const post = await prisma.post.create({
    data: {
      title: body.title,
      content: body.content,
      authorId: body.authorId,
    },
  })

  // キャッシュを無効化
  revalidateTag('posts')

  return NextResponse.json(post, { status: 201 })
}

// PUT: 投稿を更新
export async function PUT(request: Request) {
  const body = await request.json()

  const post = await prisma.post.update({
    where: { id: body.id },
    data: {
      title: body.title,
      content: body.content,
    },
  })

  // キャッシュを無効化
  revalidateTag('posts')
  revalidateTag(`post-${body.id}`)

  return NextResponse.json(post)
}

// DELETE: 投稿を削除
export async function DELETE(request: Request) {
  const { searchParams } = new URL(request.url)
  const id = parseInt(searchParams.get('id') || '0')

  await prisma.post.delete({
    where: { id },
  })

  // キャッシュを無効化
  revalidateTag('posts')
  revalidateTag(`post-${id}`)

  return NextResponse.json({ success: true })
}
```

### 実践例: 条件付きキャッシュ

```typescript
import { unstable_cache } from 'next/cache'
import prisma from '@/lib/prisma'

async function getCachedPosts(published: boolean = true) {
  return await unstable_cache(
    async () => {
      return await prisma.post.findMany({
        where: {
          published,
        },
        orderBy: {
          createdAt: 'desc',
        },
      })
    },
    ['posts', published.toString()],  // キャッシュキーに条件を含める
    {
      tags: ['posts'],
      revalidate: 60,
    }
  )()
}

export default async function PostsPage({
  searchParams,
}: {
  searchParams: { published?: string }
}) {
  const published = searchParams.published !== 'false'
  const posts = await getCachedPosts(published)

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

### 実践例: キャッシュとRequest Memoizationの組み合わせ

```typescript
import { unstable_cache } from 'next/cache'
import prisma from '@/lib/prisma'

async function getPost(id: number) {
  return await unstable_cache(
    async () => {
      return await prisma.post.findUnique({
        where: { id },
        include: {
          author: true,
        },
      })
    },
    ['post', id.toString()],
    {
      tags: ['posts', `post-${id}`],
      revalidate: 300,
    }
  )()
}

export default async function PostPage({ params }: { params: { id: string } }) {
  const postId = parseInt(params.id)

  // Request Memoizationにより、同じリクエスト内での重複呼び出しは
  // キャッシュ関数内の処理が1回だけ実行される
  const [post1, post2] = await Promise.all([
    getPost(postId),
    getPost(postId),
  ])

  return (
    <div>
      <h1>{post1?.title}</h1>
      <p>{post2?.author.name}</p>
    </div>
  )
}
```

---

## まとめ

この章では、Prisma Clientでのデータ取得について学びました。

### 学んだこと
- Prismaを用いたDBアクセス: Prisma Clientを使用したデータベースアクセス
- Prisma ClientのRequestのメモ化: 同じリクエスト内での重複クエリの最適化
- Prisma Clientのキャッシュ戦略: unstable_cacheを使用したキャッシュの実装

### 重要なポイント
1. **Prisma Client**: 型安全なデータベースクライアント
2. **Request Memoization**: 同じリクエスト内での重複クエリを自動的に最適化
3. **unstable_cache**: データをキャッシュし、パフォーマンスを向上
4. **キャッシュの無効化**: revalidateTagを使用してキャッシュを無効化

### 次のステップ
次の章では、Route Handler (API Routes)について詳しく学びます。

---

## 演習問題

### 問題1: 基本的なデータ取得
Prisma Clientを使用して、ユーザー一覧を取得し、表示するページを作成してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/users/page.tsx
import prisma from '@/lib/prisma'

export default async function UsersPage() {
  const users = await prisma.user.findMany()

  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            {user.name} ({user.email})
          </li>
        ))}
      </ul>
    </div>
  )
}
```
</details>

### 問題2: リレーションを含むデータ取得
Prisma Clientを使用して、投稿一覧を取得し、投稿者情報を含めて表示するページを作成してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/posts/page.tsx
import prisma from '@/lib/prisma'

export default async function PostsPage() {
  const posts = await prisma.post.findMany({
    include: {
      author: true,
    },
    orderBy: {
      createdAt: 'desc',
    },
  })

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
            <p>By {post.author.name}</p>
          </li>
        ))}
      </ul>
    </div>
  )
}
```
</details>

### 問題3: Request Memoizationの確認
同じリクエスト内で同じPrismaクエリを複数回実行し、Request Memoizationが適用されることを確認してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/posts/[id]/page.tsx
import prisma from '@/lib/prisma'

async function getPost(id: number) {
  console.log('🔵 Fetching post:', id)
  return await prisma.post.findUnique({
    where: { id },
  })
}

export default async function PostPage({ params }: { params: { id: string } }) {
  const postId = parseInt(params.id)

  // 同じリクエスト内で同じクエリを複数回実行
  const [post1, post2, post3] = await Promise.all([
    getPost(postId),
    getPost(postId),
    getPost(postId),
  ])

  // Request Memoizationにより、実際のクエリは1回だけ実行される
  // コンソールには "🔵 Fetching post: 1" が1回だけ表示される

  return (
    <div>
      <h1>{post1?.title}</h1>
    </div>
  )
}
```
</details>

### 問題4: unstable_cacheの実装
unstable_cacheを使用して、投稿一覧をキャッシュする関数を作成してください。

<details>
<summary>解答例</summary>

```typescript
// src/lib/cached-queries.ts
import { unstable_cache } from 'next/cache'
import prisma from '@/lib/prisma'

export async function getCachedPosts() {
  return await unstable_cache(
    async () => {
      return await prisma.post.findMany({
        where: {
          published: true,
        },
        orderBy: {
          createdAt: 'desc',
        },
      })
    },
    ['posts'],
    {
      tags: ['posts'],
      revalidate: 60,  // 60秒
    }
  )()
}
```

```typescript
// src/app/posts/page.tsx
import { getCachedPosts } from '@/lib/cached-queries'

export default async function PostsPage() {
  const posts = await getCachedPosts()

  return (
    <div>
      <h1>Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>
            <h2>{post.title}</h2>
            <p>{post.content}</p>
          </li>
        ))}
      </ul>
    </div>
  )
}
```
</details>

### 問題5: キャッシュの無効化
投稿を作成・更新・削除した際に、キャッシュを無効化するAPI Routeを作成してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/api/posts/route.ts
import { NextResponse } from 'next/server'
import { revalidateTag } from 'next/cache'
import prisma from '@/lib/prisma'

export async function POST(request: Request) {
  const body = await request.json()

  const post = await prisma.post.create({
    data: {
      title: body.title,
      content: body.content,
      authorId: body.authorId,
    },
  })

  // キャッシュを無効化
  revalidateTag('posts')

  return NextResponse.json(post, { status: 201 })
}

export async function PUT(request: Request) {
  const body = await request.json()

  const post = await prisma.post.update({
    where: { id: body.id },
    data: {
      title: body.title,
      content: body.content,
    },
  })

  // キャッシュを無効化
  revalidateTag('posts')
  revalidateTag(`post-${body.id}`)

  return NextResponse.json(post)
}

export async function DELETE(request: Request) {
  const { searchParams } = new URL(request.url)
  const id = parseInt(searchParams.get('id') || '0')

  await prisma.post.delete({
    where: { id },
  })

  // キャッシュを無効化
  revalidateTag('posts')
  revalidateTag(`post-${id}`)

  return NextResponse.json({ success: true })
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

