# 8-2. イベント (Event)

この章では、ユーザーの操作（クリック、入力など）に反応する「イベント」について学びます。イベントを理解することで、インタラクティブなWebページを作成できるようになります。

---

## 目次

- [イベントとは](#イベントとは)
- [イベントハンドラとイベントリスナ](#イベントハンドラとイベントリスナ)
- [イベントリスナの登録方法](#イベントリスナの登録方法)
- [イベントリスナを複数登録する方法](#イベントリスナを複数登録する方法)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## イベントとは

### イベントの概念
イベント（Event）は、**ユーザーの操作やブラウザの動作**を表すものです。

### 主なイベントの種類
1. **マウスイベント**: `click`, `mouseover`, `mouseout`, `mousedown`, `mouseup`
2. **キーボードイベント**: `keydown`, `keyup`, `keypress`
3. **フォームイベント**: `submit`, `change`, `input`, `focus`, `blur`
4. **ウィンドウイベント**: `load`, `resize`, `scroll`

### イベントの例

```javascript
// ボタンがクリックされたとき
button.addEventListener('click', function() {
    console.log('ボタンがクリックされました');
});

// キーが押されたとき
input.addEventListener('keydown', function() {
    console.log('キーが押されました');
});
```

### 実践例: イベントとは

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>イベントとは</title>
</head>
<body>
    <h1>イベントとは</h1>
    <button id="clickBtn">クリックしてください</button>
    <input type="text" id="textInput" placeholder="入力してください">
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // クリックイベント
        const clickBtn = document.getElementById('clickBtn');
        clickBtn.addEventListener('click', function() {
            results.push('<p>✓ ボタンがクリックされました</p>');
            output.innerHTML = results.join('');
        });
        
        // キーボードイベント
        const textInput = document.getElementById('textInput');
        textInput.addEventListener('keydown', function() {
            results.push('<p>✓ キーが押されました</p>');
            output.innerHTML = results.join('');
        });
        
        // イベントの種類
        results.push('<h2>主なイベントの種類</h2>');
        results.push('<p>• マウスイベント: click, mouseover, mouseout</p>');
        results.push('<p>• キーボードイベント: keydown, keyup, keypress</p>');
        results.push('<p>• フォームイベント: submit, change, input</p>');
        results.push('<p>• ウィンドウイベント: load, resize, scroll</p>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== イベントとは ===');
        console.log('イベントはユーザーの操作やブラウザの動作を表します');
        console.log('イベントリスナでイベントに反応できます');
    </script>
</body>
</html>
```

---

## イベントハンドラとイベントリスナ

### イベントハンドラとは
イベントハンドラは、**イベントが発生したときに実行される関数**です。

### イベントリスナとは
イベントリスナは、**イベントを監視し、発生したときに処理を実行する仕組み**です。

### イベントハンドラとイベントリスナの違い

| 項目 | イベントハンドラ | イベントリスナ |
|------|----------------|--------------|
| **登録方法** | HTML属性またはプロパティ | `addEventListener` |
| **複数登録** | 不可（上書きされる） | 可能 |
| **削除** | プロパティに`null`を代入 | `removeEventListener` |

### イベントハンドラの例

```javascript
// 方法1: HTML属性（非推奨）
// <button onclick="handleClick()">クリック</button>

// 方法2: プロパティ
button.onclick = function() {
    console.log('クリックされました');
};
```

### イベントリスナの例

```javascript
// addEventListener（推奨）
button.addEventListener('click', function() {
    console.log('クリックされました');
});
```

### 実践例: イベントハンドラとイベントリスナ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>イベントハンドラとイベントリスナ</title>
</head>
<body>
    <h1>イベントハンドラとイベントリスナ</h1>
    <button id="handlerBtn">イベントハンドラ</button>
    <button id="listenerBtn">イベントリスナ</button>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // イベントハンドラ（プロパティ）
        results.push('<h2>イベントハンドラ（プロパティ）</h2>');
        
        const handlerBtn = document.getElementById('handlerBtn');
        handlerBtn.onclick = function() {
            results.push('<p>イベントハンドラ1が実行されました</p>');
            output.innerHTML = results.join('');
        };
        
        // 上書きされる（複数登録できない）
        handlerBtn.onclick = function() {
            results.push('<p>イベントハンドラ2が実行されました（1は上書きされる）</p>');
            output.innerHTML = results.join('');
        };
        
        // イベントリスナ（addEventListener）
        results.push('<h2>イベントリスナ（addEventListener）</h2>');
        
        const listenerBtn = document.getElementById('listenerBtn');
        listenerBtn.addEventListener('click', function() {
            results.push('<p>イベントリスナ1が実行されました</p>');
            output.innerHTML = results.join('');
        });
        
        // 複数登録可能
        listenerBtn.addEventListener('click', function() {
            results.push('<p>イベントリスナ2が実行されました（両方実行される）</p>');
            output.innerHTML = results.join('');
        });
        
        // 比較表
        results.push('<h2>イベントハンドラとイベントリスナの比較</h2>');
        results.push('<table border="1" style="border-collapse: collapse;">');
        results.push('<tr><th>項目</th><th>イベントハンドラ</th><th>イベントリスナ</th></tr>');
        results.push('<tr><td>登録方法</td><td>プロパティ</td><td>addEventListener</td></tr>');
        results.push('<tr><td>複数登録</td><td>不可（上書き）</td><td>可能</td></tr>');
        results.push('<tr><td>削除</td><td>nullを代入</td><td>removeEventListener</td></tr>');
        results.push('<tr><td>推奨</td><td>非推奨</td><td>推奨</td></tr>');
        results.push('</table>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== イベントハンドラとイベントリスナ ===');
        console.log('イベントハンドラ: プロパティで登録（非推奨）');
        console.log('イベントリスナ: addEventListenerで登録（推奨）');
    </script>
</body>
</html>
```

---

## イベントリスナの登録方法

イベントリスナを登録する方法を学びます。

### addEventListenerの基本構文

```javascript
要素.addEventListener('イベント名', 関数, オプション);
```

### 基本的な使い方

```javascript
const button = document.getElementById('myButton');

// 関数を直接定義
button.addEventListener('click', function() {
    console.log('クリックされました');
});

// 関数を変数に代入
function handleClick() {
    console.log('クリックされました');
}
button.addEventListener('click', handleClick);

// アロー関数
button.addEventListener('click', () => {
    console.log('クリックされました');
});
```

### 実践例: イベントリスナの登録方法

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>イベントリスナの登録方法</title>
</head>
<body>
    <h1>イベントリスナの登録方法</h1>
    <button id="btn1">方法1: 関数を直接定義</button>
    <button id="btn2">方法2: 関数を変数に代入</button>
    <button id="btn3">方法3: アロー関数</button>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 方法1: 関数を直接定義
        const btn1 = document.getElementById('btn1');
        btn1.addEventListener('click', function() {
            results.push('<p>方法1: 関数を直接定義で実行されました</p>');
            output.innerHTML = results.join('');
        });
        
        // 方法2: 関数を変数に代入
        const btn2 = document.getElementById('btn2');
        function handleClick() {
            results.push('<p>方法2: 関数を変数に代入で実行されました</p>');
            output.innerHTML = results.join('');
        }
        btn2.addEventListener('click', handleClick);
        
        // 方法3: アロー関数
        const btn3 = document.getElementById('btn3');
        btn3.addEventListener('click', () => {
            results.push('<p>方法3: アロー関数で実行されました</p>');
            output.innerHTML = results.join('');
        });
        
        // 様々なイベント
        results.push('<h2>様々なイベント</h2>');
        
        const testDiv = document.createElement('div');
        testDiv.id = 'testDiv';
        testDiv.textContent = 'マウスを乗せてください';
        testDiv.style.padding = '20px';
        testDiv.style.backgroundColor = '#f0f0f0';
        testDiv.style.margin = '10px';
        document.body.appendChild(testDiv);
        
        // mouseoverイベント
        testDiv.addEventListener('mouseover', function() {
            this.style.backgroundColor = '#ffff00';
            this.textContent = 'マウスが乗っています';
        });
        
        // mouseoutイベント
        testDiv.addEventListener('mouseout', function() {
            this.style.backgroundColor = '#f0f0f0';
            this.textContent = 'マウスを乗せてください';
        });
        
        // 実践例: フォーム入力
        results.push('<h2>実践例: フォーム入力</h2>');
        
        const input = document.createElement('input');
        input.type = 'text';
        input.placeholder = '入力してください';
        input.style.padding = '10px';
        input.style.margin = '10px';
        input.style.width = '200px';
        document.body.appendChild(input);
        
        const inputOutput = document.createElement('div');
        inputOutput.id = 'inputOutput';
        document.body.appendChild(inputOutput);
        
        input.addEventListener('input', function() {
            inputOutput.textContent = `入力中: ${this.value}`;
        });
        
        input.addEventListener('focus', function() {
            this.style.border = '2px solid blue';
        });
        
        input.addEventListener('blur', function() {
            this.style.border = '1px solid #ccc';
        });
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== イベントリスナの登録方法 ===');
        console.log('addEventListenerでイベントリスナを登録します');
        console.log('関数を直接定義、変数に代入、アロー関数など様々な方法があります');
    </script>
</body>
</html>
```

---

## イベントリスナを複数登録する方法

同じ要素に複数のイベントリスナを登録する方法を学びます。

### 複数登録の基本

```javascript
const button = document.getElementById('myButton');

// 1つ目のリスナ
button.addEventListener('click', function() {
    console.log('1つ目の処理');
});

// 2つ目のリスナ
button.addEventListener('click', function() {
    console.log('2つ目の処理');
});

// 両方実行される
```

### 実践例: イベントリスナを複数登録する方法

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>イベントリスナを複数登録する方法</title>
</head>
<body>
    <h1>イベントリスナを複数登録する方法</h1>
    <button id="multiBtn">複数のリスナを登録</button>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        const multiBtn = document.getElementById('multiBtn');
        
        // 複数のイベントリスナを登録
        multiBtn.addEventListener('click', function() {
            results.push('<p>✓ リスナ1が実行されました</p>');
            output.innerHTML = results.join('');
        });
        
        multiBtn.addEventListener('click', function() {
            results.push('<p>✓ リスナ2が実行されました</p>');
            output.innerHTML = results.join('');
        });
        
        multiBtn.addEventListener('click', function() {
            results.push('<p>✓ リスナ3が実行されました</p>');
            output.innerHTML = results.join('');
        });
        
        results.push('<h2>複数のイベントリスナ</h2>');
        results.push('<p>addEventListenerは複数登録可能です</p>');
        results.push('<p>ボタンをクリックすると、すべてのリスナが順番に実行されます</p>');
        
        // 実践例: 複数の処理を分離
        results.push('<h2>実践例: 複数の処理を分離</h2>');
        
        const complexBtn = document.createElement('button');
        complexBtn.textContent = '複数の処理を実行';
        complexBtn.style.marginTop = '10px';
        document.body.appendChild(complexBtn);
        
        const complexOutput = document.createElement('div');
        complexOutput.id = 'complexOutput';
        complexOutput.style.marginTop = '10px';
        document.body.appendChild(complexOutput);
        
        let clickCount = 0;
        
        // 処理1: カウントを増やす
        complexBtn.addEventListener('click', function() {
            clickCount++;
        });
        
        // 処理2: ログを出力
        complexBtn.addEventListener('click', function() {
            console.log(`クリック回数: ${clickCount}`);
        });
        
        // 処理3: 表示を更新
        complexBtn.addEventListener('click', function() {
            complexOutput.textContent = `クリック回数: ${clickCount}回`;
        });
        
        // 処理4: 色を変更
        complexBtn.addEventListener('click', function() {
            if (clickCount % 2 === 0) {
                this.style.backgroundColor = '#90EE90';
            } else {
                this.style.backgroundColor = '#FFB6C1';
            }
        });
        
        // 実践例: 異なるイベントタイプ
        results.push('<h2>実践例: 異なるイベントタイプ</h2>');
        
        const multiEventBtn = document.createElement('button');
        multiEventBtn.textContent = '様々なイベント';
        multiEventBtn.style.marginTop = '10px';
        document.body.appendChild(multiEventBtn);
        
        const multiEventOutput = document.createElement('div');
        multiEventOutput.id = 'multiEventOutput';
        multiEventOutput.style.marginTop = '10px';
        document.body.appendChild(multiEventOutput);
        
        // クリックイベント
        multiEventBtn.addEventListener('click', function() {
            multiEventOutput.textContent = 'クリックされました';
        });
        
        // マウスオーバーイベント
        multiEventBtn.addEventListener('mouseover', function() {
            this.style.transform = 'scale(1.1)';
        });
        
        // マウスアウトイベント
        multiEventBtn.addEventListener('mouseout', function() {
            this.style.transform = 'scale(1)';
        });
        
        // フォーカスイベント
        multiEventBtn.addEventListener('focus', function() {
            this.style.outline = '2px solid blue';
        });
        
        // ブラーイベント
        multiEventBtn.addEventListener('blur', function() {
            this.style.outline = 'none';
        });
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== イベントリスナを複数登録する方法 ===');
        console.log('addEventListenerは複数登録可能です');
        console.log('同じイベントタイプでも、異なるイベントタイプでも登録できます');
    </script>
</body>
</html>
```

---

## まとめ

この章では、イベントについて学びました。

### 学んだこと
- イベントの概念と種類
- イベントハンドラとイベントリスナの違い
- イベントリスナの登録方法（addEventListener）
- イベントリスナの複数登録

### 重要なポイント
1. **イベント**: ユーザーの操作やブラウザの動作
2. **addEventListener**: イベントリスナを登録する（推奨）
3. **複数登録可能**: 同じ要素に複数のリスナを登録できる
4. **様々なイベント**: click, mouseover, keydown, inputなど

### 次のステップ
次の章では、非同期処理と通信について詳しく学びます。

---

## 演習問題

### 問題1: クリックイベント
ボタンにクリックイベントリスナを登録し、クリックされたときに「クリックされました」と表示してください。

<details>
<summary>解答例</summary>

```javascript
const button = document.getElementById('myButton');
button.addEventListener('click', function() {
    console.log('クリックされました');
});
```
</details>

### 問題2: 入力イベント
入力フィールドに`input`イベントリスナを登録し、入力された値を表示してください。

<details>
<summary>解答例</summary>

```javascript
const input = document.getElementById('myInput');
const output = document.getElementById('output');

input.addEventListener('input', function() {
    output.textContent = `入力値: ${this.value}`;
});
```
</details>

### 問題3: 複数のイベントリスナ
ボタンに2つのクリックイベントリスナを登録し、両方が実行されることを確認してください。

<details>
<summary>解答例</summary>

```javascript
const button = document.getElementById('myButton');

button.addEventListener('click', function() {
    console.log('リスナ1が実行されました');
});

button.addEventListener('click', function() {
    console.log('リスナ2が実行されました');
});
```
</details>

### 問題4: マウスイベント
要素に`mouseover`と`mouseout`イベントリスナを登録し、マウスが乗ったときと離れたときに色を変更してください。

<details>
<summary>解答例</summary>

```javascript
const element = document.getElementById('myElement');

element.addEventListener('mouseover', function() {
    this.style.backgroundColor = 'yellow';
});

element.addEventListener('mouseout', function() {
    this.style.backgroundColor = 'white';
});
```
</details>

### 問題5: キーボードイベント
入力フィールドに`keydown`イベントリスナを登録し、Enterキーが押されたときにメッセージを表示してください。

<details>
<summary>解答例</summary>

```javascript
const input = document.getElementById('myInput');

input.addEventListener('keydown', function(event) {
    if (event.key === 'Enter') {
        console.log('Enterキーが押されました');
    }
});
```
</details>

---

お疲れ様でした！次の章に進みましょう。

