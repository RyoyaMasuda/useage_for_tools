# 7-2. モジュール (Module)

この章では、コードを複数のファイルに分割して管理する「モジュール」について学びます。モジュールを理解することで、大規模なプログラムを効率的に開発できるようになります。

---

## 目次

- [モジュール - importとexport](#モジュール---importとexport)
- [インポート・エクスポートとは](#インポートエクスポートとは)
- [エクスポート](#エクスポート)
- [インポート](#インポート)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## モジュール - importとexport

### モジュールとは
モジュールは、**コードを複数のファイルに分割して管理する**仕組みです。

### モジュールを使う理由
1. **コードの整理**: 関連するコードを1つのファイルにまとめられる
2. **再利用性**: 他のファイルで同じコードを使い回せる
3. **保守性**: 修正箇所が明確になり、管理しやすくなる
4. **名前の衝突を防ぐ**: 各モジュールで独立したスコープを持つ

### importとexport
- **export**: 他のファイルで使えるように公開する
- **import**: 他のファイルから読み込む

### 実践例: モジュールの基本概念

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>モジュールの基本概念</title>
</head>
<body>
    <h1>モジュール - importとexport</h1>
    <div id="output"></div>
    
    <script type="module">
        // モジュールを使わない場合（すべて1つのファイル）
        const output = document.getElementById('output');
        let results = [];
        
        results.push('<h2>モジュールを使わない場合</h2>');
        results.push('<p>すべてのコードが1つのファイルに書かれる</p>');
        results.push('<p>• ファイルが長くなる</p>');
        results.push('<p>• 管理が大変</p>');
        results.push('<p>• 再利用が難しい</p>');
        
        // モジュールを使う場合
        results.push('<h2>モジュールを使う場合</h2>');
        results.push('<p>コードを複数のファイルに分割</p>');
        results.push('<p>• ファイルが整理される</p>');
        results.push('<p>• 管理しやすい</p>');
        results.push('<p>• 再利用が容易</p>');
        
        // モジュールの基本
        results.push('<h2>モジュールの基本</h2>');
        results.push('<p>• export: 他のファイルで使えるように公開</p>');
        results.push('<p>• import: 他のファイルから読み込む</p>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== モジュール ===');
        console.log('モジュールでコードを複数のファイルに分割できます');
    </script>
</body>
</html>
```

---

## インポート・エクスポートとは

### エクスポート（export）とは
エクスポートは、**他のファイルで使えるように変数や関数を公開する**ことです。

### インポート（import）とは
インポートは、**他のファイルから変数や関数を読み込む**ことです。

### 基本的な流れ

```javascript
// math.js（エクスポート側）
export function add(a, b) {
    return a + b;
}

// main.js（インポート側）
import { add } from './math.js';
console.log(add(10, 5)); // 15
```

### モジュールの特徴
1. **独立したスコープ**: 各モジュールは独立したスコープを持つ
2. **厳格モード**: 自動的に厳格モードで実行される
3. **型: "module"**: HTMLで`<script type="module">`を指定する必要がある

### 実践例: インポート・エクスポートとは

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>インポート・エクスポートとは</title>
</head>
<body>
    <h1>インポート・エクスポートとは</h1>
    <div id="output"></div>
    
    <script type="module">
        const output = document.getElementById('output');
        let results = [];
        
        // エクスポートの概念
        results.push('<h2>エクスポート（export）とは</h2>');
        results.push('<p>他のファイルで使えるように変数や関数を公開すること</p>');
        results.push('<p>例: export function add(a, b) { return a + b; }</p>');
        
        // インポートの概念
        results.push('<h2>インポート（import）とは</h2>');
        results.push('<p>他のファイルから変数や関数を読み込むこと</p>');
        results.push('<p>例: import { add } from "./math.js";</p>');
        
        // 基本的な流れ
        results.push('<h2>基本的な流れ</h2>');
        results.push('<p>1. エクスポート側（math.js）で関数を公開</p>');
        results.push('<p>2. インポート側（main.js）で関数を読み込む</p>');
        results.push('<p>3. 読み込んだ関数を使用</p>');
        
        // モジュールの特徴
        results.push('<h2>モジュールの特徴</h2>');
        results.push('<p>• 独立したスコープ: 各モジュールは独立したスコープを持つ</p>');
        results.push('<p>• 厳格モード: 自動的に厳格モードで実行される</p>');
        results.push('<p>• type="module": HTMLで&lt;script type="module"&gt;を指定</p>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== インポート・エクスポートとは ===');
        console.log('exportで公開、importで読み込みます');
    </script>
</body>
</html>
```

---

## エクスポート

エクスポートには、いくつかの方法があります。

### 名前付きエクスポート（Named Export）

#### 方法1: 宣言時にエクスポート
```javascript
// math.js
export function add(a, b) {
    return a + b;
}

export const PI = 3.14159;
```

#### 方法2: 後からエクスポート
```javascript
// math.js
function add(a, b) {
    return a + b;
}

const PI = 3.14159;

export { add, PI };
```

### デフォルトエクスポート（Default Export）

```javascript
// calculator.js
export default class Calculator {
    add(a, b) {
        return a + b;
    }
}
```

### 名前付きとデフォルトの違い

| 項目 | 名前付きエクスポート | デフォルトエクスポート |
|------|-------------------|---------------------|
| **数** | 複数可能 | 1つのみ |
| **インポート** | `import { name }` | `import name` |
| **名前の変更** | 可能（as） | 可能 |

### 実践例: エクスポート

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>エクスポート</title>
</head>
<body>
    <h1>エクスポート</h1>
    <div id="output"></div>
    
    <script type="module">
        const output = document.getElementById('output');
        let results = [];
        
        // 名前付きエクスポート（宣言時にエクスポート）
        results.push('<h2>名前付きエクスポート（宣言時にエクスポート）</h2>');
        results.push('<p>export function add(a, b) { return a + b; }</p>');
        results.push('<p>export const PI = 3.14159;</p>');
        
        // 名前付きエクスポート（後からエクスポート）
        results.push('<h2>名前付きエクスポート（後からエクスポート）</h2>');
        results.push('<p>function add(a, b) { return a + b; }</p>');
        results.push('<p>const PI = 3.14159;</p>');
        results.push('<p>export { add, PI };</p>');
        
        // デフォルトエクスポート
        results.push('<h2>デフォルトエクスポート</h2>');
        results.push('<p>export default class Calculator { ... }</p>');
        results.push('<p>1つのファイルに1つだけ</p>');
        
        // 実践例: 実際のエクスポート（インラインで説明）
        results.push('<h2>実践例: エクスポートのパターン</h2>');
        
        // このHTML内では実際のエクスポートはできないが、説明として表示
        results.push('<p><strong>math.js（名前付きエクスポート）:</strong></p>');
        results.push('<pre>export function add(a, b) { return a + b; }<br>export function subtract(a, b) { return a - b; }<br>export const PI = 3.14159;</pre>');
        
        results.push('<p><strong>calculator.js（デフォルトエクスポート）:</strong></p>');
        results.push('<pre>export default class Calculator {<br>  add(a, b) { return a + b; }<br>}</pre>');
        
        // エクスポートの比較
        results.push('<h2>エクスポートの比較</h2>');
        results.push('<table border="1" style="border-collapse: collapse;">');
        results.push('<tr><th>項目</th><th>名前付きエクスポート</th><th>デフォルトエクスポート</th></tr>');
        results.push('<tr><td>数</td><td>複数可能</td><td>1つのみ</td></tr>');
        results.push('<tr><td>インポート</td><td>import { name }</td><td>import name</td></tr>');
        results.push('<tr><td>名前の変更</td><td>可能（as）</td><td>可能</td></tr>');
        results.push('</table>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== エクスポート ===');
        console.log('名前付きエクスポートとデフォルトエクスポートがあります');
        console.log('名前付きエクスポートは複数可能、デフォルトエクスポートは1つだけ');
    </script>
</body>
</html>
```

---

## インポート

インポートには、いくつかの方法があります。

### 名前付きインポート

```javascript
// math.jsから名前付きエクスポートをインポート
import { add, subtract, PI } from './math.js';

console.log(add(10, 5));      // 15
console.log(subtract(10, 5)); // 5
console.log(PI);              // 3.14159
```

### デフォルトインポート

```javascript
// calculator.jsからデフォルトエクスポートをインポート
import Calculator from './calculator.js';

const calc = new Calculator();
```

### 名前の変更（as）

```javascript
// インポート時に名前を変更
import { add as addNumbers } from './math.js';
import Calculator as Calc from './calculator.js';
```

### すべてをインポート（*）

```javascript
// すべての名前付きエクスポートをインポート
import * as MathUtils from './math.js';

console.log(MathUtils.add(10, 5));
console.log(MathUtils.PI);
```

### 実践例: インポート

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>インポート</title>
</head>
<body>
    <h1>インポート</h1>
    <div id="output"></div>
    
    <script type="module">
        const output = document.getElementById('output');
        let results = [];
        
        // 名前付きインポート
        results.push('<h2>名前付きインポート</h2>');
        results.push('<p>import { add, subtract, PI } from "./math.js";</p>');
        results.push('<p>• 名前付きエクスポートをインポート</p>');
        results.push('<p>• 必要なものだけを選択してインポート</p>');
        
        // デフォルトインポート
        results.push('<h2>デフォルトインポート</h2>');
        results.push('<p>import Calculator from "./calculator.js";</p>');
        results.push('<p>• デフォルトエクスポートをインポート</p>');
        results.push('<p>• 名前を自由に付けられる</p>');
        
        // 名前の変更（as）
        results.push('<h2>名前の変更（as）</h2>');
        results.push('<p>import { add as addNumbers } from "./math.js";</p>');
        results.push('<p>import Calculator as Calc from "./calculator.js";</p>');
        results.push('<p>• インポート時に名前を変更できる</p>');
        
        // すべてをインポート（*）
        results.push('<h2>すべてをインポート（*）</h2>');
        results.push('<p>import * as MathUtils from "./math.js";</p>');
        results.push('<p>• すべての名前付きエクスポートを1つのオブジェクトとしてインポート</p>');
        results.push('<p>• MathUtils.add()のようにアクセス</p>');
        
        // 実践例: インポートのパターン
        results.push('<h2>実践例: インポートのパターン</h2>');
        
        results.push('<p><strong>パターン1: 名前付きインポート</strong></p>');
        results.push('<pre>import { add, subtract } from "./math.js";<br>console.log(add(10, 5));</pre>');
        
        results.push('<p><strong>パターン2: デフォルトインポート</strong></p>');
        results.push('<pre>import Calculator from "./calculator.js";<br>const calc = new Calculator();</pre>');
        
        results.push('<p><strong>パターン3: 名前の変更</strong></p>');
        results.push('<pre>import { add as sum } from "./math.js";<br>console.log(sum(10, 5));</pre>');
        
        results.push('<p><strong>パターン4: すべてをインポート</strong></p>');
        results.push('<pre>import * as Math from "./math.js";<br>console.log(Math.add(10, 5));</pre>');
        
        // インポートの注意点
        results.push('<h2>インポートの注意点</h2>');
        results.push('<p>• ファイルパスは相対パスまたは絶対パス</p>');
        results.push('<p>• .js拡張子を付ける（推奨）</p>');
        results.push('<p>• HTMLでtype="module"を指定</p>');
        results.push('<p>• モジュールは自動的に厳格モードで実行される</p>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== インポート ===');
        console.log('名前付きインポートとデフォルトインポートがあります');
        console.log('必要なものだけを選択してインポートできます');
    </script>
</body>
</html>
```

---

## まとめ

この章では、モジュールについて学びました。

### 学んだこと
- モジュールの概念（importとexport）
- インポート・エクスポートとは
- エクスポート（名前付き、デフォルト）
- インポート（名前付き、デフォルト、名前の変更、すべてをインポート）

### 重要なポイント
1. **exportで公開**: 他のファイルで使えるように公開
2. **importで読み込み**: 他のファイルから読み込む
3. **名前付きエクスポート**: 複数可能、`import { name }`
4. **デフォルトエクスポート**: 1つだけ、`import name`
5. **type="module"**: HTMLで指定が必要

### 次のステップ
次の章では、ブラウザ操作とDOM・イベントについて詳しく学びます。

---

## 演習問題

### 問題1: 名前付きエクスポート
`math.js`ファイルを作成し、`add`関数を名前付きエクスポートしてください。

<details>
<summary>解答例</summary>

**math.js**
```javascript
export function add(a, b) {
    return a + b;
}
```

**main.js**
```javascript
import { add } from './math.js';
console.log(add(10, 5)); // 15
```
</details>

### 問題2: デフォルトエクスポート
`calculator.js`ファイルを作成し、`Calculator`クラスをデフォルトエクスポートしてください。

<details>
<summary>解答例</summary>

**calculator.js**
```javascript
export default class Calculator {
    add(a, b) {
        return a + b;
    }
}
```

**main.js**
```javascript
import Calculator from './calculator.js';
const calc = new Calculator();
console.log(calc.add(10, 5)); // 15
```
</details>

### 問題3: 複数の名前付きエクスポート
`utils.js`ファイルを作成し、`add`と`subtract`関数を名前付きエクスポートしてください。

<details>
<summary>解答例</summary>

**utils.js**
```javascript
export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}
```

**main.js**
```javascript
import { add, subtract } from './utils.js';
console.log(add(10, 5));      // 15
console.log(subtract(10, 5)); // 5
```
</details>

### 問題4: 名前の変更
`math.js`から`add`関数をインポートし、`sum`という名前に変更してください。

<details>
<summary>解答例</summary>

```javascript
import { add as sum } from './math.js';
console.log(sum(10, 5)); // 15
```
</details>

### 問題5: すべてをインポート
`math.js`からすべての名前付きエクスポートを`MathUtils`という名前でインポートしてください。

<details>
<summary>解答例</summary>

**math.js**
```javascript
export function add(a, b) {
    return a + b;
}

export const PI = 3.14159;
```

**main.js**
```javascript
import * as MathUtils from './math.js';
console.log(MathUtils.add(10, 5)); // 15
console.log(MathUtils.PI);         // 3.14159
```
</details>

---

お疲れ様でした！次の章に進みましょう。



