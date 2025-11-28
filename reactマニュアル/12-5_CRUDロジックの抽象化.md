# 12-5. CRUDロジックの抽象化

この章では、CRUD（Create, Read, Update, Delete）操作のロジックを抽象化し、再利用可能なデータアクセス層を設計する方法について学びます。CRUDロジックを抽象化することで、コードの重複を減らし、保守性と拡張性を向上させることができます。

---

## 目次

- [再利用可能なデータアクセス層の設計](#再利用可能なデータアクセス層の設計)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## 再利用可能なデータアクセス層の設計

### CRUDロジックの抽象化とは
CRUDロジックの抽象化は、**Create（作成）、Read（読み取り）、Update（更新）、Delete（削除）の操作を共通化し、再利用可能にする**設計パターンです。

### 日常生活での例
- **図書館のカードシステム**: 本の貸出・返却・検索・登録を統一的な方法で管理
- **在庫管理システム**: 商品の追加・検索・更新・削除を統一的な方法で管理
- **顧客管理システム**: 顧客情報の登録・検索・更新・削除を統一的な方法で管理

### 基本的なCRUDフックの設計

```typescript
// src/hooks/useCrud.ts
import { useState, useCallback } from 'react';

interface UseCrudOptions<T> {
    endpoint: string;
    initialData?: T[];
}

interface UseCrudReturn<T> {
    data: T[];
    loading: boolean;
    error: Error | null;
    create: (item: Omit<T, 'id'>) => Promise<T>;
    read: (id: string) => Promise<T | null>;
    update: (id: string, item: Partial<T>) => Promise<T>;
    remove: (id: string) => Promise<void>;
    refresh: () => Promise<void>;
}

export function useCrud<T extends { id: string }>(
    options: UseCrudOptions<T>
): UseCrudReturn<T> {
    const [data, setData] = useState<T[]>(options.initialData || []);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState<Error | null>(null);
    
    const create = useCallback(async (item: Omit<T, 'id'>): Promise<T> => {
        setLoading(true);
        setError(null);
        try {
            const response = await fetch(options.endpoint, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(item)
            });
            const newItem = await response.json();
            setData(prev => [...prev, newItem]);
            return newItem;
        } catch (err) {
            const error = err instanceof Error ? err : new Error('作成に失敗しました');
            setError(error);
            throw error;
        } finally {
            setLoading(false);
        }
    }, [options.endpoint]);
    
    const read = useCallback(async (id: string): Promise<T | null> => {
        setLoading(true);
        setError(null);
        try {
            const response = await fetch(`${options.endpoint}/${id}`);
            if (!response.ok) return null;
            return await response.json();
        } catch (err) {
            const error = err instanceof Error ? err : new Error('読み取りに失敗しました');
            setError(error);
            return null;
        } finally {
            setLoading(false);
        }
    }, [options.endpoint]);
    
    const update = useCallback(async (id: string, item: Partial<T>): Promise<T> => {
        setLoading(true);
        setError(null);
        try {
            const response = await fetch(`${options.endpoint}/${id}`, {
                method: 'PUT',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(item)
            });
            const updatedItem = await response.json();
            setData(prev => prev.map(i => i.id === id ? updatedItem : i));
            return updatedItem;
        } catch (err) {
            const error = err instanceof Error ? err : new Error('更新に失敗しました');
            setError(error);
            throw error;
        } finally {
            setLoading(false);
        }
    }, [options.endpoint]);
    
    const remove = useCallback(async (id: string): Promise<void> => {
        setLoading(true);
        setError(null);
        try {
            await fetch(`${options.endpoint}/${id}`, { method: 'DELETE' });
            setData(prev => prev.filter(i => i.id !== id));
        } catch (err) {
            const error = err instanceof Error ? err : new Error('削除に失敗しました');
            setError(error);
            throw error;
        } finally {
            setLoading(false);
        }
    }, [options.endpoint]);
    
    const refresh = useCallback(async (): Promise<void> => {
        setLoading(true);
        setError(null);
        try {
            const response = await fetch(options.endpoint);
            const items = await response.json();
            setData(items);
        } catch (err) {
            const error = err instanceof Error ? err : new Error('更新に失敗しました');
            setError(error);
        } finally {
            setLoading(false);
        }
    }, [options.endpoint]);
    
    return { data, loading, error, create, read, update, remove, refresh };
}
```

### 実践例: 再利用可能なデータアクセス層の設計

```typescript
// src/types/entity.ts
export interface Entity {
    id: string;
    createdAt: Date;
    updatedAt: Date;
}

export interface User extends Entity {
    name: string;
    email: string;
    role: 'admin' | 'user';
}

export interface Post extends Entity {
    title: string;
    body: string;
    userId: string;
    published: boolean;
}

// src/services/apiClient.ts
import axios, { AxiosInstance, AxiosRequestConfig } from 'axios';

class ApiClient {
    private client: AxiosInstance;
    
    constructor(baseURL: string) {
        this.client = axios.create({
            baseURL,
            headers: {
                'Content-Type': 'application/json'
            }
        });
        
        // リクエストインターセプター（認証トークンの追加など）
        this.client.interceptors.request.use(
            (config) => {
                const token = localStorage.getItem('token');
                if (token) {
                    config.headers.Authorization = `Bearer ${token}`;
                }
                return config;
            },
            (error) => Promise.reject(error)
        );
        
        // レスポンスインターセプター（エラーハンドリング）
        this.client.interceptors.response.use(
            (response) => response,
            (error) => {
                if (error.response?.status === 401) {
                    // 認証エラーの処理
                    localStorage.removeItem('token');
                    window.location.href = '/login';
                }
                return Promise.reject(error);
            }
        );
    }
    
    async get<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
        const response = await this.client.get<T>(url, config);
        return response.data;
    }
    
    async post<T>(url: string, data?: unknown, config?: AxiosRequestConfig): Promise<T> {
        const response = await this.client.post<T>(url, data, config);
        return response.data;
    }
    
    async put<T>(url: string, data?: unknown, config?: AxiosRequestConfig): Promise<T> {
        const response = await this.client.put<T>(url, data, config);
        return response.data;
    }
    
    async delete<T>(url: string, config?: AxiosRequestConfig): Promise<T> {
        const response = await this.client.delete<T>(url, config);
        return response.data;
    }
}

export const apiClient = new ApiClient(process.env.REACT_APP_API_URL || '/api');

// src/services/baseService.ts
import { apiClient } from './apiClient';

export interface BaseEntity {
    id: string;
}

export interface CreateInput<T> {
    [key: string]: unknown;
}

export interface UpdateInput<T> {
    [key: string]: unknown;
}

export interface ServiceOptions {
    endpoint: string;
}

export class BaseService<T extends BaseEntity> {
    protected endpoint: string;
    
    constructor(options: ServiceOptions) {
        this.endpoint = options.endpoint;
    }
    
    async getAll(): Promise<T[]> {
        return apiClient.get<T[]>(this.endpoint);
    }
    
    async getById(id: string): Promise<T> {
        return apiClient.get<T>(`${this.endpoint}/${id}`);
    }
    
    async create(input: CreateInput<T>): Promise<T> {
        return apiClient.post<T>(this.endpoint, input);
    }
    
    async update(id: string, input: UpdateInput<T>): Promise<T> {
        return apiClient.put<T>(`${this.endpoint}/${id}`, input);
    }
    
    async delete(id: string): Promise<void> {
        return apiClient.delete<void>(`${this.endpoint}/${id}`);
    }
}

// src/services/userService.ts
import { BaseService } from './baseService';
import { User } from '../types/entity';

export interface UserCreateInput {
    name: string;
    email: string;
    role: 'admin' | 'user';
}

export interface UserUpdateInput {
    name?: string;
    email?: string;
    role?: 'admin' | 'user';
}

export class UserService extends BaseService<User> {
    constructor() {
        super({ endpoint: '/users' });
    }
    
    async create(input: UserCreateInput): Promise<User> {
        return super.create(input);
    }
    
    async update(id: string, input: UserUpdateInput): Promise<User> {
        return super.update(id, input);
    }
    
    // カスタムメソッド
    async getByEmail(email: string): Promise<User | null> {
        try {
            return await apiClient.get<User>(`${this.endpoint}/email/${email}`);
        } catch {
            return null;
        }
    }
}

export const userService = new UserService();

// src/services/postService.ts
import { BaseService } from './baseService';
import { Post } from '../types/entity';

export interface PostCreateInput {
    title: string;
    body: string;
    userId: string;
}

export interface PostUpdateInput {
    title?: string;
    body?: string;
    published?: boolean;
}

export class PostService extends BaseService<Post> {
    constructor() {
        super({ endpoint: '/posts' });
    }
    
    async create(input: PostCreateInput): Promise<Post> {
        return super.create(input);
    }
    
    async update(id: string, input: PostUpdateInput): Promise<Post> {
        return super.update(id, input);
    }
    
    // カスタムメソッド
    async getByUserId(userId: string): Promise<Post[]> {
        return apiClient.get<Post[]>(`${this.endpoint}/user/${userId}`);
    }
    
    async publish(id: string): Promise<Post> {
        return this.update(id, { published: true });
    }
    
    async unpublish(id: string): Promise<Post> {
        return this.update(id, { published: false });
    }
}

export const postService = new PostService();

// src/hooks/useCrud.ts
import { useState, useCallback, useEffect } from 'react';
import { BaseService } from '../services/baseService';
import { BaseEntity } from '../services/baseService';

interface UseCrudOptions<T extends BaseEntity> {
    service: BaseService<T>;
    autoFetch?: boolean;
}

interface UseCrudReturn<T extends BaseEntity> {
    items: T[];
    item: T | null;
    loading: boolean;
    error: Error | null;
    fetchAll: () => Promise<void>;
    fetchById: (id: string) => Promise<void>;
    create: (input: unknown) => Promise<T>;
    update: (id: string, input: unknown) => Promise<T>;
    remove: (id: string) => Promise<void>;
    clearItem: () => void;
    clearError: () => void;
}

export function useCrud<T extends BaseEntity>(
    options: UseCrudOptions<T>
): UseCrudReturn<T> {
    const { service, autoFetch = true } = options;
    
    const [items, setItems] = useState<T[]>([]);
    const [item, setItem] = useState<T | null>(null);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState<Error | null>(null);
    
    // 自動取得
    useEffect(() => {
        if (autoFetch) {
            fetchAll();
        }
    }, [autoFetch]);
    
    const fetchAll = useCallback(async () => {
        setLoading(true);
        setError(null);
        try {
            const data = await service.getAll();
            setItems(data);
        } catch (err) {
            const error = err instanceof Error ? err : new Error('取得に失敗しました');
            setError(error);
        } finally {
            setLoading(false);
        }
    }, [service]);
    
    const fetchById = useCallback(async (id: string) => {
        setLoading(true);
        setError(null);
        try {
            const data = await service.getById(id);
            setItem(data);
        } catch (err) {
            const error = err instanceof Error ? err : new Error('取得に失敗しました');
            setError(error);
            setItem(null);
        } finally {
            setLoading(false);
        }
    }, [service]);
    
    const create = useCallback(async (input: unknown): Promise<T> => {
        setLoading(true);
        setError(null);
        try {
            const newItem = await service.create(input);
            setItems(prev => [...prev, newItem]);
            return newItem;
        } catch (err) {
            const error = err instanceof Error ? err : new Error('作成に失敗しました');
            setError(error);
            throw error;
        } finally {
            setLoading(false);
        }
    }, [service]);
    
    const update = useCallback(async (id: string, input: unknown): Promise<T> => {
        setLoading(true);
        setError(null);
        try {
            const updatedItem = await service.update(id, input);
            setItems(prev => prev.map(i => i.id === id ? updatedItem : i));
            if (item?.id === id) {
                setItem(updatedItem);
            }
            return updatedItem;
        } catch (err) {
            const error = err instanceof Error ? err : new Error('更新に失敗しました');
            setError(error);
            throw error;
        } finally {
            setLoading(false);
        }
    }, [service, item]);
    
    const remove = useCallback(async (id: string): Promise<void> => {
        setLoading(true);
        setError(null);
        try {
            await service.delete(id);
            setItems(prev => prev.filter(i => i.id !== id));
            if (item?.id === id) {
                setItem(null);
            }
        } catch (err) {
            const error = err instanceof Error ? err : new Error('削除に失敗しました');
            setError(error);
            throw error;
        } finally {
            setLoading(false);
        }
    }, [service, item]);
    
    const clearItem = useCallback(() => {
        setItem(null);
    }, []);
    
    const clearError = useCallback(() => {
        setError(null);
    }, []);
    
    return {
        items,
        item,
        loading,
        error,
        fetchAll,
        fetchById,
        create,
        update,
        remove,
        clearItem,
        clearError
    };
}

// src/hooks/useUsers.ts
import { useCrud } from './useCrud';
import { userService } from '../services/userService';
import { User } from '../types/entity';

export function useUsers() {
    return useCrud<User>({
        service: userService,
        autoFetch: true
    });
}

// src/hooks/usePosts.ts
import { useCrud } from './useCrud';
import { postService } from '../services/postService';
import { Post } from '../types/entity';

export function usePosts() {
    return useCrud<Post>({
        service: postService,
        autoFetch: true
    });
}

// src/components/UserList.tsx
import React from 'react';
import { useUsers } from '../hooks/useUsers';
import { UserCreateInput } from '../services/userService';

export const UserList: React.FC = () => {
    const { items: users, loading, error, create, remove } = useUsers();
    const [showForm, setShowForm] = React.useState(false);
    const [formData, setFormData] = React.useState<UserCreateInput>({
        name: '',
        email: '',
        role: 'user'
    });
    
    const handleSubmit = async (e: React.FormEvent) => {
        e.preventDefault();
        try {
            await create(formData);
            setFormData({ name: '', email: '', role: 'user' });
            setShowForm(false);
        } catch (err) {
            console.error('ユーザーの作成に失敗しました:', err);
        }
    };
    
    if (loading) return <div>読み込み中...</div>;
    if (error) return <div>エラー: {error.message}</div>;
    
    return (
        <div>
            <h1>ユーザー一覧</h1>
            <button onClick={() => setShowForm(!showForm)}>
                {showForm ? 'フォームを閉じる' : '新規ユーザー作成'}
            </button>
            
            {showForm && (
                <form onSubmit={handleSubmit}>
                    <input
                        value={formData.name}
                        onChange={(e) => setFormData({ ...formData, name: e.target.value })}
                        placeholder="名前"
                        required
                    />
                    <input
                        type="email"
                        value={formData.email}
                        onChange={(e) => setFormData({ ...formData, email: e.target.value })}
                        placeholder="メールアドレス"
                        required
                    />
                    <select
                        value={formData.role}
                        onChange={(e) => setFormData({ ...formData, role: e.target.value as 'admin' | 'user' })}
                    >
                        <option value="user">ユーザー</option>
                        <option value="admin">管理者</option>
                    </select>
                    <button type="submit">作成</button>
                </form>
            )}
            
            <ul>
                {users.map(user => (
                    <li key={user.id}>
                        {user.name} ({user.email}) - {user.role}
                        <button onClick={() => remove(user.id)}>削除</button>
                    </li>
                ))}
            </ul>
        </div>
    );
};

// src/components/PostList.tsx
import React from 'react';
import { usePosts } from '../hooks/usePosts';
import { PostCreateInput } from '../services/postService';

export const PostList: React.FC = () => {
    const { items: posts, loading, error, create, update, remove } = usePosts();
    const [showForm, setShowForm] = React.useState(false);
    const [formData, setFormData] = React.useState<PostCreateInput>({
        title: '',
        body: '',
        userId: '1'
    });
    
    const handleSubmit = async (e: React.FormEvent) => {
        e.preventDefault();
        try {
            await create(formData);
            setFormData({ title: '', body: '', userId: '1' });
            setShowForm(false);
        } catch (err) {
            console.error('投稿の作成に失敗しました:', err);
        }
    };
    
    const handlePublish = async (id: string) => {
        try {
            await update(id, { published: true });
        } catch (err) {
            console.error('公開に失敗しました:', err);
        }
    };
    
    if (loading) return <div>読み込み中...</div>;
    if (error) return <div>エラー: {error.message}</div>;
    
    return (
        <div>
            <h1>投稿一覧</h1>
            <button onClick={() => setShowForm(!showForm)}>
                {showForm ? 'フォームを閉じる' : '新規投稿作成'}
            </button>
            
            {showForm && (
                <form onSubmit={handleSubmit}>
                    <input
                        value={formData.title}
                        onChange={(e) => setFormData({ ...formData, title: e.target.value })}
                        placeholder="タイトル"
                        required
                    />
                    <textarea
                        value={formData.body}
                        onChange={(e) => setFormData({ ...formData, body: e.target.value })}
                        placeholder="本文"
                        required
                    />
                    <button type="submit">作成</button>
                </form>
            )}
            
            <ul>
                {posts.map(post => (
                    <li key={post.id}>
                        <h3>{post.title}</h3>
                        <p>{post.body}</p>
                        <p>公開状態: {post.published ? '公開' : '非公開'}</p>
                        {!post.published && (
                            <button onClick={() => handlePublish(post.id)}>公開</button>
                        )}
                        <button onClick={() => remove(post.id)}>削除</button>
                    </li>
                ))}
            </ul>
        </div>
    );
};
```

### CRUDロジックの抽象化のメリット

1. **コードの再利用**: 同じロジックを複数のエンティティで使用できる
2. **保守性**: 1箇所を修正するだけで、すべてのエンティティに反映される
3. **一貫性**: すべてのエンティティで同じパターンを使用できる
4. **拡張性**: 新しいエンティティを簡単に追加できる
5. **テスト容易性**: 共通ロジックを1箇所でテストできる

### ベストプラクティス

#### 1. サービス層の分離

```typescript
// ✅ 良い例: サービス層を分離
class UserService extends BaseService<User> {
    // カスタムメソッド
}

// ❌ 悪い例: コンポーネント内で直接APIを呼び出す
function UserList() {
    useEffect(() => {
        fetch('/api/users').then(...);
    }, []);
}
```

#### 2. 型安全性の確保

```typescript
// ✅ 良い例: 型を明示
interface UserCreateInput {
    name: string;
    email: string;
}

// ❌ 悪い例: any型を使用
create(input: any): Promise<any>
```

#### 3. エラーハンドリングの統一

```typescript
// ✅ 良い例: 統一されたエラーハンドリング
try {
    await service.create(input);
} catch (error) {
    // エラー処理
}
```

---

## まとめ

この章では、CRUDロジックの抽象化と再利用可能なデータアクセス層の設計について学びました。

### 学んだこと
- **CRUDロジックの抽象化**: Create、Read、Update、Delete操作を共通化
- **サービス層の設計**: BaseServiceを継承して各エンティティのサービスを作成
- **カスタムフック**: useCrudフックでCRUD操作を簡単に使用
- **型安全性**: TypeScriptで型安全なデータアクセス層を構築

### 重要なポイント
1. **BaseService**: 共通のCRUD操作を提供する基底クラス
2. **サービス層**: 各エンティティごとにサービスを作成
3. **カスタムフック**: useCrudでコンポーネントから簡単に使用
4. **型安全性**: 型を明示して型安全なコードを書く
5. **エラーハンドリング**: 統一されたエラーハンドリング

### メリット
- **コードの再利用**: 同じロジックを複数箇所で使用
- **保守性**: 1箇所の修正で全体に反映
- **一貫性**: 統一されたパターン
- **拡張性**: 新しいエンティティを簡単に追加

### 次のステップ
CRUDロジックの抽象化を理解することで、保守しやすく拡張可能なReactアプリケーションを作成できるようになります。実際のプロジェクトで積極的にこのパターンを活用して、コードの品質を向上させましょう。

---

## 演習問題

### 問題1: 基本的なBaseServiceの実装
BaseServiceクラスを作成し、基本的なCRUD操作を実装してください。

<details>
<summary>解答例</summary>

```typescript
// src/services/baseService.ts
import { apiClient } from './apiClient';

export interface BaseEntity {
    id: string;
}

export class BaseService<T extends BaseEntity> {
    protected endpoint: string;
    
    constructor(endpoint: string) {
        this.endpoint = endpoint;
    }
    
    async getAll(): Promise<T[]> {
        return apiClient.get<T[]>(this.endpoint);
    }
    
    async getById(id: string): Promise<T> {
        return apiClient.get<T>(`${this.endpoint}/${id}`);
    }
    
    async create(input: unknown): Promise<T> {
        return apiClient.post<T>(this.endpoint, input);
    }
    
    async update(id: string, input: unknown): Promise<T> {
        return apiClient.put<T>(`${this.endpoint}/${id}`, input);
    }
    
    async delete(id: string): Promise<void> {
        return apiClient.delete<void>(`${this.endpoint}/${id}`);
    }
}
```
</details>

### 問題2: カスタムサービスの作成
UserServiceクラスを作成し、BaseServiceを継承してください。

<details>
<summary>解答例</summary>

```typescript
// src/services/userService.ts
import { BaseService } from './baseService';

export interface User {
    id: string;
    name: string;
    email: string;
}

export interface UserCreateInput {
    name: string;
    email: string;
}

export class UserService extends BaseService<User> {
    constructor() {
        super('/users');
    }
    
    async create(input: UserCreateInput): Promise<User> {
        return super.create(input);
    }
}

export const userService = new UserService();
```
</details>

### 問題3: useCrudフックの実装
useCrudカスタムフックを作成し、CRUD操作を提供してください。

<details>
<summary>解答例</summary>

```typescript
// src/hooks/useCrud.ts
import { useState, useCallback } from 'react';
import { BaseService, BaseEntity } from '../services/baseService';

export function useCrud<T extends BaseEntity>(service: BaseService<T>) {
    const [items, setItems] = useState<T[]>([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState<Error | null>(null);
    
    const fetchAll = useCallback(async () => {
        setLoading(true);
        try {
            const data = await service.getAll();
            setItems(data);
        } catch (err) {
            setError(err instanceof Error ? err : new Error('取得に失敗しました'));
        } finally {
            setLoading(false);
        }
    }, [service]);
    
    const create = useCallback(async (input: unknown): Promise<T> => {
        setLoading(true);
        try {
            const newItem = await service.create(input);
            setItems(prev => [...prev, newItem]);
            return newItem;
        } catch (err) {
            setError(err instanceof Error ? err : new Error('作成に失敗しました'));
            throw err;
        } finally {
            setLoading(false);
        }
    }, [service]);
    
    return { items, loading, error, fetchAll, create };
}
```
</details>

### 問題4: コンポーネントでの使用
useCrudフックを使って、ユーザーリストコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```typescript
// src/components/UserList.tsx
import React, { useEffect } from 'react';
import { useCrud } from '../hooks/useCrud';
import { userService, UserCreateInput } from '../services/userService';

export const UserList: React.FC = () => {
    const { items, loading, error, fetchAll, create } = useCrud(userService);
    
    useEffect(() => {
        fetchAll();
    }, [fetchAll]);
    
    const handleCreate = async () => {
        try {
            await create({ name: '新規ユーザー', email: 'new@example.com' });
        } catch (err) {
            console.error('作成に失敗しました:', err);
        }
    };
    
    if (loading) return <div>読み込み中...</div>;
    if (error) return <div>エラー: {error.message}</div>;
    
    return (
        <div>
            <h1>ユーザー一覧</h1>
            <button onClick={handleCreate}>新規作成</button>
            <ul>
                {items.map(user => (
                    <li key={user.id}>{user.name} ({user.email})</li>
                ))}
            </ul>
        </div>
    );
};
```
</details>

### 問題5: カスタムメソッドの追加
UserServiceに、メールアドレスでユーザーを検索するカスタムメソッドを追加してください。

<details>
<summary>解答例</summary>

```typescript
// src/services/userService.ts
import { BaseService } from './baseService';
import { apiClient } from './apiClient';

export class UserService extends BaseService<User> {
    constructor() {
        super('/users');
    }
    
    // カスタムメソッド
    async getByEmail(email: string): Promise<User | null> {
        try {
            return await apiClient.get<User>(`${this.endpoint}/email/${email}`);
        } catch {
            return null;
        }
    }
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。


