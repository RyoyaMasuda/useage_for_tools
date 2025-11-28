# 2-1. Props (プロパティ)

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

### Propsの基本構文

```jsx
// 親コンポーネントでPropsを渡す
<コンポーネント名 プロパティ名1={値1} プロパティ名2={値2} />

// 子コンポーネントでPropsを受け取る
function コンポーネント名(props) {
  return <div>{props.プロパティ名1}</div>
}
```

### 実践例: 基本的なProps

```jsx
// src/components/Greeting.jsx
function Greeting(props) {
  return <h1>こんにちは、{props.name}さん！</h1>
}

export default Greeting
```

```jsx
// src/App.jsx
import Greeting from './components/Greeting'

function App() {
  return (
    <div>
      <Greeting name="太郎" />
      <Greeting name="花子" />
      <Greeting name="次郎" />
    </div>
  )
}

export default App
```

### 実践例: 複数のProps

```jsx
// src/components/UserCard.jsx
function UserCard(props) {
  return (
    <div>
      <h2>{props.name}</h2>
      <p>年齢: {props.age}歳</p>
      <p>メール: {props.email}</p>
    </div>
  )
}

export default UserCard
```

```jsx
// src/App.jsx
import UserCard from './components/UserCard'

function App() {
  return (
    <div>
      <UserCard 
        name="太郎" 
        age={25} 
        email="taro@example.com" 
      />
      <UserCard 
        name="花子" 
        age={30} 
        email="hanako@example.com" 
      />
    </div>
  )
}

export default App
```

### 実践例: オブジェクトをPropsとして渡す

```jsx
// src/components/ProductCard.jsx
function ProductCard(props) {
  return (
    <div>
      <h3>{props.product.name}</h3>
      <p>価格: {props.product.price}円</p>
      <p>在庫: {props.product.stock}個</p>
    </div>
  )
}

export default ProductCard
```

```jsx
// src/App.jsx
import ProductCard from './components/ProductCard'

function App() {
  const product1 = {
    name: 'りんご',
    price: 100,
    stock: 50
  }
  
  const product2 = {
    name: 'バナナ',
    price: 80,
    stock: 30
  }
  
  return (
    <div>
      <ProductCard product={product1} />
      <ProductCard product={product2} />
    </div>
  )
}

export default App
```

### 実践例: 配列をPropsとして渡す

```jsx
// src/components/ItemList.jsx
function ItemList(props) {
  return (
    <ul>
      {props.items.map((item, index) => (
        <li key={index}>{item}</li>
      ))}
    </ul>
  )
}

export default ItemList
```

```jsx
// src/App.jsx
import ItemList from './components/ItemList'

function App() {
  const fruits = ['りんご', 'バナナ', 'オレンジ']
  const vegetables = ['にんじん', 'じゃがいも', 'たまねぎ']
  
  return (
    <div>
      <h2>果物</h2>
      <ItemList items={fruits} />
      <h2>野菜</h2>
      <ItemList items={vegetables} />
    </div>
  )
}

export default App
```

---

## Propsの分割代入

### 分割代入とは
分割代入は、**オブジェクトや配列から値を取り出して変数に代入する**構文です。Propsを受け取る際に使用すると、コードが簡潔になります。

### 分割代入の基本構文

```jsx
// 通常の書き方
function Component(props) {
  return <div>{props.name}</div>
}

// 分割代入を使った書き方
function Component({ name }) {
  return <div>{name}</div>
}
```

### 実践例: 基本的な分割代入

```jsx
// src/components/Greeting.jsx
function Greeting({ name }) {
  return <h1>こんにちは、{name}さん！</h1>
}

export default Greeting
```

```jsx
// src/App.jsx
import Greeting from './components/Greeting'

function App() {
  return (
    <div>
      <Greeting name="太郎" />
      <Greeting name="花子" />
    </div>
  )
}

export default App
```

### 実践例: 複数のPropsの分割代入

```jsx
// src/components/UserCard.jsx
function UserCard({ name, age, email }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>年齢: {age}歳</p>
      <p>メール: {email}</p>
    </div>
  )
}

export default UserCard
```

```jsx
// src/App.jsx
import UserCard from './components/UserCard'

function App() {
  return (
    <div>
      <UserCard 
        name="太郎" 
        age={25} 
        email="taro@example.com" 
      />
      <UserCard 
        name="花子" 
        age={30} 
        email="hanako@example.com" 
      />
    </div>
  )
}

export default App
```

### 実践例: デフォルト値の設定

