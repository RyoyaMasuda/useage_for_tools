# 4-1. フォーム実装とバリデーション（React Hook Form）

この章では、React Hook Formを使用したフォーム管理について学びます。React Hook Formを理解することで、パフォーマンスが高く、使いやすいフォームを実装できるようになります。

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
React Hook Formは、**高性能で使いやすいReactフォーム管理ライブラリ**です。最小限の再レンダリングで、シンプルなAPIを提供します。

### 日常生活での例
- **アンケートフォーム**: 複数の質問（入力フィールド）をまとめて管理し、一度に送信する
- **申込書**: 名前、住所、連絡先など、複数の情報（入力フィールド）を入力し、提出する
- **注文フォーム**: 商品情報、配送先、支払い方法など、複数の情報（入力フィールド）を入力し、注文を確定する

### React Hook Formの特徴
1. **パフォーマンス**: 最小限の再レンダリングで高速
2. **使いやすさ**: シンプルなAPIで簡単に使える
3. **バリデーション**: 簡単にバリデーションを実装できる
4. **型安全性**: TypeScriptを完全サポート

### インストール

```bash
npm install react-hook-form
```

### 実践例: 基本的なフォーム

```jsx
// src/components/BasicForm.jsx
import { useForm } from 'react-hook-form';

function BasicForm() {
    const { register, handleSubmit } = useForm();

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
        // ここでAPIに送信する処理を実装
    };

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
    );
}

export default BasicForm;
```

### registerとは
`register`は、**入力フィールドをReact Hook Formに登録する関数**です。これにより、フォームの状態管理が自動化されます。

### handleSubmitとは
`handleSubmit`は、**フォーム送信を処理する関数**です。バリデーションが成功した場合のみ、指定した関数を実行します。

### 実践例: 様々な入力タイプ

```jsx
// src/components/VariousInputs.jsx
import { useForm } from 'react-hook-form';

function VariousInputs() {
    const { register, handleSubmit } = useForm();

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
    };

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
    );
}

export default VariousInputs;
```

---

## バリデーションルールの実装

### バリデーションとは
バリデーションは、**入力データが正しい形式であるかを確認する**処理です。必須チェック、文字数制限、形式チェックなどを行います。

### 基本的なバリデーションルール

```jsx
// 必須チェック
{...register('name', { required: true })}

// カスタムメッセージ付き必須チェック
{...register('name', { required: '名前は必須です' })}

// 最小文字数
{...register('name', { minLength: { value: 3, message: '3文字以上入力してください' } })}

// 最大文字数
{...register('name', { maxLength: { value: 20, message: '20文字以内で入力してください' } })}

// パターンマッチ（正規表現）
{...register('email', { 
    pattern: {
        value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
        message: '有効なメールアドレスを入力してください'
    }
})}

// 最小値
{...register('age', { min: { value: 18, message: '18歳以上である必要があります' } })}

// 最大値
{...register('age', { max: { value: 100, message: '100歳以下である必要があります' } })}
```

### 実践例: 基本的なバリデーション

```jsx
// src/components/ValidatedForm.jsx
import { useForm } from 'react-hook-form';

function ValidatedForm() {
    const { register, handleSubmit, formState: { errors } } = useForm();

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>名前（必須）</label>
                <input 
                    {...register('name', { 
                        required: '名前は必須です',
                        minLength: {
                            value: 2,
                            message: '2文字以上入力してください'
                        }
                    })} 
                />
                {errors.name && <span style={{ color: 'red' }}>{errors.name.message}</span>}
            </div>

            <div>
                <label>メールアドレス（必須）</label>
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

            <div>
                <label>年齢</label>
                <input 
                    type="number"
                    {...register('age', { 
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
                {errors.age && <span style={{ color: 'red' }}>{errors.age.message}</span>}
            </div>

            <button type="submit">送信</button>
        </form>
    );
}

export default ValidatedForm;
```

### 実践例: カスタムバリデーション

