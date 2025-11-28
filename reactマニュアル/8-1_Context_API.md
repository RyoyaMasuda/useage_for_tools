# 8-1. Context API

この章では、Reactの「Context API」について学びます。Context APIは、コンポーネントツリー全体でデータを共有するための機能です。Propsのバケツリレーを避け、グローバルな状態を効率的に管理できるようになります。

---

## 目次

- [createContext と Provider](#createcontext-と-provider)
- [useContext の利用](#usecallback-の利用)
- [パフォーマンスの注意点](#パフォーマンスの注意点)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## createContext と Provider

### Context APIとは
Context APIは、**コンポーネントツリー全体でデータを共有する**ためのReactの機能です。Propsを何階層も渡す必要がなくなり、コードが簡潔になります。

### 日常生活での例
- **会社の規則**: 全社員に共通の規則を伝える（各部署に個別に伝える必要がない）
- **言語設定**: アプリ全体で使用する言語を一箇所で管理
- **テーマ設定**: アプリ全体の色やスタイルを一箇所で管理
- **認証情報**: ログイン状態をアプリ全体で共有

### Propsのバケツリレーの問題

Propsを何階層も渡す必要がある場合、コードが複雑になります。

```javascript
// ❌ Propsのバケツリレー
function App() {
    const user = { name: '太郎', age: 25 };
    return <ComponentA user={user} />;
}

function ComponentA({ user }) {
    return <ComponentB user={user} />;
}

function ComponentB({ user }) {
    return <ComponentC user={user} />;
}

function ComponentC({ user }) {
    return <div>{user.name}</div>;
}
```

### Context APIの基本構文

```javascript
import { createContext } from 'react';

// Contextの作成
const MyContext = createContext(デフォルト値);

// Providerで値を提供
<MyContext.Provider value={値}>
    {children}
</MyContext.Provider>
```

### 実践例: createContextとProviderの基本

```javascript
// src/contexts/UserContext.jsx
import { createContext } from 'react';

// Contextの作成
const UserContext = createContext(null);

export default UserContext;

// src/App.jsx
import { useState } from 'react';
import UserContext from './contexts/UserContext';
import UserProfile from './components/UserProfile';

function App() {
    const [user, setUser] = useState({
        name: '太郎',
        age: 25,
        email: 'taro@example.com'
    });
    
    return (
        <UserContext.Provider value={user}>
            <div>
                <h1>アプリケーション</h1>
                <UserProfile />
            </div>
        </UserContext.Provider>
    );
}

export default App;
```

### 実践例: 複数の値を持つContext

```javascript
// src/contexts/ThemeContext.jsx
import { createContext } from 'react';

const ThemeContext = createContext({
    theme: 'light',
    toggleTheme: () => {}
});

export default ThemeContext;

// src/App.jsx
import { useState } from 'react';
import ThemeContext from './contexts/ThemeContext';
import Header from './components/Header';
import Content from './components/Content';

function App() {
    const [theme, setTheme] = useState('light');
    
    const toggleTheme = () => {
        setTheme(prevTheme => prevTheme === 'light' ? 'dark' : 'light');
    };
    
    return (
        <ThemeContext.Provider value={{ theme, toggleTheme }}>
            <div className={`app ${theme}`}>
                <Header />
                <Content />
            </div>
        </ThemeContext.Provider>
    );
}

export default App;
```

### 実践例: 認証Context

```javascript
// src/contexts/AuthContext.jsx
import { createContext, useState } from 'react';

const AuthContext = createContext({
    user: null,
    isAuthenticated: false,
    login: () => {},
    logout: () => {}
});

export default AuthContext;

// src/App.jsx
import { useState } from 'react';
import AuthContext from './contexts/AuthContext';
import LoginForm from './components/LoginForm';
import Dashboard from './components/Dashboard';

function App() {
    const [user, setUser] = useState(null);
    
    const login = (userData) => {
        setUser(userData);
    };
    
    const logout = () => {
        setUser(null);
    };
    
    const value = {
        user,
        isAuthenticated: !!user,
        login,
        logout
    };
    
    return (
        <AuthContext.Provider value={value}>
            <div>
                {user ? <Dashboard /> : <LoginForm />}
            </div>
        </AuthContext.Provider>
    );
}

export default App;
```

### 実践例: カスタムProviderコンポーネント

```javascript
// src/contexts/UserContext.jsx
import { createContext, useState } from 'react';

const UserContext = createContext(null);

export function UserProvider({ children }) {
    const [user, setUser] = useState(null);
    
    const updateUser = (userData) => {
        setUser(userData);
    };
    
    const clearUser = () => {
        setUser(null);
    };
    
    const value = {
        user,
        updateUser,
        clearUser,
        isLoggedIn: !!user
    };
    
    return (
        <UserContext.Provider value={value}>
            {children}
        </UserContext.Provider>
    );
}

export default UserContext;

// src/App.jsx
import { UserProvider } from './contexts/UserContext';
import UserProfile from './components/UserProfile';

function App() {
    return (
        <UserProvider>
            <div>
                <h1>アプリケーション</h1>
                <UserProfile />
            </div>
        </UserProvider>
    );
}

export default App;
```

### 実践例: 複数のContext

```javascript
// src/contexts/ThemeContext.jsx
import { createContext } from 'react';

const ThemeContext = createContext('light');

export default ThemeContext;

// src/contexts/LanguageContext.jsx
import { createContext } from 'react';

const LanguageContext = createContext('ja');

export default LanguageContext;

// src/App.jsx
import { useState } from 'react';
import ThemeContext from './contexts/ThemeContext';
import LanguageContext from './contexts/LanguageContext';
import Header from './components/Header';

function App() {
    const [theme, setTheme] = useState('light');
    const [language, setLanguage] = useState('ja');
    
    return (
        <ThemeContext.Provider value={theme}>
            <LanguageContext.Provider value={language}>
                <div>
                    <Header />
                    {/* 他のコンポーネント */}
                </div>
            </LanguageContext.Provider>
        </ThemeContext.Provider>
    );
}

export default App;
```

---

## useContext の利用

### useContextとは
`useContext`は、**Contextの値を読み取る**ためのReactフックです。Providerで提供された値にアクセスできます。

### useContextの基本構文

```javascript
import { useContext } from 'react';
import MyContext from './contexts/MyContext';

function Component() {
    const value = useContext(MyContext);
    // valueを使用
}
```

### 実践例: useContextの基本

```javascript
// src/components/UserProfile.jsx
import { useContext } from 'react';
import UserContext from '../contexts/UserContext';

function UserProfile() {
    const user = useContext(UserContext);
    
    if (!user) {
        return <div>ログインしてください</div>;
    }
    
    return (
        <div>
            <h2>ユーザー情報</h2>
            <p>名前: {user.name}</p>
            <p>年齢: {user.age}歳</p>
            <p>メール: {user.email}</p>
        </div>
    );
}

export default UserProfile;
```

### 実践例: テーマContextの使用

```javascript
// src/components/Header.jsx
import { useContext } from 'react';
import ThemeContext from '../contexts/ThemeContext';

function Header() {
    const theme = useContext(ThemeContext);
    
    return (
        <header className={`header ${theme}`}>
            <h1>ヘッダー</h1>
            <p>現在のテーマ: {theme}</p>
        </header>
    );
}

export default Header;
```

### 実践例: 認証Contextの使用

```javascript
// src/components/Dashboard.jsx
import { useContext } from 'react';
import AuthContext from '../contexts/AuthContext';

function Dashboard() {
    const { user, logout } = useContext(AuthContext);
    
    return (
        <div>
            <h2>ダッシュボード</h2>
            <p>ようこそ、{user.name}さん！</p>
            <button onClick={logout}>ログアウト</button>
        </div>
    );
}

export default Dashboard;

// src/components/LoginForm.jsx
import { useState, useContext } from 'react';
import AuthContext from '../contexts/AuthContext';

function LoginForm() {
    const { login } = useContext(AuthContext);
    const [username, setUsername] = useState('');
    const [password, setPassword] = useState('');
    
    const handleSubmit = (e) => {
        e.preventDefault();
        // 実際の実装では、APIを呼び出して認証
        login({
            name: username,
            email: `${username}@example.com`
        });
    };
    
    return (
        <form onSubmit={handleSubmit}>
            <h2>ログイン</h2>
            <input
                type="text"
                placeholder="ユーザー名"
                value={username}
                onChange={(e) => setUsername(e.target.value)}
            />
            <input
                type="password"
                placeholder="パスワード"
                value={password}
                onChange={(e) => setPassword(e.target.value)}
            />
            <button type="submit">ログイン</button>
        </form>
    );
}

export default LoginForm;
```

### 実践例: 複数のContextを使用

```javascript
// src/components/Settings.jsx
import { useContext } from 'react';
import ThemeContext from '../contexts/ThemeContext';
import LanguageContext from '../contexts/LanguageContext';

function Settings() {
    const theme = useContext(ThemeContext);
    const language = useContext(LanguageContext);
    
    return (
        <div>
            <h2>設定</h2>
            <p>テーマ: {theme}</p>
            <p>言語: {language}</p>
        </div>
    );
}

export default Settings;
```

### 実践例: カスタムフックでContextをラップ

```javascript
// src/hooks/useUser.js
import { useContext } from 'react';
import UserContext from '../contexts/UserContext';

export function useUser() {
    const context = useContext(UserContext);
    
    if (!context) {
        throw new Error('useUser must be used within a UserProvider');
    }
    
    return context;
}

// src/components/UserProfile.jsx
import { useUser } from '../hooks/useUser';

function UserProfile() {
    const { user, updateUser, clearUser, isLoggedIn } = useUser();
    
    if (!isLoggedIn) {
        return <div>ログインしてください</div>;
    }
    
    return (
        <div>
            <h2>ユーザー情報</h2>
            <p>名前: {user.name}</p>
            <p>年齢: {user.age}歳</p>
            <button onClick={clearUser}>ログアウト</button>
        </div>
    );
}

export default UserProfile;
```

### 実践例: ショッピングカートContext

```javascript
// src/contexts/CartContext.jsx
import { createContext, useState } from 'react';

const CartContext = createContext(null);

export function CartProvider({ children }) {
    const [items, setItems] = useState([]);
    
    const addItem = (product) => {
        setItems(prevItems => {
            const existingItem = prevItems.find(item => item.id === product.id);
            if (existingItem) {
                return prevItems.map(item =>
                    item.id === product.id
                        ? { ...item, quantity: item.quantity + 1 }
                        : item
                );
            }
            return [...prevItems, { ...product, quantity: 1 }];
        });
    };
    
    const removeItem = (productId) => {
        setItems(prevItems => prevItems.filter(item => item.id !== productId));
    };
    
    const updateQuantity = (productId, quantity) => {
        setItems(prevItems =>
            prevItems.map(item =>
                item.id === productId ? { ...item, quantity } : item
            )
        );
    };
    
    const clearCart = () => {
        setItems([]);
    };
    
    const total = items.reduce((sum, item) => sum + item.price * item.quantity, 0);
    
    const value = {
        items,
        addItem,
        removeItem,
        updateQuantity,
        clearCart,
        total,
        itemCount: items.reduce((sum, item) => sum + item.quantity, 0)
    };
    
    return (
        <CartContext.Provider value={value}>
            {children}
        </CartContext.Provider>
    );
}

export default CartContext;

// src/hooks/useCart.js
import { useContext } from 'react';
import CartContext from '../contexts/CartContext';

export function useCart() {
    const context = useContext(CartContext);
    
    if (!context) {
        throw new Error('useCart must be used within a CartProvider');
    }
    
    return context;
}

// src/components/Cart.jsx
import { useCart } from '../hooks/useCart';

function Cart() {
    const { items, removeItem, updateQuantity, total, clearCart } = useCart();
    
    return (
        <div>
            <h2>ショッピングカート</h2>
            {items.length === 0 ? (
                <p>カートは空です</p>
            ) : (
                <>
                    <ul>
                        {items.map(item => (
                            <li key={item.id}>
                                {item.name} - {item.price}円 × {item.quantity}
                                <button onClick={() => updateQuantity(item.id, item.quantity + 1)}>
                                    +
                                </button>
                                <button onClick={() => updateQuantity(item.id, item.quantity - 1)}>
                                    -
                                </button>
                                <button onClick={() => removeItem(item.id)}>
                                    削除
                                </button>
                            </li>
                        ))}
                    </ul>
                    <p>合計: {total}円</p>
                    <button onClick={clearCart}>カートを空にする</button>
                </>
            )}
        </div>
    );
}

export default Cart;
```

---

## パフォーマンスの注意点

### Context APIのパフォーマンス問題

Context APIを使用する際、**Providerの値が変更されると、そのContextを使用しているすべてのコンポーネントが再レンダリング**されます。これがパフォーマンスの問題を引き起こす可能性があります。

### 問題のあるコード例

```javascript
// ❌ 問題のあるコード
function App() {
    const [user, setUser] = useState({ name: '太郎', age: 25 });
    const [theme, setTheme] = useState('light');
    
    // オブジェクトを直接作成すると、毎回新しい参照になる
    return (
        <UserContext.Provider value={{ user, setUser }}>
            <ThemeContext.Provider value={{ theme, setTheme }}>
                {/* すべてのコンポーネントが再レンダリングされる */}
            </ThemeContext.Provider>
        </UserContext.Provider>
    );
}
```

### 解決策1: Contextを分割する

関連のないデータは、別々のContextに分割します。

```javascript
// ✅ 良いコード: Contextを分割
function App() {
    const [user, setUser] = useState({ name: '太郎', age: 25 });
    const [theme, setTheme] = useState('light');
    
    return (
        <UserContext.Provider value={{ user, setUser }}>
            <ThemeContext.Provider value={theme}>
                {/* themeが変更されても、UserContextを使用するコンポーネントは再レンダリングされない */}
            </ThemeContext.Provider>
        </UserContext.Provider>
    );
}
```

### 実践例: Contextの分割

```javascript
// src/contexts/UserContext.jsx
import { createContext, useState } from 'react';

const UserContext = createContext(null);

export function UserProvider({ children }) {
    const [user, setUser] = useState(null);
    
    return (
        <UserContext.Provider value={{ user, setUser }}>
            {children}
        </UserContext.Provider>
    );
}

export default UserContext;

// src/contexts/ThemeContext.jsx
import { createContext, useState } from 'react';

const ThemeContext = createContext('light');

export function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');
    
    return (
        <ThemeContext.Provider value={theme}>
            {children}
        </ThemeContext.Provider>
    );
}

export default ThemeContext;
```

### 解決策2: useMemoで値をメモ化する

オブジェクトや配列を値として渡す場合、`useMemo`でメモ化します。

```javascript
// ✅ 良いコード: useMemoでメモ化
import { useState, useMemo } from 'react';

function App() {
    const [user, setUser] = useState({ name: '太郎', age: 25 });
    const [count, setCount] = useState(0);
    
    // userが変更されたときだけ、新しいオブジェクトを作成
    const userValue = useMemo(() => ({ user, setUser }), [user]);
    
    return (
        <UserContext.Provider value={userValue}>
            <button onClick={() => setCount(count + 1)}>
                カウント: {count}
            </button>
            {/* countが変更されても、UserContextを使用するコンポーネントは再レンダリングされない */}
        </UserContext.Provider>
    );
}
```

### 実践例: useMemoを使った最適化

```javascript
// src/contexts/AppContext.jsx
import { createContext, useState, useMemo } from 'react';

const AppContext = createContext(null);

export function AppProvider({ children }) {
    const [user, setUser] = useState(null);
    const [theme, setTheme] = useState('light');
    const [language, setLanguage] = useState('ja');
    
    // 値が変更されたときだけ、新しいオブジェクトを作成
    const value = useMemo(() => ({
        user,
        setUser,
        theme,
        setTheme,
        language,
        setLanguage
    }), [user, theme, language]);
    
    return (
        <AppContext.Provider value={value}>
            {children}
        </AppContext.Provider>
    );
}

export default AppContext;
```

### 解決策3: React.memoでコンポーネントをメモ化する

Contextを使用するコンポーネントを`React.memo`でメモ化します。

```javascript
// ✅ 良いコード: React.memoでメモ化
import { memo, useContext } from 'react';
import UserContext from '../contexts/UserContext';

const UserProfile = memo(function UserProfile() {
    const { user } = useContext(UserContext);
    
    return (
        <div>
            <h2>ユーザー情報</h2>
            <p>名前: {user?.name}</p>
        </div>
    );
});

export default UserProfile;
```

### 実践例: React.memoを使った最適化

```javascript
// src/components/ThemeDisplay.jsx
import { memo, useContext } from 'react';
import ThemeContext from '../contexts/ThemeContext';

const ThemeDisplay = memo(function ThemeDisplay() {
    const theme = useContext(ThemeContext);
    
    console.log('ThemeDisplayが再レンダリングされました');
    
    return (
        <div className={`theme-display ${theme}`}>
            <p>現在のテーマ: {theme}</p>
        </div>
    );
});

export default ThemeDisplay;

// src/components/UserDisplay.jsx
import { memo, useContext } from 'react';
import UserContext from '../contexts/UserContext';

const UserDisplay = memo(function UserDisplay() {
    const { user } = useContext(UserContext);
    
    console.log('UserDisplayが再レンダリングされました');
    
    return (
        <div>
            <h2>ユーザー情報</h2>
            <p>名前: {user?.name}</p>
        </div>
    );
});

export default UserDisplay;
```

### 解決策4: カスタムフックで選択的に値を取得する

必要な値だけを取得するカスタムフックを作成します。

```javascript
// ✅ 良いコード: 選択的に値を取得
import { useContext, useMemo } from 'react';
import UserContext from '../contexts/UserContext';

export function useUserName() {
    const { user } = useContext(UserContext);
    // user.nameだけを返す（userオブジェクト全体ではなく）
    return useMemo(() => user?.name, [user?.name]);
}

// コンポーネントで使用
function UserName() {
    const name = useUserName();
    // userオブジェクトの他のプロパティが変更されても、再レンダリングされない
    return <div>{name}</div>;
}
```

### 実践例: 選択的な値の取得

```javascript
// src/hooks/useUser.js
import { useContext, useMemo } from 'react';
import UserContext from '../contexts/UserContext';

export function useUser() {
    return useContext(UserContext);
}

export function useUserName() {
    const { user } = useContext(UserContext);
    return useMemo(() => user?.name, [user?.name]);
}

export function useUserAge() {
    const { user } = useContext(UserContext);
    return useMemo(() => user?.age, [user?.age]);
}

// src/components/UserInfo.jsx
import { useUserName, useUserAge } from '../hooks/useUser';

function UserInfo() {
    const name = useUserName();
    const age = useUserAge();
    
    // userオブジェクトの他のプロパティが変更されても、再レンダリングされない
    return (
        <div>
            <p>名前: {name}</p>
            <p>年齢: {age}歳</p>
        </div>
    );
}

export default UserInfo;
```

### パフォーマンス最適化のまとめ

1. **Contextを分割**: 関連のないデータは別々のContextに分ける
2. **useMemoでメモ化**: オブジェクトや配列を値として渡す場合はメモ化する
3. **React.memoでメモ化**: Contextを使用するコンポーネントをメモ化する
4. **選択的な値の取得**: 必要な値だけを取得するカスタムフックを作成する

---

## まとめ

この章では、ReactのContext APIについて学びました。

### 学んだこと
- **createContext**: Contextを作成する
- **Provider**: Contextの値を提供する
- **useContext**: Contextの値を読み取る
- **パフォーマンス最適化**: Contextの再レンダリングを防ぐ方法

### 重要なポイント
1. **Propsのバケツリレーを回避**: Context APIでデータを共有できる
2. **グローバル状態管理**: アプリ全体で状態を管理できる
3. **パフォーマンス**: Contextを適切に分割・最適化する
4. **カスタムフック**: Contextの使用を簡潔にする

### Context APIの使い分け
- **小規模な状態**: useStateで十分
- **中規模な状態**: Context API
- **大規模な状態**: Recoil、Jotai、Zustandなどのライブラリ

### 次のステップ
次の章では、Recoil / Jotai（またはZustand）について詳しく学びます。

---

## 演習問題

### 問題1: 基本的なContext
テーマ（light/dark）を管理するContextを作成し、コンポーネントで使用してください。

<details>
<summary>解答例</summary>

```javascript
// src/contexts/ThemeContext.jsx
import { createContext } from 'react';

const ThemeContext = createContext('light');

export default ThemeContext;

// src/App.jsx
import { useState } from 'react';
import ThemeContext from './contexts/ThemeContext';
import Header from './components/Header';

function App() {
    const [theme, setTheme] = useState('light');
    
    return (
        <ThemeContext.Provider value={theme}>
            <Header />
        </ThemeContext.Provider>
    );
}

// src/components/Header.jsx
import { useContext } from 'react';
import ThemeContext from '../contexts/ThemeContext';

function Header() {
    const theme = useContext(ThemeContext);
    
    return (
        <header className={`header ${theme}`}>
            <h1>ヘッダー</h1>
        </header>
    );
}
```
</details>

### 問題2: カスタムProvider
ユーザー情報を管理するカスタムProviderを作成してください。

<details>
<summary>解答例</summary>

```javascript
// src/contexts/UserContext.jsx
import { createContext, useState } from 'react';

const UserContext = createContext(null);

export function UserProvider({ children }) {
    const [user, setUser] = useState(null);
    
    return (
        <UserContext.Provider value={{ user, setUser }}>
            {children}
        </UserContext.Provider>
    );
}

export default UserContext;

// src/components/UserProfile.jsx
import { useContext } from 'react';
import UserContext from '../contexts/UserContext';

function UserProfile() {
    const { user } = useContext(UserContext);
    
    if (!user) {
        return <div>ログインしてください</div>;
    }
    
    return <div>ようこそ、{user.name}さん！</div>;
}
```
</details>

### 問題3: カスタムフック
Contextを使用するカスタムフックを作成してください。

<details>
<summary>解答例</summary>

```javascript
// src/hooks/useTheme.js
import { useContext } from 'react';
import ThemeContext from '../contexts/ThemeContext';

export function useTheme() {
    const context = useContext(ThemeContext);
    
    if (!context) {
        throw new Error('useTheme must be used within a ThemeProvider');
    }
    
    return context;
}

// src/components/ThemeButton.jsx
import { useTheme } from '../hooks/useTheme';

function ThemeButton() {
    const { theme, toggleTheme } = useTheme();
    
    return (
        <button onClick={toggleTheme}>
            現在のテーマ: {theme}
        </button>
    );
}
```
</details>

### 問題4: useMemoを使った最適化
useMemoを使って、Contextの値を最適化してください。

<details>
<summary>解答例</summary>

```javascript
// src/contexts/AppContext.jsx
import { createContext, useState, useMemo } from 'react';

const AppContext = createContext(null);

export function AppProvider({ children }) {
    const [user, setUser] = useState(null);
    const [theme, setTheme] = useState('light');
    
    const value = useMemo(() => ({
        user,
        setUser,
        theme,
        setTheme
    }), [user, theme]);
    
    return (
        <AppContext.Provider value={value}>
            {children}
        </AppContext.Provider>
    );
}
```
</details>

### 問題5: 複数のContext
テーマと言語を管理する2つのContextを作成し、コンポーネントで両方を使用してください。

<details>
<summary>解答例</summary>

```javascript
// src/contexts/ThemeContext.jsx
import { createContext } from 'react';

const ThemeContext = createContext('light');

export default ThemeContext;

// src/contexts/LanguageContext.jsx
import { createContext } from 'react';

const LanguageContext = createContext('ja');

export default LanguageContext;

// src/components/Settings.jsx
import { useContext } from 'react';
import ThemeContext from '../contexts/ThemeContext';
import LanguageContext from '../contexts/LanguageContext';

function Settings() {
    const theme = useContext(ThemeContext);
    const language = useContext(LanguageContext);
    
    return (
        <div>
            <p>テーマ: {theme}</p>
            <p>言語: {language}</p>
        </div>
    );
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

