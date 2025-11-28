# 7-1. Parallel RoutesとIntercepting Routes

この章では、Next.jsの高度なルーティング機能であるParallel RoutesとIntercepting Routesについて学びます。これらの機能を理解することで、より柔軟で高度なルーティングを実装できるようになります。

---

## 目次

- [Parallel Routes (並行ルーティング) の概念](#parallel-routes-並行ルーティング-の概念)
- [Intercepting Routes (ルートの横取り) の概念](#intercepting-routes-ルートの横取り-の概念)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## Parallel Routes (並行ルーティング) の概念

### Parallel Routesとは
Parallel Routesは、**同じレイアウト内で複数のページを同時に表示する**機能です。`@folder`という命名規則を使用して、複数のスロットを定義し、それぞれに独立したルーティングを設定できます。

### 日常生活での例
- **ダッシュボード**: 複数のパネル（天気、ニュース、カレンダー）を同時に表示
- **テレビの分割画面**: 複数のチャンネルを同時に表示
- **デスクトップのウィンドウ**: 複数のアプリケーションを同時に表示

### Parallel Routesの基本構造

```
app/
├── layout.tsx
├── page.tsx
├── @analytics/
│   └── page.tsx
├── @team/
│   └── page.tsx
└── @feed/
    └── page.tsx
```

### 基本的なParallel Routes

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({
    children,
    analytics,
    team,
    feed,
}: {
    children: React.ReactNode;
    analytics: React.ReactNode;
    team: React.ReactNode;
    feed: React.ReactNode;
}) {
    return (
        <div>
            <h1>ダッシュボード</h1>
            <div className="dashboard-grid">
                <div className="analytics-slot">{analytics}</div>
                <div className="team-slot">{team}</div>
                <div className="feed-slot">{feed}</div>
            </div>
            {children}
        </div>
    );
}

// app/dashboard/@analytics/page.tsx
export default function Analytics() {
    return (
        <div>
            <h2>分析</h2>
            <p>訪問者数: 1,234</p>
        </div>
    );
}

// app/dashboard/@team/page.tsx
export default function Team() {
    return (
        <div>
            <h2>チーム</h2>
            <ul>
                <li>メンバー1</li>
                <li>メンバー2</li>
            </ul>
        </div>
    );
}

// app/dashboard/@feed/page.tsx
export default function Feed() {
    return (
        <div>
            <h2>フィード</h2>
            <ul>
                <li>更新1</li>
                <li>更新2</li>
            </ul>
        </div>
    );
}
```

### 実践例: Parallel Routes

#### ダッシュボードの実装

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({
    children,
    analytics,
    notifications,
    sidebar,
}: {
    children: React.ReactNode;
    analytics: React.ReactNode;
    notifications: React.ReactNode;
    sidebar: React.ReactNode;
}) {
    return (
        <div className="dashboard-layout">
            <aside className="sidebar">{sidebar}</aside>
            <main className="main-content">
                <div className="analytics-section">{analytics}</div>
                <div className="notifications-section">{notifications}</div>
                {children}
            </main>
        </div>
    );
}

// app/dashboard/@analytics/page.tsx
async function getAnalytics() {
    // データ取得処理
    return {
        visitors: 1234,
        pageViews: 5678,
        bounceRate: 45.5,
    };
}

export default async function Analytics() {
    const data = await getAnalytics();
    
    return (
        <div className="analytics-card">
            <h2>分析データ</h2>
            <div className="stats">
                <div>
                    <p>訪問者数</p>
                    <p className="stat-value">{data.visitors}</p>
                </div>
                <div>
                    <p>ページビュー</p>
                    <p className="stat-value">{data.pageViews}</p>
                </div>
                <div>
                    <p>直帰率</p>
                    <p className="stat-value">{data.bounceRate}%</p>
                </div>
            </div>
        </div>
    );
}

// app/dashboard/@notifications/page.tsx
async function getNotifications() {
    // データ取得処理
    return [
        { id: '1', message: '新しいメッセージ', time: '5分前' },
        { id: '2', message: 'タスクが完了', time: '10分前' },
    ];
}

export default async function Notifications() {
    const notifications = await getNotifications();
    
    return (
        <div className="notifications-card">
            <h2>通知</h2>
            <ul>
                {notifications.map(notification => (
                    <li key={notification.id}>
                        <p>{notification.message}</p>
                        <time>{notification.time}</time>
                    </li>
                ))}
            </ul>
        </div>
    );
}

// app/dashboard/@sidebar/page.tsx
export default function Sidebar() {
    return (
        <nav className="sidebar-nav">
            <ul>
                <li><a href="/dashboard">ダッシュボード</a></li>
                <li><a href="/dashboard/analytics">分析</a></li>
                <li><a href="/dashboard/notifications">通知</a></li>
                <li><a href="/dashboard/settings">設定</a></li>
            </ul>
        </nav>
    );
}
```

#### 条件付きスロット

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({
    children,
    analytics,
    team,
}: {
    children: React.ReactNode;
    analytics: React.ReactNode;
    team: React.ReactNode;
}) {
    return (
        <div>
            <h1>ダッシュボード</h1>
            <div className="dashboard-content">
                {analytics}
                {team}
            </div>
            {children}
        </div>
    );
}

// app/dashboard/@analytics/page.tsx
export default function Analytics() {
    return <div>分析データ</div>;
}

// app/dashboard/@analytics/default.tsx
export default function DefaultAnalytics() {
    return <div>分析データを読み込み中...</div>;
}

// app/dashboard/@team/page.tsx
export default function Team() {
    return <div>チーム情報</div>;
}

// app/dashboard/@team/default.tsx
export default function DefaultTeam() {
    return <div>チーム情報を読み込み中...</div>;
}
```

### スロットのルーティング

Parallel Routesでは、各スロットが独立したルーティングを持ちます。

```
app/
├── dashboard/
│   ├── layout.tsx
│   ├── @analytics/
│   │   ├── page.tsx          → /dashboard (analyticsスロット)
│   │   ├── settings/
│   │   │   └── page.tsx      → /dashboard/settings (analyticsスロット)
│   │   └── default.tsx
│   └── @team/
│       ├── page.tsx          → /dashboard (teamスロット)
│       ├── members/
│       │   └── page.tsx      → /dashboard/members (teamスロット)
│       └── default.tsx
```

```tsx
// app/dashboard/@analytics/settings/page.tsx
export default function AnalyticsSettings() {
    return (
        <div>
            <h2>分析設定</h2>
            <form>
                <label>
                    <input type="checkbox" /> 訪問者数を表示
                </label>
                <label>
                    <input type="checkbox" /> ページビューを表示
                </label>
            </form>
        </div>
    );
}

// app/dashboard/@team/members/page.tsx
export default function TeamMembers() {
    return (
        <div>
            <h2>チームメンバー</h2>
            <ul>
                <li>メンバー1</li>
                <li>メンバー2</li>
            </ul>
        </div>
    );
}
```

---

## Intercepting Routes (ルートの横取り) の概念

### Intercepting Routesとは
Intercepting Routesは、**同じルートを異なるコンテキストで表示する**機能です。`(.)folder`という命名規則を使用して、ルートを「横取り」し、モーダルやサイドバーなどで表示できます。

### 日常生活での例
- **モーダルウィンドウ**: ページ遷移せずに、同じ内容をモーダルで表示
- **サイドバー**: ページ遷移せずに、同じ内容をサイドバーで表示
- **プレビュー**: ページ遷移せずに、同じ内容をプレビューで表示

### Intercepting Routesの基本構造

```
app/
├── @modal/
│   └── (.)photo/
│       └── [id]/
│           └── page.tsx      → モーダルで表示
└── photo/
    └── [id]/
        └── page.tsx          → 通常のページとして表示
```

### 基本的なIntercepting Routes

```tsx
// app/@modal/default.tsx
export default function Default() {
    return null; // モーダルを表示しない
}

// app/@modal/(.)photo/[id]/page.tsx
'use client';

import { useRouter } from 'next/navigation';
import { useEffect, useRef } from 'react';

export default function PhotoModal({
    params,
}: {
    params: { id: string };
}) {
    const router = useRouter();
    const modalRef = useRef<HTMLDivElement>(null);
    
    useEffect(() => {
        const handleClickOutside = (e: MouseEvent) => {
            if (modalRef.current && !modalRef.current.contains(e.target as Node)) {
                router.back();
            }
        };
        
        document.addEventListener('mousedown', handleClickOutside);
        return () => document.removeEventListener('mousedown', handleClickOutside);
    }, [router]);
    
    return (
        <div className="modal-overlay">
            <div className="modal-content" ref={modalRef}>
                <button onClick={() => router.back()}>閉じる</button>
                <h2>写真 {params.id}</h2>
                <img src={`/photos/${params.id}.jpg`} alt={`写真 ${params.id}`} />
            </div>
        </div>
    );
}

// app/photo/[id]/page.tsx
export default function PhotoPage({
    params,
}: {
    params: { id: string };
}) {
    return (
        <div>
            <h1>写真 {params.id}</h1>
            <img src={`/photos/${params.id}.jpg`} alt={`写真 ${params.id}`} />
        </div>
    );
}
```

### Intercepting Routesのパターン

#### 1. 同じレベル `(.)`

```
app/
├── @modal/
│   └── (.)photo/
│       └── [id]/
│           └── page.tsx
└── photo/
    └── [id]/
        └── page.tsx
```

#### 2. 1レベル上 `(..)`

```
app/
├── @modal/
│   └── (..)photo/
│       └── [id]/
│           └── page.tsx
└── photo/
    └── [id]/
        └── page.tsx
```

#### 3. 2レベル上 `(..)(..)`

```
app/
├── @modal/
│   └── (..)(..)photo/
│       └── [id]/
│           └── page.tsx
└── photo/
    └── [id]/
        └── page.tsx
```

#### 4. ルートレベル `(...)`

```
app/
├── @modal/
│   └── (...photo)/
│       └── [id]/
│           └── page.tsx
└── photo/
    └── [id]/
        └── page.tsx
```

### 実践例: Intercepting Routes

#### モーダルでの写真表示

```tsx
// app/layout.tsx
export default function RootLayout({
    children,
    modal,
}: {
    children: React.ReactNode;
    modal: React.ReactNode;
}) {
    return (
        <html>
            <body>
                {children}
                {modal}
            </body>
        </html>
    );
}

// app/@modal/default.tsx
export default function Default() {
    return null;
}

// app/@modal/(.)photo/[id]/page.tsx
'use client';

import { useRouter } from 'next/navigation';
import { useEffect, useRef } from 'react';

interface PhotoModalProps {
    params: { id: string };
}

export default function PhotoModal({ params }: PhotoModalProps) {
    const router = useRouter();
    const modalRef = useRef<HTMLDivElement>(null);
    
    useEffect(() => {
        const handleEscape = (e: KeyboardEvent) => {
            if (e.key === 'Escape') {
                router.back();
            }
        };
        
        const handleClickOutside = (e: MouseEvent) => {
            if (modalRef.current && !modalRef.current.contains(e.target as Node)) {
                router.back();
            }
        };
        
        document.addEventListener('keydown', handleEscape);
        document.addEventListener('mousedown', handleClickOutside);
        
        return () => {
            document.removeEventListener('keydown', handleEscape);
            document.removeEventListener('mousedown', handleClickOutside);
        };
    }, [router]);
    
    return (
        <div className="modal-overlay">
            <div className="modal-content" ref={modalRef}>
                <button onClick={() => router.back()} className="close-button">
                    ×
                </button>
                <h2>写真 {params.id}</h2>
                <img
                    src={`/photos/${params.id}.jpg`}
                    alt={`写真 ${params.id}`}
                    className="modal-image"
                />
            </div>
        </div>
    );
}

// app/photo/[id]/page.tsx
interface PhotoPageProps {
    params: { id: string };
}

export default function PhotoPage({ params }: PhotoPageProps) {
    return (
        <div>
            <h1>写真 {params.id}</h1>
            <img
                src={`/photos/${params.id}.jpg`}
                alt={`写真 ${params.id}`}
            />
        </div>
    );
}

// app/photo/page.tsx
interface Photo {
    id: string;
    title: string;
    url: string;
}

async function getPhotos(): Promise<Photo[]> {
    // データ取得処理
    return [
        { id: '1', title: '写真1', url: '/photos/1.jpg' },
        { id: '2', title: '写真2', url: '/photos/2.jpg' },
    ];
}

export default async function PhotosPage() {
    const photos = await getPhotos();
    
    return (
        <div>
            <h1>写真一覧</h1>
            <div className="photo-grid">
                {photos.map(photo => (
                    <a key={photo.id} href={`/photo/${photo.id}`}>
                        <img src={photo.url} alt={photo.title} />
                        <p>{photo.title}</p>
                    </a>
                ))}
            </div>
        </div>
    );
}
```

#### サイドバーでの詳細表示

```tsx
// app/layout.tsx
export default function RootLayout({
    children,
    sidebar,
}: {
    children: React.ReactNode;
    sidebar: React.ReactNode;
}) {
    return (
        <html>
            <body>
                <div className="layout-container">
                    <main>{children}</main>
                    <aside>{sidebar}</aside>
                </div>
            </body>
        </html>
    );
}

// app/@sidebar/default.tsx
export default function Default() {
    return null;
}

// app/@sidebar/(.)product/[id]/page.tsx
interface ProductSidebarProps {
    params: { id: string };
}

async function getProduct(id: string) {
    // データ取得処理
    return {
        id,
        name: '商品名',
        price: 1000,
        description: '商品説明',
    };
}

export default async function ProductSidebar({
    params,
}: ProductSidebarProps) {
    const product = await getProduct(params.id);
    
    return (
        <div className="sidebar-content">
            <h2>{product.name}</h2>
            <p>価格: ¥{product.price}</p>
            <p>{product.description}</p>
        </div>
    );
}

// app/product/[id]/page.tsx
interface ProductPageProps {
    params: { id: string };
}

async function getProduct(id: string) {
    // データ取得処理
    return {
        id,
        name: '商品名',
        price: 1000,
        description: '商品説明',
    };
}

export default async function ProductPage({ params }: ProductPageProps) {
    const product = await getProduct(params.id);
    
    return (
        <div>
            <h1>{product.name}</h1>
            <p>価格: ¥{product.price}</p>
            <p>{product.description}</p>
        </div>
    );
}
```

### Parallel RoutesとIntercepting Routesの組み合わせ

```tsx
// app/layout.tsx
export default function RootLayout({
    children,
    modal,
    sidebar,
}: {
    children: React.ReactNode;
    modal: React.ReactNode;
    sidebar: React.ReactNode;
}) {
    return (
        <html>
            <body>
                <div className="layout-container">
                    <main>{children}</main>
                    <aside>{sidebar}</aside>
                </div>
                {modal}
            </body>
        </html>
    );
}

// app/@modal/(.)photo/[id]/page.tsx
'use client';

import { useRouter } from 'next/navigation';

export default function PhotoModal({
    params,
}: {
    params: { id: string };
}) {
    const router = useRouter();
    
    return (
        <div className="modal-overlay">
            <div className="modal-content">
                <button onClick={() => router.back()}>閉じる</button>
                <h2>写真 {params.id}</h2>
            </div>
        </div>
    );
}

// app/@sidebar/(.)product/[id]/page.tsx
export default function ProductSidebar({
    params,
}: {
    params: { id: string };
}) {
    return (
        <div className="sidebar-content">
            <h2>商品 {params.id}</h2>
        </div>
    );
}
```

---

## まとめ

この章では、Parallel RoutesとIntercepting Routesについて学びました。

### 学んだこと
- **Parallel Routes**: 同じレイアウト内で複数のページを同時に表示
- **Intercepting Routes**: 同じルートを異なるコンテキストで表示
- **スロット**: `@folder`でスロットを定義
- **ルートの横取り**: `(.)folder`でルートを横取り
- **組み合わせ**: Parallel RoutesとIntercepting Routesを組み合わせて使用

### 重要なポイント
1. **Parallel Routes**: `@folder`でスロットを定義し、複数のページを同時に表示
2. **Intercepting Routes**: `(.)folder`でルートを横取りし、モーダルやサイドバーで表示
3. **default.tsx**: スロットがマッチしない場合のフォールバック
4. **ルーティング**: 各スロットが独立したルーティングを持つ
5. **組み合わせ**: 両方の機能を組み合わせて、より柔軟なUIを実現

### 使い分けの目安
- **Parallel Routes**: ダッシュボードのように複数の情報を同時に表示したい場合
- **Intercepting Routes**: モーダルやサイドバーで同じ内容を表示したい場合

### 次のステップ
Parallel RoutesとIntercepting Routesを理解することで、より柔軟で高度なルーティングを実装できるようになります。次の章では、モーダルとルーティングの連携について詳しく学びます。

---

## 演習問題

### 問題1: Parallel Routesの実装
ダッシュボードで、分析、通知、サイドバーの3つのスロットを同時に表示するParallel Routesを実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({
    children,
    analytics,
    notifications,
    sidebar,
}: {
    children: React.ReactNode;
    analytics: React.ReactNode;
    notifications: React.ReactNode;
    sidebar: React.ReactNode;
}) {
    return (
        <div>
            <aside>{sidebar}</aside>
            <main>
                <div>{analytics}</div>
                <div>{notifications}</div>
                {children}
            </main>
        </div>
    );
}

// app/dashboard/@analytics/page.tsx
export default function Analytics() {
    return <div>分析データ</div>;
}

// app/dashboard/@notifications/page.tsx
export default function Notifications() {
    return <div>通知</div>;
}

// app/dashboard/@sidebar/page.tsx
export default function Sidebar() {
    return <nav>サイドバー</nav>;
}
```
</details>

### 問題2: Intercepting Routesの実装
写真一覧ページから写真をクリックしたときに、モーダルで写真を表示するIntercepting Routesを実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/layout.tsx
export default function RootLayout({
    children,
    modal,
}: {
    children: React.ReactNode;
    modal: React.ReactNode;
}) {
    return (
        <html>
            <body>
                {children}
                {modal}
            </body>
        </html>
    );
}

// app/@modal/default.tsx
export default function Default() {
    return null;
}

// app/@modal/(.)photo/[id]/page.tsx
'use client';

import { useRouter } from 'next/navigation';

export default function PhotoModal({
    params,
}: {
    params: { id: string };
}) {
    const router = useRouter();
    
    return (
        <div className="modal-overlay">
            <div className="modal-content">
                <button onClick={() => router.back()}>閉じる</button>
                <h2>写真 {params.id}</h2>
            </div>
        </div>
    );
}

// app/photo/[id]/page.tsx
export default function PhotoPage({
    params,
}: {
    params: { id: string };
}) {
    return (
        <div>
            <h1>写真 {params.id}</h1>
        </div>
    );
}
```
</details>

### 問題3: default.tsxの実装
Parallel Routesで、スロットがマッチしない場合のフォールバックとして`default.tsx`を実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/dashboard/@analytics/default.tsx
export default function DefaultAnalytics() {
    return <div>分析データを読み込み中...</div>;
}

// app/dashboard/@notifications/default.tsx
export default function DefaultNotifications() {
    return <div>通知を読み込み中...</div>;
}
```
</details>

### 問題4: スロットのルーティング
Parallel Routesで、各スロットが独立したルーティングを持つように実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/dashboard/@analytics/settings/page.tsx
export default function AnalyticsSettings() {
    return <div>分析設定</div>;
}

// app/dashboard/@team/members/page.tsx
export default function TeamMembers() {
    return <div>チームメンバー</div>;
}
```
</details>

### 問題5: Parallel RoutesとIntercepting Routesの組み合わせ
Parallel RoutesとIntercepting Routesを組み合わせて、モーダルとサイドバーを同時に表示できるように実装してください。

<details>
<summary>解答例</summary>

```tsx
// app/layout.tsx
export default function RootLayout({
    children,
    modal,
    sidebar,
}: {
    children: React.ReactNode;
    modal: React.ReactNode;
    sidebar: React.ReactNode;
}) {
    return (
        <html>
            <body>
                <div>
                    <main>{children}</main>
                    <aside>{sidebar}</aside>
                </div>
                {modal}
            </body>
        </html>
    );
}

// app/@modal/(.)photo/[id]/page.tsx
'use client';

import { useRouter } from 'next/navigation';

export default function PhotoModal({
    params,
}: {
    params: { id: string };
}) {
    const router = useRouter();
    return (
        <div className="modal">
            <button onClick={() => router.back()}>閉じる</button>
            <h2>写真 {params.id}</h2>
        </div>
    );
}

// app/@sidebar/(.)product/[id]/page.tsx
export default function ProductSidebar({
    params,
}: {
    params: { id: string };
}) {
    return (
        <div className="sidebar">
            <h2>商品 {params.id}</h2>
        </div>
    );
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