```jsx
// src/components/CustomValidationForm.jsx
import { useForm } from 'react-hook-form';

function CustomValidationForm() {
    const { register, handleSubmit, formState: { errors }, watch } = useForm();

    // パスワードの確認用（watchで監視）
    const password = watch('password');

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>パスワード</label>
                <input 
                    type="password"
                    {...register('password', { 
                        required: 'パスワードは必須です',
                        minLength: {
                            value: 8,
                            message: '8文字以上入力してください'
                        },
                        validate: (value) => {
                            // カスタムバリデーション: 大文字、小文字、数字を含むかチェック
                            const hasUpperCase = /[A-Z]/.test(value);
                            const hasLowerCase = /[a-z]/.test(value);
                            const hasNumber = /[0-9]/.test(value);
                            
                            if (!hasUpperCase || !hasLowerCase || !hasNumber) {
                                return 'パスワードは大文字、小文字、数字を含む必要があります';
                            }
                            return true;
                        }
                    })} 
                />
                {errors.password && <span style={{ color: 'red' }}>{errors.password.message}</span>}
            </div>

            <div>
                <label>パスワード（確認）</label>
                <input 
                    type="password"
                    {...register('confirmPassword', { 
                        required: 'パスワードの確認は必須です',
                        validate: (value) => {
                            return value === password || 'パスワードが一致しません';
                        }
                    })} 
                />
                {errors.confirmPassword && <span style={{ color: 'red' }}>{errors.confirmPassword.message}</span>}
            </div>

            <button type="submit">送信</button>
        </form>
    );
}

export default CustomValidationForm;
```

### 実践例: 非同期バリデーション

```jsx
// src/components/AsyncValidationForm.jsx
import { useForm } from 'react-hook-form';

// ユーザー名の重複チェック（模擬）
const checkUsernameAvailability = async (username) => {
    // 実際のアプリケーションでは、APIを呼び出す
    await new Promise(resolve => setTimeout(resolve, 1000));
    const takenUsernames = ['admin', 'user', 'test'];
    return !takenUsernames.includes(username);
};

function AsyncValidationForm() {
    const { register, handleSubmit, formState: { errors } } = useForm();

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>ユーザー名</label>
                <input 
                    {...register('username', { 
                        required: 'ユーザー名は必須です',
                        minLength: {
                            value: 3,
                            message: '3文字以上入力してください'
                        },
                        validate: async (value) => {
                            const isAvailable = await checkUsernameAvailability(value);
                            return isAvailable || 'このユーザー名は既に使用されています';
                        }
                    })} 
                />
                {errors.username && <span style={{ color: 'red' }}>{errors.username.message}</span>}
            </div>

            <button type="submit">送信</button>
        </form>
    );
}

export default AsyncValidationForm;
```

---

## エラーメッセージの表示

### エラーメッセージとは
エラーメッセージは、**バリデーションエラーが発生した際に表示するメッセージ**です。ユーザーに何が間違っているかを明確に伝えます。

### formState.errorsの使用

```jsx
const { formState: { errors } } = useForm();

// エラーメッセージの表示
{errors.fieldName && <span>{errors.fieldName.message}</span>}
```

### 実践例: エラーメッセージの表示

```jsx
// src/components/ErrorDisplayForm.jsx
import { useForm } from 'react-hook-form';

function ErrorDisplayForm() {
    const { register, handleSubmit, formState: { errors } } = useForm();

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>名前</label>
                <input 
                    {...register('name', { 
                        required: '名前は必須です',
                        minLength: {
                            value: 2,
                            message: '2文字以上入力してください'
                        }
                    })} 
                />
                {errors.name && (
                    <div style={{ color: 'red', fontSize: '14px', marginTop: '4px' }}>
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
                />
                {errors.email && (
                    <div style={{ color: 'red', fontSize: '14px', marginTop: '4px' }}>
                        {errors.email.message}
                    </div>
                )}
            </div>

            <button type="submit">送信</button>
        </form>
    );
}

export default ErrorDisplayForm;
```

