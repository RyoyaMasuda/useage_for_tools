# 6-1. Server Actionの基礎

この章では、Server Actionの基礎について学びます。Server Actionを使用することで、サーバーサイドでデータを更新し、クライアントサイドから簡単に呼び出すことができます。

---

## 目次

- [Server Actionとは](#server-actionとは)
- [Server Actionの定義とフォーム連携](#server-actionの定義とフォーム連携)
- [`use server` ディレクティブ](#use-server-ディレクティブ)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## Server Actionとは

### Server Actionとは
Server Actionは、**サーバーサイドで実行される関数**です。クライアントコンポーネントから直接呼び出すことができ、API Routeを作成する必要がありません。

### Server Actionの特徴
- **サーバーサイドで実行**: サーバーで実行されるため、データベースに直接アクセスできる
- **型安全**: TypeScriptと完全に統合され、型安全なデータ更新が可能
- **簡単な実装**: API Routeを作成する必要がなく、関数を定義するだけで使用できる
- **フォーム連携**: HTMLフォームと直接連携できる

### Server Actionの利点
- **開発効率**: API Routeを作成する必要がないため、開発が簡単
- **パフォーマンス**: サーバーサイドで実行されるため、クライアントに送信されるコードが少ない
- **セキュリティ**: サーバーサイドで実行されるため、機密情報を扱える

---

## Server Actionの定義とフォーム連携

### 基本的なServer Actionの定義

```typescript
// src/app/actions.ts
'use server'

export async function createUser(name: string, email: string) {
  // サーバーサイドで実行される処理
  const user = await prisma.user.create({
    data: {
      name,
      email,
    },
  })

  return user
}
```

### フォームとの連携

#### 基本的なフォーム連携

```typescript
// src/app/users/new/page.tsx
import { createUser } from '@/app/actions'

export default function NewUserPage() {
  return (
    <form action={createUser}>
      <input type="text" name="name" placeholder="Name" required />
      <input type="email" name="email" placeholder="Email" required />
      <button type="submit">Create User</button>
    </form>
  )
}
```

#### FormDataを使用したServer Action

```typescript
// src/app/actions.ts
'use server'

import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function createUser(formData: FormData) {
  const name = formData.get('name') as string
  const email = formData.get('email') as string

  if (!name || !email) {
    return {
      error: 'Name and email are required',
    }
  }

  const user = await prisma.user.create({
    data: {
      name,
      email,
    },
  })

  redirect(`/users/${user.id}`)
}
```

### 実践例: ユーザー作成フォーム

```typescript
// src/app/actions/user-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function createUser(formData: FormData) {
  const name = formData.get('name') as string
  const email = formData.get('email') as string

  if (!name || !email) {
    return {
      error: 'Name and email are required',
    }
  }

  try {
    const user = await prisma.user.create({
      data: {
        name,
        email,
      },
    })

    // キャッシュを無効化
    revalidatePath('/users')

    // 作成したユーザーページにリダイレクト
    redirect(`/users/${user.id}`)
  } catch (error) {
    return {
      error: 'Failed to create user',
    }
  }
}
```

```typescript
// src/app/users/new/page.tsx
import { createUser } from '@/app/actions/user-actions'

export default function NewUserPage() {
  return (
    <div className="container mx-auto px-4 py-8">
      <h1 className="text-3xl font-bold mb-6">Create User</h1>
      <form action={createUser} className="max-w-md">
        <div className="mb-4">
          <label htmlFor="name" className="block text-sm font-medium text-gray-700">
            Name
          </label>
          <input
            type="text"
            id="name"
            name="name"
            required
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
          />
        </div>
        <div className="mb-4">
          <label htmlFor="email" className="block text-sm font-medium text-gray-700">
            Email
          </label>
          <input
            type="email"
            id="email"
            name="email"
            required
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
          />
        </div>
        <button
          type="submit"
          className="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700"
        >
          Create User
        </button>
      </form>
    </div>
  )
}
```

### 実践例: エラーハンドリング付きフォーム

```typescript
// src/app/actions/user-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import prisma from '@/lib/prisma'

export async function createUser(
  prevState: any,
  formData: FormData
): Promise<{ error?: string; success?: boolean }> {
  const name = formData.get('name') as string
  const email = formData.get('email') as string

  if (!name || !email) {
    return {
      error: 'Name and email are required',
    }
  }

  try {
    await prisma.user.create({
      data: {
        name,
        email,
      },
    })

    revalidatePath('/users')

    return {
      success: true,
    }
  } catch (error: any) {
    if (error.code === 'P2002') {
      return {
        error: 'Email already exists',
      }
    }

    return {
      error: 'Failed to create user',
    }
  }
}
```

```typescript
// src/app/users/new/page.tsx
'use client'

import { useActionState } from 'react'
import { createUser } from '@/app/actions/user-actions'

export default function NewUserPage() {
  const [state, formAction] = useActionState(createUser, null)

  return (
    <div className="container mx-auto px-4 py-8">
      <h1 className="text-3xl font-bold mb-6">Create User</h1>
      <form action={formAction} className="max-w-md">
        {state?.error && (
          <div className="mb-4 p-4 bg-red-50 border border-red-200 rounded-md">
            <p className="text-red-800">{state.error}</p>
          </div>
        )}
        {state?.success && (
          <div className="mb-4 p-4 bg-green-50 border border-green-200 rounded-md">
            <p className="text-green-800">User created successfully!</p>
          </div>
        )}
        <div className="mb-4">
          <label htmlFor="name" className="block text-sm font-medium text-gray-700">
            Name
          </label>
          <input
            type="text"
            id="name"
            name="name"
            required
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
          />
        </div>
        <div className="mb-4">
          <label htmlFor="email" className="block text-sm font-medium text-gray-700">
            Email
          </label>
          <input
            type="email"
            id="email"
            name="email"
            required
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
          />
        </div>
        <button
          type="submit"
          className="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700"
        >
          Create User
        </button>
      </form>
    </div>
  )
}
```

### 実践例: 非同期フォーム送信

```typescript
// src/app/users/new/page.tsx
'use client'

import { useTransition } from 'react'
import { createUser } from '@/app/actions/user-actions'

export default function NewUserPage() {
  const [isPending, startTransition] = useTransition()

  const handleSubmit = async (formData: FormData) => {
    startTransition(async () => {
      await createUser(formData)
    })
  }

  return (
    <div className="container mx-auto px-4 py-8">
      <h1 className="text-3xl font-bold mb-6">Create User</h1>
      <form action={handleSubmit} className="max-w-md">
        <div className="mb-4">
          <label htmlFor="name" className="block text-sm font-medium text-gray-700">
            Name
          </label>
          <input
            type="text"
            id="name"
            name="name"
            required
            disabled={isPending}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 disabled:bg-gray-100"
          />
        </div>
        <div className="mb-4">
          <label htmlFor="email" className="block text-sm font-medium text-gray-700">
            Email
          </label>
          <input
            type="email"
            id="email"
            name="email"
            required
            disabled={isPending}
            className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 disabled:bg-gray-100"
          />
        </div>
        <button
          type="submit"
          disabled={isPending}
          className="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700 disabled:bg-gray-400 disabled:cursor-not-allowed"
        >
          {isPending ? 'Creating...' : 'Create User'}
        </button>
      </form>
    </div>
  )
}
```

### 実践例: 複数のServer Action

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
  } catch (error) {
    return {
      error: 'Failed to update post',
    }
  }
}

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
  } catch (error) {
    return {
      error: 'Failed to delete post',
    }
  }
}
```

---

## `use server` ディレクティブ

### `use server` とは
`use server`は、**Server Actionを定義するためのディレクティブ**です。ファイルの先頭または関数の前に配置することで、その関数をServer Actionとして扱います。

### ファイルレベルでの`use server`

```typescript
// src/app/actions.ts
'use server'  // ファイル内のすべての関数がServer Actionになる

