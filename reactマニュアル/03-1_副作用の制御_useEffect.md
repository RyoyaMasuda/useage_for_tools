# 3-1. 副作用の制御 (useEffect)

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

### useEffectの基本構文

```jsx
import { useEffect } from 'react';

function Component() {
    useEffect(() => {
        // 副作用の処理
    });
    
    return <div>コンテンツ</div>;
}
```

### 副作用とは
副作用（Side Effect）は、**コンポーネントのレンダリング以外の処理**です。例えば：
- APIからデータを取得する
- タイマーを設定する
- DOMを直接操作する
- ローカルストレージに保存する

### 実践例: useEffectの基本構文

```jsx
// src/App.jsx
import React, { useState, useEffect } from 'react';

// 例1: 基本的なuseEffect
function BasicEffect() {
    const [count, setCount] = useState(0);
    
    // レンダリングのたびに実行される
    useEffect(() => {
        console.log('コンポーネントがレンダリングされました');
        console.log(`現在のカウント: ${count}`);
    });
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}

// 例2: 初回レンダリング時のみ実行
function EffectOnce() {
    const [message, setMessage] = useState('');
    
    // 初回レンダリング時のみ実行される
    useEffect(() => {
        console.log('コンポーネントがマウントされました');
        setMessage('ようこそ！');
    }, []); // 空の依存配列
    
    return (
        <div>
            <p>{message}</p>
        </div>
    );
}

// 例3: 特定の値が変更されたときのみ実行
function EffectWithDependency() {
    const [count, setCount] = useState(0);
    const [doubleCount, setDoubleCount] = useState(0);
    
    // countが変更されたときのみ実行される
    useEffect(() => {
        console.log(`countが${count}に変更されました`);
        setDoubleCount(count * 2);
    }, [count]); // countを依存配列に指定
    
    return (
        <div>
            <p>カウント: {count}</p>
            <p>2倍: {doubleCount}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}

// 例4: APIからデータを取得
function FetchData() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);
    
    useEffect(() => {
        // 初回レンダリング時にデータを取得
        fetch('https://jsonplaceholder.typicode.com/users')
            .then(response => response.json())
            .then(data => {
                setUsers(data);
                setLoading(false);
            })
            .catch(error => {
                console.error('エラー:', error);
                setLoading(false);
            });
    }, []); // 空の依存配列で初回のみ実行
    
    if (loading) {
        return <p>読み込み中...</p>;
    }
    
    return (
        <div>
            <h2>ユーザー一覧</h2>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </div>
    );
}

// 例5: ローカルストレージへの保存
function SaveToLocalStorage() {
    const [name, setName] = useState('');
    
    // 初回レンダリング時にローカルストレージから読み込む
    useEffect(() => {
        const savedName = localStorage.getItem('name');
        if (savedName) {
            setName(savedName);
        }
    }, []);
    
    // nameが変更されたときにローカルストレージに保存
    useEffect(() => {
        if (name) {
            localStorage.setItem('name', name);
        }
    }, [name]);
    
    return (
        <div>
            <input
                type="text"
                value={name}
                onChange={(e) => setName(e.target.value)}
                placeholder="名前を入力"
            />
            <p>保存された名前: {name}</p>
        </div>
    );
}

// メインコンポーネント
function App() {
    return (
        <div className="app">
            <h1>useEffectの基本構文</h1>
            <BasicEffect />
            <hr />
            <EffectOnce />
            <hr />
            <EffectWithDependency />
            <hr />
            <FetchData />
            <hr />
            <SaveToLocalStorage />
        </div>
    );
}

export default App;
```

---

## 依存配列 (dependency array) の正しい設定

### 依存配列とは
依存配列は、**useEffectの第2引数に渡す配列**です。この配列に指定した値が変更されたときのみ、useEffectが実行されます。

### 依存配列のパターン

#### 1. 依存配列なし（毎回実行）

```jsx
useEffect(() => {
    // レンダリングのたびに実行される
    console.log('実行されました');
});
```

#### 2. 空の依存配列（初回のみ実行）

```jsx
useEffect(() => {
    // 初回レンダリング時のみ実行される
    console.log('初回のみ実行');
}, []);
```

#### 3. 依存配列に値を指定（値が変更されたときのみ実行）

