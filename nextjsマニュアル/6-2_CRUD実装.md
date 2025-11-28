# 6-2. CRUD実装

この章では、Server Actionを使用したCRUD（Create, Read, Update, Delete）操作の実装について学びます。データの作成、読み取り、更新、削除を実装することで、完全なデータ管理機能を構築できます。

---

## 目次

- [CRUDとは](#crudとは)
- [Server Actionによるデータ保存 (Create/Update)](#server-actionによるデータ保存-createupdate)
- [Server Actionによるデータ削除 (Delete)](#server-actionによるデータ削除-delete)
- [バックエンド間連携によるデータ保存・削除](#バックエンド間連携によるデータ保存削除)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## CRUDとは

### CRUDとは
CRUDは、**データベース操作の基本となる4つの操作**を表します：
- **Create（作成）**: 新しいデータを作成
- **Read（読み取り）**: 既存のデータを取得
- **Update（更新）**: 既存のデータを更新
- **Delete（削除）**: 既存のデータを削除

### CRUD操作の重要性
CRUD操作は、**あらゆるWebアプリケーションの基礎**です。ユーザー管理、投稿管理、商品管理など、すべての機能はCRUD操作に基づいています。

---

## Server Actionによるデータ保存 (Create/Update)

### Create（作成）の実装

#### 基本的なCreate操作

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function createPost(formData: FormData) {
  const title = formData.get('title') as string
  const content = formData.get('content') as string
  const authorId = parseInt(formData.get('authorId') as string)

  if (!title || !content || !authorId) {
    return {
      error: 'Title, content, and author are required',
    }
  }

  try {
    const post = await prisma.post.create({
      data: {
        title,
        content,
        authorId,
      },
    })

    revalidatePath('/posts')
    redirect(`/posts/${post.id}`)
  } catch (error) {
    return {
      error: 'Failed to create post',
    }
  }
}
```

#### 実践例: バリデーション付きCreate操作

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function createPost(
  prevState: any,
  formData: FormData
): Promise<{ error?: string; success?: boolean }> {
  const title = formData.get('title') as string
  const content = formData.get('content') as string
  const authorId = parseInt(formData.get('authorId') as string)

  // バリデーション
  if (!title || title.trim().length === 0) {
    return {
      error: 'Title is required',
    }
  }

  if (title.length > 100) {
    return {
      error: 'Title must be less than 100 characters',
    }
  }

  if (!content || content.trim().length === 0) {
    return {
      error: 'Content is required',
    }
  }

  if (content.length > 5000) {
    return {
      error: 'Content must be less than 5000 characters',
    }
  }

  if (!authorId || isNaN(authorId)) {
    return {
      error: 'Author is required',
    }
  }

  try {
    const post = await prisma.post.create({
      data: {
        title: title.trim(),
        content: content.trim(),
        authorId,
      },
    })

    revalidatePath('/posts')
    redirect(`/posts/${post.id}`)
  } catch (error: any) {
    if (error.code === 'P2002') {
      return {
        error: 'Post with this title already exists',
      }
    }

    return {
      error: 'Failed to create post',
    }
  }
}
```

#### 実践例: Create操作のフォーム

```typescript
// src/app/posts/new/page.tsx
'use client'

import { useActionState } from 'react'
import { createPost } from '@/app/actions/post-actions'
import { useRouter } from 'next/navigation'

export default function NewPostPage() {
  const router = useRouter()
  const [state, formAction] = useActionState(createPost, null)

  return (
    <div className="container mx-auto px-4 py-8">
      <h1 className="text-3xl font-bold mb-6">Create Post</h1>
      <form action={formAction} className="max-w-2xl">
        {state?.error && (
          <div className="mb-4 p-4 bg-red-50 border border-red-200 rounded-md">
            <p className="text-red-800">{state.error}</p>
          </div>
        )}
        <div className="mb-4">
          <label htmlFor="title" className="block text-sm font-medium text-gray-700">
            Title
          </label>
          <input
            type="text"
            id="title"
            name="title"
            required
            maxLength={100}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
          />
        </div>
        <div className="mb-4">
          <label htmlFor="content" className="block text-sm font-medium text-gray-700">
            Content
          </label>
          <textarea
            id="content"
            name="content"
            required
            rows={10}
            maxLength={5000}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
          />
        </div>
        <div className="mb-4">
          <label htmlFor="authorId" className="block text-sm font-medium text-gray-700">
            Author ID
          </label>
          <input
            type="number"
            id="authorId"
            name="authorId"
            required
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
          />
        </div>
        <button
          type="submit"
          className="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700"
        >
          Create Post
        </button>
      </form>
    </div>
  )
}
```

### Update（更新）の実装

#### 基本的なUpdate操作

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function updatePost(formData: FormData) {
  const id = parseInt(formData.get('id') as string)
  const title = formData.get('title') as string
  const content = formData.get('content') as string

  if (!id || !title || !content) {
    return {
      error: 'ID, title, and content are required',
    }
  }

  try {
    const post = await prisma.post.update({
      where: { id },
      data: {
        title,
        content,
      },
    })

    revalidatePath('/posts')
    revalidatePath(`/posts/${post.id}`)
    redirect(`/posts/${post.id}`)
  } catch (error: any) {
    if (error.code === 'P2025') {
      return {
        error: 'Post not found',
      }
    }

    return {
      error: 'Failed to update post',
    }
  }
}
```

#### 実践例: 部分更新（Partial Update）

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import prisma from '@/lib/prisma'

export async function updatePost(
  id: number,
  data: {
    title?: string
    content?: string
    published?: boolean
  }
) {
  try {
    const post = await prisma.post.update({
      where: { id },
      data: {
        ...(data.title && { title: data.title }),
        ...(data.content && { content: data.content }),
        ...(data.published !== undefined && { published: data.published }),
      },
    })

    revalidatePath('/posts')
    revalidatePath(`/posts/${post.id}`)

    return {
      success: true,
      post,
    }
  } catch (error: any) {
    if (error.code === 'P2025') {
      return {
        success: false,
        error: 'Post not found',
      }
    }

    return {
      success: false,
      error: 'Failed to update post',
    }
  }
}
```

#### 実践例: Update操作のフォーム

```typescript
// src/app/posts/[id]/edit/page.tsx
import prisma from '@/lib/prisma'
import { notFound } from 'next/navigation'
import { updatePost } from '@/app/actions/post-actions'

interface EditPostPageProps {
  params: {
    id: string
  }
}

export default async function EditPostPage({ params }: EditPostPageProps) {
  const post = await prisma.post.findUnique({
    where: {
      id: parseInt(params.id),
    },
  })

  if (!post) {
    notFound()
  }

  return (
    <div className="container mx-auto px-4 py-8">
      <h1 className="text-3xl font-bold mb-6">Edit Post</h1>
      <form action={updatePost} className="max-w-2xl">
        <input type="hidden" name="id" value={post.id} />
        <div className="mb-4">
          <label htmlFor="title" className="block text-sm font-medium text-gray-700">
            Title
          </label>
          <input
            type="text"
            id="title"
            name="title"
            defaultValue={post.title}
            required
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
          />
        </div>
        <div className="mb-4">
          <label htmlFor="content" className="block text-sm font-medium text-gray-700">
            Content
          </label>
          <textarea
            id="content"
            name="content"
            defaultValue={post.content}
            required
            rows={10}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
          />
        </div>
        <button
          type="submit"
          className="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700"
        >
          Update Post
        </button>
      </form>
    </div>
  )
}
```

### 実践例: CreateとUpdateの統合

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function savePost(formData: FormData) {
  const id = formData.get('id') as string | null
  const title = formData.get('title') as string
  const content = formData.get('content') as string
  const authorId = parseInt(formData.get('authorId') as string)

  // バリデーション
  if (!title || !content || !authorId) {
    return {
      error: 'Title, content, and author are required',
    }
  }

  try {
    let post

    if (id) {
      // Update
      post = await prisma.post.update({
        where: { id: parseInt(id) },
        data: {
          title,
          content,
        },
      })
    } else {
      // Create
      post = await prisma.post.create({
        data: {
          title,
          content,
          authorId,
        },
      })
    }

    revalidatePath('/posts')
    revalidatePath(`/posts/${post.id}`)
    redirect(`/posts/${post.id}`)
  } catch (error: any) {
    if (error.code === 'P2025') {
      return {
        error: 'Post not found',
      }
    }

    return {
      error: `Failed to ${id ? 'update' : 'create'} post`,
    }
  }
}
```

---

## Server Actionによるデータ削除 (Delete)

### Delete（削除）の実装

#### 基本的なDelete操作

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function deletePost(formData: FormData) {
  const id = parseInt(formData.get('id') as string)

  if (!id) {
    return {
      error: 'ID is required',
    }
  }

  try {
    await prisma.post.delete({
      where: { id },
    })

    revalidatePath('/posts')
    redirect('/posts')
  } catch (error: any) {
    if (error.code === 'P2025') {
      return {
        error: 'Post not found',
      }
    }

    return {
      error: 'Failed to delete post',
    }
  }
}
```

#### 実践例: 確認付きDelete操作

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function deletePost(formData: FormData) {
  const id = parseInt(formData.get('id') as string)
  const confirmed = formData.get('confirmed') === 'true'

  if (!id) {
    return {
      error: 'ID is required',
    }
  }

  if (!confirmed) {
    return {
      error: 'Please confirm deletion',
    }
  }

  try {
    await prisma.post.delete({
      where: { id },
    })

    revalidatePath('/posts')
    redirect('/posts')
  } catch (error: any) {
    if (error.code === 'P2025') {
      return {
        error: 'Post not found',
      }
    }

    return {
      error: 'Failed to delete post',
    }
  }
}
```

#### 実践例: Delete操作のUI

```typescript
// src/app/posts/[id]/page.tsx
import prisma from '@/lib/prisma'
import { notFound } from 'next/navigation'
import { deletePost } from '@/app/actions/post-actions'
import Link from 'next/link'

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
          name: true,
        },
      },
    },
  })

  if (!post) {
    notFound()
  }

  return (
    <div className="container mx-auto px-4 py-8">
      <div className="mb-4">
        <Link href="/posts" className="text-indigo-600 hover:text-indigo-800">
          ← Back to Posts
        </Link>
      </div>
      <h1 className="text-3xl font-bold mb-4">{post.title}</h1>
      <p className="text-gray-600 mb-4">By {post.author.name}</p>
      <div className="prose mb-8">
        <p>{post.content}</p>
      </div>
      <div className="flex gap-4">
        <Link
          href={`/posts/${post.id}/edit`}
          className="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700"
        >
          Edit
        </Link>
        <form action={deletePost}>
          <input type="hidden" name="id" value={post.id} />
          <input type="hidden" name="confirmed" value="true" />
          <button
            type="submit"
            className="bg-red-600 text-white px-4 py-2 rounded-md hover:bg-red-700"
            onClick={(e) => {
              if (!confirm('Are you sure you want to delete this post?')) {
                e.preventDefault()
              }
            }}
          >
            Delete
          </button>
        </form>
      </div>
    </div>
  )
}
```

#### 実践例: 複数レコードの削除

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import prisma from '@/lib/prisma'

export async function deletePosts(formData: FormData) {
  const ids = formData.getAll('ids') as string[]

  if (ids.length === 0) {
    return {
      error: 'No posts selected',
    }
  }

  try {
    await prisma.post.deleteMany({
      where: {
        id: {
          in: ids.map((id) => parseInt(id)),
        },
      },
    })

    revalidatePath('/posts')

    return {
      success: true,
      message: `${ids.length} post(s) deleted`,
    }
  } catch (error) {
    return {
      error: 'Failed to delete posts',
    }
  }
}
```

