# 3-1. 副作用とフック（useEffect）

この章では、Reactコンポーネントで副作用を制御する「useEffect」フックについて学びます。useEffectを理解することで、API呼び出し、タイマー、DOM操作などの副作用を適切に管理できるようになります。

---

## 目次

- [useEffectの基本構文](#useeffectの基本構文)
- [依存配列 (dependency array) の正しい設定](#依存配列-dependency-array-の正しい設定)
- [クリーンアップ関数 (アンマウント時の処理)](#クリーンアップ関数-アンマウント時の処理)
- [注意点: 無限ループと不要な実行を防ぐ](#注意点-無限ループと不要な実行を防ぐ)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## useEffectの基本構文

### useEffectとは
useEffectは、**コンポーネントのレンダリング後に副作用を実行する**ためのフックです。API呼び出し、タイマー設定、DOM操作など、レンダリングとは直接関係ない処理を実行する際に使用します。

### 日常生活での例
- **料理の準備**: 材料を切った後（レンダリング後）に、フライパンを温める（副作用）
- **本を読む**: 本を開いた後（レンダリング後）に、メモを取る（副作用）
- **運動の準備**: ストレッチをした後（レンダリング後）に、水分補給をする（副作用）

### プログラミングでの例

```jsx
import { useState, useEffect } from 'react';

function Counter() {
    const [count, setCount] = useState(0);

    // レンダリング後に実行される
    useEffect(() => {
        console.log(`カウントが${count}になりました`);
    });

    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
}
```

### useEffectの基本構文

```jsx
import { useEffect } from 'react';

// 基本構文
useEffect(() => {
    // 副作用の処理
}, [依存配列]);
```

### useEffectの特徴
1. **レンダリング後に実行**: コンポーネントのレンダリングが完了した後に実行される
2. **副作用の分離**: レンダリングロジックと副作用を分離できる
3. **ライフサイクル管理**: コンポーネントのマウント、更新、アンマウント時に処理を実行できる
4. **依存配列による制御**: 依存配列で実行タイミングを制御できる

### 実践例: useEffectの基本

```jsx
// src/components/UserProfile.jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);

    // コンポーネントがマウントされた時にユーザー情報を取得
    useEffect(() => {
        console.log('ユーザー情報を取得中...');
        
        // 実際のAPI呼び出しの代わりに、模擬データを使用
        setTimeout(() => {
            setUser({
                id: userId,
                name: '太郎',
                email: 'taro@example.com'
            });
            setLoading(false);
        }, 1000);
    }, [userId]);  // userIdが変更された時に再実行

    if (loading) {
        return <p>読み込み中...</p>;
    }

    return (
        <div>
            <h2>{user.name}</h2>
            <p>メール: {user.email}</p>
        </div>
    );
}

// src/App.jsx
function App() {
    const [userId, setUserId] = useState(1);

    return (
        <div>
            <button onClick={() => setUserId(userId === 1 ? 2 : 1)}>
                ユーザーを切り替え
            </button>
            <UserProfile userId={userId} />
        </div>
    );
}

export default App;
```

### 実践例: タイトルの変更

```jsx
// src/components/PageTitle.jsx
import { useEffect } from 'react';

function PageTitle({ title }) {
    // ページタイトルを変更
    useEffect(() => {
        document.title = title;
    }, [title]);

    return <h1>{title}</h1>;
}

// src/App.jsx
function App() {
    const [pageTitle, setPageTitle] = useState('ホーム');

    return (
        <div>
            <button onClick={() => setPageTitle('ホーム')}>ホーム</button>
            <button onClick={() => setPageTitle('About')}>About</button>
            <button onClick={() => setPageTitle('Contact')}>Contact</button>
            <PageTitle title={pageTitle} />
        </div>
    );
}

export default App;
```

### 実践例: ローカルストレージへの保存

```jsx
// src/components/TodoList.jsx
import { useState, useEffect } from 'react';

function TodoList() {
    const [todos, setTodos] = useState(() => {
        // 初回レンダリング時にローカルストレージから読み込む
        const saved = localStorage.getItem('todos');
        return saved ? JSON.parse(saved) : [];
    });

    // todosが変更された時にローカルストレージに保存
    useEffect(() => {
        localStorage.setItem('todos', JSON.stringify(todos));
        console.log('Todoリストを保存しました');
    }, [todos]);

    const addTodo = (text) => {
        setTodos([...todos, { id: Date.now(), text, completed: false }]);
    };

    return (
        <div>
            <h2>Todoリスト</h2>
            <ul>
                {todos.map(todo => (
                    <li key={todo.id}>{todo.text}</li>
                ))}
            </ul>
            <button onClick={() => addTodo('新しいタスク')}>
                タスクを追加
            </button>
        </div>
    );
}

export default TodoList;
```

---

## 依存配列 (dependency array) の正しい設定

### 依存配列とは
依存配列は、**useEffectをいつ実行するかを制御する**配列です。配列に指定した値が変更された時のみ、useEffectが再実行されます。

### 依存配列のパターン

```jsx
// 1. 依存配列なし: 毎回のレンダリング後に実行
useEffect(() => {
    console.log('毎回実行');
});

// 2. 空の依存配列: 初回マウント時のみ実行
useEffect(() => {
    console.log('初回のみ実行');
}, []);

// 3. 依存配列に値: 指定した値が変更された時のみ実行
useEffect(() => {
    console.log('値が変更された時のみ実行');
}, [value]);
```

### 依存配列の重要性
1. **パフォーマンス**: 不要な再実行を防ぐ
2. **無限ループの防止**: 適切な依存配列で無限ループを防ぐ
3. **意図の明確化**: どの値に依存しているかが明確になる

### 実践例: 依存配列なし（毎回実行）

```jsx
// src/components/Counter.jsx
import { useState, useEffect } from 'react';

function Counter() {
    const [count, setCount] = useState(0);

    // 依存配列なし: 毎回のレンダリング後に実行
    useEffect(() => {
        console.log(`カウント: ${count}`);
        // 注意: このパターンは通常避けるべき
    });

    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
}

export default Counter;
```

### 実践例: 空の依存配列（初回のみ実行）

```jsx
// src/components/DataFetcher.jsx
import { useState, useEffect } from 'react';

function DataFetcher() {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);

    // 空の依存配列: 初回マウント時のみ実行
    useEffect(() => {
        console.log('データを取得中...');
        
        // API呼び出しの模擬
        fetch('https://api.example.com/data')
            .then(response => response.json())
            .then(data => {
                setData(data);
                setLoading(false);
            })
            .catch(error => {
                console.error('エラー:', error);
                setLoading(false);
            });
    }, []);  // 空の依存配列

    if (loading) {
        return <p>読み込み中...</p>;
    }

    return (
        <div>
            <h2>データ</h2>
            <pre>{JSON.stringify(data, null, 2)}</pre>
        </div>
    );
}

export default DataFetcher;
```

### 実践例: 依存配列に値（値が変更された時のみ実行）

```jsx
// src/components/UserPosts.jsx
import { useState, useEffect } from 'react';

function UserPosts({ userId }) {
    const [posts, setPosts] = useState([]);
    const [loading, setLoading] = useState(true);

    // userIdが変更された時に実行
    useEffect(() => {
        console.log(`ユーザー${userId}の投稿を取得中...`);
        setLoading(true);

        // API呼び出しの模擬
        setTimeout(() => {
            setPosts([
                { id: 1, title: '投稿1', userId },
                { id: 2, title: '投稿2', userId }
            ]);
            setLoading(false);
        }, 1000);
    }, [userId]);  // userIdが依存配列に含まれている

    if (loading) {
        return <p>読み込み中...</p>;
    }

    return (
        <div>
            <h2>ユーザー{userId}の投稿</h2>
            <ul>
                {posts.map(post => (
                    <li key={post.id}>{post.title}</li>
                ))}
            </ul>
        </div>
    );
}

// src/App.jsx
function App() {
    const [userId, setUserId] = useState(1);

    return (
        <div>
            <button onClick={() => setUserId(userId === 1 ? 2 : 1)}>
                ユーザーを切り替え
            </button>
            <UserPosts userId={userId} />
        </div>
    );
}

export default App;
```

### 実践例: 複数の依存値

```jsx
// src/components/SearchResults.jsx
import { useState, useEffect } from 'react';

function SearchResults({ query, category }) {
    const [results, setResults] = useState([]);
    const [loading, setLoading] = useState(false);

    // queryとcategoryの両方が変更された時に実行
    useEffect(() => {
        if (!query) {
            setResults([]);
            return;
        }

        setLoading(true);
        console.log(`検索中: ${query}, カテゴリ: ${category}`);

        // API呼び出しの模擬
        setTimeout(() => {
            setResults([
                { id: 1, title: `${query}の結果1`, category },
                { id: 2, title: `${query}の結果2`, category }
            ]);
            setLoading(false);
        }, 500);
    }, [query, category]);  // 複数の依存値

    if (loading) {
        return <p>検索中...</p>;
    }

    return (
        <div>
            <h2>検索結果</h2>
            <ul>
                {results.map(result => (
                    <li key={result.id}>{result.title}</li>
                ))}
            </ul>
        </div>
    );
}

// src/App.jsx
function App() {
    const [query, setQuery] = useState('');
    const [category, setCategory] = useState('all');

    return (
        <div>
            <input 
                value={query}
                onChange={(e) => setQuery(e.target.value)}
                placeholder="検索..."
            />
            <select value={category} onChange={(e) => setCategory(e.target.value)}>
                <option value="all">すべて</option>
                <option value="tech">技術</option>
                <option value="news">ニュース</option>
            </select>
            <SearchResults query={query} category={category} />
        </div>
    );
}

export default App;
```

### 依存配列の注意点

```jsx
// ❌ 悪い例: 依存配列に含めるべき値を含めていない
function BadExample({ userId }) {
    const [data, setData] = useState(null);

    useEffect(() => {
        fetch(`/api/users/${userId}`)  // userIdを使用している
            .then(res => res.json())
            .then(setData);
    }, []);  // userIdが依存配列に含まれていない（警告が出る）

    return <div>{data?.name}</div>;
}

// ✅ 良い例: 使用している値を依存配列に含める
function GoodExample({ userId }) {
    const [data, setData] = useState(null);

    useEffect(() => {
        fetch(`/api/users/${userId}`)
            .then(res => res.json())
            .then(setData);
    }, [userId]);  // userIdを依存配列に含める

    return <div>{data?.name}</div>;
}
```

---

## クリーンアップ関数 (アンマウント時の処理)

### クリーンアップ関数とは
クリーンアップ関数は、**コンポーネントがアンマウントされる時や、依存配列の値が変更される前に実行される**関数です。タイマーのクリア、イベントリスナーの削除、APIリクエストのキャンセルなどに使用します。

### クリーンアップ関数の基本構文

```jsx
useEffect(() => {
    // 副作用の処理

    // クリーンアップ関数を返す
    return () => {
        // クリーンアップ処理
    };
}, [依存配列]);
```

### クリーンアップ関数の実行タイミング
1. **アンマウント時**: コンポーネントが削除される時
2. **依存配列の値が変更される前**: 新しい副作用が実行される前に、前の副作用をクリーンアップ

### 実践例: タイマーのクリーンアップ

```jsx
// src/components/Timer.jsx
import { useState, useEffect } from 'react';

function Timer() {
    const [seconds, setSeconds] = useState(0);

    useEffect(() => {
        // タイマーを設定
        const intervalId = setInterval(() => {
            setSeconds(prev => prev + 1);
        }, 1000);

        // クリーンアップ関数: タイマーをクリア
        return () => {
            clearInterval(intervalId);
            console.log('タイマーをクリアしました');
        };
    }, []);  // 初回のみ実行

    return (
        <div>
            <p>経過時間: {seconds}秒</p>
        </div>
    );
}

// src/App.jsx
function App() {
    const [showTimer, setShowTimer] = useState(true);

    return (
        <div>
            {showTimer && <Timer />}
            <button onClick={() => setShowTimer(!showTimer)}>
                {showTimer ? 'タイマーを非表示' : 'タイマーを表示'}
            </button>
        </div>
    );
}

export default App;
```

### 実践例: イベントリスナーのクリーンアップ

```jsx
// src/components/WindowSize.jsx
import { useState, useEffect } from 'react';

function WindowSize() {
    const [windowSize, setWindowSize] = useState({
        width: window.innerWidth,
        height: window.innerHeight
    });

    useEffect(() => {
        // ウィンドウサイズ変更のイベントリスナーを追加
        const handleResize = () => {
            setWindowSize({
                width: window.innerWidth,
                height: window.innerHeight
            });
        };

        window.addEventListener('resize', handleResize);

        // クリーンアップ関数: イベントリスナーを削除
        return () => {
            window.removeEventListener('resize', handleResize);
            console.log('イベントリスナーを削除しました');
        };
    }, []);  // 初回のみ実行

    return (
        <div>
            <p>ウィンドウサイズ: {windowSize.width} x {windowSize.height}</p>
        </div>
    );
}

export default WindowSize;
```

### 実践例: APIリクエストのキャンセル

```jsx
// src/components/UserData.jsx
import { useState, useEffect } from 'react';

function UserData({ userId }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        let cancelled = false;  // キャンセルフラグ

        const fetchUser = async () => {
            setLoading(true);
            
            try {
                // 実際のAPI呼び出しの代わりに、模擬データを使用
                await new Promise(resolve => setTimeout(resolve, 1000));
                
                if (!cancelled) {
                    setUser({
                        id: userId,
                        name: `ユーザー${userId}`,
                        email: `user${userId}@example.com`
                    });
                    setLoading(false);
                }
            } catch (error) {
                if (!cancelled) {
                    console.error('エラー:', error);
                    setLoading(false);
                }
            }
        };

        fetchUser();

        // クリーンアップ関数: リクエストをキャンセル
        return () => {
            cancelled = true;
            console.log(`ユーザー${userId}の取得をキャンセルしました`);
        };
    }, [userId]);

    if (loading) {
        return <p>読み込み中...</p>;
    }

    return (
        <div>
            <h2>{user?.name}</h2>
            <p>メール: {user?.email}</p>
        </div>
    );
}

// src/App.jsx
function App() {
    const [userId, setUserId] = useState(1);

    return (
        <div>
            <button onClick={() => setUserId(userId === 1 ? 2 : 1)}>
                ユーザーを切り替え
            </button>
            <UserData userId={userId} />
        </div>
    );
}

export default App;
```

### 実践例: 複数のクリーンアップ処理

```jsx
// src/components/ComplexCleanup.jsx
import { useState, useEffect } from 'react';

function ComplexCleanup() {
    const [count, setCount] = useState(0);
    const [isActive, setIsActive] = useState(true);

    useEffect(() => {
        if (!isActive) return;

        // タイマー1
        const intervalId1 = setInterval(() => {
            setCount(prev => prev + 1);
        }, 1000);

        // タイマー2
        const intervalId2 = setInterval(() => {
            console.log('2秒ごとに実行');
        }, 2000);

        // イベントリスナー
        const handleKeyPress = (e) => {
            console.log('キーが押されました:', e.key);
        };
        window.addEventListener('keydown', handleKeyPress);

        // クリーンアップ関数: すべての処理をクリーンアップ
        return () => {
            clearInterval(intervalId1);
            clearInterval(intervalId2);
            window.removeEventListener('keydown', handleKeyPress);
            console.log('すべての処理をクリーンアップしました');
        };
    }, [isActive]);

    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setIsActive(!isActive)}>
                {isActive ? '停止' : '開始'}
            </button>
        </div>
    );
}

export default ComplexCleanup;
```

---

## 注意点: 無限ループと不要な実行を防ぐ

### 無限ループとは
無限ループは、**useEffectが無限に再実行される**問題です。主に依存配列の設定ミスや、useEffect内でStateを更新する際に発生します。

### 無限ループが発生する原因
1. **依存配列の設定ミス**: 依存配列に含めるべき値を含めていない、または不要な値を含めている
2. **Stateの更新**: useEffect内でStateを更新し、そのStateが依存配列に含まれている
3. **オブジェクトや配列の参照**: 毎回新しいオブジェクトや配列を作成している

### 実践例: 無限ループの発生パターン

```jsx
// ❌ 悪い例1: 依存配列なしでStateを更新
function BadExample1() {
    const [count, setCount] = useState(0);

    useEffect(() => {
        setCount(count + 1);  // 無限ループ！
    });  // 依存配列なし

    return <div>{count}</div>;
}

// ❌ 悪い例2: 依存配列に更新するStateを含める
function BadExample2() {
    const [count, setCount] = useState(0);

    useEffect(() => {
        setCount(count + 1);  // 無限ループ！
    }, [count]);  // countが依存配列に含まれている

    return <div>{count}</div>;
}

// ❌ 悪い例3: 毎回新しいオブジェクトを作成
function BadExample3() {
    const [data, setData] = useState(null);

    useEffect(() => {
        setData({ value: 1 });  // 毎回新しいオブジェクト
    }, [{ value: 1 }]);  // 毎回新しいオブジェクトが作成される

    return <div>{data?.value}</div>;
}
```

### 実践例: 無限ループの解決方法

```jsx
// ✅ 良い例1: 依存配列を適切に設定
function GoodExample1() {
    const [count, setCount] = useState(0);

    // 初回のみ実行
    useEffect(() => {
        console.log('コンポーネントがマウントされました');
    }, []);  // 空の依存配列

    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
}

// ✅ 良い例2: 関数型の更新を使用
function GoodExample2() {
    const [count, setCount] = useState(0);

    useEffect(() => {
        // 関数型の更新を使用（依存配列にcountを含めなくてよい）
        const timer = setInterval(() => {
            setCount(prev => prev + 1);  // 前の値を使用
        }, 1000);

        return () => clearInterval(timer);
    }, []);  // 空の依存配列でOK

    return <div>{count}</div>;
}

// ✅ 良い例3: useRefを使用して値を保持
import { useState, useEffect, useRef } from 'react';

function GoodExample3() {
    const [count, setCount] = useState(0);
    const countRef = useRef(0);

    useEffect(() => {
        countRef.current = count;  // 再レンダリングを起こさない
    }, [count]);

    useEffect(() => {
        const timer = setInterval(() => {
            console.log('現在のカウント:', countRef.current);
        }, 1000);

        return () => clearInterval(timer);
    }, []);  // 空の依存配列でOK

    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
}
```

### 実践例: 不要な再実行を防ぐ

```jsx
// src/components/OptimizedDataFetcher.jsx
import { useState, useEffect, useMemo } from 'react';

function OptimizedDataFetcher({ userId, filters }) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(false);

    // フィルターをメモ化（不要な再実行を防ぐ）
    const memoizedFilters = useMemo(() => {
        return {
            category: filters.category,
            sort: filters.sort
        };
    }, [filters.category, filters.sort]);

    useEffect(() => {
        setLoading(true);
        console.log('データを取得中...', userId, memoizedFilters);

        // API呼び出しの模擬
        setTimeout(() => {
            setData({
                userId,
                items: ['アイテム1', 'アイテム2']
            });
            setLoading(false);
        }, 1000);
    }, [userId, memoizedFilters]);  // メモ化された値を使用

    if (loading) {
        return <p>読み込み中...</p>;
    }

    return (
        <div>
            <h2>データ</h2>
            <pre>{JSON.stringify(data, null, 2)}</pre>
        </div>
    );
}

// src/App.jsx
function App() {
    const [userId, setUserId] = useState(1);
    const [filters, setFilters] = useState({ category: 'all', sort: 'asc' });

    return (
        <div>
            <button onClick={() => setUserId(userId === 1 ? 2 : 1)}>
                ユーザーを切り替え
            </button>
            <select 
                value={filters.category}
                onChange={(e) => setFilters({ ...filters, category: e.target.value })}
            >
                <option value="all">すべて</option>
                <option value="tech">技術</option>
            </select>
            <OptimizedDataFetcher userId={userId} filters={filters} />
        </div>
    );
}

export default App;
```

### 実践例: ESLintの警告に対処する

```jsx
// ESLintのexhaustive-depsルールの警告に対処
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
    const [user, setUser] = useState(null);
    const [apiKey] = useState('secret-key');  // 変更されない値

    useEffect(() => {
        // apiKeyは変更されないので、依存配列に含めなくても良い
        // ただし、ESLintが警告を出す可能性がある
        fetch(`/api/users/${userId}?key=${apiKey}`)
            .then(res => res.json())
            .then(setUser);
    }, [userId]);  // userIdのみを依存配列に含める

    // または、useRefを使用して値を保持
    // const apiKeyRef = useRef('secret-key');
    // useEffect(() => {
    //     fetch(`/api/users/${userId}?key=${apiKeyRef.current}`)
    //         .then(res => res.json())
    //         .then(setUser);
    // }, [userId]);

    return <div>{user?.name}</div>;
}
```

---

## まとめ

この章では、useEffectについて以下のことを学びました。

### useEffectの基本構文
- useEffectは、コンポーネントのレンダリング後に副作用を実行するフック
- API呼び出し、タイマー設定、DOM操作などに使用する
- レンダリングロジックと副作用を分離できる

### 依存配列の正しい設定
- **依存配列なし**: 毎回のレンダリング後に実行（通常は避ける）
- **空の依存配列**: 初回マウント時のみ実行
- **依存配列に値**: 指定した値が変更された時のみ実行
- 使用している値を依存配列に含めることが重要

### クリーンアップ関数
- クリーンアップ関数は、コンポーネントのアンマウント時や依存配列の値が変更される前に実行される
- タイマーのクリア、イベントリスナーの削除、APIリクエストのキャンセルなどに使用する
- メモリリークを防ぐために重要

### 無限ループと不要な実行を防ぐ
- 依存配列の設定ミスが無限ループの主な原因
- 関数型の更新を使用することで、依存配列を最小限にできる
- useRefやuseMemoを使用して、不要な再実行を防ぐ

### useEffectの活用場面
- API呼び出しとデータ取得
- タイマーやインターバルの設定
- イベントリスナーの追加と削除
- DOM操作や外部ライブラリとの連携
- ローカルストレージへの保存

### 次のステップ
useEffectを理解したら、次のトピックに進みましょう：
- DOM参照と値の保持（useRef）（3-2）
- フォーム実装とバリデーション（4章）
- パフォーマンス最適化とメモ化（6章）

---

## 演習問題

以下の問題を解いて、useEffectの理解を深めましょう。

### 問題1: 基本的なuseEffect
コンポーネントがマウントされた時に「コンポーネントがマウントされました」とコンソールに出力するuseEffectを作成してください。

<details>
<summary>解答例</summary>

```jsx
import { useEffect } from 'react';

function MyComponent() {
    useEffect(() => {
        console.log('コンポーネントがマウントされました');
    }, []);  // 空の依存配列

    return <div>コンポーネント</div>;
}

export default MyComponent;
```
</details>

### 問題2: 依存配列の使用
`userId`をPropsとして受け取り、`userId`が変更された時にユーザー情報を取得するuseEffectを作成してください。

<details>
<summary>解答例</summary>

```jsx
import { useState, useEffect } from 'react';

function UserProfile({ userId }) {
    const [user, setUser] = useState(null);

    useEffect(() => {
        console.log(`ユーザー${userId}の情報を取得中...`);
        // 実際のAPI呼び出しの代わりに、模擬データを使用
        setTimeout(() => {
            setUser({
                id: userId,
                name: `ユーザー${userId}`
            });
        }, 1000);
    }, [userId]);  // userIdが依存配列に含まれている

    if (!user) {
        return <p>読み込み中...</p>;
    }

    return <div>{user.name}</div>;
}

export default UserProfile;
```
</details>

### 問題3: クリーンアップ関数
1秒ごとにカウントを増やすタイマーを作成し、コンポーネントがアンマウントされる時にタイマーをクリアしてください。

<details>
<summary>解答例</summary>

```jsx
import { useState, useEffect } from 'react';

function Timer() {
    const [seconds, setSeconds] = useState(0);

    useEffect(() => {
        const intervalId = setInterval(() => {
            setSeconds(prev => prev + 1);
        }, 1000);

        // クリーンアップ関数
        return () => {
            clearInterval(intervalId);
        };
    }, []);  // 空の依存配列

    return <div>経過時間: {seconds}秒</div>;
}

export default Timer;
```
</details>

### 問題4: ウィンドウサイズの監視
ウィンドウサイズを監視し、サイズが変更された時に表示を更新するコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```jsx
import { useState, useEffect } from 'react';

function WindowSize() {
    const [size, setSize] = useState({
        width: window.innerWidth,
        height: window.innerHeight
    });

    useEffect(() => {
        const handleResize = () => {
            setSize({
                width: window.innerWidth,
                height: window.innerHeight
            });
        };

        window.addEventListener('resize', handleResize);

        // クリーンアップ関数
        return () => {
            window.removeEventListener('resize', handleResize);
        };
    }, []);  // 空の依存配列

    return (
        <div>
            <p>幅: {size.width}px</p>
            <p>高さ: {size.height}px</p>
        </div>
    );
}

export default WindowSize;
```
</details>

### 問題5: ローカルストレージへの保存
入力フィールドの値をローカルストレージに保存し、コンポーネントがマウントされた時に値を復元するコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```jsx
import { useState, useEffect } from 'react';

function PersistentInput() {
    const [value, setValue] = useState(() => {
        // 初回レンダリング時にローカルストレージから読み込む
        return localStorage.getItem('inputValue') || '';
    });

    useEffect(() => {
        // 値が変更された時にローカルストレージに保存
        localStorage.setItem('inputValue', value);
    }, [value]);

    return (
        <div>
            <input
                value={value}
                onChange={(e) => setValue(e.target.value)}
                placeholder="入力してください"
            />
            <p>保存された値: {value}</p>
        </div>
    );
}

export default PersistentInput;
```
</details>

---

お疲れ様でした！次の章に進みましょう。