```jsx
// src/components/Button.jsx
function Button({ label, onClick, disabled = false }) {
  return (
    <button onClick={onClick} disabled={disabled}>
      {label}
    </button>
  )
}

export default Button
```

```jsx
// src/App.jsx
import Button from './components/Button'

function App() {
  const handleClick = () => {
    console.log('ボタンがクリックされました')
  }
  
  return (
    <div>
      <Button label="クリック" onClick={handleClick} />
      <Button label="無効" onClick={handleClick} disabled={true} />
      {/* disabledが省略された場合はfalseが使用される */}
      <Button label="デフォルト" onClick={handleClick} />
    </div>
  )
}

export default App
```

### 実践例: 残りのPropsを取得（スプレッド構文）

```jsx
// src/components/Card.jsx
function Card({ title, children, ...otherProps }) {
  return (
    <div {...otherProps}>
      <h2>{title}</h2>
      {children}
    </div>
  )
}

export default Card
```

```jsx
// src/App.jsx
import Card from './components/Card'

function App() {
  return (
    <div>
      <Card 
        title="カードタイトル" 
        className="custom-card"
        style={{ padding: '20px' }}
      >
        <p>カードの内容</p>
      </Card>
    </div>
  )
}

export default App
```

---

## children プロップスの活用

### childrenとは
`children`は、**コンポーネントの開始タグと終了タグの間に書かれた内容**を受け取る特別なPropsです。

### childrenの基本構文

```jsx
// 親コンポーネント
<コンポーネント名>
  ここに書いた内容がchildrenになる
</コンポーネント名>

// 子コンポーネント
function コンポーネント名({ children }) {
  return <div>{children}</div>
}
```

### 実践例: 基本的なchildren

```jsx
// src/components/Container.jsx
function Container({ children }) {
  return (
    <div style={{ padding: '20px', border: '1px solid #ccc' }}>
      {children}
    </div>
  )
}

export default Container
```

```jsx
// src/App.jsx
import Container from './components/Container'

function App() {
  return (
    <Container>
      <h1>タイトル</h1>
      <p>これはコンテナの中の内容です</p>
    </Container>
  )
}

export default App
```

### 実践例: 複数のchildren

```jsx
// src/components/Layout.jsx
function Layout({ header, main, footer }) {
  return (
    <div>
      <header>{header}</header>
      <main>{main}</main>
      <footer>{footer}</footer>
    </div>
  )
}

export default Layout
```

```jsx
// src/App.jsx
import Layout from './components/Layout'

function App() {
  return (
    <Layout
      header={<h1>ヘッダー</h1>}
      main={<p>メインコンテンツ</p>}
      footer={<p>フッター</p>}
    />
  )
}

export default App
```

### 実践例: カードコンポーネント

```jsx
// src/components/Card.jsx
function Card({ title, children }) {
  return (
    <div style={{
      border: '1px solid #ddd',
      borderRadius: '8px',
      padding: '20px',
      margin: '10px'
    }}>
      {title && <h2>{title}</h2>}
      <div>{children}</div>
    </div>
  )
}

export default Card
```

```jsx
// src/App.jsx
import Card from './components/Card'

function App() {
  return (
    <div>
      <Card title="カード1">
        <p>これはカード1の内容です</p>
        <button>アクション</button>
      </Card>
      
      <Card title="カード2">
        <ul>
          <li>項目1</li>
          <li>項目2</li>
          <li>項目3</li>
        </ul>
      </Card>
    </div>
  )
}

export default App
```

### 実践例: モーダルコンポーネント

```jsx
// src/components/Modal.jsx
function Modal({ isOpen, onClose, children }) {
  if (!isOpen) return null
  
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
        backgroundColor: 'white',
        padding: '20px',
        borderRadius: '8px',
        maxWidth: '500px'
      }}>
        {children}
        <button onClick={onClose}>閉じる</button>
      </div>
    </div>
  )
}

export default Modal
```

```jsx
// src/App.jsx
import { useState } from 'react'
import Modal from './components/Modal'

function App() {
  const [isOpen, setIsOpen] = useState(false)
  
  return (
    <div>
      <button onClick={() => setIsOpen(true)}>
        モーダルを開く
      </button>
      
      <Modal isOpen={isOpen} onClose={() => setIsOpen(false)}>
        <h2>モーダルタイトル</h2>
        <p>これはモーダルの内容です</p>
        <p>childrenプロップスを使用して、</p>
        <p>自由な内容を表示できます</p>
      </Modal>
    </div>
  )
}

export default App
```

### 実践例: レイアウトコンポーネント