---

## バックエンド間連携によるデータ保存・削除

### バックエンド間連携とは
バックエンド間連携は、**複数のサーバーやサービス間でデータを連携する仕組み**です。Next.jsのServer Actionから、外部APIや別のバックエンドサービスを呼び出すことができます。

### 実践例: 外部APIとの連携

```typescript
// src/app/actions/external-actions.ts
'use server'

import { revalidatePath } from 'next/cache'

export async function syncWithExternalAPI(formData: FormData) {
  const title = formData.get('title') as string
  const content = formData.get('content') as string

  try {
    // 外部APIにデータを送信
    const response = await fetch('https://api.example.com/posts', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${process.env.EXTERNAL_API_KEY}`,
      },
      body: JSON.stringify({
        title,
        content,
      }),
    })

    if (!response.ok) {
      throw new Error('Failed to sync with external API')
    }

    const data = await response.json()

    // ローカルデータベースにも保存
    // const post = await prisma.post.create({
    //   data: {
    //     title,
    //     content,
    //     externalId: data.id,
    //   },
    // })

    revalidatePath('/posts')

    return {
      success: true,
      data,
    }
  } catch (error) {
    return {
      error: 'Failed to sync with external API',
    }
  }
}
```

### 実践例: 複数のデータベース操作

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function createPostWithTags(formData: FormData) {
  const title = formData.get('title') as string
  const content = formData.get('content') as string
  const authorId = parseInt(formData.get('authorId') as string)
  const tagIds = formData.getAll('tagIds') as string[]

  if (!title || !content || !authorId) {
    return {
      error: 'Title, content, and author are required',
    }
  }

  try {
    // トランザクションを使用して複数の操作を実行
    const post = await prisma.$transaction(async (tx) => {
      // 投稿を作成
      const newPost = await tx.post.create({
        data: {
          title,
          content,
          authorId,
        },
      })

      // タグを関連付け
      if (tagIds.length > 0) {
        await tx.post.update({
          where: { id: newPost.id },
          data: {
            tags: {
              connect: tagIds.map((id) => ({ id: parseInt(id) })),
            },
          },
        })
      }

      // ユーザーの投稿数を更新
      await tx.user.update({
        where: { id: authorId },
        data: {
          postCount: {
            increment: 1,
          },
        },
      })

      return newPost
    })

    revalidatePath('/posts')
    redirect(`/posts/${post.id}`)
  } catch (error) {
    return {
      error: 'Failed to create post',
    }
  }
}
```

