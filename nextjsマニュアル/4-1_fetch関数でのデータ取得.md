# 4-1. fetch関数でのデータ取得

この章では、Next.jsのApp Routerにおける`fetch`関数を使用したデータ取得について学びます。Next.jsは`fetch`関数を拡張しており、自動的なリクエストのメモ化やキャッシュ機能を提供します。これらの機能を理解することで、効率的でパフォーマンスの高いアプリケーションを構築できるようになります。

---

## 目次

- [fetch関数の基本](#fetch関数の基本)
- [Requestのメモ化 (Request Memoization)](#requestのメモ化-request-memoization)
- [fetch関数のキャッシュ (Data Cache)](#fetch関数のキャッシュ-data-cache)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## fetch関数の基本

### fetch関数とは
`fetch`関数は、**HTTPリクエストを送信してデータを取得するための標準API**です。Next.jsのApp Routerでは、この`fetch`関数を拡張して、自動的なキャッシュやリクエストのメモ化などの機能を提供します。

### Next.jsでのfetch関数の特徴
- **自動キャッシュ**: デフォルトでリクエスト結果をキャッシュ
- **リクエストのメモ化**: 同じリクエストを自動的に重複排除
- **Server Componentでの使用**: サーバーサイドでのデータ取得に最適化

### 基本的なfetchの使用

#### 1. シンプルなGETリクエスト

```tsx
// app/posts/page.tsx
async function getPosts() {
  const response = await fetch('https://api.example.com/posts')
  const posts = await response.json()
  return posts
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```

#### 2. エラーハンドリング

```tsx
// app/posts/page.tsx
async function getPosts() {
  try {
    const response = await fetch('https://api.example.com/posts')
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`)
    }
    
    const posts = await response.json()
    return posts
  } catch (error) {
    console.error('Failed to fetch posts:', error)
    return []
  }
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      {posts.length === 0 ? (
        <p>投稿が見つかりませんでした</p>
      ) : (
        <ul>
          {posts.map((post: any) => (
            <li key={post.id}>{post.title}</li>
          ))}
        </ul>
      )}
    </div>
  )
}
```

#### 3. リクエストオプションの設定

```tsx
// app/posts/page.tsx
async function getPosts() {
  const response = await fetch('https://api.example.com/posts', {
    method: 'GET',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': 'Bearer your-token',
    },
    // キャッシュの設定（後述）
    cache: 'force-cache',
  })

  if (!response.ok) {
    throw new Error('Failed to fetch posts')
  }

  return response.json()
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```

### 実践例: 外部APIからのデータ取得

```tsx
// app/users/page.tsx
interface User {
  id: number
  name: string
  email: string
}

async function getUsers(): Promise<User[]> {
  const response = await fetch('https://jsonplaceholder.typicode.com/users', {
    next: { revalidate: 3600 }, // 1時間ごとに再検証
  })

  if (!response.ok) {
    throw new Error('Failed to fetch users')
  }

  return response.json()
}