import prisma from '@/lib/prisma'

export async function createUser(name: string, email: string) {
  const user = await prisma.user.create({
    data: {
      name,
      email,
    },
  })

  return user
}

export async function updateUser(id: number, name: string) {
  const user = await prisma.user.update({
    where: { id },
    data: { name },
  })

  return user
}
```

### 関数レベルでの`use server`

```typescript
// src/app/components/UserForm.tsx
import prisma from '@/lib/prisma'

// この関数だけがServer Action
async function createUser(formData: FormData) {
  'use server'

  const name = formData.get('name') as string
  const email = formData.get('email') as string

  const user = await prisma.user.create({
    data: {
      name,
      email,
    },
  })

  return user
}

// この関数は通常の関数（Server Actionではない）
function formatUser(user: any) {
  return `${user.name} (${user.email})`
}

export default function UserForm() {
  return (
    <form action={createUser}>
      <input type="text" name="name" required />
      <input type="email" name="email" required />
      <button type="submit">Create User</button>
    </form>
  )
}
```

### `use server`の使い分け

#### ファイルレベルでの使用（推奨）
- **複数のServer Actionを定義する場合**: 関連するServer Actionを1つのファイルにまとめる
- **再利用性**: 複数のコンポーネントから使用するServer Action

```typescript
// src/app/actions/user-actions.ts
'use server'