```jsx
useEffect(() => {
    // countが変更されたときのみ実行される
    console.log(`count: ${count}`);
}, [count]);
```

### 依存配列の重要性

依存配列を正しく設定しないと、以下の問題が発生する可能性があります：
- **無限ループ**: 依存配列を指定しないと、毎回実行されて無限ループになる
- **古い値の参照**: 依存配列を指定しないと、古い値を参照する可能性がある
- **不要な実行**: 依存配列を正しく設定しないと、不要な処理が実行される

### 実践例: 依存配列の正しい設定

```jsx
// src/App.jsx
import React, { useState, useEffect } from 'react';

// 例1: 依存配列なし（毎回実行）
function NoDependencyArray() {
    const [count, setCount] = useState(0);
    
    // ⚠️ 注意: 毎回実行される（無限ループの可能性）
    useEffect(() => {
        console.log('毎回実行されます');
        // この中でsetCountを呼ぶと無限ループになる
    });
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}

// 例2: 空の依存配列（初回のみ実行）
function EmptyDependencyArray() {
    const [count, setCount] = useState(0);
    
    // ✅ 初回レンダリング時のみ実行される
    useEffect(() => {
        console.log('初回のみ実行されます');
    }, []);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}

// 例3: 単一の依存配列
function SingleDependency() {
    const [count, setCount] = useState(0);
    const [message, setMessage] = useState('');
    
    // ✅ countが変更されたときのみ実行される
    useEffect(() => {
        console.log(`countが${count}に変更されました`);
        setMessage(`カウントは${count}です`);
    }, [count]);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <p>{message}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}

// 例4: 複数の依存配列
function MultipleDependencies() {
    const [count, setCount] = useState(0);
    const [multiplier, setMultiplier] = useState(2);
    const [result, setResult] = useState(0);
    
    // ✅ countまたはmultiplierが変更されたときのみ実行される
    useEffect(() => {
        console.log('計算が実行されました');
        setResult(count * multiplier);
    }, [count, multiplier]);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <p>倍数: {multiplier}</p>
            <p>結果: {result}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
            <button onClick={() => setMultiplier(multiplier + 1)}>倍数を増やす</button>
        </div>
    );
}

// 例5: オブジェクトの依存配列（注意が必要）
function ObjectDependency() {
    const [user, setUser] = useState({ name: '太郎', age: 25 });
    const [message, setMessage] = useState('');
    
    // ⚠️ 注意: オブジェクトの参照が変わるたびに実行される
    useEffect(() => {
        console.log('userが変更されました');
        setMessage(`${user.name}は${user.age}歳です`);
    }, [user]); // オブジェクト全体を依存配列に指定
    
    // ✅ 推奨: 必要なプロパティのみを依存配列に指定
    useEffect(() => {
        console.log('user.nameまたはuser.ageが変更されました');
        setMessage(`${user.name}は${user.age}歳です`);
    }, [user.name, user.age]); // 個別のプロパティを指定
    
    return (
        <div>
            <p>{message}</p>
            <button onClick={() => setUser({ ...user, age: user.age + 1 })}>
                年齢を増やす
            </button>
        </div>
    );
}

// 例6: 関数の依存配列
function FunctionDependency() {
    const [count, setCount] = useState(0);
    
    const calculate = () => {
        return count * 2;
    };
    
    // ⚠️ 注意: 関数は毎回新しい参照が作成される
    useEffect(() => {
        const result = calculate();
        console.log(`結果: ${result}`);
    }, [calculate]); // 関数を依存配列に指定すると毎回実行される
    
    // ✅ 推奨: useCallbackで関数をメモ化するか、関数内で直接使用
    useEffect(() => {
        const result = count * 2;
        console.log(`結果: ${result}`);
    }, [count]); // 関数ではなく、値を使用
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}

// メインコンポーネント
function App() {
    return (
        <div className="app">
            <h1>依存配列の正しい設定</h1>
            <NoDependencyArray />
            <hr />
            <EmptyDependencyArray />
            <hr />
            <SingleDependency />
            <hr />
            <MultipleDependencies />
            <hr />
            <ObjectDependency />
            <hr />
            <FunctionDependency />
        </div>
    );
}

export default App;
```

### 依存配列のベストプラクティス

