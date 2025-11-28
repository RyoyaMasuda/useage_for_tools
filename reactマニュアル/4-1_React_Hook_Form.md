# 4-1. React Hook Form

この章では、React Hook Formを使用したフォーム管理について学びます。React Hook Formは、パフォーマンスが高く、使いやすいフォーム管理ライブラリです。

---

## 目次

- [フォーム管理の基本 (register, handleSubmit)](#フォーム管理の基本-register-handlesubmit)
- [バリデーションルールの実装](#バリデーションルールの実装)
- [エラーメッセージの表示](#エラーメッセージの表示)
- [UIライブラリとの連携](#uiライブラリとの連携)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## フォーム管理の基本 (register, handleSubmit)

### React Hook Formとは
React Hook Formは、**高性能で使いやすいReactフォーム管理ライブラリ**です。

### React Hook Formの特徴
- **パフォーマンス**: 最小限の再レンダリング
- **使いやすさ**: シンプルなAPI
- **バリデーション**: 簡単にバリデーションを実装できる
- **型安全性**: TypeScriptを完全サポート

### インストール

```bash
npm install react-hook-form
```

### register - 入力フィールドの登録

`register`は、**入力フィールドをReact Hook Formに登録する関数**です。

#### 基本的な使い方

```jsx
import { useForm } from 'react-hook-form'

function Form() {
  const { register } = useForm()
  
  return (
    <form>
      <input {...register('name')} />
    </form>
  )
}
```

### handleSubmit - フォーム送信の処理

`handleSubmit`は、**フォーム送信を処理する関数**です。バリデーションが成功した場合のみ、指定した関数を実行します。

#### 基本的な使い方

```jsx
import { useForm } from 'react-hook-form'

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

### 実践例: 基本的なフォーム

```jsx
// src/components/BasicForm.jsx
import { useForm } from 'react-hook-form'

function BasicForm() {
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

export default BasicForm
```

### 実践例: 様々な入力タイプ

```jsx
// src/components/VariousInputs.jsx
import { useForm } from 'react-hook-form'

function VariousInputs() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
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

## バリデーションルールの実装

### バリデーションとは
バリデーションは、**入力値が正しい形式かどうかをチェックする**処理です。

### 基本的なバリデーションルール

```jsx
<input
  {...register('fieldName', {
    required: 'この項目は必須です',
    minLength: {
      value: 3,
      message: '3文字以上入力してください'
    },
    pattern: {
      value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
      message: '有効なメールアドレスを入力してください'
    }
  })}
/>
```

### 実践例: 基本的なバリデーション

```jsx
// src/components/ValidatedForm.jsx
import { useForm } from 'react-hook-form'

function ValidatedForm() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>名前（必須）</label>
        <input
          {...register('name', {
            required: '名前は必須です'
          })}
        />
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
      </div>
      
      <div>
        <label>パスワード（8文字以上）</label>
        <input
          type="password"
          {...register('password', {
            required: 'パスワードは必須です',
            minLength: {
              value: 8,
              message: 'パスワードは8文字以上である必要があります'
            }
          })}
        />
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default ValidatedForm
```

### 実践例: 様々なバリデーションルール

```jsx
// src/components/AdvancedValidation.jsx
import { useForm } from 'react-hook-form'

function AdvancedValidation() {
  const { register, handleSubmit, watch } = useForm()
  
  // パスワードの確認用（watchで監視）
  const password = watch('password')
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>ユーザー名</label>
        <input
          {...register('username', {
            required: 'ユーザー名は必須です',
            minLength: {
              value: 3,
              message: 'ユーザー名は3文字以上である必要があります'
            },
            maxLength: {
              value: 20,
              message: 'ユーザー名は20文字以下である必要があります'
            },
            pattern: {
              value: /^[a-zA-Z0-9_]+$/,
              message: 'ユーザー名は英数字とアンダースコアのみ使用できます'
            }
          })}
        />
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
      </div>
      
      <div>
        <label>年齢</label>
        <input
          type="number"
          {...register('age', {
            required: '年齢は必須です',
            min: {
              value: 18,
              message: '18歳以上である必要があります'
            },
            max: {
              value: 100,
              message: '100歳以下である必要があります'
            }
          })}
        />
      </div>
      
      <div>
        <label>パスワード</label>
        <input
          type="password"
          {...register('password', {
            required: 'パスワードは必須です',
            minLength: {
              value: 8,
              message: 'パスワードは8文字以上である必要があります'
            }
          })}
        />
      </div>
      
      <div>
        <label>パスワード（確認）</label>
        <input
          type="password"
          {...register('confirmPassword', {
            required: 'パスワードの確認は必須です',
            validate: (value) =>
              value === password || 'パスワードが一致しません'
          })}
        />
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default AdvancedValidation
```

### 実践例: カスタムバリデーション

```jsx
// src/components/CustomValidation.jsx
import { useForm } from 'react-hook-form'

function CustomValidation() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  // カスタムバリデーション関数
  const validatePhoneNumber = (value) => {
    const phoneRegex = /^[0-9]{10,11}$/
    return phoneRegex.test(value) || '有効な電話番号を入力してください'
  }
  
  const validateWebsite = (value) => {
    if (!value) return true // オプショナル
    const urlRegex = /^https?:\/\/.+\..+/
    return urlRegex.test(value) || '有効なURLを入力してください'
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>電話番号</label>
        <input
          {...register('phone', {
            validate: validatePhoneNumber
          })}
        />
      </div>
      
      <div>
        <label>ウェブサイト（オプション）</label>
        <input
          {...register('website', {
            validate: validateWebsite
          })}
        />
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default CustomValidation
```

---

## エラーメッセージの表示

### エラーメッセージの取得

`formState.errors`を使用して、エラーメッセージを取得できます。

### 基本的な使い方

```jsx
import { useForm } from 'react-hook-form'

function Form() {
  const { register, handleSubmit, formState: { errors } } = useForm()
  
  return (
    <form>
      <input {...register('name', { required: '名前は必須です' })} />
      {errors.name && <span>{errors.name.message}</span>}
    </form>
  )
}
```

### 実践例: エラーメッセージの表示

```jsx
// src/components/FormWithErrors.jsx
import { useForm } from 'react-hook-form'

function FormWithErrors() {
  const { register, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>名前</label>
        <input
          {...register('name', {
            required: '名前は必須です',
            minLength: {
              value: 2,
              message: '名前は2文字以上である必要があります'
            }
          })}
        />
        {errors.name && (
          <span style={{ color: 'red' }}>{errors.name.message}</span>
        )}
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
        {errors.email && (
          <span style={{ color: 'red' }}>{errors.email.message}</span>
        )}
      </div>
      
      <div>
        <label>パスワード</label>
        <input
          type="password"
          {...register('password', {
            required: 'パスワードは必須です',
            minLength: {
              value: 8,
              message: 'パスワードは8文字以上である必要があります'
            }
          })}
        />
        {errors.password && (
          <span style={{ color: 'red' }}>{errors.password.message}</span>
        )}
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default FormWithErrors
```

### 実践例: エラーメッセージのスタイリング

```jsx
// src/components/StyledErrorForm.jsx
import { useForm } from 'react-hook-form'

function StyledErrorForm() {
  const { register, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>名前</label>
        <input
          {...register('name', {
            required: '名前は必須です'
          })}
          style={{
            border: errors.name ? '1px solid red' : '1px solid #ccc'
          }}
        />
        {errors.name && (
          <div style={{
            color: 'red',
            fontSize: '12px',
            marginTop: '4px'
          }}>
            {errors.name.message}
          </div>
        )}
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
          style={{
            border: errors.email ? '1px solid red' : '1px solid #ccc'
          }}
        />
        {errors.email && (
          <div style={{
            color: 'red',
            fontSize: '12px',
            marginTop: '4px'
          }}>
            {errors.email.message}
          </div>
        )}
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default StyledErrorForm
```

### 実践例: エラーサマリーの表示

```jsx
// src/components/FormWithErrorSummary.jsx
import { useForm } from 'react-hook-form'

function FormWithErrorSummary() {
  const { register, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  const hasErrors = Object.keys(errors).length > 0
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      {hasErrors && (
        <div style={{
          backgroundColor: '#fee',
          border: '1px solid #fcc',
          padding: '10px',
          marginBottom: '20px',
          borderRadius: '4px'
        }}>
          <h3>エラーがあります</h3>
          <ul>
            {Object.entries(errors).map(([field, error]) => (
              <li key={field}>
                {field}: {error.message}
              </li>
            ))}
          </ul>
        </div>
      )}
      
      <div>
        <label>名前</label>
        <input
          {...register('name', {
            required: '名前は必須です'
          })}
        />
        {errors.name && (
          <span style={{ color: 'red' }}>{errors.name.message}</span>
        )}
      </div>
      
      <div>
        <label>メールアドレス</label>
        <input
          type="email"
          {...register('email', {
            required: 'メールアドレスは必須です'
          })}
        />
        {errors.email && (
          <span style={{ color: 'red' }}>{errors.email.message}</span>
        )}
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default FormWithErrorSummary
```

---

## UIライブラリとの連携

### UIライブラリとの連携とは
React Hook Formは、**様々なUIライブラリと連携**できます。`Controller`コンポーネントを使用することで、カスタムコンポーネントと統合できます。

### Controllerコンポーネント

`Controller`は、**カスタムコンポーネントをReact Hook Formに統合する**コンポーネントです。

### 基本的な使い方

```jsx
import { useForm, Controller } from 'react-hook-form'

function Form() {
  const { control, handleSubmit } = useForm()
  
  return (
    <form>
      <Controller
        name="fieldName"
        control={control}
        render={({ field }) => (
          <CustomComponent {...field} />
        )}
      />
    </form>
  )
}
```

### 実践例: カスタムコンポーネントとの連携

```jsx
// src/components/CustomInput.jsx
function CustomInput({ value, onChange, error, label }) {
  return (
    <div>
      <label>{label}</label>
      <input
        type="text"
        value={value}
        onChange={onChange}
        style={{
          border: error ? '1px solid red' : '1px solid #ccc'
        }}
      />
      {error && (
        <span style={{ color: 'red' }}>{error.message}</span>
      )}
    </div>
  )
}
```

```jsx
// src/components/FormWithCustomInput.jsx
import { useForm, Controller } from 'react-hook-form'
import CustomInput from './CustomInput'

function FormWithCustomInput() {
  const { control, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        name="name"
        control={control}
        rules={{
          required: '名前は必須です'
        }}
        render={({ field }) => (
          <CustomInput
            {...field}
            label="名前"
            error={errors.name}
          />
        )}
      />
      
      <Controller
        name="email"
        control={control}
        rules={{
          required: 'メールアドレスは必須です',
          pattern: {
            value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
            message: '有効なメールアドレスを入力してください'
          }
        }}
        render={({ field }) => (
          <CustomInput
            {...field}
            label="メールアドレス"
            error={errors.email}
          />
        )}
      />
      
      <button type="submit">送信</button>
    </form>
  )
}

export default FormWithCustomInput
```

### 実践例: Material-UIとの連携

```jsx
// src/components/MUIForm.jsx
import { useForm, Controller } from 'react-hook-form'
import { TextField, Button, Box } from '@mui/material'

function MUIForm() {
  const { control, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Box sx={{ display: 'flex', flexDirection: 'column', gap: 2 }}>
        <Controller
          name="name"
          control={control}
          rules={{ required: '名前は必須です' }}
          render={({ field }) => (
            <TextField
              {...field}
              label="名前"
              error={!!errors.name}
              helperText={errors.name?.message}
            />
          )}
        />
        
        <Controller
          name="email"
          control={control}
          rules={{
            required: 'メールアドレスは必須です',
            pattern: {
              value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
              message: '有効なメールアドレスを入力してください'
            }
          }}
          render={({ field }) => (
            <TextField
              {...field}
              label="メールアドレス"
              type="email"
              error={!!errors.email}
              helperText={errors.email?.message}
            />
          )}
        />
        
        <Button type="submit" variant="contained">
          送信
        </Button>
      </Box>
    </form>
  )
}

export default MUIForm
```

### 実践例: Chakra UIとの連携

```jsx
// src/components/ChakraForm.jsx
import { useForm, Controller } from 'react-hook-form'
import {
  FormControl,
  FormLabel,
  Input,
  FormErrorMessage,
  Button,
  VStack
} from '@chakra-ui/react'

function ChakraForm() {
  const { control, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <VStack spacing={4}>
        <Controller
          name="name"
          control={control}
          rules={{ required: '名前は必須です' }}
          render={({ field }) => (
            <FormControl isInvalid={!!errors.name}>
              <FormLabel>名前</FormLabel>
              <Input {...field} />
              <FormErrorMessage>
                {errors.name?.message}
              </FormErrorMessage>
            </FormControl>
          )}
        />
        
        <Controller
          name="email"
          control={control}
          rules={{
            required: 'メールアドレスは必須です',
            pattern: {
              value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
              message: '有効なメールアドレスを入力してください'
            }
          }}
          render={({ field }) => (
            <FormControl isInvalid={!!errors.email}>
              <FormLabel>メールアドレス</FormLabel>
              <Input {...field} type="email" />
              <FormErrorMessage>
                {errors.email?.message}
              </FormErrorMessage>
            </FormControl>
          )}
        />
        
        <Button type="submit" colorScheme="blue">
          送信
        </Button>
      </VStack>
    </form>
  )
}

export default ChakraForm
```

### 実践例: セレクトボックスとの連携

```jsx
// src/components/SelectForm.jsx
import { useForm, Controller } from 'react-hook-form'

function SelectForm() {
  const { control, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('送信されたデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>都道府県</label>
        <Controller
          name="prefecture"
          control={control}
          rules={{ required: '都道府県を選択してください' }}
          render={({ field }) => (
            <select {...field}>
              <option value="">選択してください</option>
              <option value="tokyo">東京都</option>
              <option value="osaka">大阪府</option>
              <option value="kyoto">京都府</option>
            </select>
          )}
        />
        {errors.prefecture && (
          <span style={{ color: 'red' }}>{errors.prefecture.message}</span>
        )}
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default SelectForm
```

---

## まとめ

この章では、React Hook Formを使用したフォーム管理について学びました。

### 学んだこと
- フォーム管理の基本: registerとhandleSubmitの使い方
- バリデーションルール: 様々なバリデーションルールの実装
- エラーメッセージの表示: formState.errorsを使用したエラー表示
- UIライブラリとの連携: Controllerコンポーネントを使用した統合

### 重要なポイント
1. **register**: `{...register('フィールド名')}`で入力フィールドを登録
2. **handleSubmit**: `handleSubmit(onSubmit)`でフォーム送信を処理
3. **バリデーション**: registerの第2引数でバリデーションルールを指定
4. **エラー表示**: `formState.errors`でエラーメッセージを取得
5. **Controller**: カスタムコンポーネントとReact Hook Formを統合

### 次のステップ
次の章では、スキーマバリデーションについて詳しく学びます。

---

## 演習問題

### 問題1: 基本的なフォーム
`register`と`handleSubmit`を使用して、名前とメールアドレスを入力するフォームを作成してください。

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

### 問題2: バリデーションルール
名前を必須項目にし、メールアドレスに正規表現パターンを設定してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/ValidatedForm.jsx
import { useForm } from 'react-hook-form'

function ValidatedForm() {
  const { register, handleSubmit } = useForm()
  
  const onSubmit = (data) => {
    console.log('フォームデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>名前</label>
        <input
          {...register('name', {
            required: '名前は必須です'
          })}
        />
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
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default ValidatedForm
```
</details>

### 問題3: エラーメッセージの表示
バリデーションエラーが発生した場合、エラーメッセージを表示してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/FormWithErrors.jsx
import { useForm } from 'react-hook-form'

function FormWithErrors() {
  const { register, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('フォームデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>名前</label>
        <input
          {...register('name', {
            required: '名前は必須です'
          })}
        />
        {errors.name && (
          <span style={{ color: 'red' }}>{errors.name.message}</span>
        )}
      </div>
      
      <div>
        <label>メールアドレス</label>
        <input
          type="email"
          {...register('email', {
            required: 'メールアドレスは必須です'
          })}
        />
        {errors.email && (
          <span style={{ color: 'red' }}>{errors.email.message}</span>
        )}
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default FormWithErrors
```
</details>

### 問題4: Controllerコンポーネント
`Controller`コンポーネントを使用して、カスタム入力コンポーネントと連携してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/ControllerForm.jsx
import { useForm, Controller } from 'react-hook-form'

function ControllerForm() {
  const { control, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('フォームデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Controller
        name="name"
        control={control}
        rules={{ required: '名前は必須です' }}
        render={({ field }) => (
          <div>
            <label>名前</label>
            <input {...field} />
            {errors.name && (
              <span style={{ color: 'red' }}>{errors.name.message}</span>
            )}
          </div>
        )}
      />
      
      <button type="submit">送信</button>
    </form>
  )
}

export default ControllerForm
```
</details>

### 問題5: 複数のバリデーションルール
名前、メールアドレス、パスワードの3つのフィールドに、それぞれ適切なバリデーションルールを設定してください。

<details>
<summary>解答例</summary>

```jsx
// src/components/MultiValidationForm.jsx
import { useForm } from 'react-hook-form'

function MultiValidationForm() {
  const { register, handleSubmit, formState: { errors } } = useForm()
  
  const onSubmit = (data) => {
    console.log('フォームデータ:', data)
  }
  
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>名前</label>
        <input
          {...register('name', {
            required: '名前は必須です',
            minLength: {
              value: 2,
              message: '名前は2文字以上である必要があります'
            }
          })}
        />
        {errors.name && (
          <span style={{ color: 'red' }}>{errors.name.message}</span>
        )}
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
        {errors.email && (
          <span style={{ color: 'red' }}>{errors.email.message}</span>
        )}
      </div>
      
      <div>
        <label>パスワード</label>
        <input
          type="password"
          {...register('password', {
            required: 'パスワードは必須です',
            minLength: {
              value: 8,
              message: 'パスワードは8文字以上である必要があります'
            }
          })}
        />
        {errors.password && (
          <span style={{ color: 'red' }}>{errors.password.message}</span>
        )}
      </div>
      
      <button type="submit">送信</button>
    </form>
  )
}

export default MultiValidationForm
```
</details>

---

お疲れ様でした！次の章に進みましょう。

