# 5-1. Route Handlerの基本

この章では、Next.jsのApp RouterにおけるRoute Handler（API Routes）について学びます。Route Handlerを使用することで、サーバーサイドでAPIエンドポイントを作成し、HTTPリクエストを処理できるようになります。これにより、フルスタックアプリケーションを構築できます。

---

## 目次

- [Route Handlerの定義 (`route.ts`)](#route-handlerの定義-routets)
- [GET, POST, PUT, DELETE の実装](#get-post-put-delete-の実装)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## Route Handlerの定義 (`route.ts`)

### Route Handlerとは
Route Handlerは、**Next.jsのApp RouterでAPIエンドポイントを作成するための機能**です。`route.ts`（または`route.js`）ファイルを使用して、HTTPメソッド（GET、POST、PUT、DELETEなど）に対応するハンドラー関数を定義します。

### Route Handlerの特徴
- **ファイルベースルーティング**: `route.ts`ファイルの配置でルートが決定
- **HTTPメソッド対応**: GET、POST、PUT、DELETEなどのメソッドに対応
- **Server Component**: サーバーサイドでのみ実行される
- **型安全性**: TypeScriptで型安全に実装可能

### 基本的なRoute Handlerの構造

#### 1. ファイル構造

```
app/
└── api/
    └── hello/
        └── route.ts    # /api/hello エンドポイント
```

#### 2. 基本的なGETハンドラー

```tsx
// app/api/hello/route.ts
import { NextResponse } from 'next/server'

export async function GET() {
  return NextResponse.json({ message: 'Hello, Next.js!' })
}
```

このRoute Handlerは、`GET /api/hello`リクエストに対して`{ message: 'Hello, Next.js!' }`を返します。

### 実践例: 基本的なRoute Handler

```tsx
// app/api/hello/route.ts
import { NextResponse } from 'next/server'

export async function GET() {
  return NextResponse.json({
    message: 'Hello, Next.js!',
    timestamp: new Date().toISOString(),
  })
}
```

#### クライアントからの呼び出し

```tsx
// app/page.tsx
async function fetchHello() {
  const response = await fetch('http://localhost:3000/api/hello')
  const data = await response.json()
  return data
}

export default async function Home() {
  const data = await fetchHello()

  return (
    <div>
      <h1>{data.message}</h1>
      <p>タイムスタンプ: {data.timestamp}</p>
    </div>
  )
}
```

### 実践例: 動的ルートでのRoute Handler

```tsx
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id

  // データベースから投稿を取得（例）
  const post = {
    id: id,
    title: `投稿 ${id}`,
    content: 'これは投稿の内容です',
  }

  return NextResponse.json(post)
}
```

このRoute Handlerは、`GET /api/posts/1`のようなリクエストに対して、IDに応じた投稿データを返します。

### 実践例: クエリパラメータの取得

```tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url)
  const page = searchParams.get('page') || '1'
  const limit = searchParams.get('limit') || '10'

  // クエリパラメータを使用してデータを取得
  const posts = [
    { id: 1, title: '投稿1' },
    { id: 2, title: '投稿2' },
  ]

  return NextResponse.json({
    posts,
    page: parseInt(page),
    limit: parseInt(limit),
  })
}
```

このRoute Handlerは、`GET /api/posts?page=1&limit=10`のようなリクエストを処理します。

### 実践例: エラーハンドリング

```tsx
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  try {
    const id = params.id

    // データベースから投稿を取得（例）
    if (id === '999') {
      throw new Error('投稿が見つかりません')
    }

    const post = {
      id: id,
      title: `投稿 ${id}`,
      content: 'これは投稿の内容です',
    }

    return NextResponse.json(post)
  } catch (error) {
    return NextResponse.json(
      { error: '投稿の取得に失敗しました' },
      { status: 404 }
    )
  }
}
```

### 実践例: ヘッダーの設定

```tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

export async function GET() {
  const posts = [
    { id: 1, title: '投稿1' },
    { id: 2, title: '投稿2' },
  ]

  return NextResponse.json(posts, {
    headers: {
      'Cache-Control': 'public, s-maxage=60, stale-while-revalidate=300',
      'Content-Type': 'application/json',
    },
  })
}
```

---

## GET, POST, PUT, DELETE の実装

### HTTPメソッドの対応

Route Handlerでは、以下のHTTPメソッドに対応する関数をエクスポートできます：

- **GET**: データの取得
- **POST**: データの作成
- **PUT**: データの更新（全体）
- **PATCH**: データの更新（部分）
- **DELETE**: データの削除
- **HEAD**: ヘッダーのみ取得
- **OPTIONS**: CORSプリフライトリクエスト

### 1. GETメソッドの実装

GETメソッドは、**データを取得するために使用**します。

```tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

export async function GET() {
  // データベースから投稿を取得（例）
  const posts = [
    { id: 1, title: '投稿1', content: '内容1' },
    { id: 2, title: '投稿2', content: '内容2' },
  ]

  return NextResponse.json(posts)
}
```

#### 実践例: 動的パラメータを使用したGET

```tsx
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id

  // データベースから特定の投稿を取得
  const post = {
    id: id,
    title: `投稿 ${id}`,
    content: 'これは投稿の内容です',
  }

  return NextResponse.json(post)
}
```

### 2. POSTメソッドの実装

POSTメソッドは、**新しいデータを作成するために使用**します。

```tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  try {
    const body = await request.json()
    const { title, content } = body

    // バリデーション
    if (!title || !content) {
      return NextResponse.json(
        { error: 'タイトルと内容は必須です' },
        { status: 400 }
      )
    }

    // データベースに投稿を保存（例）
    const newPost = {
      id: Date.now(),
      title,
      content,
      createdAt: new Date().toISOString(),
    }

    return NextResponse.json(newPost, { status: 201 })
  } catch (error) {
    return NextResponse.json(
      { error: '投稿の作成に失敗しました' },
      { status: 500 }
    )
  }
}
```

#### 実践例: フォームデータの処理

```tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  try {
    const formData = await request.formData()
    const title = formData.get('title') as string
    const content = formData.get('content') as string
    const file = formData.get('file') as File | null

    // バリデーション
    if (!title || !content) {
      return NextResponse.json(
        { error: 'タイトルと内容は必須です' },
        { status: 400 }
      )
    }

    // ファイルの処理（例）
    if (file) {
      const bytes = await file.arrayBuffer()
      const buffer = Buffer.from(bytes)
      // ファイルを保存する処理...
    }

    const newPost = {
      id: Date.now(),
      title,
      content,
      createdAt: new Date().toISOString(),
    }

    return NextResponse.json(newPost, { status: 201 })
  } catch (error) {
    return NextResponse.json(
      { error: '投稿の作成に失敗しました' },
      { status: 500 }
    )
  }
}
```

### 3. PUTメソッドの実装

PUTメソッドは、**データ全体を更新するために使用**します。

```tsx
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

export async function PUT(
  request: Request,
  { params }: { params: { id: string } }
) {
  try {
    const id = params.id
    const body = await request.json()
    const { title, content } = body

    // バリデーション
    if (!title || !content) {
      return NextResponse.json(
        { error: 'タイトルと内容は必須です' },
        { status: 400 }
      )
    }

    // データベースで投稿を更新（例）
    const updatedPost = {
      id: id,
      title,
      content,
      updatedAt: new Date().toISOString(),
    }

    return NextResponse.json(updatedPost)
  } catch (error) {
    return NextResponse.json(
      { error: '投稿の更新に失敗しました' },
      { status: 500 }
    )
  }
}
```

### 4. PATCHメソッドの実装

PATCHメソッドは、**データの一部を更新するために使用**します。

```tsx
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

export async function PATCH(
  request: Request,
  { params }: { params: { id: string } }
) {
  try {
    const id = params.id
    const body = await request.json()

    // 部分的な更新（送信されたフィールドのみ更新）
    const updatedPost = {
      id: id,
      ...body,
      updatedAt: new Date().toISOString(),
    }

    return NextResponse.json(updatedPost)
  } catch (error) {
    return NextResponse.json(
      { error: '投稿の更新に失敗しました' },
      { status: 500 }
    )
  }
}
```

### 5. DELETEメソッドの実装

DELETEメソッドは、**データを削除するために使用**します。

```tsx
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

export async function DELETE(
  request: Request,
  { params }: { params: { id: string } }
) {
  try {
    const id = params.id

    // データベースから投稿を削除（例）
    // await deletePost(id)

    return NextResponse.json(
      { message: `投稿 ${id} を削除しました` },
      { status: 200 }
    )
  } catch (error) {
    return NextResponse.json(
      { error: '投稿の削除に失敗しました' },
      { status: 500 }
    )
  }
}
```

### 実践例: 複数のメソッドを実装

```tsx
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

// GET: 投稿の取得
export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id

  const post = {
    id: id,
    title: `投稿 ${id}`,
    content: 'これは投稿の内容です',
  }

  return NextResponse.json(post)
}

// PUT: 投稿の更新（全体）
export async function PUT(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id
  const body = await request.json()
  const { title, content } = body

  if (!title || !content) {
    return NextResponse.json(
      { error: 'タイトルと内容は必須です' },
      { status: 400 }
    )
  }

  const updatedPost = {
    id: id,
    title,
    content,
    updatedAt: new Date().toISOString(),
  }

  return NextResponse.json(updatedPost)
}

// DELETE: 投稿の削除
export async function DELETE(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id

  return NextResponse.json(
    { message: `投稿 ${id} を削除しました` },
    { status: 200 }
  )
}
```

### 実践例: CRUD操作の完全な実装

```tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

// GET: 投稿一覧の取得
export async function GET(request: Request) {
  const { searchParams } = new URL(request.url)
  const page = searchParams.get('page') || '1'
  const limit = searchParams.get('limit') || '10'

  const posts = [
    { id: 1, title: '投稿1', content: '内容1' },
    { id: 2, title: '投稿2', content: '内容2' },
  ]

  return NextResponse.json({
    posts,
    page: parseInt(page),
    limit: parseInt(limit),
    total: posts.length,
  })
}

// POST: 新規投稿の作成
export async function POST(request: Request) {
  try {
    const body = await request.json()
    const { title, content } = body

    if (!title || !content) {
      return NextResponse.json(
        { error: 'タイトルと内容は必須です' },
        { status: 400 }
      )
    }

    const newPost = {
      id: Date.now(),
      title,
      content,
      createdAt: new Date().toISOString(),
    }

    return NextResponse.json(newPost, { status: 201 })
  } catch (error) {
    return NextResponse.json(
      { error: '投稿の作成に失敗しました' },
      { status: 500 }
    )
  }
}
```

```tsx
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

// GET: 特定の投稿の取得
export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id

  const post = {
    id: id,
    title: `投稿 ${id}`,
    content: 'これは投稿の内容です',
  }

  return NextResponse.json(post)
}

// PUT: 投稿の更新（全体）
export async function PUT(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id
  const body = await request.json()
  const { title, content } = body

  if (!title || !content) {
    return NextResponse.json(
      { error: 'タイトルと内容は必須です' },
      { status: 400 }
    )
  }

  const updatedPost = {
    id: id,
    title,
    content,
    updatedAt: new Date().toISOString(),
  }

  return NextResponse.json(updatedPost)
}

// PATCH: 投稿の更新（部分）
export async function PATCH(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id
  const body = await request.json()

  const updatedPost = {
    id: id,
    ...body,
    updatedAt: new Date().toISOString(),
  }

  return NextResponse.json(updatedPost)
}

// DELETE: 投稿の削除
export async function DELETE(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id

  return NextResponse.json(
    { message: `投稿 ${id} を削除しました` },
    { status: 200 }
  )
}
```

### 実践例: クライアントからの呼び出し

```tsx
// app/posts/page.tsx
'use client'

import { useState, useEffect } from 'react'

interface Post {
  id: number
  title: string
  content: string
}

export default function PostsPage() {
  const [posts, setPosts] = useState<Post[]>([])
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    fetch('/api/posts')
      .then((res) => res.json())
      .then((data) => {
        setPosts(data.posts)
        setLoading(false)
      })
  }, [])

  const handleCreate = async () => {
    const response = await fetch('/api/posts', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        title: '新しい投稿',
        content: 'これは新しい投稿の内容です',
      }),
    })

    const newPost = await response.json()
    setPosts([...posts, newPost])
  }

  if (loading) return <div>読み込み中...</div>

  return (
    <div>
      <h1>投稿一覧</h1>
      <button onClick={handleCreate}>新規投稿</button>
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

### 実践例: エラーハンドリングとバリデーション

```tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  try {
    const body = await request.json()
    const { title, content } = body

    // バリデーション
    if (!title || typeof title !== 'string' || title.trim().length === 0) {
      return NextResponse.json(
        { error: 'タイトルは必須です' },
        { status: 400 }
      )
    }

    if (!content || typeof content !== 'string' || content.trim().length === 0) {
      return NextResponse.json(
        { error: '内容は必須です' },
        { status: 400 }
      )
    }

    if (title.length > 100) {
      return NextResponse.json(
        { error: 'タイトルは100文字以内で入力してください' },
        { status: 400 }
      )
    }

    // データベースに投稿を保存
    const newPost = {
      id: Date.now(),
      title: title.trim(),
      content: content.trim(),
      createdAt: new Date().toISOString(),
    }

    return NextResponse.json(newPost, { status: 201 })
  } catch (error) {
    console.error('Error creating post:', error)
    return NextResponse.json(
      { error: '投稿の作成に失敗しました' },
      { status: 500 }
    )
  }
}
```

---

## まとめ

この章では、Next.jsのApp RouterにおけるRoute Handlerの基本について学びました。

### 学んだこと
- Route Handlerの定義: `route.ts`ファイルを使用したAPIエンドポイントの作成
- GET, POST, PUT, DELETEの実装: 各HTTPメソッドに対応するハンドラー関数の実装

### 重要なポイント
1. **Route Handler**: `route.ts`ファイルでAPIエンドポイントを定義
2. **HTTPメソッド**: GET、POST、PUT、PATCH、DELETEなどのメソッドに対応
3. **動的ルート**: `[id]`のような動的セグメントを使用可能
4. **リクエスト処理**: Requestオブジェクトからデータを取得
5. **レスポンス**: NextResponseを使用してJSONやその他の形式でレスポンスを返す
6. **エラーハンドリング**: 適切なエラーハンドリングとバリデーションの実装

### 次のステップ
次の章では、Route Handlerのレンダリングと挙動について詳しく学びます。

---

## 演習問題

### 問題1: 基本的なGETハンドラーの作成
`/api/hello`エンドポイントで、`{ message: 'Hello, Next.js!' }`を返すGETハンドラーを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/api/hello/route.ts
import { NextResponse } from 'next/server'

export async function GET() {
  return NextResponse.json({ message: 'Hello, Next.js!' })
}
```
</details>

### 問題2: POSTハンドラーの作成
`/api/posts`エンドポイントで、新規投稿を作成するPOSTハンドラーを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

export async function POST(request: Request) {
  try {
    const body = await request.json()
    const { title, content } = body

    if (!title || !content) {
      return NextResponse.json(
        { error: 'タイトルと内容は必須です' },
        { status: 400 }
      )
    }

    const newPost = {
      id: Date.now(),
      title,
      content,
      createdAt: new Date().toISOString(),
    }

    return NextResponse.json(newPost, { status: 201 })
  } catch (error) {
    return NextResponse.json(
      { error: '投稿の作成に失敗しました' },
      { status: 500 }
    )
  }
}
```
</details>

### 問題3: 動的ルートでのGETハンドラー
`/api/posts/[id]`エンドポイントで、特定の投稿を取得するGETハンドラーを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

export async function GET(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id

  const post = {
    id: id,
    title: `投稿 ${id}`,
    content: 'これは投稿の内容です',
  }

  return NextResponse.json(post)
}
```
</details>

### 問題4: PUTとDELETEハンドラーの実装
`/api/posts/[id]`エンドポイントで、投稿を更新するPUTハンドラーと削除するDELETEハンドラーを作成してください。

<details>
<summary>解答例</summary>

```tsx
// app/api/posts/[id]/route.ts
import { NextResponse } from 'next/server'

export async function PUT(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id
  const body = await request.json()
  const { title, content } = body

  if (!title || !content) {
    return NextResponse.json(
      { error: 'タイトルと内容は必須です' },
      { status: 400 }
    )
  }

  const updatedPost = {
    id: id,
    title,
    content,
    updatedAt: new Date().toISOString(),
  }

  return NextResponse.json(updatedPost)
}

export async function DELETE(
  request: Request,
  { params }: { params: { id: string } }
) {
  const id = params.id

  return NextResponse.json(
    { message: `投稿 ${id} を削除しました` },
    { status: 200 }
  )
}
```
</details>

### 問題5: クエリパラメータの処理
`/api/posts`エンドポイントで、`page`と`limit`クエリパラメータを受け取り、ページネーションを実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/api/posts/route.ts
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  const { searchParams } = new URL(request.url)
  const page = parseInt(searchParams.get('page') || '1')
  const limit = parseInt(searchParams.get('limit') || '10')

  const posts = [
    { id: 1, title: '投稿1' },
    { id: 2, title: '投稿2' },
    // ... より多くの投稿
  ]

  const startIndex = (page - 1) * limit
  const endIndex = startIndex + limit
  const paginatedPosts = posts.slice(startIndex, endIndex)

  return NextResponse.json({
    posts: paginatedPosts,
    page,
    limit,
    total: posts.length,
    totalPages: Math.ceil(posts.length / limit),
  })
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