1. **必要な値のみを指定**: 使用している値は必ず依存配列に含める
2. **オブジェクトは個別に指定**: オブジェクト全体ではなく、必要なプロパティのみを指定
3. **関数はuseCallbackでメモ化**: 関数を依存配列に含める場合は、useCallbackでメモ化
4. **ESLintの警告に従う**: React HooksのESLintルールに従う

---

## クリーンアップ関数 (アンマウント時の処理)

### クリーンアップ関数とは
クリーンアップ関数は、**コンポーネントがアンマウントされる前や、依存配列の値が変更される前に実行される関数**です。タイマーのクリア、イベントリスナーの削除、APIリクエストのキャンセルなどに使用します。

### クリーンアップ関数の基本構文

```jsx
useEffect(() => {
    // 副作用の処理
    
    return () => {
        // クリーンアップ処理
    };
}, [依存配列]);
```

### クリーンアップ関数が実行されるタイミング

1. **コンポーネントのアンマウント時**: コンポーネントが削除される前
2. **依存配列の値が変更される前**: 新しい副作用が実行される前

### 実践例: クリーンアップ関数

```jsx
// src/App.jsx
import React, { useState, useEffect } from 'react';

// 例1: タイマーのクリーンアップ
function TimerExample() {
    const [seconds, setSeconds] = useState(0);
    
    useEffect(() => {
        const intervalId = setInterval(() => {
            setSeconds(prev => prev + 1);
        }, 1000);
        
        // クリーンアップ: タイマーをクリア
        return () => {
            clearInterval(intervalId);
            console.log('タイマーがクリアされました');
        };
    }, []); // 初回のみ実行
    
    return (
        <div>
            <p>経過時間: {seconds}秒</p>
        </div>
    );
}

// 例2: イベントリスナーのクリーンアップ
function EventListenerExample() {
    const [mousePosition, setMousePosition] = useState({ x: 0, y: 0 });
    
    useEffect(() => {
        const handleMouseMove = (e: MouseEvent) => {
            setMousePosition({ x: e.clientX, y: e.clientY });
        };
        
        window.addEventListener('mousemove', handleMouseMove);
        
        // クリーンアップ: イベントリスナーを削除
        return () => {
            window.removeEventListener('mousemove', handleMouseMove);
            console.log('イベントリスナーが削除されました');
        };
    }, []);
    
    return (
        <div>
            <p>マウス位置: X={mousePosition.x}, Y={mousePosition.y}</p>
        </div>
    );
}

// 例3: APIリクエストのキャンセル
function ApiRequestExample() {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    
    useEffect(() => {
        let cancelled = false;
        
        const fetchData = async () => {
            try {
                const response = await fetch('https://jsonplaceholder.typicode.com/posts/1');
                const json = await response.json();
                
                // コンポーネントがアンマウントされていない場合のみ状態を更新
                if (!cancelled) {
                    setData(json);
                    setLoading(false);
                }
            } catch (error) {
                if (!cancelled) {
                    console.error('エラー:', error);
                    setLoading(false);
                }
            }
        };
        
        fetchData();
        
        // クリーンアップ: リクエストをキャンセル
        return () => {
            cancelled = true;
            console.log('リクエストがキャンセルされました');
        };
    }, []);
    
    if (loading) return <p>読み込み中...</p>;
    if (!data) return <p>データがありません</p>;
    
    return (
        <div>
            <h2>{data.title}</h2>
            <p>{data.body}</p>
        </div>
    );
}

// 例4: 複数のクリーンアップ
function MultipleCleanups() {
    const [count, setCount] = useState(0);
    
    useEffect(() => {
        const intervalId = setInterval(() => {
            setCount(prev => prev + 1);
        }, 1000);
        
        const timeoutId = setTimeout(() => {
            console.log('5秒経過しました');
        }, 5000);
        
        // クリーンアップ: 複数の処理をクリア
        return () => {
            clearInterval(intervalId);
            clearTimeout(timeoutId);
            console.log('すべてのタイマーがクリアされました');
        };
    }, []);
    
    return (
        <div>
            <p>カウント: {count}</p>
        </div>
    );
}

// 例5: 依存配列が変更されたときのクリーンアップ
function CleanupOnDependencyChange() {
    const [userId, setUserId] = useState(1);
    const [user, setUser] = useState(null);
    
    useEffect(() => {
        let cancelled = false;
        
        const fetchUser = async () => {
            try {
                const response = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`);
                const json = await response.json();
                
                if (!cancelled) {
                    setUser(json);
                }
            } catch (error) {
                if (!cancelled) {
                    console.error('エラー:', error);
                }
            }
        };
        
        fetchUser();
        
        // クリーンアップ: userIdが変更される前やアンマウント時に実行
        return () => {
            cancelled = true;
            console.log(`ユーザー${userId}の取得がキャンセルされました`);
        };
    }, [userId]); // userIdが変更されるたびに実行
    
    return (
        <div>
            <p>ユーザーID: {userId}</p>
            <button onClick={() => setUserId(userId + 1)}>次のユーザー</button>
            {user && (
                <div>
                    <h3>{user.name}</h3>
                    <p>{user.email}</p>
                </div>
            )}
        </div>
    );
}

