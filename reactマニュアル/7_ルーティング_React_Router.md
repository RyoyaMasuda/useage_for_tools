# 7. ルーティング (React Router)

この章では、Reactアプリケーションでページ遷移を実現する「React Router」について学びます。React Routerを理解することで、シングルページアプリケーション（SPA）で複数のページを管理し、URLに基づいて適切なコンポーネントを表示できるようになります。

---

## 目次

- [ルーティングの設定 (BrowserRouter, Routes, Route)](#ルーティングの設定-browserrouter-routes-route)
- [ページ遷移 (Link, useNavigate)](#ページ遷移-link-usenavigate)
- [URLパラメータ (useParams) とクエリパラメータ](#urlパラメータ-useparams-とクエリパラメータ)
- [ネストされたルート (Outlet)](#ネストされたルート-outlet)
- [404ページの実装](#404ページの実装)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## ルーティングの設定 (BrowserRouter, Routes, Route)

### React Routerとは
React Routerは、**Reactアプリケーションでルーティング（ページ遷移）を実現する**ライブラリです。URLに基づいて適切なコンポーネントを表示し、シングルページアプリケーション（SPA）で複数のページを管理できます。

### 日常生活での例
- **本の目次**: 目次（ルーティング設定）を見て、該当するページ（コンポーネント）に移動する
- **住所**: 住所（URL）に基づいて、該当する建物（コンポーネント）を見つける
- **駅の案内板**: 案内板（ルーティング設定）を見て、目的のホーム（コンポーネント）に向かう

### React Routerの基本概念
1. **BrowserRouter**: ブラウザの履歴APIを使用してルーティングを管理
2. **Routes**: 複数のルートを定義するコンテナ
3. **Route**: 特定のURLパスとコンポーネントを対応付ける

### インストール

```bash
npm install react-router-dom
```

### 実践例: 基本的なルーティング設定

```jsx
// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';
import Contact from './pages/Contact';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/about" element={<About />} />
                <Route path="/contact" element={<Contact />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;

// src/pages/Home.jsx
function Home() {
    return (
        <div>
            <h1>ホームページ</h1>
            <p>ようこそ、ホームページへ！</p>
        </div>
    );
}

export default Home;

// src/pages/About.jsx
function About() {
    return (
        <div>
            <h1>Aboutページ</h1>
            <p>このサイトについて</p>
        </div>
    );
}

export default About;

// src/pages/Contact.jsx
function Contact() {
    return (
        <div>
            <h1>Contactページ</h1>
            <p>お問い合わせ</p>
        </div>
    );
}

export default Contact;
```

### BrowserRouterとは
BrowserRouterは、**HTML5のHistory APIを使用してルーティングを管理する**コンポーネントです。通常、アプリケーションの最上位で使用します。

### 実践例: BrowserRouterの使用

```jsx
// src/main.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
    <React.StrictMode>
        <BrowserRouter>
            <App />
        </BrowserRouter>
    </React.StrictMode>
);

// src/App.jsx
import { Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';

function App() {
    return (
        <Routes>
            <Route path="/" element={<Home />} />
            <Route path="/about" element={<About />} />
        </Routes>
    );
}

export default App;
```

### RoutesとRouteとは
- **Routes**: 複数のRouteをまとめるコンテナコンポーネント
- **Route**: 特定のパスとコンポーネントを対応付ける

### 実践例: 複数のルート

```jsx
// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import Products from './pages/Products';
import ProductDetail from './pages/ProductDetail';
import Cart from './pages/Cart';
import Checkout from './pages/Checkout';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/products" element={<Products />} />
                <Route path="/products/:id" element={<ProductDetail />} />
                <Route path="/cart" element={<Cart />} />
                <Route path="/checkout" element={<Checkout />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;
```

### ルートパスの種類

```jsx
// 完全一致
<Route path="/about" element={<About />} />

// 動的パラメータ
<Route path="/users/:id" element={<UserDetail />} />

// ネストされたルート
<Route path="/dashboard/*" element={<Dashboard />} />

// ワイルドカード（404ページなど）
<Route path="*" element={<NotFound />} />
```

---

## ページ遷移 (Link, useNavigate)

### Linkとは
Linkは、**ページ遷移を行うためのコンポーネント**です。通常の`<a>`タグと似ていますが、ページ全体をリロードせずに遷移できます。

### useNavigateとは
useNavigateは、**プログラムからページ遷移を行うためのフック**です。ボタンのクリックやフォーム送信後など、条件に応じて遷移する際に使用します。

### 実践例: Linkを使ったページ遷移

```jsx
// src/components/Navigation.jsx
import { Link } from 'react-router-dom';

function Navigation() {
    return (
        <nav style={{
            backgroundColor: '#333',
            padding: '16px',
            display: 'flex',
            gap: '16px'
        }}>
            <Link 
                to="/" 
                style={{ color: '#fff', textDecoration: 'none' }}
            >
                ホーム
            </Link>
            <Link 
                to="/about" 
                style={{ color: '#fff', textDecoration: 'none' }}
            >
                About
            </Link>
            <Link 
                to="/products" 
                style={{ color: '#fff', textDecoration: 'none' }}
            >
                商品一覧
            </Link>
            <Link 
                to="/contact" 
                style={{ color: '#fff', textDecoration: 'none' }}
            >
                お問い合わせ
            </Link>
        </nav>
    );
}

export default Navigation;

// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Navigation from './components/Navigation';
import Home from './pages/Home';
import About from './pages/About';
import Products from './pages/Products';
import Contact from './pages/Contact';

function App() {
    return (
        <BrowserRouter>
            <Navigation />
            <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/about" element={<About />} />
                <Route path="/products" element={<Products />} />
                <Route path="/contact" element={<Contact />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;
```

### 実践例: useNavigateを使ったページ遷移

```jsx
// src/pages/Login.jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';

function Login() {
    const [email, setEmail] = useState('');
    const [password, setPassword] = useState('');
    const navigate = useNavigate();

    const handleSubmit = (e) => {
        e.preventDefault();
        
        // ログイン処理（模擬）
        if (email && password) {
            console.log('ログイン成功');
            // ログイン成功後、ダッシュボードに遷移
            navigate('/dashboard');
        } else {
            alert('メールアドレスとパスワードを入力してください');
        }
    };

    return (
        <div style={{ maxWidth: '400px', margin: '0 auto', padding: '32px' }}>
            <h1>ログイン</h1>
            <form onSubmit={handleSubmit}>
                <div style={{ marginBottom: '16px' }}>
                    <label>メールアドレス</label>
                    <input
                        type="email"
                        value={email}
                        onChange={(e) => setEmail(e.target.value)}
                        style={{ width: '100%', padding: '8px' }}
                    />
                </div>
                <div style={{ marginBottom: '16px' }}>
                    <label>パスワード</label>
                    <input
                        type="password"
                        value={password}
                        onChange={(e) => setPassword(e.target.value)}
                        style={{ width: '100%', padding: '8px' }}
                    />
                </div>
                <button type="submit" style={{ width: '100%', padding: '8px' }}>
                    ログイン
                </button>
            </form>
            <button 
                onClick={() => navigate('/register')}
                style={{ marginTop: '16px', width: '100%', padding: '8px' }}
            >
                新規登録
            </button>
        </div>
    );
}

export default Login;
```

### 実践例: 条件付き遷移

```jsx
// src/pages/ProductDetail.jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';

function ProductDetail({ productId }) {
    const [quantity, setQuantity] = useState(1);
    const navigate = useNavigate();

    const handleAddToCart = () => {
        // カートに追加する処理
        console.log(`商品${productId}を${quantity}個カートに追加`);
        
        // カートページに遷移
        navigate('/cart');
    };

    const handleBack = () => {
        // 前のページに戻る
        navigate(-1);
    };

    return (
        <div style={{ padding: '32px' }}>
            <button onClick={handleBack}>戻る</button>
            <h1>商品詳細 {productId}</h1>
            <div>
                <label>数量: </label>
                <input
                    type="number"
                    value={quantity}
                    onChange={(e) => setQuantity(Number(e.target.value))}
                    min="1"
                />
            </div>
            <button onClick={handleAddToCart}>カートに追加</button>
        </div>
    );
}

export default ProductDetail;
```

### 実践例: リダイレクト

```jsx
// src/pages/ProtectedRoute.jsx
import { Navigate } from 'react-router-dom';

function ProtectedRoute({ children, isAuthenticated }) {
    if (!isAuthenticated) {
        // 認証されていない場合、ログインページにリダイレクト
        return <Navigate to="/login" replace />;
    }

    return children;
}

// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import ProtectedRoute from './pages/ProtectedRoute';
import Dashboard from './pages/Dashboard';
import Login from './pages/Login';

function App() {
    const isAuthenticated = true; // 実際は認証状態を管理

    return (
        <BrowserRouter>
            <Routes>
                <Route path="/login" element={<Login />} />
                <Route
                    path="/dashboard"
                    element={
                        <ProtectedRoute isAuthenticated={isAuthenticated}>
                            <Dashboard />
                        </ProtectedRoute>
                    }
                />
            </Routes>
        </BrowserRouter>
    );
}

export default App;
```

---

## URLパラメータ (useParams) とクエリパラメータ

### URLパラメータとは
URLパラメータは、**URLの一部として含まれる動的な値**です。例: `/users/123` の `123` がユーザーIDのパラメータです。

### クエリパラメータとは
クエリパラメータは、**URLの`?`以降に含まれる値**です。例: `/search?q=react&page=1` の `q=react` と `page=1` がクエリパラメータです。

### useParamsとは
useParamsは、**URLパラメータを取得するためのフック**です。ルート定義で`:id`のように定義したパラメータを取得できます。

### useSearchParamsとは
useSearchParamsは、**クエリパラメータを取得・設定するためのフック**です。URLの`?`以降のパラメータを操作できます。

### 実践例: URLパラメータの使用

```jsx
// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import UserList from './pages/UserList';
import UserDetail from './pages/UserDetail';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/users" element={<UserList />} />
                <Route path="/users/:id" element={<UserDetail />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;

// src/pages/UserList.jsx
import { Link } from 'react-router-dom';

function UserList() {
    const users = [
        { id: 1, name: '太郎', email: 'taro@example.com' },
        { id: 2, name: '花子', email: 'hanako@example.com' },
        { id: 3, name: '次郎', email: 'jiro@example.com' }
    ];

    return (
        <div style={{ padding: '32px' }}>
            <h1>ユーザー一覧</h1>
            <ul>
                {users.map(user => (
                    <li key={user.id}>
                        <Link to={`/users/${user.id}`}>
                            {user.name} ({user.email})
                        </Link>
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default UserList;

// src/pages/UserDetail.jsx
import { useParams, Link } from 'react-router-dom';

function UserDetail() {
    const { id } = useParams(); // URLパラメータを取得

    // 実際のアプリケーションでは、APIからユーザー情報を取得
    const users = {
        1: { id: 1, name: '太郎', email: 'taro@example.com', age: 25 },
        2: { id: 2, name: '花子', email: 'hanako@example.com', age: 30 },
        3: { id: 3, name: '次郎', email: 'jiro@example.com', age: 28 }
    };

    const user = users[id];

    if (!user) {
        return (
            <div>
                <h1>ユーザーが見つかりません</h1>
                <Link to="/users">ユーザー一覧に戻る</Link>
            </div>
        );
    }

    return (
        <div style={{ padding: '32px' }}>
            <Link to="/users">← ユーザー一覧に戻る</Link>
            <h1>ユーザー詳細</h1>
            <p>ID: {user.id}</p>
            <p>名前: {user.name}</p>
            <p>メール: {user.email}</p>
            <p>年齢: {user.age}歳</p>
        </div>
    );
}

export default UserDetail;
```

### 実践例: 複数のURLパラメータ

```jsx
// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import ProductList from './pages/ProductList';
import ProductDetail from './pages/ProductDetail';
import CategoryProduct from './pages/CategoryProduct';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/products" element={<ProductList />} />
                <Route path="/products/:category/:id" element={<CategoryProduct />} />
                <Route path="/products/:id" element={<ProductDetail />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;

// src/pages/CategoryProduct.jsx
import { useParams } from 'react-router-dom';

function CategoryProduct() {
    const { category, id } = useParams(); // 複数のパラメータを取得

    return (
        <div style={{ padding: '32px' }}>
            <h1>カテゴリ: {category}</h1>
            <h2>商品ID: {id}</h2>
            <p>カテゴリ{category}の商品{id}の詳細ページです</p>
        </div>
    );
}

export default CategoryProduct;
```

### 実践例: クエリパラメータの使用

```jsx
// src/pages/Search.jsx
import { useState } from 'react';
import { useSearchParams, Link } from 'react-router-dom';

function Search() {
    const [searchParams, setSearchParams] = useSearchParams();
    const [query, setQuery] = useState(searchParams.get('q') || '');

    const handleSearch = (e) => {
        e.preventDefault();
        // クエリパラメータを設定
        setSearchParams({ q: query });
    };

    const currentQuery = searchParams.get('q');
    const page = parseInt(searchParams.get('page') || '1');

    // 検索結果（模擬）
    const results = currentQuery
        ? [
            { id: 1, title: `${currentQuery}の結果1` },
            { id: 2, title: `${currentQuery}の結果2` },
            { id: 3, title: `${currentQuery}の結果3` }
          ]
        : [];

    return (
        <div style={{ padding: '32px' }}>
            <h1>検索</h1>
            <form onSubmit={handleSearch}>
                <input
                    type="text"
                    value={query}
                    onChange={(e) => setQuery(e.target.value)}
                    placeholder="検索..."
                    style={{ padding: '8px', width: '300px' }}
                />
                <button type="submit" style={{ padding: '8px', marginLeft: '8px' }}>
                    検索
                </button>
            </form>

            {currentQuery && (
                <div style={{ marginTop: '32px' }}>
                    <h2>検索結果: "{currentQuery}"</h2>
                    <p>ページ: {page}</p>
                    <ul>
                        {results.map(result => (
                            <li key={result.id}>
                                <Link to={`/products/${result.id}`}>
                                    {result.title}
                                </Link>
                            </li>
                        ))}
                    </ul>
                    <div style={{ marginTop: '16px' }}>
                        <button
                            onClick={() => setSearchParams({ q: currentQuery, page: page - 1 })}
                            disabled={page <= 1}
                        >
                            前のページ
                        </button>
                        <span style={{ margin: '0 16px' }}>{page}</span>
                        <button
                            onClick={() => setSearchParams({ q: currentQuery, page: page + 1 })}
                        >
                            次のページ
                        </button>
                    </div>
                </div>
            )}
        </div>
    );
}

export default Search;
```

### 実践例: URLパラメータとクエリパラメータの組み合わせ

```jsx
// src/pages/ProductList.jsx
import { Link, useSearchParams } from 'react-router-dom';

function ProductList() {
    const [searchParams] = useSearchParams();
    const category = searchParams.get('category');
    const sort = searchParams.get('sort') || 'name';

    const products = [
        { id: 1, name: 'ノートパソコン', category: 'electronics', price: 98000 },
        { id: 2, name: 'マウス', category: 'electronics', price: 2500 },
        { id: 3, name: 'デスク', category: 'furniture', price: 15000 },
        { id: 4, name: '椅子', category: 'furniture', price: 8000 }
    ];

    // カテゴリでフィルタリング
    let filteredProducts = category
        ? products.filter(p => p.category === category)
        : products;

    // ソート
    filteredProducts.sort((a, b) => {
        if (sort === 'name') {
            return a.name.localeCompare(b.name);
        } else if (sort === 'price') {
            return a.price - b.price;
        }
        return 0;
    });

    return (
        <div style={{ padding: '32px' }}>
            <h1>商品一覧</h1>
            <div style={{ marginBottom: '16px' }}>
                <Link to="/products?category=electronics">電子機器</Link>
                {' | '}
                <Link to="/products?category=furniture">家具</Link>
                {' | '}
                <Link to="/products">すべて</Link>
            </div>
            <div style={{ marginBottom: '16px' }}>
                <Link to={`/products?category=${category || ''}&sort=name`}>
                    名前でソート
                </Link>
                {' | '}
                <Link to={`/products?category=${category || ''}&sort=price`}>
                    価格でソート
                </Link>
            </div>
            <ul>
                {filteredProducts.map(product => (
                    <li key={product.id}>
                        <Link to={`/products/${product.id}`}>
                            {product.name} - ¥{product.price.toLocaleString()}
                        </Link>
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default ProductList;
```

---

## ネストされたルート (Outlet)

### Outletとは
Outletは、**ネストされたルート（子ルート）を表示するためのコンポーネント**です。親ルートのコンポーネント内で、子ルートのコンポーネントを表示する場所を指定します。

### ネストされたルートとは
ネストされたルートは、**親ルートの下に子ルートを定義する**構造です。レイアウトを共有しながら、異なるコンテンツを表示できます。

### 実践例: 基本的なネストされたルート

```jsx
// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Dashboard from './pages/Dashboard';
import DashboardHome from './pages/DashboardHome';
import DashboardUsers from './pages/DashboardUsers';
import DashboardSettings from './pages/DashboardSettings';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/dashboard" element={<Dashboard />}>
                    <Route index element={<DashboardHome />} />
                    <Route path="users" element={<DashboardUsers />} />
                    <Route path="settings" element={<DashboardSettings />} />
                </Route>
            </Routes>
        </BrowserRouter>
    );
}

export default App;

// src/pages/Dashboard.jsx
import { Outlet, Link } from 'react-router-dom';

function Dashboard() {
    return (
        <div style={{ display: 'flex' }}>
            <aside style={{
                width: '200px',
                backgroundColor: '#f5f5f5',
                padding: '16px',
                minHeight: '100vh'
            }}>
                <h2>ダッシュボード</h2>
                <nav style={{ display: 'flex', flexDirection: 'column', gap: '8px' }}>
                    <Link to="/dashboard">ホーム</Link>
                    <Link to="/dashboard/users">ユーザー</Link>
                    <Link to="/dashboard/settings">設定</Link>
                </nav>
            </aside>
            <main style={{ flex: 1, padding: '32px' }}>
                <Outlet /> {/* 子ルートがここに表示される */}
            </main>
        </div>
    );
}

export default Dashboard;

// src/pages/DashboardHome.jsx
function DashboardHome() {
    return (
        <div>
            <h1>ダッシュボードホーム</h1>
            <p>ようこそ、ダッシュボードへ！</p>
        </div>
    );
}

export default DashboardHome;

// src/pages/DashboardUsers.jsx
function DashboardUsers() {
    return (
        <div>
            <h1>ユーザー管理</h1>
            <p>ユーザー一覧がここに表示されます</p>
        </div>
    );
}

export default DashboardUsers;

// src/pages/DashboardSettings.jsx
function DashboardSettings() {
    return (
        <div>
            <h1>設定</h1>
            <p>設定ページがここに表示されます</p>
        </div>
    );
}

export default DashboardSettings;
```

### 実践例: 複数階層のネストされたルート

```jsx
// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import AdminLayout from './pages/AdminLayout';
import AdminDashboard from './pages/AdminDashboard';
import AdminUsers from './pages/AdminUsers';
import AdminUserDetail from './pages/AdminUserDetail';
import AdminProducts from './pages/AdminProducts';
import AdminProductDetail from './pages/AdminProductDetail';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/admin" element={<AdminLayout />}>
                    <Route index element={<AdminDashboard />} />
                    <Route path="users" element={<AdminUsers />} />
                    <Route path="users/:id" element={<AdminUserDetail />} />
                    <Route path="products" element={<AdminProducts />} />
                    <Route path="products/:id" element={<AdminProductDetail />} />
                </Route>
            </Routes>
        </BrowserRouter>
    );
}

export default App;

// src/pages/AdminLayout.jsx
import { Outlet, Link, useLocation } from 'react-router-dom';

function AdminLayout() {
    const location = useLocation();

    return (
        <div style={{ display: 'flex', flexDirection: 'column', minHeight: '100vh' }}>
            <header style={{
                backgroundColor: '#333',
                color: '#fff',
                padding: '16px'
            }}>
                <h1>管理画面</h1>
            </header>
            <div style={{ display: 'flex', flex: 1 }}>
                <aside style={{
                    width: '200px',
                    backgroundColor: '#f5f5f5',
                    padding: '16px'
                }}>
                    <nav style={{ display: 'flex', flexDirection: 'column', gap: '8px' }}>
                        <Link 
                            to="/admin"
                            style={{
                                color: location.pathname === '/admin' ? '#007bff' : '#333',
                                textDecoration: 'none'
                            }}
                        >
                            ダッシュボード
                        </Link>
                        <Link 
                            to="/admin/users"
                            style={{
                                color: location.pathname.startsWith('/admin/users') ? '#007bff' : '#333',
                                textDecoration: 'none'
                            }}
                        >
                            ユーザー管理
                        </Link>
                        <Link 
                            to="/admin/products"
                            style={{
                                color: location.pathname.startsWith('/admin/products') ? '#007bff' : '#333',
                                textDecoration: 'none'
                            }}
                        >
                            商品管理
                        </Link>
                    </nav>
                </aside>
                <main style={{ flex: 1, padding: '32px' }}>
                    <Outlet />
                </main>
            </div>
        </div>
    );
}

export default AdminLayout;
```

### 実践例: ネストされたルートでのナビゲーション

```jsx
// src/pages/AdminUsers.jsx
import { Link, Outlet } from 'react-router-dom';
import { useLocation } from 'react-router-dom';

function AdminUsers() {
    const location = useLocation();

    return (
        <div>
            <h1>ユーザー管理</h1>
            <nav style={{ marginBottom: '16px', display: 'flex', gap: '16px' }}>
                <Link 
                    to="/admin/users"
                    style={{
                        color: location.pathname === '/admin/users' ? '#007bff' : '#333',
                        textDecoration: 'none'
                    }}
                >
                    ユーザー一覧
                </Link>
                <Link 
                    to="/admin/users/new"
                    style={{
                        color: location.pathname === '/admin/users/new' ? '#007bff' : '#333',
                        textDecoration: 'none'
                    }}
                >
                    新規作成
                </Link>
            </nav>
            <Outlet />
        </div>
    );
}

// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import AdminLayout from './pages/AdminLayout';
import AdminUsers from './pages/AdminUsers';
import UserList from './pages/UserList';
import UserNew from './pages/UserNew';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/admin" element={<AdminLayout />}>
                    <Route path="users" element={<AdminUsers />}>
                        <Route index element={<UserList />} />
                        <Route path="new" element={<UserNew />} />
                    </Route>
                </Route>
            </Routes>
        </BrowserRouter>
    );
}

export default App;
```

---

## 404ページの実装

### 404ページとは
404ページは、**存在しないURLにアクセスした際に表示するページ**です。ユーザーが間違ったURLにアクセスした場合や、削除されたページにアクセスした場合に表示されます。

### 実践例: 基本的な404ページ

```jsx
// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';
import NotFound from './pages/NotFound';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/about" element={<About />} />
                <Route path="*" element={<NotFound />} /> {/* 404ページ */}
            </Routes>
        </BrowserRouter>
    );
}

export default App;

// src/pages/NotFound.jsx
import { Link } from 'react-router-dom';

function NotFound() {
    return (
        <div style={{
            display: 'flex',
            flexDirection: 'column',
            alignItems: 'center',
            justifyContent: 'center',
            minHeight: '100vh',
            textAlign: 'center'
        }}>
            <h1 style={{ fontSize: '72px', margin: 0 }}>404</h1>
            <h2>ページが見つかりません</h2>
            <p>お探しのページは存在しないか、移動または削除された可能性があります。</p>
            <Link 
                to="/"
                style={{
                    padding: '12px 24px',
                    backgroundColor: '#007bff',
                    color: '#fff',
                    textDecoration: 'none',
                    borderRadius: '4px',
                    marginTop: '16px'
                }}
            >
                ホームに戻る
            </Link>
        </div>
    );
}

export default NotFound;
```

### 実践例: 詳細な404ページ

```jsx
// src/pages/NotFound.jsx
import { Link, useNavigate } from 'react-router-dom';

function NotFound() {
    const navigate = useNavigate();

    return (
        <div style={{
            display: 'flex',
            flexDirection: 'column',
            alignItems: 'center',
            justifyContent: 'center',
            minHeight: '100vh',
            textAlign: 'center',
            padding: '32px'
        }}>
            <div style={{ fontSize: '120px', fontWeight: 'bold', color: '#ccc' }}>
                404
            </div>
            <h1 style={{ marginTop: '16px' }}>ページが見つかりません</h1>
            <p style={{ color: '#666', marginTop: '8px' }}>
                お探しのページは存在しないか、移動または削除された可能性があります。
            </p>
            <div style={{ marginTop: '32px', display: 'flex', gap: '16px' }}>
                <button
                    onClick={() => navigate(-1)}
                    style={{
                        padding: '12px 24px',
                        backgroundColor: '#6c757d',
                        color: '#fff',
                        border: 'none',
                        borderRadius: '4px',
                        cursor: 'pointer'
                    }}
                >
                    前のページに戻る
                </button>
                <Link
                    to="/"
                    style={{
                        padding: '12px 24px',
                        backgroundColor: '#007bff',
                        color: '#fff',
                        textDecoration: 'none',
                        borderRadius: '4px'
                    }}
                >
                    ホームに戻る
                </Link>
            </div>
            <div style={{ marginTop: '48px' }}>
                <h3>よくアクセスされるページ</h3>
                <ul style={{ listStyle: 'none', padding: 0 }}>
                    <li style={{ margin: '8px 0' }}>
                        <Link to="/" style={{ color: '#007bff', textDecoration: 'none' }}>
                            ホーム
                        </Link>
                    </li>
                    <li style={{ margin: '8px 0' }}>
                        <Link to="/about" style={{ color: '#007bff', textDecoration: 'none' }}>
                            About
                        </Link>
                    </li>
                    <li style={{ margin: '8px 0' }}>
                        <Link to="/contact" style={{ color: '#007bff', textDecoration: 'none' }}>
                            お問い合わせ
                        </Link>
                    </li>
                </ul>
            </div>
        </div>
    );
}

export default NotFound;
```

### 実践例: 条件付き404ページ

```jsx
// src/pages/ProductDetail.jsx
import { useParams, Link, Navigate } from 'react-router-dom';

function ProductDetail() {
    const { id } = useParams();

    // 実際のアプリケーションでは、APIから商品情報を取得
    const products = {
        1: { id: 1, name: 'ノートパソコン', price: 98000 },
        2: { id: 2, name: 'マウス', price: 2500 },
        3: { id: 3, name: 'キーボード', price: 5000 }
    };

    const product = products[id];

    // 商品が見つからない場合、404ページにリダイレクト
    if (!product) {
        return <Navigate to="/404" replace />;
    }

    return (
        <div style={{ padding: '32px' }}>
            <Link to="/products">← 商品一覧に戻る</Link>
            <h1>{product.name}</h1>
            <p>価格: ¥{product.price.toLocaleString()}</p>
        </div>
    );
}

// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Products from './pages/Products';
import ProductDetail from './pages/ProductDetail';
import NotFound from './pages/NotFound';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/products" element={<Products />} />
                <Route path="/products/:id" element={<ProductDetail />} />
                <Route path="/404" element={<NotFound />} />
                <Route path="*" element={<NotFound />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;
```

---

## まとめ

この章では、React Routerについて以下のことを学びました。

### ルーティングの設定
- **BrowserRouter**: ブラウザの履歴APIを使用してルーティングを管理
- **Routes**: 複数のルートを定義するコンテナ
- **Route**: 特定のURLパスとコンポーネントを対応付ける
- ルートパスには完全一致、動的パラメータ、ネストされたルート、ワイルドカードがある

### ページ遷移
- **Link**: ページ遷移を行うためのコンポーネント（`<a>`タグの代替）
- **useNavigate**: プログラムからページ遷移を行うためのフック
- **Navigate**: リダイレクトを行うためのコンポーネント
- 条件付き遷移や、前のページへの戻りなどが可能

### URLパラメータとクエリパラメータ
- **useParams**: URLパラメータを取得するためのフック（例: `/users/:id`）
- **useSearchParams**: クエリパラメータを取得・設定するためのフック（例: `?q=react`）
- 複数のパラメータを組み合わせて使用できる

### ネストされたルート
- **Outlet**: ネストされたルート（子ルート）を表示するためのコンポーネント
- 親ルートのレイアウトを共有しながら、異なるコンテンツを表示できる
- 複数階層のネストも可能

### 404ページ
- 存在しないURLにアクセスした際に表示するページ
- ワイルドカードルート（`path="*"`）を使用して実装
- ユーザーフレンドリーなエラーページを提供できる

### React Routerの活用場面
- シングルページアプリケーション（SPA）の構築
- 複数ページの管理
- 認証が必要なページの保護
- URLに基づいたコンテンツの表示

### 次のステップ
React Routerを理解したら、次のトピックに進みましょう：
- グローバル状態管理（8章）
- API連携とデータフェッチ（9章）
- React × TypeScript 実践（10章）

---

## 演習問題

以下の問題を解いて、React Routerの理解を深めましょう。

### 問題1: 基本的なルーティング
`Home`、`About`、`Contact`の3つのページを作成し、ルーティングを設定してください。

<details>
<summary>解答例</summary>

```jsx
// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';
import Contact from './pages/Contact';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/about" element={<About />} />
                <Route path="/contact" element={<Contact />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;
```
</details>

### 問題2: Linkを使ったナビゲーション
ナビゲーションバーを作成し、`Link`を使って各ページに遷移できるようにしてください。

<details>
<summary>解答例</summary>

```jsx
// src/components/Navigation.jsx
import { Link } from 'react-router-dom';

function Navigation() {
    return (
        <nav style={{
            backgroundColor: '#333',
            padding: '16px',
            display: 'flex',
            gap: '16px'
        }}>
            <Link to="/" style={{ color: '#fff', textDecoration: 'none' }}>
                ホーム
            </Link>
            <Link to="/about" style={{ color: '#fff', textDecoration: 'none' }}>
                About
            </Link>
            <Link to="/contact" style={{ color: '#fff', textDecoration: 'none' }}>
                お問い合わせ
            </Link>
        </nav>
    );
}

export default Navigation;
```
</details>

### 問題3: URLパラメータの使用
`/users/:id`のルートを作成し、`useParams`を使ってユーザーIDを取得して表示してください。

<details>
<summary>解答例</summary>

```jsx
// src/pages/UserDetail.jsx
import { useParams } from 'react-router-dom';

function UserDetail() {
    const { id } = useParams();

    return (
        <div>
            <h1>ユーザー詳細</h1>
            <p>ユーザーID: {id}</p>
        </div>
    );
}

export default UserDetail;

// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import UserDetail from './pages/UserDetail';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/users/:id" element={<UserDetail />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;
```
</details>

### 問題4: ネストされたルート
`Dashboard`レイアウトを作成し、その中に`DashboardHome`と`DashboardSettings`の子ルートを設定してください。

<details>
<summary>解答例</summary>

```jsx
// src/pages/Dashboard.jsx
import { Outlet, Link } from 'react-router-dom';

function Dashboard() {
    return (
        <div style={{ display: 'flex' }}>
            <aside style={{ width: '200px', backgroundColor: '#f5f5f5', padding: '16px' }}>
                <nav>
                    <Link to="/dashboard">ホーム</Link>
                    <br />
                    <Link to="/dashboard/settings">設定</Link>
                </nav>
            </aside>
            <main style={{ flex: 1, padding: '32px' }}>
                <Outlet />
            </main>
        </div>
    );
}

export default Dashboard;

// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Dashboard from './pages/Dashboard';
import DashboardHome from './pages/DashboardHome';
import DashboardSettings from './pages/DashboardSettings';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/dashboard" element={<Dashboard />}>
                    <Route index element={<DashboardHome />} />
                    <Route path="settings" element={<DashboardSettings />} />
                </Route>
            </Routes>
        </BrowserRouter>
    );
}

export default App;
```
</details>

### 問題5: 404ページの実装
存在しないURLにアクセスした際に表示する404ページを作成してください。

<details>
<summary>解答例</summary>

```jsx
// src/pages/NotFound.jsx
import { Link } from 'react-router-dom';

function NotFound() {
    return (
        <div style={{ textAlign: 'center', padding: '32px' }}>
            <h1>404</h1>
            <h2>ページが見つかりません</h2>
            <p>お探しのページは存在しないか、移動または削除された可能性があります。</p>
            <Link to="/">ホームに戻る</Link>
        </div>
    );
}

export default NotFound;

// src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import NotFound from './pages/NotFound';

function App() {
    return (
        <BrowserRouter>
            <Routes>
                <Route path="/" element={<Home />} />
                <Route path="*" element={<NotFound />} />
            </Routes>
        </BrowserRouter>
    );
}

export default App;
```
</details>

---

お疲れ様でした！次の章に進みましょう。