### 実践例: エラーメッセージコンポーネント

```jsx
// src/components/ErrorMessage.jsx
function ErrorMessage({ error }) {
    if (!error) return null;

    return (
        <span style={{ 
            color: 'red', 
            fontSize: '14px', 
            marginTop: '4px',
            display: 'block'
        }}>
            {error.message}
        </span>
    );
}

// src/components/FormWithErrorComponent.jsx
import { useForm } from 'react-hook-form';
import ErrorMessage from './ErrorMessage';

function FormWithErrorComponent() {
    const { register, handleSubmit, formState: { errors } } = useForm();

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>名前</label>
                <input 
                    {...register('name', { required: '名前は必須です' })} 
                />
                <ErrorMessage error={errors.name} />
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
                <ErrorMessage error={errors.email} />
            </div>

            <button type="submit">送信</button>
        </form>
    );
}

export default FormWithErrorComponent;
```

### 実践例: 送信時のエラー表示

```jsx
// src/components/SubmitErrorForm.jsx
import { useForm } from 'react-hook-form';

function SubmitErrorForm() {
    const { register, handleSubmit, formState: { errors }, setError } = useForm();

    const onSubmit = async (data) => {
        try {
            // API呼び出し（模擬）
            await new Promise((resolve, reject) => {
                setTimeout(() => {
                    // サーバー側のバリデーションエラーを模擬
                    reject({ 
                        field: 'email',
                        message: 'このメールアドレスは既に登録されています'
                    });
                }, 1000);
            });
        } catch (error) {
            // サーバー側のエラーをフォームに設定
            setError(error.field, {
                type: 'server',
                message: error.message
            });
        }
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>メールアドレス</label>
                <input 
                    type="email"
                    {...register('email', { 
                        required: 'メールアドレスは必須です'
                    })} 
                />
                {errors.email && (
                    <div style={{ color: 'red', fontSize: '14px', marginTop: '4px' }}>
                        {errors.email.message}
                    </div>
                )}
            </div>

            <button type="submit">送信</button>
        </form>
    );
}

export default SubmitErrorForm;
```

---

## UIライブラリとの連携

### UIライブラリとの連携とは
React Hook Formは、**Chakra UIやMaterial-UIなどのUIライブラリと簡単に連携**できます。`Controller`コンポーネントを使用することで、任意のUIコンポーネントと統合できます。

### Controllerコンポーネントとは
`Controller`は、**React Hook FormとUIライブラリのコンポーネントを連携する**ためのコンポーネントです。カスタムコンポーネントやUIライブラリのコンポーネントと統合できます。

### 実践例: Chakra UIとの連携

```jsx
// src/components/ChakraUIForm.jsx
import { useForm, Controller } from 'react-hook-form';
import {
    Box,
    Button,
    FormControl,
    FormLabel,
    FormErrorMessage,
    Input,
    VStack
} from '@chakra-ui/react';

function ChakraUIForm() {
    const { control, handleSubmit, formState: { errors } } = useForm();

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
    };

    return (
        <Box maxW="md" mx="auto" mt={8} p={6} borderWidth="1px" borderRadius="lg">
            <form onSubmit={handleSubmit(onSubmit)}>
                <VStack spacing={4}>
                    <FormControl isInvalid={!!errors.name}>
                        <FormLabel>名前</FormLabel>
                        <Controller
                            name="name"
                            control={control}
                            rules={{ required: '名前は必須です' }}
                            render={({ field }) => <Input {...field} />}
                        />
                        <FormErrorMessage>
                            {errors.name && errors.name.message}
                        </FormErrorMessage>
                    </FormControl>

                    <FormControl isInvalid={!!errors.email}>
                        <FormLabel>メールアドレス</FormLabel>
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
                            render={({ field }) => <Input type="email" {...field} />}
                        />
                        <FormErrorMessage>
                            {errors.email && errors.email.message}
                        </FormErrorMessage>
                    </FormControl>

                    <Button type="submit" colorScheme="blue" width="full">
                        送信
                    </Button>
                </VStack>
            </form>
        </Box>
    );
}

export default ChakraUIForm;
```

