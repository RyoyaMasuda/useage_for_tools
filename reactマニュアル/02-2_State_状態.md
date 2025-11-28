# 2-2. State (状態)

この章では、Reactの「State（状態）」について学びます。Stateは、コンポーネント内で変化するデータを管理するための重要な機能です。Stateを理解することで、インタラクティブなアプリケーションを作成できるようになります。

---

## 目次

- [useState フックの基本](#usestate-フックの基本)
- [状態の更新と再レンダリングの仕組み](#状態の更新と再レンダリングの仕組み)
- [オブジェクトや配列のState更新 (スプレッド構文, Immer)](#オブジェクトや配列のstate更新-スプレッド構文-immer)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## useState フックの基本

### Stateとは
State（状態）は、**コンポーネント内で変化するデータを管理する**ための機能です。Stateが変更されると、Reactは自動的にコンポーネントを再レンダリングします。

### 日常生活での例
- **電灯のスイッチ**: ON/OFFの状態が変わる
- **温度計**: 温度の値が変わる
- **カウンター**: 数字が増えたり減ったりする
- **ショッピングカート**: 商品の数が変わる

### useStateフックとは
`useState`は、**コンポーネントにStateを追加する**ためのReactフックです。フックは、関数コンポーネントでReactの機能を使うための関数です。

### useStateの基本構文

```javascript
import { useState } from 'react';

const [状態の値, 状態を更新する関数] = useState(初期値);
```

### 基本的なuseStateの使用

```javascript
import { useState } from 'react';

function Counter() {
    const [count, setCount] = useState(0);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>増やす</button>
        </div>
    );
}
```

### useStateの特徴
1. **初期値の設定**: `useState(初期値)`で初期値を設定
2. **配列の分割代入**: `[値, 更新関数]`の形式で取得
3. **更新関数**: Stateを更新するための関数が自動的に作成される
4. **再レンダリング**: Stateが更新されると自動的に再レンダリング

### 実践例: useStateの基本

```javascript
// src/components/Counter.jsx
import { useState } from 'react';

function Counter() {
    // 数値のState
    const [count, setCount] = useState(0);
    
    const increment = () => {
        setCount(count + 1);
    };
    
    const decrement = () => {
        setCount(count - 1);
    };
    
    const reset = () => {
        setCount(0);
    };
    
    return (
        <div>
            <h2>カウンター</h2>
            <p>現在の値: {count}</p>
            <button onClick={increment}>+1</button>
            <button onClick={decrement}>-1</button>
            <button onClick={reset}>リセット</button>
        </div>
    );
}

export default Counter;
```

### 実践例: 文字列のState

```javascript
// src/components/Greeting.jsx
import { useState } from 'react';

function Greeting() {
    const [name, setName] = useState('');
    
    const handleChange = (e) => {
        setName(e.target.value);
    };
    
    return (
        <div>
            <h2>挨拶</h2>
            <input 
                type="text" 
                value={name} 
                onChange={handleChange}
                placeholder="名前を入力してください"
            />
            {name && <p>こんにちは、{name}さん！</p>}
        </div>
    );
}

export default Greeting;
```

### 実践例: 真偽値のState

```javascript
// src/components/Toggle.jsx
import { useState } from 'react';

function Toggle() {
    const [isOn, setIsOn] = useState(false);
    
    const toggle = () => {
        setIsOn(!isOn);
    };
    
    return (
        <div>
            <h2>スイッチ</h2>
            <p>状態: {isOn ? 'ON' : 'OFF'}</p>
            <button onClick={toggle}>
                {isOn ? 'OFFにする' : 'ONにする'}
            </button>
            {isOn && <p>💡 電灯が点いています</p>}
        </div>
    );
}

export default Toggle;
```

### 実践例: 複数のState

```javascript
// src/components/UserForm.jsx
import { useState } from 'react';

function UserForm() {
    const [name, setName] = useState('');
    const [age, setAge] = useState(0);
    const [email, setEmail] = useState('');
    
    const handleSubmit = (e) => {
        e.preventDefault();
        console.log('送信:', { name, age, email });
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <h2>ユーザー情報</h2>
            <div>
                <label>
                    名前:
                    <input 
                        type="text" 
                        value={name} 
                        onChange={(e) => setName(e.target.value)}
                    />
                </label>
            </div>
            <div>
                <label>
                    年齢:
                    <input 
                        type="number" 
                        value={age} 
                        onChange={(e) => setAge(Number(e.target.value))}
                    />
                </label>
            </div>
            <div>
                <label>
                    メール:
                    <input 
                        type="email" 
                        value={email} 
                        onChange={(e) => setEmail(e.target.value)}
                    />
                </label>
            </div>
            <button type="submit">送信</button>
        </form>
    );
}

export default UserForm;
```

### useStateのルール
1. **トップレベルで呼び出す**: 条件分岐やループの中では呼び出せない
2. **関数コンポーネント内で使用**: クラスコンポーネントでは使用できない
3. **カスタムフック内でも使用可能**: カスタムフックを作成できる

### 実践例: useStateのルール

```javascript
// ✅ 正しい使い方
function Component() {
    const [count, setCount] = useState(0);
    // ...
}

// ❌ 間違った使い方（条件分岐の中）
function Component() {
    if (true) {
        const [count, setCount] = useState(0); // エラー
    }
}

// ❌ 間違った使い方（ループの中）
function Component() {
    for (let i = 0; i < 10; i++) {
        const [count, setCount] = useState(0); // エラー
    }
}
```

---

## 状態の更新と再レンダリングの仕組み

### 再レンダリングとは
再レンダリングは、**Stateが更新されたときに、Reactがコンポーネントを再描画する**仕組みです。

### 再レンダリングの流れ
1. **Stateの更新**: `setState`関数を呼び出す
2. **再レンダリング**: Reactがコンポーネントを再実行
3. **画面の更新**: 変更された部分だけが更新される

### 状態更新の基本

```javascript
// 直接値を設定
setCount(10);

// 前の値を使って更新
setCount(count + 1);

// 関数形式で更新（推奨）
setCount(prevCount => prevCount + 1);
```

### 実践例: 状態の更新方法

```javascript
// src/components/UpdateExample.jsx
import { useState } from 'react';

function UpdateExample() {
    const [count, setCount] = useState(0);
    
    // 方法1: 直接値を設定
    const setToTen = () => {
        setCount(10);
    };
    
    // 方法2: 前の値を使って更新
    const increment = () => {
        setCount(count + 1);
    };
    
    // 方法3: 関数形式で更新（推奨）
    const incrementWithFunction = () => {
        setCount(prevCount => prevCount + 1);
    };
    
    // 方法4: 複数回の更新（関数形式が必要）
    const incrementTwice = () => {
        // ❌ これは正しく動作しない（同じ値を使うため）
        // setCount(count + 1);
        // setCount(count + 1);
        
        // ✅ 関数形式を使うと正しく動作する
        setCount(prevCount => prevCount + 1);
        setCount(prevCount => prevCount + 1);
    };
    
    return (
        <div>
            <h2>更新方法の例</h2>
            <p>カウント: {count}</p>
            <button onClick={setToTen}>10に設定</button>
            <button onClick={increment}>+1（直接）</button>
            <button onClick={incrementWithFunction}>+1（関数形式）</button>
            <button onClick={incrementTwice}>+2（関数形式）</button>
        </div>
    );
}

export default UpdateExample;
```

### 関数形式の更新が推奨される理由

関数形式（`prevState => newState`）を使うと、**最新のStateの値を使って更新**できます。特に、複数回の更新や、非同期処理の後に更新する場合に重要です。

### 実践例: 関数形式の重要性

```javascript
// src/components/FunctionUpdate.jsx
import { useState } from 'react';

function FunctionUpdate() {
    const [count, setCount] = useState(0);
    
    // ❌ 問題のあるコード
    const badIncrement = () => {
        setTimeout(() => {
            setCount(count + 1); // 古いcountの値を使う可能性がある
        }, 1000);
    };
    
    // ✅ 正しいコード
    const goodIncrement = () => {
        setTimeout(() => {
            setCount(prevCount => prevCount + 1); // 最新の値を使う
        }, 1000);
    };
    
    // ✅ 複数回の更新も正しく動作
    const incrementMultiple = () => {
        setCount(prevCount => prevCount + 1);
        setCount(prevCount => prevCount + 1);
        setCount(prevCount => prevCount + 1);
        // 結果: +3される
    };
    
    return (
        <div>
            <h2>関数形式の更新</h2>
            <p>カウント: {count}</p>
            <button onClick={badIncrement}>+1（問題あり）</button>
            <button onClick={goodIncrement}>+1（正しい）</button>
            <button onClick={incrementMultiple}>+3（関数形式）</button>
        </div>
    );
}

export default FunctionUpdate;
```

### 再レンダリングの最適化

Reactは、**変更された部分だけを更新**します。すべてを再描画するわけではありません。

### 実践例: 再レンダリングの確認

```javascript
// src/components/RerenderExample.jsx
import { useState } from 'react';

function RerenderExample() {
    const [count, setCount] = useState(0);
    const [name, setName] = useState('太郎');
    
    console.log('コンポーネントが再レンダリングされました');
    
    return (
        <div>
            <h2>再レンダリングの例</h2>
            <p>カウント: {count}</p>
            <p>名前: {name}</p>
            <button onClick={() => setCount(count + 1)}>
                カウントを増やす
            </button>
            <button onClick={() => setName(name === '太郎' ? '花子' : '太郎')}>
                名前を変更
            </button>
            <p>※コンソールを開いて、再レンダリングを確認してください</p>
        </div>
    );
}

export default RerenderExample;
```

### 実践例: 条件付きレンダリングとState

```javascript
// src/components/ConditionalRender.jsx
import { useState } from 'react';

function ConditionalRender() {
    const [isLoggedIn, setIsLoggedIn] = useState(false);
    const [username, setUsername] = useState('');
    
    const handleLogin = () => {
        setIsLoggedIn(true);
    };
    
    const handleLogout = () => {
        setIsLoggedIn(false);
        setUsername('');
    };
    
    return (
        <div>
            <h2>ログイン状態</h2>
            {isLoggedIn ? (
                <div>
                    <p>ようこそ、{username || 'ゲスト'}さん！</p>
                    <button onClick={handleLogout}>ログアウト</button>
                </div>
            ) : (
                <div>
                    <input 
                        type="text" 
                        placeholder="ユーザー名"
                        value={username}
                        onChange={(e) => setUsername(e.target.value)}
                    />
                    <button onClick={handleLogin}>ログイン</button>
                </div>
            )}
        </div>
    );
}

export default ConditionalRender;
```

---

## オブジェクトや配列のState更新 (スプレッド構文, Immer)

### オブジェクトのState更新

オブジェクトのStateを更新する場合、**既存のオブジェクトを変更せず、新しいオブジェクトを作成**する必要があります。

### スプレッド構文を使った更新

```javascript
const [user, setUser] = useState({ name: '太郎', age: 25 });

// オブジェクトの更新
setUser({ ...user, age: 26 });
```

### 実践例: オブジェクトのState更新

```javascript
// src/components/ObjectState.jsx
import { useState } from 'react';

function ObjectState() {
    const [user, setUser] = useState({
        name: '太郎',
        age: 25,
        email: 'taro@example.com'
    });
    
    // 名前を更新
    const updateName = (newName) => {
        setUser({ ...user, name: newName });
    };
    
    // 年齢を更新
    const updateAge = (newAge) => {
        setUser({ ...user, age: newAge });
    };
    
    // メールを更新
    const updateEmail = (newEmail) => {
        setUser({ ...user, email: newEmail });
    };
    
    // 複数のプロパティを一度に更新
    const updateMultiple = () => {
        setUser({
            ...user,
            name: '花子',
            age: 30
        });
    };
    
    return (
        <div>
            <h2>オブジェクトのState</h2>
            <p>名前: {user.name}</p>
            <p>年齢: {user.age}歳</p>
            <p>メール: {user.email}</p>
            <div>
                <input 
                    type="text" 
                    placeholder="名前"
                    value={user.name}
                    onChange={(e) => updateName(e.target.value)}
                />
                <input 
                    type="number" 
                    placeholder="年齢"
                    value={user.age}
                    onChange={(e) => updateAge(Number(e.target.value))}
                />
                <input 
                    type="email" 
                    placeholder="メール"
                    value={user.email}
                    onChange={(e) => updateEmail(e.target.value)}
                />
                <button onClick={updateMultiple}>複数更新</button>
            </div>
        </div>
    );
}

export default ObjectState;
```

### ネストしたオブジェクトの更新

ネストしたオブジェクトを更新する場合も、スプレッド構文を複数回使います。

### 実践例: ネストしたオブジェクトの更新

```javascript
// src/components/NestedObject.jsx
import { useState } from 'react';

function NestedObject() {
    const [user, setUser] = useState({
        name: '太郎',
        address: {
            street: '1-2-3 サンプル町',
            city: '東京',
            zipCode: '123-4567'
        },
        contact: {
            email: 'taro@example.com',
            phone: '090-1234-5678'
        }
    });
    
    // 住所の街を更新
    const updateCity = (newCity) => {
        setUser({
            ...user,
            address: {
                ...user.address,
                city: newCity
            }
        });
    };
    
    // 連絡先のメールを更新
    const updateEmail = (newEmail) => {
        setUser({
            ...user,
            contact: {
                ...user.contact,
                email: newEmail
            }
        });
    };
    
    return (
        <div>
            <h2>ネストしたオブジェクト</h2>
            <p>名前: {user.name}</p>
            <p>住所: {user.address.city} {user.address.street}</p>
            <p>メール: {user.contact.email}</p>
            <div>
                <input 
                    type="text" 
                    placeholder="都市"
                    value={user.address.city}
                    onChange={(e) => updateCity(e.target.value)}
                />
                <input 
                    type="email" 
                    placeholder="メール"
                    value={user.contact.email}
                    onChange={(e) => updateEmail(e.target.value)}
                />
            </div>
        </div>
    );
}

export default NestedObject;
```

### 配列のState更新

配列のStateを更新する場合も、**既存の配列を変更せず、新しい配列を作成**する必要があります。

### 実践例: 配列のState更新

```javascript
// src/components/ArrayState.jsx
import { useState } from 'react';

function ArrayState() {
    const [items, setItems] = useState(['りんご', 'バナナ', 'オレンジ']);
    const [newItem, setNewItem] = useState('');
    
    // 要素を追加
    const addItem = () => {
        if (newItem.trim()) {
            setItems([...items, newItem]);
            setNewItem('');
        }
    };
    
    // 要素を削除
    const removeItem = (index) => {
        setItems(items.filter((_, i) => i !== index));
    };
    
    // 要素を更新
    const updateItem = (index, newValue) => {
        setItems(items.map((item, i) => i === index ? newValue : item));
    };
    
    // 要素を並び替え（先頭に移動）
    const moveToTop = (index) => {
        const newItems = [...items];
        const [movedItem] = newItems.splice(index, 1);
        setItems([movedItem, ...newItems]);
    };
    
    return (
        <div>
            <h2>配列のState</h2>
            <ul>
                {items.map((item, index) => (
                    <li key={index}>
                        {item}
                        <button onClick={() => removeItem(index)}>削除</button>
                        <button onClick={() => updateItem(index, '更新済み')}>
                            更新
                        </button>
                        <button onClick={() => moveToTop(index)}>
                            先頭に移動
                        </button>
                    </li>
                ))}
            </ul>
            <div>
                <input 
                    type="text" 
                    value={newItem}
                    onChange={(e) => setNewItem(e.target.value)}
                    placeholder="新しいアイテム"
                />
                <button onClick={addItem}>追加</button>
            </div>
        </div>
    );
}

export default ArrayState;
```

### 実践例: オブジェクトの配列

```javascript
// src/components/ObjectArray.jsx
import { useState } from 'react';

function ObjectArray() {
    const [users, setUsers] = useState([
        { id: 1, name: '太郎', age: 25 },
        { id: 2, name: '花子', age: 30 },
        { id: 3, name: '次郎', age: 28 }
    ]);
    
    // ユーザーを追加
    const addUser = () => {
        const newUser = {
            id: Date.now(),
            name: '新規ユーザー',
            age: 20
        };
        setUsers([...users, newUser]);
    };
    
    // ユーザーを削除
    const removeUser = (id) => {
        setUsers(users.filter(user => user.id !== id));
    };
    
    // ユーザーの年齢を更新
    const updateAge = (id, newAge) => {
        setUsers(users.map(user => 
            user.id === id ? { ...user, age: newAge } : user
        ));
    };
    
    return (
        <div>
            <h2>ユーザーリスト</h2>
            <ul>
                {users.map(user => (
                    <li key={user.id}>
                        {user.name} ({user.age}歳)
                        <button onClick={() => removeUser(user.id)}>
                            削除
                        </button>
                        <button onClick={() => updateAge(user.id, user.age + 1)}>
                            年齢+1
                        </button>
                    </li>
                ))}
            </ul>
            <button onClick={addUser}>ユーザーを追加</button>
        </div>
    );
}

export default ObjectArray;
```

### Immerを使った更新（高度）

Immerは、**不変性を保ちながら、より直感的にStateを更新**できるライブラリです。複雑なネストしたオブジェクトや配列の更新が簡単になります。

### Immerのインストール

```bash
npm install immer
```

### Immerの基本構文

```javascript
import { useImmer } from 'use-immer';

const [state, updateState] = useImmer(初期値);

// 更新（直接変更するように書ける）
updateState(draft => {
    draft.property = newValue;
});
```

### 実践例: Immerを使った更新

```javascript
// src/components/ImmerExample.jsx
import { useImmer } from 'use-immer';

function ImmerExample() {
    const [user, updateUser] = useImmer({
        name: '太郎',
        age: 25,
        address: {
            street: '1-2-3 サンプル町',
            city: '東京',
            zipCode: '123-4567'
        },
        hobbies: ['読書', '映画']
    });
    
    // 名前を更新（Immerを使うと簡単）
    const updateName = (newName) => {
        updateUser(draft => {
            draft.name = newName;
        });
    };
    
    // 住所の都市を更新（ネストしていても簡単）
    const updateCity = (newCity) => {
        updateUser(draft => {
            draft.address.city = newCity;
        });
    };
    
    // 趣味を追加（配列の操作も簡単）
    const addHobby = (hobby) => {
        updateUser(draft => {
            draft.hobbies.push(hobby);
        });
    };
    
    return (
        <div>
            <h2>Immerの例</h2>
            <p>名前: {user.name}</p>
            <p>年齢: {user.age}歳</p>
            <p>住所: {user.address.city} {user.address.street}</p>
            <p>趣味: {user.hobbies.join(', ')}</p>
            <div>
                <input 
                    type="text" 
                    placeholder="名前"
                    value={user.name}
                    onChange={(e) => updateName(e.target.value)}
                />
                <input 
                    type="text" 
                    placeholder="都市"
                    value={user.address.city}
                    onChange={(e) => updateCity(e.target.value)}
                />
                <button onClick={() => addHobby('新しい趣味')}>
                    趣味を追加
                </button>
            </div>
        </div>
    );
}

export default ImmerExample;
```

### スプレッド構文 vs Immer

| 方法 | メリット | デメリット |
|------|---------|-----------|
| **スプレッド構文** | 標準機能、追加ライブラリ不要 | ネストが深いと複雑になる |
| **Immer** | 直感的に書ける、ネストが深くても簡単 | 追加ライブラリが必要 |

### 実践例: 複雑なStateの更新

```javascript
// src/components/ComplexState.jsx
import { useState } from 'react';

function ComplexState() {
    const [formData, setFormData] = useState({
        personalInfo: {
            name: '',
            age: 0
        },
        contactInfo: {
            email: '',
            phone: ''
        },
        preferences: {
            theme: 'light',
            notifications: true
        }
    });
    
    // スプレッド構文を使った更新
    const updatePersonalInfo = (field, value) => {
        setFormData({
            ...formData,
            personalInfo: {
                ...formData.personalInfo,
                [field]: value
            }
        });
    };
    
    const updateContactInfo = (field, value) => {
        setFormData({
            ...formData,
            contactInfo: {
                ...formData.contactInfo,
                [field]: value
            }
        });
    };
    
    const toggleNotifications = () => {
        setFormData({
            ...formData,
            preferences: {
                ...formData.preferences,
                notifications: !formData.preferences.notifications
            }
        });
    };
    
    return (
        <div>
            <h2>複雑なState</h2>
            <div>
                <h3>個人情報</h3>
                <input 
                    type="text" 
                    placeholder="名前"
                    value={formData.personalInfo.name}
                    onChange={(e) => updatePersonalInfo('name', e.target.value)}
                />
                <input 
                    type="number" 
                    placeholder="年齢"
                    value={formData.personalInfo.age}
                    onChange={(e) => updatePersonalInfo('age', Number(e.target.value))}
                />
            </div>
            <div>
                <h3>連絡先</h3>
                <input 
                    type="email" 
                    placeholder="メール"
                    value={formData.contactInfo.email}
                    onChange={(e) => updateContactInfo('email', e.target.value)}
                />
                <input 
                    type="tel" 
                    placeholder="電話"
                    value={formData.contactInfo.phone}
                    onChange={(e) => updateContactInfo('phone', e.target.value)}
                />
            </div>
            <div>
                <h3>設定</h3>
                <label>
                    <input 
                        type="checkbox" 
                        checked={formData.preferences.notifications}
                        onChange={toggleNotifications}
                    />
                    通知を受け取る
                </label>
            </div>
        </div>
    );
}

export default ComplexState;
```

---

## まとめ

この章では、ReactのState（状態）について学びました。

### 学んだこと
- **useStateフック**: コンポーネントにStateを追加する
- **状態の更新**: `setState`関数でStateを更新する
- **再レンダリング**: Stateが更新されると自動的に再レンダリングされる
- **関数形式の更新**: 最新のStateの値を使って更新する
- **オブジェクトの更新**: スプレッド構文で新しいオブジェクトを作成する
- **配列の更新**: スプレッド構文や配列メソッドで新しい配列を作成する
- **Immer**: 複雑なStateの更新を簡単にするライブラリ

### 重要なポイント
1. **不変性**: Stateは直接変更せず、新しい値を作成する
2. **関数形式**: 複数回の更新や非同期処理では関数形式を使う
3. **スプレッド構文**: オブジェクトや配列をコピーして更新する
4. **再レンダリング**: Stateが更新されると自動的に再レンダリングされる

### State更新のベストプラクティス
- **関数形式を使う**: 前の値に依存する更新では関数形式を使う
- **不変性を保つ**: 既存のStateを変更せず、新しい値を作成する
- **適切な分割**: 関連のないStateは別々に管理する
- **Immerの活用**: 複雑なネストがある場合はImmerを検討する

### 次のステップ
次の章では、イベントハンドリングについて詳しく学びます。

---

## 演習問題

### 問題1: 基本的なカウンター
`useState`を使って、カウントを増減させるカウンターコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```javascript
import { useState } from 'react';

function Counter() {
    const [count, setCount] = useState(0);
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={() => setCount(count + 1)}>+1</button>
            <button onClick={() => setCount(count - 1)}>-1</button>
            <button onClick={() => setCount(0)}>リセット</button>
        </div>
    );
}

export default Counter;
```
</details>

### 問題2: 入力フィールドとState
名前を入力して表示するコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```javascript
import { useState } from 'react';

function NameInput() {
    const [name, setName] = useState('');
    
    return (
        <div>
            <input 
                type="text" 
                value={name}
                onChange={(e) => setName(e.target.value)}
                placeholder="名前を入力"
            />
            {name && <p>こんにちは、{name}さん！</p>}
        </div>
    );
}

export default NameInput;
```
</details>

### 問題3: 関数形式の更新
関数形式を使って、カウントを3回増やすボタンを作成してください。

<details>
<summary>解答例</summary>

```javascript
import { useState } from 'react';

function IncrementThree() {
    const [count, setCount] = useState(0);
    
    const incrementThree = () => {
        setCount(prevCount => prevCount + 1);
        setCount(prevCount => prevCount + 1);
        setCount(prevCount => prevCount + 1);
    };
    
    return (
        <div>
            <p>カウント: {count}</p>
            <button onClick={incrementThree}>+3</button>
        </div>
    );
}

export default IncrementThree;
```
</details>

### 問題4: オブジェクトのState更新
ユーザー情報（名前、年齢、メール）を管理するコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```javascript
import { useState } from 'react';

function UserInfo() {
    const [user, setUser] = useState({
        name: '',
        age: 0,
        email: ''
    });
    
    return (
        <div>
            <input 
                type="text" 
                placeholder="名前"
                value={user.name}
                onChange={(e) => setUser({ ...user, name: e.target.value })}
            />
            <input 
                type="number" 
                placeholder="年齢"
                value={user.age}
                onChange={(e) => setUser({ ...user, age: Number(e.target.value) })}
            />
            <input 
                type="email" 
                placeholder="メール"
                value={user.email}
                onChange={(e) => setUser({ ...user, email: e.target.value })}
            />
            <div>
                <p>名前: {user.name}</p>
                <p>年齢: {user.age}歳</p>
                <p>メール: {user.email}</p>
            </div>
        </div>
    );
}

export default UserInfo;
```
</details>

### 問題5: 配列のState更新
TODOリストを作成し、アイテムの追加・削除ができるコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```javascript
import { useState } from 'react';

function TodoList() {
    const [todos, setTodos] = useState([]);
    const [newTodo, setNewTodo] = useState('');
    
    const addTodo = () => {
        if (newTodo.trim()) {
            setTodos([...todos, newTodo]);
            setNewTodo('');
        }
    };
    
    const removeTodo = (index) => {
        setTodos(todos.filter((_, i) => i !== index));
    };
    
    return (
        <div>
            <h2>TODOリスト</h2>
            <div>
                <input 
                    type="text" 
                    value={newTodo}
                    onChange={(e) => setNewTodo(e.target.value)}
                    placeholder="新しいTODO"
                />
                <button onClick={addTodo}>追加</button>
            </div>
            <ul>
                {todos.map((todo, index) => (
                    <li key={index}>
                        {todo}
                        <button onClick={() => removeTodo(index)}>削除</button>
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default TodoList;
```
</details>

---

お疲れ様でした！次の章に進みましょう。