export default async function UsersPage() {
  const users = await getUsers()

  return (
    <div>
      <h1>ユーザー一覧</h1>
      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>名前</th>
            <th>メール</th>
          </tr>
        </thead>
        <tbody>
          {users.map((user) => (
            <tr key={user.id}>
              <td>{user.id}</td>
              <td>{user.name}</td>
              <td>{user.email}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  )
}
```

### 実践例: 動的ルートでのデータ取得

```tsx
// app/posts/[id]/page.tsx
interface Post {
  id: number
  title: string
  body: string
  userId: number
}

async function getPost(id: string): Promise<Post> {
  const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`, {
    next: { revalidate: 60 }, // 60秒ごとに再検証
  })

  if (!response.ok) {
    throw new Error('Failed to fetch post')
  }

  return response.json()
}

export default async function PostPage({
  params,
}: {
  params: { id: string }
}) {
  const post = await getPost(params.id)

  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </div>
  )
}
```

### 実践例: 複数のデータソースからの取得

```tsx
// app/dashboard/page.tsx
async function getStats() {
  const [posts, users, comments] = await Promise.all([
    fetch('https://api.example.com/posts').then((res) => res.json()),
    fetch('https://api.example.com/users').then((res) => res.json()),
    fetch('https://api.example.com/comments').then((res) => res.json()),
  ])

  return {
    posts: posts.length,
    users: users.length,
    comments: comments.length,
  }
}

export default async function DashboardPage() {
  const stats = await getStats()

  return (
    <div>
      <h1>ダッシュボード</h1>
      <div className="stats">
        <div>
          <h2>投稿数</h2>
          <p>{stats.posts}</p>
        </div>
        <div>
          <h2>ユーザー数</h2>
          <p>{stats.users}</p>
        </div>
        <div>
          <h2>コメント数</h2>
          <p>{stats.comments}</p>
        </div>
      </div>
    </div>
  )
}
```

---

## Requestのメモ化 (Request Memoization)

### Request Memoizationとは
Request Memoizationは、**同じリクエストを自動的に重複排除する機能**です。同じURLとオプションを持つ`fetch`リクエストが、同じレンダリングパス内で複数回呼び出された場合、Next.jsは自動的に1回のリクエストにまとめます。

### Request Memoizationの特徴
- **自動的な重複排除**: 同じリクエストを自動的に1回にまとめる
- **レンダリングパス内での有効**: 同じレンダリングパス内でのみ有効
- **パフォーマンス向上**: 不要なネットワークリクエストを削減

### 実践例: Request Memoizationの動作

#### 1. 同じリクエストの重複排除

```tsx
// app/posts/page.tsx
async function getPost(id: number) {
  console.log(`Fetching post ${id}...`) // このログは1回だけ出力される
  const response = await fetch(`https://api.example.com/posts/${id}`)
  return response.json()
}

export default async function PostsPage() {
  // 同じリクエストが複数回呼び出されても、実際のリクエストは1回だけ
  const [post1, post2, post3] = await Promise.all([
    getPost(1),
    getPost(1), // 同じIDなので、リクエストは重複排除される
    getPost(1), // 同じIDなので、リクエストは重複排除される
  ])

  return (
    <div>
      <h1>投稿</h1>
      <p>{post1.title}</p>
      <p>{post2.title}</p>
      <p>{post3.title}</p>
    </div>
  )
}
```

#### 2. 異なるリクエストは別々に実行

```tsx
// app/posts/page.tsx
async function getPost(id: number) {
  const response = await fetch(`https://api.example.com/posts/${id}`)
  return response.json()
}

export default async function PostsPage() {
  // 異なるIDなので、それぞれ別のリクエストが実行される
  const [post1, post2, post3] = await Promise.all([
    getPost(1),
    getPost(2),
    getPost(3),
  ])

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        <li>{post1.title}</li>
        <li>{post2.title}</li>
        <li>{post3.title}</li>
      </ul>
    </div>
  )
}
```

### 実践例: レイアウトとページでのRequest Memoization

```tsx
// app/posts/layout.tsx
async function getPostCount() {
  const response = await fetch('https://api.example.com/posts', {
    next: { revalidate: 3600 },
  })
  const posts = await response.json()
  return posts.length
}

export default async function PostsLayout({
  children,
}: {
  children: React.ReactNode
}) {
  const count = await getPostCount()

  return (
    <div>
      <aside>
        <p>総投稿数: {count}</p>
      </aside>
      <main>{children}</main>
    </div>
  )
}
```

```tsx
// app/posts/page.tsx
async function getPosts() {
  // レイアウトと同じURLなので、リクエストは重複排除される
  const response = await fetch('https://api.example.com/posts', {
    next: { revalidate: 3600 },
  })
  return response.json()
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```

### 実践例: ヘルパー関数でのRequest Memoization

```tsx
// lib/api.ts
export async function fetchUser(id: number) {
  const response = await fetch(`https://api.example.com/users/${id}`, {
    next: { revalidate: 3600 },
  })
  return response.json()
}

export async function fetchUserPosts(userId: number) {
  const response = await fetch(`https://api.example.com/users/${userId}/posts`, {
    next: { revalidate: 3600 },
  })
  return response.json()
}
```

```tsx
// app/users/[id]/page.tsx
import { fetchUser, fetchUserPosts } from '@/lib/api'

export default async function UserPage({
  params,
}: {
  params: { id: string }
}) {
  const userId = parseInt(params.id)

  // 同じレンダリングパス内で同じ関数を呼び出しても、Request Memoizationにより
  // 実際のリクエストは1回だけ実行される
  const [user, posts] = await Promise.all([
    fetchUser(userId),
    fetchUserPosts(userId),
  ])

  return (
    <div>
      <h1>{user.name}</h1>
      <h2>投稿一覧</h2>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```

### Request Memoizationの制限事項

Request Memoizationは、**同じレンダリングパス内でのみ有効**です。以下の場合は適用されません：

- 異なるレンダリングパス（別のページリクエスト）
- 異なるリクエストオプション（URL、メソッド、ヘッダーなどが異なる場合）
- `cache: 'no-store'`が設定されている場合

---

## fetch関数のキャッシュ (Data Cache)

### Data Cacheとは
Data Cacheは、**fetchリクエストの結果をサーバー間で永続化するキャッシュ**です。Next.jsは、`fetch`関数の結果を自動的にキャッシュし、同じリクエストに対してキャッシュされた結果を返します。

### キャッシュの種類

Next.jsの`fetch`関数では、以下のキャッシュオプションを使用できます：

1. **`cache: 'force-cache'`** (デフォルト): リクエスト結果をキャッシュ
2. **`cache: 'no-store'`**: キャッシュを無効化
3. **`next: { revalidate: seconds }`**: 指定した秒数ごとに再検証
4. **`next: { tags: ['tag-name'] }`**: タグベースのキャッシュ制御

### 1. force-cache（デフォルト）

```tsx
// app/posts/page.tsx
async function getPosts() {
  // cache: 'force-cache' がデフォルト
  const response = await fetch('https://api.example.com/posts')
  return response.json()
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```

### 2. no-store（キャッシュ無効化）

```tsx
// app/posts/page.tsx
async function getPosts() {
  // キャッシュを無効化（常に最新のデータを取得）
  const response = await fetch('https://api.example.com/posts', {
    cache: 'no-store',
  })
  return response.json()
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```

### 3. revalidate（時間ベースの再検証）

```tsx
// app/posts/page.tsx
async function getPosts() {
  // 60秒ごとに再検証（ISR: Incremental Static Regeneration）
  const response = await fetch('https://api.example.com/posts', {
    next: { revalidate: 60 },
  })
  return response.json()
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```

### 4. tags（タグベースのキャッシュ制御）

```tsx
// app/posts/page.tsx
async function getPosts() {
  // タグベースのキャッシュ制御
  const response = await fetch('https://api.example.com/posts', {
    next: { tags: ['posts'] },
  })
  return response.json()
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```

### 実践例: キャッシュ戦略の使い分け

```tsx
// lib/api.ts
// 静的データ（変更が少ない）
export async function getStaticData() {
  const response = await fetch('https://api.example.com/static-data', {
    cache: 'force-cache', // 永続的にキャッシュ
  })
  return response.json()
}

// 動的データ（頻繁に更新される）
export async function getDynamicData() {
  const response = await fetch('https://api.example.com/dynamic-data', {
    cache: 'no-store', // キャッシュしない
  })
  return response.json()
}

// 時間ベースの再検証（定期的に更新）
export async function getTimeBasedData() {
  const response = await fetch('https://api.example.com/time-based-data', {
    next: { revalidate: 3600 }, // 1時間ごとに再検証
  })
  return response.json()
}

// タグベースのキャッシュ制御（手動で無効化可能）
export async function getTaggedData() {
  const response = await fetch('https://api.example.com/tagged-data', {
    next: { tags: ['data'] },
  })
  return response.json()
}
```

### 実践例: キャッシュの無効化（revalidateTag）

```tsx
// app/api/posts/route.ts
import { revalidateTag } from 'next/cache'

export async function POST(request: Request) {
  // データを更新
  const body = await request.json()
  // ... データ更新処理 ...

  // タグ 'posts' に関連するキャッシュを無効化
  revalidateTag('posts')

  return Response.json({ success: true })
}
```

```tsx
// app/posts/page.tsx
async function getPosts() {
  const response = await fetch('https://api.example.com/posts', {
    next: { tags: ['posts'] },
  })
  return response.json()
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```

### 実践例: 複数のキャッシュ戦略の組み合わせ

```tsx
// app/dashboard/page.tsx
async function getDashboardData() {
  // 複数のデータソースから異なるキャッシュ戦略で取得
  const [staticData, dynamicData, timeBasedData] = await Promise.all([
    // 静的データ（永続的にキャッシュ）
    fetch('https://api.example.com/static', {
      cache: 'force-cache',
    }).then((res) => res.json()),

    // 動的データ（キャッシュしない）
    fetch('https://api.example.com/dynamic', {
      cache: 'no-store',
    }).then((res) => res.json()),

    // 時間ベースの再検証（1時間ごと）
    fetch('https://api.example.com/time-based', {
      next: { revalidate: 3600 },
    }).then((res) => res.json()),
  ])

  return {
    static: staticData,
    dynamic: dynamicData,
    timeBased: timeBasedData,
  }
}

export default async function DashboardPage() {
  const data = await getDashboardData()

  return (
    <div>
      <h1>ダッシュボード</h1>
      <div>
        <h2>静的データ</h2>
        <pre>{JSON.stringify(data.static, null, 2)}</pre>
      </div>
      <div>
        <h2>動的データ</h2>
        <pre>{JSON.stringify(data.dynamic, null, 2)}</pre>
      </div>
      <div>
        <h2>時間ベースデータ</h2>
        <pre>{JSON.stringify(data.timeBased, null, 2)}</pre>
      </div>
    </div>
  )
}
```

### キャッシュの動作確認

開発環境でキャッシュの動作を確認するには、以下のようにログを追加します：

```tsx
// app/posts/page.tsx
async function getPosts() {
  const timestamp = new Date().toISOString()
  console.log(`[${timestamp}] Fetching posts...`)

  const response = await fetch('https://api.example.com/posts', {
    next: { revalidate: 60 },
  })
  return response.json()
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```

---

## まとめ

この章では、Next.jsのApp Routerにおける`fetch`関数を使用したデータ取得について学びました。

### 学んだこと
- fetch関数の基本: Server Componentでのデータ取得方法
- Requestのメモ化: 同じリクエストの自動的な重複排除
- fetch関数のキャッシュ: データキャッシュの種類と使い分け

### 重要なポイント
1. **fetch関数**: Next.jsで拡張された標準のfetch API
2. **Request Memoization**: 同じレンダリングパス内でのリクエストの重複排除
3. **Data Cache**: サーバー間でのデータ永続化
4. **キャッシュ戦略**: `force-cache`、`no-store`、`revalidate`、`tags`の使い分け
5. **パフォーマンス**: 適切なキャッシュ戦略により、パフォーマンスを向上

### 次のステップ
次の章では、Prisma Clientを使用したデータ取得について詳しく学びます。

---

## 演習問題

### 問題1: 基本的なfetchの使用
外部APIからデータを取得し、Server Componentで表示してください。

<details>
<summary>解答例</summary>

```tsx
// app/users/page.tsx
interface User {
  id: number
  name: string
  email: string
}

async function getUsers(): Promise<User[]> {
  const response = await fetch('https://jsonplaceholder.typicode.com/users')

  if (!response.ok) {
    throw new Error('Failed to fetch users')
  }

  return response.json()
}

export default async function UsersPage() {
  const users = await getUsers()

  return (
    <div>
      <h1>ユーザー一覧</h1>
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

### 問題2: Request Memoizationの確認
同じリクエストを複数回呼び出し、Request Memoizationが動作することを確認してください。

<details>
<summary>解答例</summary>

```tsx
// app/posts/[id]/page.tsx
async function getPost(id: number) {
  console.log(`Fetching post ${id}...`)
  const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`)
  return response.json()
}

export default async function PostPage({
  params,
}: {
  params: { id: string }
}) {
  const postId = parseInt(params.id)

  // 同じリクエストを3回呼び出すが、実際のリクエストは1回だけ
  const [post1, post2, post3] = await Promise.all([
    getPost(postId),
    getPost(postId),
    getPost(postId),
  ])

  return (
    <div>
      <h1>{post1.title}</h1>
      <p>{post1.body}</p>
    </div>
  )
}
```

コンソールには「Fetching post X...」が1回だけ出力されます。
</details>

### 問題3: キャッシュ戦略の実装
時間ベースの再検証（`revalidate`）を使用して、データを定期的に更新してください。

<details>
<summary>解答例</summary>

```tsx
// app/posts/page.tsx
async function getPosts() {
  const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
    next: { revalidate: 60 }, // 60秒ごとに再検証
  })

  if (!response.ok) {
    throw new Error('Failed to fetch posts')
  }

  return response.json()
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  )
}
```
</details>

### 問題4: タグベースのキャッシュ制御
タグベースのキャッシュを使用し、Server Actionでキャッシュを無効化してください。

<details>
<summary>解答例</summary>

```tsx
// app/posts/page.tsx
import { revalidateTag } from 'next/cache'

async function getPosts() {
  const response = await fetch('https://api.example.com/posts', {
    next: { tags: ['posts'] },
  })
  return response.json()
}

async function updatePosts() {
  'use server'
  
  // データ更新処理...
  
  // キャッシュを無効化
  revalidateTag('posts')
}

export default async function PostsPage() {
  const posts = await getPosts()

  return (
    <div>
      <h1>投稿一覧</h1>
      <ul>
        {posts.map((post: any) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
      <form action={updatePosts}>
        <button type="submit">投稿を更新</button>
      </form>
    </div>
  )
}
```
</details>

### 問題5: 複数のデータソースからの取得
`Promise.all`を使用して、複数のデータソースから並列にデータを取得してください。

<details>
<summary>解答例</summary>

```tsx
// app/dashboard/page.tsx
async function getDashboardData() {
  const [posts, users, comments] = await Promise.all([
    fetch('https://jsonplaceholder.typicode.com/posts').then((res) => res.json()),
    fetch('https://jsonplaceholder.typicode.com/users').then((res) => res.json()),
    fetch('https://jsonplaceholder.typicode.com/comments').then((res) => res.json()),
  ])

  return {
    posts: posts.length,
    users: users.length,
    comments: comments.length,
  }
}

export default async function DashboardPage() {
  const stats = await getDashboardData()

  return (
    <div>
      <h1>ダッシュボード</h1>
      <div>
        <h2>統計</h2>
        <p>投稿数: {stats.posts}</p>
        <p>ユーザー数: {stats.users}</p>
        <p>コメント数: {stats.comments}</p>
      </div>
    </div>
  )
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