### 実践例: Material-UIとの連携

```jsx
// src/components/MUIForm.jsx
import { useForm, Controller } from 'react-hook-form';
import {
    Box,
    Button,
    TextField,
    Container,
    Stack
} from '@mui/material';

function MUIForm() {
    const { control, handleSubmit, formState: { errors } } = useForm();

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
    };

    return (
        <Container maxWidth="sm">
            <Box sx={{ mt: 4 }}>
                <form onSubmit={handleSubmit(onSubmit)}>
                    <Stack spacing={3}>
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
                                    fullWidth
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
                                    fullWidth
                                />
                            )}
                        />

                        <Button type="submit" variant="contained" fullWidth>
                            送信
                        </Button>
                    </Stack>
                </form>
            </Box>
        </Container>
    );
}

export default MUIForm;
```

### 実践例: カスタムコンポーネントとの連携

```jsx
// src/components/CustomInput.jsx
function CustomInput({ value, onChange, error, ...props }) {
    return (
        <div>
            <input
                value={value}
                onChange={(e) => onChange(e.target.value)}
                style={{
                    border: error ? '2px solid red' : '1px solid #ccc',
                    padding: '8px',
                    borderRadius: '4px',
                    width: '100%'
                }}
                {...props}
            />
            {error && (
                <span style={{ color: 'red', fontSize: '14px' }}>
                    {error.message}
                </span>
            )}
        </div>
    );
}

// src/components/CustomComponentForm.jsx
import { useForm, Controller } from 'react-hook-form';
import CustomInput from './CustomInput';

function CustomComponentForm() {
    const { control, handleSubmit, formState: { errors } } = useForm();

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>名前</label>
                <Controller
                    name="name"
                    control={control}
                    rules={{ required: '名前は必須です' }}
                    render={({ field, fieldState }) => (
                        <CustomInput
                            {...field}
                            error={fieldState.error}
                        />
                    )}
                />
            </div>

            <div>
                <label>メールアドレス</label>
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
                    render={({ field, fieldState }) => (
                        <CustomInput
                            {...field}
                            type="email"
                            error={fieldState.error}
                        />
                    )}
                />
            </div>

            <button type="submit">送信</button>
        </form>
    );
}

export default CustomComponentForm;
```

### 実践例: react-hook-formとUIライブラリの統合パッケージ

```bash
# Chakra UI用の統合パッケージ
npm install @hookform/resolvers

# Material-UI用の統合パッケージ（必要に応じて）
# MUIは標準でreact-hook-formと統合可能
```

```jsx
// src/components/IntegratedForm.jsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';
import {
    Box,
    Button,
    FormControl,
    FormLabel,
    FormErrorMessage,
    Input,
    VStack
} from '@chakra-ui/react';
import { Controller } from 'react-hook-form';

// Zodスキーマでバリデーションを定義
const schema = z.object({
    name: z.string().min(2, '2文字以上入力してください'),
    email: z.string().email('有効なメールアドレスを入力してください')
});

function IntegratedForm() {
    const { control, handleSubmit, formState: { errors } } = useForm({
        resolver: zodResolver(schema)
    });

    const onSubmit = (data) => {
        console.log('送信されたデータ:', data);
    };

    return (
        <Box maxW="md" mx="auto" mt={8} p={6} borderWidth="1px" borderRadius="lg">
            <form onSubmit={handleSubmit(onSubmit)}>
                <VStack spacing={4}>
                    <FormControl isInvalid={!!errors.name}>
                        <FormLabel>名前</FormLabel>
                        <Controller
                            name="name"
                            control={control}
                            render={({ field }) => <Input {...field} />}
                        />
                        <FormErrorMessage>
                            {errors.name && errors.name.message}
                        </FormErrorMessage>
                    </FormControl>

                    <FormControl isInvalid={!!errors.email}>
                        <FormLabel>メールアドレス</FormLabel>
                        <Controller
                            name="email"
                            control={control}
                            render={({ field }) => <Input type="email" {...field} />}
                        />
                        <FormErrorMessage>
                            {errors.email && errors.email.message}
                        </FormErrorMessage>
                    </FormControl>

                    <Button type="submit" colorScheme="blue" width="full">
                        送信
                    </Button>
                </VStack>
            </form>
        </Box>
    );
}

export default IntegratedForm;
```

