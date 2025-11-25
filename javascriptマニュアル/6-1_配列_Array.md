# 6-1. 配列 (Array)

この章では、複数のデータをまとめて扱う「配列」について学びます。配列を理解することで、大量のデータを効率的に管理できるようになります。

---

## 目次

- [配列 - 複数のデータをまとめて扱う](#配列---複数のデータをまとめて扱う)
- [配列とは](#配列とは)
- [配列とインデックス](#配列とインデックス)
- [length 配列の要素の数を確認](#length-配列の要素の数を確認)
- [配列の要素の上書き](#配列の要素の上書き)
- [constで宣言した変数の配列についての補足](#constで宣言した変数の配列についての補足)
- [push 配列の要素を追加](#push-配列の要素を追加)
- [shift 配列の先頭を削除](#shift-配列の先頭を削除)
- [pop 配列の末尾を削除](#pop-配列の末尾を削除)
- [多次元配列](#多次元配列)
- [分割代入](#分割代入)
- [スプレッド構文/レストパラメータ](#スプレッド構文レストパラメータ)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## 配列 - 複数のデータをまとめて扱う

### 配列の概念
配列（Array）は、**複数のデータを順番に並べて、1つの変数で管理する**データ構造です。

### 日常生活での例
- **買い物リスト**: りんご、バナナ、オレンジ...（複数の商品を1つのリストで管理）
- **出席番号**: 1番、2番、3番...（複数の番号を順番に管理）
- **成績表**: 85点、90点、78点...（複数の点数を管理）

### プログラミングでの例
```javascript
// 配列の作成
const fruits = ['りんご', 'バナナ', 'オレンジ'];

// 配列の要素にアクセス
console.log(fruits[0]); // 'りんご'
console.log(fruits[1]); // 'バナナ'
console.log(fruits[2]); // 'オレンジ'
```

### 配列を使う理由
1. **データの整理**: 関連するデータを1つにまとめられる
2. **ループ処理**: 同じ処理を複数のデータに適用できる
3. **順序の管理**: データの順番を保持できる
4. **効率的な操作**: 配列のメソッドで様々な操作ができる

### 実践例: 配列の基本概念

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>配列の基本概念</title>
</head>
<body>
    <h1>配列 - 複数のデータをまとめて扱う</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 配列を使わない場合（複数の変数が必要）
        results.push('<h2>配列を使わない場合</h2>');
        const fruit1 = 'りんご';
        const fruit2 = 'バナナ';
        const fruit3 = 'オレンジ';
        results.push(`<p>${fruit1}, ${fruit2}, ${fruit3}</p>`);
        results.push('<p>複数の変数が必要で、管理が大変</p>');
        
        // 配列を使う場合（1つの変数で管理）
        results.push('<h2>配列を使う場合</h2>');
        const fruits = ['りんご', 'バナナ', 'オレンジ'];
        results.push(`<p>配列: [${fruits.join(', ')}]</p>`);
        results.push('<p>1つの変数で複数のデータを管理できる</p>');
        
        // ループで処理
        results.push('<h2>ループで処理</h2>');
        results.push('<ul>');
        for (let i = 0; i < fruits.length; i++) {
            results.push(`<li>${fruits[i]}</li>`);
        }
        results.push('</ul>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 配列の基本 ===');
        console.log('配列を使うことで、複数のデータを効率的に管理できます');
    </script>
</body>
</html>
```

---

## 配列とは

### 配列の定義
配列は、**複数の値を順番に並べたデータ構造**です。各値は「要素」と呼ばれます。

### 配列の作成方法

#### 方法1: 配列リテラル（推奨）
```javascript
const fruits = ['りんご', 'バナナ', 'オレンジ'];
```

#### 方法2: Arrayコンストラクタ
```javascript
const fruits = new Array('りんご', 'バナナ', 'オレンジ');
```

#### 方法3: 空の配列を作成
```javascript
const emptyArray = [];
const emptyArray2 = new Array();
```

### 配列の特徴
1. **順序がある**: 要素の順番が保持される
2. **インデックスでアクセス**: 0から始まる番号で要素にアクセス
3. **型が混在可能**: 異なる型の値を含められる（非推奨）
4. **動的なサイズ**: 要素を追加・削除できる

### 実践例: 配列とは

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>配列とは</title>
</head>
<body>
    <h1>配列とは</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 配列リテラル（推奨）
        const fruits1 = ['りんご', 'バナナ', 'オレンジ'];
        results.push('<h2>配列リテラル（推奨）</h2>');
        results.push(`<p>[${fruits1.join(', ')}]</p>`);
        
        // Arrayコンストラクタ
        const fruits2 = new Array('りんご', 'バナナ', 'オレンジ');
        results.push('<h2>Arrayコンストラクタ</h2>');
        results.push(`<p>[${fruits2.join(', ')}]</p>`);
        
        // 空の配列
        const emptyArray = [];
        results.push('<h2>空の配列</h2>');
        results.push(`<p>空の配列: [${emptyArray.join(', ')}]（要素数: ${emptyArray.length}）</p>`);
        
        // 様々な型の配列
        results.push('<h2>様々な型の配列</h2>');
        
        const numbers = [1, 2, 3, 4, 5];
        results.push(`<p>数値の配列: [${numbers.join(', ')}]</p>`);
        
        const strings = ['Hello', 'World', 'JavaScript'];
        results.push(`<p>文字列の配列: [${strings.join(', ')}]</p>`);
        
        const mixed = [1, '文字列', true, null];
        results.push(`<p>混合型の配列: [${mixed.join(', ')}]（非推奨）</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 配列とは ===');
        console.log('配列は複数の値を順番に並べたデータ構造です');
        console.log('配列リテラル [] を使うことが推奨されます');
    </script>
</body>
</html>
```

---

## 配列とインデックス

配列の各要素には、**インデックス（添字）**という番号が付けられています。

### インデックスの基本
- **0から始まる**: 最初の要素のインデックスは0
- **順番に増える**: 1番目の要素は0、2番目の要素は1、3番目の要素は2...
- **角括弧でアクセス**: `配列[インデックス]`で要素にアクセス

### インデックスの例

```javascript
const fruits = ['りんご', 'バナナ', 'オレンジ'];

console.log(fruits[0]); // 'りんご'（1番目の要素）
console.log(fruits[1]); // 'バナナ'（2番目の要素）
console.log(fruits[2]); // 'オレンジ'（3番目の要素）
```

### インデックスの範囲
- **有効な範囲**: 0から（配列の長さ - 1）まで
- **範囲外アクセス**: `undefined`が返される

```javascript
const arr = [1, 2, 3];
console.log(arr[0]);  // 1
console.log(arr[2]);  // 3
console.log(arr[3]);  // undefined（範囲外）
console.log(arr[-1]); // undefined（範囲外）
```

### 実践例: 配列とインデックス

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>配列とインデックス</title>
</head>
<body>
    <h1>配列とインデックス</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        const fruits = ['りんご', 'バナナ', 'オレンジ', 'ぶどう'];
        
        // インデックスでアクセス
        results.push('<h2>インデックスでアクセス</h2>');
        results.push(`<p>fruits[0]: ${fruits[0]}（1番目の要素）</p>`);
        results.push(`<p>fruits[1]: ${fruits[1]}（2番目の要素）</p>`);
        results.push(`<p>fruits[2]: ${fruits[2]}（3番目の要素）</p>`);
        results.push(`<p>fruits[3]: ${fruits[3]}（4番目の要素）</p>`);
        
        // インデックスの特徴
        results.push('<h2>インデックスの特徴</h2>');
        results.push('<p>• 0から始まる</p>');
        results.push('<p>• 順番に増える（0, 1, 2, 3...）</p>');
        results.push('<p>• 角括弧 [] でアクセス</p>');
        
        // 範囲外アクセス
        results.push('<h2>範囲外アクセス</h2>');
        results.push(`<p>fruits[4]: ${fruits[4]}（undefined - 範囲外）</p>`);
        results.push(`<p>fruits[-1]: ${fruits[-1]}（undefined - 範囲外）</p>`);
        
        // 実践例: インデックスを使った処理
        results.push('<h2>実践例: インデックスを使った処理</h2>');
        
        const scores = [85, 90, 78, 92, 88];
        results.push(`<p>点数: [${scores.join(', ')}]</p>`);
        results.push(`<p>1番目の点数: ${scores[0]}点</p>`);
        results.push(`<p>最後の点数: ${scores[scores.length - 1]}点</p>`);
        
        // インデックスと要素の対応表
        results.push('<h2>インデックスと要素の対応</h2>');
        results.push('<table border="1" style="border-collapse: collapse;">');
        results.push('<tr><th>インデックス</th><th>要素</th></tr>');
        for (let i = 0; i < fruits.length; i++) {
            results.push(`<tr><td>${i}</td><td>${fruits[i]}</td></tr>`);
        }
        results.push('</table>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 配列とインデックス ===');
        console.log('インデックスは0から始まります');
        console.log('配列[インデックス]で要素にアクセスできます');
    </script>
</body>
</html>
```

---

## length 配列の要素の数を確認

`length`プロパティは、配列の要素数を取得できます。

### lengthの基本

```javascript
const fruits = ['りんご', 'バナナ', 'オレンジ'];
console.log(fruits.length); // 3
```

### lengthの特徴
1. **自動更新**: 要素を追加・削除すると自動的に更新される
2. **読み取り専用ではない**: 値を設定することもできる（非推奨）
3. **最後の要素のインデックス**: `length - 1`で最後の要素にアクセスできる

### lengthの使い方

```javascript
const arr = [1, 2, 3, 4, 5];

// 要素数を取得
console.log(arr.length); // 5

// 最後の要素にアクセス
console.log(arr[arr.length - 1]); // 5

// 空の配列かチェック
if (arr.length === 0) {
    console.log('配列は空です');
}
```

### 実践例: lengthの使い方

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>length 配列の要素の数を確認</title>
</head>
<body>
    <h1>length - 配列の要素の数を確認</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的なlength
        const fruits = ['りんご', 'バナナ', 'オレンジ'];
        results.push('<h2>基本的なlength</h2>');
        results.push(`<p>配列: [${fruits.join(', ')}]</p>`);
        results.push(`<p>要素数: ${fruits.length}</p>`);
        
        // 空の配列
        const emptyArray = [];
        results.push('<h2>空の配列</h2>');
        results.push(`<p>要素数: ${emptyArray.length}</p>`);
        
        // 最後の要素にアクセス
        results.push('<h2>最後の要素にアクセス</h2>');
        results.push(`<p>配列: [${fruits.join(', ')}]</p>`);
        results.push(`<p>最後の要素: ${fruits[fruits.length - 1]}</p>`);
        
        // 要素の追加による自動更新
        results.push('<h2>要素の追加による自動更新</h2>');
        const numbers = [1, 2, 3];
        results.push(`<p>追加前: [${numbers.join(', ')}]（要素数: ${numbers.length}）</p>`);
        
        numbers.push(4);
        results.push(`<p>追加後: [${numbers.join(', ')}]（要素数: ${numbers.length}）</p>`);
        
        // 空の配列かチェック
        results.push('<h2>空の配列かチェック</h2>');
        
        function isEmpty(arr) {
            return arr.length === 0;
        }
        
        results.push(`<p>空の配列: ${isEmpty([])}</p>`);
        results.push(`<p>要素がある配列: ${isEmpty([1, 2, 3])}</p>`);
        
        // 実践例: 配列のループ
        results.push('<h2>実践例: 配列のループ</h2>');
        
        const items = ['りんご', 'バナナ', 'オレンジ'];
        results.push(`<p>配列: [${items.join(', ')}]（要素数: ${items.length}）</p>`);
        results.push('<ul>');
        for (let i = 0; i < items.length; i++) {
            results.push(`<li>${i + 1}. ${items[i]}</li>`);
        }
        results.push('</ul>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== length ===');
        console.log('lengthプロパティで配列の要素数を取得できます');
        console.log('要素を追加・削除すると自動的に更新されます');
    </script>
</body>
</html>
```

---

## 配列の要素の上書き

配列の要素は、インデックスを使って値を上書きできます。

### 要素の上書きの基本

```javascript
const fruits = ['りんご', 'バナナ', 'オレンジ'];

// 要素を上書き
fruits[1] = 'みかん';
console.log(fruits); // ['りんご', 'みかん', 'オレンジ']
```

### 上書きの特徴
1. **直接代入**: `配列[インデックス] = 新しい値`で上書き
2. **既存の要素のみ**: 範囲外のインデックスに代入すると、配列が拡張される
3. **constでも可能**: `const`で宣言した配列でも要素の上書きは可能

### 実践例: 配列の要素の上書き

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>配列の要素の上書き</title>
</head>
<body>
    <h1>配列の要素の上書き</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的な上書き
        const fruits = ['りんご', 'バナナ', 'オレンジ'];
        results.push('<h2>基本的な上書き</h2>');
        results.push(`<p>元の配列: [${fruits.join(', ')}]</p>`);
        
        fruits[1] = 'みかん';
        results.push(`<p>上書き後: [${fruits.join(', ')}]</p>`);
        
        // 複数の要素を上書き
        results.push('<h2>複数の要素を上書き</h2>');
        const numbers = [1, 2, 3, 4, 5];
        results.push(`<p>元の配列: [${numbers.join(', ')}]</p>`);
        
        numbers[0] = 10;
        numbers[2] = 30;
        numbers[4] = 50;
        results.push(`<p>上書き後: [${numbers.join(', ')}]</p>`);
        
        // constで宣言した配列でも上書き可能
        results.push('<h2>constで宣言した配列でも上書き可能</h2>');
        const constArray = [1, 2, 3];
        results.push(`<p>元の配列: [${constArray.join(', ')}]</p>`);
        
        constArray[1] = 20;
        results.push(`<p>上書き後: [${constArray.join(', ')}]</p>`);
        results.push('<p>constで宣言しても、配列の要素は上書きできます</p>');
        results.push('<p>（配列自体の再代入はできません）</p>');
        
        // 範囲外のインデックスに代入
        results.push('<h2>範囲外のインデックスに代入</h2>');
        const arr = [1, 2, 3];
        results.push(`<p>元の配列: [${arr.join(', ')}]（要素数: ${arr.length}）</p>`);
        
        arr[5] = 6;
        results.push(`<p>arr[5] = 6 後: [${arr.join(', ')}]（要素数: ${arr.length}）</p>`);
        results.push(`<p>arr[3]: ${arr[3]}（undefined - 空の要素）</p>`);
        results.push(`<p>arr[4]: ${arr[4]}（undefined - 空の要素）</p>`);
        results.push('<p>範囲外のインデックスに代入すると、配列が拡張されます</p>');
        
        // 実践例: スコアの更新
        results.push('<h2>実践例: スコアの更新</h2>');
        
        const scores = [85, 90, 78, 92, 88];
        results.push(`<p>元の点数: [${scores.join(', ')}]</p>`);
        
        // 2番目のスコアを更新
        scores[1] = 95;
        results.push(`<p>更新後: [${scores.join(', ')}]</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 配列の要素の上書き ===');
        console.log('配列[インデックス] = 新しい値 で要素を上書きできます');
        console.log('constで宣言した配列でも要素の上書きは可能です');
    </script>
</body>
</html>
```

---

## constで宣言した変数の配列についての補足

`const`で宣言した配列について、重要なポイントを説明します。

### constと配列の関係

```javascript
const fruits = ['りんご', 'バナナ'];

// 要素の上書きは可能
fruits[0] = 'みかん'; // OK

// 配列自体の再代入は不可
// fruits = ['新しい配列']; // TypeError
```

### constで宣言した配列の特徴
1. **要素の変更は可能**: 配列の要素を追加・削除・変更できる
2. **配列の再代入は不可**: 配列自体を別の配列に置き換えることはできない
3. **参照の固定**: 配列への参照（メモリ上の場所）が固定される

### 実践例: constで宣言した変数の配列

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>constで宣言した変数の配列</title>
</head>
<body>
    <h1>constで宣言した変数の配列についての補足</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        const fruits = ['りんご', 'バナナ', 'オレンジ'];
        
        // 要素の上書きは可能
        results.push('<h2>要素の上書きは可能</h2>');
        results.push(`<p>元の配列: [${fruits.join(', ')}]</p>`);
        
        fruits[1] = 'みかん';
        results.push(`<p>要素を上書き: [${fruits.join(', ')}]</p>`);
        
        // 要素の追加は可能
        results.push('<h2>要素の追加は可能</h2>');
        fruits.push('ぶどう');
        results.push(`<p>要素を追加: [${fruits.join(', ')}]</p>`);
        
        // 要素の削除は可能
        results.push('<h2>要素の削除は可能</h2>');
        fruits.pop();
        results.push(`<p>要素を削除: [${fruits.join(', ')}]</p>`);
        
        // 配列の再代入は不可
        results.push('<h2>配列の再代入は不可</h2>');
        results.push('<p>constで宣言した配列は、配列自体を再代入できません</p>');
        // fruits = ['新しい配列']; // TypeError
        
        // 理由の説明
        results.push('<h2>理由の説明</h2>');
        results.push('<p>constは「参照（メモリ上の場所）」を固定します</p>');
        results.push('<p>• 配列の要素を変更する → OK（同じ配列を変更）</p>');
        results.push('<p>• 配列自体を再代入する → NG（別の配列に置き換える）</p>');
        
        // 実践例: constとletの比較
        results.push('<h2>constとletの比較</h2>');
        
        const constArray = [1, 2, 3];
        let letArray = [1, 2, 3];
        
        // どちらも要素の変更は可能
        constArray[0] = 10;
        letArray[0] = 10;
        results.push(`<p>const配列: [${constArray.join(', ')}]（要素変更OK）</p>`);
        results.push(`<p>let配列: [${letArray.join(', ')}]（要素変更OK）</p>`);
        
        // 配列の再代入
        // constArray = [4, 5, 6]; // TypeError
        letArray = [4, 5, 6]; // OK
        results.push(`<p>let配列を再代入: [${letArray.join(', ')}]（再代入OK）</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== constで宣言した変数の配列 ===');
        console.log('constで宣言した配列でも、要素の変更は可能です');
        console.log('配列自体の再代入はできません');
    </script>
</body>
</html>
```

---

## push 配列の要素を追加

`push`メソッドは、配列の**末尾**に要素を追加します。

### pushの基本

```javascript
const fruits = ['りんご', 'バナナ'];
fruits.push('オレンジ');
console.log(fruits); // ['りんご', 'バナナ', 'オレンジ']
```

### pushの特徴
1. **末尾に追加**: 配列の最後に要素が追加される
2. **複数の要素を追加可能**: 複数の引数を渡せる
3. **新しい長さを返す**: 追加後の配列の長さが返される

### pushの使い方

```javascript
const arr = [1, 2, 3];

// 1つの要素を追加
arr.push(4);
console.log(arr); // [1, 2, 3, 4]

// 複数の要素を追加
arr.push(5, 6);
console.log(arr); // [1, 2, 3, 4, 5, 6]

// 戻り値（新しい長さ）
const newLength = arr.push(7);
console.log(newLength); // 7
```

### 実践例: pushの使い方

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>push 配列の要素を追加</title>
</head>
<body>
    <h1>push - 配列の要素を追加</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的なpush
        const fruits = ['りんご', 'バナナ'];
        results.push('<h2>基本的なpush</h2>');
        results.push(`<p>元の配列: [${fruits.join(', ')}]</p>`);
        
        fruits.push('オレンジ');
        results.push(`<p>push後: [${fruits.join(', ')}]</p>`);
        
        // 複数の要素を追加
        results.push('<h2>複数の要素を追加</h2>');
        const numbers = [1, 2, 3];
        results.push(`<p>元の配列: [${numbers.join(', ')}]</p>`);
        
        numbers.push(4, 5, 6);
        results.push(`<p>複数push後: [${numbers.join(', ')}]</p>`);
        
        // 戻り値（新しい長さ）
        results.push('<h2>戻り値（新しい長さ）</h2>');
        const arr = [1, 2, 3];
        const newLength = arr.push(4);
        results.push(`<p>配列: [${arr.join(', ')}]</p>`);
        results.push(`<p>新しい長さ: ${newLength}</p>`);
        
        // 実践例: 買い物リストに追加
        results.push('<h2>実践例: 買い物リストに追加</h2>');
        
        const shoppingList = ['りんご', 'バナナ'];
        results.push(`<p>買い物リスト: [${shoppingList.join(', ')}]</p>`);
        
        shoppingList.push('オレンジ', 'ぶどう');
        results.push(`<p>追加後: [${shoppingList.join(', ')}]</p>`);
        
        // 実践例: スコアの追加
        results.push('<h2>実践例: スコアの追加</h2>');
        
        const scores = [85, 90, 78];
        results.push(`<p>元の点数: [${scores.join(', ')}]</p>`);
        
        scores.push(92, 88);
        results.push(`<p>新しい点数を追加: [${scores.join(', ')}]</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== push ===');
        console.log('pushメソッドで配列の末尾に要素を追加できます');
        console.log('複数の要素を一度に追加することもできます');
    </script>
</body>
</html>
```

---

## shift 配列の先頭を削除

`shift`メソッドは、配列の**先頭**の要素を削除し、その要素を返します。

### shiftの基本

```javascript
const fruits = ['りんご', 'バナナ', 'オレンジ'];
const first = fruits.shift();
console.log(first);  // 'りんご'
console.log(fruits); // ['バナナ', 'オレンジ']
```

### shiftの特徴
1. **先頭を削除**: 配列の最初の要素が削除される
2. **要素を返す**: 削除された要素が返される
3. **配列を変更**: 元の配列が変更される（破壊的メソッド）
4. **空の配列**: 空の配列で呼び出すと`undefined`が返される

### shiftの使い方

```javascript
const arr = [1, 2, 3, 4, 5];

// 先頭を削除
const removed = arr.shift();
console.log(removed); // 1
console.log(arr);     // [2, 3, 4, 5]

// 空の配列
const empty = [];
const result = empty.shift();
console.log(result); // undefined
```

### 実践例: shiftの使い方

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>shift 配列の先頭を削除</title>
</head>
<body>
    <h1>shift - 配列の先頭を削除</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的なshift
        const fruits = ['りんご', 'バナナ', 'オレンジ'];
        results.push('<h2>基本的なshift</h2>');
        results.push(`<p>元の配列: [${fruits.join(', ')}]</p>`);
        
        const removed = fruits.shift();
        results.push(`<p>削除された要素: ${removed}</p>`);
        results.push(`<p>shift後: [${fruits.join(', ')}]</p>`);
        
        // 複数回shift
        results.push('<h2>複数回shift</h2>');
        const numbers = [1, 2, 3, 4, 5];
        results.push(`<p>元の配列: [${numbers.join(', ')}]</p>`);
        
        numbers.shift();
        results.push(`<p>1回目shift後: [${numbers.join(', ')}]</p>`);
        
        numbers.shift();
        results.push(`<p>2回目shift後: [${numbers.join(', ')}]</p>`);
        
        // 空の配列
        results.push('<h2>空の配列</h2>');
        const empty = [];
        const result = empty.shift();
        results.push(`<p>空の配列でshift: ${result}（undefined）</p>`);
        
        // 実践例: キュー（待ち行列）の実装
        results.push('<h2>実践例: キュー（待ち行列）の実装</h2>');
        
        const queue = [];
        results.push('<p>キューに追加:</p>');
        queue.push('タスク1');
        queue.push('タスク2');
        queue.push('タスク3');
        results.push(`<p>キュー: [${queue.join(', ')}]</p>`);
        
        results.push('<p>キューから処理:</p>');
        const task1 = queue.shift();
        results.push(`<p>処理中: ${task1}</p>`);
        results.push(`<p>残りのキュー: [${queue.join(', ')}]</p>`);
        
        const task2 = queue.shift();
        results.push(`<p>処理中: ${task2}</p>`);
        results.push(`<p>残りのキュー: [${queue.join(', ')}]</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== shift ===');
        console.log('shiftメソッドで配列の先頭の要素を削除できます');
        console.log('削除された要素が返されます');
    </script>
</body>
</html>
```

---

## pop 配列の末尾を削除

`pop`メソッドは、配列の**末尾**の要素を削除し、その要素を返します。

### popの基本

```javascript
const fruits = ['りんご', 'バナナ', 'オレンジ'];
const last = fruits.pop();
console.log(last);   // 'オレンジ'
console.log(fruits); // ['りんご', 'バナナ']
```

### popの特徴
1. **末尾を削除**: 配列の最後の要素が削除される
2. **要素を返す**: 削除された要素が返される
3. **配列を変更**: 元の配列が変更される（破壊的メソッド）
4. **空の配列**: 空の配列で呼び出すと`undefined`が返される

### popの使い方

```javascript
const arr = [1, 2, 3, 4, 5];

// 末尾を削除
const removed = arr.pop();
console.log(removed); // 5
console.log(arr);     // [1, 2, 3, 4]

// 空の配列
const empty = [];
const result = empty.pop();
console.log(result); // undefined
```

### 実践例: popの使い方

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>pop 配列の末尾を削除</title>
</head>
<body>
    <h1>pop - 配列の末尾を削除</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的なpop
        const fruits = ['りんご', 'バナナ', 'オレンジ'];
        results.push('<h2>基本的なpop</h2>');
        results.push(`<p>元の配列: [${fruits.join(', ')}]</p>`);
        
        const removed = fruits.pop();
        results.push(`<p>削除された要素: ${removed}</p>`);
        results.push(`<p>pop後: [${fruits.join(', ')}]</p>`);
        
        // 複数回pop
        results.push('<h2>複数回pop</h2>');
        const numbers = [1, 2, 3, 4, 5];
        results.push(`<p>元の配列: [${numbers.join(', ')}]</p>`);
        
        numbers.pop();
        results.push(`<p>1回目pop後: [${numbers.join(', ')}]</p>`);
        
        numbers.pop();
        results.push(`<p>2回目pop後: [${numbers.join(', ')}]</p>`);
        
        // 空の配列
        results.push('<h2>空の配列</h2>');
        const empty = [];
        const result = empty.pop();
        results.push(`<p>空の配列でpop: ${result}（undefined）</p>`);
        
        // 実践例: スタック（後入れ先出し）の実装
        results.push('<h2>実践例: スタック（後入れ先出し）の実装</h2>');
        
        const stack = [];
        results.push('<p>スタックに追加:</p>');
        stack.push('データ1');
        stack.push('データ2');
        stack.push('データ3');
        results.push(`<p>スタック: [${stack.join(', ')}]</p>`);
        
        results.push('<p>スタックから取り出し:</p>');
        const data3 = stack.pop();
        results.push(`<p>取り出し: ${data3}</p>`);
        results.push(`<p>残りのスタック: [${stack.join(', ')}]</p>`);
        
        const data2 = stack.pop();
        results.push(`<p>取り出し: ${data2}</p>`);
        results.push(`<p>残りのスタック: [${stack.join(', ')}]</p>`);
        
        // pushとpopの組み合わせ
        results.push('<h2>pushとpopの組み合わせ</h2>');
        results.push('<p>push: 末尾に追加</p>');
        results.push('<p>pop: 末尾を削除</p>');
        results.push('<p>→ スタック（後入れ先出し）の実装に適している</p>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== pop ===');
        console.log('popメソッドで配列の末尾の要素を削除できます');
        console.log('削除された要素が返されます');
    </script>
</body>
</html>
```

---

## 多次元配列

多次元配列は、配列の中に配列を入れた構造です。

### 多次元配列の基本

```javascript
// 2次元配列（配列の配列）
const matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];

console.log(matrix[0][0]); // 1（1行目の1列目）
console.log(matrix[1][2]); // 6（2行目の3列目）
```

### 多次元配列の特徴
1. **配列の中に配列**: 配列の要素が配列になっている
2. **2次元、3次元...**: 何次元でも作れる
3. **表形式のデータ**: 表や行列を表現できる

### 実践例: 多次元配列

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>多次元配列</title>
</head>
<body>
    <h1>多次元配列</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 2次元配列（表形式）
        results.push('<h2>2次元配列（表形式）</h2>');
        
        const table = [
            [1, 2, 3],
            [4, 5, 6],
            [7, 8, 9]
        ];
        
        results.push('<p>2次元配列:</p>');
        results.push('<table border="1" style="border-collapse: collapse;">');
        for (let i = 0; i < table.length; i++) {
            results.push('<tr>');
            for (let j = 0; j < table[i].length; j++) {
                results.push(`<td>${table[i][j]}</td>`);
            }
            results.push('</tr>');
        }
        results.push('</table>');
        
        results.push(`<p>table[0][0]: ${table[0][0]}（1行目の1列目）</p>`);
        results.push(`<p>table[1][2]: ${table[1][2]}（2行目の3列目）</p>`);
        
        // 実践例: 成績表
        results.push('<h2>実践例: 成績表</h2>');
        
        const scores = [
            ['太郎', 85, 90, 78],
            ['花子', 92, 88, 95],
            ['次郎', 78, 85, 80]
        ];
        
        results.push('<p>成績表:</p>');
        results.push('<table border="1" style="border-collapse: collapse;">');
        results.push('<tr><th>名前</th><th>国語</th><th>数学</th><th>英語</th></tr>');
        for (let i = 0; i < scores.length; i++) {
            results.push('<tr>');
            for (let j = 0; j < scores[i].length; j++) {
                results.push(`<td>${scores[i][j]}</td>`);
            }
            results.push('</tr>');
        }
        results.push('</table>');
        
        // 3次元配列
        results.push('<h2>3次元配列</h2>');
        
        const cube = [
            [
                [1, 2],
                [3, 4]
            ],
            [
                [5, 6],
                [7, 8]
            ]
        ];
        
        results.push(`<p>cube[0][0][0]: ${cube[0][0][0]}</p>`);
        results.push(`<p>cube[1][1][1]: ${cube[1][1][1]}</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 多次元配列 ===');
        console.log('配列の中に配列を入れることで、多次元配列を作れます');
        console.log('表形式のデータを表現するのに便利です');
    </script>
</body>
</html>
```

---

## 分割代入

分割代入（destructuring）は、配列やオブジェクトから値を取り出して、変数に代入する構文です。

### 配列の分割代入の基本

```javascript
const fruits = ['りんご', 'バナナ', 'オレンジ'];

// 分割代入
const [first, second, third] = fruits;
console.log(first);  // 'りんご'
console.log(second); // 'バナナ'
console.log(third);  // 'オレンジ'
```

### 分割代入の特徴
1. **簡潔な記述**: 複数の変数に一度に代入できる
2. **スキップ可能**: 不要な要素をスキップできる
3. **デフォルト値**: 値が存在しない場合のデフォルト値を設定できる

### 分割代入の使い方

```javascript
const arr = [1, 2, 3];

// 基本的な分割代入
const [a, b, c] = arr;
console.log(a, b, c); // 1, 2, 3

// 一部だけ取り出す
const [x, y] = arr;
console.log(x, y); // 1, 2

// スキップ
const [first, , third] = arr;
console.log(first, third); // 1, 3

// デフォルト値
const [p, q, r = 0] = [1, 2];
console.log(p, q, r); // 1, 2, 0
```

### 実践例: 分割代入

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>分割代入</title>
</head>
<body>
    <h1>分割代入</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的な分割代入
        results.push('<h2>基本的な分割代入</h2>');
        
        const fruits = ['りんご', 'バナナ', 'オレンジ'];
        const [first, second, third] = fruits;
        
        results.push(`<p>配列: [${fruits.join(', ')}]</p>`);
        results.push(`<p>first: ${first}</p>`);
        results.push(`<p>second: ${second}</p>`);
        results.push(`<p>third: ${third}</p>`);
        
        // 一部だけ取り出す
        results.push('<h2>一部だけ取り出す</h2>');
        
        const numbers = [1, 2, 3, 4, 5];
        const [a, b] = numbers;
        
        results.push(`<p>配列: [${numbers.join(', ')}]</p>`);
        results.push(`<p>a: ${a}, b: ${b}</p>`);
        
        // スキップ
        results.push('<h2>スキップ</h2>');
        
        const [x, , z] = numbers;
        results.push(`<p>配列: [${numbers.join(', ')}]</p>`);
        results.push(`<p>x: ${x}, z: ${z}（2番目をスキップ）</p>`);
        
        // デフォルト値
        results.push('<h2>デフォルト値</h2>');
        
        const [p, q, r = 0] = [1, 2];
        results.push(`<p>配列: [1, 2]</p>`);
        results.push(`<p>p: ${p}, q: ${q}, r: ${r}（rはデフォルト値）</p>`);
        
        // 実践例: 関数の戻り値
        results.push('<h2>実践例: 関数の戻り値</h2>');
        
        function getMinMax(numbers) {
            return [Math.min(...numbers), Math.max(...numbers)];
        }
        
        const [min, max] = getMinMax([10, 5, 20, 15]);
        results.push(`<p>配列: [10, 5, 20, 15]</p>`);
        results.push(`<p>最小値: ${min}, 最大値: ${max}</p>`);
        
        // 実践例: 値の入れ替え
        results.push('<h2>実践例: 値の入れ替え</h2>');
        
        let a1 = 10;
        let a2 = 20;
        
        results.push(`<p>入れ替え前: a1=${a1}, a2=${a2}</p>`);
        
        [a1, a2] = [a2, a1];
        
        results.push(`<p>入れ替え後: a1=${a1}, a2=${a2}</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 分割代入 ===');
        console.log('配列から値を取り出して、変数に代入できます');
        console.log('コードが簡潔になり、読みやすくなります');
    </script>
</body>
</html>
```

---

## スプレッド構文/レストパラメータ

スプレッド構文（`...`）は、配列を展開したり、複数の値をまとめたりする構文です。

### スプレッド構文の基本

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// 配列を展開
const combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]
```

### スプレッド構文の使い方

#### 1. 配列のコピー
```javascript
const original = [1, 2, 3];
const copy = [...original];
```

#### 2. 配列の結合
```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4]
```

#### 3. 配列への要素の追加
```javascript
const arr = [1, 2, 3];
const newArr = [0, ...arr, 4]; // [0, 1, 2, 3, 4]
```

### レストパラメータ

レストパラメータは、関数の引数を配列として受け取る構文です。

```javascript
function sum(...numbers) {
    return numbers.reduce((acc, n) => acc + n, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15
```

### 実践例: スプレッド構文/レストパラメータ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>スプレッド構文/レストパラメータ</title>
</head>
<body>
    <h1>スプレッド構文/レストパラメータ</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 配列のコピー
        results.push('<h2>配列のコピー</h2>');
        
        const original = [1, 2, 3];
        const copy = [...original];
        
        results.push(`<p>元の配列: [${original.join(', ')}]</p>`);
        results.push(`<p>コピー: [${copy.join(', ')}]</p>`);
        
        copy.push(4);
        results.push(`<p>コピーに追加後: [${copy.join(', ')}]</p>`);
        results.push(`<p>元の配列: [${original.join(', ')}]（変更されない）</p>`);
        
        // 配列の結合
        results.push('<h2>配列の結合</h2>');
        
        const arr1 = [1, 2, 3];
        const arr2 = [4, 5, 6];
        const combined = [...arr1, ...arr2];
        
        results.push(`<p>arr1: [${arr1.join(', ')}]</p>`);
        results.push(`<p>arr2: [${arr2.join(', ')}]</p>`);
        results.push(`<p>結合後: [${combined.join(', ')}]</p>`);
        
        // 配列への要素の追加
        results.push('<h2>配列への要素の追加</h2>');
        
        const arr = [2, 3, 4];
        const newArr = [1, ...arr, 5];
        
        results.push(`<p>元の配列: [${arr.join(', ')}]</p>`);
        results.push(`<p>前後に追加: [${newArr.join(', ')}]</p>`);
        
        // レストパラメータ
        results.push('<h2>レストパラメータ</h2>');
        
        function sum(...numbers) {
            return numbers.reduce((acc, n) => acc + n, 0);
        }
        
        results.push(`<p>sum(1, 2, 3): ${sum(1, 2, 3)}</p>`);
        results.push(`<p>sum(1, 2, 3, 4, 5): ${sum(1, 2, 3, 4, 5)}</p>`);
        
        // 実践例: 最大値・最小値
        results.push('<h2>実践例: 最大値・最小値</h2>');
        
        function getMax(...numbers) {
            return Math.max(...numbers);
        }
        
        function getMin(...numbers) {
            return Math.min(...numbers);
        }
        
        results.push(`<p>getMax(10, 5, 20, 15): ${getMax(10, 5, 20, 15)}</p>`);
        results.push(`<p>getMin(10, 5, 20, 15): ${getMin(10, 5, 20, 15)}</p>`);
        
        // 実践例: 配列の展開
        results.push('<h2>実践例: 配列の展開</h2>');
        
        const numbers = [1, 2, 3];
        results.push(`<p>配列: [${numbers.join(', ')}]</p>`);
        results.push(`<p>Math.max(...numbers): ${Math.max(...numbers)}</p>`);
        results.push(`<p>Math.min(...numbers): ${Math.min(...numbers)}</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== スプレッド構文/レストパラメータ ===');
        console.log('スプレッド構文で配列を展開できます');
        console.log('レストパラメータで複数の引数を配列として受け取れます');
    </script>
</body>
</html>
```

---

## まとめ

この章では、配列について学びました。

### 学んだこと
- 配列の概念と使い方
- インデックスによる要素へのアクセス
- `length`プロパティで要素数を取得
- 要素の上書き
- `const`で宣言した配列の特徴
- `push`で要素を追加
- `shift`で先頭を削除
- `pop`で末尾を削除
- 多次元配列
- 分割代入
- スプレッド構文/レストパラメータ

### 重要なポイント
1. **インデックスは0から始まる**: 最初の要素のインデックスは0
2. **`length`で要素数を取得**: `配列.length`で要素数が分かる
3. **`const`でも要素は変更可能**: 配列自体の再代入は不可だが、要素の変更は可能
4. **`push`/`pop`/`shift`**: 配列の操作に便利なメソッド
5. **分割代入とスプレッド構文**: コードを簡潔に書ける

### 次のステップ
次の章では、オブジェクトについて詳しく学びます。

---

## 演習問題

### 問題1: 配列の作成とアクセス
配列`['りんご', 'バナナ', 'オレンジ']`を作成し、2番目の要素（インデックス1）を表示してください。

<details>
<summary>解答例</summary>

```javascript
const fruits = ['りんご', 'バナナ', 'オレンジ'];
console.log(fruits[1]); // 'バナナ'
```
</details>

### 問題2: 配列の要素を追加
配列`[1, 2, 3]`に`push`メソッドを使って、`4`と`5`を追加してください。

<details>
<summary>解答例</summary>

```javascript
const numbers = [1, 2, 3];
numbers.push(4, 5);
console.log(numbers); // [1, 2, 3, 4, 5]
```
</details>

### 問題3: 配列の要素を削除
配列`['りんご', 'バナナ', 'オレンジ']`から、`pop`メソッドで最後の要素を削除してください。

<details>
<summary>解答例</summary>

```javascript
const fruits = ['りんご', 'バナナ', 'オレンジ'];
const removed = fruits.pop();
console.log(removed); // 'オレンジ'
console.log(fruits);  // ['りんご', 'バナナ']
```
</details>

### 問題4: 分割代入
配列`[10, 20, 30]`から分割代入を使って、最初の要素と最後の要素を取り出してください。

<details>
<summary>解答例</summary>

```javascript
const numbers = [10, 20, 30];
const [first, , last] = numbers;
console.log(first); // 10
console.log(last);  // 30
```
</details>

### 問題5: スプレッド構文
2つの配列`[1, 2, 3]`と`[4, 5, 6]`をスプレッド構文で結合してください。

<details>
<summary>解答例</summary>

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]
```
</details>

---

お疲れ様でした！次の章に進みましょう。