// メインコンポーネント
function App() {
    const [showTimer, setShowTimer] = useState(true);
    
    return (
        <div className="app">
            <h1>クリーンアップ関数</h1>
            
            <div>
                <button onClick={() => setShowTimer(!showTimer)}>
                    {showTimer ? 'タイマーを非表示' : 'タイマーを表示'}
                </button>
                {showTimer && <TimerExample />}
            </div>
            
            <hr />
            <EventListenerExample />
            <hr />
            <ApiRequestExample />
            <hr />
            <MultipleCleanups />
            <hr />
            <CleanupOnDependencyChange />
        </div>
    );
}

export default App;
```

---

## 注意点: 無限ループと不要な実行を防ぐ

### 無限ループとは
無限ループは、**useEffectが実行されるたびに状態を更新し、それが再レンダリングを引き起こし、またuseEffectが実行される**という繰り返しです。

### 無限ループが発生する原因

#### 1. 依存配列を指定しない

```jsx
// ⚠️ 無限ループが発生する例
function InfiniteLoop() {
    const [count, setCount] = useState(0);
    
    useEffect(() => {
        setCount(count + 1); // 毎回実行されるため無限ループ
    }); // 依存配列なし
    
    return <div>{count}</div>;
}
```

#### 2. 依存配列に状態更新関数を含める

```jsx
// ⚠️ 無限ループが発生する例
function InfiniteLoop2() {
    const [count, setCount] = useState(0);
    
    useEffect(() => {
        setCount(prev => prev + 1);
    }, [count]); // countが変更されるたびに実行され、またcountを更新する
    
    return <div>{count}</div>;
}
```

#### 3. オブジェクトや配列を依存配列に含める

```jsx
// ⚠️ 無限ループが発生する可能性がある例
function InfiniteLoop3() {
    const [user, setUser] = useState({ name: '太郎', age: 25 });
    
    useEffect(() => {
        setUser({ ...user, age: user.age + 1 });
    }, [user]); // userオブジェクトの参照が変わるたびに実行
    
    return <div>{user.name}</div>;
}
```

### 無限ループを防ぐ方法

#### 1. 依存配列を正しく設定する

```jsx
// ✅ 正しい: 依存配列を空にする
useEffect(() => {
    // 初回のみ実行
}, []);

// ✅ 正しい: 必要な値のみを依存配列に含める
useEffect(() => {
    // countが変更されたときのみ実行
}, [count]);
```

#### 2. 関数型の更新を使用する

```jsx
// ✅ 正しい: 関数型の更新を使用
useEffect(() => {
    setCount(prev => prev + 1);
}, []); // 依存配列を空にして初回のみ実行
```

#### 3. useCallbackで関数をメモ化する

```jsx
import { useCallback } from 'react';

const handleClick = useCallback(() => {
    // 処理
}, []); // 依存配列を空にして関数をメモ化

useEffect(() => {
    handleClick();
}, [handleClick]); // メモ化された関数を使用
```

### 実践例: 無限ループと不要な実行を防ぐ

```jsx
// src/App.jsx
import React, { useState, useEffect, useCallback, useMemo } from 'react';

// 例1: 無限ループを防ぐ（依存配列を空にする）
function PreventInfiniteLoop1() {
    const [count, setCount] = useState(0);
    
    // ✅ 正しい: 依存配列を空にして初回のみ実行
    useEffect(() => {
        console.log('初回のみ実行されます');
        // 関数型の更新を使用して、依存配列にcountを含めない
        const intervalId = setInterval(() => {
            setCount(prev => prev + 1);
        }, 1000);
        
        return () => clearInterval(intervalId);
    }, []); // 空の依存配列
    
    return <div>カウント: {count}</div>;
}

