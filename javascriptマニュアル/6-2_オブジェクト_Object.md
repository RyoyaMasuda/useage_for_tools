# 6-2. オブジェクト (Object)

この章では、名前と値のペアでデータを管理する「オブジェクト」について学びます。オブジェクトを理解することで、関連するデータを整理して管理できるようになります。

---

## 目次

- [オブジェクト - 名前と値のペアでデータを管理](#オブジェクト---名前と値のペアでデータを管理)
- [オブジェクトとは](#オブジェクトとは)
- [オブジェクト プロパティの参照](#オブジェクト-プロパティの参照)
- [オブジェクト プロパティの追加と上書き](#オブジェクト-プロパティの追加と上書き)
- [データ型（後編）- オブジェクト型と参照の仕組み](#データ型後編---オブジェクト型と参照の仕組み)
- [配列とオブジェクトにおける変数のコピー](#配列とオブジェクトにおける変数のコピー)
- [配列とオブジェクトの組み合わせ](#配列とオブジェクトの組み合わせ)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## オブジェクト - 名前と値のペアでデータを管理

### オブジェクトの概念
オブジェクト（Object）は、**名前（キー）と値（バリュー）のペア**でデータを管理するデータ構造です。

### 日常生活での例
- **名刺**: 名前、電話番号、メールアドレス...（関連する情報を1つにまとめる）
- **辞書**: 単語と意味のペア（キーと値の関係）
- **住所録**: 名前と住所のペア

### プログラミングでの例
```javascript
// オブジェクトの作成
const person = {
    name: '太郎',
    age: 25,
    city: '東京'
};

// プロパティにアクセス
console.log(person.name); // '太郎'
console.log(person.age);  // 25
```

### オブジェクトを使う理由
1. **関連データの整理**: 関連する情報を1つにまとめられる
2. **意味のある名前**: インデックスではなく、意味のある名前でアクセスできる
3. **柔軟な構造**: 必要な情報だけを含められる
4. **データの表現**: 現実世界の「もの」を表現しやすい

### 実践例: オブジェクトの基本概念

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>オブジェクトの基本概念</title>
</head>
<body>
    <h1>オブジェクト - 名前と値のペアでデータを管理</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // オブジェクトを使わない場合（複数の変数が必要）
        results.push('<h2>オブジェクトを使わない場合</h2>');
        const userName = '太郎';
        const userAge = 25;
        const userCity = '東京';
        results.push(`<p>名前: ${userName}, 年齢: ${userAge}, 居住地: ${userCity}</p>`);
        results.push('<p>複数の変数が必要で、管理が大変</p>');
        
        // オブジェクトを使う場合（1つのオブジェクトで管理）
        results.push('<h2>オブジェクトを使う場合</h2>');
        const person = {
            name: '太郎',
            age: 25,
            city: '東京'
        };
        results.push(`<p>名前: ${person.name}, 年齢: ${person.age}, 居住地: ${person.city}</p>`);
        results.push('<p>1つのオブジェクトで関連するデータを管理できる</p>');
        
        // オブジェクトの構造
        results.push('<h2>オブジェクトの構造</h2>');
        results.push('<p>オブジェクトは「名前（キー）」と「値（バリュー）」のペアで構成されます</p>');
        results.push('<p>例: name: "太郎" → nameがキー、"太郎"が値</p>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== オブジェクトの基本 ===');
        console.log('オブジェクトを使うことで、関連するデータを効率的に管理できます');
    </script>
</body>
</html>
```

---

## オブジェクトとは

### オブジェクトの定義
オブジェクトは、**プロパティ（名前と値のペア）の集合**です。

### オブジェクトの作成方法

#### 方法1: オブジェクトリテラル（推奨）
```javascript
const person = {
    name: '太郎',
    age: 25
};
```

#### 方法2: Objectコンストラクタ
```javascript
const person = new Object();
person.name = '太郎';
person.age = 25;
```

### オブジェクトの特徴
1. **プロパティ**: 名前（キー）と値（バリュー）のペア
2. **動的な構造**: 後からプロパティを追加・削除できる
3. **様々な値**: 文字列、数値、真偽値、配列、オブジェクトなど、どんな値でも格納できる
4. **メソッド**: 関数もプロパティとして持てる

### 実践例: オブジェクトとは

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>オブジェクトとは</title>
</head>
<body>
    <h1>オブジェクトとは</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // オブジェクトリテラル（推奨）
        const person1 = {
            name: '太郎',
            age: 25,
            city: '東京'
        };
        results.push('<h2>オブジェクトリテラル（推奨）</h2>');
        results.push(`<p>名前: ${person1.name}, 年齢: ${person1.age}, 居住地: ${person1.city}</p>`);
        
        // Objectコンストラクタ
        const person2 = new Object();
        person2.name = '花子';
        person2.age = 30;
        person2.city = '大阪';
        results.push('<h2>Objectコンストラクタ</h2>');
        results.push(`<p>名前: ${person2.name}, 年齢: ${person2.age}, 居住地: ${person2.city}</p>`);
        
        // 様々な型の値
        results.push('<h2>様々な型の値</h2>');
        
        const mixedObject = {
            string: '文字列',
            number: 42,
            boolean: true,
            array: [1, 2, 3],
            object: { nested: 'ネストされたオブジェクト' },
            null: null,
            undefined: undefined
        };
        
        results.push(`<p>文字列: ${mixedObject.string}</p>`);
        results.push(`<p>数値: ${mixedObject.number}</p>`);
        results.push(`<p>真偽値: ${mixedObject.boolean}</p>`);
        results.push(`<p>配列: [${mixedObject.array.join(', ')}]</p>`);
        results.push(`<p>オブジェクト: ${JSON.stringify(mixedObject.object)}</p>`);
        
        // メソッド（関数）を持つオブジェクト
        results.push('<h2>メソッド（関数）を持つオブジェクト</h2>');
        
        const calculator = {
            add: function(a, b) {
                return a + b;
            },
            subtract: function(a, b) {
                return a - b;
            }
        };
        
        results.push(`<p>10 + 5 = ${calculator.add(10, 5)}</p>`);
        results.push(`<p>10 - 5 = ${calculator.subtract(10, 5)}</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== オブジェクトとは ===');
        console.log('オブジェクトはプロパティ（名前と値のペア）の集合です');
        console.log('オブジェクトリテラル {} を使うことが推奨されます');
    </script>
</body>
</html>
```

---

## オブジェクト プロパティの参照

オブジェクトのプロパティにアクセスする方法を学びます。

### プロパティへのアクセス方法

#### 方法1: ドット記法（推奨）
```javascript
const person = {
    name: '太郎',
    age: 25
};

console.log(person.name); // '太郎'
console.log(person.age);  // 25
```

#### 方法2: ブラケット記法
```javascript
const person = {
    name: '太郎',
    age: 25
};

console.log(person['name']); // '太郎'
console.log(person['age']);  // 25
```

### ドット記法とブラケット記法の使い分け

- **ドット記法**: プロパティ名が固定の場合（推奨）
- **ブラケット記法**: プロパティ名が変数の場合、または特殊な文字を含む場合

```javascript
const person = { name: '太郎' };
const propName = 'name';

// ドット記法
console.log(person.name); // '太郎'

// ブラケット記法（変数を使う場合）
console.log(person[propName]); // '太郎'

// ブラケット記法（特殊な文字を含む場合）
const obj = { 'first-name': '太郎' };
// console.log(obj.first-name); // エラー
console.log(obj['first-name']); // '太郎'
```

### 実践例: オブジェクト プロパティの参照

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>オブジェクト プロパティの参照</title>
</head>
<body>
    <h1>オブジェクト プロパティの参照</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        const person = {
            name: '太郎',
            age: 25,
            city: '東京',
            email: 'taro@example.com'
        };
        
        // ドット記法（推奨）
        results.push('<h2>ドット記法（推奨）</h2>');
        results.push(`<p>person.name: ${person.name}</p>`);
        results.push(`<p>person.age: ${person.age}</p>`);
        results.push(`<p>person.city: ${person.city}</p>`);
        
        // ブラケット記法
        results.push('<h2>ブラケット記法</h2>');
        results.push(`<p>person['name']: ${person['name']}</p>`);
        results.push(`<p>person['age']: ${person['age']}</p>`);
        results.push(`<p>person['city']: ${person['city']}</p>`);
        
        // 変数を使ったアクセス
        results.push('<h2>変数を使ったアクセス</h2>');
        
        const propName = 'name';
        results.push(`<p>person[propName]: ${person[propName]}</p>`);
        results.push('<p>ブラケット記法は、プロパティ名が変数の場合に便利</p>');
        
        // 存在しないプロパティ
        results.push('<h2>存在しないプロパティ</h2>');
        results.push(`<p>person.phone: ${person.phone}（undefined）</p>`);
        results.push(`<p>person['phone']: ${person['phone']}（undefined）</p>`);
        
        // 実践例: プロパティの動的アクセス
        results.push('<h2>実践例: プロパティの動的アクセス</h2>');
        
        function getProperty(obj, propName) {
            return obj[propName];
        }
        
        results.push(`<p>getProperty(person, 'name'): ${getProperty(person, 'name')}</p>`);
        results.push(`<p>getProperty(person, 'age'): ${getProperty(person, 'age')}</p>`);
        
        // すべてのプロパティを表示
        results.push('<h2>すべてのプロパティを表示</h2>');
        results.push('<ul>');
        for (const key in person) {
            results.push(`<li>${key}: ${person[key]}</li>`);
        }
        results.push('</ul>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== オブジェクト プロパティの参照 ===');
        console.log('ドット記法とブラケット記法でプロパティにアクセスできます');
        console.log('ドット記法が推奨されますが、変数を使う場合はブラケット記法が必要です');
    </script>
</body>
</html>
```

---

## オブジェクト プロパティの追加と上書き

オブジェクトのプロパティを追加したり、既存のプロパティを上書きする方法を学びます。

### プロパティの追加

```javascript
const person = {
    name: '太郎'
};

// プロパティを追加
person.age = 25;
person.city = '東京';

console.log(person); // { name: '太郎', age: 25, city: '東京' }
```

### プロパティの上書き

```javascript
const person = {
    name: '太郎',
    age: 25
};

// プロパティを上書き
person.age = 30;
console.log(person); // { name: '太郎', age: 30 }
```

### プロパティの削除

```javascript
const person = {
    name: '太郎',
    age: 25
};

// プロパティを削除
delete person.age;
console.log(person); // { name: '太郎' }
```

### 実践例: オブジェクト プロパティの追加と上書き

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>オブジェクト プロパティの追加と上書き</title>
</head>
<body>
    <h1>オブジェクト プロパティの追加と上書き</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // プロパティの追加
        results.push('<h2>プロパティの追加</h2>');
        
        const person = {
            name: '太郎'
        };
        results.push(`<p>初期状態: ${JSON.stringify(person)}</p>`);
        
        person.age = 25;
        person.city = '東京';
        results.push(`<p>プロパティ追加後: ${JSON.stringify(person)}</p>`);
        
        // プロパティの上書き
        results.push('<h2>プロパティの上書き</h2>');
        
        const user = {
            name: '太郎',
            age: 25
        };
        results.push(`<p>元のオブジェクト: ${JSON.stringify(user)}</p>`);
        
        user.age = 30;
        results.push(`<p>ageを上書き: ${JSON.stringify(user)}</p>`);
        
        // プロパティの削除
        results.push('<h2>プロパティの削除</h2>');
        
        const obj = {
            a: 1,
            b: 2,
            c: 3
        };
        results.push(`<p>削除前: ${JSON.stringify(obj)}</p>`);
        
        delete obj.b;
        results.push(`<p>削除後: ${JSON.stringify(obj)}</p>`);
        
        // ドット記法とブラケット記法
        results.push('<h2>ドット記法とブラケット記法</h2>');
        
        const data = {};
        
        // ドット記法で追加
        data.name = '太郎';
        data.age = 25;
        results.push(`<p>ドット記法で追加: ${JSON.stringify(data)}</p>`);
        
        // ブラケット記法で追加
        data['city'] = '東京';
        data['email'] = 'taro@example.com';
        results.push(`<p>ブラケット記法で追加: ${JSON.stringify(data)}</p>`);
        
        // 実践例: ユーザー情報の更新
        results.push('<h2>実践例: ユーザー情報の更新</h2>');
        
        const userInfo = {
            name: '太郎',
            age: 25
        };
        results.push(`<p>初期情報: ${JSON.stringify(userInfo)}</p>`);
        
        // 情報を追加
        userInfo.email = 'taro@example.com';
        userInfo.phone = '090-1234-5678';
        results.push(`<p>情報追加後: ${JSON.stringify(userInfo)}</p>`);
        
        // 情報を更新
        userInfo.age = 26;
        results.push(`<p>情報更新後: ${JSON.stringify(userInfo)}</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== オブジェクト プロパティの追加と上書き ===');
        console.log('プロパティを追加・上書き・削除できます');
        console.log('ドット記法とブラケット記法の両方を使えます');
    </script>
</body>
</html>
```

---

## データ型（後編）- オブジェクト型と参照の仕組み

オブジェクト型は、プリミティブ型とは異なる「参照」の仕組みを持ちます。

### プリミティブ型とオブジェクト型の違い

#### プリミティブ型（値のコピー）
```javascript
let a = 10;
let b = a;  // 値がコピーされる
b = 20;
console.log(a); // 10（変わらない）
console.log(b); // 20
```

#### オブジェクト型（参照のコピー）
```javascript
let obj1 = { value: 10 };
let obj2 = obj1;  // 参照がコピーされる
obj2.value = 20;
console.log(obj1.value); // 20（変わってしまう！）
console.log(obj2.value); // 20
```

### 参照とは
オブジェクトは、**メモリ上の場所（参照）**を保存します。変数に代入すると、参照がコピーされるため、同じオブジェクトを指すことになります。

### 実践例: オブジェクト型と参照の仕組み

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>オブジェクト型と参照の仕組み</title>
</head>
<body>
    <h1>データ型（後編）- オブジェクト型と参照の仕組み</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // プリミティブ型（値のコピー）
        results.push('<h2>プリミティブ型（値のコピー）</h2>');
        
        let a = 10;
        let b = a;
        b = 20;
        
        results.push(`<p>a = ${a}, b = ${b}</p>`);
        results.push('<p>aは変わらない（値がコピーされる）</p>');
        
        // オブジェクト型（参照のコピー）
        results.push('<h2>オブジェクト型（参照のコピー）</h2>');
        
        let obj1 = { value: 10 };
        let obj2 = obj1;
        obj2.value = 20;
        
        results.push(`<p>obj1.value = ${obj1.value}, obj2.value = ${obj2.value}</p>`);
        results.push('<p>obj1も変わってしまう（参照がコピーされる）</p>');
        
        // 参照の確認
        results.push('<h2>参照の確認</h2>');
        
        const obj3 = { name: '太郎' };
        const obj4 = obj3;
        const obj5 = { name: '太郎' };
        
        results.push(`<p>obj3 === obj4: ${obj3 === obj4}（同じ参照）</p>`);
        results.push(`<p>obj3 === obj5: ${obj3 === obj5}（異なる参照）</p>`);
        results.push('<p>同じ値でも、別のオブジェクトは異なる参照になります</p>');
        
        // 独立したコピーを作る
        results.push('<h2>独立したコピーを作る</h2>');
        
        const original = { value: 10 };
        const copy = { ...original };  // スプレッド構文でコピー
        
        copy.value = 20;
        results.push(`<p>original.value = ${original.value}（変わらない）</p>`);
        results.push(`<p>copy.value = ${copy.value}</p>`);
        results.push('<p>スプレッド構文で独立したコピーを作れます</p>');
        
        // 実践例: 配列のコピー
        results.push('<h2>実践例: 配列のコピー</h2>');
        
        const arr1 = [1, 2, 3];
        const arr2 = arr1;  // 参照のコピー
        const arr3 = [...arr1];  // 独立したコピー
        
        arr2.push(4);
        arr3.push(5);
        
        results.push(`<p>arr1: [${arr1.join(', ')}]（arr2の変更が反映される）</p>`);
        results.push(`<p>arr2: [${arr2.join(', ')}]</p>`);
        results.push(`<p>arr3: [${arr3.join(', ')}]（独立している）</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== オブジェクト型と参照の仕組み ===');
        console.log('オブジェクト型は参照を保存します');
        console.log('参照がコピーされるため、同じオブジェクトを指すことになります');
    </script>
</body>
</html>
```

---

## 配列とオブジェクトにおける変数のコピー

配列とオブジェクトをコピーする方法を学びます。

### 参照のコピー（問題のある方法）

```javascript
const original = [1, 2, 3];
const copy = original;  // 参照がコピーされる

copy.push(4);
console.log(original); // [1, 2, 3, 4]（元も変わってしまう！）
```

### 独立したコピーを作る方法

#### 方法1: スプレッド構文（推奨）
```javascript
const original = [1, 2, 3];
const copy = [...original];  // 独立したコピー

copy.push(4);
console.log(original); // [1, 2, 3]（変わらない）
console.log(copy);      // [1, 2, 3, 4]
```

#### 方法2: Array.from()
```javascript
const original = [1, 2, 3];
const copy = Array.from(original);
```

#### 方法3: Object.assign()（オブジェクトの場合）
```javascript
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original);
```

### 実践例: 配列とオブジェクトにおける変数のコピー

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>配列とオブジェクトにおける変数のコピー</title>
</head>
<body>
    <h1>配列とオブジェクトにおける変数のコピー</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 参照のコピー（問題のある方法）
        results.push('<h2>参照のコピー（問題のある方法）</h2>');
        
        const original1 = [1, 2, 3];
        const referenceCopy = original1;
        
        referenceCopy.push(4);
        results.push(`<p>元の配列: [${original1.join(', ')}]（変わってしまう！）</p>`);
        results.push(`<p>コピー: [${referenceCopy.join(', ')}]</p>`);
        
        // スプレッド構文でコピー（推奨）
        results.push('<h2>スプレッド構文でコピー（推奨）</h2>');
        
        const original2 = [1, 2, 3];
        const independentCopy = [...original2];
        
        independentCopy.push(4);
        results.push(`<p>元の配列: [${original2.join(', ')}]（変わらない）</p>`);
        results.push(`<p>コピー: [${independentCopy.join(', ')}]</p>`);
        
        // Array.from()でコピー
        results.push('<h2>Array.from()でコピー</h2>');
        
        const original3 = [1, 2, 3];
        const copy3 = Array.from(original3);
        
        copy3.push(4);
        results.push(`<p>元の配列: [${original3.join(', ')}]（変わらない）</p>`);
        results.push(`<p>コピー: [${copy3.join(', ')}]</p>`);
        
        // オブジェクトのコピー
        results.push('<h2>オブジェクトのコピー</h2>');
        
        const originalObj = { a: 1, b: 2, c: 3 };
        const objCopy = { ...originalObj };  // スプレッド構文
        
        objCopy.d = 4;
        results.push(`<p>元のオブジェクト: ${JSON.stringify(originalObj)}（変わらない）</p>`);
        results.push(`<p>コピー: ${JSON.stringify(objCopy)}</p>`);
        
        // Object.assign()でコピー
        results.push('<h2>Object.assign()でコピー</h2>');
        
        const originalObj2 = { x: 10, y: 20 };
        const objCopy2 = Object.assign({}, originalObj2);
        
        objCopy2.z = 30;
        results.push(`<p>元のオブジェクト: ${JSON.stringify(originalObj2)}（変わらない）</p>`);
        results.push(`<p>コピー: ${JSON.stringify(objCopy2)}</p>`);
        
        // 実践例: データのバックアップ
        results.push('<h2>実践例: データのバックアップ</h2>');
        
        const userData = {
            name: '太郎',
            age: 25,
            scores: [85, 90, 78]
        };
        
        const backup = { ...userData };
        backup.scores = [...userData.scores];  // ネストされた配列もコピー
        
        userData.age = 30;
        userData.scores.push(95);
        
        results.push(`<p>元のデータ: ${JSON.stringify(userData)}</p>`);
        results.push(`<p>バックアップ: ${JSON.stringify(backup)}（変わらない）</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 配列とオブジェクトにおける変数のコピー ===');
        console.log('スプレッド構文で独立したコピーを作れます');
        console.log('ネストされたオブジェクトや配列も注意深くコピーする必要があります');
    </script>
</body>
</html>
```

---

## 配列とオブジェクトの組み合わせ

配列とオブジェクトを組み合わせることで、より複雑なデータ構造を表現できます。

### オブジェクトの配列

```javascript
const users = [
    { name: '太郎', age: 25 },
    { name: '花子', age: 30 },
    { name: '次郎', age: 20 }
];
```

### 配列を持つオブジェクト

```javascript
const person = {
    name: '太郎',
    hobbies: ['読書', '映画', '旅行']
};
```

### オブジェクトのオブジェクト

```javascript
const company = {
    name: '株式会社ABC',
    address: {
        city: '東京',
        street: '渋谷1-1-1'
    }
};
```

### 実践例: 配列とオブジェクトの組み合わせ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>配列とオブジェクトの組み合わせ</title>
</head>
<body>
    <h1>配列とオブジェクトの組み合わせ</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // オブジェクトの配列
        results.push('<h2>オブジェクトの配列</h2>');
        
        const users = [
            { name: '太郎', age: 25, city: '東京' },
            { name: '花子', age: 30, city: '大阪' },
            { name: '次郎', age: 20, city: '名古屋' }
        ];
        
        results.push('<table border="1" style="border-collapse: collapse;">');
        results.push('<tr><th>名前</th><th>年齢</th><th>居住地</th></tr>');
        for (let i = 0; i < users.length; i++) {
            results.push(`<tr><td>${users[i].name}</td><td>${users[i].age}</td><td>${users[i].city}</td></tr>`);
        }
        results.push('</table>');
        
        // 配列を持つオブジェクト
        results.push('<h2>配列を持つオブジェクト</h2>');
        
        const person = {
            name: '太郎',
            age: 25,
            hobbies: ['読書', '映画', '旅行'],
            scores: [85, 90, 78]
        };
        
        results.push(`<p>名前: ${person.name}</p>`);
        results.push(`<p>年齢: ${person.age}</p>`);
        results.push(`<p>趣味: ${person.hobbies.join(', ')}</p>`);
        results.push(`<p>点数: [${person.scores.join(', ')}]</p>`);
        
        // オブジェクトのオブジェクト
        results.push('<h2>オブジェクトのオブジェクト</h2>');
        
        const company = {
            name: '株式会社ABC',
            address: {
                city: '東京',
                street: '渋谷1-1-1',
                zipCode: '150-0001'
            },
            contact: {
                phone: '03-1234-5678',
                email: 'info@example.com'
            }
        };
        
        results.push(`<p>会社名: ${company.name}</p>`);
        results.push(`<p>住所: ${company.address.city} ${company.address.street}</p>`);
        results.push(`<p>電話: ${company.contact.phone}</p>`);
        
        // 実践例: 複雑なデータ構造
        results.push('<h2>実践例: 複雑なデータ構造</h2>');
        
        const students = [
            {
                name: '太郎',
                age: 20,
                courses: [
                    { name: '数学', score: 85 },
                    { name: '英語', score: 90 }
                ]
            },
            {
                name: '花子',
                age: 21,
                courses: [
                    { name: '数学', score: 92 },
                    { name: '英語', score: 88 }
                ]
            }
        ];
        
        results.push('<p>学生情報:</p>');
        for (let i = 0; i < students.length; i++) {
            results.push(`<p><strong>${students[i].name}</strong>（${students[i].age}歳）</p>`);
            results.push('<ul>');
            for (let j = 0; j < students[i].courses.length; j++) {
                const course = students[i].courses[j];
                results.push(`<li>${course.name}: ${course.score}点</li>`);
            }
            results.push('</ul>');
        }
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 配列とオブジェクトの組み合わせ ===');
        console.log('配列とオブジェクトを組み合わせることで、複雑なデータ構造を表現できます');
        console.log('オブジェクトの配列、配列を持つオブジェクト、オブジェクトのオブジェクトなど');
    </script>
</body>
</html>
```

---

## まとめ

この章では、オブジェクトについて学びました。

### 学んだこと
- オブジェクトの概念（名前と値のペア）
- オブジェクトの作成方法
- プロパティの参照（ドット記法、ブラケット記法）
- プロパティの追加と上書き
- オブジェクト型と参照の仕組み
- 配列とオブジェクトにおける変数のコピー
- 配列とオブジェクトの組み合わせ

### 重要なポイント
1. **名前と値のペア**: オブジェクトはプロパティ（キーとバリュー）の集合
2. **ドット記法とブラケット記法**: プロパティにアクセスする2つの方法
3. **参照の仕組み**: オブジェクト型は参照を保存する
4. **独立したコピー**: スプレッド構文で独立したコピーを作れる
5. **組み合わせ**: 配列とオブジェクトを組み合わせて複雑なデータを表現

### 次のステップ
次の章では、配列の便利なメソッドについて詳しく学びます。

---

## 演習問題

### 問題1: オブジェクトの作成
名前、年齢、居住地を持つオブジェクトを作成してください。

<details>
<summary>解答例</summary>

```javascript
const person = {
    name: '太郎',
    age: 25,
    city: '東京'
};

console.log(person.name); // '太郎'
console.log(person.age);  // 25
console.log(person.city); // '東京'
```
</details>

### 問題2: プロパティの追加
オブジェクト`{ name: '太郎' }`に、`age: 25`と`city: '東京'`を追加してください。

<details>
<summary>解答例</summary>

```javascript
const person = { name: '太郎' };
person.age = 25;
person.city = '東京';

console.log(person); // { name: '太郎', age: 25, city: '東京' }
```
</details>

### 問題3: オブジェクトのコピー
オブジェクト`{ a: 1, b: 2 }`をスプレッド構文でコピーし、コピーに`c: 3`を追加してください。元のオブジェクトが変わらないことを確認してください。

<details>
<summary>解答例</summary>

```javascript
const original = { a: 1, b: 2 };
const copy = { ...original };

copy.c = 3;

console.log(original); // { a: 1, b: 2 }（変わらない）
console.log(copy);      // { a: 1, b: 2, c: 3 }
```
</details>

### 問題4: オブジェクトの配列
3人のユーザー情報（名前、年齢）を持つ配列を作成してください。

<details>
<summary>解答例</summary>

```javascript
const users = [
    { name: '太郎', age: 25 },
    { name: '花子', age: 30 },
    { name: '次郎', age: 20 }
];

console.log(users[0].name); // '太郎'
console.log(users[1].age);  // 30
```
</details>

### 問題5: ネストされたオブジェクト
住所情報（都道府県、市区町村）を持つオブジェクトを作成してください。

<details>
<summary>解答例</summary>

```javascript
const person = {
    name: '太郎',
    address: {
        prefecture: '東京都',
        city: '渋谷区'
    }
};

console.log(person.address.prefecture); // '東京都'
console.log(person.address.city);      // '渋谷区'
```
</details>

---

お疲れ様でした！次の章に進みましょう。


