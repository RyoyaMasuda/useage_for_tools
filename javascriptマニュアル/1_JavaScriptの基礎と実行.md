# 1. JavaScriptの基礎と実行

この章では、JavaScriptプログラミングを始めるために必要な基礎知識を学びます。実務未経験の方でも理解できるよう、一つひとつ丁寧に説明していきます。

---

## 目次

- [基本的な用語](#基本的な用語)
- [JavaScriptとは?](#javascriptとは)
- [JavaScriptの実行方法](#javascriptの実行方法)
- [最初のプログラム - "Hello, World!"](#最初のプログラム---hello-world)
- [コードはどこに書く？ - 3つの記述方法を体験する](#コードはどこに書く---3つの記述方法を体験する)
- [index.htmlのコード](#indexhtmlのコード)
- [検証ツール（開発者ツール）](#検証ツール開発者ツール)
- [コンソールについて](#コンソールについて)
- [エラーの確認方法](#エラーの確認方法)
- [コメントとデバッグの初歩 / コメントアウトとは](#コメントとデバッグの初歩--コメントアウトとは)
- [コード記述のルール](#コード記述のルール)
- [厳格モード (`'use strict'`)](#厳格モード-usestrict)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## 基本的な用語

JavaScriptを学ぶ前に、よく使われる基本的な用語を理解しておきましょう。

### プログラミング言語
コンピュータに指示を出すための人工的な言語です。JavaScriptは、Webページを動的にするためのプログラミング言語です。

### コード（ソースコード）
プログラミング言語で書かれた命令の集まりです。人間が読める形式で書かれています。

### 実行（ランタイム）
コードをコンピュータが理解できる形式に変換して、実際に動作させることです。

### ブラウザ
Webページを表示するソフトウェアです。Chrome、Firefox、Safari、Edgeなどがあります。

### 開発者ツール（DevTools）
ブラウザに組み込まれている、開発者がコードを確認・デバッグするためのツールです。

---

## JavaScriptとは?

### JavaScriptの概要
JavaScriptは、Webページに動的な機能を追加するためのプログラミング言語です。1995年にNetscape社によって開発され、現在では世界中のWebサイトで使用されています。

### JavaScriptの特徴
- **クライアントサイド言語**: ブラウザ上で実行される
- **インタープリタ型言語**: コンパイル（事前変換）が不要で、そのまま実行できる
- **動的型付け言語**: 変数の型を事前に宣言する必要がない
- **オブジェクト指向**: オブジェクトを中心としたプログラミングが可能

### JavaScriptができること
- ボタンをクリックしたときの動作を制御
- フォームの入力内容を検証
- ページの内容を動的に変更
- サーバーとのデータ通信
- アニメーションの実装

### JavaScriptとJavaの違い
名前が似ていますが、**全く別の言語**です。
- **Java**: サーバーサイドやアプリ開発で使用されるコンパイル型言語
- **JavaScript**: ブラウザ上で動作するインタープリタ型言語

---

## JavaScriptの実行方法

JavaScriptを実行する方法は主に3つあります。

### 方法1: HTMLファイルに直接記述
HTMLファイル内に`<script>`タグを使ってJavaScriptを記述します。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>JavaScriptの実行例</title>
</head>
<body>
    <h1>JavaScriptの実行</h1>
    
    <script>
        // JavaScriptのコードをここに書く
        console.log('Hello, JavaScript!');
    </script>
</body>
</html>
```

### 方法2: 外部ファイルとして記述
JavaScriptコードを別ファイル（`.js`）に分けて、HTMLから読み込みます。

**index.html**
```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>外部JavaScriptファイル</title>
</head>
<body>
    <h1>外部ファイルからJavaScriptを読み込む</h1>
    
    <!-- 外部JavaScriptファイルを読み込む -->
    <script src="script.js"></script>
</body>
</html>
```

**script.js**
```javascript
// 外部JavaScriptファイル
console.log('外部ファイルから実行されました！');
```

### 方法3: ブラウザのコンソールで直接実行
ブラウザの開発者ツールのコンソールに直接コードを入力して実行できます。

1. ブラウザでF12キー（または右クリック→「検証」）を押す
2. 「Console」タブを開く
3. コードを入力してEnterキーを押す

```javascript
// コンソールに直接入力
console.log('コンソールから実行！');
```

---

## 最初のプログラム - "Hello, World!"

プログラミングの最初の一歩として、画面に「Hello, World!」と表示するプログラムを作成しましょう。

### HTMLファイルの作成
まず、`index.html`という名前のファイルを作成します。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello, World!</title>
</head>
<body>
    <h1>JavaScriptの最初のプログラム</h1>
    
    <script>
        // コンソールに出力
        console.log('Hello, World!');
        
        // アラートで表示
        alert('Hello, World!');
        
        // ページに表示
        document.write('<p>Hello, World!</p>');
    </script>
</body>
</html>
```

### 実行方法
1. ファイルを保存する
2. ブラウザで`index.html`を開く
3. 開発者ツールのコンソールを確認する

### コードの説明
- `console.log()`: コンソールにメッセージを出力します（開発者向け）
- `alert()`: ポップアップウィンドウでメッセージを表示します
- `document.write()`: HTMLページに直接内容を書き込みます（学習用、実務では非推奨）

---

## コードはどこに書く？ - 3つの記述方法を体験する

JavaScriptを記述する場所には、主に3つの方法があります。それぞれの特徴と使い分けを理解しましょう。

### 方法1: HTMLの`<head>`内に記述
ページが読み込まれる前に実行されます。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>head内のJavaScript</title>
    
    <script>
        // head内で実行
        console.log('head内で実行されました');
    </script>
</head>
<body>
    <h1>head内のJavaScript</h1>
</body>
</html>
```

**注意点**: HTML要素が読み込まれる前に実行されるため、DOM要素にアクセスできない場合があります。

### 方法2: HTMLの`<body>`内に記述（推奨）
HTML要素が読み込まれた後に実行されます。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>body内のJavaScript</title>
</head>
<body>
    <h1>body内のJavaScript</h1>
    <p id="message">メッセージが表示されます</p>
    
    <script>
        // body内で実行（HTML要素が読み込まれた後）
        const message = document.getElementById('message');
        message.textContent = 'JavaScriptで変更されました！';
        console.log('body内で実行されました');
    </script>
</body>
</html>
```

**推奨される方法**: HTML要素にアクセスできるため、この方法が一般的です。

### 方法3: 外部ファイルとして記述（最も推奨）
コードを別ファイルに分けることで、管理しやすくなります。

**index.html**
```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>外部JavaScriptファイル</title>
</head>
<body>
    <h1>外部JavaScriptファイル</h1>
    <button id="btn">クリックしてください</button>
    
    <!-- bodyの最後に配置するのが一般的 -->
    <script src="script.js"></script>
</body>
</html>
```

**script.js**
```javascript
// 外部JavaScriptファイル
document.addEventListener('DOMContentLoaded', function() {
    const btn = document.getElementById('btn');
    
    btn.addEventListener('click', function() {
        alert('ボタンがクリックされました！');
    });
    
    console.log('外部ファイルが読み込まれました');
});
```

### 3つの方法の比較

| 方法 | メリット | デメリット | 用途 |
|------|---------|-----------|------|
| head内 | 早期実行可能 | DOM要素にアクセスできない | 初期設定など |
| body内 | DOM要素にアクセス可能 | HTMLと混在 | 小規模なスクリプト |
| 外部ファイル | 管理しやすい、再利用可能 | ファイルが増える | **実務ではこちらが推奨** |

---

## index.htmlのコード

実際の開発で使用する、基本的な`index.html`のテンプレートを紹介します。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <!-- 文字コードの指定（必須） -->
    <meta charset="UTF-8">
    
    <!-- レスポンシブデザイン用の設定 -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    <!-- ページのタイトル -->
    <title>JavaScript学習</title>
    
    <!-- 外部CSSファイルの読み込み（必要に応じて） -->
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <!-- ページのコンテンツ -->
    <header>
        <h1>JavaScript学習サイト</h1>
    </header>
    
    <main>
        <section>
            <h2>メインコンテンツ</h2>
            <p>ここにコンテンツを記述します</p>
        </section>
    </main>
    
    <!-- JavaScriptファイルの読み込み（bodyの最後が推奨） -->
    <script src="script.js"></script>
</body>
</html>
```

### 各要素の説明

- `<!DOCTYPE html>`: HTML5であることを宣言
- `<html lang="ja">`: ページの言語を日本語に設定
- `<meta charset="UTF-8">`: 文字コードをUTF-8に設定（日本語を正しく表示するために必須）
- `<meta name="viewport">`: モバイルデバイスでの表示を最適化
- `<title>`: ブラウザのタブに表示されるタイトル
- `<script src="script.js">`: 外部JavaScriptファイルを読み込み

---

## 検証ツール（開発者ツール）

開発者ツールは、JavaScriptの開発・デバッグに欠かせないツールです。使い方を覚えましょう。

### 開発者ツールの開き方

**Windows/Linux:**
- `F12`キー
- `Ctrl + Shift + I`
- 右クリック → 「検証」

**Mac:**
- `Cmd + Option + I`
- 右クリック → 「検証」

### 主要なタブ

#### 1. Elements（要素）
HTMLの構造を確認・編集できます。

```html
<!-- Elementsタブで確認できる -->
<div id="container">
    <p>この要素を確認・編集できます</p>
</div>
```

#### 2. Console（コンソール）
JavaScriptのコードを実行したり、エラーメッセージを確認できます。

```javascript
// コンソールで実行
console.log('デバッグ情報');
console.error('エラー情報');
console.warn('警告情報');
```

#### 3. Sources（ソース）
JavaScriptファイルの内容を確認し、ブレークポイントを設定してデバッグできます。

#### 4. Network（ネットワーク）
サーバーとの通信内容を確認できます。

### 実践例

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>開発者ツールの練習</title>
</head>
<body>
    <h1>開発者ツールを開いてみましょう</h1>
    <button id="testBtn">テストボタン</button>
    
    <script>
        const btn = document.getElementById('testBtn');
        
        btn.addEventListener('click', function() {
            console.log('ボタンがクリックされました');
            console.log('現在の時刻:', new Date());
        });
        
        // ページ読み込み時にコンソールに出力
        console.log('ページが読み込まれました');
    </script>
</body>
</html>
```

**手順:**
1. 上記のHTMLファイルを作成
2. ブラウザで開く
3. F12キーで開発者ツールを開く
4. Consoleタブを確認
5. ボタンをクリックして、コンソールの出力を確認

---

## コンソールについて

コンソールは、JavaScriptの開発で最も重要なツールの一つです。詳しく学びましょう。

### console.log() - 基本的な出力
最もよく使われるメソッドです。情報をコンソールに出力します。

```javascript
// 文字列の出力
console.log('Hello, World!');

// 数値の出力
console.log(42);

// 変数の出力
const name = '太郎';
console.log(name);

// 複数の値を一度に出力
console.log('名前:', name, '年齢:', 25);
```

### その他のconsoleメソッド

#### console.error() - エラー情報の出力
```javascript
console.error('これはエラーメッセージです');
```

#### console.warn() - 警告情報の出力
```javascript
console.warn('これは警告メッセージです');
```

#### console.info() - 情報の出力
```javascript
console.info('これは情報メッセージです');
```

#### console.table() - 表形式で出力
```javascript
const users = [
    { name: '太郎', age: 25 },
    { name: '花子', age: 30 }
];
console.table(users);
```

### 実践例

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>コンソールの練習</title>
</head>
<body>
    <h1>コンソールの使い方</h1>
    
    <script>
        // 基本的な出力
        console.log('=== コンソールの練習 ===');
        
        // 変数の確認
        const userName = '山田太郎';
        const userAge = 25;
        console.log('ユーザー名:', userName);
        console.log('年齢:', userAge);
        
        // 計算結果の確認
        const sum = 10 + 20;
        console.log('10 + 20 =', sum);
        
        // 配列の確認
        const fruits = ['りんご', 'バナナ', 'オレンジ'];
        console.log('フルーツ:', fruits);
        console.table(fruits);
        
        // オブジェクトの確認
        const user = {
            name: '山田太郎',
            age: 25,
            email: 'yamada@example.com'
        };
        console.log('ユーザー情報:', user);
        console.table(user);
        
        // エラーと警告の例
        console.warn('これは警告です');
        console.error('これはエラーです（実際のエラーではありません）');
    </script>
</body>
</html>
```

### コンソールを使ったデバッグのコツ

1. **変数の値を確認する**
```javascript
let count = 0;
console.log('カウント:', count); // 値を確認
```

2. **処理の流れを追跡する**
```javascript
console.log('処理開始');
// 何らかの処理
console.log('処理中...');
// 何らかの処理
console.log('処理完了');
```

3. **条件分岐の確認**
```javascript
const age = 20;
console.log('年齢:', age);
if (age >= 20) {
    console.log('成人です');
} else {
    console.log('未成年です');
}
```

---

## エラーの確認方法

プログラミングでは、エラーは避けられません。エラーの種類と確認方法を学びましょう。

### エラーの種類

#### 1. 構文エラー（Syntax Error）
コードの書き方（構文）が間違っている場合に発生します。

```javascript
// 間違い: クォートが閉じられていない
console.log('Hello, World!); // SyntaxError

// 正しい
console.log('Hello, World!');
```

#### 2. 参照エラー（Reference Error）
存在しない変数や関数を参照しようとした場合に発生します。

```javascript
// 間違い: 変数が定義されていない
console.log(undefinedVariable); // ReferenceError

// 正しい
const variable = '値';
console.log(variable);
```

#### 3. 型エラー（Type Error）
データ型が期待されるものと異なる場合に発生します。

```javascript
// 間違い: 数値に存在しないメソッドを呼び出している
const num = 42;
num.toUpperCase(); // TypeError

// 正しい
const str = '42';
str.toUpperCase();
```

### エラーの確認方法

#### 方法1: コンソールで確認
開発者ツールのConsoleタブで、エラーメッセージが赤色で表示されます。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>エラーの確認</title>
</head>
<body>
    <h1>エラーの確認方法</h1>
    
    <script>
        // 意図的にエラーを発生させる
        console.log('エラー前の処理');
        
        // この行でエラーが発生する
        console.log(undefinedVariable); // ReferenceError
        
        // この行は実行されない（エラーで停止）
        console.log('エラー後の処理');
    </script>
</body>
</html>
```

**実行結果:**
- コンソールに赤色のエラーメッセージが表示されます
- エラーが発生した行番号が表示されます
- エラー後のコードは実行されません

#### 方法2: try-catch文でエラーを捕捉
エラーが発生してもプログラムを続行したい場合に使用します。

```javascript
try {
    // エラーが発生する可能性のあるコード
    console.log(undefinedVariable);
} catch (error) {
    // エラーが発生した場合の処理
    console.error('エラーが発生しました:', error.message);
    console.log('プログラムは続行されます');
}

console.log('エラー処理後のコード');
```

### 実践例: エラーハンドリング

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>エラーハンドリング</title>
</head>
<body>
    <h1>エラーハンドリングの練習</h1>
    <button id="errorBtn">エラーを発生させる</button>
    <button id="safeBtn">安全に実行</button>
    <div id="result"></div>
    
    <script>
        const errorBtn = document.getElementById('errorBtn');
        const safeBtn = document.getElementById('safeBtn');
        const result = document.getElementById('result');
        
        // エラーを発生させるボタン
        errorBtn.addEventListener('click', function() {
            try {
                // 意図的にエラーを発生
                console.log(undefinedVariable);
                result.textContent = '成功';
            } catch (error) {
                result.textContent = 'エラー: ' + error.message;
                console.error('エラー詳細:', error);
            }
        });
        
        // 安全に実行するボタン
        safeBtn.addEventListener('click', function() {
            try {
                const message = '正常に実行されました';
                console.log(message);
                result.textContent = message;
            } catch (error) {
                result.textContent = 'エラー: ' + error.message;
            }
        });
    </script>
</body>
</html>
```

### エラーを防ぐコツ

1. **変数を使用前に確認する**
```javascript
// 悪い例
console.log(userName); // エラーの可能性

// 良い例
if (typeof userName !== 'undefined') {
    console.log(userName);
} else {
    console.log('変数が定義されていません');
}
```

2. **コンソールを常に確認する**
開発中は、開発者ツールのコンソールを開いたままにしておきましょう。

3. **エラーメッセージを読む**
エラーメッセージには、問題の原因が書かれています。必ず確認しましょう。

---

## コメントとデバッグの初歩 / コメントアウトとは

コメントは、コードに説明を追加したり、一時的にコードを無効化するために使用します。

### コメントの種類

#### 1. 一行コメント
`//` から行末までがコメントになります。

```javascript
// これは一行コメントです
console.log('Hello'); // 行末にもコメントを書けます
```

#### 2. 複数行コメント
`/*` と `*/` で囲まれた部分がコメントになります。

```javascript
/*
これは複数行のコメントです
複数行にわたって
説明を書くことができます
*/
```

### コメントの使い方

#### 1. コードの説明を書く
```javascript
// ユーザー名を取得
const userName = '山田太郎';

// 年齢を計算（2024年基準）
const age = 2024 - 1999;
```

#### 2. コードを一時的に無効化（コメントアウト）
```javascript
// console.log('この行は実行されません');

/*
console.log('この部分も');
console.log('実行されません');
*/
```

#### 3. TODO（やるべきこと）を記録
```javascript
// TODO: エラーハンドリングを追加する
// FIXME: この部分のバグを修正する
// NOTE: この処理は後で最適化が必要
```

### 実践例: コメントの活用

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>コメントの練習</title>
</head>
<body>
    <h1>コメントの使い方</h1>
    <button id="calcBtn">計算を実行</button>
    <div id="output"></div>
    
    <script>
        // ボタンと出力要素を取得
        const calcBtn = document.getElementById('calcBtn');
        const output = document.getElementById('output');
        
        // 計算関数の定義
        function calculateSum(a, b) {
            // 2つの数値を足し算する
            return a + b;
        }
        
        // ボタンクリック時の処理
        calcBtn.addEventListener('click', function() {
            // 計算を実行
            const result = calculateSum(10, 20);
            
            // 結果を表示
            output.textContent = '結果: ' + result;
            
            // デバッグ用のログ（本番環境では削除またはコメントアウト）
            // console.log('計算結果:', result);
        });
        
        /*
        将来的な機能追加の予定:
        - 引き算機能
        - 掛け算機能
        - 割り算機能
        */
    </script>
</body>
</html>
```

### デバッグの初歩

デバッグとは、プログラムの不具合（バグ）を見つけて修正することです。

#### デバッグの基本手順

1. **問題を確認する**
```javascript
// 期待する動作: 10 + 20 = 30 と表示される
// 実際の動作: 何も表示されない

const a = 10;
const b = 20;
// console.log('aの値:', a); // デバッグ用
// console.log('bの値:', b); // デバッグ用
const result = a + b;
console.log('結果:', result);
```

2. **変数の値を確認する**
```javascript
let count = 0;
console.log('初期値:', count);

count = count + 1;
console.log('1回目の処理後:', count);

count = count + 1;
console.log('2回目の処理後:', count);
```

3. **処理の流れを追跡する**
```javascript
console.log('処理開始');

if (条件) {
    console.log('条件が真の場合');
    // 処理A
} else {
    console.log('条件が偽の場合');
    // 処理B
}

console.log('処理終了');
```

### 実践例: デバッグの練習

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>デバッグの練習</title>
</head>
<body>
    <h1>デバッグの練習</h1>
    <input type="number" id="num1" placeholder="数値1">
    <input type="number" id="num2" placeholder="数値2">
    <button id="addBtn">足し算</button>
    <div id="result"></div>
    
    <script>
        const num1Input = document.getElementById('num1');
        const num2Input = document.getElementById('num2');
        const addBtn = document.getElementById('addBtn');
        const result = document.getElementById('result');
        
        addBtn.addEventListener('click', function() {
            // デバッグ: 入力値を確認
            console.log('入力値1:', num1Input.value);
            console.log('入力値2:', num2Input.value);
            
            // 文字列として取得されるので、数値に変換
            const num1 = Number(num1Input.value);
            const num2 = Number(num2Input.value);
            
            // デバッグ: 変換後の値を確認
            console.log('変換後1:', num1);
            console.log('変換後2:', num2);
            
            // 計算
            const sum = num1 + num2;
            
            // デバッグ: 計算結果を確認
            console.log('計算結果:', sum);
            
            // 結果を表示
            result.textContent = '結果: ' + sum;
        });
    </script>
</body>
</html>
```

---

## コード記述のルール

読みやすく、保守しやすいコードを書くための基本的なルールを学びましょう。

### 1. インデント（字下げ）
コードの階層を視覚的に分かりやすくするために、スペースやタブで字下げします。

```javascript
// 悪い例: インデントがない
function greet(name){
if(name){
console.log('Hello, ' + name);
}else{
console.log('Hello, Guest');
}
}

// 良い例: 適切なインデント
function greet(name) {
    if (name) {
        console.log('Hello, ' + name);
    } else {
        console.log('Hello, Guest');
    }
}
```

### 2. セミコロン（;）
文の終わりにセミコロンを付けます（JavaScriptでは省略可能ですが、明示的に書くことが推奨されます）。

```javascript
// 推奨
const name = '太郎';
console.log(name);

// 省略可能だが、明示的に書く方が安全
const age = 25
console.log(age)
```

### 3. 命名規則
変数や関数の名前は、その役割が分かるように付けます。

```javascript
// 悪い例: 意味が分からない
const x = 10;
const n = '太郎';

// 良い例: 意味が明確
const userAge = 25;
const userName = '太郎';

// 関数名も明確に
function getUserName() {
    return userName;
}
```

### 4. コードの長さ
1行が長くなりすぎないようにします（目安: 80〜120文字）。

```javascript
// 悪い例: 長すぎる
const message = 'これは非常に長いメッセージで、1行が長くなりすぎて読みにくくなっています。';

// 良い例: 適切な長さ
const message = 'これは適切な長さのメッセージです。';
```

### 5. 空行の使い方
関連するコードをグループ化するために空行を使います。

```javascript
// 変数の定義
const userName = '太郎';
const userAge = 25;

// 関数の定義
function greet(name) {
    return 'Hello, ' + name;
}

// 実行
const message = greet(userName);
console.log(message);
```

### 実践例: 良いコードと悪いコードの比較

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>コード記述のルール</title>
</head>
<body>
    <h1>コード記述のルール</h1>
    
    <script>
        // ===== 悪い例 =====
        // インデントがない、命名が不明確、コメントがない
        function calc(a,b){
        if(a>0&&b>0){
        return a+b;
        }else{
        return 0;
        }
        }
        const r=calc(10,20);
        console.log(r);
        
        // ===== 良い例 =====
        // 適切なインデント、明確な命名、コメントがある
        function calculateSum(num1, num2) {
            // 両方の数値が正の値の場合のみ計算
            if (num1 > 0 && num2 > 0) {
                return num1 + num2;
            } else {
                return 0;
            }
        }
        
        const result = calculateSum(10, 20);
        console.log('計算結果:', result);
    </script>
</body>
</html>
```

### コーディング規約

多くのプロジェクトでは、**ESLint**というツールを使って、コードの品質を自動的にチェックします。基本的なルールを守ることで、チーム開発でも問題が起きにくくなります。

---

## 厳格モード (`'use strict'`)

厳格モードは、JavaScriptのコードをより安全に、より厳密に実行するためのモードです。

### 厳格モードとは
- より厳密なエラーチェックが行われる
- 一部の機能が制限される
- 将来のバージョンとの互換性が向上する

### 厳格モードの有効化

#### 方法1: ファイル全体で有効化
ファイルの先頭に `'use strict';` を記述します。

```javascript
'use strict';

// このファイル全体が厳格モードで実行される
const name = '太郎';
console.log(name);
```

#### 方法2: 関数内でのみ有効化
関数の先頭に `'use strict';` を記述します。

```javascript
function strictFunction() {
    'use strict';
    // この関数内のみ厳格モード
    const name = '太郎';
    return name;
}

function normalFunction() {
    // この関数は通常モード
    name = '太郎'; // エラーにならない（非推奨）
    return name;
}
```

### 厳格モードでの主な変更点

#### 1. 未宣言変数の使用がエラーになる
```javascript
'use strict';

// 通常モード: エラーにならない（非推奨）
// name = '太郎';

// 厳格モード: ReferenceError
name = '太郎'; // エラー！
```

#### 2. 予約語の使用が制限される
```javascript
'use strict';

// 通常モード: エラーにならない場合がある
// 厳格モード: エラーになる
const let = '変数'; // SyntaxError
```

#### 3. 関数内のthisがundefinedになる
```javascript
'use strict';

function test() {
    console.log(this); // undefined（通常モードではwindowオブジェクト）
}

test();
```

### 実践例: 厳格モードの比較

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>厳格モード</title>
</head>
<body>
    <h1>厳格モードの練習</h1>
    
    <script>
        // 厳格モードを有効化
        'use strict';
        
        // 1. 変数宣言なしでの代入はエラー
        // name = '太郎'; // ReferenceError
        
        // 正しい書き方
        const name = '太郎';
        console.log('名前:', name);
        
        // 2. 関数内でのthis
        function showThis() {
            console.log('thisの値:', this); // undefined
        }
        showThis();
        
        // 3. オブジェクトのメソッド内ではthisはオブジェクトを指す
        const person = {
            name: '花子',
            greet: function() {
                'use strict';
                console.log('こんにちは、' + this.name + 'です');
            }
        };
        person.greet(); // 正常に動作
        
        // 4. 削除できないプロパティの削除はエラー
        const obj = { prop: 'value' };
        // delete Object.prototype; // TypeError（厳格モード）
        
        console.log('厳格モードで正常に実行されました');
    </script>
</body>
</html>
```

### 厳格モードを使うべき場面

- **新しいプロジェクト**: 最初から厳格モードを使う
- **既存のコード**: 慎重に導入する（既存の動作が変わる可能性がある）
- **モジュール**: ES6モジュール（`import`/`export`）を使う場合は自動的に厳格モード

### 実践例: 厳格モードを使った安全なコード

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>厳格モードの実践</title>
</head>
<body>
    <h1>厳格モードで安全なコードを書く</h1>
    <button id="testBtn">テスト実行</button>
    <div id="output"></div>
    
    <script>
        'use strict';
        
        const testBtn = document.getElementById('testBtn');
        const output = document.getElementById('output');
        
        // 厳格モードでは、すべての変数を宣言する必要がある
        const userName = '山田太郎';
        const userAge = 25;
        
        function createUserInfo(name, age) {
            // すべての変数を適切に宣言
            const info = {
                name: name,
                age: age,
                isAdult: age >= 20
            };
            return info;
        }
        
        testBtn.addEventListener('click', function() {
            const userInfo = createUserInfo(userName, userAge);
            output.innerHTML = `
                <p>名前: ${userInfo.name}</p>
                <p>年齢: ${userInfo.age}</p>
                <p>成人: ${userInfo.isAdult ? 'はい' : 'いいえ'}</p>
            `;
            console.log('ユーザー情報:', userInfo);
        });
    </script>
</body>
</html>
```

---

## まとめ

この章では、JavaScriptの基礎と実行方法について学びました。

### 学んだこと
- JavaScriptとは何か、その特徴と用途
- JavaScriptの3つの実行方法（HTML内、外部ファイル、コンソール）
- 最初のプログラム「Hello, World!」の作成
- 開発者ツールの使い方
- コンソールを使ったデバッグ方法
- エラーの種類と確認方法
- コメントの書き方とデバッグの初歩
- コード記述のルール
- 厳格モード（'use strict'）の使い方

### 次のステップ
次の章では、変数・定数とデータ型について詳しく学びます。

---

## 演習問題

### 問題1: 基本的な出力
コンソールに「JavaScriptを始めました！」と表示するプログラムを作成してください。

<details>
<summary>解答例</summary>

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>演習1</title>
</head>
<body>
    <script>
        console.log('JavaScriptを始めました！');
    </script>
</body>
</html>
```
</details>

### 問題2: 外部ファイルの作成
JavaScriptコードを外部ファイル（`script.js`）に分けて、HTMLから読み込むプログラムを作成してください。

<details>
<summary>解答例</summary>

**index.html**
```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>演習2</title>
</head>
<body>
    <h1>外部ファイルの読み込み</h1>
    <script src="script.js"></script>
</body>
</html>
```

**script.js**
```javascript
console.log('外部ファイルから実行されました！');
```
</details>

### 問題3: 開発者ツールの活用
開発者ツールのコンソールを使って、以下の情報を出力してください：
- 自分の名前
- 今日の日付
- 簡単な計算結果（例: 10 × 5）

<details>
<summary>解答例</summary>

```javascript
console.log('名前: 山田太郎');
console.log('日付:', new Date());
console.log('10 × 5 =', 10 * 5);
```
</details>

### 問題4: コメントの練習
適切なコメントを追加したコードを作成してください。変数名は「userName」、値は「花子」とします。

<details>
<summary>解答例</summary>

```javascript
// ユーザー名を格納する変数
const userName = '花子';

// ユーザー名をコンソールに出力
console.log('ユーザー名:', userName);
```
</details>

### 問題5: 厳格モードの実践
厳格モードを使って、2つの数値を足し算する関数を作成してください。

<details>
<summary>解答例</summary>

```javascript
'use strict';

function addNumbers(num1, num2) {
    // 2つの数値を足し算する
    return num1 + num2;
}

const result = addNumbers(15, 25);
console.log('計算結果:', result);
```
</details>

---

お疲れ様でした！次の章に進みましょう。