import prisma from '@/lib/prisma'

export async function createUser(formData: FormData) {
  // ...
}

export async function updateUser(formData: FormData) {
  // ...
}

export async function deleteUser(formData: FormData) {
  // ...
}
```

#### 関数レベルでの使用
- **単一のServer Action**: 特定のコンポーネントでのみ使用するServer Action
- **インライン定義**: コンポーネント内で直接定義する場合

```typescript
// src/app/components/UserForm.tsx
'use client'

export default function UserForm() {
  async function handleSubmit(formData: FormData) {
    'use server'

    const name = formData.get('name') as string
    const email = formData.get('email') as string

    // サーバーサイドで実行される処理
    await prisma.user.create({
      data: { name, email },
    })
  }

  return (
    <form action={handleSubmit}>
      <input type="text" name="name" required />
      <input type="email" name="email" required />
      <button type="submit">Create User</button>
    </form>
  )
}
```

### 実践例: Server Actionの整理

```typescript
// src/app/actions/index.ts
'use server'

// ユーザー関連のServer Actionを再エクスポート
export { createUser, updateUser, deleteUser } from './user-actions'
export { createPost, updatePost, deletePost } from './post-actions'
export { createComment, updateComment, deleteComment } from './comment-actions'
```

```typescript
// src/app/users/new/page.tsx
import { createUser } from '@/app/actions'

export default function NewUserPage() {
  return (
    <form action={createUser}>
      {/* ... */}
    </form>
  )
}
```

### 実践例: Server Actionの型定義

```typescript
// src/app/actions/types.ts
export interface CreateUserResult {
  success: boolean
  error?: string
  user?: {
    id: number
    name: string
    email: string
  }
}

export interface UpdateUserResult {
  success: boolean
  error?: string
  user?: {
    id: number
    name: string
    email: string
  }
}
```

```typescript
// src/app/actions/user-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import prisma from '@/lib/prisma'
import type { CreateUserResult, UpdateUserResult } from './types'

export async function createUser(
  formData: FormData
): Promise<CreateUserResult> {
  const name = formData.get('name') as string
  const email = formData.get('email') as string

  if (!name || !email) {
    return {
      success: false,
      error: 'Name and email are required',
    }
  }

  try {
    const user = await prisma.user.create({
      data: {
        name,
        email,
      },
    })

    revalidatePath('/users')

    return {
      success: true,
      user: {
        id: user.id,
        name: user.name,
        email: user.email,
      },
    }
  } catch (error) {
    return {
      success: false,
      error: 'Failed to create user',
    }
  }
}

