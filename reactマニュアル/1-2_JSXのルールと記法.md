# 1-2. JSXのルールと記法

この章では、Reactの核となる「JSX」について学びます。JSXを使うことで、HTMLのような見た目でJavaScriptコードを書き、効率的にUIを構築できるようになります。JSXのルールと記法を理解することで、Reactコンポーネントを正しく作成できるようになります。

---

## 目次

- [JSXの仕組みとJavaScript式の埋め込み](#jsxの仕組みとjavascript式の埋め込み)
- [フラグメント (<>...</>) と単一要素](#フラグメント--と単一要素)
- [属性の指定 (className, styleオブジェクト)](#属性の指定-classname-styleオブジェクト)
- [条件付きレンダリング (&&, 三項演算子)](#条件付きレンダリング--三項演算子)
- [リストレンダリング (map) と key 属性](#リストレンダリング-map-と-key-属性)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## JSXの仕組みとJavaScript式の埋め込み

### JSXとは
JSX（JavaScript XML）は、**JavaScriptの中にHTMLのような構文を書ける**記法です。Reactでは、JSXを使ってコンポーネントを定義します。

### 日常生活での例
- **テンプレート**: 手紙のテンプレート（JSX）に名前や日付（JavaScript式）を埋め込む
- **レシピカード**: カードのデザイン（JSX）に料理名や材料（JavaScript式）を表示する
- **名刺**: 名刺のレイアウト（JSX）に個人情報（JavaScript式）を表示する

### JSXの基本構文

```jsx
// JSXの基本形
const element = <h1>Hello, World!</h1>;

// JavaScript式を埋め込む（波括弧 {} を使用）
const name = "太郎";
const element2 = <h1>Hello, {name}!</h1>;
```

### JavaScript式の埋め込み

JSXの中では、**波括弧 `{}` を使ってJavaScript式を埋め込めます**。

#### 変数の埋め込み

```jsx
function App() {
    const name = "太郎";
    const age = 25;
    
    return (
        <div>
            <p>名前: {name}</p>
            <p>年齢: {age}歳</p>
        </div>
    );
}
```

#### 計算式の埋め込み

```jsx
function App() {
    const price = 1000;
    const tax = 0.1;
    
    return (
        <div>
            <p>価格: {price}円</p>
            <p>税込価格: {price * (1 + tax)}円</p>
        </div>
    );
}
```

#### 関数呼び出しの埋め込み

```jsx
function App() {
    const formatDate = (date) => {
        return date.toLocaleDateString('ja-JP');
    };
    
    const today = new Date();
    
    return (
        <div>
            <p>今日は {formatDate(today)} です</p>
        </div>
    );
}
```

### 実践例: JSXの仕組みとJavaScript式の埋め込み

```jsx
// src/App.jsx
import React from 'react';

function App() {
    // 変数の定義
    const userName = "太郎";
    const userAge = 25;
    const isActive = true;
    
    // 計算式
    const price = 1000;
    const quantity = 3;
    const total = price * quantity;
    
    // 関数の定義
    const greet = (name) => {
        return `こんにちは、${name}さん！`;
    };
    
    const formatCurrency = (amount) => {
        return `¥${amount.toLocaleString()}`;
    };
    
    // 配列
    const items = ["りんご", "バナナ", "オレンジ"];
    
    return (
        <div>
            <h1>JSXの基本例</h1>
            
            {/* 変数の埋め込み */}
            <section>
                <h2>変数の埋め込み</h2>
                <p>名前: {userName}</p>
                <p>年齢: {userAge}歳</p>
                <p>アクティブ: {isActive ? "はい" : "いいえ"}</p>
            </section>
            
            {/* 計算式の埋め込み */}
            <section>
                <h2>計算式の埋め込み</h2>
                <p>単価: {formatCurrency(price)}</p>
                <p>数量: {quantity}個</p>
                <p>合計: {formatCurrency(total)}</p>
            </section>
            
            {/* 関数呼び出しの埋め込み */}
            <section>
                <h2>関数呼び出しの埋め込み</h2>
                <p>{greet(userName)}</p>
                <p>現在の日時: {new Date().toLocaleString('ja-JP')}</p>
            </section>
            
            {/* 配列の要素へのアクセス */}
            <section>
                <h2>配列の要素へのアクセス</h2>
                <p>最初のアイテム: {items[0]}</p>
                <p>アイテム数: {items.length}個</p>
            </section>
            
            {/* 条件式の埋め込み */}
            <section>
                <h2>条件式の埋め込み</h2>
                <p>価格: {price > 500 ? "高価" : "手頃"}</p>
                <p>在庫: {quantity > 0 ? "あり" : "なし"}</p>
            </section>
        </div>
    );
}

export default App;
```

### JSXの重要なルール

1. **波括弧で囲む**: JavaScript式は `{}` で囲む
2. **式のみ**: `{}` の中には式（expression）のみ書ける（文（statement）は書けない）
3. **文字列リテラル**: 文字列は `{}` なしでも書けるが、変数や式は `{}` が必要
4. **コメント**: `{/* コメント */}` の形式でコメントを書ける

### 注意点

```jsx
// ✅ 正しい: 式
const element1 = <p>{name}</p>;
const element2 = <p>{2 + 3}</p>;
const element3 = <p>{isActive ? "ON" : "OFF"}</p>;

// ❌ 間違い: 文（if文など）は書けない
// const element4 = <p>{if (isActive) return "ON"}</p>; // エラー

// ✅ 正しい: 三項演算子を使う
const element5 = <p>{isActive ? "ON" : "OFF"}</p>;
```

---

## フラグメント (<>...</>) と単一要素

### フラグメントとは
フラグメントは、**余分なDOM要素を作らずに複数の要素をグループ化する**機能です。

### 問題点: 複数の要素を返す場合

JSXでは、**1つのコンポーネントは1つのルート要素を返す必要があります**。

```jsx
// ❌ エラー: 複数の要素を直接返せない
function App() {
    return (
        <h1>タイトル</h1>
        <p>本文</p>
    );
}
```

### 解決策1: divで囲む（従来の方法）

```jsx
// ✅ 動作するが、不要なdiv要素が追加される
function App() {
    return (
        <div>
            <h1>タイトル</h1>
            <p>本文</p>
        </div>
    );
}
```

### 解決策2: フラグメントを使う（推奨）

```jsx
// ✅ 推奨: 余分なDOM要素を作らない
function App() {
    return (
        <>
            <h1>タイトル</h1>
            <p>本文</p>
        </>
    );
}

// または明示的に記述
function App() {
    return (
        <React.Fragment>
            <h1>タイトル</h1>
            <p>本文</p>
        </React.Fragment>
    );
}
```

### フラグメントのメリット
- **余分なDOM要素を作らない**: HTMLの構造がシンプルになる
- **CSSの影響を受けない**: ラッパー要素がないため、スタイリングが簡単
- **パフォーマンス**: 不要な要素がないため、わずかにパフォーマンスが向上

### 実践例: フラグメントの使用

```jsx
// src/App.jsx
import React from 'react';

// 例1: 基本的なフラグメントの使用
function Header() {
    return (
        <>
            <h1>サイトタイトル</h1>
            <nav>
                <a href="/">ホーム</a>
                <a href="/about">について</a>
            </nav>
        </>
    );
}

// 例2: リストアイテムを返す場合
function ItemList() {
    const items = ["アイテム1", "アイテム2", "アイテム3"];
    
    return (
        <>
            {items.map((item, index) => (
                <li key={index}>{item}</li>
            ))}
        </>
    );
}

// 例3: 条件付きで複数の要素を返す
function UserProfile({ user }) {
    if (!user) {
        return <p>ユーザー情報がありません</p>;
    }
    
    return (
        <>
            <h2>{user.name}</h2>
            <p>年齢: {user.age}歳</p>
            <p>メール: {user.email}</p>
        </>
    );
}

// 例4: テーブルの行を返す場合
function TableRow({ data }) {
    return (
        <>
            <td>{data.id}</td>
            <td>{data.name}</td>
            <td>{data.price}円</td>
        </>
    );
}

// 例5: モーダルやポップアップの構造
function Modal({ isOpen, onClose, children }) {
    if (!isOpen) return null;
    
    return (
        <>
            <div className="modal-overlay" onClick={onClose}></div>
            <div className="modal-content">
                {children}
            </div>
        </>
    );
}

// メインコンポーネント
function App() {
    const user = {
        name: "太郎",
        age: 25,
        email: "taro@example.com"
    };
    
    return (
        <div>
            <Header />
            <ul>
                <ItemList />
            </ul>
            <UserProfile user={user} />
        </div>
    );
}

export default App;
```

### フラグメントとkey属性

リストをレンダリングする際、フラグメントにもkey属性を指定できます。

```jsx
function ItemList({ items }) {
    return (
        <>
            {items.map((item) => (
                <React.Fragment key={item.id}>
                    <dt>{item.name}</dt>
                    <dd>{item.description}</dd>
                </React.Fragment>
            ))}
        </>
    );
}
```

---

## 属性の指定 (className, styleオブジェクト)

### JSXでの属性の指定

JSXでは、HTMLの属性とは異なる点があります。

### className属性

HTMLでは `class` を使いますが、JSXでは **`className`** を使います（`class`はJavaScriptの予約語のため）。

```jsx
// ❌ 間違い: classは使えない
// <div class="container">内容</div>

// ✅ 正しい: classNameを使う
<div className="container">内容</div>
```

### style属性

JSXでは、**`style`属性にはオブジェクトを渡します**（文字列ではない）。

```jsx
// ❌ 間違い: 文字列は使えない
// <div style="color: red; font-size: 16px;">テキスト</div>

// ✅ 正しい: オブジェクトを使う
<div style={{ color: 'red', fontSize: '16px' }}>テキスト</div>
```

### styleオブジェクトの書き方

```jsx
// インラインスタイル
const element = (
    <div style={{ color: 'red', fontSize: '20px' }}>
        テキスト
    </div>
);

// 変数に格納
const styleObject = {
    color: 'blue',
    backgroundColor: 'yellow',
    padding: '10px',
    borderRadius: '5px'
};

const element2 = <div style={styleObject}>テキスト</div>;
```

### キャメルケースへの変換

CSSプロパティ名は、**キャメルケース（camelCase）に変換**します。

| CSS | JSX |
|-----|-----|
| `background-color` | `backgroundColor` |
| `font-size` | `fontSize` |
| `margin-top` | `marginTop` |
| `border-radius` | `borderRadius` |

### 実践例: 属性の指定

```jsx
// src/App.jsx
import React from 'react';
import './App.css'; // CSSファイルのインポート

function App() {
    // styleオブジェクトの定義
    const containerStyle = {
        padding: '20px',
        backgroundColor: '#f0f0f0',
        borderRadius: '8px',
        margin: '20px 0'
    };
    
    const titleStyle = {
        color: '#333',
        fontSize: '24px',
        fontWeight: 'bold',
        marginBottom: '10px'
    };
    
    const buttonStyle = {
        padding: '10px 20px',
        backgroundColor: '#007bff',
        color: 'white',
        border: 'none',
        borderRadius: '4px',
        cursor: 'pointer',
        fontSize: '16px'
    };
    
    // 動的なスタイル
    const isActive = true;
    const dynamicStyle = {
        color: isActive ? 'green' : 'gray',
        fontWeight: isActive ? 'bold' : 'normal'
    };
    
    return (
        <div className="app-container">
            {/* classNameの使用 */}
            <header className="header">
                <h1 className="title">Reactアプリケーション</h1>
            </header>
            
            {/* styleオブジェクトの使用 */}
            <main style={containerStyle}>
                <h2 style={titleStyle}>スタイルの例</h2>
                
                {/* インラインスタイル */}
                <p style={{ color: 'blue', fontSize: '18px' }}>
                    インラインスタイルの例
                </p>
                
                {/* 変数に格納したスタイル */}
                <button style={buttonStyle}>
                    クリックしてください
                </button>
                
                {/* 動的なスタイル */}
                <p style={dynamicStyle}>
                    状態に応じてスタイルが変わります
                </p>
                
                {/* 複数のスタイルを組み合わせ */}
                <div style={{
                    ...containerStyle,
                    backgroundColor: '#e0e0e0',
                    border: '2px solid #333'
                }}>
                    スタイルのマージ例
                </div>
            </main>
            
            {/* 条件付きクラス名 */}
            <footer className={`footer ${isActive ? 'active' : 'inactive'}`}>
                フッター
            </footer>
        </div>
    );
}

export default App;
```

### その他の属性の注意点

#### HTMLFor属性

```jsx
// HTML: <label for="email">メール</label>
// JSX: <label htmlFor="email">メール</label>
```

#### tabIndex属性

```jsx
// HTML: <div tabindex="0">要素</div>
// JSX: <div tabIndex={0}>要素</div>
```

#### 真偽値属性

```jsx
// HTML: <input disabled>
// JSX: <input disabled={true} /> または <input disabled />
```

### 実践例: フォーム要素の属性

```jsx
function FormExample() {
    return (
        <form>
            {/* htmlForの使用 */}
            <label htmlFor="email">メールアドレス</label>
            <input 
                id="email"
                type="email"
                className="form-input"
                style={{ padding: '8px', width: '100%' }}
            />
            
            {/* 真偽値属性 */}
            <input 
                type="checkbox"
                defaultChecked={true}
                disabled={false}
            />
            
            {/* 数値属性 */}
            <input 
                type="number"
                min={0}
                max={100}
                step={1}
            />
            
            {/* データ属性 */}
            <div data-testid="test-element">
                テスト要素
            </div>
        </form>
    );
}
```

---

## 条件付きレンダリング (&&, 三項演算子)

### 条件付きレンダリングとは
条件付きレンダリングは、**条件に応じて異なる内容を表示する**機能です。

### 方法1: &&演算子（論理AND）

`&&`演算子を使うと、**条件が真の場合のみ要素を表示**できます。

```jsx
function App() {
    const isLoggedIn = true;
    
    return (
        <div>
            {isLoggedIn && <p>ログイン中です</p>}
        </div>
    );
}
```

### 方法2: 三項演算子

三項演算子を使うと、**条件に応じて異なる要素を表示**できます。

```jsx
function App() {
    const isLoggedIn = true;
    
    return (
        <div>
            {isLoggedIn ? (
                <p>ログイン中です</p>
            ) : (
                <p>ログインしてください</p>
            )}
        </div>
    );
}
```

### 方法3: if文（早期リターン）

コンポーネント内でif文を使い、**早期リターン**で条件分岐することもできます。

```jsx
function App({ user }) {
    if (!user) {
        return <p>ユーザー情報がありません</p>;
    }
    
    return (
        <div>
            <p>こんにちは、{user.name}さん</p>
        </div>
    );
}
```

### 実践例: 条件付きレンダリング

```jsx
// src/App.jsx
import React, { useState } from 'react';

function App() {
    const [isLoggedIn, setIsLoggedIn] = useState(false);
    const [user, setUser] = useState(null);
    const [items, setItems] = useState([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);
    
    // 例1: &&演算子による条件付きレンダリング
    const WelcomeMessage = () => (
        <div>
            {isLoggedIn && <h2>ようこそ！</h2>}
            {!isLoggedIn && <p>ログインしてください</p>}
        </div>
    );
    
    // 例2: 三項演算子による条件付きレンダリング
    const UserStatus = () => (
        <div>
            {isLoggedIn ? (
                <div>
                    <p>ログイン中です</p>
                    <button onClick={() => setIsLoggedIn(false)}>
                        ログアウト
                    </button>
                </div>
            ) : (
                <div>
                    <p>ログインしていません</p>
                    <button onClick={() => setIsLoggedIn(true)}>
                        ログイン
                    </button>
                </div>
            )}
        </div>
    );
    
    // 例3: 早期リターンによる条件分岐
    const UserProfile = ({ user }) => {
        if (!user) {
            return <p>ユーザー情報がありません</p>;
        }
        
        return (
            <div>
                <h3>ユーザープロフィール</h3>
                <p>名前: {user.name}</p>
                <p>年齢: {user.age}歳</p>
            </div>
        );
    };
    
    // 例4: 複数の条件
    const Content = () => {
        if (loading) {
            return <p>読み込み中...</p>;
        }
        
        if (error) {
            return <p>エラー: {error}</p>;
        }
        
        if (items.length === 0) {
            return <p>アイテムがありません</p>;
        }
        
        return (
            <ul>
                {items.map((item) => (
                    <li key={item.id}>{item.name}</li>
                ))}
            </ul>
        );
    };
    
    // 例5: ネストした条件
    const ComplexCondition = () => {
        const role = user?.role;
        const isAdmin = role === 'admin';
        const isEditor = role === 'editor';
        
        return (
            <div>
                {isAdmin && (
                    <div>
                        <h4>管理者メニュー</h4>
                        <button>ユーザー管理</button>
                        <button>設定</button>
                    </div>
                )}
                {isEditor && !isAdmin && (
                    <div>
                        <h4>編集者メニュー</h4>
                        <button>記事編集</button>
                    </div>
                )}
                {!isAdmin && !isEditor && (
                    <p>一般ユーザーです</p>
                )}
            </div>
        );
    };
    
    // 例6: 数値や文字列の条件チェック
    const ScoreDisplay = ({ score }) => {
        return (
            <div>
                {score > 80 && <p className="excellent">優秀です！</p>}
                {score > 60 && score <= 80 && <p className="good">良好です</p>}
                {score <= 60 && <p className="needs-improvement">要改善です</p>}
            </div>
        );
    };
    
    return (
        <div className="app">
            <h1>条件付きレンダリングの例</h1>
            
            <section>
                <h2>例1: &&演算子</h2>
                <WelcomeMessage />
            </section>
            
            <section>
                <h2>例2: 三項演算子</h2>
                <UserStatus />
            </section>
            
            <section>
                <h2>例3: 早期リターン</h2>
                <UserProfile user={user} />
            </section>
            
            <section>
                <h2>例4: 複数の条件</h2>
                <Content />
            </section>
            
            <section>
                <h2>例5: ネストした条件</h2>
                <ComplexCondition />
            </section>
            
            <section>
                <h2>例6: 数値の条件</h2>
                <ScoreDisplay score={85} />
                <ScoreDisplay score={70} />
                <ScoreDisplay score={50} />
            </section>
        </div>
    );
}

export default App;
```

### 注意点: &&演算子の落とし穴

```jsx
// ⚠️ 注意: 0や空文字列はfalsyな値
const count = 0;

// これは何も表示されない（0が表示されない）
{count && <p>アイテム数: {count}</p>}

// ✅ 正しい: 明示的に真偽値に変換
{count > 0 && <p>アイテム数: {count}</p>}
{Boolean(count) && <p>アイテム数: {count}</p>}
```

---

## リストレンダリング (map) と key 属性

### リストレンダリングとは
リストレンダリングは、**配列のデータを元に複数の要素を表示する**機能です。

### mapメソッドの使用

`map`メソッドを使って、配列の各要素をJSX要素に変換します。

```jsx
function App() {
    const items = ["りんご", "バナナ", "オレンジ"];
    
    return (
        <ul>
            {items.map((item) => (
                <li key={item}>{item}</li>
            ))}
        </ul>
    );
}
```

### key属性の重要性

`key`属性は、**Reactが要素を識別するために必要**です。リストの各要素に一意のkeyを指定する必要があります。

#### key属性がない場合の問題

```jsx
// ⚠️ 警告: key属性がない
{items.map((item) => (
    <li>{item}</li>
))}
```

#### key属性がある場合

```jsx
// ✅ 正しい: key属性を指定
{items.map((item, index) => (
    <li key={index}>{item}</li>
))}
```

### key属性のベストプラクティス

1. **一意性**: 各keyは一意である必要がある
2. **安定性**: リストの順序が変わっても、同じ要素には同じkeyを使う
3. **推奨**: 可能な限り、データのIDを使う（indexは避ける）

```jsx
// ✅ 推奨: データのIDを使う
const users = [
    { id: 1, name: "太郎" },
    { id: 2, name: "花子" },
    { id: 3, name: "次郎" }
];

{users.map((user) => (
    <div key={user.id}>{user.name}</div>
))}

// ⚠️ 避けるべき: indexを使う（順序が変わる場合に問題）
{users.map((user, index) => (
    <div key={index}>{user.name}</div>
))}
```

### 実践例: リストレンダリング

```jsx
// src/App.jsx
import React from 'react';

function App() {
    // 例1: 基本的なリストレンダリング
    const fruits = ["りんご", "バナナ", "オレンジ", "ぶどう"];
    
    // 例2: オブジェクトの配列
    const users = [
        { id: 1, name: "太郎", age: 25, email: "taro@example.com" },
        { id: 2, name: "花子", age: 30, email: "hanako@example.com" },
        { id: 3, name: "次郎", age: 28, email: "jiro@example.com" }
    ];
    
    // 例3: 商品リスト
    const products = [
        { id: 1, name: "商品A", price: 1000, inStock: true },
        { id: 2, name: "商品B", price: 2000, inStock: false },
        { id: 3, name: "商品C", price: 1500, inStock: true }
    ];
    
    // 例4: タスクリスト
    const tasks = [
        { id: 1, title: "タスク1", completed: true },
        { id: 2, title: "タスク2", completed: false },
        { id: 3, title: "タスク3", completed: true }
    ];
    
    return (
        <div className="app">
            <h1>リストレンダリングの例</h1>
            
            {/* 例1: 基本的なリスト */}
            <section>
                <h2>果物リスト</h2>
                <ul>
                    {fruits.map((fruit, index) => (
                        <li key={index}>{fruit}</li>
                    ))}
                </ul>
            </section>
            
            {/* 例2: オブジェクトの配列 */}
            <section>
                <h2>ユーザーリスト</h2>
                <div className="user-list">
                    {users.map((user) => (
                        <div key={user.id} className="user-card">
                            <h3>{user.name}</h3>
                            <p>年齢: {user.age}歳</p>
                            <p>メール: {user.email}</p>
                        </div>
                    ))}
                </div>
            </section>
            
            {/* 例3: 条件付きリストレンダリング */}
            <section>
                <h2>在庫あり商品</h2>
                <div className="product-list">
                    {products
                        .filter(product => product.inStock)
                        .map((product) => (
                            <div key={product.id} className="product-card">
                                <h3>{product.name}</h3>
                                <p>価格: ¥{product.price.toLocaleString()}</p>
                                <span className="in-stock">在庫あり</span>
                            </div>
                        ))
                    }
                </div>
            </section>
            
            {/* 例4: ネストしたリスト */}
            <section>
                <h2>タスクリスト</h2>
                <ul className="task-list">
                    {tasks.map((task) => (
                        <li key={task.id} className={task.completed ? 'completed' : ''}>
                            <input 
                                type="checkbox" 
                                checked={task.completed} 
                                readOnly
                            />
                            <span>{task.title}</span>
                            {task.completed && <span className="checkmark">✓</span>}
                        </li>
                    ))}
                </ul>
            </section>
            
            {/* 例5: テーブルでのリストレンダリング */}
            <section>
                <h2>ユーザーテーブル</h2>
                <table>
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>名前</th>
                            <th>年齢</th>
                            <th>メール</th>
                        </tr>
                    </thead>
                    <tbody>
                        {users.map((user) => (
                            <tr key={user.id}>
                                <td>{user.id}</td>
                                <td>{user.name}</td>
                                <td>{user.age}</td>
                                <td>{user.email}</td>
                            </tr>
                        ))}
                    </tbody>
                </table>
            </section>
            
            {/* 例6: 空のリストの処理 */}
            <section>
                <h2>空のリストの処理</h2>
                {fruits.length === 0 ? (
                    <p>アイテムがありません</p>
                ) : (
                    <ul>
                        {fruits.map((fruit, index) => (
                            <li key={index}>{fruit}</li>
                        ))}
                    </ul>
                )}
            </section>
        </div>
    );
}

export default App;
```

### リストレンダリングのベストプラクティス

1. **key属性は必須**: リストの各要素に一意のkeyを指定
2. **IDを使う**: 可能な限り、データのIDをkeyとして使う
3. **indexは避ける**: リストの順序が変わる可能性がある場合は、indexをkeyに使わない
4. **空のリストの処理**: リストが空の場合の表示を考慮する
5. **フィルタリング**: 必要に応じて、表示前にデータをフィルタリングする

### 注意点: key属性の位置

```jsx
// ✅ 正しい: 最外側の要素にkeyを指定
{items.map((item) => (
    <div key={item.id}>
        <h3>{item.title}</h3>
        <p>{item.description}</p>
    </div>
))}

// ❌ 間違い: 内側の要素にkeyを指定
{items.map((item) => (
    <div>
        <h3 key={item.id}>{item.title}</h3>
        <p>{item.description}</p>
    </div>
))}
```

---

## まとめ

この章では、ReactのJSXのルールと記法について学びました。

### 学んだこと
- **JSXの仕組み**: JavaScriptの中にHTMLのような構文を書ける
- **JavaScript式の埋め込み**: 波括弧 `{}` を使って変数や式を埋め込む
- **フラグメント**: 余分なDOM要素を作らずに複数の要素をグループ化
- **属性の指定**: `className`と`style`オブジェクトの使い方
- **条件付きレンダリング**: `&&`演算子と三項演算子による条件分岐
- **リストレンダリング**: `map`メソッドと`key`属性によるリスト表示

### 重要なポイント
1. **波括弧**: JavaScript式は `{}` で囲む
2. **className**: HTMLの`class`ではなく`className`を使う
3. **styleオブジェクト**: `style`属性にはオブジェクトを渡す
4. **フラグメント**: 複数の要素を返す場合は`<>...</>`を使う
5. **key属性**: リストレンダリングでは必ず一意のkeyを指定する

### 使い分けの目安
- **&&演算子**: 条件が真の場合のみ表示したい場合
- **三項演算子**: 条件に応じて異なる内容を表示したい場合
- **早期リターン**: コンポーネント全体の表示を切り替えたい場合
- **フラグメント**: 余分なDOM要素を作りたくない場合

### 次のステップ
次の章では、コンポーネント開発の基礎（Props & State）について詳しく学びます。JSXのルールを理解することで、Reactコンポーネントを正しく作成できるようになります。

---

## 演習問題

### 問題1: JavaScript式の埋め込み
変数`name`と`age`を定義し、JSX内で表示してください。

<details>
<summary>解答例</summary>

```jsx
function App() {
    const name = "太郎";
    const age = 25;
    
    return (
        <div>
            <p>名前: {name}</p>
            <p>年齢: {age}歳</p>
        </div>
    );
}
```
</details>

### 問題2: フラグメントの使用
フラグメントを使って、`<h1>`と`<p>`要素を返すコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```jsx
function Header() {
    return (
        <>
            <h1>タイトル</h1>
            <p>サブタイトル</p>
        </>
    );
}
```
</details>

### 問題3: classNameとstyle属性
`className`で"container"クラスを指定し、`style`で背景色を青、文字色を白に設定してください。

<details>
<summary>解答例</summary>

```jsx
function App() {
    return (
        <div 
            className="container"
            style={{ 
                backgroundColor: 'blue', 
                color: 'white' 
            }}
        >
            コンテンツ
        </div>
    );
}
```
</details>

### 問題4: 条件付きレンダリング
変数`isLoggedIn`が`true`の場合は"ログイン中"、`false`の場合は"ログインしてください"を表示してください。

<details>
<summary>解答例</summary>

```jsx
function App() {
    const isLoggedIn = true;
    
    return (
        <div>
            {isLoggedIn ? (
                <p>ログイン中</p>
            ) : (
                <p>ログインしてください</p>
            )}
        </div>
    );
}
```
</details>

### 問題5: リストレンダリング
配列`["りんご", "バナナ", "オレンジ"]`を`<ul>`と`<li>`で表示してください。各要素に適切な`key`属性を指定してください。

<details>
<summary>解答例</summary>

```jsx
function App() {
    const fruits = ["りんご", "バナナ", "オレンジ"];
    
    return (
        <ul>
            {fruits.map((fruit, index) => (
                <li key={index}>{fruit}</li>
            ))}
        </ul>
    );
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。


