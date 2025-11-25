# 8-1. DOM (Document Object Model)

この章では、HTMLを操作するための「DOM」について学びます。DOMを理解することで、JavaScriptでWebページを動的に変更できるようになります。

---

## 目次

- [Windowオブジェクトとは](#windowオブジェクトとは)
- [Windowオブジェクトのメソッド](#windowオブジェクトのメソッド)
- [Windowオブジェクトのプロパティ](#windowオブジェクトのプロパティ)
- [DOMとは](#domとは)
- [DOMツリー](#domツリー)
- [特定のHTML要素の取得](#特定のhtml要素の取得)
- [要素内のコンテンツの取得・変更](#要素内のコンテンツの取得変更)
- [要素の作成](#要素の作成)
- [要素の追加・削除](#要素の追加削除)
- [要素の属性の取得・変更](#要素の属性の取得変更)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## Windowオブジェクトとは

### Windowオブジェクトの概念
`Window`オブジェクトは、**ブラウザウィンドウ全体を表す**オブジェクトです。JavaScriptの最上位オブジェクトです。

### Windowオブジェクトの特徴
1. **グローバルオブジェクト**: すべてのグローバル変数や関数は`Window`オブジェクトのプロパティ
2. **ブラウザウィンドウ**: ブラウザのウィンドウ全体を表す
3. **documentプロパティ**: `window.document`でDOMにアクセスできる

### Windowオブジェクトの基本

```javascript
// windowは省略可能
console.log('Hello');        // window.console.log()と同じ
alert('Hello');              // window.alert()と同じ
const x = 10;                // window.xと同じ
```

### 実践例: Windowオブジェクトとは

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>Windowオブジェクトとは</title>
</head>
<body>
    <h1>Windowオブジェクトとは</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // Windowオブジェクトの確認
        results.push('<h2>Windowオブジェクトの確認</h2>');
        results.push(`<p>window: ${typeof window}</p>`);
        results.push(`<p>window === this: ${window === this}</p>`);
        
        // windowは省略可能
        results.push('<h2>windowは省略可能</h2>');
        results.push('<p>console.log() は window.console.log() と同じ</p>');
        results.push('<p>alert() は window.alert() と同じ</p>');
        results.push('<p>document は window.document と同じ</p>');
        
        // グローバル変数はwindowのプロパティ
        results.push('<h2>グローバル変数はwindowのプロパティ</h2>');
        
        const globalVar = 'グローバル変数';
        results.push(`<p>globalVar: ${globalVar}</p>`);
        results.push(`<p>window.globalVar: ${window.globalVar}</p>`);
        results.push(`<p>globalVar === window.globalVar: ${globalVar === window.globalVar}</p>`);
        
        // Windowオブジェクトの特徴
        results.push('<h2>Windowオブジェクトの特徴</h2>');
        results.push('<p>• グローバルオブジェクト: すべてのグローバル変数や関数はWindowのプロパティ</p>');
        results.push('<p>• ブラウザウィンドウ: ブラウザのウィンドウ全体を表す</p>');
        results.push('<p>• documentプロパティ: window.documentでDOMにアクセス</p>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== Windowオブジェクト ===');
        console.log('Windowオブジェクトはブラウザウィンドウ全体を表します');
        console.log('windowは省略可能です');
    </script>
</body>
</html>
```

---

## Windowオブジェクトのメソッド

`Window`オブジェクトには、様々な便利なメソッドが用意されています。

### 主なWindowメソッド

#### alert() - アラートを表示
```javascript
alert('こんにちは！');
```

#### confirm() - 確認ダイアログ
```javascript
const result = confirm('削除しますか？');
console.log(result); // true または false
```

#### prompt() - 入力ダイアログ
```javascript
const name = prompt('名前を入力してください');
console.log(name); // 入力された文字列
```

#### setTimeout() - 遅延実行
```javascript
setTimeout(() => {
    console.log('3秒後に実行');
}, 3000);
```

#### setInterval() - 繰り返し実行
```javascript
setInterval(() => {
    console.log('1秒ごとに実行');
}, 1000);
```

### 実践例: Windowオブジェクトのメソッド

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>Windowオブジェクトのメソッド</title>
</head>
<body>
    <h1>Windowオブジェクトのメソッド</h1>
    <button id="alertBtn">アラート</button>
    <button id="confirmBtn">確認</button>
    <button id="promptBtn">入力</button>
    <button id="timeoutBtn">遅延実行</button>
    <button id="intervalBtn">繰り返し実行</button>
    <button id="stopIntervalBtn">停止</button>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let intervalId = null;
        
        document.getElementById('alertBtn').addEventListener('click', function() {
            alert('こんにちは！');
        });
        
        document.getElementById('confirmBtn').addEventListener('click', function() {
            const result = confirm('削除しますか？');
            output.innerHTML = `<p>結果: ${result ? 'OK' : 'キャンセル'}</p>`;
        });
        
        document.getElementById('promptBtn').addEventListener('click', function() {
            const name = prompt('名前を入力してください');
            if (name) {
                output.innerHTML = `<p>入力された名前: ${name}</p>`;
            }
        });
        
        document.getElementById('timeoutBtn').addEventListener('click', function() {
            output.innerHTML = '<p>3秒後にメッセージが表示されます...</p>';
            setTimeout(() => {
                output.innerHTML = '<p>3秒経過しました！</p>';
            }, 3000);
        });
        
        document.getElementById('intervalBtn').addEventListener('click', function() {
            let count = 0;
            intervalId = setInterval(() => {
                count++;
                output.innerHTML = `<p>カウント: ${count}</p>`;
            }, 1000);
        });
        
        document.getElementById('stopIntervalBtn').addEventListener('click', function() {
            if (intervalId) {
                clearInterval(intervalId);
                output.innerHTML = '<p>繰り返しを停止しました</p>';
            }
        });
        
        // コンソールで確認
        console.log('=== Windowオブジェクトのメソッド ===');
        console.log('alert, confirm, prompt, setTimeout, setIntervalなど');
    </script>
</body>
</html>
```

---

## Windowオブジェクトのプロパティ

`Window`オブジェクトには、様々な便利なプロパティが用意されています。

### 主なWindowプロパティ

#### innerWidth / innerHeight - ウィンドウのサイズ
```javascript
console.log(window.innerWidth);  // ウィンドウの幅
console.log(window.innerHeight); // ウィンドウの高さ
```

#### location - URL情報
```javascript
console.log(window.location.href);     // 現在のURL
console.log(window.location.hostname); // ホスト名
console.log(window.location.pathname); // パス
```

#### document - DOMへのアクセス
```javascript
console.log(window.document); // documentオブジェクト
```

#### navigator - ブラウザ情報
```javascript
console.log(navigator.userAgent); // ユーザーエージェント
```

### 実践例: Windowオブジェクトのプロパティ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>Windowオブジェクトのプロパティ</title>
</head>
<body>
    <h1>Windowオブジェクトのプロパティ</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // ウィンドウのサイズ
        results.push('<h2>ウィンドウのサイズ</h2>');
        results.push(`<p>innerWidth: ${window.innerWidth}px</p>`);
        results.push(`<p>innerHeight: ${window.innerHeight}px</p>`);
        
        // location（URL情報）
        results.push('<h2>location（URL情報）</h2>');
        results.push(`<p>href: ${window.location.href}</p>`);
        results.push(`<p>hostname: ${window.location.hostname}</p>`);
        results.push(`<p>pathname: ${window.location.pathname}</p>`);
        results.push(`<p>protocol: ${window.location.protocol}</p>`);
        
        // document
        results.push('<h2>document</h2>');
        results.push(`<p>document: ${typeof window.document}</p>`);
        results.push(`<p>document.title: ${window.document.title}</p>`);
        
        // navigator（ブラウザ情報）
        results.push('<h2>navigator（ブラウザ情報）</h2>');
        results.push(`<p>userAgent: ${navigator.userAgent}</p>`);
        results.push(`<p>language: ${navigator.language}</p>`);
        
        // 実践例: ウィンドウサイズの変更を検知
        results.push('<h2>実践例: ウィンドウサイズの変更を検知</h2>');
        results.push('<p>ウィンドウサイズを変更すると、値が更新されます</p>');
        
        window.addEventListener('resize', function() {
            const sizeInfo = document.getElementById('sizeInfo');
            if (sizeInfo) {
                sizeInfo.textContent = `サイズ: ${window.innerWidth} × ${window.innerHeight}px`;
            }
        });
        
        results.push(`<p id="sizeInfo">サイズ: ${window.innerWidth} × ${window.innerHeight}px</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== Windowオブジェクトのプロパティ ===');
        console.log('innerWidth:', window.innerWidth);
        console.log('innerHeight:', window.innerHeight);
        console.log('location:', window.location.href);
    </script>
</body>
</html>
```

---

## DOMとは

### DOMの概念
DOM（Document Object Model）は、**HTMLをJavaScriptで操作するための仕組み**です。

### DOMの特徴
1. **HTMLの表現**: HTMLをオブジェクトとして表現
2. **動的な操作**: JavaScriptでHTMLを変更できる
3. **ツリー構造**: HTMLはツリー（木）構造で表現される

### DOMの基本

```javascript
// HTML要素を取得
const element = document.getElementById('myId');

// 要素の内容を変更
element.textContent = '新しい内容';

// 要素のスタイルを変更
element.style.color = 'red';
```

### 実践例: DOMとは

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>DOMとは</title>
</head>
<body>
    <h1>DOMとは</h1>
    <div id="output"></div>
    <button id="changeBtn">内容を変更</button>
    
    <script>
        const output = document.getElementById('output');
        const changeBtn = document.getElementById('changeBtn');
        
        // 初期表示
        output.textContent = 'DOMの基本';
        
        changeBtn.addEventListener('click', function() {
            // DOM操作: 内容を変更
            output.textContent = 'DOMで内容が変更されました！';
            output.style.color = 'red';
            output.style.fontSize = '20px';
        });
        
        // コンソールで確認
        console.log('=== DOMとは ===');
        console.log('DOMはHTMLをJavaScriptで操作するための仕組みです');
        console.log('documentオブジェクトを通じてアクセスします');
    </script>
</body>
</html>
```

---

## DOMツリー

### DOMツリーの概念
DOMツリーは、**HTMLをツリー（木）構造で表現したもの**です。

### DOMツリーの構造

```html
<html>
  <head>
    <title>タイトル</title>
  </head>
  <body>
    <h1>見出し</h1>
    <p>段落</p>
  </body>
</html>
```

このHTMLは、以下のようなツリー構造になります：

```
html
├── head
│   └── title
└── body
    ├── h1
    └── p
```

### 実践例: DOMツリー

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>DOMツリー</title>
</head>
<body>
    <h1>DOMツリー</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // DOMツリーの概念
        results.push('<h2>DOMツリーの概念</h2>');
        results.push('<p>HTMLはツリー（木）構造で表現されます</p>');
        
        // DOMツリーの例
        results.push('<h2>DOMツリーの例</h2>');
        results.push('<pre>');
        results.push('html<br>');
        results.push('├── head<br>');
        results.push('│   └── title<br>');
        results.push('└── body<br>');
        results.push('    ├── h1<br>');
        results.push('    └── p<br>');
        results.push('</pre>');
        
        // 実際のDOMツリーを確認
        results.push('<h2>実際のDOMツリー</h2>');
        results.push(`<p>document: ${document.nodeName}</p>`);
        results.push(`<p>document.documentElement: ${document.documentElement.nodeName}</p>`);
        results.push(`<p>document.body: ${document.body.nodeName}</p>`);
        
        // 親子関係
        results.push('<h2>親子関係</h2>');
        const h1 = document.querySelector('h1');
        if (h1) {
            results.push(`<p>h1の親要素: ${h1.parentElement.nodeName}</p>`);
            results.push(`<p>h1の子要素: ${h1.childNodes.length}個</p>`);
        }
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== DOMツリー ===');
        console.log('HTMLはツリー構造で表現されます');
        console.log('document.documentElement:', document.documentElement);
    </script>
</body>
</html>
```

---

## 特定のHTML要素の取得

HTML要素を取得する方法を学びます。

### 要素の取得方法

#### getElementById() - IDで取得
```javascript
const element = document.getElementById('myId');
```

#### querySelector() - セレクタで取得（1つ）
```javascript
const element = document.querySelector('.myClass');
const element2 = document.querySelector('#myId');
```

#### querySelectorAll() - セレクタで取得（すべて）
```javascript
const elements = document.querySelectorAll('.myClass');
```

#### getElementsByClassName() - クラス名で取得
```javascript
const elements = document.getElementsByClassName('myClass');
```

#### getElementsByTagName() - タグ名で取得
```javascript
const elements = document.getElementsByTagName('div');
```

### 実践例: 特定のHTML要素の取得

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>特定のHTML要素の取得</title>
</head>
<body>
    <h1>特定のHTML要素の取得</h1>
    <div id="myId">IDで取得</div>
    <div class="myClass">クラス1</div>
    <div class="myClass">クラス2</div>
    <p>段落1</p>
    <p>段落2</p>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // getElementById
        results.push('<h2>getElementById（IDで取得）</h2>');
        const elementById = document.getElementById('myId');
        results.push(`<p>取得した要素: ${elementById.textContent}</p>`);
        
        // querySelector（1つ）
        results.push('<h2>querySelector（セレクタで取得、1つ）</h2>');
        const elementByClass = document.querySelector('.myClass');
        results.push(`<p>取得した要素: ${elementByClass.textContent}</p>`);
        
        // querySelectorAll（すべて）
        results.push('<h2>querySelectorAll（セレクタで取得、すべて）</h2>');
        const elementsByClass = document.querySelectorAll('.myClass');
        results.push(`<p>取得した要素数: ${elementsByClass.length}個</p>`);
        elementsByClass.forEach((el, index) => {
            results.push(`<p>要素${index + 1}: ${el.textContent}</p>`);
        });
        
        // getElementsByClassName
        results.push('<h2>getElementsByClassName（クラス名で取得）</h2>');
        const elementsByClassName = document.getElementsByClassName('myClass');
        results.push(`<p>取得した要素数: ${elementsByClassName.length}個</p>`);
        
        // getElementsByTagName
        results.push('<h2>getElementsByTagName（タグ名で取得）</h2>');
        const elementsByTag = document.getElementsByTagName('p');
        results.push(`<p>取得したp要素数: ${elementsByTag.length}個</p>`);
        
        // 実践例: フォーム要素の取得
        results.push('<h2>実践例: フォーム要素の取得</h2>');
        results.push('<input type="text" id="username" placeholder="ユーザー名">');
        results.push('<button id="getValueBtn">値を取得</button>');
        
        document.getElementById('getValueBtn').addEventListener('click', function() {
            const username = document.getElementById('username').value;
            results.push(`<p>入力された値: ${username}</p>`);
            output.innerHTML = results.join('');
        });
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 特定のHTML要素の取得 ===');
        console.log('getElementById, querySelector, querySelectorAllなど');
        console.log('様々な方法で要素を取得できます');
    </script>
</body>
</html>
```

---

## 要素内のコンテンツの取得・変更

要素の内容（テキストやHTML）を取得・変更する方法を学びます。

### コンテンツの取得・変更方法

#### textContent - テキスト内容
```javascript
const element = document.getElementById('myId');
console.log(element.textContent);  // 取得
element.textContent = '新しいテキスト';  // 変更
```

#### innerHTML - HTML内容
```javascript
const element = document.getElementById('myId');
console.log(element.innerHTML);  // 取得
element.innerHTML = '<strong>太字</strong>';  // 変更（HTMLとして解釈）
```

#### innerText - 表示テキスト
```javascript
const element = document.getElementById('myId');
console.log(element.innerText);  // 取得
element.innerText = '新しいテキスト';  // 変更
```

### textContentとinnerHTMLの違い

| 項目 | textContent | innerHTML |
|------|------------|-----------|
| **HTMLタグ** | テキストとして扱う | HTMLとして解釈 |
| **セキュリティ** | 安全 | XSSのリスクあり |
| **パフォーマンス** | 速い | やや遅い |

### 実践例: 要素内のコンテンツの取得・変更

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>要素内のコンテンツの取得・変更</title>
</head>
<body>
    <h1>要素内のコンテンツの取得・変更</h1>
    <div id="textContent">テキスト内容</div>
    <div id="innerHTML"><strong>HTML内容</strong></div>
    <button id="changeTextBtn">テキストを変更</button>
    <button id="changeHtmlBtn">HTMLを変更</button>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // textContent
        results.push('<h2>textContent（テキスト内容）</h2>');
        const textElement = document.getElementById('textContent');
        results.push(`<p>取得: ${textElement.textContent}</p>`);
        
        document.getElementById('changeTextBtn').addEventListener('click', function() {
            textElement.textContent = 'テキストが変更されました！';
            results.push(`<p>変更後: ${textElement.textContent}</p>`);
            output.innerHTML = results.join('');
        });
        
        // innerHTML
        results.push('<h2>innerHTML（HTML内容）</h2>');
        const htmlElement = document.getElementById('innerHTML');
        results.push(`<p>取得: ${htmlElement.innerHTML}</p>`);
        
        document.getElementById('changeHtmlBtn').addEventListener('click', function() {
            htmlElement.innerHTML = '<strong>太字</strong>と<em>斜体</em>が使えます';
            results.push(`<p>変更後: ${htmlElement.innerHTML}</p>`);
            output.innerHTML = results.join('');
        });
        
        // textContentとinnerHTMLの違い
        results.push('<h2>textContentとinnerHTMLの違い</h2>');
        results.push('<table border="1" style="border-collapse: collapse;">');
        results.push('<tr><th>項目</th><th>textContent</th><th>innerHTML</th></tr>');
        results.push('<tr><td>HTMLタグ</td><td>テキストとして扱う</td><td>HTMLとして解釈</td></tr>');
        results.push('<tr><td>セキュリティ</td><td>安全</td><td>XSSのリスクあり</td></tr>');
        results.push('<tr><td>パフォーマンス</td><td>速い</td><td>やや遅い</td></tr>');
        results.push('</table>');
        
        // 実践例: 動的な内容の更新
        results.push('<h2>実践例: 動的な内容の更新</h2>');
        const dynamicElement = document.createElement('div');
        dynamicElement.id = 'dynamic';
        document.body.appendChild(dynamicElement);
        
        let count = 0;
        setInterval(() => {
            count++;
            dynamicElement.textContent = `カウント: ${count}`;
        }, 1000);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 要素内のコンテンツの取得・変更 ===');
        console.log('textContent: テキスト内容');
        console.log('innerHTML: HTML内容');
    </script>
</body>
</html>
```

---

## 要素の作成

新しいHTML要素を作成する方法を学びます。

### 要素の作成方法

#### createElement() - 要素を作成
```javascript
const newElement = document.createElement('div');
newElement.textContent = '新しい要素';
```

### 実践例: 要素の作成

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>要素の作成</title>
</head>
<body>
    <h1>要素の作成</h1>
    <button id="createBtn">要素を作成</button>
    <div id="container"></div>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        const container = document.getElementById('container');
        let results = [];
        
        // 基本的な要素の作成
        results.push('<h2>基本的な要素の作成</h2>');
        
        document.getElementById('createBtn').addEventListener('click', function() {
            // div要素を作成
            const newDiv = document.createElement('div');
            newDiv.textContent = '新しく作成された要素';
            newDiv.style.padding = '10px';
            newDiv.style.backgroundColor = '#f0f0f0';
            newDiv.style.margin = '5px';
            
            container.appendChild(newDiv);
            results.push(`<p>要素を作成しました: ${newDiv.textContent}</p>`);
            output.innerHTML = results.join('');
        });
        
        // 様々な要素の作成
        results.push('<h2>様々な要素の作成</h2>');
        
        // ボタン要素
        const button = document.createElement('button');
        button.textContent = '動的に作成されたボタン';
        button.addEventListener('click', function() {
            alert('ボタンがクリックされました！');
        });
        container.appendChild(button);
        
        // 入力要素
        const input = document.createElement('input');
        input.type = 'text';
        input.placeholder = '入力してください';
        container.appendChild(input);
        
        // 実践例: リストの動的作成
        results.push('<h2>実践例: リストの動的作成</h2>');
        
        const items = ['りんご', 'バナナ', 'オレンジ'];
        const ul = document.createElement('ul');
        
        items.forEach(item => {
            const li = document.createElement('li');
            li.textContent = item;
            ul.appendChild(li);
        });
        
        container.appendChild(ul);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 要素の作成 ===');
        console.log('createElementメソッドで新しい要素を作成できます');
    </script>
</body>
</html>
```

---

## 要素の追加・削除

要素を追加・削除する方法を学びます。

### 要素の追加方法

#### appendChild() - 子要素として追加
```javascript
const parent = document.getElementById('parent');
const child = document.createElement('div');
parent.appendChild(child);
```

#### insertBefore() - 指定位置に追加
```javascript
const parent = document.getElementById('parent');
const newElement = document.createElement('div');
const referenceElement = document.getElementById('reference');
parent.insertBefore(newElement, referenceElement);
```

### 要素の削除方法

#### removeChild() - 子要素を削除
```javascript
const parent = document.getElementById('parent');
const child = document.getElementById('child');
parent.removeChild(child);
```

#### remove() - 要素自体を削除
```javascript
const element = document.getElementById('myId');
element.remove();
```

### 実践例: 要素の追加・削除

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>要素の追加・削除</title>
</head>
<body>
    <h1>要素の追加・削除</h1>
    <button id="addBtn">要素を追加</button>
    <button id="removeBtn">要素を削除</button>
    <div id="container">
        <div class="item">項目1</div>
        <div class="item">項目2</div>
    </div>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        const container = document.getElementById('container');
        let itemCount = 2;
        
        // 要素の追加
        document.getElementById('addBtn').addEventListener('click', function() {
            itemCount++;
            const newItem = document.createElement('div');
            newItem.className = 'item';
            newItem.textContent = `項目${itemCount}`;
            newItem.style.padding = '10px';
            newItem.style.margin = '5px';
            newItem.style.backgroundColor = '#e0e0e0';
            
            container.appendChild(newItem);
            output.innerHTML = `<p>要素を追加しました: 項目${itemCount}</p>`;
        });
        
        // 要素の削除
        document.getElementById('removeBtn').addEventListener('click', function() {
            const items = container.querySelectorAll('.item');
            if (items.length > 0) {
                const lastItem = items[items.length - 1];
                lastItem.remove();
                output.innerHTML = '<p>最後の要素を削除しました</p>';
            } else {
                output.innerHTML = '<p>削除する要素がありません</p>';
            }
        });
        
        // 実践例: 動的なリスト管理
        const listContainer = document.createElement('div');
        listContainer.id = 'listContainer';
        listContainer.style.marginTop = '20px';
        document.body.appendChild(listContainer);
        
        const addListBtn = document.createElement('button');
        addListBtn.textContent = 'リストに追加';
        addListBtn.style.marginRight = '10px';
        
        const removeListBtn = document.createElement('button');
        removeListBtn.textContent = 'リストから削除';
        
        const list = document.createElement('ul');
        list.id = 'dynamicList';
        
        listContainer.appendChild(addListBtn);
        listContainer.appendChild(removeListBtn);
        listContainer.appendChild(list);
        
        let listItemCount = 0;
        
        addListBtn.addEventListener('click', function() {
            listItemCount++;
            const li = document.createElement('li');
            li.textContent = `リスト項目${listItemCount}`;
            list.appendChild(li);
        });
        
        removeListBtn.addEventListener('click', function() {
            const items = list.querySelectorAll('li');
            if (items.length > 0) {
                items[items.length - 1].remove();
            }
        });
        
        // コンソールで確認
        console.log('=== 要素の追加・削除 ===');
        console.log('appendChild: 子要素として追加');
        console.log('remove: 要素を削除');
    </script>
</body>
</html>
```

---

## 要素の属性の取得・変更

要素の属性（id、class、srcなど）を取得・変更する方法を学びます。

### 属性の取得・変更方法

#### getAttribute() - 属性を取得
```javascript
const element = document.getElementById('myId');
const id = element.getAttribute('id');
```

#### setAttribute() - 属性を設定
```javascript
const element = document.getElementById('myId');
element.setAttribute('class', 'newClass');
```

#### removeAttribute() - 属性を削除
```javascript
const element = document.getElementById('myId');
element.removeAttribute('class');
```

#### プロパティとして直接アクセス
```javascript
const element = document.getElementById('myId');
element.id = 'newId';        // id属性
element.className = 'newClass'; // class属性
element.src = 'new-image.jpg';  // src属性
```

### 実践例: 要素の属性の取得・変更

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>要素の属性の取得・変更</title>
    <style>
        .highlight {
            background-color: yellow;
            padding: 10px;
        }
        .blue {
            color: blue;
        }
    </style>
</head>
<body>
    <h1>要素の属性の取得・変更</h1>
    <div id="myDiv" class="original">属性のテスト</div>
    <img id="myImage" src="" alt="画像">
    <button id="getAttrBtn">属性を取得</button>
    <button id="setAttrBtn">属性を設定</button>
    <button id="removeAttrBtn">属性を削除</button>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        const myDiv = document.getElementById('myDiv');
        const myImage = document.getElementById('myImage');
        let results = [];
        
        // 属性の取得
        document.getElementById('getAttrBtn').addEventListener('click', function() {
            results = [];
            results.push('<h2>属性の取得</h2>');
            results.push(`<p>id: ${myDiv.getAttribute('id')}</p>`);
            results.push(`<p>class: ${myDiv.getAttribute('class')}</p>`);
            results.push(`<p>プロパティとして: myDiv.id = ${myDiv.id}</p>`);
            results.push(`<p>プロパティとして: myDiv.className = ${myDiv.className}</p>`);
            output.innerHTML = results.join('');
        });
        
        // 属性の設定
        document.getElementById('setAttrBtn').addEventListener('click', function() {
            myDiv.setAttribute('class', 'highlight blue');
            myDiv.setAttribute('data-custom', 'custom-value');
            myImage.setAttribute('src', 'https://via.placeholder.com/150');
            myImage.setAttribute('alt', 'プレースホルダー画像');
            
            results = [];
            results.push('<h2>属性を設定しました</h2>');
            results.push(`<p>class: ${myDiv.getAttribute('class')}</p>`);
            results.push(`<p>data-custom: ${myDiv.getAttribute('data-custom')}</p>`);
            output.innerHTML = results.join('');
        });
        
        // 属性の削除
        document.getElementById('removeAttrBtn').addEventListener('click', function() {
            myDiv.removeAttribute('class');
            myDiv.removeAttribute('data-custom');
            
            results = [];
            results.push('<h2>属性を削除しました</h2>');
            results.push(`<p>class: ${myDiv.getAttribute('class')}（null）</p>`);
            output.innerHTML = results.join('');
        });
        
        // プロパティとして直接アクセス
        results.push('<h2>プロパティとして直接アクセス</h2>');
        results.push('<p>element.id = "newId"</p>');
        results.push('<p>element.className = "newClass"</p>');
        results.push('<p>element.src = "new-image.jpg"</p>');
        
        // 実践例: 画像の切り替え
        results.push('<h2>実践例: 画像の切り替え</h2>');
        const imageBtn = document.createElement('button');
        imageBtn.textContent = '画像を切り替え';
        imageBtn.style.marginTop = '10px';
        
        let imageIndex = 0;
        const images = [
            'https://via.placeholder.com/150?text=Image1',
            'https://via.placeholder.com/150?text=Image2',
            'https://via.placeholder.com/150?text=Image3'
        ];
        
        imageBtn.addEventListener('click', function() {
            imageIndex = (imageIndex + 1) % images.length;
            myImage.setAttribute('src', images[imageIndex]);
            myImage.setAttribute('alt', `画像${imageIndex + 1}`);
        });
        
        document.body.appendChild(imageBtn);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 要素の属性の取得・変更 ===');
        console.log('getAttribute: 属性を取得');
        console.log('setAttribute: 属性を設定');
        console.log('removeAttribute: 属性を削除');
    </script>
</body>
</html>
```

---

## まとめ

この章では、DOMについて学びました。

### 学んだこと
- Windowオブジェクトの概念とメソッド・プロパティ
- DOMの概念とDOMツリー
- 特定のHTML要素の取得方法
- 要素内のコンテンツの取得・変更
- 要素の作成
- 要素の追加・削除
- 要素の属性の取得・変更

### 重要なポイント
1. **Windowオブジェクト**: ブラウザウィンドウ全体を表す
2. **DOM**: HTMLをJavaScriptで操作する仕組み
3. **要素の取得**: `getElementById`, `querySelector`など
4. **コンテンツの変更**: `textContent`, `innerHTML`
5. **要素の操作**: `createElement`, `appendChild`, `remove`

### 次のステップ
次の章では、イベントについて詳しく学びます。

---

## 演習問題

### 問題1: 要素の取得
IDが`myElement`の要素を取得してください。

<details>
<summary>解答例</summary>

```javascript
const element = document.getElementById('myElement');
console.log(element);
```
</details>

### 問題2: コンテンツの変更
IDが`myDiv`の要素の内容を「新しい内容」に変更してください。

<details>
<summary>解答例</summary>

```javascript
const element = document.getElementById('myDiv');
element.textContent = '新しい内容';
```
</details>

### 問題3: 要素の作成
新しい`div`要素を作成し、テキストを「新しい要素」に設定してください。

<details>
<summary>解答例</summary>

```javascript
const newDiv = document.createElement('div');
newDiv.textContent = '新しい要素';
```
</details>

### 問題4: 要素の追加
作成した要素を、IDが`container`の要素の子要素として追加してください。

<details>
<summary>解答例</summary>

```javascript
const newDiv = document.createElement('div');
newDiv.textContent = '新しい要素';
const container = document.getElementById('container');
container.appendChild(newDiv);
```
</details>

### 問題5: 属性の設定
要素に`class="highlight"`属性を設定してください。

<details>
<summary>解答例</summary>

```javascript
const element = document.getElementById('myElement');
element.setAttribute('class', 'highlight');
// または
element.className = 'highlight';
```
</details>

---

お疲れ様でした！次の章に進みましょう。

