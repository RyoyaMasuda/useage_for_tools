# 1-3. ORMライブラリ「Prisma」の導入

この章では、ORMライブラリ「Prisma」の導入について学びます。Prismaを使用することで、型安全なデータベースアクセスが可能になります。

---

## 目次

- [Prismaとは](#prismaとは)
- [Prisma schemaの概要と定義](#prisma-schemaの概要と定義)
- [Prisma Clientの概要と生成](#prisma-clientの概要と生成)
- [DBビジュアルエディター「Prisma Studio」の使い方](#dbビジュアルエディターprisma-studioの使い方)
- [マイグレーションとシーディング (初期データ投入)](#マイグレーションとシーディング-初期データ投入)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## Prismaとは

### ORMとは
ORM（Object-Relational Mapping）は、**オブジェクト指向プログラミングとリレーショナルデータベースを結びつける技術**です。SQLを直接書かずに、プログラミング言語のオブジェクトとしてデータベースを操作できます。

### Prismaの特徴
- **型安全性**: TypeScriptと完全に統合され、型安全なデータベースアクセスが可能
- **マイグレーション**: データベーススキーマの変更を簡単に管理
- **Prisma Studio**: データベースを視覚的に操作できるGUIツール
- **高速**: 最適化されたクエリで高速なデータベースアクセス

### Prismaのインストール

```bash
# Prisma CLIをプロジェクトに追加
npm install prisma --save-dev

# Prisma Clientをインストール
npm install @prisma/client
```

### Prismaの初期化

```bash
# Prismaを初期化（PostgreSQLを使用する場合）
npx prisma init --datasource-provider postgresql
```

このコマンドを実行すると、以下のファイルが作成されます：
- `prisma/schema.prisma`: Prismaスキーマファイル
- `.env`: 環境変数ファイル（DATABASE_URLが追加される）

---

## Prisma schemaの概要と定義

### Prisma Schemaとは
Prisma Schemaは、**データベースの構造を定義するファイル**です。モデル、フィールド、リレーションを定義します。

### 基本的なスキーマ構造

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
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

### データソースの設定

```prisma
datasource db {
  provider = "postgresql"  // データベースの種類
  url      = env("DATABASE_URL")  // 環境変数から接続URLを取得
}
```

#### サポートされているデータベース
- PostgreSQL
- MySQL
- SQLite
- SQL Server
- MongoDB

### モデルの定義

モデルは、**データベースのテーブルに対応**します。

#### 基本的なモデル定義

```prisma
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

### フィールドの型

#### スカラー型

```prisma
model Example {
  // 数値型
  id        Int      @id @default(autoincrement())
  age       Int
  price     Float
  bigInt    BigInt
  
  // 文字列型
  name      String
  text      String   @db.Text  // 長いテキスト
  
  // 真偽値
  isActive  Boolean  @default(true)
  
  // 日時
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  
  // JSON
  metadata  Json
  
  // バイナリ
  image     Bytes
}
```

### フィールド属性

#### @id: プライマリキー

```prisma
model User {
  id Int @id @default(autoincrement())
}
```

#### @default: デフォルト値

```prisma
model User {
  id        Int      @id @default(autoincrement())
  isActive  Boolean  @default(true)
  createdAt DateTime @default(now())
}
```

#### @unique: 一意制約

```prisma
model User {
  id    Int    @id @default(autoincrement())
  email String @unique
}
```

#### @updatedAt: 自動更新

```prisma
model User {
  updatedAt DateTime @updatedAt
}
```

#### @optional: オプショナルフィールド

```prisma
model User {
  name String?  // ? でオプショナルを表す
}
```

### リレーションの定義

#### 1対多のリレーション

```prisma
model User {
  id    Int    @id @default(autoincrement())
  email String @unique
  posts Post[]  // 1人のユーザーが複数の投稿を持つ
}

model Post {
  id        Int    @id @default(autoincrement())
  title     String
  content   String
  authorId  Int
  author    User   @relation(fields: [authorId], references: [id])
}
```

#### 多対多のリレーション

```prisma
model Post {
  id       Int       @id @default(autoincrement())
  title    String
  tags     Tag[]
}

model Tag {
  id    Int    @id @default(autoincrement())
  name  String @unique
  posts Post[]
}
```

#### 1対1のリレーション

```prisma
model User {
  id      Int     @id @default(autoincrement())
  email   String  @unique
  profile Profile?
}

model Profile {
  id     Int    @id @default(autoincrement())
  bio    String?
  userId Int    @unique
  user   User   @relation(fields: [userId], references: [id])
}
```

### 実践例: 完全なスキーマ定義

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
  profile   Profile?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Post {
  id        Int      @id @default(autoincrement())
  title     String
  content   String   @db.Text
  published Boolean  @default(false)
  authorId  Int
  author    User     @relation(fields: [authorId], references: [id], onDelete: Cascade)
  tags      Tag[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Tag {
  id    Int    @id @default(autoincrement())
  name  String @unique
  posts Post[]
}

model Profile {
  id     Int    @id @default(autoincrement())
  bio    String?
  userId Int    @unique
  user   User   @relation(fields: [userId], references: [id], onDelete: Cascade)
}
```

---

## Prisma Clientの概要と生成

### Prisma Clientとは
Prisma Clientは、**Prisma Schemaから自動生成される型安全なデータベースクライアント**です。

### Prisma Clientの生成

```bash
# Prisma Clientを生成
npx prisma generate
```

このコマンドを実行すると、`node_modules/.prisma/client`にPrisma Clientが生成されます。

### Prisma Clientの使用

#### 基本的な使用方法

```typescript
// src/lib/prisma.ts
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

export default prisma
```

#### シングルトンパターン（推奨）

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

### CRUD操作

#### Create（作成）

```typescript
// 単一レコードの作成
const user = await prisma.user.create({
  data: {
    email: 'user@example.com',
    name: 'John Doe',
  },
})

// 複数レコードの作成
const users = await prisma.user.createMany({
  data: [
    { email: 'user1@example.com', name: 'User 1' },
    { email: 'user2@example.com', name: 'User 2' },
  ],
})
```

#### Read（読み取り）

```typescript
// 全件取得
const users = await prisma.user.findMany()

// 単一レコードの取得
const user = await prisma.user.findUnique({
  where: {
    id: 1,
  },
})

// 条件に一致するレコードの取得
const users = await prisma.user.findMany({
  where: {
    email: {
      contains: '@example.com',
    },
  },
})

// リレーションを含む取得
const user = await prisma.user.findUnique({
  where: {
    id: 1,
  },
  include: {
    posts: true,
    profile: true,
  },
})
```

#### Update（更新）

```typescript
// 単一レコードの更新
const user = await prisma.user.update({
  where: {
    id: 1,
  },
  data: {
    name: 'Jane Doe',
  },
})

// 複数レコードの更新
const result = await prisma.user.updateMany({
  where: {
    email: {
      contains: '@example.com',
    },
  },
  data: {
    name: 'Updated Name',
  },
})

// レコードの存在確認と更新（存在しない場合は作成）
const user = await prisma.user.upsert({
  where: {
    email: 'user@example.com',
  },
  update: {
    name: 'Updated Name',
  },
  create: {
    email: 'user@example.com',
    name: 'New User',
  },
})
```

#### Delete（削除）

```typescript
// 単一レコードの削除
const user = await prisma.user.delete({
  where: {
    id: 1,
  },
})

// 複数レコードの削除
const result = await prisma.user.deleteMany({
  where: {
    email: {
      contains: '@example.com',
    },
  },
})
```

### 高度なクエリ

#### ソート

```typescript
const users = await prisma.user.findMany({
  orderBy: {
    createdAt: 'desc',
  },
})
```

#### ページネーション

```typescript
const users = await prisma.user.findMany({
  skip: 10,  // 最初の10件をスキップ
  take: 5,   // 5件を取得
})
```

#### 集計

```typescript
const count = await prisma.user.count()

const count = await prisma.user.count({
  where: {
    email: {
      contains: '@example.com',
    },
  },
})
```

### 実践例: Next.jsでの使用

```typescript
// src/app/api/users/route.ts
import { NextResponse } from 'next/server'
import prisma from '@/lib/prisma'

// GET: ユーザー一覧を取得
export async function GET() {
  try {
    const users = await prisma.user.findMany({
      include: {
        posts: true,
      },
    })
    return NextResponse.json(users)
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to fetch users' },
      { status: 500 }
    )
  }
}

// POST: ユーザーを作成
export async function POST(request: Request) {
  try {
    const body = await request.json()
    const user = await prisma.user.create({
      data: {
        email: body.email,
        name: body.name,
      },
    })
    return NextResponse.json(user, { status: 201 })
  } catch (error) {
    return NextResponse.json(
      { error: 'Failed to create user' },
      { status: 500 }
    )
  }
}
```

```typescript
// src/app/users/page.tsx
import prisma from '@/lib/prisma'

export default async function UsersPage() {
  const users = await prisma.user.findMany({
    include: {
      posts: true,
    },
  })

  return (
    <div>
      <h1>Users</h1>
      <ul>
        {users.map((user) => (
          <li key={user.id}>
            {user.name} ({user.email})
            <ul>
              {user.posts.map((post) => (
                <li key={post.id}>{post.title}</li>
              ))}
            </ul>
          </li>
        ))}
      </ul>
    </div>
  )
}
```

---

## DBビジュアルエディター「Prisma Studio」の使い方

### Prisma Studioとは
Prisma Studioは、**データベースを視覚的に操作できるGUIツール**です。ブラウザでデータの確認、作成、更新、削除ができます。

### Prisma Studioの起動

```bash
# Prisma Studioを起動
npx prisma studio
```

このコマンドを実行すると、ブラウザで `http://localhost:5555` が開きます。

### Prisma Studioの基本操作

#### 1. モデルの選択
左側のサイドバーから、操作したいモデル（テーブル）を選択します。

#### 2. データの確認
選択したモデルのデータが一覧表示されます。

#### 3. データの作成
「Add record」ボタンをクリックして、新しいレコードを作成できます。

#### 4. データの更新
レコードをクリックして、フィールドを編集できます。

#### 5. データの削除
レコードを選択して、「Delete」ボタンをクリックして削除できます。

### 実践例: Prisma Studioでの操作

1. **ユーザーの作成**
   - 左側のサイドバーから「User」を選択
   - 「Add record」ボタンをクリック
   - フォームにデータを入力
   - 「Save 1 change」ボタンをクリック

2. **投稿の作成（リレーションを含む）**
   - 左側のサイドバーから「Post」を選択
   - 「Add record」ボタンをクリック
   - フォームにデータを入力
   - 「author」フィールドで既存のユーザーを選択
   - 「Save 1 change」ボタンをクリック

3. **データの検索**
   - 検索ボックスにキーワードを入力
   - フィルター機能を使用してデータを絞り込み

### 実践例: 開発用スクリプトの設定

```json
// package.json
{
  "scripts": {
    "db:studio": "prisma studio",
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev",
    "db:seed": "tsx prisma/seed.ts"
  }
}
```

```bash
# Prisma Studioを起動
npm run db:studio
```

---

## マイグレーションとシーディング (初期データ投入)

### マイグレーションとは
マイグレーションは、**データベーススキーマの変更を管理する仕組み**です。スキーマの変更を記録し、データベースに適用できます。

### マイグレーションの作成

```bash
# マイグレーションを作成（開発環境）
npx prisma migrate dev --name add_user_model
```

このコマンドを実行すると：
1. `prisma/migrations`ディレクトリにマイグレーションファイルが作成される
2. データベースにマイグレーションが適用される
3. Prisma Clientが自動的に再生成される

### マイグレーションの適用

```bash
# 本番環境でマイグレーションを適用
npx prisma migrate deploy
```

### マイグレーションのリセット（開発環境のみ）

```bash
# データベースをリセットして、すべてのマイグレーションを再適用
npx prisma migrate reset
```

⚠️ **注意**: このコマンドはデータベース内のすべてのデータを削除します。開発環境でのみ使用してください。

### シーディングとは
シーディングは、**データベースに初期データを投入する処理**です。開発やテストで使用するデータを簡単に投入できます。

### シーディングファイルの作成

```typescript
// prisma/seed.ts
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

async function main() {
  // ユーザーの作成
  const user1 = await prisma.user.upsert({
    where: { email: 'user1@example.com' },
    update: {},
    create: {
      email: 'user1@example.com',
      name: 'User 1',
      posts: {
        create: [
          {
            title: 'First Post',
            content: 'This is the first post',
            published: true,
          },
          {
            title: 'Second Post',
            content: 'This is the second post',
            published: false,
          },
        ],
      },
    },
  })

  const user2 = await prisma.user.upsert({
    where: { email: 'user2@example.com' },
    update: {},
    create: {
      email: 'user2@example.com',
      name: 'User 2',
      posts: {
        create: [
          {
            title: 'Third Post',
            content: 'This is the third post',
            published: true,
          },
        ],
      },
    },
  })

  // タグの作成
  const tag1 = await prisma.tag.upsert({
    where: { name: 'TypeScript' },
    update: {},
    create: {
      name: 'TypeScript',
    },
  })

  const tag2 = await prisma.tag.upsert({
    where: { name: 'Next.js' },
    update: {},
    create: {
      name: 'Next.js',
    },
  })

  console.log({ user1, user2, tag1, tag2 })
}

main()
  .catch((e) => {
    console.error(e)
    process.exit(1)
  })
  .finally(async () => {
    await prisma.$disconnect()
  })
```

### シーディングの実行

```bash
# シーディングを実行
npx prisma db seed
```

### package.jsonの設定

```json
// package.json
{
  "prisma": {
    "seed": "tsx prisma/seed.ts"
  },
  "scripts": {
    "db:seed": "prisma db seed"
  },
  "devDependencies": {
    "tsx": "^4.0.0"
  }
}
```

### 実践例: 完全なマイグレーションとシーディングのワークフロー

```bash
# 1. スキーマを変更
# prisma/schema.prisma を編集

# 2. マイグレーションを作成
npx prisma migrate dev --name add_new_field

# 3. シーディングを実行
npm run db:seed

# 4. Prisma Studioでデータを確認
npm run db:studio
```

### 実践例: 開発用スクリプトの設定

```json
// package.json
{
  "scripts": {
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev",
    "db:migrate:deploy": "prisma migrate deploy",
    "db:migrate:reset": "prisma migrate reset",
    "db:seed": "prisma db seed",
    "db:studio": "prisma studio",
    "db:setup": "prisma migrate dev && prisma db seed"
  }
}
```

```bash
# 開発環境のセットアップ（マイグレーション + シーディング）
npm run db:setup
```

---

## まとめ

この章では、ORMライブラリ「Prisma」の導入について学びました。

### 学んだこと
- Prisma schemaの概要と定義: データベースの構造を定義
- Prisma Clientの概要と生成: 型安全なデータベースクライアント
- DBビジュアルエディター「Prisma Studio」の使い方: データベースを視覚的に操作
- マイグレーションとシーディング: データベーススキーマの管理と初期データの投入

### 重要なポイント
1. **Prisma Schema**: データベースの構造を定義するファイル
2. **Prisma Client**: 型安全なデータベースクライアント
3. **Prisma Studio**: データベースを視覚的に操作できるGUIツール
4. **マイグレーション**: データベーススキーマの変更を管理
5. **シーディング**: 初期データを投入

### 次のステップ
次の章では、アプリケーションのルーティングについて詳しく学びます。

---

## 演習問題

### 問題1: Prisma Schemaの定義
UserモデルとPostモデルを含むPrisma Schemaを作成してください。Userは複数のPostを持つことができ、Postは1つのUserに属します。

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
  content   String
  authorId  Int
  author    User     @relation(fields: [authorId], references: [id])
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```
</details>

### 問題2: Prisma Clientの生成と使用
Prisma Clientを生成し、ユーザーを作成するコードを書いてください。

<details>
<summary>解答例</summary>

```bash
# Prisma Clientを生成
npx prisma generate
```

```typescript
// src/lib/prisma.ts
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

export default prisma
```

```typescript
// ユーザーを作成
import prisma from '@/lib/prisma'

const user = await prisma.user.create({
  data: {
    email: 'user@example.com',
    name: 'John Doe',
  },
})
```
</details>

### 問題3: Prisma Studioの起動
Prisma Studioを起動し、データベースの内容を確認してください。

<details>
<summary>解答例</summary>

```bash
# Prisma Studioを起動
npx prisma studio
```

ブラウザで `http://localhost:5555` が開きます。
</details>

### 問題4: マイグレーションの作成
Prisma Schemaを変更し、マイグレーションを作成してください。

<details>
<summary>解答例</summary>

```prisma
// prisma/schema.prisma に新しいフィールドを追加
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  age       Int?     // 新しいフィールド
  posts     Post[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

```bash
# マイグレーションを作成
npx prisma migrate dev --name add_age_to_user
```
</details>

### 問題5: シーディングの実装
シーディングファイルを作成し、初期データを投入してください。

<details>
<summary>解答例</summary>

```typescript
// prisma/seed.ts
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()

async function main() {
  const user = await prisma.user.upsert({
    where: { email: 'user@example.com' },
    update: {},
    create: {
      email: 'user@example.com',
      name: 'John Doe',
      posts: {
        create: [
          {
            title: 'First Post',
            content: 'This is the first post',
          },
        ],
      },
    },
  })

  console.log({ user })
}

main()
  .catch((e) => {
    console.error(e)
    process.exit(1)
  })
  .finally(async () => {
    await prisma.$disconnect()
  })
```

```json
// package.json
{
  "prisma": {
    "seed": "tsx prisma/seed.ts"
  }
}
```

```bash
# シーディングを実行
npx prisma db seed
```
</details>

---

お疲れ様でした！次の章に進みましょう。

