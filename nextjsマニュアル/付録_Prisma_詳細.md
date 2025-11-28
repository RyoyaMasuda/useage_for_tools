# 付録：Prisma 詳細

この付録では、Next.jsで使用するPrismaについて詳しく学びます。Prisma schemaの概要、Prisma Clientの概要、マイグレーションとシーディングを理解することで、データベース操作を効率的に行えるようになります。

---

## 目次

- [A.1 Prisma schemaの概要](#a1-prisma-schemaの概要)
- [A.2 Prisma Clientの概要](#a2-prisma-clientの概要)
- [A.3 Prismaのマイグレーションとシーディング](#a3-prismaのマイグレーションとシーディング)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## A.1 Prisma schemaの概要

### Prisma schemaとは
Prisma schemaは、**データベースの構造を定義するファイル**です。`schema.prisma`ファイルに、データベース接続情報、モデル定義、リレーションなどを記述します。

### 日常生活での例
- **設計図**: 建物の構造を定義する設計図
- **レシピ**: 料理の作り方を定義するレシピ
- **地図**: 地域の構造を定義する地図

### 基本的なPrisma schema

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  published Boolean  @default(false)
  authorId  Int
  author    User     @relation(fields: [authorId], references: [id])
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

### データソースの設定

#### PostgreSQL

```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}
```

#### MySQL

```prisma
datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}
```

#### SQLite

```prisma
datasource db {
  provider = "sqlite"
  url      = "file:./dev.db"
}
```

### モデルの定義

#### 基本的なモデル

```prisma
model User {
  id    Int    @id @default(autoincrement())
  email String @unique
  name  String
}
```

#### フィールドの型

```prisma
model Example {
  // 数値型
  id        Int      @id @default(autoincrement())
  count     Int
  price     Float
  
  // 文字列型
  name      String
  email     String   @unique
  bio       String?
  
  // 日時型
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  
  // ブール型
  isActive  Boolean  @default(true)
  
  // JSON型
  metadata  Json?
}
```

#### フィールド属性

```prisma
model User {
  id        Int      @id                    // プライマリキー
  email     String   @unique                // 一意制約
  name      String?                         // オプショナル（NULL許可）
  age       Int      @default(0)            // デフォルト値
  createdAt DateTime @default(now())       // デフォルト値（現在時刻）
  updatedAt DateTime @updatedAt            // 自動更新
  posts     Post[]                         // リレーション
}
```

### リレーションの定義

#### 1対多（One-to-Many）

```prisma
model User {
  id    Int    @id @default(autoincrement())
  email String
  posts Post[]  // 1人のユーザーが複数の投稿を持つ
}

model Post {
  id       Int  @id @default(autoincrement())
  title    String
  authorId Int
  author   User @relation(fields: [authorId], references: [id])
}
```

#### 多対多（Many-to-Many）

```prisma
model Post {
  id       Int      @id @default(autoincrement())
  title    String
  tags     Tag[]    @relation("PostTags")
}

model Tag {
  id    Int    @id @default(autoincrement())
  name  String @unique
  posts Post[] @relation("PostTags")
}
```

#### 1対1（One-to-One）

```prisma
model User {
  id      Int     @id @default(autoincrement())
  email   String
  profile Profile?
}

model Profile {
  id     Int    @id @default(autoincrement())
  bio    String?
  userId Int    @unique
  user   User   @relation(fields: [userId], references: [id])
}
```

### 実践例: Prisma schema

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  password  String
  role      Role     @default(USER)
  posts     Post[]
  profile   Profile?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Profile {
  id        Int      @id @default(autoincrement())
  bio       String?
  avatar    String?
  userId    Int      @unique
  user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String?
  published Boolean  @default(false)
  authorId  Int
  author    User     @relation(fields: [authorId], references: [id], onDelete: Cascade)
  tags      Tag[]     @relation("PostTags")
  comments  Comment[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Tag {
  id    Int    @id @default(autoincrement())
  name  String @unique
  posts Post[] @relation("PostTags")
}

model Comment {
  id        Int      @id @default(autoincrement())
  content   String
  postId    Int
  post      Post     @relation(fields: [postId], references: [id], onDelete: Cascade)
  authorId  Int
  author    User     @relation(fields: [authorId], references: [id], onDelete: Cascade)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

enum Role {
  USER
  ADMIN
}
```

---

## A.2 Prisma Clientの概要

### Prisma Clientとは
Prisma Clientは、**Prisma schemaから自動生成される型安全なデータベースクライアント**です。TypeScriptの型推論を活用して、安全にデータベース操作を行えます。

### 日常生活での例
- **リモコン**: テレビを操作するリモコン
- **インターフェース**: 機械を操作するインターフェース
- **API**: サービスを利用するAPI

### Prisma Clientの生成

```bash
npx prisma generate
```

### Prisma Clientの初期化

```tsx
// lib/prisma.ts
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma =
  globalForPrisma.prisma ??
  new PrismaClient({
    log: ['query', 'error', 'warn'],
  });

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

### 基本的なCRUD操作

#### Create（作成）

```tsx
// 単一レコードの作成
const user = await prisma.user.create({
  data: {
    email: 'user@example.com',
    name: '太郎',
    password: 'hashedpassword',
  },
});

// 複数レコードの作成
const users = await prisma.user.createMany({
  data: [
    { email: 'user1@example.com', name: '太郎', password: 'hash1' },
    { email: 'user2@example.com', name: '花子', password: 'hash2' },
  ],
});
```

#### Read（読み取り）

```tsx
// 単一レコードの取得
const user = await prisma.user.findUnique({
  where: {
    id: 1,
  },
});

// 複数レコードの取得
const users = await prisma.user.findMany({
  where: {
    role: 'USER',
  },
  orderBy: {
    createdAt: 'desc',
  },
});

// 最初のレコードの取得
const firstUser = await prisma.user.findFirst({
  where: {
    email: {
      contains: '@example.com',
    },
  },
});
```

#### Update（更新）

```tsx
// 単一レコードの更新
const user = await prisma.user.update({
  where: {
    id: 1,
  },
  data: {
    name: '新しい名前',
  },
});

// 複数レコードの更新
const result = await prisma.user.updateMany({
  where: {
    role: 'USER',
  },
  data: {
    role: 'ADMIN',
  },
});

// レコードの更新または作成（upsert）
const user = await prisma.user.upsert({
  where: {
    email: 'user@example.com',
  },
  update: {
    name: '更新された名前',
  },
  create: {
    email: 'user@example.com',
    name: '新しい名前',
    password: 'hashedpassword',
  },
});
```

#### Delete（削除）

```tsx
// 単一レコードの削除
const user = await prisma.user.delete({
  where: {
    id: 1,
  },
});

// 複数レコードの削除
const result = await prisma.user.deleteMany({
  where: {
    role: 'GUEST',
  },
});
```

### リレーションの操作

#### リレーションを含む取得

```tsx
// 投稿と著者を取得
const post = await prisma.post.findUnique({
  where: {
    id: 1,
  },
  include: {
    author: true,
    tags: true,
    comments: {
      include: {
        author: true,
      },
    },
  },
});

// ユーザーと投稿を取得
const user = await prisma.user.findUnique({
  where: {
    id: 1,
  },
  include: {
    posts: {
      where: {
        published: true,
      },
      orderBy: {
        createdAt: 'desc',
      },
    },
    profile: true,
  },
});
```

#### リレーションの作成

```tsx
// 投稿とタグを同時に作成
const post = await prisma.post.create({
  data: {
    title: '新しい投稿',
    content: '投稿内容',
    author: {
      connect: {
        id: 1,
      },
    },
    tags: {
      connectOrCreate: [
        {
          where: { name: '技術' },
          create: { name: '技術' },
        },
        {
          where: { name: 'Next.js' },
          create: { name: 'Next.js' },
        },
      ],
    },
  },
});
```

### クエリの条件指定

#### where句

```tsx
// 等価条件
const users = await prisma.user.findMany({
  where: {
    role: 'USER',
  },
});

// 比較条件
const posts = await prisma.post.findMany({
  where: {
    createdAt: {
      gte: new Date('2024-01-01'),
    },
  },
});

// 文字列検索
const users = await prisma.user.findMany({
  where: {
    email: {
      contains: '@example.com',
    },
  },
});

// 複合条件
const posts = await prisma.post.findMany({
  where: {
    AND: [
      { published: true },
      { createdAt: { gte: new Date('2024-01-01') } },
    ],
  },
});
```

#### orderBy句

```tsx
// 単一フィールドでソート
const posts = await prisma.post.findMany({
  orderBy: {
    createdAt: 'desc',
  },
});

// 複数フィールドでソート
const posts = await prisma.post.findMany({
  orderBy: [
    { published: 'asc' },
    { createdAt: 'desc' },
  ],
});
```

#### select句

```tsx
// 特定のフィールドのみ取得
const user = await prisma.user.findUnique({
  where: {
    id: 1,
  },
  select: {
    id: true,
    email: true,
    name: true,
  },
});
```

#### takeとskip（ページネーション）

```tsx
// 最初の10件を取得
const posts = await prisma.post.findMany({
  take: 10,
  skip: 0,
  orderBy: {
    createdAt: 'desc',
  },
});

// 2ページ目を取得（11-20件目）
const posts = await prisma.post.findMany({
  take: 10,
  skip: 10,
  orderBy: {
    createdAt: 'desc',
  },
});
```

### 実践例: Prisma Client

```tsx
// app/api/users/route.ts
import { prisma } from '@/lib/prisma';
import { NextResponse } from 'next/server';

// GET: ユーザー一覧を取得
export async function GET() {
  const users = await prisma.user.findMany({
    include: {
      posts: {
        where: {
          published: true,
        },
      },
      profile: true,
    },
    orderBy: {
      createdAt: 'desc',
    },
  });
  
  return NextResponse.json(users);
}

// POST: ユーザーを作成
export async function POST(request: Request) {
  const body = await request.json();
  
  const user = await prisma.user.create({
    data: {
      email: body.email,
      name: body.name,
      password: body.password,
    },
  });
  
  return NextResponse.json(user);
}

// app/api/posts/route.ts
import { prisma } from '@/lib/prisma';
import { NextResponse } from 'next/server';

// GET: 投稿一覧を取得
export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  const page = parseInt(searchParams.get('page') || '1');
  const limit = parseInt(searchParams.get('limit') || '10');
  
  const [posts, total] = await Promise.all([
    prisma.post.findMany({
      where: {
        published: true,
      },
      include: {
        author: {
          select: {
            id: true,
            name: true,
            email: true,
          },
        },
        tags: true,
      },
      orderBy: {
        createdAt: 'desc',
      },
      take: limit,
      skip: (page - 1) * limit,
    }),
    prisma.post.count({
      where: {
        published: true,
      },
    }),
  ]);
  
  return NextResponse.json({
    posts,
    total,
    page,
    limit,
  });
}

// app/actions.ts
'use server';

import { prisma } from '@/lib/prisma';
import { revalidatePath } from 'next/cache';

// 投稿を作成
export async function createPost(data: {
  title: string;
  content: string;
  authorId: number;
  tagIds: number[];
}) {
  const post = await prisma.post.create({
    data: {
      title: data.title,
      content: data.content,
      author: {
        connect: {
          id: data.authorId,
        },
      },
      tags: {
        connect: data.tagIds.map(id => ({ id })),
      },
    },
    include: {
      author: true,
      tags: true,
    },
  });
  
  revalidatePath('/posts');
  return post;
}

// 投稿を更新
export async function updatePost(
  id: number,
  data: {
    title?: string;
    content?: string;
    published?: boolean;
  }
) {
  const post = await prisma.post.update({
    where: {
      id,
    },
    data,
  });
  
  revalidatePath('/posts');
  revalidatePath(`/posts/${id}`);
  return post;
}

// 投稿を削除
export async function deletePost(id: number) {
  await prisma.post.delete({
    where: {
      id,
  },
  });
  
  revalidatePath('/posts');
}
```

---

## A.3 Prismaのマイグレーションとシーディング

### マイグレーションとは
マイグレーションは、**データベースのスキーマを変更するための仕組み**です。Prisma schemaの変更をデータベースに反映させます。

### シーディングとは
シーディングは、**データベースに初期データを投入する**処理です。開発やテスト用のデータを簡単に投入できます。

### マイグレーションの実行

#### マイグレーションの作成

```bash
npx prisma migrate dev --name init
```

#### マイグレーションの適用

```bash
npx prisma migrate deploy
```

#### マイグレーションの状態確認

```bash
npx prisma migrate status
```

### 実践例: マイグレーション

#### 初回マイグレーション

```bash
# 1. Prisma schemaを作成
# prisma/schema.prisma

# 2. マイグレーションを作成
npx prisma migrate dev --name init

# 3. Prisma Clientを生成
npx prisma generate
```

#### スキーマの変更とマイグレーション

```prisma
// prisma/schema.prisma
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  // 新しいフィールドを追加
  age       Int?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

```bash
# マイグレーションを作成
npx prisma migrate dev --name add_age_to_user
```

### シーディングの実装

#### seed.tsファイルの作成

```tsx
// prisma/seed.ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

async function main() {
  // ユーザーを作成
  const user1 = await prisma.user.upsert({
    where: { email: 'user1@example.com' },
    update: {},
    create: {
      email: 'user1@example.com',
      name: '太郎',
      password: 'hashedpassword1',
      role: 'USER',
      profile: {
        create: {
          bio: '太郎のプロフィール',
        },
      },
    },
  });

  const user2 = await prisma.user.upsert({
    where: { email: 'user2@example.com' },
    update: {},
    create: {
      email: 'user2@example.com',
      name: '花子',
      password: 'hashedpassword2',
      role: 'ADMIN',
      profile: {
        create: {
          bio: '花子のプロフィール',
        },
      },
    },
  });

  // タグを作成
  const tag1 = await prisma.tag.upsert({
    where: { name: '技術' },
    update: {},
    create: { name: '技術' },
  });

  const tag2 = await prisma.tag.upsert({
    where: { name: 'Next.js' },
    update: {},
    create: { name: 'Next.js' },
  });

  // 投稿を作成
  const post1 = await prisma.post.create({
    data: {
      title: '最初の投稿',
      content: 'これは最初の投稿です。',
      published: true,
      author: {
        connect: { id: user1.id },
      },
      tags: {
        connect: [{ id: tag1.id }, { id: tag2.id }],
      },
    },
  });

  const post2 = await prisma.post.create({
    data: {
      title: '2番目の投稿',
      content: 'これは2番目の投稿です。',
      published: true,
      author: {
        connect: { id: user2.id },
      },
      tags: {
        connect: [{ id: tag1.id }],
      },
    },
  });

  // コメントを作成
  await prisma.comment.create({
    data: {
      content: '素晴らしい投稿ですね！',
      post: {
        connect: { id: post1.id },
      },
      author: {
        connect: { id: user2.id },
      },
    },
  });

  console.log('シーディングが完了しました');
}

main()
  .catch((e) => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

#### package.jsonの設定

```json
{
  "prisma": {
    "seed": "ts-node --compiler-options {\"module\":\"CommonJS\"} prisma/seed.ts"
  }
}
```

#### シーディングの実行

```bash
npx prisma db seed
```

### 実践例: マイグレーションとシーディング

#### 開発環境でのマイグレーション

```bash
# 開発環境でマイグレーションを作成・適用
npx prisma migrate dev --name add_comments

# シーディングを実行
npx prisma db seed
```

#### 本番環境でのマイグレーション

```bash
# 本番環境でマイグレーションを適用
npx prisma migrate deploy

# Prisma Clientを生成
npx prisma generate
```

#### マイグレーションのロールバック

```bash
# マイグレーションの状態を確認
npx prisma migrate status

# データベースをリセット（開発環境のみ）
npx prisma migrate reset
```

### Prisma Studio

Prisma Studioは、**データベースを視覚的に操作できるGUIツール**です。

#### Prisma Studioの起動

```bash
npx prisma studio
```

#### Prisma Studioの機能

- データの閲覧
- データの作成・更新・削除
- リレーションの確認
- データの検索・フィルタリング

---

## まとめ

この付録では、Prismaの詳細について学びました。

### 学んだこと
- **Prisma schema**: データベースの構造を定義するファイル
- **Prisma Client**: 型安全なデータベースクライアント
- **マイグレーション**: データベーススキーマの変更管理
- **シーディング**: 初期データの投入
- **Prisma Studio**: データベースの視覚的操作

### 重要なポイント
1. **Prisma schema**: モデル、リレーション、フィールドを定義
2. **Prisma Client**: 型安全なCRUD操作を提供
3. **マイグレーション**: スキーマの変更を管理
4. **シーディング**: 開発・テスト用のデータを投入
5. **Prisma Studio**: データベースを視覚的に操作

### 使い分けの目安
- **Prisma schema**: データベースの構造を定義
- **Prisma Client**: アプリケーションからデータベースを操作
- **マイグレーション**: スキーマの変更を管理
- **シーディング**: 初期データを投入

### 次のステップ
Prismaを理解することで、型安全で効率的なデータベース操作ができるようになります。Next.jsアプリケーションでPrismaを活用して、堅牢なデータベース層を構築しましょう。

---

## 演習問題

### 問題1: Prisma schemaの作成
User、Post、Commentの3つのモデルを持つPrisma schemaを作成してください。

<details>
<summary>解答例</summary>

```prisma
// prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        Int       @id @default(autoincrement())
  email     String    @unique
  name      String?
  posts     Post[]
  comments  Comment[]
  createdAt DateTime  @default(now())
  updatedAt DateTime  @updatedAt
}

model Post {
  id        Int       @id @default(autoincrement())
  title     String
  content   String?
  authorId  Int
  author    User      @relation(fields: [authorId], references: [id])
  comments  Comment[]
  createdAt DateTime  @default(now())
  updatedAt DateTime  @updatedAt
}

model Comment {
  id        Int      @id @default(autoincrement())
  content   String
  postId    Int
  post      Post     @relation(fields: [postId], references: [id])
  authorId  Int
  author    User     @relation(fields: [authorId], references: [id])
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```
</details>

### 問題2: Prisma ClientでのCRUD操作
Prisma Clientを使って、ユーザーの作成、取得、更新、削除を行うコードを書いてください。

<details>
<summary>解答例</summary>

```tsx
import { prisma } from '@/lib/prisma';

// 作成
const user = await prisma.user.create({
  data: {
    email: 'user@example.com',
    name: '太郎',
  },
});

// 取得
const user = await prisma.user.findUnique({
  where: { id: 1 },
});

// 更新
const user = await prisma.user.update({
  where: { id: 1 },
  data: { name: '新しい名前' },
});

// 削除
await prisma.user.delete({
  where: { id: 1 },
});
```
</details>

### 問題3: リレーションを含む取得
Prisma Clientを使って、投稿と著者、コメントを同時に取得するコードを書いてください。

<details>
<summary>解答例</summary>

```tsx
import { prisma } from '@/lib/prisma';

const post = await prisma.post.findUnique({
  where: { id: 1 },
  include: {
    author: true,
    comments: {
      include: {
        author: true,
      },
    },
  },
});
```
</details>

### 問題4: マイグレーションの作成
Prisma schemaに新しいフィールドを追加し、マイグレーションを作成してください。

<details>
<summary>解答例</summary>

```prisma
// prisma/schema.prisma
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  age       Int?     // 新しいフィールド
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

```bash
npx prisma migrate dev --name add_age_to_user
```
</details>

### 問題5: シーディングの実装
seed.tsファイルを作成し、ユーザーと投稿の初期データを投入してください。

<details>
<summary>解答例</summary>

```tsx
// prisma/seed.ts
import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

async function main() {
  const user = await prisma.user.create({
    data: {
      email: 'user@example.com',
      name: '太郎',
      posts: {
        create: {
          title: '最初の投稿',
          content: '投稿内容',
        },
      },
    },
  });
  
  console.log('シーディングが完了しました');
}

main()
  .catch(console.error)
  .finally(() => prisma.$disconnect());
```
</details>

---

お疲れ様でした！Prismaの詳細を理解することで、Next.jsアプリケーションで効率的にデータベースを操作できるようになりました。

