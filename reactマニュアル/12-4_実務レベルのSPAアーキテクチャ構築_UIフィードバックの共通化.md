# 12-4. 実務レベルのSPAアーキテクチャ構築（UIフィードバックの共通化）

この章では、Reactアプリケーションでユーザーに適切なフィードバックを提供するための共通化されたUIフィードバック機構について学びます。グローバルなエラーハンドリングとローディング状態の管理を理解することで、一貫性のあるユーザー体験を提供できるようになります。

---

## 目次

- [グローバルなエラーハンドリング機構](#グローバルなエラーハンドリング機構)
- [画面全体を覆うローディング (Overlay Loading) の管理](#画面全体を覆うローディング-overlay-loading-の管理)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## グローバルなエラーハンドリング機構

### グローバルなエラーハンドリングとは
グローバルなエラーハンドリングは、**アプリケーション全体で発生するエラーを一元管理する**仕組みです。エラーが発生した際に、適切なメッセージを表示し、ユーザーに分かりやすいフィードバックを提供します。

### 日常生活での例
- **病院の救急対応**: 緊急事態（エラー）が発生した際に、全員に通知（エラーメッセージ）を送る
- **ビルの火災報知器**: 火災（エラー）が発生した際に、全フロアに警報（エラーメッセージ）を鳴らす
- **学校の緊急放送**: 緊急事態（エラー）が発生した際に、全校に放送（エラーメッセージ）を流す

### エラーハンドリングの重要性
1. **ユーザー体験**: エラーが発生した際に、適切なメッセージを表示することで、ユーザーの混乱を防ぐ
2. **デバッグ**: エラー情報を適切に記録することで、問題の特定が容易になる
3. **一貫性**: アプリケーション全体で同じエラーハンドリングを使用することで、一貫性のある体験を提供
4. **保守性**: エラーハンドリングを一元管理することで、修正が容易になる

### 実践例: エラーコンテキストの作成

```typescript
// src/contexts/ErrorContext.tsx
import React, { createContext, useContext, useState, useCallback, ReactNode } from 'react';
import { Alert, AlertIcon, AlertTitle, AlertDescription, Box, CloseButton } from '@chakra-ui/react';

interface ErrorContextType {
    showError: (message: string, details?: string) => void;
    clearError: () => void;
}

const ErrorContext = createContext<ErrorContextType | undefined>(undefined);

export function ErrorProvider({ children }: { children: ReactNode }) {
    const [error, setError] = useState<{ message: string; details?: string } | null>(null);

    const showError = useCallback((message: string, details?: string) => {
        setError({ message, details });
        // 5秒後に自動的にエラーをクリア
        setTimeout(() => {
            setError(null);
        }, 5000);
    }, []);

    const clearError = useCallback(() => {
        setError(null);
    }, []);

    return (
        <ErrorContext.Provider value={{ showError, clearError }}>
            {children}
            {error && (
                <Box
                    position="fixed"
                    top={4}
                    right={4}
                    zIndex={9999}
                    maxW="md"
                >
                    <Alert status="error" borderRadius="md" boxShadow="lg">
                        <AlertIcon />
                        <Box flex="1">
                            <AlertTitle>{error.message}</AlertTitle>
                            {error.details && (
                                <AlertDescription>{error.details}</AlertDescription>
                            )}
                        </Box>
                        <CloseButton
                            position="absolute"
                            right="8px"
                            top="8px"
                            onClick={clearError}
                        />
                    </Alert>
                </Box>
            )}
        </ErrorContext.Provider>
    );
}

export function useError() {
    const context = useContext(ErrorContext);
    if (context === undefined) {
        throw new Error('useError must be used within an ErrorProvider');
    }
    return context;
}
```

### 実践例: エラーハンドリングフック

```typescript
// src/hooks/useErrorHandler.ts
import { useError } from '@/contexts/ErrorContext';
import { useCallback } from 'react';

export function useErrorHandler() {
    const { showError } = useError();

    const handleError = useCallback((error: unknown) => {
        if (error instanceof Error) {
            showError(error.message);
        } else if (typeof error === 'string') {
            showError(error);
        } else {
            showError('予期しないエラーが発生しました');
        }
    }, [showError]);

    const handleApiError = useCallback((error: any) => {
        if (error.response) {
            // APIエラーレスポンス
            const message = error.response.data?.message || 'リクエストに失敗しました';
            const details = error.response.data?.details;
            showError(message, details);
        } else if (error.request) {
            // リクエストは送信されたが、レスポンスがなかった
            showError('サーバーに接続できませんでした');
        } else {
            // リクエストの設定中にエラーが発生
            showError('リクエストの送信に失敗しました');
        }
    }, [showError]);

    return {
        handleError,
        handleApiError
    };
}
```

### 実践例: APIクライアントでのエラーハンドリング

```typescript
// src/services/api/client.ts
import axios, { AxiosError, AxiosInstance } from 'axios';
import { env } from '@/config/env';

// エラーイベントを発火するためのカスタムイベント
export const errorEvent = new EventTarget();

export const apiClient: AxiosInstance = axios.create({
    baseURL: env.apiUrl,
    timeout: 10000,
    headers: {
        'Content-Type': 'application/json'
    }
});

// リクエストインターセプター
apiClient.interceptors.request.use(
    (config) => {
        const token = localStorage.getItem('token');
        if (token) {
            config.headers.Authorization = `Bearer ${token}`;
        }
        return config;
    },
    (error) => {
        return Promise.reject(error);
    }
);

// レスポンスインターセプター
apiClient.interceptors.response.use(
    (response) => response,
    (error: AxiosError) => {
        // エラーイベントを発火
        errorEvent.dispatchEvent(new CustomEvent('apiError', { detail: error }));

        // 401エラーの場合、認証エラーとして処理
        if (error.response?.status === 401) {
            localStorage.removeItem('token');
            window.location.href = '/login';
        }

        return Promise.reject(error);
    }
);
```

### 実践例: エラーイベントリスナーの設定

```typescript
// src/App.tsx
import { useEffect } from 'react';
import { ErrorProvider, useError } from './contexts/ErrorContext';
import { errorEvent } from './services/api/client';

function AppContent() {
    const { showError } = useError();

    useEffect(() => {
        const handleApiError = (event: Event) => {
            const customEvent = event as CustomEvent<AxiosError>;
            const error = customEvent.detail;

            if (error.response) {
                const message = error.response.data?.message || 'リクエストに失敗しました';
                showError(message);
            } else if (error.request) {
                showError('サーバーに接続できませんでした');
            } else {
                showError('リクエストの送信に失敗しました');
            }
        };

        errorEvent.addEventListener('apiError', handleApiError);

        return () => {
            errorEvent.removeEventListener('apiError', handleApiError);
        };
    }, [showError]);

    return (
        <div>
            {/* アプリケーションのコンテンツ */}
        </div>
    );
}

function App() {
    return (
        <ErrorProvider>
            <AppContent />
        </ErrorProvider>
    );
}

export default App;
```

### 実践例: コンポーネントでのエラーハンドリング

```typescript
// src/components/UserList.tsx
import { useState, useEffect } from 'react';
import { useErrorHandler } from '@/hooks/useErrorHandler';
import { userService } from '@/services/api/users';
import { User } from '@/types/user';

export function UserList() {
    const [users, setUsers] = useState<User[]>([]);
    const [loading, setLoading] = useState(true);
    const { handleApiError } = useErrorHandler();

    useEffect(() => {
        const fetchUsers = async () => {
            try {
                setLoading(true);
                const data = await userService.getUsers();
                setUsers(data);
            } catch (error) {
                handleApiError(error);
            } finally {
                setLoading(false);
            }
        };

        fetchUsers();
    }, [handleApiError]);

    if (loading) {
        return <p>読み込み中...</p>;
    }

    return (
        <div>
            <h2>ユーザー一覧</h2>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </div>
    );
}
```

### 実践例: グローバルエラーバウンダリー

```typescript
// src/components/ErrorBoundary.tsx
import React, { Component, ErrorInfo, ReactNode } from 'react';
import { Box, Heading, Text, Button } from '@chakra-ui/react';

interface Props {
    children: ReactNode;
}

interface State {
    hasError: boolean;
    error: Error | null;
}

export class ErrorBoundary extends Component<Props, State> {
    constructor(props: Props) {
        super(props);
        this.state = {
            hasError: false,
            error: null
        };
    }

    static getDerivedStateFromError(error: Error): State {
        return {
            hasError: true,
            error
        };
    }

    componentDidCatch(error: Error, errorInfo: ErrorInfo) {
        // エラーをログに記録
        console.error('ErrorBoundary caught an error:', error, errorInfo);
        
        // エラー追跡サービスに送信（例: Sentry）
        // errorTrackingService.captureException(error, { extra: errorInfo });
    }

    handleReset = () => {
        this.setState({
            hasError: false,
            error: null
        });
    };

    render() {
        if (this.state.hasError) {
            return (
                <Box
                    p={8}
                    textAlign="center"
                    minH="100vh"
                    display="flex"
                    flexDirection="column"
                    justifyContent="center"
                    alignItems="center"
                >
                    <Heading size="lg" mb={4}>
                        エラーが発生しました
                    </Heading>
                    <Text mb={4} color="gray.600">
                        {this.state.error?.message || '予期しないエラーが発生しました'}
                    </Text>
                    <Button onClick={this.handleReset} colorScheme="blue">
                        再試行
                    </Button>
                </Box>
            );
        }

        return this.props.children;
    }
}

// src/main.tsx
import { ErrorBoundary } from './components/ErrorBoundary';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')!).render(
    <React.StrictMode>
        <ErrorBoundary>
            <App />
        </ErrorBoundary>
    </React.StrictMode>
);
```

---

## 画面全体を覆うローディング (Overlay Loading) の管理

### Overlay Loadingとは
Overlay Loadingは、**画面全体を覆うローディング表示**です。API呼び出し中や、重要な処理の実行中に、ユーザーに処理中であることを明確に示します。

### 日常生活での例
- **エレベーターの表示**: エレベーターが動いている間（ローディング中）、「運転中」の表示（ローディング表示）を出す
- **ATMの処理中**: ATMが処理中（ローディング中）の間、「処理中です」の表示（ローディング表示）を出す
- **電車のドア**: 電車のドアが閉まっている間（ローディング中）、「閉まっています」の表示（ローディング表示）を出す

### ローディング状態の管理の重要性
1. **ユーザー体験**: 処理中であることを明確に示すことで、ユーザーの不安を軽減
2. **操作の防止**: ローディング中に操作を防ぐことで、予期しない動作を防止
3. **一貫性**: アプリケーション全体で同じローディング表示を使用することで、一貫性のある体験を提供
4. **視覚的フィードバック**: 処理の進行状況を視覚的に示すことで、ユーザーに安心感を与える

### 実践例: ローディングコンテキストの作成

```typescript
// src/contexts/LoadingContext.tsx
import React, { createContext, useContext, useState, useCallback, ReactNode } from 'react';
import { Spinner, Box, Text, VStack } from '@chakra-ui/react';

interface LoadingContextType {
    showLoading: (message?: string) => void;
    hideLoading: () => void;
    isLoading: boolean;
}

const LoadingContext = createContext<LoadingContextType | undefined>(undefined);

export function LoadingProvider({ children }: { children: ReactNode }) {
    const [loading, setLoading] = useState(false);
    const [message, setMessage] = useState<string | undefined>(undefined);

    const showLoading = useCallback((msg?: string) => {
        setMessage(msg);
        setLoading(true);
    }, []);

    const hideLoading = useCallback(() => {
        setLoading(false);
        setMessage(undefined);
    }, []);

    return (
        <LoadingContext.Provider value={{ showLoading, hideLoading, isLoading: loading }}>
            {children}
            {loading && (
                <Box
                    position="fixed"
                    top={0}
                    left={0}
                    right={0}
                    bottom={0}
                    bg="rgba(0, 0, 0, 0.5)"
                    zIndex={9999}
                    display="flex"
                    justifyContent="center"
                    alignItems="center"
                >
                    <VStack spacing={4} bg="white" p={8} borderRadius="md" boxShadow="lg">
                        <Spinner size="xl" color="blue.500" thickness="4px" />
                        {message && (
                            <Text fontSize="md" color="gray.600">
                                {message}
                            </Text>
                        )}
                    </VStack>
                </Box>
            )}
        </LoadingContext.Provider>
    );
}

export function useLoading() {
    const context = useContext(LoadingContext);
    if (context === undefined) {
        throw new Error('useLoading must be used within a LoadingProvider');
    }
    return context;
}
```

### 実践例: ローディングフック

```typescript
// src/hooks/useLoadingHandler.ts
import { useLoading } from '@/contexts/LoadingContext';
import { useCallback } from 'react';

export function useLoadingHandler() {
    const { showLoading, hideLoading } = useLoading();

    const withLoading = useCallback(async <T,>(
        asyncFn: () => Promise<T>,
        message?: string
    ): Promise<T> => {
        try {
            showLoading(message);
            return await asyncFn();
        } finally {
            hideLoading();
        }
    }, [showLoading, hideLoading]);

    return {
        showLoading,
        hideLoading,
        withLoading
    };
}
```

### 実践例: APIクライアントでのローディング管理

```typescript
// src/services/api/client.ts
import axios, { AxiosInstance, InternalAxiosRequestConfig, AxiosResponse } from 'axios';
import { env } from '@/config/env';

// ローディングイベントを発火するためのカスタムイベント
export const loadingEvent = new EventTarget();

let loadingCount = 0;

const incrementLoading = () => {
    loadingCount++;
    if (loadingCount === 1) {
        loadingEvent.dispatchEvent(new CustomEvent('loadingStart'));
    }
};

const decrementLoading = () => {
    loadingCount--;
    if (loadingCount === 0) {
        loadingEvent.dispatchEvent(new CustomEvent('loadingEnd'));
    }
};

export const apiClient: AxiosInstance = axios.create({
    baseURL: env.apiUrl,
    timeout: 10000,
    headers: {
        'Content-Type': 'application/json'
    }
});

// リクエストインターセプター
apiClient.interceptors.request.use(
    (config: InternalAxiosRequestConfig) => {
        incrementLoading();
        const token = localStorage.getItem('token');
        if (token) {
            config.headers.Authorization = `Bearer ${token}`;
        }
        return config;
    },
    (error) => {
        decrementLoading();
        return Promise.reject(error);
    }
);

// レスポンスインターセプター
apiClient.interceptors.response.use(
    (response: AxiosResponse) => {
        decrementLoading();
        return response;
    },
    (error) => {
        decrementLoading();
        return Promise.reject(error);
    }
);
```

### 実践例: ローディングイベントリスナーの設定

```typescript
// src/App.tsx
import { useEffect } from 'react';
import { LoadingProvider, useLoading } from './contexts/LoadingContext';
import { loadingEvent } from './services/api/client';

function AppContent() {
    const { showLoading, hideLoading } = useLoading();

    useEffect(() => {
        const handleLoadingStart = () => {
            showLoading('読み込み中...');
        };

        const handleLoadingEnd = () => {
            hideLoading();
        };

        loadingEvent.addEventListener('loadingStart', handleLoadingStart);
        loadingEvent.addEventListener('loadingEnd', handleLoadingEnd);

        return () => {
            loadingEvent.removeEventListener('loadingStart', handleLoadingStart);
            loadingEvent.removeEventListener('loadingEnd', handleLoadingEnd);
        };
    }, [showLoading, hideLoading]);

    return (
        <div>
            {/* アプリケーションのコンテンツ */}
        </div>
    );
}

function App() {
    return (
        <LoadingProvider>
            <AppContent />
        </LoadingProvider>
    );
}

export default App;
```

### 実践例: コンポーネントでのローディング管理

```typescript
// src/components/UserList.tsx
import { useState, useEffect } from 'react';
import { useLoadingHandler } from '@/hooks/useLoadingHandler';
import { userService } from '@/services/api/users';
import { User } from '@/types/user';

export function UserList() {
    const [users, setUsers] = useState<User[]>([]);
    const { withLoading } = useLoadingHandler();

    useEffect(() => {
        const fetchUsers = async () => {
            const data = await withLoading(
                () => userService.getUsers(),
                'ユーザー一覧を読み込み中...'
            );
            setUsers(data);
        };

        fetchUsers();
    }, [withLoading]);

    return (
        <div>
            <h2>ユーザー一覧</h2>
            <ul>
                {users.map(user => (
                    <li key={user.id}>{user.name}</li>
                ))}
            </ul>
        </div>
    );
}
```

### 実践例: 複数のローディング状態の管理

```typescript
// src/contexts/LoadingContext.tsx
import React, { createContext, useContext, useState, useCallback, ReactNode } from 'react';
import { Spinner, Box, Text, VStack, Progress } from '@chakra-ui/react';

interface LoadingState {
    isLoading: boolean;
    message?: string;
    progress?: number; // 0-100
}

interface LoadingContextType {
    showLoading: (message?: string) => void;
    hideLoading: () => void;
    setProgress: (progress: number) => void;
    loadingState: LoadingState;
}

const LoadingContext = createContext<LoadingContextType | undefined>(undefined);

export function LoadingProvider({ children }: { children: ReactNode }) {
    const [loadingState, setLoadingState] = useState<LoadingState>({
        isLoading: false
    });

    const showLoading = useCallback((message?: string) => {
        setLoadingState({
            isLoading: true,
            message,
            progress: undefined
        });
    }, []);

    const hideLoading = useCallback(() => {
        setLoadingState({
            isLoading: false
        });
    }, []);

    const setProgress = useCallback((progress: number) => {
        setLoadingState(prev => ({
            ...prev,
            progress
        }));
    }, []);

    return (
        <LoadingContext.Provider value={{ showLoading, hideLoading, setProgress, loadingState }}>
            {children}
            {loadingState.isLoading && (
                <Box
                    position="fixed"
                    top={0}
                    left={0}
                    right={0}
                    bottom={0}
                    bg="rgba(0, 0, 0, 0.5)"
                    zIndex={9999}
                    display="flex"
                    justifyContent="center"
                    alignItems="center"
                >
                    <VStack spacing={4} bg="white" p={8} borderRadius="md" boxShadow="lg" minW="300px">
                        <Spinner size="xl" color="blue.500" thickness="4px" />
                        {loadingState.message && (
                            <Text fontSize="md" color="gray.600">
                                {loadingState.message}
                            </Text>
                        )}
                        {loadingState.progress !== undefined && (
                            <Box w="100%">
                                <Progress
                                    value={loadingState.progress}
                                    colorScheme="blue"
                                    size="sm"
                                    borderRadius="md"
                                />
                                <Text fontSize="sm" color="gray.500" mt={2} textAlign="center">
                                    {loadingState.progress}%
                                </Text>
                            </Box>
                        )}
                    </VStack>
                </Box>
            )}
        </LoadingContext.Provider>
    );
}

export function useLoading() {
    const context = useContext(LoadingContext);
    if (context === undefined) {
        throw new Error('useLoading must be used within a LoadingProvider');
    }
    return context;
}
```

### 実践例: エラーとローディングの統合

```typescript
// src/App.tsx
import { ErrorProvider } from './contexts/ErrorContext';
import { LoadingProvider } from './contexts/LoadingContext';
import { AppContent } from './AppContent';

function App() {
    return (
        <ErrorProvider>
            <LoadingProvider>
                <AppContent />
            </LoadingProvider>
        </ErrorProvider>
    );
}

export default App;
```

---

## まとめ

この章では、UIフィードバックの共通化について以下のことを学びました。

### グローバルなエラーハンドリング機構
- **エラーコンテキスト**: アプリケーション全体でエラーを管理するコンテキスト
- **エラーハンドリングフック**: エラーを処理するためのカスタムフック
- **APIクライアントでのエラーハンドリング**: インターセプターを使用したエラー処理
- **エラーバウンダリー**: Reactのエラーバウンダリーを使用したエラー処理
- **一貫性**: アプリケーション全体で同じエラーハンドリングを使用

### 画面全体を覆うローディング (Overlay Loading) の管理
- **ローディングコンテキスト**: アプリケーション全体でローディング状態を管理するコンテキスト
- **ローディングフック**: ローディングを処理するためのカスタムフック
- **APIクライアントでのローディング管理**: インターセプターを使用したローディング管理
- **進捗表示**: 処理の進捗を表示する機能
- **一貫性**: アプリケーション全体で同じローディング表示を使用

### UIフィードバックの共通化のメリット
- **ユーザー体験**: 一貫性のあるフィードバックにより、ユーザー体験が向上
- **保守性**: フィードバック処理を一元管理することで、修正が容易になる
- **開発効率**: 共通のコンポーネントやフックを使用することで、開発効率が向上
- **エラー追跡**: エラーを一元管理することで、問題の特定が容易になる

### 次のステップ
UIフィードバックの共通化を理解したら、次のトピックに進みましょう：
- CRUDロジックの抽象化（12-5）
- 実務レベルのアプリケーション開発の実践

---

## 演習問題

以下の問題を解いて、UIフィードバックの共通化の理解を深めましょう。

### 問題1: エラーコンテキストの作成
エラーを表示するコンテキストを作成し、エラーメッセージを表示する機能を実装してください。

<details>
<summary>解答例</summary>

```typescript
// src/contexts/ErrorContext.tsx
import { createContext, useContext, useState, ReactNode } from 'react';

interface ErrorContextType {
    showError: (message: string) => void;
    error: string | null;
}

const ErrorContext = createContext<ErrorContextType | undefined>(undefined);

export function ErrorProvider({ children }: { children: ReactNode }) {
    const [error, setError] = useState<string | null>(null);

    const showError = (message: string) => {
        setError(message);
        setTimeout(() => setError(null), 5000);
    };

    return (
        <ErrorContext.Provider value={{ showError, error }}>
            {children}
            {error && (
                <div style={{ position: 'fixed', top: 0, right: 0, padding: '16px', background: 'red', color: 'white' }}>
                    {error}
                </div>
            )}
        </ErrorContext.Provider>
    );
}

export function useError() {
    const context = useContext(ErrorContext);
    if (!context) throw new Error('useError must be used within ErrorProvider');
    return context;
}
```
</details>

### 問題2: ローディングコンテキストの作成
ローディング状態を管理するコンテキストを作成し、画面全体を覆うローディング表示を実装してください。

<details>
<summary>解答例</summary>

```typescript
// src/contexts/LoadingContext.tsx
import { createContext, useContext, useState, ReactNode } from 'react';

interface LoadingContextType {
    showLoading: () => void;
    hideLoading: () => void;
    isLoading: boolean;
}

const LoadingContext = createContext<LoadingContextType | undefined>(undefined);

export function LoadingProvider({ children }: { children: ReactNode }) {
    const [isLoading, setIsLoading] = useState(false);

    return (
        <LoadingContext.Provider value={{
            showLoading: () => setIsLoading(true),
            hideLoading: () => setIsLoading(false),
            isLoading
        }}>
            {children}
            {isLoading && (
                <div style={{
                    position: 'fixed',
                    top: 0,
                    left: 0,
                    right: 0,
                    bottom: 0,
                    background: 'rgba(0,0,0,0.5)',
                    display: 'flex',
                    justifyContent: 'center',
                    alignItems: 'center'
                }}>
                    <div>読み込み中...</div>
                </div>
            )}
        </LoadingContext.Provider>
    );
}

export function useLoading() {
    const context = useContext(LoadingContext);
    if (!context) throw new Error('useLoading must be used within LoadingProvider');
    return context;
}
```
</details>

### 問題3: APIクライアントでのエラーハンドリング
APIクライアントのインターセプターでエラーを処理し、エラーコンテキストに通知する機能を実装してください。

<details>
<summary>解答例</summary>

```typescript
// src/services/api/client.ts
import axios from 'axios';
import { errorEvent } from './events';

export const apiClient = axios.create({
    baseURL: import.meta.env.VITE_API_URL
});

apiClient.interceptors.response.use(
    response => response,
    error => {
        errorEvent.dispatchEvent(new CustomEvent('apiError', { detail: error }));
        return Promise.reject(error);
    }
);
```
</details>

### 問題4: ローディングフックの作成
非同期処理をラップして、自動的にローディングを表示するフックを作成してください。

<details>
<summary>解答例</summary>

```typescript
// src/hooks/useLoadingHandler.ts
import { useLoading } from '@/contexts/LoadingContext';
import { useCallback } from 'react';

export function useLoadingHandler() {
    const { showLoading, hideLoading } = useLoading();

    const withLoading = useCallback(async <T,>(fn: () => Promise<T>): Promise<T> => {
        try {
            showLoading();
            return await fn();
        } finally {
            hideLoading();
        }
    }, [showLoading, hideLoading]);

    return { withLoading };
}
```
</details>

### 問題5: エラーバウンダリーの実装
Reactのエラーバウンダリーを実装し、予期しないエラーをキャッチして表示してください。

<details>
<summary>解答例</summary>

```typescript
// src/components/ErrorBoundary.tsx
import { Component, ErrorInfo, ReactNode } from 'react';

interface Props {
    children: ReactNode;
}

interface State {
    hasError: boolean;
    error: Error | null;
}

export class ErrorBoundary extends Component<Props, State> {
    state: State = { hasError: false, error: null };

    static getDerivedStateFromError(error: Error): State {
        return { hasError: true, error };
    }

    componentDidCatch(error: Error, errorInfo: ErrorInfo) {
        console.error('ErrorBoundary:', error, errorInfo);
    }

    render() {
        if (this.state.hasError) {
            return (
                <div>
                    <h1>エラーが発生しました</h1>
                    <p>{this.state.error?.message}</p>
                </div>
            );
        }

        return this.props.children;
    }
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

