# 2-1. コンポーネント開発の基礎（Props）

この章では、Reactコンポーネント間でデータを渡す「Props（プロパティ）」について学びます。Propsを理解することで、コンポーネントを再利用可能にし、柔軟で保守しやすいコードを書けるようになります。

---

## 目次

- [親から子へのデータ受け渡し](#親から子へのデータ受け渡し)
- [Propsの分割代入](#propsの分割代入)
- [children プロップスの活用](#children-プロップスの活用)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## 親から子へのデータ受け渡し

### Propsとは
Props（プロパティ）は、**親コンポーネントから子コンポーネントにデータを渡す**仕組みです。関数の引数のように、コンポーネントに情報を渡すことができます。

### 日常生活での例
- **レシピの材料**: 料理のレシピ（親）が、必要な材料（Props）を各手順（子）に渡す
- **学校の連絡**: 担任の先生（親）が、連絡事項（Props）を各生徒（子）に伝える
- **工場の指示**: 工場長（親）が、作業指示（Props）を各作業員（子）に伝える

### プログラミングでの例

```jsx
// 親コンポーネント
function App() {
    return (
        <div>
            <Greeting name="太郎" />
            <Greeting name="花子" />
        </div>
    );
}

// 子コンポーネント（Propsを受け取る）
function Greeting({ name }) {
    return <h1>こんにちは、{name}さん！</h1>;
}
```

### Propsの基本構文

```jsx
// 親コンポーネントでPropsを渡す
<コンポーネント名 プロパティ名1={値1} プロパティ名2={値2} />

// 子コンポーネントでPropsを受け取る
function コンポーネント名(props) {
    return <div>{props.プロパティ名1}</div>;
}

// または分割代入を使う
function コンポーネント名({ プロパティ名1, プロパティ名2 }) {
    return <div>{プロパティ名1}</div>;
}
```

### Propsの特徴
1. **一方向のデータフロー**: 親から子へのみデータが流れる
2. **読み取り専用**: Propsは変更できない（immutable）
3. **型安全性**: TypeScriptを使うと、Propsの型を定義できる
4. **再利用性**: 同じコンポーネントに異なるPropsを渡して再利用できる

### 実践例: 親から子へのデータ受け渡し

```jsx
// src/components/UserCard.jsx
// 子コンポーネント: ユーザーカード
function UserCard({ name, age, email }) {
    return (
        <div style={{ border: '1px solid #ccc', padding: '16px', margin: '8px' }}>
            <h2>{name}</h2>
            <p>年齢: {age}歳</p>
            <p>メール: {email}</p>
        </div>
    );
}

// src/App.jsx
// 親コンポーネント: ユーザー情報を子コンポーネントに渡す
function App() {
    return (
        <div>
            <h1>ユーザー一覧</h1>
            <UserCard name="太郎" age={25} email="taro@example.com" />
            <UserCard name="花子" age={30} email="hanako@example.com" />
            <UserCard name="次郎" age={28} email="jiro@example.com" />
        </div>
    );
}

export default App;
```

### 複数のPropsを渡す

```jsx
// src/components/ProductCard.jsx
// 商品カードコンポーネント
function ProductCard({ id, name, price, inStock }) {
    return (
        <div style={{ 
            border: '1px solid #ddd', 
            padding: '16px', 
            margin: '8px',
            backgroundColor: inStock ? '#fff' : '#f5f5f5'
        }}>
            <h3>{name}</h3>
            <p>価格: ¥{price.toLocaleString()}</p>
            <p>在庫: {inStock ? 'あり' : 'なし'}</p>
        </div>
    );
}

// src/App.jsx
function App() {
    const products = [
        { id: 1, name: "ノートパソコン", price: 98000, inStock: true },
        { id: 2, name: "マウス", price: 2500, inStock: true },
        { id: 3, name: "キーボード", price: 5000, inStock: false }
    ];

    return (
        <div>
            <h1>商品一覧</h1>
            {products.map(product => (
                <ProductCard
                    key={product.id}
                    id={product.id}
                    name={product.name}
                    price={product.price}
                    inStock={product.inStock}
                />
            ))}
        </div>
    );
}

export default App;
```

### Propsでオブジェクトを渡す

```jsx
// src/components/UserProfile.jsx
// オブジェクト全体をPropsとして渡す
function UserProfile({ user }) {
    return (
        <div>
            <h2>{user.name}</h2>
            <p>年齢: {user.age}歳</p>
            <p>メール: {user.email}</p>
            <p>住所: {user.address}</p>
        </div>
    );
}

// src/App.jsx
function App() {
    const user = {
        name: "太郎",
        age: 25,
        email: "taro@example.com",
        address: "東京都渋谷区"
    };

    return (
        <div>
            <UserProfile user={user} />
        </div>
    );
}

export default App;
```

### Propsで関数を渡す

```jsx
// src/components/Button.jsx
// 関数をPropsとして渡す
function Button({ label, onClick }) {
    return (
        <button onClick={onClick} style={{ padding: '8px 16px', margin: '4px' }}>
            {label}
        </button>
    );
}

// src/App.jsx
function App() {
    const handleClick = () => {
        alert('ボタンがクリックされました！');
    };

    const handleSubmit = () => {
        console.log('フォームが送信されました');
    };

    return (
        <div>
            <Button label="クリック" onClick={handleClick} />
            <Button label="送信" onClick={handleSubmit} />
        </div>
    );
}

export default App;
```

### Propsのデフォルト値

```jsx
// src/components/Greeting.jsx
// Propsにデフォルト値を設定
function Greeting({ name = "ゲスト", greeting = "こんにちは" }) {
    return (
        <div>
            <h1>{greeting}、{name}さん！</h1>
        </div>
    );
}

// src/App.jsx
function App() {
    return (
        <div>
            <Greeting name="太郎" greeting="おはよう" />
            <Greeting name="花子" />  {/* greetingはデフォルト値が使われる */}
            <Greeting />  {/* nameとgreetingの両方がデフォルト値 */}
        </div>
    );
}

export default App;
```

---

## Propsの分割代入

### 分割代入とは
分割代入は、**オブジェクトや配列から値を取り出して、変数に代入する**構文です。Propsを受け取る際に、分割代入を使うとコードが簡潔になります。

### 分割代入の基本構文

```jsx
// 通常の書き方
function Component(props) {
    return <div>{props.name}</div>;
}

// 分割代入を使った書き方（推奨）
function Component({ name, age }) {
    return <div>{name}、{age}歳</div>;
}
```

### 分割代入のメリット
1. **コードが簡潔**: `props.`を繰り返し書く必要がない
2. **可読性の向上**: どのPropsを使うか一目で分かる
3. **型定義が明確**: TypeScriptを使う場合、型定義が分かりやすい

### 実践例: Propsの分割代入

```jsx
// src/components/UserInfo.jsx
// 分割代入を使ったコンポーネント
function UserInfo({ name, age, email, isActive }) {
    return (
        <div style={{ 
            padding: '16px', 
            border: '1px solid #ccc',
            backgroundColor: isActive ? '#e8f5e9' : '#f5f5f5'
        }}>
            <h2>{name}</h2>
            <p>年齢: {age}歳</p>
            <p>メール: {email}</p>
            <p>ステータス: {isActive ? 'アクティブ' : '非アクティブ'}</p>
        </div>
    );
}

// src/App.jsx
function App() {
    return (
        <div>
            <UserInfo 
                name="太郎" 
                age={25} 
                email="taro@example.com" 
                isActive={true} 
            />
            <UserInfo 
                name="花子" 
                age={30} 
                email="hanako@example.com" 
                isActive={false} 
            />
        </div>
    );
}

export default App;
```

### 分割代入とデフォルト値の組み合わせ

```jsx
// src/components/Product.jsx
// 分割代入とデフォルト値を組み合わせる
function Product({ 
    name, 
    price, 
    discount = 0,  // デフォルト値
    description = "説明なし"  // デフォルト値
}) {
    const finalPrice = price - discount;
    
    return (
        <div style={{ padding: '16px', border: '1px solid #ddd' }}>
            <h3>{name}</h3>
            <p>説明: {description}</p>
            <p>定価: ¥{price.toLocaleString()}</p>
            {discount > 0 && (
                <p style={{ color: 'red' }}>
                    割引: -¥{discount.toLocaleString()}
                </p>
            )}
            <p><strong>最終価格: ¥{finalPrice.toLocaleString()}</strong></p>
        </div>
    );
}

// src/App.jsx
function App() {
    return (
        <div>
            <Product 
                name="ノートパソコン" 
                price={98000} 
                discount={5000}
                description="高性能なノートパソコン"
            />
            <Product 
                name="マウス" 
                price={2500}
                // discountとdescriptionはデフォルト値が使われる
            />
        </div>
    );
}

export default App;
```

### 残りのPropsをまとめて受け取る（スプレッド構文）

```jsx
// src/components/Card.jsx
// 一部のPropsを分割代入し、残りをまとめて受け取る
function Card({ title, children, ...otherProps }) {
    return (
        <div 
            style={{ 
                padding: '16px', 
                border: '1px solid #ccc',
                margin: '8px'
            }}
            {...otherProps}  // 残りのPropsを展開
        >
            <h2>{title}</h2>
            {children}
        </div>
    );
}

// src/App.jsx
function App() {
    return (
        <div>
            <Card 
                title="カード1" 
                className="custom-card"
                data-testid="card-1"
            >
                <p>これはカードの内容です</p>
            </Card>
        </div>
    );
}

export default App;
```

### ネストしたオブジェクトの分割代入

```jsx
// src/components/UserProfile.jsx
// ネストしたオブジェクトの分割代入
function UserProfile({ 
    user: { name, age, contact: { email, phone } } 
}) {
    return (
        <div style={{ padding: '16px', border: '1px solid #ccc' }}>
            <h2>{name}</h2>
            <p>年齢: {age}歳</p>
            <p>メール: {email}</p>
            <p>電話: {phone}</p>
        </div>
    );
}

// src/App.jsx
function App() {
    const user = {
        name: "太郎",
        age: 25,
        contact: {
            email: "taro@example.com",
            phone: "03-1234-5678"
        }
    };

    return (
        <div>
            <UserProfile user={user} />
        </div>
    );
}

export default App;
```

---

## children プロップスの活用

### childrenとは
`children`は、**コンポーネントの開始タグと終了タグの間に書かれた内容**を受け取る特別なPropsです。コンポーネントをラッパーとして使う際に便利です。

### 日常生活での例
- **箱**: 箱（コンポーネント）の中に、様々な物（children）を入れる
- **本の表紙**: 表紙（コンポーネント）の中に、本文（children）を入れる
- **フォルダ**: フォルダ（コンポーネント）の中に、ファイル（children）を入れる

### プログラミングでの例

```jsx
// 子コンポーネント
function Card({ children }) {
    return (
        <div style={{ border: '1px solid #ccc', padding: '16px' }}>
            {children}
        </div>
    );
}

// 親コンポーネント
function App() {
    return (
        <Card>
            <h2>タイトル</h2>
            <p>これはカードの内容です</p>
        </Card>
    );
}
```

### childrenの基本構文

```jsx
// childrenを受け取るコンポーネント
function Component({ children }) {
    return <div>{children}</div>;
}

// 使用例
<Component>
    ここに書いた内容がchildrenになる
</Component>
```

### childrenの特徴
1. **柔軟性**: 任意のコンテンツを子要素として渡せる
2. **再利用性**: 同じコンポーネントで異なる内容を表示できる
3. **コンポーネント合成**: 複数のコンポーネントを組み合わせられる

### 実践例: childrenの基本

```jsx
// src/components/Container.jsx
// childrenを使ったコンテナコンポーネント
function Container({ children, title }) {
    return (
        <div style={{ 
            border: '2px solid #333', 
            padding: '20px', 
            margin: '16px',
            borderRadius: '8px'
        }}>
            {title && <h2>{title}</h2>}
            {children}
        </div>
    );
}

// src/App.jsx
function App() {
    return (
        <div>
            <Container title="ユーザー情報">
                <p>名前: 太郎</p>
                <p>年齢: 25歳</p>
                <p>メール: taro@example.com</p>
            </Container>

            <Container title="商品情報">
                <ul>
                    <li>ノートパソコン: ¥98,000</li>
                    <li>マウス: ¥2,500</li>
                    <li>キーボード: ¥5,000</li>
                </ul>
            </Container>
        </div>
    );
}

export default App;
```

### 複数のchildrenを受け取る

```jsx
// src/components/Layout.jsx
// 複数のchildrenを受け取るレイアウトコンポーネント
function Layout({ header, sidebar, main, footer }) {
    return (
        <div style={{ display: 'flex', flexDirection: 'column', minHeight: '100vh' }}>
            <header style={{ backgroundColor: '#333', color: '#fff', padding: '16px' }}>
                {header}
            </header>
            <div style={{ display: 'flex', flex: 1 }}>
                <aside style={{ width: '200px', backgroundColor: '#f5f5f5', padding: '16px' }}>
                    {sidebar}
                </aside>
                <main style={{ flex: 1, padding: '16px' }}>
                    {main}
                </main>
            </div>
            <footer style={{ backgroundColor: '#333', color: '#fff', padding: '16px' }}>
                {footer}
            </footer>
        </div>
    );
}

// src/App.jsx
function App() {
    return (
        <Layout
            header={<h1>サイトタイトル</h1>}
            sidebar={
                <nav>
                    <ul>
                        <li>ホーム</li>
                        <li>About</li>
                        <li>Contact</li>
                    </ul>
                </nav>
            }
            main={
                <div>
                    <h2>メインコンテンツ</h2>
                    <p>ここにメインの内容が入ります</p>
                </div>
            }
            footer={<p>&copy; 2024 My Website</p>}
        />
    );
}

export default App;
```

### childrenと他のPropsの組み合わせ

```jsx
// src/components/Button.jsx
// childrenと他のPropsを組み合わせる
function Button({ children, onClick, variant = "primary", disabled = false }) {
    const styles = {
        primary: { backgroundColor: '#007bff', color: '#fff' },
        secondary: { backgroundColor: '#6c757d', color: '#fff' },
        danger: { backgroundColor: '#dc3545', color: '#fff' }
    };

    return (
        <button
            onClick={onClick}
            disabled={disabled}
            style={{
                padding: '8px 16px',
                border: 'none',
                borderRadius: '4px',
                cursor: disabled ? 'not-allowed' : 'pointer',
                opacity: disabled ? 0.6 : 1,
                ...styles[variant]
            }}
        >
            {children}
        </button>
    );
}

// src/App.jsx
function App() {
    return (
        <div>
            <Button onClick={() => alert('クリック！')}>
                プライマリーボタン
            </Button>
            <Button variant="secondary" onClick={() => console.log('クリック')}>
                セカンダリーボタン
            </Button>
            <Button variant="danger" disabled>
                無効なボタン
            </Button>
        </div>
    );
}

export default App;
```

### childrenでコンポーネントを渡す

```jsx
// src/components/Modal.jsx
// childrenとしてコンポーネントを渡す
function Modal({ isOpen, onClose, children, title }) {
    if (!isOpen) return null;

    return (
        <div style={{
            position: 'fixed',
            top: 0,
            left: 0,
            right: 0,
            bottom: 0,
            backgroundColor: 'rgba(0, 0, 0, 0.5)',
            display: 'flex',
            justifyContent: 'center',
            alignItems: 'center'
        }}>
            <div style={{
                backgroundColor: '#fff',
                padding: '24px',
                borderRadius: '8px',
                maxWidth: '500px',
                width: '90%'
            }}>
                <div style={{ display: 'flex', justifyContent: 'space-between', marginBottom: '16px' }}>
                    <h2>{title}</h2>
                    <button onClick={onClose}>×</button>
                </div>
                {children}
            </div>
        </div>
    );
}

// src/App.jsx
function App() {
    const [isOpen, setIsOpen] = useState(false);

    return (
        <div>
            <button onClick={() => setIsOpen(true)}>
                モーダルを開く
            </button>
            <Modal 
                isOpen={isOpen} 
                onClose={() => setIsOpen(false)}
                title="確認"
            >
                <p>この操作を実行しますか？</p>
                <div style={{ display: 'flex', gap: '8px', marginTop: '16px' }}>
                    <button onClick={() => setIsOpen(false)}>キャンセル</button>
                    <button onClick={() => {
                        alert('実行しました');
                        setIsOpen(false);
                    }}>実行</button>
                </div>
            </Modal>
        </div>
    );
}

export default App;
```

### childrenの型チェック（React.Children）

```jsx
// src/components/List.jsx
// React.Childrenを使ってchildrenを操作
import { Children, cloneElement } from 'react';

function List({ children }) {
    return (
        <ul style={{ listStyle: 'none', padding: 0 }}>
            {Children.map(children, (child, index) => {
                // 各子要素にkeyを追加
                return (
                    <li key={index} style={{ padding: '8px', borderBottom: '1px solid #ddd' }}>
                        {child}
                    </li>
                );
            })}
        </ul>
    );
}

// src/App.jsx
function App() {
    return (
        <List>
            <span>項目1</span>
            <span>項目2</span>
            <span>項目3</span>
        </List>
    );
}

export default App;
```

---

## まとめ

この章では、Propsについて以下のことを学びました。

### 親から子へのデータ受け渡し
- Propsは、親コンポーネントから子コンポーネントにデータを渡す仕組み
- 文字列、数値、真偽値、オブジェクト、関数など、様々な型のデータを渡せる
- Propsは読み取り専用で、一方向のデータフローを実現する
- デフォルト値を設定することで、Propsを省略可能にできる

### Propsの分割代入
- 分割代入を使うと、コードが簡潔で読みやすくなる
- デフォルト値と組み合わせることで、柔軟なコンポーネントを作成できる
- スプレッド構文を使って、残りのPropsをまとめて受け取れる
- ネストしたオブジェクトも分割代入できる

### childrenプロップス
- `children`は、コンポーネントの開始タグと終了タグの間の内容を受け取る特別なProps
- 任意のコンテンツやコンポーネントを子要素として渡せる
- ラッパーコンポーネントやレイアウトコンポーネントでよく使われる
- 他のPropsと組み合わせて、柔軟なコンポーネントを作成できる

### Propsの活用場面
- コンポーネントの再利用性を高める
- データを親から子に渡して、コンポーネントを動的にする
- コンポーネントをラッパーとして使う（children）
- イベントハンドラーを子コンポーネントに渡す

### 次のステップ
Propsを理解したら、次のトピックに進みましょう：
- State（状態）の管理（2-2）
- イベントハンドリング（2-3）
- 副作用とフック（3章）

---

## 演習問題

以下の問題を解いて、Propsの理解を深めましょう。

### 問題1: 基本的なProps
`UserCard`コンポーネントを作成し、`name`、`age`、`email`をPropsとして受け取り、表示してください。

<details>
<summary>解答例</summary>

```jsx
// UserCard.jsx
function UserCard({ name, age, email }) {
    return (
        <div style={{ border: '1px solid #ccc', padding: '16px', margin: '8px' }}>
            <h2>{name}</h2>
            <p>年齢: {age}歳</p>
            <p>メール: {email}</p>
        </div>
    );
}

// App.jsx
function App() {
    return (
        <div>
            <UserCard name="太郎" age={25} email="taro@example.com" />
            <UserCard name="花子" age={30} email="hanako@example.com" />
        </div>
    );
}

export default App;
```
</details>

### 問題2: Propsの分割代入とデフォルト値
`Product`コンポーネントを作成し、`name`、`price`、`discount`（デフォルト値0）をPropsとして受け取り、最終価格を表示してください。

<details>
<summary>解答例</summary>

```jsx
// Product.jsx
function Product({ name, price, discount = 0 }) {
    const finalPrice = price - discount;
    
    return (
        <div style={{ padding: '16px', border: '1px solid #ddd' }}>
            <h3>{name}</h3>
            <p>定価: ¥{price.toLocaleString()}</p>
            {discount > 0 && <p>割引: -¥{discount.toLocaleString()}</p>}
            <p><strong>最終価格: ¥{finalPrice.toLocaleString()}</strong></p>
        </div>
    );
}

// App.jsx
function App() {
    return (
        <div>
            <Product name="ノートパソコン" price={98000} discount={5000} />
            <Product name="マウス" price={2500} />
        </div>
    );
}

export default App;
```
</details>

### 問題3: childrenプロップス
`Card`コンポーネントを作成し、`title`と`children`をPropsとして受け取り、カード形式で表示してください。

<details>
<summary>解答例</summary>

```jsx
// Card.jsx
function Card({ title, children }) {
    return (
        <div style={{ border: '1px solid #ccc', padding: '16px', margin: '8px' }}>
            <h2>{title}</h2>
            {children}
        </div>
    );
}

// App.jsx
function App() {
    return (
        <div>
            <Card title="ユーザー情報">
                <p>名前: 太郎</p>
                <p>年齢: 25歳</p>
            </Card>
            <Card title="商品情報">
                <ul>
                    <li>ノートパソコン</li>
                    <li>マウス</li>
                </ul>
            </Card>
        </div>
    );
}

export default App;
```
</details>

### 問題4: Propsで関数を渡す
`Button`コンポーネントを作成し、`label`と`onClick`をPropsとして受け取り、クリック時にアラートを表示してください。

<details>
<summary>解答例</summary>

```jsx
// Button.jsx
function Button({ label, onClick }) {
    return (
        <button onClick={onClick} style={{ padding: '8px 16px', margin: '4px' }}>
            {label}
        </button>
    );
}

// App.jsx
function App() {
    const handleClick = () => {
        alert('ボタンがクリックされました！');
    };

    return (
        <div>
            <Button label="クリック" onClick={handleClick} />
        </div>
    );
}

export default App;
```
</details>

### 問題5: childrenと他のPropsの組み合わせ
`Container`コンポーネントを作成し、`title`、`children`、`backgroundColor`（デフォルト値'#fff'）をPropsとして受け取り、スタイル付きのコンテナを表示してください。

<details>
<summary>解答例</summary>

```jsx
// Container.jsx
function Container({ title, children, backgroundColor = '#fff' }) {
    return (
        <div style={{ 
            backgroundColor,
            border: '1px solid #ccc',
            padding: '16px',
            margin: '8px',
            borderRadius: '4px'
        }}>
            {title && <h2>{title}</h2>}
            {children}
        </div>
    );
}

// App.jsx
function App() {
    return (
        <div>
            <Container title="コンテナ1" backgroundColor="#e3f2fd">
                <p>これはコンテナの内容です</p>
            </Container>
            <Container title="コンテナ2">
                <p>背景色はデフォルト値が使われます</p>
            </Container>
        </div>
    );
}

export default App;
```
</details>

---

お疲れ様でした！次の章に進みましょう。

