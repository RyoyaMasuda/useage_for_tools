# 8-2. Recoil / Jotai (または Zustand)

この章では、グローバル状態管理ライブラリ（Recoil、Jotai、Zustand）について学びます。これらのライブラリを使うことで、Context APIよりも効率的にグローバル状態を管理できるようになります。

---

## 目次

- [Atom の概念](#atom-の概念)
- [グローバルステートの更新と参照](#グローバルステートの更新と参照)
- [どのライブラリを選ぶべきかの指針](#どのライブラリを選ぶべきかの指針)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## Atom の概念

### Atomとは
Atomは、**グローバル状態の最小単位**です。RecoilやJotaiでは、Atomを使って状態を定義し、複数のコンポーネントで共有します。

### 日常生活での例
- **共有のメモ帳**: 複数の人が同じメモ帳（Atom）を見て、情報を共有する
- **共通のカレンダー**: 複数の人が同じカレンダー（Atom）を見て、予定を確認する
- **共有の設定**: 複数の人が同じ設定（Atom）を参照・更新する

### RecoilのAtom

#### インストール

```bash
npm install recoil
```

#### Atomの定義

```typescript
// src/atoms/counterAtom.ts
import { atom } from 'recoil';

export const counterAtom = atom({
    key: 'counterAtom', // 一意のキー
    default: 0 // デフォルト値
});
```

#### Atomの使用

```typescript
// src/components/Counter.tsx
import { useRecoilState } from 'recoil';
import { counterAtom } from '../atoms/counterAtom';

export const Counter: React.FC = () => {
    const [count, setCount] = useRecoilState(counterAtom);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
};
```

### JotaiのAtom

#### インストール

```bash
npm install jotai
```

#### Atomの定義

```typescript
// src/atoms/counterAtom.ts
import { atom } from 'jotai';

export const counterAtom = atom(0); // デフォルト値
```

#### Atomの使用

```typescript
// src/components/Counter.tsx
import { useAtom } from 'jotai';
import { counterAtom } from '../atoms/counterAtom';

export const Counter: React.FC = () => {
    const [count, setCount] = useAtom(counterAtom);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
};
```

### ZustandのStore

#### インストール

```bash
npm install zustand
```

#### Storeの定義

```typescript
// src/stores/counterStore.ts
import { create } from 'zustand';

interface CounterState {
    count: number;
    increment: () => void;
    decrement: () => void;
    reset: () => void;
}

export const useCounterStore = create<CounterState>((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 })),
    decrement: () => set((state) => ({ count: state.count - 1 })),
    reset: () => set({ count: 0 })
}));
```

#### Storeの使用

```typescript
// src/components/Counter.tsx
import { useCounterStore } from '../stores/counterStore';

export const Counter: React.FC = () => {
    const { count, increment, decrement, reset } = useCounterStore();
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={increment}>増やす</button>
            <button onClick={decrement}>減らす</button>
            <button onClick={reset}>リセット</button>
        </div>
    );
};
```

### 実践例: Atomの概念

```typescript
// Recoilの例
// src/atoms/userAtom.ts
import { atom } from 'recoil';

export interface User {
    id: string;
    name: string;
    email: string;
}

export const userAtom = atom<User | null>({
    key: 'userAtom',
    default: null
});

// src/components/UserProfile.tsx
import { useRecoilValue } from 'recoil';
import { userAtom } from '../atoms/userAtom';

export const UserProfile: React.FC = () => {
    const user = useRecoilValue(userAtom); // 読み取り専用
    
    if (!user) return <div>ユーザー情報がありません</div>;
    
    return (
        <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
        </div>
    );
};

// src/components/UserEditor.tsx
import { useRecoilState } from 'recoil';
import { userAtom } from '../atoms/userAtom';

export const UserEditor: React.FC = () => {
    const [user, setUser] = useRecoilState(userAtom);
    
    if (!user) return null;
    
    return (
        <div>
            <input
                value={user.name}
                onChange={(e) => setUser({ ...user, name: e.target.value })}
            />
        </div>
    );
};

// Jotaiの例
// src/atoms/userAtom.ts
import { atom } from 'jotai';

export interface User {
    id: string;
    name: string;
    email: string;
}

export const userAtom = atom<User | null>(null);

// src/components/UserProfile.tsx
import { useAtomValue } from 'jotai';
import { userAtom } from '../atoms/userAtom';

export const UserProfile: React.FC = () => {
    const user = useAtomValue(userAtom); // 読み取り専用
    
    if (!user) return <div>ユーザー情報がありません</div>;
    
    return (
        <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
        </div>
    );
};

// Zustandの例
// src/stores/userStore.ts
import { create } from 'zustand';

interface User {
    id: string;
    name: string;
    email: string;
}

interface UserState {
    user: User | null;
    setUser: (user: User | null) => void;
    updateUserName: (name: string) => void;
}

export const useUserStore = create<UserState>((set) => ({
    user: null,
    setUser: (user) => set({ user }),
    updateUserName: (name) => set((state) => ({
        user: state.user ? { ...state.user, name } : null
    }))
}));

// src/components/UserProfile.tsx
import { useUserStore } from '../stores/userStore';

export const UserProfile: React.FC = () => {
    const user = useUserStore((state) => state.user); // 必要な部分のみ購読
    
    if (!user) return <div>ユーザー情報がありません</div>;
    
    return (
        <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
        </div>
    );
};
```

---

## グローバルステートの更新と参照

### Recoilでの更新と参照

#### 基本的な使用方法

```typescript
// src/atoms/counterAtom.ts
import { atom } from 'recoil';

export const counterAtom = atom({
    key: 'counterAtom',
    default: 0
});

// src/components/Counter.tsx
import { useRecoilState, useRecoilValue, useSetRecoilState } from 'recoil';
import { counterAtom } from '../atoms/counterAtom';

// 読み書き両方
export const CounterWithState: React.FC = () => {
    const [count, setCount] = useRecoilState(counterAtom);
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
};

// 読み取り専用
export const CounterDisplay: React.FC = () => {
    const count = useRecoilValue(counterAtom);
    return <p>カウント: {count}</p>;
};

// 書き込み専用
export const CounterControls: React.FC = () => {
    const setCount = useSetRecoilState(counterAtom);
    return (
        <div>
            <button onClick={() => setCount(prev => prev + 1)}>増やす</button>
            <button onClick={() => setCount(0)}>リセット</button>
        </div>
    );
};
```

### Jotaiでの更新と参照

#### 基本的な使用方法

```typescript
// src/atoms/counterAtom.ts
import { atom } from 'jotai';

export const counterAtom = atom(0);

// src/components/Counter.tsx
import { useAtom, useAtomValue, useSetAtom } from 'jotai';
import { counterAtom } from '../atoms/counterAtom';

// 読み書き両方
export const CounterWithState: React.FC = () => {
    const [count, setCount] = useAtom(counterAtom);
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
};

// 読み取り専用
export const CounterDisplay: React.FC = () => {
    const count = useAtomValue(counterAtom);
    return <p>カウント: {count}</p>;
};

// 書き込み専用
export const CounterControls: React.FC = () => {
    const setCount = useSetAtom(counterAtom);
    return (
        <div>
            <button onClick={() => setCount(prev => prev + 1)}>増やす</button>
            <button onClick={() => setCount(0)}>リセット</button>
        </div>
    );
};
```

### Zustandでの更新と参照

#### 基本的な使用方法

```typescript
// src/stores/counterStore.ts
import { create } from 'zustand';

interface CounterState {
    count: number;
    increment: () => void;
    decrement: () => void;
    reset: () => void;
}

export const useCounterStore = create<CounterState>((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 })),
    decrement: () => set((state) => ({ count: state.count - 1 })),
    reset: () => set({ count: 0 })
}));

// src/components/Counter.tsx
import { useCounterStore } from '../stores/counterStore';

// すべての状態とアクションを使用
export const CounterFull: React.FC = () => {
    const { count, increment, decrement, reset } = useCounterStore();
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={increment}>増やす</button>
            <button onClick={decrement}>減らす</button>
            <button onClick={reset}>リセット</button>
        </div>
    );
};

// 必要な部分のみ購読（パフォーマンス最適化）
export const CounterDisplay: React.FC = () => {
    const count = useCounterStore((state) => state.count);
    return <p>カウント: {count}</p>;
};

export const CounterControls: React.FC = () => {
    const increment = useCounterStore((state) => state.increment);
    const decrement = useCounterStore((state) => state.decrement);
    const reset = useCounterStore((state) => state.reset);
    
    return (
        <div>
            <button onClick={increment}>増やす</button>
            <button onClick={decrement}>減らす</button>
            <button onClick={reset}>リセット</button>
        </div>
    );
};
```

### 実践例: グローバルステートの更新と参照

```typescript
// Recoilの実践例
// src/atoms/todoAtom.ts
import { atom } from 'recoil';

export interface Todo {
    id: string;
    text: string;
    completed: boolean;
}

export const todosAtom = atom<Todo[]>({
    key: 'todosAtom',
    default: []
});

// src/components/TodoList.tsx
import { useRecoilValue } from 'recoil';
import { todosAtom } from '../atoms/todoAtom';

export const TodoList: React.FC = () => {
    const todos = useRecoilValue(todosAtom);
    
    return (
        <ul>
            {todos.map(todo => (
                <li key={todo.id}>
                    {todo.text} - {todo.completed ? '完了' : '未完了'}
                </li>
            ))}
        </ul>
    );
};

// src/components/TodoForm.tsx
import { useSetRecoilState } from 'recoil';
import { todosAtom, Todo } from '../atoms/todoAtom';

export const TodoForm: React.FC = () => {
    const setTodos = useSetRecoilState(todosAtom);
    const [text, setText] = React.useState('');
    
    const handleSubmit = (e: React.FormEvent) => {
        e.preventDefault();
        const newTodo: Todo = {
            id: Date.now().toString(),
            text,
            completed: false
        };
        setTodos(prev => [...prev, newTodo]);
        setText('');
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <input
                value={text}
                onChange={(e) => setText(e.target.value)}
                placeholder="新しいタスク"
            />
            <button type="submit">追加</button>
        </form>
    );
};

// Jotaiの実践例
// src/atoms/todoAtom.ts
import { atom } from 'jotai';

export interface Todo {
    id: string;
    text: string;
    completed: boolean;
}

export const todosAtom = atom<Todo[]>([]);

// src/components/TodoList.tsx
import { useAtomValue } from 'jotai';
import { todosAtom } from '../atoms/todoAtom';

export const TodoList: React.FC = () => {
    const todos = useAtomValue(todosAtom);
    
    return (
        <ul>
            {todos.map(todo => (
                <li key={todo.id}>
                    {todo.text} - {todo.completed ? '完了' : '未完了'}
                </li>
            ))}
        </ul>
    );
};

// Zustandの実践例
// src/stores/todoStore.ts
import { create } from 'zustand';

export interface Todo {
    id: string;
    text: string;
    completed: boolean;
}

interface TodoState {
    todos: Todo[];
    addTodo: (text: string) => void;
    toggleTodo: (id: string) => void;
    removeTodo: (id: string) => void;
}

export const useTodoStore = create<TodoState>((set) => ({
    todos: [],
    addTodo: (text) => set((state) => ({
        todos: [...state.todos, {
            id: Date.now().toString(),
            text,
            completed: false
        }]
    })),
    toggleTodo: (id) => set((state) => ({
        todos: state.todos.map(todo =>
            todo.id === id ? { ...todo, completed: !todo.completed } : todo
        )
    })),
    removeTodo: (id) => set((state) => ({
        todos: state.todos.filter(todo => todo.id !== id)
    }))
}));

// src/components/TodoList.tsx
import { useTodoStore } from '../stores/todoStore';

export const TodoList: React.FC = () => {
    const todos = useTodoStore((state) => state.todos);
    
    return (
        <ul>
            {todos.map(todo => (
                <li key={todo.id}>
                    {todo.text} - {todo.completed ? '完了' : '未完了'}
                </li>
            ))}
        </ul>
    );
};
```

---

## どのライブラリを選ぶべきかの指針

### ライブラリの比較

| 特徴 | Recoil | Jotai | Zustand |
|------|--------|-------|---------|
| **開発元** | Meta (Facebook) | コミュニティ | コミュニティ |
| **学習曲線** | 中 | 低 | 低 |
| **バンドルサイズ** | 中 | 小 | 小 |
| **TypeScript** | 良好 | 良好 | 良好 |
| **DevTools** | あり | あり | あり |
| **非同期処理** | サポート | サポート | サポート |
| **パフォーマンス** | 良好 | 良好 | 良好 |

### Recoilを選ぶべき場合

1. **Meta製のライブラリを信頼する**: Facebook/Metaが開発している
2. **複雑な状態管理**: 複雑な状態の依存関係を管理したい
3. **Selector機能**: 派生状態を計算したい
4. **大規模プロジェクト**: 大規模なプロジェクトで使用したい

#### Recoilの特徴

```typescript
// Selectorの例
import { selector } from 'recoil';
import { todosAtom } from './todosAtom';

export const completedTodosSelector = selector({
    key: 'completedTodosSelector',
    get: ({ get }) => {
        const todos = get(todosAtom);
        return todos.filter(todo => todo.completed);
    }
});
```

### Jotaiを選ぶべき場合

1. **シンプルさを重視**: 最小限のAPIで簡単に使いたい
2. **小さなバンドルサイズ**: バンドルサイズを小さく保ちたい
3. **Composable**: Atomを組み合わせて複雑な状態を作りたい
4. **TypeScript**: TypeScriptとの統合が良い

#### Jotaiの特徴

```typescript
// 派生Atomの例
import { atom } from 'jotai';
import { todosAtom } from './todosAtom';

export const completedTodosAtom = atom((get) => {
    const todos = get(todosAtom);
    return todos.filter(todo => todo.completed);
});
```

### Zustandを選ぶべき場合

1. **シンプルなAPI**: シンプルで直感的なAPIが好み
2. **ミドルウェア**: Redux DevToolsなどのミドルウェアを使いたい
3. **アクション中心**: アクション（関数）を中心に設計したい
4. **軽量**: 軽量で高速なライブラリが好み

#### Zustandの特徴

```typescript
// ミドルウェアの例
import { create } from 'zustand';
import { devtools } from 'zustand/middleware';

export const useStore = create(
    devtools((set) => ({
        count: 0,
        increment: () => set((state) => ({ count: state.count + 1 }))
    }))
);
```

### 実践例: ライブラリの選択指針

```typescript
// 例1: シンプルなカウンター → JotaiまたはZustand
// Jotai
const countAtom = atom(0);

// Zustand
const useCounterStore = create((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 }))
}));

// 例2: 複雑な状態管理 → Recoil
// Recoil
const userAtom = atom<User | null>({ key: 'user', default: null });
const userProfileSelector = selector({
    key: 'userProfile',
    get: ({ get }) => {
        const user = get(userAtom);
        return user ? { name: user.name, email: user.email } : null;
    }
});

// 例3: アクション中心の設計 → Zustand
// Zustand
const useTodoStore = create((set) => ({
    todos: [],
    addTodo: (text) => set((state) => ({
        todos: [...state.todos, { id: Date.now().toString(), text }]
    })),
    removeTodo: (id) => set((state) => ({
        todos: state.todos.filter(todo => todo.id !== id)
    }))
}));
```

### 選択のベストプラクティス

1. **プロジェクトの規模**: 小規模ならJotai/Zustand、大規模ならRecoil
2. **チームの経験**: チームが慣れ親しんでいるライブラリを選ぶ
3. **要件**: プロジェクトの要件に合わせて選ぶ
4. **パフォーマンス**: パフォーマンス要件を考慮する
5. **メンテナンス**: アクティブにメンテナンスされているライブラリを選ぶ

---

## まとめ

この章では、グローバル状態管理ライブラリ（Recoil、Jotai、Zustand）について学びました。

### 学んだこと
- **Atomの概念**: グローバル状態の最小単位
- **Recoil**: Meta製の状態管理ライブラリ、Selector機能が強力
- **Jotai**: シンプルで軽量な状態管理ライブラリ
- **Zustand**: アクション中心のシンプルな状態管理ライブラリ
- **ライブラリの選択**: プロジェクトの要件に合わせて選択

### 重要なポイント
1. **Atom**: グローバル状態を定義する最小単位
2. **更新と参照**: 各ライブラリで異なるAPIを使用
3. **パフォーマンス**: 必要な部分のみ購読してパフォーマンスを最適化
4. **選択指針**: プロジェクトの要件に合わせて適切なライブラリを選択
5. **TypeScript**: すべてのライブラリがTypeScriptをサポート

### 使い分けの目安
- **Recoil**: 複雑な状態管理、大規模プロジェクト
- **Jotai**: シンプルさと軽量性を重視
- **Zustand**: アクション中心の設計、Redux DevToolsとの統合

### 次のステップ
グローバル状態管理ライブラリを理解することで、より効率的に状態を管理できるようになります。実際のプロジェクトで積極的にこれらのライブラリを活用して、アプリケーションの状態管理を改善しましょう。

---

## 演習問題

### 問題1: RecoilのAtom定義
Recoilを使って、カウンターのAtomを定義してください。

<details>
<summary>解答例</summary>

```typescript
// src/atoms/counterAtom.ts
import { atom } from 'recoil';

export const counterAtom = atom({
    key: 'counterAtom',
    default: 0
});

// src/components/Counter.tsx
import { useRecoilState } from 'recoil';
import { counterAtom } from '../atoms/counterAtom';

export const Counter: React.FC = () => {
    const [count, setCount] = useRecoilState(counterAtom);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
};
```
</details>

### 問題2: JotaiのAtom定義
Jotaiを使って、カウンターのAtomを定義してください。

<details>
<summary>解答例</summary>

```typescript
// src/atoms/counterAtom.ts
import { atom } from 'jotai';

export const counterAtom = atom(0);

// src/components/Counter.tsx
import { useAtom } from 'jotai';
import { counterAtom } from '../atoms/counterAtom';

export const Counter: React.FC = () => {
    const [count, setCount] = useAtom(counterAtom);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
};
```
</details>

### 問題3: ZustandのStore定義
Zustandを使って、カウンターのStoreを定義してください。

<details>
<summary>解答例</summary>

```typescript
// src/stores/counterStore.ts
import { create } from 'zustand';

interface CounterState {
    count: number;
    increment: () => void;
    decrement: () => void;
}

export const useCounterStore = create<CounterState>((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 })),
    decrement: () => set((state) => ({ count: state.count - 1 }))
}));

// src/components/Counter.tsx
import { useCounterStore } from '../stores/counterStore';

export const Counter: React.FC = () => {
    const { count, increment, decrement } = useCounterStore();
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={increment}>増やす</button>
            <button onClick={decrement}>減らす</button>
        </div>
    );
};
```
</details>

### 問題4: 読み取り専用の参照
RecoilまたはJotaiを使って、読み取り専用でAtomの値を参照してください。

<details>
<summary>解答例</summary>

```typescript
// Recoil
import { useRecoilValue } from 'recoil';
import { counterAtom } from '../atoms/counterAtom';

export const CounterDisplay: React.FC = () => {
    const count = useRecoilValue(counterAtom);
    return <p>カウント: {count}</p>;
};

// Jotai
import { useAtomValue } from 'jotai';
import { counterAtom } from '../atoms/counterAtom';

export const CounterDisplay: React.FC = () => {
    const count = useAtomValue(counterAtom);
    return <p>カウント: {count}</p>;
};
```
</details>

### 問題5: Zustandでの部分的な購読
Zustandを使って、必要な部分のみを購読してパフォーマンスを最適化してください。

<details>
<summary>解答例</summary>

```typescript
// src/stores/counterStore.ts
import { create } from 'zustand';

interface CounterState {
    count: number;
    otherState: string;
    increment: () => void;
}

export const useCounterStore = create<CounterState>((set) => ({
    count: 0,
    otherState: 'test',
    increment: () => set((state) => ({ count: state.count + 1 }))
}));

// src/components/Counter.tsx
import { useCounterStore } from '../stores/counterStore';

export const Counter: React.FC = () => {
    // countのみを購読（otherStateが変更されても再レンダリングされない）
    const count = useCounterStore((state) => state.count);
    const increment = useCounterStore((state) => state.increment);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={increment}>増やす</button>
        </div>
    );
};
```
</details>

---

お疲れ様でした！次の章に進みましょう。