```jsx
// src/components/PageLayout.jsx
function PageLayout({ children }) {
  return (
    <div>
      <header style={{
        backgroundColor: '#333',
        color: 'white',
        padding: '20px'
      }}>
        <h1>ヘッダー</h1>
      </header>
      
      <main style={{ padding: '20px' }}>
        {children}
      </main>
      
      <footer style={{
        backgroundColor: '#333',
        color: 'white',
        padding: '20px',
        textAlign: 'center'
      }}>
        <p>フッター</p>
      </footer>
    </div>
  )
}

export default PageLayout
```

```jsx
// src/App.jsx
import PageLayout from './components/PageLayout'

function App() {
  return (
    <PageLayout>
      <h2>ホームページ</h2>
      <p>これはメインコンテンツです</p>
      <p>childrenプロップスを使用して、</p>
      <p>ページの内容を自由に記述できます</p>
    </PageLayout>
  )
}

export default App
```

---

## まとめ

この章では、ReactのProps（プロパティ）について学びました。

### 学んだこと
- 親から子へのデータ受け渡し: Propsを使用してデータを渡す
- Propsの分割代入: コードを簡潔にする
- childrenプロップス: コンポーネントの内容を柔軟に指定

### 重要なポイント
1. **Props**: 親から子へデータを渡す仕組み
2. **分割代入**: `{ name, age }`のように簡潔に書ける
3. **children**: コンポーネントの内容を柔軟に指定できる
4. **再利用性**: Propsを使用することでコンポーネントを再利用可能に

### 次のステップ
次の章では、State（状態）について詳しく学びます。

---

## 演習問題

### 問題1: 基本的なProps
`Greeting`コンポーネントを作成し、`name`というPropsを受け取って「こんにちは、{name}さん！」と表示してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/Greeting.jsx
function Greeting({ name }) {
  return <h1>こんにちは、{name}さん！</h1>
}

export default Greeting
```

```jsx
// src/App.jsx
import Greeting from './components/Greeting'

function App() {
  return (
    <div>
      <Greeting name="太郎" />
      <Greeting name="花子" />
    </div>
  )
}

export default App
```
</details>

### 問題2: 複数のProps
`UserCard`コンポーネントを作成し、`name`、`age`、`email`というPropsを受け取って表示してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/UserCard.jsx
function UserCard({ name, age, email }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>年齢: {age}歳</p>
      <p>メール: {email}</p>
    </div>
  )
}

export default UserCard
```

```jsx
// src/App.jsx
import UserCard from './components/UserCard'

function App() {
  return (
    <div>
      <UserCard 
        name="太郎" 
        age={25} 
        email="taro@example.com" 
      />
    </div>
  )
}

export default App
```
</details>

### 問題3: デフォルト値
`Button`コンポーネントを作成し、`label`と`disabled`というPropsを受け取り、`disabled`のデフォルト値を`false`に設定してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/Button.jsx
function Button({ label, disabled = false }) {
  return (
    <button disabled={disabled}>
      {label}
    </button>
  )
}

export default Button
```

```jsx
// src/App.jsx
import Button from './components/Button'

function App() {
  return (
    <div>
      <Button label="クリック" />
      <Button label="無効" disabled={true} />
    </div>
  )
}

export default App
```
</details>

### 問題4: childrenプロップス
`Card`コンポーネントを作成し、`title`と`children`というPropsを受け取って、カード形式で表示してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/Card.jsx
function Card({ title, children }) {
  return (
    <div style={{
      border: '1px solid #ddd',
      borderRadius: '8px',
      padding: '20px'
    }}>
      {title && <h2>{title}</h2>}
      <div>{children}</div>
    </div>
  )
}

export default Card
```

```jsx
// src/App.jsx
import Card from './components/Card'

function App() {
  return (
    <Card title="カードタイトル">
      <p>これはカードの内容です</p>
    </Card>
  )
}

export default App
```
</details>

### 問題5: 配列をPropsとして渡す
`ItemList`コンポーネントを作成し、`items`という配列のPropsを受け取って、リスト形式で表示してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/ItemList.jsx
function ItemList({ items }) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{item}</li>
      ))}
    </ul>
  )
}

export default ItemList
```

```jsx
// src/App.jsx
import ItemList from './components/ItemList'

function App() {
  const fruits = ['りんご', 'バナナ', 'オレンジ']
  
  return (
    <div>
      <ItemList items={fruits} />
    </div>
  )
}

export default App
```
</details>

---

お疲れ様でした！次の章に進みましょう。

