# 9-2. API連携とデータフェッチ（データフェッチライブラリ）

この章では、Reactアプリケーションでデータフェッチを効率的に行うためのライブラリ「SWR」と「React Query」について学びます。これらのライブラリを理解することで、キャッシュ機能や自動再検証を活用し、より効率的で保守しやすいコードを書けるようになります。

---

## 目次

- [キャッシュ機能と自動再検証](#キャッシュ機能と自動再検証)
- [カスタムフック化によるロジック分離](#カスタムフック化によるロジック分離)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## キャッシュ機能と自動再検証

### データフェッチライブラリとは
データフェッチライブラリは、**APIからデータを取得する処理を効率化する**ライブラリです。キャッシュ機能、自動再検証、エラーハンドリングなどを提供し、開発者がデータフェッチに集中できるようにします。

### 主なライブラリ
1. **SWR (stale-while-revalidate)**: Vercelが開発した軽量なデータフェッチライブラリ
2. **React Query (TanStack Query)**: より高機能なデータフェッチライブラリ

### 日常生活での例
- **図書館の予約システム**: 一度借りた本の情報（キャッシュ）を保存し、新しい情報（再検証）を定期的に確認する
- **天気予報アプリ**: 過去の天気情報（キャッシュ）を表示しながら、最新の情報（再検証）を取得する
- **ニュースアプリ**: 既に読んだ記事（キャッシュ）を表示しながら、新しい記事（再検証）を取得する

### SWRとは
SWRは、**stale-while-revalidate（古いデータを表示しながら再検証する）**という戦略を採用したデータフェッチライブラリです。軽量で使いやすく、自動的なキャッシュと再検証を提供します。

### SWRのインストール

```bash
npm install swr
```

### 実践例: SWRの基本使用

```jsx
// src/hooks/useUsers.js
import useSWR from 'swr';

// フェッチャー関数
const fetcher = async (url) => {
    const response = await fetch(url);
    if (!response.ok) {
        throw new Error('データの取得に失敗しました');
    }
    return response.json();
};

// カスタムフック
export function useUsers() {
    const { data, error, isLoading } = useSWR('/api/users', fetcher);

    return {
        users: data,
        isLoading,
        isError: error
    };
}

// src/components/UserList.jsx
import { useUsers } from '../hooks/useUsers';

function UserList() {
    const { users, isLoading, isError } = useUsers();

    if (isLoading) {
        return <p>読み込み中...</p>;
    }

    if (isError) {
        return <p>エラーが発生しました</p>;
    }

    return (
        <div>
            <h2>ユーザー一覧</h2>
            <ul>
                {users?.map(user => (
                    <li key={user.id}>
                        {user.name} ({user.email})
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default UserList;
```

### SWRのキャッシュ機能

```jsx
// src/components/UserProfile.jsx
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

function UserProfile({ userId }) {
    // 同じキーで複数のコンポーネントがデータを取得すると、キャッシュが共有される
    const { data: user, error, isLoading } = useSWR(
        `/api/users/${userId}`,
        fetcher
    );

    if (isLoading) return <p>読み込み中...</p>;
    if (error) return <p>エラーが発生しました</p>;

    return (
        <div>
            <h2>{user.name}</h2>
            <p>メール: {user.email}</p>
        </div>
    );
}

// src/components/UserCard.jsx
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

function UserCard({ userId }) {
    // 同じキーを使用しているため、キャッシュが共有される
    const { data: user } = useSWR(`/api/users/${userId}`, fetcher);

    return (
        <div>
            <h3>{user?.name}</h3>
        </div>
    );
}

// src/App.jsx
function App() {
    return (
        <div>
            <UserProfile userId={1} />
            <UserCard userId={1} /> {/* キャッシュが共有される */}
        </div>
    );
}

export default App;
```

### SWRの自動再検証

```jsx
// src/components/ProductList.jsx
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

function ProductList() {
    // 自動的に再検証される
    const { data: products, error, isLoading } = useSWR(
        '/api/products',
        fetcher,
        {
            // フォーカス時に再検証（デフォルト: true）
            revalidateOnFocus: true,
            // 再接続時に再検証（デフォルト: true）
            revalidateOnReconnect: true,
            // インターバルで再検証（5秒ごと）
            refreshInterval: 5000
        }
    );

    if (isLoading) return <p>読み込み中...</p>;
    if (error) return <p>エラーが発生しました</p>;

    return (
        <div>
            <h2>商品一覧</h2>
            <ul>
                {products?.map(product => (
                    <li key={product.id}>
                        {product.name} - ¥{product.price.toLocaleString()}
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default ProductList;
```

### React Queryとは
React Query（TanStack Query）は、**より高機能なデータフェッチライブラリ**です。SWRよりも多くの機能を提供し、複雑なデータフェッチのシナリオに対応できます。

### React Queryのインストール

```bash
npm install @tanstack/react-query
```

### 実践例: React Queryの基本使用

```jsx
// src/main.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import App from './App';

// QueryClientの作成
const queryClient = new QueryClient({
    defaultOptions: {
        queries: {
            refetchOnWindowFocus: false, // フォーカス時の再検証を無効化
            retry: 1, // リトライ回数
            staleTime: 5 * 60 * 1000 // 5分間は新鮮とみなす
        }
    }
});

ReactDOM.createRoot(document.getElementById('root')).render(
    <React.StrictMode>
        <QueryClientProvider client={queryClient}>
            <App />
        </QueryClientProvider>
    </React.StrictMode>
);

// src/hooks/useUsers.js
import { useQuery } from '@tanstack/react-query';

const fetchUsers = async () => {
    const response = await fetch('/api/users');
    if (!response.ok) {
        throw new Error('データの取得に失敗しました');
    }
    return response.json();
};

export function useUsers() {
    return useQuery({
        queryKey: ['users'],
        queryFn: fetchUsers
    });
}

// src/components/UserList.jsx
import { useUsers } from '../hooks/useUsers';

function UserList() {
    const { data: users, isLoading, isError, error } = useUsers();

    if (isLoading) {
        return <p>読み込み中...</p>;
    }

    if (isError) {
        return <p>エラー: {error.message}</p>;
    }

    return (
        <div>
            <h2>ユーザー一覧</h2>
            <ul>
                {users?.map(user => (
                    <li key={user.id}>
                        {user.name} ({user.email})
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default UserList;
```

### React Queryのキャッシュ機能

```jsx
// src/hooks/useUser.js
import { useQuery } from '@tanstack/react-query';

const fetchUser = async (userId) => {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) {
        throw new Error('データの取得に失敗しました');
    }
    return response.json();
};

export function useUser(userId) {
    return useQuery({
        queryKey: ['user', userId], // キャッシュキー
        queryFn: () => fetchUser(userId),
        enabled: !!userId, // userIdが存在する場合のみ実行
        staleTime: 5 * 60 * 1000 // 5分間は新鮮とみなす
    });
}

// src/components/UserProfile.jsx
import { useUser } from '../hooks/useUser';

function UserProfile({ userId }) {
    const { data: user, isLoading, isError } = useUser(userId);

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) return <p>エラーが発生しました</p>;

    return (
        <div>
            <h2>{user.name}</h2>
            <p>メール: {user.email}</p>
        </div>
    );
}

// src/components/UserCard.jsx
import { useUser } from '../hooks/useUser';

function UserCard({ userId }) {
    // 同じqueryKeyを使用しているため、キャッシュが共有される
    const { data: user } = useUser(userId);

    return (
        <div>
            <h3>{user?.name}</h3>
        </div>
    );
}
```

### React Queryの自動再検証

```jsx
// src/hooks/useProducts.js
import { useQuery } from '@tanstack/react-query';

const fetchProducts = async () => {
    const response = await fetch('/api/products');
    if (!response.ok) {
        throw new Error('データの取得に失敗しました');
    }
    return response.json();
};

export function useProducts() {
    return useQuery({
        queryKey: ['products'],
        queryFn: fetchProducts,
        // 自動再検証の設定
        refetchOnWindowFocus: true, // フォーカス時に再検証
        refetchOnReconnect: true, // 再接続時に再検証
        refetchInterval: 5000, // 5秒ごとに再検証
        staleTime: 0 // 常に古いデータとみなす（再検証を促す）
    });
}

// src/components/ProductList.jsx
import { useProducts } from '../hooks/useProducts';

function ProductList() {
    const { data: products, isLoading, isError } = useProducts();

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) return <p>エラーが発生しました</p>;

    return (
        <div>
            <h2>商品一覧</h2>
            <ul>
                {products?.map(product => (
                    <li key={product.id}>
                        {product.name} - ¥{product.price.toLocaleString()}
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default ProductList;
```

### 手動での再検証

```jsx
// src/components/UserList.jsx
import { useUsers } from '../hooks/useUsers';
import { useQueryClient } from '@tanstack/react-query';

function UserList() {
    const { data: users, isLoading, isError } = useUsers();
    const queryClient = useQueryClient();

    const handleRefresh = () => {
        // 手動で再検証
        queryClient.invalidateQueries({ queryKey: ['users'] });
    };

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) return <p>エラーが発生しました</p>;

    return (
        <div>
            <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center' }}>
                <h2>ユーザー一覧</h2>
                <button onClick={handleRefresh}>更新</button>
            </div>
            <ul>
                {users?.map(user => (
                    <li key={user.id}>
                        {user.name} ({user.email})
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default UserList;
```

---

## カスタムフック化によるロジック分離

### カスタムフックとは
カスタムフックは、**データフェッチのロジックをコンポーネントから分離する**ためのフックです。同じロジックを複数のコンポーネントで再利用でき、コードの保守性が向上します。

### カスタムフックのメリット
1. **再利用性**: 同じロジックを複数のコンポーネントで使用できる
2. **保守性**: ロジックを1箇所に集約できる
3. **テスト容易性**: ロジックを独立してテストできる
4. **可読性**: コンポーネントがシンプルになる

### 実践例: SWRを使ったカスタムフック

```jsx
// src/hooks/useUser.js
import useSWR from 'swr';

const fetcher = async (url) => {
    const response = await fetch(url);
    if (!response.ok) {
        throw new Error('データの取得に失敗しました');
    }
    return response.json();
};

export function useUser(userId) {
    const { data, error, isLoading, mutate } = useSWR(
        userId ? `/api/users/${userId}` : null,
        fetcher
    );

    return {
        user: data,
        isLoading,
        isError: error,
        mutate // 手動で再検証する関数
    };
}

// src/components/UserProfile.jsx
import { useUser } from '../hooks/useUser';

function UserProfile({ userId }) {
    const { user, isLoading, isError, mutate } = useUser(userId);

    const handleRefresh = () => {
        mutate(); // 手動で再検証
    };

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) return <p>エラーが発生しました</p>;

    return (
        <div>
            <div style={{ display: 'flex', justifyContent: 'space-between' }}>
                <h2>{user.name}</h2>
                <button onClick={handleRefresh}>更新</button>
            </div>
            <p>メール: {user.email}</p>
            <p>年齢: {user.age}歳</p>
        </div>
    );
}

export default UserProfile;
```

### 実践例: React Queryを使ったカスタムフック

```jsx
// src/hooks/useUser.js
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

const fetchUser = async (userId) => {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) {
        throw new Error('データの取得に失敗しました');
    }
    return response.json();
};

const updateUser = async ({ userId, data }) => {
    const response = await fetch(`/api/users/${userId}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data)
    });
    if (!response.ok) {
        throw new Error('更新に失敗しました');
    }
    return response.json();
};