### 実践例: 外部サービスとの連携（メール送信）

```typescript
// src/app/actions/notification-actions.ts
'use server'

import prisma from '@/lib/prisma'

export async function createPostAndNotify(formData: FormData) {
  const title = formData.get('title') as string
  const content = formData.get('content') as string
  const authorId = parseInt(formData.get('authorId') as string)

  try {
    // 投稿を作成
    const post = await prisma.post.create({
      data: {
        title,
        content,
        authorId,
      },
      include: {
        author: true,
      },
    })

    // メール送信サービスを呼び出し
    await fetch('https://api.email-service.com/send', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${process.env.EMAIL_API_KEY}`,
      },
      body: JSON.stringify({
        to: post.author.email,
        subject: 'Post Created',
        body: `Your post "${post.title}" has been created successfully.`,
      }),
    })

    return {
      success: true,
      post,
    }
  } catch (error) {
    return {
      error: 'Failed to create post and send notification',
    }
  }
}
```

### 実践例: バッチ処理

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import prisma from '@/lib/prisma'

export async function batchCreatePosts(formData: FormData) {
  const postsData = JSON.parse(formData.get('posts') as string) as Array<{
    title: string
    content: string
    authorId: number
  }>

  if (!postsData || postsData.length === 0) {
    return {
      error: 'No posts data provided',
    }
  }

  try {
    // バッチ処理で複数の投稿を作成
    const posts = await prisma.$transaction(
      postsData.map((data) =>
        prisma.post.create({
          data: {
            title: data.title,
            content: data.content,
            authorId: data.authorId,
          },
        })
      )
    )

    revalidatePath('/posts')

    return {
      success: true,
      count: posts.length,
      posts,
    }
  } catch (error) {
    return {
      error: 'Failed to batch create posts',
    }
  }
}
```

