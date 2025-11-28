# 3-2. DOM参照と値の保持 (useRef)

この章では、Reactの`useRef`フックについて学びます。`useRef`を使うことで、DOM要素に直接アクセスしたり、再レンダリングを起こさずに値を保持したりできるようになります。`useRef`を理解することで、より高度なReactコンポーネントを作成できるようになります。

---

## 目次

- [DOM要素へのアクセス](#dom要素へのアクセス)
- [再レンダリングを起こさない変数の保持](#再レンダリングを起こさない変数の保持)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## DOM要素へのアクセス

### useRefとは
`useRef`は、**DOM要素への参照を保持する**ためのReactフックです。通常の変数とは異なり、再レンダリングされても値が保持されます。

### 日常生活での例
- **名札**: 特定の人（DOM要素）を指し示す名札（ref）
- **住所**: 特定の場所（DOM要素）を特定する住所（ref）
- **参照番号**: 特定の商品（DOM要素）を識別する参照番号（ref）

### useRefの基本構文

```jsx
import { useRef } from 'react';

function Component() {
    const ref = useRef(初期値);
    
    return <div ref={ref}>要素</div>;
}
```

### DOM要素への参照

`useRef`を使ってDOM要素にアクセスするには、JSX要素の`ref`属性にrefオブジェクトを渡します。

```jsx
import { useRef } from 'react';

function App() {
    const inputRef = useRef(null);
    
    const handleClick = () => {
        // ref.currentでDOM要素にアクセス
        inputRef.current.focus();
    };
    
    return (
        <div>
            <input ref={inputRef} type="text" />
            <button onClick={handleClick}>フォーカス</button>
        </div>
    );
}
```

### refオブジェクトの特徴

1. **currentプロパティ**: `ref.current`で実際のDOM要素や値にアクセス
2. **再レンダリングされない**: refの値が変わっても再レンダリングが発生しない
3. **null初期化**: DOM要素のrefは通常`null`で初期化

### 実践例: DOM要素へのアクセス

```jsx
// src/App.jsx
import React, { useRef } from 'react';

function App() {
    // 例1: 入力フィールドへのフォーカス
    const inputRef = useRef(null);
    const textareaRef = useRef(null);
    
    const focusInput = () => {
        inputRef.current?.focus();
    };
    
    const focusTextarea = () => {
        textareaRef.current?.focus();
    };
    
    // 例2: スクロール位置の制御
    const scrollRef = useRef(null);
    
    const scrollToTop = () => {
        scrollRef.current?.scrollIntoView({ behavior: 'smooth' });
    };
    
    const scrollToBottom = () => {
        if (scrollRef.current) {
            scrollRef.current.scrollTop = scrollRef.current.scrollHeight;
        }
    };
    
    // 例3: 要素のサイズや位置の取得
    const boxRef = useRef(null);
    
    const getBoxInfo = () => {
        if (boxRef.current) {
            const rect = boxRef.current.getBoundingClientRect();
            console.log('幅:', rect.width);
            console.log('高さ:', rect.height);
            console.log('X座標:', rect.x);
            console.log('Y座標:', rect.y);
        }
    };
    
    // 例4: 動画や音声の制御
    const videoRef = useRef(null);
    
    const playVideo = () => {
        videoRef.current?.play();
    };
    
    const pauseVideo = () => {
        videoRef.current?.pause();
    };
    
    // 例5: カンバス要素への描画
    const canvasRef = useRef(null);
    
    const drawOnCanvas = () => {
        const canvas = canvasRef.current;
        if (canvas) {
            const ctx = canvas.getContext('2d');
            ctx.fillStyle = 'blue';
            ctx.fillRect(10, 10, 100, 100);
        }
    };
    
    return (
        <div className="app">
            <h1>useRefによるDOM要素へのアクセス</h1>
            
            {/* 例1: 入力フィールドへのフォーカス */}
            <section>
                <h2>例1: 入力フィールドへのフォーカス</h2>
                <input 
                    ref={inputRef} 
                    type="text" 
                    placeholder="テキスト入力"
                />
                <button onClick={focusInput}>入力欄にフォーカス</button>
                
                <textarea 
                    ref={textareaRef}
                    placeholder="複数行入力"
                    rows={4}
                />
                <button onClick={focusTextarea}>テキストエリアにフォーカス</button>
            </section>
            
            {/* 例2: スクロール位置の制御 */}
            <section>
                <h2>例2: スクロール位置の制御</h2>
                <div 
                    ref={scrollRef}
                    style={{
                        height: '200px',
                        overflow: 'auto',
                        border: '1px solid #ccc',
                        padding: '10px'
                    }}
                >
                    <div style={{ height: '500px', background: 'linear-gradient(to bottom, #f0f0f0, #e0e0e0)' }}>
                        <p>スクロール可能なコンテンツ</p>
                        <p>下にスクロールしてください</p>
                        <p>...</p>
                        <p>最後のコンテンツ</p>
                    </div>
                </div>
                <button onClick={scrollToTop}>上にスクロール</button>
                <button onClick={scrollToBottom}>下にスクロール</button>
            </section>
            
            {/* 例3: 要素のサイズや位置の取得 */}
            <section>
                <h2>例3: 要素のサイズや位置の取得</h2>
                <div
                    ref={boxRef}
                    style={{
                        width: '200px',
                        height: '100px',
                        backgroundColor: 'lightblue',
                        padding: '20px',
                        margin: '20px'
                    }}
                >
                    測定対象の要素
                </div>
                <button onClick={getBoxInfo}>要素情報を取得（コンソールを確認）</button>
            </section>
            
            {/* 例4: 動画の制御 */}
            <section>
                <h2>例4: 動画の制御</h2>
                <video
                    ref={videoRef}
                    width="400"
                    controls
                    src="https://sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4"
                >
                    お使いのブラウザは動画タグをサポートしていません。
                </video>
                <div>
                    <button onClick={playVideo}>再生</button>
                    <button onClick={pauseVideo}>一時停止</button>
                </div>
            </section>
            
            {/* 例5: カンバスへの描画 */}
            <section>
                <h2>例5: カンバスへの描画</h2>
                <canvas
                    ref={canvasRef}
                    width="200"
                    height="200"
                    style={{ border: '1px solid #000' }}
                />
                <button onClick={drawOnCanvas}>描画</button>
            </section>
        </div>
    );
}

export default App;
```

### 複数のrefの使用

複数のDOM要素にアクセスする場合、それぞれに別々のrefを作成します。

```jsx
function Form() {
    const nameRef = useRef(null);
    const emailRef = useRef(null);
    const messageRef = useRef(null);
    
    const focusNext = (currentRef, nextRef) => {
        if (currentRef.current?.value === '') {
            currentRef.current?.focus();
        } else {
            nextRef.current?.focus();
        }
    };
    
    return (
        <form>
            <input 
                ref={nameRef}
                type="text"
                placeholder="名前"
                onKeyDown={(e) => {
                    if (e.key === 'Enter') {
                        focusNext(nameRef, emailRef);
                    }
                }}
            />
            <input 
                ref={emailRef}
                type="email"
                placeholder="メール"
                onKeyDown={(e) => {
                    if (e.key === 'Enter') {
                        focusNext(emailRef, messageRef);
                    }
                }}
            />
            <textarea 
                ref={messageRef}
                placeholder="メッセージ"
            />
        </form>
    );
}
```

### 注意点: nullチェック

DOM要素にアクセスする際は、**必ずnullチェックを行う**ことが重要です。

```jsx
// ✅ 安全: オプショナルチェーンを使用
inputRef.current?.focus();

// ✅ 安全: if文でチェック
if (inputRef.current) {
    inputRef.current.focus();
}

// ⚠️ 危険: nullチェックなし（エラーになる可能性）
inputRef.current.focus(); // currentがnullの場合エラー
```

---

## 再レンダリングを起こさない変数の保持

### useRefのもう一つの用途

`useRef`は、DOM要素への参照だけでなく、**再レンダリングを起こさずに値を保持する**ためにも使えます。

### useStateとの違い

| 特徴 | useState | useRef |
|------|----------|--------|
| 再レンダリング | 値が変わると再レンダリング | 値が変わっても再レンダリングされない |
| 値の更新 | `setState`で更新 | `ref.current`で直接更新 |
| 用途 | UIに表示する値 | UIに表示しない値、前回の値を保持 |

### 基本的な使い方

```jsx
import { useRef } from 'react';

function Component() {
    const countRef = useRef(0);
    
    const increment = () => {
        countRef.current += 1;
        console.log(countRef.current); // 値は更新されるが、再レンダリングは発生しない
    };
    
    return <button onClick={increment}>カウント: {countRef.current}</button>;
}
```

### 実践例: 再レンダリングを起こさない変数の保持

```jsx
// src/App.jsx
import React, { useState, useRef, useEffect } from 'react';

function App() {
    // 例1: 前回の値を保持
    const [count, setCount] = useState(0);
    const prevCountRef = useRef();
    
    useEffect(() => {
        prevCountRef.current = count;
    });
    
    const prevCount = prevCountRef.current;
    
    // 例2: タイマーIDの保持
    const [seconds, setSeconds] = useState(0);
    const timerIdRef = useRef(null);
    
    const startTimer = () => {
        if (timerIdRef.current === null) {
            timerIdRef.current = setInterval(() => {
                setSeconds(prev => prev + 1);
            }, 1000);
        }
    };
    
    const stopTimer = () => {
        if (timerIdRef.current !== null) {
            clearInterval(timerIdRef.current);
            timerIdRef.current = null;
        }
    };
    
    // 例3: レンダリング回数のカウント
    const renderCountRef = useRef(0);
    renderCountRef.current += 1;
    
    // 例4: フォームの送信回数の制限
    const submitCountRef = useRef(0);
    const [message, setMessage] = useState('');
    
    const handleSubmit = (e) => {
        e.preventDefault();
        submitCountRef.current += 1;
        
        if (submitCountRef.current > 3) {
            setMessage('送信回数の上限に達しました');
            return;
        }
        
        setMessage(`送信回数: ${submitCountRef.current}`);
    };
    
    // 例5: アニメーションフレームIDの保持
    const [position, setPosition] = useState(0);
    const animationFrameRef = useRef(null);
    
    const startAnimation = () => {
        let startTime = null;
        
        const animate = (timestamp) => {
            if (startTime === null) {
                startTime = timestamp;
            }
            
            const elapsed = timestamp - startTime;
            const newPosition = (elapsed / 10) % 200;
            setPosition(newPosition);
            
            animationFrameRef.current = requestAnimationFrame(animate);
        };
        
        animationFrameRef.current = requestAnimationFrame(animate);
    };
    
    const stopAnimation = () => {
        if (animationFrameRef.current !== null) {
            cancelAnimationFrame(animationFrameRef.current);
            animationFrameRef.current = null;
        }
    };
    
    // 例6: 前回のpropsやstateとの比較
    const [value, setValue] = useState('');
    const prevValueRef = useRef('');
    
    useEffect(() => {
        if (prevValueRef.current !== value) {
            console.log('値が変更されました:', prevValueRef.current, '→', value);
            prevValueRef.current = value;
        }
    }, [value]);
    
    return (
        <div className="app">
            <h1>useRefによる値の保持</h1>
            
            {/* 例1: 前回の値を保持 */}
            <section>
                <h2>例1: 前回の値を保持</h2>
                <p>現在の値: {count}</p>
                <p>前回の値: {prevCount ?? 'なし'}</p>
                <button onClick={() => setCount(count + 1)}>カウントアップ</button>
            </section>
            
            {/* 例2: タイマーIDの保持 */}
            <section>
                <h2>例2: タイマーIDの保持</h2>
                <p>経過時間: {seconds}秒</p>
                <button onClick={startTimer}>開始</button>
                <button onClick={stopTimer}>停止</button>
            </section>
            
            {/* 例3: レンダリング回数のカウント */}
            <section>
                <h2>例3: レンダリング回数（再レンダリングされない）</h2>
                <p>レンダリング回数: {renderCountRef.current}</p>
                <p>※この値は再レンダリングされても表示が更新されません</p>
            </section>
            
            {/* 例4: フォームの送信回数の制限 */}
            <section>
                <h2>例4: フォームの送信回数の制限</h2>
                <form onSubmit={handleSubmit}>
                    <input type="text" placeholder="名前" />
                    <button type="submit">送信</button>
                </form>
                <p>{message}</p>
            </section>
            
            {/* 例5: アニメーション */}
            <section>
                <h2>例5: アニメーションフレームIDの保持</h2>
                <div
                    style={{
                        width: '50px',
                        height: '50px',
                        backgroundColor: 'blue',
                        transform: `translateX(${position}px)`,
                        transition: 'transform 0.1s'
                    }}
                />
                <button onClick={startAnimation}>アニメーション開始</button>
                <button onClick={stopAnimation}>アニメーション停止</button>
            </section>
            
            {/* 例6: 前回の値との比較 */}
            <section>
                <h2>例6: 前回の値との比較</h2>
                <input
                    type="text"
                    value={value}
                    onChange={(e) => setValue(e.target.value)}
                    placeholder="値を入力（コンソールを確認）"
                />
                <p>現在の値: {value}</p>
            </section>
        </div>
    );
}

export default App;
```

### useRefとuseStateの使い分け

#### useStateを使う場合
- UIに表示する値
- 値が変わったときに再レンダリングが必要な場合
- 値の変更を追跡したい場合

```jsx
const [count, setCount] = useState(0); // UIに表示する値
```

#### useRefを使う場合
- UIに表示しない値
- 再レンダリングを起こしたくない値
- タイマーID、アニメーションフレームIDなどの識別子
- 前回の値を保持したい場合

```jsx
const timerIdRef = useRef(null); // UIに表示しない値
```

### 実践例: カスタムフックでのuseRef

```jsx
// カスタムフック: 前回の値を返す
function usePrevious(value) {
    const ref = useRef();
    
    useEffect(() => {
        ref.current = value;
    });
    
    return ref.current;
}

// 使用例
function Component() {
    const [count, setCount] = useState(0);
    const prevCount = usePrevious(count);
    
    return (
        <div>
            <p>現在: {count}</p>
            <p>前回: {prevCount ?? 'なし'}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}
```

### 注意点: useRefの更新タイミング

`useRef`の値は**同期的に更新**されますが、**再レンダリングは発生しません**。

```jsx
function Component() {
    const countRef = useRef(0);
    
    const handleClick = () => {
        countRef.current += 1;
        console.log(countRef.current); // 即座に更新される
        
        // ただし、画面には反映されない（再レンダリングされない）
    };
    
    return (
        <div>
            <p>カウント: {countRef.current}</p> {/* 再レンダリングされない限り更新されない */}
            <button onClick={handleClick}>カウントアップ</button>
        </div>
    );
}
```

---

## まとめ

この章では、Reactの`useRef`フックについて学びました。

### 学んだこと
- **DOM要素へのアクセス**: `ref`属性を使ってDOM要素に直接アクセス
- **値の保持**: 再レンダリングを起こさずに値を保持
- **useStateとの違い**: 再レンダリングの有無による使い分け
- **実践的な使用例**: フォーカス制御、スクロール制御、タイマーIDの保持など

### 重要なポイント
1. **ref属性**: JSX要素の`ref`属性にrefオブジェクトを渡す
2. **currentプロパティ**: `ref.current`でDOM要素や値にアクセス
3. **nullチェック**: DOM要素にアクセスする際は必ずnullチェック
4. **再レンダリング**: useRefの値が変わっても再レンダリングされない
5. **使い分け**: UIに表示する値はuseState、表示しない値はuseRef

### 使い分けの目安
- **useState**: UIに表示する値、値の変更で再レンダリングが必要な場合
- **useRef**: DOM要素への参照、再レンダリングを起こしたくない値、タイマーIDなどの識別子

### 次のステップ
`useRef`を理解することで、DOM操作やパフォーマンス最適化ができるようになります。次の章では、フォーム実装とバリデーションについて詳しく学びます。

---

## 演習問題

### 問題1: 入力フィールドへのフォーカス
`useRef`を使って、ボタンをクリックしたときに入力フィールドにフォーカスを当てるコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```jsx
import { useRef } from 'react';

function App() {
    const inputRef = useRef(null);
    
    const handleClick = () => {
        inputRef.current?.focus();
    };
    
    return (
        <div>
            <input ref={inputRef} type="text" />
            <button onClick={handleClick}>フォーカス</button>
        </div>
    );
}
```
</details>

### 問題2: スクロール位置の制御
`useRef`を使って、ボタンをクリックしたときに特定の要素までスクロールするコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```jsx
import { useRef } from 'react';

function App() {
    const targetRef = useRef(null);
    
    const scrollToTarget = () => {
        targetRef.current?.scrollIntoView({ behavior: 'smooth' });
    };
    
    return (
        <div>
            <button onClick={scrollToTarget}>スクロール</button>
            <div style={{ height: '1000px' }}>長いコンテンツ</div>
            <div ref={targetRef} style={{ height: '100px', backgroundColor: 'yellow' }}>
                ターゲット要素
            </div>
        </div>
    );
}
```
</details>

### 問題3: 前回の値を保持
`useRef`を使って、前回のカウント値を保持し、表示するコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```jsx
import { useState, useRef, useEffect } from 'react';

function App() {
    const [count, setCount] = useState(0);
    const prevCountRef = useRef();
    
    useEffect(() => {
        prevCountRef.current = count;
    });
    
    const prevCount = prevCountRef.current;
    
    return (
        <div>
            <p>現在の値: {count}</p>
            <p>前回の値: {prevCount ?? 'なし'}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}
```
</details>

### 問題4: タイマーの実装
`useRef`を使って、タイマーIDを保持し、開始・停止ができるタイマーコンポーネントを作成してください。

<details>
<summary>解答例</summary>

```jsx
import { useState, useRef } from 'react';

function App() {
    const [seconds, setSeconds] = useState(0);
    const timerIdRef = useRef(null);
    
    const startTimer = () => {
        if (timerIdRef.current === null) {
            timerIdRef.current = setInterval(() => {
                setSeconds(prev => prev + 1);
            }, 1000);
        }
    };
    
    const stopTimer = () => {
        if (timerIdRef.current !== null) {
            clearInterval(timerIdRef.current);
            timerIdRef.current = null;
        }
    };
    
    return (
        <div>
            <p>経過時間: {seconds}秒</p>
            <button onClick={startTimer}>開始</button>
            <button onClick={stopTimer}>停止</button>
        </div>
    );
}
```
</details>

### 問題5: レンダリング回数のカウント
`useRef`を使って、コンポーネントのレンダリング回数をカウントするコンポーネントを作成してください（再レンダリングを起こさない方法で）。

<details>
<summary>解答例</summary>

```jsx
import { useState, useRef } from 'react';

function App() {
    const [count, setCount] = useState(0);
    const renderCountRef = useRef(0);
    
    renderCountRef.current += 1;
    
    return (
        <div>
            <p>カウント: {count}</p>
            <p>レンダリング回数: {renderCountRef.current}</p>
            <button onClick={() => setCount(count + 1)}>カウントアップ</button>
        </div>
    );
}
```
</details>

---

お疲れ様でした！次の章に進みましょう。