---

## まとめ

この章では、React Hook Formについて以下のことを学びました。

### フォーム管理の基本
- **register**: 入力フィールドをReact Hook Formに登録する関数
- **handleSubmit**: フォーム送信を処理する関数
- **useForm**: React Hook Formのメインフック
- 様々な入力タイプ（テキスト、数値、メール、パスワード、チェックボックス、ラジオボタン、セレクトボックス、日付）に対応

### バリデーションルールの実装
- **基本的なバリデーション**: required、minLength、maxLength、pattern、min、max
- **カスタムバリデーション**: validate関数を使用した独自のバリデーション
- **非同期バリデーション**: サーバー側のバリデーションとの連携
- **パスワード確認**: watchを使用した値の監視

### エラーメッセージの表示
- **formState.errors**: エラー情報を取得
- **エラーメッセージの表示**: エラーが発生した際にメッセージを表示
- **エラーメッセージコンポーネント**: 再利用可能なエラーメッセージコンポーネント
- **サーバー側のエラー**: setErrorを使用したサーバー側のエラー表示

### UIライブラリとの連携
- **Controller**: UIライブラリのコンポーネントと連携するためのコンポーネント
- **Chakra UI**: Chakra UIとの連携方法
- **Material-UI**: Material-UIとの連携方法
- **カスタムコンポーネント**: 独自のコンポーネントとの連携方法

### React Hook Formのメリット
- **パフォーマンス**: 最小限の再レンダリングで高速
- **使いやすさ**: シンプルなAPIで簡単に使える
- **バリデーション**: 簡単にバリデーションを実装できる
- **型安全性**: TypeScriptを完全サポート
- **UIライブラリとの統合**: 様々なUIライブラリと簡単に連携できる

### 次のステップ
React Hook Formを理解したら、次のトピックに進みましょう：
- スキーマバリデーション（4-2）
- スタイリングとコンポーネント設計（5章）

---

## 演習問題

以下の問題を解いて、React Hook Formの理解を深めましょう。

### 問題1: 基本的なフォーム
名前とメールアドレスを入力するフォームを作成し、送信時にデータをコンソールに表示してください。

<details>
<summary>解答例</summary>

```jsx
import { useForm } from 'react-hook-form';

function BasicForm() {
    const { register, handleSubmit } = useForm();

    const onSubmit = (data) => {
        console.log('フォームデータ:', data);
    };

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
    );
}

export default BasicForm;
```
</details>

### 問題2: バリデーション付きフォーム
名前（必須、2文字以上）とメールアドレス（必須、有効な形式）を入力するフォームを作成し、バリデーションエラーを表示してください。

<details>
<summary>解答例</summary>

```jsx
import { useForm } from 'react-hook-form';

function ValidatedForm() {
    const { register, handleSubmit, formState: { errors } } = useForm();

    const onSubmit = (data) => {
        console.log('フォームデータ:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>名前</label>
                <input 
                    {...register('name', { 
                        required: '名前は必須です',
                        minLength: { value: 2, message: '2文字以上入力してください' }
                    })} 
                />
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
    );
}

export default ValidatedForm;
```
</details>

### 問題3: パスワード確認フォーム
パスワードとパスワード確認を入力するフォームを作成し、パスワードが一致することを確認してください。

