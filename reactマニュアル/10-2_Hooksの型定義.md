# 10-2. Hooksの型定義

この章では、TypeScriptでReact Hooksの型を定義する方法について学びます。Hooksに型を付けることで、型安全なコードを書けるようになります。

---

## 目次

- [useState<T>, useRef<T>](#usestatet-usereft)
- [カスタムフックの型](#カスタムフックの型)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## useState<T>, useRef<T>

### useStateの型定義

`useState`は、**ジェネリクスを使用して型を指定**できます。

### 基本的な型定義

```typescript
const [state, setState] = useState<型>(初期値)
```

### 実践例: useStateの型定義

```typescript
// src/components/Counter.tsx
import { useState } from 'react'

function Counter() {
  // 数値型を指定
  const [count, setCount] = useState<number>(0)
  
  // 文字列型を指定
  const [name, setName] = useState<string>('')
  
  // 真偽値型を指定
  const [isActive, setIsActive] = useState<boolean>(false)
  
  return (
    <div>
      <p>カウント: {count}</p>
      <button onClick={() => setCount(count + 1)}>増やす</button>
      
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      
      <button onClick={() => setIsActive(!isActive)}>
        {isActive ? 'アクティブ' : '非アクティブ'}
      </button>
    </div>
  )
}

export default Counter
```

### 実践例: オブジェクトの型定義

```typescript
// src/components/UserForm.tsx
import { useState } from 'react'

interface User {
  name: string
  email: string
  age: number
}

function UserForm() {
  // オブジェクト型を指定
  const [user, setUser] = useState<User>({
    name: '',
    email: '',
    age: 0
  })
  
  const handleChange = (field: keyof User, value: string | number) => {
    setUser({
      ...user,
      [field]: value
    })
  }
  
  return (
    <div>
      <input
        type="text"
        value={user.name}
        onChange={(e) => handleChange('name', e.target.value)}
        placeholder="名前"
      />
      <input
        type="email"
        value={user.email}
        onChange={(e) => handleChange('email', e.target.value)}
        placeholder="メールアドレス"
      />
      <input
        type="number"
        value={user.age}
        onChange={(e) => handleChange('age', Number(e.target.value))}
        placeholder="年齢"
      />
    </div>
  )
}

export default UserForm
```

### 実践例: 配列の型定義

```typescript
// src/components/TodoList.tsx
import { useState } from 'react'

interface Todo {
  id: number
  text: string
  completed: boolean
}

function TodoList() {
  // 配列型を指定
  const [todos, setTodos] = useState<Todo[]>([])
  
  const addTodo = (text: string) => {
    const newTodo: Todo = {
      id: Date.now(),
      text,
      completed: false
    }
    setTodos([...todos, newTodo])
  }
  
  const toggleTodo = (id: number) => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ))
  }
  
  return (
    <div>
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            <input
              type="checkbox"
              checked={todo.completed}
              onChange={() => toggleTodo(todo.id)}
            />
            {todo.text}
          </li>
        ))}
      </ul>
    </div>
  )
}

export default TodoList
```

### 実践例: 型推論を活用

```typescript
// src/components/AutoInfer.tsx
import { useState } from 'react'

function AutoInfer() {
  // 型推論により、number型と推論される
  const [count, setCount] = useState(0)
  
  // 型推論により、string型と推論される
  const [message, setMessage] = useState('')
  
  // 型推論により、boolean型と推論される
  const [isVisible, setIsVisible] = useState(false)
  
  // オブジェクトの場合も型推論される
  const [user, setUser] = useState({
    name: '',
    email: ''
  })
  
  return (
    <div>
      <p>カウント: {count}</p>
      <p>メッセージ: {message}</p>
      <p>表示: {isVisible ? '表示中' : '非表示'}</p>
      <p>ユーザー: {user.name} ({user.email})</p>
    </div>
  )
}

export default AutoInfer
```

### useRefの型定義

`useRef`は、**DOM要素への参照や値を保持する**フックです。型を指定することで、安全に使用できます。

### 基本的な型定義

```typescript
// DOM要素への参照
const ref = useRef<HTMLInputElement>(null)

// 値の保持
const ref = useRef<型>(初期値)
```

### 実践例: useRefの型定義（DOM要素）

```typescript
// src/components/InputFocus.tsx
import { useRef } from 'react'

function InputFocus() {
  // DOM要素への参照
  const inputRef = useRef<HTMLInputElement>(null)
  
  const handleFocus = () => {
    // nullチェックが必要
    if (inputRef.current) {
      inputRef.current.focus()
    }
  }
  
  return (
    <div>
      <input
        ref={inputRef}
        type="text"
        placeholder="入力してください"
      />
      <button onClick={handleFocus}>フォーカス</button>
    </div>
  )
}

export default InputFocus
```

### 実践例: useRefの型定義（値の保持）

```typescript
// src/components/Counter.tsx
import { useState, useRef, useEffect } from 'react'

function Counter() {
  const [count, setCount] = useState<number>(0)
  // 前回の値を保持
  const prevCountRef = useRef<number>(0)
  
  useEffect(() => {
    prevCountRef.current = count
  }, [count])
  
  return (
    <div>
      <p>現在のカウント: {count}</p>
      <p>前回のカウント: {prevCountRef.current}</p>
      <button onClick={() => setCount(count + 1)}>増やす</button>
    </div>
  )
}

export default Counter
```

### 実践例: 複数のuseRef

```typescript
// src/components/MultiRef.tsx
import { useRef } from 'react'

function MultiRef() {
  // 複数のDOM要素への参照
  const inputRef = useRef<HTMLInputElement>(null)
  const textareaRef = useRef<HTMLTextAreaElement>(null)
  const buttonRef = useRef<HTMLButtonElement>(null)
  
  const handleFocusInput = () => {
    inputRef.current?.focus()
  }
  
  const handleFocusTextarea = () => {
    textareaRef.current?.focus()
  }
  
  return (
    <div>
      <input ref={inputRef} type="text" placeholder="入力" />
      <button onClick={handleFocusInput}>入力にフォーカス</button>
      
      <textarea ref={textareaRef} placeholder="テキストエリア" />
      <button onClick={handleFocusTextarea}>テキストエリアにフォーカス</button>
      
      <button ref={buttonRef}>ボタン</button>
    </div>
  )
}

export default MultiRef
```

### 実践例: useRefとuseStateの組み合わせ

```typescript
// src/components/Timer.tsx
import { useState, useRef, useEffect } from 'react'

function Timer() {
  const [seconds, setSeconds] = useState<number>(0)
  const [isRunning, setIsRunning] = useState<boolean>(false)
  // タイマーIDを保持
  const intervalRef = useRef<NodeJS.Timeout | null>(null)
  
  useEffect(() => {
    if (isRunning) {
      intervalRef.current = setInterval(() => {
        setSeconds(prev => prev + 1)
      }, 1000)
    } else {
      if (intervalRef.current) {
        clearInterval(intervalRef.current)
      }
    }
    
    return () => {
      if (intervalRef.current) {
        clearInterval(intervalRef.current)
      }
    }
  }, [isRunning])
  
  const handleStart = () => {
    setIsRunning(true)
  }
  
  const handleStop = () => {
    setIsRunning(false)
  }
  
  const handleReset = () => {
    setSeconds(0)
    setIsRunning(false)
  }
  
  return (
    <div>
      <p>経過時間: {seconds}秒</p>
      <button onClick={handleStart}>開始</button>
      <button onClick={handleStop}>停止</button>
      <button onClick={handleReset}>リセット</button>
    </div>
  )
}

export default Timer
```

---

## カスタムフックの型定義

### カスタムフックとは
カスタムフックは、**ロジックを再利用するための関数**です。`use`で始まる名前を持ち、他のフックを使用できます。

### カスタムフックの型定義の重要性
- **型安全性**: 引数と戻り値の型を明示できる
- **エラー検出**: コンパイル時にエラーを発見できる
- **IDE支援**: コード補完や型チェックが向上
- **可読性**: 関数の仕様が明確になる

### 基本的な型定義

```typescript
function useCustomHook<T>(arg: T): ReturnType {
  // フックの実装
  return value
}
```

### 実践例: useCounterフック

```typescript
// src/hooks/useCounter.ts
import { useState } from 'react'

interface UseCounterReturn {
  count: number
  increment: () => void
  decrement: () => void
  reset: () => void
}

function useCounter(initialValue: number = 0): UseCounterReturn {
  const [count, setCount] = useState<number>(initialValue)
  
  const increment = () => {
    setCount(count + 1)
  }
  
  const decrement = () => {
    setCount(count - 1)
  }
  
  const reset = () => {
    setCount(initialValue)
  }
  
  return { count, increment, decrement, reset }
}

export default useCounter
```

```typescript
// src/components/Counter.tsx
import useCounter from '../hooks/useCounter'

function Counter() {
  const { count, increment, decrement, reset } = useCounter(0)
  
  return (
    <div>
      <p>カウント: {count}</p>
      <button onClick={increment}>増やす</button>
      <button onClick={decrement}>減らす</button>
      <button onClick={reset}>リセット</button>
    </div>
  )
}

export default Counter
```

### 実践例: useInputフック

```typescript
// src/hooks/useInput.ts
import { useState, ChangeEvent } from 'react'

interface UseInputReturn {
  value: string
  onChange: (e: ChangeEvent<HTMLInputElement>) => void
  reset: () => void
}

function useInput(initialValue: string = ''): UseInputReturn {
  const [value, setValue] = useState<string>(initialValue)
  
  const onChange = (e: ChangeEvent<HTMLInputElement>) => {
    setValue(e.target.value)
  }
  
  const reset = () => {
    setValue(initialValue)
  }
  
  return { value, onChange, reset }
}

export default useInput
```

```typescript
// src/components/Input.tsx
import useInput from '../hooks/useInput'

function Input() {
  const { value, onChange, reset } = useInput('')
  
  return (
    <div>
      <input
        type="text"
        value={value}
        onChange={onChange}
        placeholder="入力してください"
      />
      <button onClick={reset}>リセット</button>
    </div>
  )
}

export default Input
```

### 実践例: useToggleフック

```typescript
// src/hooks/useToggle.ts
import { useState } from 'react'

interface UseToggleReturn {
  value: boolean
  toggle: () => void
  setTrue: () => void
  setFalse: () => void
}

function useToggle(initialValue: boolean = false): UseToggleReturn {
  const [value, setValue] = useState<boolean>(initialValue)
  
  const toggle = () => {
    setValue(prev => !prev)
  }
  
  const setTrue = () => {
    setValue(true)
  }
  
  const setFalse = () => {
    setValue(false)
  }
  
  return { value, toggle, setTrue, setFalse }
}

export default useToggle
```

```typescript
// src/components/Toggle.tsx
import useToggle from '../hooks/useToggle'

function Toggle() {
  const { value, toggle, setTrue, setFalse } = useToggle(false)
  
  return (
    <div>
      <p>状態: {value ? 'ON' : 'OFF'}</p>
      <button onClick={toggle}>切り替え</button>
      <button onClick={setTrue}>ON</button>
      <button onClick={setFalse}>OFF</button>
    </div>
  )
}

export default Toggle
```

### 実践例: useFetchフック（ジェネリクス使用）

```typescript
// src/hooks/useFetch.ts
import { useState, useEffect } from 'react'

interface UseFetchReturn<T> {
  data: T | null
  loading: boolean
  error: Error | null
  refetch: () => void
}

function useFetch<T>(url: string): UseFetchReturn<T> {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState<boolean>(true)
  const [error, setError] = useState<Error | null>(null)
  
  const fetchData = async () => {
    try {
      setLoading(true)
      setError(null)
      const response = await fetch(url)
      if (!response.ok) {
        throw new Error('データの取得に失敗しました')
      }
      const json = await response.json()
      setData(json)
    } catch (err) {
      setError(err as Error)
    } finally {
      setLoading(false)
    }
  }
  
  useEffect(() => {
    fetchData()
  }, [url])
  
  return { data, loading, error, refetch: fetchData }
}

export default useFetch
```

```typescript
// src/components/UserList.tsx
import useFetch from '../hooks/useFetch'

interface User {
  id: number
  name: string
  email: string
}

function UserList() {
  const { data, loading, error, refetch } = useFetch<User[]>('/api/users')
  
  if (loading) return <p>読み込み中...</p>
  if (error) return <p>エラー: {error.message}</p>
  
  return (
    <div>
      <button onClick={refetch}>再取得</button>
      <ul>
        {data?.map(user => (
          <li key={user.id}>
            {user.name} - {user.email}
          </li>
        ))}
      </ul>
    </div>
  )
}

export default UserList
```

### 実践例: useLocalStorageフック

```typescript
// src/hooks/useLocalStorage.ts
import { useState, useEffect } from 'react'

interface UseLocalStorageReturn<T> {
  value: T
  setValue: (value: T) => void
  removeValue: () => void
}

function useLocalStorage<T>(
  key: string,
  initialValue: T
): UseLocalStorageReturn<T> {
  const [value, setValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key)
      return item ? JSON.parse(item) : initialValue
    } catch {
      return initialValue
    }
  })
  
  useEffect(() => {
    try {
      window.localStorage.setItem(key, JSON.stringify(value))
    } catch (error) {
      console.error('localStorageへの保存に失敗しました', error)
    }
  }, [key, value])
  
  const removeValue = () => {
    try {
      window.localStorage.removeItem(key)
      setValue(initialValue)
    } catch (error) {
      console.error('localStorageからの削除に失敗しました', error)
    }
  }
  
  return { value, setValue, removeValue }
}

export default useLocalStorage
```

```typescript
// src/components/Settings.tsx
import useLocalStorage from '../hooks/useLocalStorage'

interface Settings {
  theme: 'light' | 'dark'
  language: 'ja' | 'en'
}

function Settings() {
  const { value, setValue, removeValue } = useLocalStorage<Settings>(
    'settings',
    { theme: 'light', language: 'ja' }
  )
  
  return (
    <div>
      <div>
        <label>テーマ:</label>
        <select
          value={value.theme}
          onChange={(e) =>
            setValue({ ...value, theme: e.target.value as 'light' | 'dark' })
          }
        >
          <option value="light">ライト</option>
          <option value="dark">ダーク</option>
        </select>
      </div>
      <div>
        <label>言語:</label>
        <select
          value={value.language}
          onChange={(e) =>
            setValue({ ...value, language: e.target.value as 'ja' | 'en' })
          }
        >
          <option value="ja">日本語</option>
          <option value="en">English</option>
        </select>
      </div>
      <button onClick={removeValue}>リセット</button>
    </div>
  )
}

export default Settings
```

### 実践例: useDebounceフック

```typescript
// src/hooks/useDebounce.ts
import { useState, useEffect } from 'react'

function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value)
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value)
    }, delay)
    
    return () => {
      clearTimeout(handler)
    }
  }, [value, delay])
  
  return debouncedValue
}

export default useDebounce
```

```typescript
// src/components/Search.tsx
import { useState, useEffect } from 'react'
import useDebounce from '../hooks/useDebounce'

function Search() {
  const [query, setQuery] = useState<string>('')
  const debouncedQuery = useDebounce<string>(query, 500)
  
  useEffect(() => {
    if (debouncedQuery) {
      console.log('検索:', debouncedQuery)
      // 検索APIを呼び出す
    }
  }, [debouncedQuery])
  
  return (
    <input
      type="text"
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      placeholder="検索..."
    />
  )
}

export default Search
```

---

## まとめ

この章では、TypeScriptでReact Hooksの型を定義する方法について学びました。

### 学んだこと
- useState<T>: ジェネリクスを使用して型を指定
- useRef<T>: DOM要素への参照や値の保持に型を指定
- カスタムフックの型定義: 引数と戻り値の型を明示

### 重要なポイント
1. **useState**: `useState<型>(初期値)`で型を指定
2. **useRef**: `useRef<型>(初期値)`で型を指定
3. **型推論**: 初期値から型を推論できる場合もある
4. **カスタムフック**: インターフェースや型エイリアスを使用して型を定義
5. **ジェネリクス**: 汎用的な型定義に使用

### 次のステップ
次の章では、型安全な開発について詳しく学びます。

---

## 演習問題

### 問題1: useStateの型定義
`Counter`コンポーネントで、`useState`に数値型を指定してください。

<details>
<summary>解答例</summary>

```typescript
// src/components/Counter.tsx
import { useState } from 'react'

function Counter() {
  const [count, setCount] = useState<number>(0)
  
  return (
    <div>
      <p>カウント: {count}</p>
      <button onClick={() => setCount(count + 1)}>増やす</button>
    </div>
  )
}

export default Counter
```
</details>

### 問題2: useRefの型定義
`InputFocus`コンポーネントで、`useRef`に`HTMLInputElement`型を指定してください。

<details>
<summary>解答例</summary>

```typescript
// src/components/InputFocus.tsx
import { useRef } from 'react'

function InputFocus() {
  const inputRef = useRef<HTMLInputElement>(null)
  
  const handleFocus = () => {
    inputRef.current?.focus()
  }
  
  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={handleFocus}>フォーカス</button>
    </div>
  )
}

export default InputFocus
```
</details>

### 問題3: カスタムフックの型定義
`useCounter`フックを作成し、戻り値の型を定義してください。

<details>
<summary>解答例</summary>

```typescript
// src/hooks/useCounter.ts
import { useState } from 'react'

interface UseCounterReturn {
  count: number
  increment: () => void
  decrement: () => void
}

function useCounter(initialValue: number = 0): UseCounterReturn {
  const [count, setCount] = useState<number>(initialValue)
  
  const increment = () => setCount(count + 1)
  const decrement = () => setCount(count - 1)
  
  return { count, increment, decrement }
}

export default useCounter
```
</details>

### 問題4: ジェネリクスを使用したフック
ジェネリクスを使用して、任意の型の値を扱える`useValue`フックを作成してください。

<details>
<summary>解答例</summary>

```typescript
// src/hooks/useValue.ts
import { useState } from 'react'

interface UseValueReturn<T> {
  value: T
  setValue: (value: T) => void
  reset: () => void
}

function useValue<T>(initialValue: T): UseValueReturn<T> {
  const [value, setValue] = useState<T>(initialValue)
  
  const reset = () => {
    setValue(initialValue)
  }
  
  return { value, setValue, reset }
}

export default useValue
```
</details>

### 問題5: useFetchフックの型定義
ジェネリクスを使用して、`useFetch`フックに型を定義してください。

<details>
<summary>解答例</summary>

```typescript
// src/hooks/useFetch.ts
import { useState, useEffect } from 'react'

interface UseFetchReturn<T> {
  data: T | null
  loading: boolean
  error: Error | null
}

function useFetch<T>(url: string): UseFetchReturn<T> {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState<boolean>(true)
  const [error, setError] = useState<Error | null>(null)
  
  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true)
        const response = await fetch(url)
        const json = await response.json()
        setData(json)
      } catch (err) {
        setError(err as Error)
      } finally {
        setLoading(false)
      }
    }
    
    fetchData()
  }, [url])
  
  return { data, loading, error }
}

export default useFetch
```
</details>

---

お疲れ様でした！次の章に進みましょう。