### 実践例: データ同期

```typescript
// src/app/actions/sync-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import prisma from '@/lib/prisma'

export async function syncPostsWithExternalService() {
  try {
    // 外部サービスからデータを取得
    const response = await fetch('https://api.example.com/posts', {
      headers: {
        'Authorization': `Bearer ${process.env.EXTERNAL_API_KEY}`,
      },
    })

    if (!response.ok) {
      throw new Error('Failed to fetch from external service')
    }

    const externalPosts = await response.json()

    // ローカルデータベースと同期
    const syncedPosts = await prisma.$transaction(
      externalPosts.map((externalPost: any) =>
        prisma.post.upsert({
          where: {
            externalId: externalPost.id,
          },
          update: {
            title: externalPost.title,
            content: externalPost.content,
            updatedAt: new Date(externalPost.updatedAt),
          },
          create: {
            title: externalPost.title,
            content: externalPost.content,
            externalId: externalPost.id,
            authorId: 1, // デフォルトの作成者
          },
        })
      )
    )

    revalidatePath('/posts')

    return {
      success: true,
      count: syncedPosts.length,
    }
  } catch (error) {
    return {
      error: 'Failed to sync posts',
    }
  }
}
```

---

## まとめ

この章では、Server Actionを使用したCRUD実装について学びました。

### 学んだこと
- CRUDとは: Create, Read, Update, Deleteの4つの基本操作
- Server Actionによるデータ保存: CreateとUpdate操作の実装
- Server Actionによるデータ削除: Delete操作の実装
- バックエンド間連携: 外部APIやサービスとの連携