// 例2: 無限ループを防ぐ（必要な値のみを依存配列に含める）
function PreventInfiniteLoop2() {
    const [count, setCount] = useState(0);
    const [multiplier, setMultiplier] = useState(2);
    const [result, setResult] = useState(0);
    
    // ✅ 正しい: 必要な値のみを依存配列に含める
    useEffect(() => {
        setResult(count * multiplier);
    }, [count, multiplier]); // countとmultiplierのみを指定
    
    return (
        <div>
            <p>カウント: {count}</p>
            <p>倍数: {multiplier}</p>
            <p>結果: {result}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
            <button onClick={() => setMultiplier(multiplier + 1)}>倍数を増やす</button>
        </div>
    );
}

// 例3: オブジェクトの依存配列を適切に設定
function PreventInfiniteLoop3() {
    const [user, setUser] = useState({ name: '太郎', age: 25 });
    
    // ✅ 正しい: 個別のプロパティを依存配列に指定
    useEffect(() => {
        console.log(`ユーザー: ${user.name}, 年齢: ${user.age}`);
    }, [user.name, user.age]); // オブジェクト全体ではなく、個別のプロパティ
    
    return (
        <div>
            <p>{user.name} ({user.age}歳)</p>
            <button onClick={() => setUser({ ...user, age: user.age + 1 })}>
                年齢を増やす
            </button>
        </div>
    );
}

// 例4: useCallbackで関数をメモ化
function PreventInfiniteLoop4() {
    const [count, setCount] = useState(0);
    const [message, setMessage] = useState('');
    
    // ✅ 正しい: useCallbackで関数をメモ化
    const updateMessage = useCallback(() => {
        setMessage(`カウントは${count}です`);
    }, [count]); // countが変更されたときのみ関数を再作成
    
    useEffect(() => {
        updateMessage();
    }, [updateMessage]); // メモ化された関数を使用
    
    return (
        <div>
            <p>カウント: {count}</p>
            <p>{message}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}

// 例5: useMemoで値をメモ化
function PreventInfiniteLoop5() {
    const [count, setCount] = useState(0);
    const [multiplier, setMultiplier] = useState(2);
    
    // ✅ 正しい: useMemoで計算結果をメモ化
    const result = useMemo(() => {
        console.log('計算が実行されました');
        return count * multiplier;
    }, [count, multiplier]); // countまたはmultiplierが変更されたときのみ再計算
    
    useEffect(() => {
        console.log(`結果: ${result}`);
    }, [result]); // メモ化された値を使用
    
    return (
        <div>
            <p>カウント: {count}</p>
            <p>倍数: {multiplier}</p>
            <p>結果: {result}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
            <button onClick={() => setMultiplier(multiplier + 1)}>倍数を増やす</button>
        </div>
    );
}

// 例6: 条件付き実行
function ConditionalExecution() {
    const [count, setCount] = useState(0);
    const [enabled, setEnabled] = useState(true);
    
    // ✅ 正しい: 条件付きで実行
    useEffect(() => {
        if (!enabled) return; // 条件をチェック
        
        const intervalId = setInterval(() => {
            setCount(prev => prev + 1);
        }, 1000);
        
        return () => clearInterval(intervalId);
    }, [enabled]); // enabledが変更されたときのみ実行
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setEnabled(!enabled)}>
                {enabled ? '停止' : '開始'}
            </button>
        </div>
    );
}

// メインコンポーネント
function App() {
    return (
        <div className="app">
            <h1>無限ループと不要な実行を防ぐ</h1>
            <PreventInfiniteLoop1 />
            <hr />
            <PreventInfiniteLoop2 />
            <hr />
            <PreventInfiniteLoop3 />
            <hr />
            <PreventInfiniteLoop4 />
            <hr />
            <PreventInfiniteLoop5 />
            <hr />
            <ConditionalExecution />
        </div>
    );
}