export async function updateUser(
  formData: FormData
): Promise<UpdateUserResult> {
  const id = parseInt(formData.get('id') as string)
  const name = formData.get('name') as string

  if (!id || !name) {
    return {
      success: false,
      error: 'ID and name are required',
    }
  }

  try {
    const user = await prisma.user.update({
      where: { id },
      data: { name },
    })

    revalidatePath('/users')
    revalidatePath(`/users/${user.id}`)

    return {
      success: true,
      user: {
        id: user.id,
        name: user.name,
        email: user.email,
      },
    }
  } catch (error) {
    return {
      success: false,
      error: 'Failed to update user',
    }
  }
}
```

---

## まとめ

この章では、Server Actionの基礎について学びました。

### 学んだこと
- Server Actionとは: サーバーサイドで実行される関数
- Server Actionの定義とフォーム連携: フォームと連携したServer Actionの実装
- `use server` ディレクティブ: Server Actionを定義するためのディレクティブ

### 重要なポイント
1. **Server Action**: サーバーサイドで実行される関数で、API Routeを作成する必要がない
2. **フォーム連携**: HTMLフォームと直接連携できる
3. **`use server`**: ファイルレベルまたは関数レベルで使用できる
4. **型安全性**: TypeScriptと完全に統合され、型安全なデータ更新が可能

### 次のステップ
次の章では、CRUD実装について詳しく学びます。

---

## 演習問題

### 問題1: 基本的なServer Actionの定義
ユーザーを作成するServer Actionを定義してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/actions/user-actions.ts
'use server'

import prisma from '@/lib/prisma'

export async function createUser(formData: FormData) {
  const name = formData.get('name') as string
  const email = formData.get('email') as string

  const user = await prisma.user.create({
    data: {
      name,
      email,
    },
  })

  return user
}
```
</details>

### 問題2: フォームとの連携
Server Actionを使用して、ユーザー作成フォームを作成してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/users/new/page.tsx
import { createUser } from '@/app/actions/user-actions'

export default function NewUserPage() {
  return (
    <form action={createUser}>
      <input type="text" name="name" placeholder="Name" required />
      <input type="email" name="email" placeholder="Email" required />
      <button type="submit">Create User</button>
    </form>
  )
}
```
</details>

### 問題3: エラーハンドリング
エラーハンドリングを含むServer Actionを作成してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/actions/user-actions.ts
'use server'

import prisma from '@/lib/prisma'

export async function createUser(
  prevState: any,
  formData: FormData
): Promise<{ error?: string; success?: boolean }> {
  const name = formData.get('name') as string
  const email = formData.get('email') as string

  if (!name || !email) {
    return {
      error: 'Name and email are required',
    }
  }

  try {
    await prisma.user.create({
      data: {
        name,
        email,
      },
    })

    return {
      success: true,
    }
  } catch (error: any) {
    if (error.code === 'P2002') {
      return {
        error: 'Email already exists',
      }
    }

    return {
      error: 'Failed to create user',
    }
  }
}
```
</details>

### 問題4: `use server`の使い分け
ファイルレベルと関数レベルでの`use server`の使い分けを説明し、それぞれの例を示してください。

<details>
<summary>解答例</summary>

```typescript
// ファイルレベル（推奨）
// src/app/actions/user-actions.ts
'use server'

import prisma from '@/lib/prisma'

export async function createUser(formData: FormData) {
  // ...
}

export async function updateUser(formData: FormData) {
  // ...
}
```

```typescript
// 関数レベル
// src/app/components/UserForm.tsx
'use client'

export default function UserForm() {
  async function handleSubmit(formData: FormData) {
    'use server'

    const name = formData.get('name') as string
    const email = formData.get('email') as string

    await prisma.user.create({
      data: { name, email },
    })
  }

  return (
    <form action={handleSubmit}>
      {/* ... */}
    </form>
  )
}
```
</details>

### 問題5: 完全なServer Actionの実装
エラーハンドリング、キャッシュの無効化、リダイレクトを含む完全なServer Actionを作成してください。

<details>
<summary>解答例</summary>

```typescript
// src/app/actions/user-actions.ts
'use server'

import { revalidatePath } from 'next/cache'
import { redirect } from 'next/navigation'
import prisma from '@/lib/prisma'

export async function createUser(formData: FormData) {
  const name = formData.get('name') as string
  const email = formData.get('email') as string

  if (!name || !email) {
    return {
      error: 'Name and email are required',
    }
  }

  try {
    const user = await prisma.user.create({
      data: {
        name,
        email,
      },
    })

    revalidatePath('/users')
    redirect(`/users/${user.id}`)
  } catch (error: any) {
    if (error.code === 'P2002') {
      return {
        error: 'Email already exists',
      }
    }

    return {
      error: 'Failed to create user',
    }
  }
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