<details>
<summary>解答例</summary>

```jsx
import { useForm } from 'react-hook-form';

function PasswordForm() {
    const { register, handleSubmit, formState: { errors }, watch } = useForm();
    const password = watch('password');

    const onSubmit = (data) => {
        console.log('フォームデータ:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>パスワード</label>
                <input 
                    type="password"
                    {...register('password', { required: 'パスワードは必須です' })} 
                />
                {errors.password && <span style={{ color: 'red' }}>{errors.password.message}</span>}
            </div>
            <div>
                <label>パスワード（確認）</label>
                <input 
                    type="password"
                    {...register('confirmPassword', { 
                        required: 'パスワードの確認は必須です',
                        validate: (value) => value === password || 'パスワードが一致しません'
                    })} 
                />
                {errors.confirmPassword && <span style={{ color: 'red' }}>{errors.confirmPassword.message}</span>}
            </div>
            <button type="submit">送信</button>
        </form>
    );
}

export default PasswordForm;
```
</details>

### 問題4: Chakra UIとの連携
Chakra UIを使用して、名前とメールアドレスを入力するフォームを作成してください。

<details>
<summary>解答例</summary>

```jsx
import { useForm, Controller } from 'react-hook-form';
import { Box, Button, FormControl, FormLabel, FormErrorMessage, Input, VStack } from '@chakra-ui/react';

function ChakraForm() {
    const { control, handleSubmit, formState: { errors } } = useForm();

    const onSubmit = (data) => {
        console.log('フォームデータ:', data);
    };

    return (
        <Box maxW="md" mx="auto" mt={8} p={6}>
            <form onSubmit={handleSubmit(onSubmit)}>
                <VStack spacing={4}>
                    <FormControl isInvalid={!!errors.name}>
                        <FormLabel>名前</FormLabel>
                        <Controller
                            name="name"
                            control={control}
                            rules={{ required: '名前は必須です' }}
                            render={({ field }) => <Input {...field} />}
                        />
                        <FormErrorMessage>{errors.name?.message}</FormErrorMessage>
                    </FormControl>
                    <FormControl isInvalid={!!errors.email}>
                        <FormLabel>メールアドレス</FormLabel>
                        <Controller
                            name="email"
                            control={control}
                            rules={{ required: 'メールアドレスは必須です' }}
                            render={({ field }) => <Input type="email" {...field} />}
                        />
                        <FormErrorMessage>{errors.email?.message}</FormErrorMessage>
                    </FormControl>
                    <Button type="submit" colorScheme="blue" width="full">送信</Button>
                </VStack>
            </form>
        </Box>
    );
}

export default ChakraForm;
```
</details>

### 問題5: 複数のバリデーションルール
名前（必須、2-20文字）、メールアドレス（必須、有効な形式）、年齢（18-100歳）を入力するフォームを作成してください。

<details>
<summary>解答例</summary>

```jsx
import { useForm } from 'react-hook-form';

function MultiValidationForm() {
    const { register, handleSubmit, formState: { errors } } = useForm();

    const onSubmit = (data) => {
        console.log('フォームデータ:', data);
    };

    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <div>
                <label>名前</label>
                <input 
                    {...register('name', { 
                        required: '名前は必須です',
                        minLength: { value: 2, message: '2文字以上入力してください' },
                        maxLength: { value: 20, message: '20文字以内で入力してください' }
                    })} 
                />
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
            <div>
                <label>年齢</label>
                <input 
                    type="number"
                    {...register('age', { 
                        required: '年齢は必須です',
                        min: { value: 18, message: '18歳以上である必要があります' },
                        max: { value: 100, message: '100歳以下である必要があります' }
                    })} 
                />
                {errors.age && <span style={{ color: 'red' }}>{errors.age.message}</span>}
            </div>
            <button type="submit">送信</button>
        </form>
    );
}

export default MultiValidationForm;
```
</details>

---

お疲れ様でした！次の章に進みましょう。