export function useUser(userId) {
    return useQuery({
        queryKey: ['user', userId],
        queryFn: () => fetchUser(userId),
        enabled: !!userId
    });
}

export function useUpdateUser() {
    const queryClient = useQueryClient();

    return useMutation({
        mutationFn: updateUser,
        onSuccess: (data, variables) => {
            // 更新成功後、キャッシュを更新
            queryClient.setQueryData(['user', variables.userId], data);
            // 関連するクエリを無効化
            queryClient.invalidateQueries({ queryKey: ['users'] });
        }
    });
}

// src/components/UserProfile.jsx
import { useState } from 'react';
import { useUser, useUpdateUser } from '../hooks/useUser';

function UserProfile({ userId }) {
    const { data: user, isLoading, isError } = useUser(userId);
    const updateUser = useUpdateUser();
    const [name, setName] = useState('');

    const handleUpdate = () => {
        updateUser.mutate({
            userId,
            data: { name }
        });
    };

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) return <p>エラーが発生しました</p>;

    return (
        <div>
            <h2>{user.name}</h2>
            <p>メール: {user.email}</p>
            <div style={{ marginTop: '16px' }}>
                <input
                    type="text"
                    value={name}
                    onChange={(e) => setName(e.target.value)}
                    placeholder="新しい名前"
                />
                <button onClick={handleUpdate} disabled={updateUser.isPending}>
                    {updateUser.isPending ? '更新中...' : '更新'}
                </button>
            </div>
        </div>
    );
}

