# 4-1. フォーム管理の基本 (register, handleSubmit)

この章では、React Hook Formを使用したフォーム管理の基本について学びます。React Hook Formは、パフォーマンスが高く、使いやすいフォーム管理ライブラリです。

---

## 目次

- [React Hook Formとは](#react-hook-formとは)
- [インストールとセットアップ](#インストールとセットアップ)
- [register - 入力フィールドの登録](#register---入力フィールドの登録)
- [handleSubmit - フォーム送信の処理](#handlesubmit---フォーム送信の処理)
- [実践例: 基本的なフォーム](#実践例-基本的なフォーム)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## React Hook Formとは

### React Hook Formの特徴
React Hook Formは、**高性能で使いやすいReactフォーム管理ライブラリ**です。

### 主な特徴
- **パフォーマンス**: 最小限の再レンダリング
- **使いやすさ**: シンプルなAPI
- **バリデーション**: 簡単にバリデーションを実装できる
- **型安全性**: TypeScriptを完全サポート

### 従来のフォーム管理との比較

#### 従来の方法（useState）
```jsx
function Form() {
  const [name, setName] = useState('')
  const [email, setEmail] = useState('')
  const [errors, setErrors] = useState({})
  
  // 各フィールドごとに状態管理が必要
  // バリデーションロジックも自分で実装
}
```

#### React Hook Form
```jsx
function Form() {
  const { register, handleSubmit } = useForm()
  
  // シンプルで、パフォーマンスも良い
}
```

---

## インストールとセットアップ

### インストール

```bash
npm install react-hook-form
```

### 基本的なセットアップ

```jsx
import { useForm } from 'react-hook-form'

function MyForm() {
  const { register, handleSubmit } = useForm()
  
  // フォームの処理
}
```

### 実践例: インストールとセットアップ

```bash
# プロジェクトにReact Hook Formをインストール
npm install react-hook-form
```

```jsx
// src/App.jsx
import { useForm } from 'react-hook-form'
import './App.css'

function App() {
  const { register, handleSubmit } = useForm()
  
  return (
    <div className="App">
      <h1>React Hook Formの基本</h1>
      {/* フォームの内容 */}
    </div>
  )
}

export default App
```

---

## register - 入力フィールドの登録

### registerとは
`register`は、**入力フィールドをReact Hook Formに登録する関数**です。これにより、フォームの状態管理が自動化されます。

### registerの基本構文

```jsx
<input {...register('フィールド名')} />
```

### registerの特徴
- **自動的な状態管理**: 入力値の管理が自動化される
- **バリデーション**: バリデーションルールを簡単に追加できる
- **パフォーマンス**: 最小限の再レンダリング

### 基本的な使い方

```jsx
function Form() {
  const { register } = useForm()
  
  return (
    <form>
      <input {...register('name')} />
      <input {...register('email')} />
    </form>
  )
}
```

### 実践例: registerの基本

```jsx
// src/components/BasicForm.jsx
import { useForm } from 'react-hook-form'

function BasicForm() {
  const { register } = useForm()
  
  return (
    <form>
      <div>
        <label>名前</label>
        <input {...register('name')} />
      </div>
      
      <div>
        <label>メールアドレス</label>
        <input type="email" {...register('email')} />
      </div>
      
      <div>
        <label>メッセージ</label>
        <textarea {...register('message')} />
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default BasicForm
```

### 実践例: 様々な入力タイプ

```jsx
// src/components/VariousInputs.jsx
import { useForm } from 'react-hook-form'

function VariousInputs() {
  const { register } = useForm()
  
  return (
    <form>
      {/* テキスト入力 */}
      <div>
        <label>テキスト</label>
        <input type="text" {...register('text')} />
      </div>
      
      {/* 数値入力 */}
      <div>
        <label>数値</label>
        <input type="number" {...register('number')} />
      </div>
      
      {/* メールアドレス */}
      <div>
        <label>メール</label>
        <input type="email" {...register('email')} />
      </div>
      
      {/* パスワード */}
      <div>
        <label>パスワード</label>
        <input type="password" {...register('password')} />
      </div>
      
      {/* チェックボックス */}
      <div>
        <label>
          <input type="checkbox" {...register('checkbox')} />
          チェックボックス
        </label>
      </div>
      
      {/* ラジオボタン */}
      <div>
        <label>
          <input type="radio" value="option1" {...register('radio')} />
          オプション1
        </label>
        <label>
          <input type="radio" value="option2" {...register('radio')} />
          オプション2
        </label>
      </div>
      
      {/* セレクトボックス */}
      <div>
        <label>選択</label>
        <select {...register('select')}>
          <option value="">選択してください</option>
          <option value="option1">オプション1</option>
          <option value="option2">オプション2</option>
        </select>
      </div>
      
      {/* 日付 */}
      <div>
        <label>日付</label>
        <input type="date" {...register('date')} />
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default VariousInputs
```

---

## handleSubmit - フォーム送信の処理

### handleSubmitとは
`handleSubmit`は、**フォーム送信を処理する関数**です。バリデーションが成功した場合のみ、指定した関数を実行します。

### handleSubmitの基本構文

```jsx
const onSubmit = (data) => {
  // フォーム送信時の処理
  console.log(data)
}

<form onSubmit={handleSubmit(onSubmit)}>
  {/* フォームの内容 */}
</form>
```

### handleSubmitの特徴
- **自動バリデーション**: 送信前にバリデーションを実行
- **データの取得**: フォームデータを自動的に取得
- **エラーハンドリング**: バリデーションエラーがある場合は送信を阻止

### 基本的な使い方

```jsx
function Form() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log(data) // フォームデータが取得できる
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('name')} />
      <button type="submit">送信</button>
    </form>
  )
}
```

### 実践例: handleSubmitの基本

```jsx
// src/components/SubmitForm.jsx
import { useForm } from 'react-hook-form'

function SubmitForm() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
    alert('フォームが送信されました！')
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>名前</label>
        <input {...register('name')} />
      </div>
      
      <div>
        <label>メールアドレス</label>
        <input type="email" {...register('email')} />
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default SubmitForm
```

### 実践例: エラーハンドリング

```jsx
// src/components/FormWithErrors.jsx
import { useForm } from 'react-hook-form'

function FormWithErrors() {
  const { register, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  const onError = (errors) => {
    console.log('バリデーションエラー:', errors)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit, onError)}>
      <div>
        <label>名前</label>
        <input {...register('name', { required: '名前は必須です' })} />
        {errors.name && <span style={{ color: 'red' }}>{errors.name.message}</span>}
      </div>
      
      <div>
        <label>メールアドレス</label>
        <input 
          type="email" 
          {...register('email', { 
            required: 'メールアドレスは必須です',
            pattern: {
              value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
              message: '有効なメールアドレスを入力してください'
            }
          })} 
        />
        {errors.email && <span style={{ color: 'red' }}>{errors.email.message}</span>}
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default FormWithErrors
```

---

## 実践例: 基本的なフォーム

### 実践例1: お問い合わせフォーム

```jsx
// src/components/ContactForm.jsx
import { useForm } from 'react-hook-form'

function ContactForm() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('お問い合わせ内容:', data)
    // ここでAPIに送信する処理を実装
    alert('お問い合わせを受け付けました！')
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <h2>お問い合わせフォーム</h2>
      
      <div>
        <label>お名前</label>
        <input 
          type="text" 
          {...register('name')} 
          placeholder="山田太郎"
        />
      </div>
      
      <div>
        <label>メールアドレス</label>
        <input 
          type="email" 
          {...register('email')} 
          placeholder="example@mail.com"
        />
      </div>
      
      <div>
        <label>件名</label>
        <input 
          type="text" 
          {...register('subject')} 
          placeholder="お問い合わせの件名"
        />
      </div>
      
      <div>
        <label>メッセージ</label>
        <textarea 
          {...register('message')} 
          rows="5"
          placeholder="お問い合わせ内容をご記入ください"
        />
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default ContactForm
```

### 実践例2: ログインフォーム

```jsx
// src/components/LoginForm.jsx
import { useForm } from 'react-hook-form'

function LoginForm() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('ログイン情報:', data)
    // ここで認証処理を実装
    alert('ログインしました！')
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <h2>ログイン</h2>
      
      <div>
        <label>ユーザー名</label>
        <input 
          type="text" 
          {...register('username')} 
          placeholder="ユーザー名を入力"
        />
      </div>
      
      <div>
        <label>パスワード</label>
        <input 
          type="password" 
          {...register('password')} 
          placeholder="パスワードを入力"
        />
      </div>
      
      <div>
        <label>
          <input type="checkbox" {...register('remember')} />
          ログイン状態を保持
        </label>
      </div>
      
      <button type="submit">ログイン</button>
    </form>
  )
}

export default LoginForm
```

### 実践例3: ユーザー登録フォーム

```jsx
// src/components/RegistrationForm.jsx
import { useForm } from 'react-hook-form'

function RegistrationForm() {
  const { register, handleSubmit, watch } = useForm()
  
  // パスワードの確認用（watchで監視）
  const password = watch('password')
  
  const onSubmit = (data) => {
    console.log('登録情報:', data)
    // ここでユーザー登録処理を実装
    alert('ユーザー登録が完了しました！')
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <h2>ユーザー登録</h2>
      
      <div>
        <label>ユーザー名</label>
        <input 
          type="text" 
          {...register('username')} 
          placeholder="ユーザー名を入力"
        />
      </div>
      
      <div>
        <label>メールアドレス</label>
        <input 
          type="email" 
          {...register('email')} 
          placeholder="example@mail.com"
        />
      </div>
      
      <div>
        <label>パスワード</label>
        <input 
          type="password" 
          {...register('password')} 
          placeholder="パスワードを入力"
        />
      </div>
      
      <div>
        <label>パスワード（確認）</label>
        <input 
          type="password" 
          {...register('confirmPassword', {
            validate: value => value === password || 'パスワードが一致しません'
          })} 
          placeholder="パスワードを再入力"
        />
      </div>
      
      <div>
        <label>
          <input type="checkbox" {...register('agreeToTerms')} />
          利用規約に同意する
        </label>
      </div>
      
      <button type="submit">登録</button>
    </form>
  )
}

export default RegistrationForm
```

### 実践例4: 検索フォーム

```jsx
// src/components/SearchForm.jsx
import { useForm } from 'react-hook-form'

function SearchForm({ onSearch }) {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('検索クエリ:', data.query)
    // 親コンポーネントに検索クエリを渡す
    if (onSearch) {
      onSearch(data.query)
    }
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <input 
          type="text" 
          {...register('query')} 
          placeholder="検索..."
        />
        <button type="submit">検索</button>
      </div>
    </form>
  )
}

export default SearchForm
```

### 実践例5: 設定フォーム

```jsx
// src/components/SettingsForm.jsx
import { useForm } from 'react-hook-form'

function SettingsForm() {
  const { register, handleSubmit } = useForm({
    defaultValues: {
      theme: 'light',
      language: 'ja',
      notifications: true
    }
  })
  
  const onSubmit = (data) => {
    console.log('設定:', data)
    // ここで設定を保存する処理を実装
    alert('設定を保存しました！')
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <h2>設定</h2>
      
      <div>
        <label>テーマ</label>
        <select {...register('theme')}>
          <option value="light">ライト</option>
          <option value="dark">ダーク</option>
        </select>
      </div>
      
      <div>
        <label>言語</label>
        <select {...register('language')}>
          <option value="ja">日本語</option>
          <option value="en">English</option>
          <option value="zh">中文</option>
        </select>
      </div>
      
      <div>
        <label>
          <input type="checkbox" {...register('notifications')} />
          通知を受け取る
        </label>
      </div>
      
      <button type="submit">保存</button>
    </form>
  )
}

export default SettingsForm
```

---

## まとめ

この章では、React Hook Formを使用したフォーム管理の基本について学びました。

### 学んだこと
- React Hook Form: 高性能なフォーム管理ライブラリ
- register: 入力フィールドを登録する関数
- handleSubmit: フォーム送信を処理する関数

### 重要なポイント
1. **register**: `{...register('フィールド名')}`で入力フィールドを登録
2. **handleSubmit**: `handleSubmit(onSubmit)`でフォーム送信を処理
3. **データの取得**: `onSubmit`関数の引数でフォームデータを取得
4. **パフォーマンス**: 最小限の再レンダリングで高速

### 次のステップ
次の章では、バリデーションルールの実装について詳しく学びます。

---

## 演習問題

### 問題1: 基本的なフォーム
名前とメールアドレスを入力するフォームを作成し、送信時にデータをコンソールに表示してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/ExerciseForm.jsx
import { useForm } from 'react-hook-form'

function ExerciseForm() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('フォームデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>名前</label>
        <input {...register('name')} />
      </div>
      
      <div>
        <label>メールアドレス</label>
        <input type="email" {...register('email')} />
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default ExerciseForm
```
</details>

### 問題2: 複数の入力フィールド
名前、年齢、メールアドレス、メッセージを入力するフォームを作成してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/MultiFieldForm.jsx
import { useForm } from 'react-hook-form'

function MultiFieldForm() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('フォームデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>名前</label>
        <input {...register('name')} />
      </div>
      
      <div>
        <label>年齢</label>
        <input type="number" {...register('age')} />
      </div>
      
      <div>
        <label>メールアドレス</label>
        <input type="email" {...register('email')} />
      </div>
      
      <div>
        <label>メッセージ</label>
        <textarea {...register('message')} />
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default MultiFieldForm
```
</details>

### 問題3: チェックボックスとラジオボタン
チェックボックスとラジオボタンを含むフォームを作成してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/CheckboxRadioForm.jsx
import { useForm } from 'react-hook-form'

function CheckboxRadioForm() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('フォームデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>性別</label>
        <label>
          <input type="radio" value="male" {...register('gender')} />
          男性
        </label>
        <label>
          <input type="radio" value="female" {...register('gender')} />
          女性
        </label>
      </div>
      
      <div>
        <label>
          <input type="checkbox" {...register('newsletter')} />
          ニュースレターを受け取る
        </label>
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default CheckboxRadioForm
```
</details>

### 問題4: セレクトボックス
セレクトボックスを含むフォームを作成してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/SelectForm.jsx
import { useForm } from 'react-hook-form'

function SelectForm() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('フォームデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>都道府県</label>
        <select {...register('prefecture')}>
          <option value="">選択してください</option>
          <option value="tokyo">東京都</option>
          <option value="osaka">大阪府</option>
          <option value="kyoto">京都府</option>
        </select>
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default SelectForm
```
</details>

### 問題5: デフォルト値の設定
デフォルト値を持つフォームを作成してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/DefaultValuesForm.jsx
import { useForm } from 'react-hook-form'

function DefaultValuesForm() {
  const { register, handleSubmit } = useForm({
    defaultValues: {
      name: '山田太郎',
      email: 'yamada@example.com',
      age: 25
    }
  })
  
  const onSubmit = (data) => {
    console.log('フォームデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>名前</label>
        <input {...register('name')} />
      </div>
      
      <div>
        <label>メールアドレス</label>
        <input type="email" {...register('email')} />
      </div>
      
      <div>
        <label>年齢</label>
        <input type="number" {...register('age')} />
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default DefaultValuesForm
```
</details>

---

お疲れ様でした！次の章に進みましょう。