### 重要なポイント
1. **Create**: 新しいデータを作成する操作
2. **Update**: 既存のデータを更新する操作
3. **Delete**: 既存のデータを削除する操作
4. **バックエンド間連携**: 複数のサービス間でデータを連携

### 次のステップ
次の章では、高度なUIフィードバックについて詳しく学びます。

---

## 演習問題

### 問題1: Create操作の実装
投稿を作成するServer Actionを実装してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function createPost(formData: FormData) {
  const title = formData.get('title') as string
  const content = formData.get('content') as string
  const authorId = parseInt(formData.get('authorId') as string)

  if (!title || !content || !authorId) {
    return {
      error: 'Title, content, and author are required',
    }
  }

  try {
    const post = await prisma.post.create({
      data: {
        title,
        content,
        authorId,
      },
    })

    revalidatePath('/posts')
    redirect(`/posts/${post.id}`)
  } catch (error) {
    return {
      error: 'Failed to create post',
    }
  }
}
```
</details>

### 問題2: Update操作の実装
投稿を更新するServer Actionを実装してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function updatePost(formData: FormData) {
  const id = parseInt(formData.get('id') as string)
  const title = formData.get('title') as string
  const content = formData.get('content') as string

  if (!id || !title || !content) {
    return {
      error: 'ID, title, and content are required',
    }
  }

  try {
    const post = await prisma.post.update({
      where: { id },
      data: {
        title,
        content,
      },
    })

    revalidatePath('/posts')
    revalidatePath(`/posts/${post.id}`)
    redirect(`/posts/${post.id}`)
  } catch (error: any) {
    if (error.code === 'P2025') {
      return {
        error: 'Post not found',
      }
    }

    return {
      error: 'Failed to update post',
    }
  }
}
```
</details>

### 問題3: Delete操作の実装
投稿を削除するServer Actionを実装してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function deletePost(formData: FormData) {
  const id = parseInt(formData.get('id') as string)

  if (!id) {
    return {
      error: 'ID is required',
    }
  }

  try {
    await prisma.post.delete({
      where: { id },
    })

    revalidatePath('/posts')
    redirect('/posts')
  } catch (error: any) {
    if (error.code === 'P2025') {
      return {
        error: 'Post not found',
      }
    }

    return {
      error: 'Failed to delete post',
    }
  }
}
```
</details>

### 問題4: トランザクションを使用した複数操作
トランザクションを使用して、投稿を作成し、同時にタグを関連付けるServer Actionを実装してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function createPostWithTags(formData: FormData) {
  const title = formData.get('title') as string
  const content = formData.get('content') as string
  const authorId = parseInt(formData.get('authorId') as string)
  const tagIds = formData.getAll('tagIds') as string[]

  try {
    const post = await prisma.$transaction(async (tx) => {
      const newPost = await tx.post.create({
        data: {
          title,
          content,
          authorId,
        },
      })

      if (tagIds.length > 0) {
        await tx.post.update({
          where: { id: newPost.id },
          data: {
            tags: {
              connect: tagIds.map((id) => ({ id: parseInt(id) })),
            },
          },
        })
      }

      return newPost
    })

    revalidatePath('/posts')
    redirect(`/posts/${post.id}`)
  } catch (error) {
    return {
      error: 'Failed to create post',
    }
  }
}
```
</details>

### 問題5: 外部APIとの連携
外部APIと連携して、投稿を作成するServer Actionを実装してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/actions/post-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import prisma from '@/lib/prisma'

export async function createPostWithExternalSync(formData: FormData) {
  const title = formData.get('title') as string
  const content = formData.get('content') as string
  const authorId = parseInt(formData.get('authorId') as string)

  try {
    // ローカルデータベースに保存
    const post = await prisma.post.create({
      data: {
        title,
        content,
        authorId,
      },
    })

    // 外部APIに同期
    const response = await fetch('https://api.example.com/posts', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${process.env.EXTERNAL_API_KEY}`,
      },
      body: JSON.stringify({
        title,
        content,
        localId: post.id,
      }),
    })

    if (response.ok) {
      const externalData = await response.json()
      
      // 外部IDを保存
      await prisma.post.update({
        where: { id: post.id },
        data: {
          externalId: externalData.id,
        },
      })
    }

    revalidatePath('/posts')

    return {
      success: true,
      post,
    }
  } catch (error) {
    return {
      error: 'Failed to create post',
    }
  }
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