export default UserProfile;
```

### 実践例: 複数のカスタムフック

```jsx
// src/hooks/useProducts.js
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

export function useProducts(category = null) {
    const url = category ? `/api/products?category=${category}` : '/api/products';
    const { data, error, isLoading, mutate } = useSWR(url, fetcher);

    return {
        products: data,
        isLoading,
        isError: error,
        refresh: mutate
    };
}

// src/hooks/useProduct.js
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

export function useProduct(productId) {
    const { data, error, isLoading, mutate } = useSWR(
        productId ? `/api/products/${productId}` : null,
        fetcher
    );

    return {
        product: data,
        isLoading,
        isError: error,
        refresh: mutate
    };
}

// src/components/ProductList.jsx
import { useState } from 'react';
import { useProducts } from '../hooks/useProducts';

function ProductList() {
    const [category, setCategory] = useState(null);
    const { products, isLoading, isError, refresh } = useProducts(category);

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) return <p>エラーが発生しました</p>;

    return (
        <div>
            <div style={{ marginBottom: '16px' }}>
                <button onClick={() => setCategory(null)}>すべて</button>
                <button onClick={() => setCategory('electronics')}>電子機器</button>
                <button onClick={() => setCategory('furniture')}>家具</button>
                <button onClick={refresh}>更新</button>
            </div>
            <ul>
                {products?.map(product => (
                    <li key={product.id}>
                        {product.name} - ¥{product.price.toLocaleString()}
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default ProductList;
```

### 実践例: React Queryを使った複数のカスタムフック

```jsx
// src/hooks/useProducts.js
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

const fetchProducts = async (category = null) => {
    const url = category ? `/api/products?category=${category}` : '/api/products';
    const response = await fetch(url);
    if (!response.ok) {
        throw new Error('データの取得に失敗しました');
    }
    return response.json();
};

export function useProducts(category = null) {
    return useQuery({
        queryKey: ['products', category],
        queryFn: () => fetchProducts(category)
    });
}

const createProduct = async (data) => {
    const response = await fetch('/api/products', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data)
    });
    if (!response.ok) {
        throw new Error('作成に失敗しました');
    }
    return response.json();
};

export function useCreateProduct() {
    const queryClient = useQueryClient();

    return useMutation({
        mutationFn: createProduct,
        onSuccess: () => {
            // 商品一覧のキャッシュを無効化
            queryClient.invalidateQueries({ queryKey: ['products'] });
        }
    });
}

// src/components/ProductList.jsx
import { useState } from 'react';
import { useProducts, useCreateProduct } from '../hooks/useProducts';

function ProductList() {
    const [category, setCategory] = useState(null);
    const { data: products, isLoading, isError } = useProducts(category);
    const createProduct = useCreateProduct();

    const handleCreate = () => {
        createProduct.mutate({
            name: '新しい商品',
            price: 1000,
            category: category || 'electronics'
        });
    };

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) return <p>エラーが発生しました</p>;

    return (
        <div>
            <div style={{ marginBottom: '16px' }}>
                <button onClick={() => setCategory(null)}>すべて</button>
                <button onClick={() => setCategory('electronics')}>電子機器</button>
                <button onClick={() => setCategory('furniture')}>家具</button>
                <button onClick={handleCreate} disabled={createProduct.isPending}>
                    {createProduct.isPending ? '作成中...' : '商品を作成'}
                </button>
            </div>
            <ul>
                {products?.map(product => (
                    <li key={product.id}>
                        {product.name} - ¥{product.price.toLocaleString()}
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default ProductList;
```

### 実践例: エラーハンドリングを含むカスタムフック

```jsx
// src/hooks/useUser.js
import { useQuery } from '@tanstack/react-query';

const fetchUser = async (userId) => {
    const response = await fetch(`/api/users/${userId}`);
    
    if (response.status === 404) {
        throw new Error('ユーザーが見つかりません');
    }
    
    if (!response.ok) {
        throw new Error('データの取得に失敗しました');
    }
    
    return response.json();
};

export function useUser(userId) {
    return useQuery({
        queryKey: ['user', userId],
        queryFn: () => fetchUser(userId),
        enabled: !!userId,
        retry: (failureCount, error) => {
            // 404エラーの場合はリトライしない
            if (error.message === 'ユーザーが見つかりません') {
                return false;
            }
            // その他のエラーは3回までリトライ
            return failureCount < 3;
        },
        retryDelay: (attemptIndex) => Math.min(1000 * 2 ** attemptIndex, 30000)
    });
}

// src/components/UserProfile.jsx
import { useUser } from '../hooks/useUser';

function UserProfile({ userId }) {
    const { data: user, isLoading, isError, error } = useUser(userId);

    if (isLoading) {
        return <p>読み込み中...</p>;
    }

    if (isError) {
        return (
            <div>
                <p style={{ color: 'red' }}>エラー: {error.message}</p>
                {error.message === 'ユーザーが見つかりません' && (
                    <p>ユーザーIDを確認してください</p>
                )}
            </div>
        );
    }

    return (
        <div>
            <h2>{user.name}</h2>
            <p>メール: {user.email}</p>
        </div>
    );
}

export default UserProfile;
```

---

## まとめ

この章では、データフェッチライブラリについて以下のことを学びました。

### キャッシュ機能と自動再検証
- **SWR**: 軽量で使いやすいデータフェッチライブラリ。stale-while-revalidate戦略を採用
- **React Query**: より高機能なデータフェッチライブラリ。複雑なシナリオに対応
- **キャッシュ機能**: 同じデータを複数のコンポーネントで共有できる
- **自動再検証**: フォーカス時、再接続時、インターバルで自動的にデータを更新
- **手動再検証**: 必要に応じて手動でデータを更新できる

### カスタムフック化によるロジック分離
- **再利用性**: 同じロジックを複数のコンポーネントで使用できる
- **保守性**: ロジックを1箇所に集約できる
- **テスト容易性**: ロジックを独立してテストできる
- **可読性**: コンポーネントがシンプルになる
- **エラーハンドリング**: カスタムフック内でエラーハンドリングを統一できる

### データフェッチライブラリのメリット
- **パフォーマンス**: キャッシュにより、不要なAPI呼び出しを削減
- **ユーザー体験**: 古いデータを即座に表示し、バックグラウンドで更新
- **開発効率**: データフェッチのロジックを簡潔に書ける
- **保守性**: ロジックを分離し、管理しやすくなる

### 次のステップ
データフェッチライブラリを理解したら、次のトピックに進みましょう：
- React × TypeScript 実践（10章）
- テスト（11章）
- 実務レベルのSPAアーキテクチャ構築（12章）

---

## 演習問題

以下の問題を解いて、データフェッチライブラリの理解を深めましょう。

### 問題1: SWRを使った基本的なデータ取得
SWRを使って、ユーザー一覧を取得するカスタムフックを作成してください。

<details>
<summary>解答例</summary>

```jsx
// src/hooks/useUsers.js
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

export function useUsers() {
    const { data, error, isLoading } = useSWR('/api/users', fetcher);

    return {
        users: data,
        isLoading,
        isError: error
    };
}

// src/components/UserList.jsx
import { useUsers } from '../hooks/useUsers';

function UserList() {
    const { users, isLoading, isError } = useUsers();

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) return <p>エラーが発生しました</p>;

    return (
        <ul>
            {users?.map(user => (
                <li key={user.id}>{user.name}</li>
            ))}
        </ul>
    );
}

export default UserList;
```
</details>

### 問題2: React Queryを使ったデータ取得
React Queryを使って、商品一覧を取得するカスタムフックを作成してください。

<details>
<summary>解答例</summary>

```jsx
// src/hooks/useProducts.js
import { useQuery } from '@tanstack/react-query';

const fetchProducts = async () => {
    const response = await fetch('/api/products');
    if (!response.ok) {
        throw new Error('データの取得に失敗しました');
    }
    return response.json();
};

export function useProducts() {
    return useQuery({
        queryKey: ['products'],
        queryFn: fetchProducts
    });
}

// src/components/ProductList.jsx
import { useProducts } from '../hooks/useProducts';

function ProductList() {
    const { data: products, isLoading, isError } = useProducts();

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) return <p>エラーが発生しました</p>;

    return (
        <ul>
            {products?.map(product => (
                <li key={product.id}>{product.name}</li>
            ))}
        </ul>
    );
}

export default ProductList;
```
</details>

### 問題3: 自動再検証の設定
SWRを使って、5秒ごとに自動的にデータを再検証する設定を追加してください。

<details>
<summary>解答例</summary>

```jsx
// src/hooks/useProducts.js
import useSWR from 'swr';

const fetcher = (url) => fetch(url).then(res => res.json());

export function useProducts() {
    const { data, error, isLoading } = useSWR('/api/products', fetcher, {
        refreshInterval: 5000 // 5秒ごとに再検証
    });

    return {
        products: data,
        isLoading,
        isError: error
    };
}

export default useProducts;
```
</details>

### 問題4: カスタムフックでのエラーハンドリング
React Queryを使って、エラーハンドリングを含むカスタムフックを作成してください。

<details>
<summary>解答例</summary>

```jsx
// src/hooks/useUser.js
import { useQuery } from '@tanstack/react-query';

const fetchUser = async (userId) => {
    const response = await fetch(`/api/users/${userId}`);
    if (response.status === 404) {
        throw new Error('ユーザーが見つかりません');
    }
    if (!response.ok) {
        throw new Error('データの取得に失敗しました');
    }
    return response.json();
};

export function useUser(userId) {
    return useQuery({
        queryKey: ['user', userId],
        queryFn: () => fetchUser(userId),
        enabled: !!userId,
        retry: (failureCount, error) => {
            if (error.message === 'ユーザーが見つかりません') {
                return false;
            }
            return failureCount < 3;
        }
    });
}

// src/components/UserProfile.jsx
import { useUser } from '../hooks/useUser';

function UserProfile({ userId }) {
    const { data: user, isLoading, isError, error } = useUser(userId);

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) {
        return <p>エラー: {error.message}</p>;
    }

    return <div>{user.name}</div>;
}

export default UserProfile;
```
</details>

### 問題5: 手動での再検証
React Queryを使って、手動でデータを再検証する機能を実装してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/ProductList.jsx
import { useProducts } from '../hooks/useProducts';
import { useQueryClient } from '@tanstack/react-query';

function ProductList() {
    const { data: products, isLoading, isError } = useProducts();
    const queryClient = useQueryClient();

    const handleRefresh = () => {
        queryClient.invalidateQueries({ queryKey: ['products'] });
    };

    if (isLoading) return <p>読み込み中...</p>;
    if (isError) return <p>エラーが発生しました</p>;

    return (
        <div>
            <button onClick={handleRefresh}>更新</button>
            <ul>
                {products?.map(product => (
                    <li key={product.id}>{product.name}</li>
                ))}
            </ul>
        </div>
    );
}

export default ProductList;
```
</details>

---

お疲れ様でした！次の章に進みましょう。