export default App;
```

### ベストプラクティス

1. **依存配列を必ず設定**: 依存配列を設定して、不要な実行を防ぐ
2. **ESLintの警告に従う**: React HooksのESLintルールに従う
3. **関数型の更新を使用**: 状態更新で関数型の更新を使用する
4. **useCallback/useMemoを活用**: 関数や値をメモ化して、不要な再実行を防ぐ
5. **条件付き実行**: 条件をチェックして、必要なときのみ実行する

---

## まとめ

この章では、ReactのuseEffectフックについて学びました。

### 学んだこと
- **useEffectの基本構文**: レンダリング後に副作用を実行
- **依存配列**: 副作用の実行タイミングを制御
- **クリーンアップ関数**: アンマウント時や依存配列の値が変更される前の処理
- **無限ループの防止**: 依存配列の正しい設定とベストプラクティス

### 重要なポイント
1. **依存配列**: 副作用の実行タイミングを制御する
2. **空の依存配列**: 初回レンダリング時のみ実行
3. **クリーンアップ関数**: タイマーやイベントリスナーのクリーンアップ
4. **無限ループ**: 依存配列を正しく設定して防ぐ
5. **ベストプラクティス**: ESLintの警告に従い、適切なパターンを使用

### 使い分けの目安
- **依存配列なし**: 毎回実行が必要な場合（通常は避ける）
- **空の依存配列**: 初回のみ実行（API呼び出し、イベントリスナーの登録など）
- **依存配列に値を指定**: 値が変更されたときのみ実行

### 次のステップ
useEffectを理解することで、API呼び出し、タイマー、DOM操作などの副作用を適切に管理できるようになります。次の章では、DOM参照と値の保持（useRef）について詳しく学びます。

---

## 演習問題

### 問題1: 基本的なuseEffect
useEffectを使って、コンポーネントがマウントされたときに「コンポーネントがマウントされました」とコンソールに出力してください。

<details>
<summary>解答例</summary>

```jsx
import { useEffect } from 'react';

function App() {
    useEffect(() => {
        console.log('コンポーネントがマウントされました');
    }, []);
    
    return <div>アプリ</div>;
}
```
</details>

### 問題2: 依存配列の設定
countが変更されたときに、コンソールにcountの値を出力してください。

<details>
<summary>解答例</summary>

```jsx
import { useState, useEffect } from 'react';

function App() {
    const [count, setCount] = useState(0);
    
    useEffect(() => {
        console.log(`count: ${count}`);
    }, [count]);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}
```
</details>

### 問題3: クリーンアップ関数
タイマーを設定し、コンポーネントがアンマウントされたときにタイマーをクリアしてください。

<details>
<summary>解答例</summary>

```jsx
import { useState, useEffect } from 'react';

function App() {
    const [seconds, setSeconds] = useState(0);
    
    useEffect(() => {
        const intervalId = setInterval(() => {
            setSeconds(prev => prev + 1);
        }, 1000);
        
        return () => {
            clearInterval(intervalId);
        };
    }, []);
    
    return <div>経過時間: {seconds}秒</div>;
}
```
</details>

### 問題4: APIからデータを取得
useEffectを使って、APIからデータを取得して表示してください。

<details>
<summary>解答例</summary>

```jsx
import { useState, useEffect } from 'react';

function App() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);
    
    useEffect(() => {
        fetch('https://jsonplaceholder.typicode.com/users')
            .then(response => response.json())
            .then(data => {
                setUsers(data);
                setLoading(false);
            })
            .catch(error => {
                console.error('エラー:', error);
                setLoading(false);
            });
    }, []);
    
    if (loading) return <div>読み込み中...</div>;
    
    return (
        <ul>
            {users.map(user => (
                <li key={user.id}>{user.name}</li>
            ))}
        </ul>
    );
}
```
</details>

### 問題5: 無限ループの防止
無限ループが発生しないように、useEffectを正しく設定してください。

<details>
<summary>解答例</summary>

```jsx
import { useState, useEffect } from 'react';

function App() {
    const [count, setCount] = useState(0);
    
    // ✅ 正しい: 依存配列を空にして初回のみ実行
    useEffect(() => {
        const intervalId = setInterval(() => {
            setCount(prev => prev + 1); // 関数型の更新を使用
        }, 1000);
        
        return () => clearInterval(intervalId);
    }, []); // 空の依存配列
    
    return <div>カウント: {count}</div>;
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。


