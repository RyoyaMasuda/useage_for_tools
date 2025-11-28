# 7-1. クラス (Class)

この章では、オブジェクト指向プログラミングの基礎となる「クラス」について学びます。クラスを理解することで、関連するデータと処理を整理して管理できるようになります。

---

## 目次

- [クラス構文 - オブジェクト指向プログラミング](#クラス構文---オブジェクト指向プログラミング)
- [クラスとは](#クラスとは)
- [クラスの定義方法](#クラスの定義方法)
- [インスタンス](#インスタンス)
- [インスタンスにプロパティを追加する方法](#インスタンスにプロパティを追加する方法)
- [コンストラクタ](#コンストラクタ)
- [メソッド](#メソッド)
- [this プロパティの呼び出し](#this-プロパティの呼び出し)
- [this メソッドの呼び出し](#this-メソッドの呼び出し)
- [静的メソッド](#静的メソッド)
- [静的メソッドとインスタンスメソッドの違い](#静的メソッドとインスタンスメソッドの違い)
- [静的プロパティ](#静的プロパティ)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## クラス構文 - オブジェクト指向プログラミング

### オブジェクト指向プログラミングとは
オブジェクト指向プログラミング（OOP）は、**データと処理を1つにまとめて管理する**プログラミングの考え方です。

### クラスの概念
クラスは、**オブジェクトの設計図**のようなものです。同じ構造のオブジェクトを効率的に作成できます。

### 日常生活での例
- **自動車の設計図**: クラス（設計図）から、実際の車（インスタンス）を作る
- **家の設計図**: クラス（設計図）から、実際の家（インスタンス）を作る

### プログラミングでの例
```javascript
// クラス（設計図）
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
}

// インスタンス（実際のオブジェクト）
const person1 = new Person('太郎', 25);
const person2 = new Person('花子', 30);
```

### 実践例: クラス構文の基本概念

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>クラス構文の基本概念</title>
</head>
<body>
    <h1>クラス構文 - オブジェクト指向プログラミング</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // クラスを使わない場合（同じ構造のオブジェクトを個別に作成）
        results.push('<h2>クラスを使わない場合</h2>');
        
        const person1 = {
            name: '太郎',
            age: 25,
            greet: function() {
                return `こんにちは、${this.name}です`;
            }
        };
        
        const person2 = {
            name: '花子',
            age: 30,
            greet: function() {
                return `こんにちは、${this.name}です`;
            }
        };
        
        results.push(`<p>${person1.greet()}</p>`);
        results.push(`<p>${person2.greet()}</p>`);
        results.push('<p>同じ構造のオブジェクトを個別に作成する必要がある</p>');
        
        // クラスを使う場合（設計図から効率的に作成）
        results.push('<h2>クラスを使う場合</h2>');
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
            
            greet() {
                return `こんにちは、${this.name}です`;
            }
        }
        
        const person3 = new Person('次郎', 20);
        const person4 = new Person('三郎', 35);
        
        results.push(`<p>${person3.greet()}</p>`);
        results.push(`<p>${person4.greet()}</p>`);
        results.push('<p>クラス（設計図）から効率的にオブジェクトを作成できる</p>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== クラス構文 ===');
        console.log('クラスはオブジェクトの設計図です');
        console.log('同じ構造のオブジェクトを効率的に作成できます');
    </script>
</body>
</html>
```

---

## クラスとは

### クラスの定義
クラスは、**オブジェクトの設計図**です。同じ構造と動作を持つオブジェクトを効率的に作成できます。

### クラスの特徴
1. **設計図**: オブジェクトの構造を定義
2. **再利用性**: 同じクラスから複数のオブジェクトを作成できる
3. **カプセル化**: データと処理を1つにまとめる
4. **継承**: 他のクラスを拡張できる（上級者向け）

### クラスとインスタンスの関係

```javascript
// クラス（設計図）
class Car {
    constructor(brand, model) {
        this.brand = brand;
        this.model = model;
    }
}

// インスタンス（実際のオブジェクト）
const car1 = new Car('トヨタ', 'プリウス');
const car2 = new Car('ホンダ', 'フィット');
```

### 実践例: クラスとは

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>クラスとは</title>
</head>
<body>
    <h1>クラスとは</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的なクラス
        results.push('<h2>基本的なクラス</h2>');
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
        }
        
        const person1 = new Person('太郎', 25);
        const person2 = new Person('花子', 30);
        
        results.push(`<p>person1: 名前=${person1.name}, 年齢=${person1.age}</p>`);
        results.push(`<p>person2: 名前=${person2.name}, 年齢=${person2.age}</p>`);
        
        // クラスの特徴
        results.push('<h2>クラスの特徴</h2>');
        results.push('<p>• 設計図: オブジェクトの構造を定義</p>');
        results.push('<p>• 再利用性: 同じクラスから複数のオブジェクトを作成</p>');
        results.push('<p>• カプセル化: データと処理を1つにまとめる</p>');
        
        // 実践例: 商品クラス
        results.push('<h2>実践例: 商品クラス</h2>');
        
        class Product {
            constructor(name, price) {
                this.name = name;
                this.price = price;
            }
        }
        
        const product1 = new Product('りんご', 100);
        const product2 = new Product('バナナ', 150);
        const product3 = new Product('オレンジ', 120);
        
        results.push(`<p>${product1.name}: ${product1.price}円</p>`);
        results.push(`<p>${product2.name}: ${product2.price}円</p>`);
        results.push(`<p>${product3.name}: ${product3.price}円</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== クラスとは ===');
        console.log('クラスはオブジェクトの設計図です');
        console.log('同じ構造のオブジェクトを効率的に作成できます');
    </script>
</body>
</html>
```

---

## クラスの定義方法

クラスを定義する方法を学びます。

### クラス定義の基本構文

```javascript
class クラス名 {
    constructor(引数) {
        // 初期化処理
    }
    
    メソッド名() {
        // 処理
    }
}
```

### 基本的なクラス定義

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    greet() {
        return `こんにちは、${this.name}です`;
    }
}
```

### クラス名の規則
- **大文字で始める**: クラス名は大文字で始める（慣習）
- **キャメルケース**: `Person`, `UserAccount`など

### 実践例: クラスの定義方法

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>クラスの定義方法</title>
</head>
<body>
    <h1>クラスの定義方法</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的なクラス定義
        results.push('<h2>基本的なクラス定義</h2>');
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
            
            greet() {
                return `こんにちは、${this.name}です。${this.age}歳です。`;
            }
        }
        
        const person = new Person('太郎', 25);
        results.push(`<p>${person.greet()}</p>`);
        
        // クラス名の規則
        results.push('<h2>クラス名の規則</h2>');
        results.push('<p>• 大文字で始める（慣習）</p>');
        results.push('<p>• キャメルケースを使用</p>');
        results.push('<p>例: Person, UserAccount, ShoppingCart</p>');
        
        // 実践例: 複数のメソッドを持つクラス
        results.push('<h2>実践例: 複数のメソッドを持つクラス</h2>');
        
        class Calculator {
            constructor(value = 0) {
                this.value = value;
            }
            
            add(num) {
                this.value += num;
                return this;
            }
            
            subtract(num) {
                this.value -= num;
                return this;
            }
            
            getValue() {
                return this.value;
            }
        }
        
        const calc = new Calculator(10);
        calc.add(5).subtract(3);
        results.push(`<p>計算結果: ${calc.getValue()}</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== クラスの定義方法 ===');
        console.log('classキーワードでクラスを定義します');
        console.log('クラス名は大文字で始めることが推奨されます');
    </script>
</body>
</html>
```

---

## インスタンス

インスタンスは、クラスから作成された実際のオブジェクトです。

### インスタンスの作成

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
}

// インスタンスの作成
const person1 = new Person('太郎', 25);
const person2 = new Person('花子', 30);
```

### インスタンスの特徴
1. **独立したオブジェクト**: 各インスタンスは独立している
2. **同じ構造**: 同じクラスから作成されたインスタンスは同じ構造を持つ
3. **異なる値**: 各インスタンスは異なる値を持つ

### 実践例: インスタンス

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>インスタンス</title>
</head>
<body>
    <h1>インスタンス</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
            
            introduce() {
                return `私は${this.name}です。${this.age}歳です。`;
            }
        }
        
        // インスタンスの作成
        results.push('<h2>インスタンスの作成</h2>');
        
        const person1 = new Person('太郎', 25);
        const person2 = new Person('花子', 30);
        const person3 = new Person('次郎', 20);
        
        results.push(`<p>person1: ${person1.introduce()}</p>`);
        results.push(`<p>person2: ${person2.introduce()}</p>`);
        results.push(`<p>person3: ${person3.introduce()}</p>`);
        
        // インスタンスの特徴
        results.push('<h2>インスタンスの特徴</h2>');
        results.push('<p>• 独立したオブジェクト: 各インスタンスは独立している</p>');
        results.push('<p>• 同じ構造: 同じクラスから作成されたインスタンスは同じ構造を持つ</p>');
        results.push('<p>• 異なる値: 各インスタンスは異なる値を持つ</p>');
        
        // 実践例: 商品のインスタンス
        results.push('<h2>実践例: 商品のインスタンス</h2>');
        
        class Product {
            constructor(name, price, stock) {
                this.name = name;
                this.price = price;
                this.stock = stock;
            }
            
            getInfo() {
                return `${this.name}: ${this.price}円（在庫: ${this.stock}）`;
            }
        }
        
        const product1 = new Product('りんご', 100, 10);
        const product2 = new Product('バナナ', 150, 5);
        const product3 = new Product('オレンジ', 120, 8);
        
        results.push(`<p>${product1.getInfo()}</p>`);
        results.push(`<p>${product2.getInfo()}</p>`);
        results.push(`<p>${product3.getInfo()}</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== インスタンス ===');
        console.log('インスタンスはクラスから作成された実際のオブジェクトです');
        console.log('newキーワードでインスタンスを作成します');
    </script>
</body>
</html>
```

---

## インスタンスにプロパティを追加する方法

インスタンスにプロパティを追加する方法を学びます。

### コンストラクタで追加

```javascript
class Person {
    constructor(name, age) {
        this.name = name;  // プロパティを追加
        this.age = age;    // プロパティを追加
    }
}
```

### インスタンス作成後に追加

```javascript
const person = new Person('太郎', 25);
person.city = '東京';  // プロパティを追加
person.email = 'taro@example.com';  // プロパティを追加
```

### 実践例: インスタンスにプロパティを追加する方法

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>インスタンスにプロパティを追加する方法</title>
</head>
<body>
    <h1>インスタンスにプロパティを追加する方法</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
        }
        
        // コンストラクタで追加
        results.push('<h2>コンストラクタで追加</h2>');
        
        const person1 = new Person('太郎', 25);
        results.push(`<p>person1.name: ${person1.name}</p>`);
        results.push(`<p>person1.age: ${person1.age}</p>`);
        
        // インスタンス作成後に追加
        results.push('<h2>インスタンス作成後に追加</h2>');
        
        const person2 = new Person('花子', 30);
        person2.city = '東京';
        person2.email = 'hanako@example.com';
        
        results.push(`<p>person2.name: ${person2.name}</p>`);
        results.push(`<p>person2.age: ${person2.age}</p>`);
        results.push(`<p>person2.city: ${person2.city}</p>`);
        results.push(`<p>person2.email: ${person2.email}</p>`);
        
        // 実践例: ユーザー情報の追加
        results.push('<h2>実践例: ユーザー情報の追加</h2>');
        
        class User {
            constructor(username, email) {
                this.username = username;
                this.email = email;
                this.createdAt = new Date();
            }
        }
        
        const user = new User('taro', 'taro@example.com');
        user.lastLogin = new Date();
        user.isActive = true;
        
        results.push(`<p>ユーザー名: ${user.username}</p>`);
        results.push(`<p>メール: ${user.email}</p>`);
        results.push(`<p>作成日: ${user.createdAt}</p>`);
        results.push(`<p>最終ログイン: ${user.lastLogin}</p>`);
        results.push(`<p>アクティブ: ${user.isActive}</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== インスタンスにプロパティを追加する方法 ===');
        console.log('コンストラクタで追加する方法と、インスタンス作成後に追加する方法があります');
    </script>
</body>
</html>
```

---

## コンストラクタ

コンストラクタは、インスタンスが作成されるときに自動的に実行される特別なメソッドです。

### コンストラクタの基本

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
}
```

### コンストラクタの特徴
1. **自動実行**: インスタンス作成時に自動的に実行される
2. **初期化**: インスタンスの初期値を設定する
3. **必須ではない**: コンストラクタは省略可能

### コンストラクタの使い方

```javascript
class Person {
    constructor(name, age) {
        // 初期化処理
        this.name = name;
        this.age = age;
        this.createdAt = new Date();
    }
}
```

### 実践例: コンストラクタ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>コンストラクタ</title>
</head>
<body>
    <h1>コンストラクタ</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的なコンストラクタ
        results.push('<h2>基本的なコンストラクタ</h2>');
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
        }
        
        const person = new Person('太郎', 25);
        results.push(`<p>名前: ${person.name}, 年齢: ${person.age}</p>`);
        
        // コンストラクタで初期値を設定
        results.push('<h2>コンストラクタで初期値を設定</h2>');
        
        class User {
            constructor(username, email) {
                this.username = username;
                this.email = email;
                this.createdAt = new Date();
                this.isActive = true;
            }
        }
        
        const user = new User('taro', 'taro@example.com');
        results.push(`<p>ユーザー名: ${user.username}</p>`);
        results.push(`<p>メール: ${user.email}</p>`);
        results.push(`<p>作成日: ${user.createdAt}</p>`);
        results.push(`<p>アクティブ: ${user.isActive}</p>`);
        
        // デフォルト値を持つコンストラクタ
        results.push('<h2>デフォルト値を持つコンストラクタ</h2>');
        
        class Product {
            constructor(name, price = 0, stock = 0) {
                this.name = name;
                this.price = price;
                this.stock = stock;
            }
        }
        
        const product1 = new Product('りんご', 100, 10);
        const product2 = new Product('バナナ');  // デフォルト値が使われる
        
        results.push(`<p>product1: ${product1.name}, ${product1.price}円, 在庫${product1.stock}</p>`);
        results.push(`<p>product2: ${product2.name}, ${product2.price}円, 在庫${product2.stock}</p>`);
        
        // コンストラクタなしのクラス
        results.push('<h2>コンストラクタなしのクラス</h2>');
        
        class Simple {
            // コンストラクタを省略可能
        }
        
        const simple = new Simple();
        results.push(`<p>コンストラクタなしでもインスタンスを作成できます</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== コンストラクタ ===');
        console.log('コンストラクタはインスタンス作成時に自動的に実行されます');
        console.log('初期化処理を行うのに適しています');
    </script>
</body>
</html>
```

---

## メソッド

メソッドは、クラス内で定義される関数です。

### メソッドの基本

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    greet() {
        return `こんにちは、${this.name}です`;
    }
}
```

### メソッドの特徴
1. **クラス内で定義**: クラスの中に定義される
2. **インスタンスから呼び出し**: インスタンスを通じて呼び出す
3. **thisキーワード**: インスタンス自身を参照する

### 実践例: メソッド

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>メソッド</title>
</head>
<body>
    <h1>メソッド</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的なメソッド
        results.push('<h2>基本的なメソッド</h2>');
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
            
            greet() {
                return `こんにちは、${this.name}です`;
            }
            
            getAge() {
                return `${this.age}歳です`;
            }
        }
        
        const person = new Person('太郎', 25);
        results.push(`<p>${person.greet()}</p>`);
        results.push(`<p>${person.getAge()}</p>`);
        
        // 複数のメソッド
        results.push('<h2>複数のメソッド</h2>');
        
        class Calculator {
            constructor(value = 0) {
                this.value = value;
            }
            
            add(num) {
                this.value += num;
                return this;
            }
            
            subtract(num) {
                this.value -= num;
                return this;
            }
            
            multiply(num) {
                this.value *= num;
                return this;
            }
            
            getValue() {
                return this.value;
            }
        }
        
        const calc = new Calculator(10);
        calc.add(5).subtract(3).multiply(2);
        results.push(`<p>計算結果: ${calc.getValue()}</p>`);
        
        // 実践例: 銀行口座クラス
        results.push('<h2>実践例: 銀行口座クラス</h2>');
        
        class BankAccount {
            constructor(owner, initialBalance = 0) {
                this.owner = owner;
                this.balance = initialBalance;
            }
            
            deposit(amount) {
                this.balance += amount;
                return this.balance;
            }
            
            withdraw(amount) {
                if (this.balance >= amount) {
                    this.balance -= amount;
                    return this.balance;
                } else {
                    return '残高不足';
                }
            }
            
            getBalance() {
                return this.balance;
            }
        }
        
        const account = new BankAccount('太郎', 1000);
        results.push(`<p>初期残高: ${account.getBalance()}円</p>`);
        results.push(`<p>入金後: ${account.deposit(500)}円</p>`);
        results.push(`<p>出金後: ${account.withdraw(300)}円</p>`);
        results.push(`<p>現在の残高: ${account.getBalance()}円</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== メソッド ===');
        console.log('メソッドはクラス内で定義される関数です');
        console.log('インスタンスを通じて呼び出します');
    </script>
</body>
</html>
```

---

## this プロパティの呼び出し

`this`キーワードは、インスタンス自身を参照します。

### thisの基本

```javascript
class Person {
    constructor(name, age) {
        this.name = name;  // thisでインスタンス自身を参照
        this.age = age;
    }
    
    getName() {
        return this.name;  // thisでプロパティにアクセス
    }
}
```

### thisの特徴
1. **インスタンス自身を参照**: メソッド内で`this`はインスタンス自身を指す
2. **プロパティへのアクセス**: `this.プロパティ名`でプロパティにアクセス
3. **メソッドの呼び出し**: `this.メソッド名()`でメソッドを呼び出せる

### 実践例: this プロパティの呼び出し

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>this プロパティの呼び出し</title>
</head>
<body>
    <h1>this - プロパティの呼び出し</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // thisでプロパティにアクセス
        results.push('<h2>thisでプロパティにアクセス</h2>');
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
            
            getName() {
                return this.name;  // thisでプロパティにアクセス
            }
            
            getAge() {
                return this.age;
            }
            
            getInfo() {
                return `${this.name}は${this.age}歳です`;
            }
        }
        
        const person = new Person('太郎', 25);
        results.push(`<p>getName(): ${person.getName()}</p>`);
        results.push(`<p>getAge(): ${person.getAge()}</p>`);
        results.push(`<p>getInfo(): ${person.getInfo()}</p>`);
        
        // thisでプロパティを変更
        results.push('<h2>thisでプロパティを変更</h2>');
        
        class Counter {
            constructor(value = 0) {
                this.value = value;
            }
            
            increment() {
                this.value++;  // thisでプロパティを変更
                return this.value;
            }
            
            decrement() {
                this.value--;
                return this.value;
            }
        }
        
        const counter = new Counter(10);
        results.push(`<p>初期値: ${counter.value}</p>`);
        results.push(`<p>increment後: ${counter.increment()}</p>`);
        results.push(`<p>decrement後: ${counter.decrement()}</p>`);
        
        // 実践例: 商品クラス
        results.push('<h2>実践例: 商品クラス</h2>');
        
        class Product {
            constructor(name, price, stock) {
                this.name = name;
                this.price = price;
                this.stock = stock;
            }
            
            reduceStock(amount) {
                if (this.stock >= amount) {
                    this.stock -= amount;
                    return true;
                }
                return false;
            }
            
            getTotalValue() {
                return this.price * this.stock;  // thisでプロパティにアクセス
            }
        }
        
        const product = new Product('りんご', 100, 10);
        results.push(`<p>商品: ${product.name}, 価格: ${product.price}円, 在庫: ${product.stock}</p>`);
        results.push(`<p>在庫価値: ${product.getTotalValue()}円</p>`);
        product.reduceStock(3);
        results.push(`<p>在庫減少後: ${product.stock}, 在庫価値: ${product.getTotalValue()}円</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== this プロパティの呼び出し ===');
        console.log('thisキーワードでインスタンス自身のプロパティにアクセスできます');
    </script>
</body>
</html>
```

---

## this メソッドの呼び出し

`this`キーワードを使って、同じクラス内の他のメソッドを呼び出すことができます。

### thisでメソッドを呼び出す

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    greet() {
        return `こんにちは、${this.name}です`;
    }
    
    introduce() {
        return `${this.greet()}。${this.age}歳です`;  // thisでメソッドを呼び出し
    }
}
```

### 実践例: this メソッドの呼び出し

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>this メソッドの呼び出し</title>
</head>
<body>
    <h1>this - メソッドの呼び出し</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // thisでメソッドを呼び出す
        results.push('<h2>thisでメソッドを呼び出す</h2>');
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
            
            greet() {
                return `こんにちは、${this.name}です`;
            }
            
            introduce() {
                return `${this.greet()}。${this.age}歳です`;  // thisでメソッドを呼び出し
            }
        }
        
        const person = new Person('太郎', 25);
        results.push(`<p>${person.introduce()}</p>`);
        
        // メソッドチェーン
        results.push('<h2>メソッドチェーン</h2>');
        
        class Calculator {
            constructor(value = 0) {
                this.value = value;
            }
            
            add(num) {
                this.value += num;
                return this;  // thisを返すことでチェーン可能
            }
            
            subtract(num) {
                this.value -= num;
                return this;
            }
            
            multiply(num) {
                this.value *= num;
                return this;
            }
            
            getValue() {
                return this.value;
            }
        }
        
        const calc = new Calculator(10);
        const result = calc.add(5).subtract(3).multiply(2).getValue();
        results.push(`<p>計算結果: ${result}</p>`);
        
        // 実践例: 銀行口座クラス
        results.push('<h2>実践例: 銀行口座クラス</h2>');
        
        class BankAccount {
            constructor(owner, initialBalance = 0) {
                this.owner = owner;
                this.balance = initialBalance;
                this.transactions = [];
            }
            
            deposit(amount) {
                this.balance += amount;
                this.addTransaction('入金', amount);
                return this;
            }
            
            withdraw(amount) {
                if (this.balance >= amount) {
                    this.balance -= amount;
                    this.addTransaction('出金', amount);
                    return this;
                }
                return this;
            }
            
            addTransaction(type, amount) {
                this.transactions.push({ type, amount, date: new Date() });
            }
            
            getBalance() {
                return this.balance;
            }
            
            getStatement() {
                return `残高: ${this.getBalance()}円, 取引数: ${this.transactions.length}件`;
            }
        }
        
        const account = new BankAccount('太郎', 1000);
        account.deposit(500).withdraw(300);
        results.push(`<p>${account.getStatement()}</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== this メソッドの呼び出し ===');
        console.log('thisキーワードで同じクラス内の他のメソッドを呼び出せます');
        console.log('メソッドチェーンを実現するためにthisを返すこともできます');
    </script>
</body>
</html>
```

---

## 静的メソッド

静的メソッドは、インスタンスを作成せずにクラスから直接呼び出せるメソッドです。

### 静的メソッドの基本

```javascript
class MathUtils {
    static add(a, b) {
        return a + b;
    }
    
    static multiply(a, b) {
        return a * b;
    }
}

// インスタンスを作成せずに呼び出し
console.log(MathUtils.add(10, 5)); // 15
```

### 静的メソッドの特徴
1. **インスタンス不要**: `new`でインスタンスを作成する必要がない
2. **クラスから直接呼び出し**: `クラス名.メソッド名()`で呼び出す
3. **thisは使えない**: インスタンスがないため、`this`は使えない

### 実践例: 静的メソッド

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>静的メソッド</title>
</head>
<body>
    <h1>静的メソッド</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的な静的メソッド
        results.push('<h2>基本的な静的メソッド</h2>');
        
        class MathUtils {
            static add(a, b) {
                return a + b;
            }
            
            static subtract(a, b) {
                return a - b;
            }
            
            static multiply(a, b) {
                return a * b;
            }
        }
        
        results.push(`<p>MathUtils.add(10, 5): ${MathUtils.add(10, 5)}</p>`);
        results.push(`<p>MathUtils.subtract(10, 5): ${MathUtils.subtract(10, 5)}</p>`);
        results.push(`<p>MathUtils.multiply(10, 5): ${MathUtils.multiply(10, 5)}</p>`);
        
        // 実践例: ユーティリティクラス
        results.push('<h2>実践例: ユーティリティクラス</h2>');
        
        class StringUtils {
            static capitalize(str) {
                return str.charAt(0).toUpperCase() + str.slice(1);
            }
            
            static reverse(str) {
                return str.split('').reverse().join('');
            }
            
            static isEmail(email) {
                return email.includes('@');
            }
        }
        
        results.push(`<p>capitalize('hello'): ${StringUtils.capitalize('hello')}</p>`);
        results.push(`<p>reverse('hello'): ${StringUtils.reverse('hello')}</p>`);
        results.push(`<p>isEmail('test@example.com'): ${StringUtils.isEmail('test@example.com')}</p>`);
        
        // 実践例: ファクトリーメソッド
        results.push('<h2>実践例: ファクトリーメソッド</h2>');
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
            
            static createAdult(name, age) {
                if (age >= 20) {
                    return new Person(name, age);
                }
                return null;
            }
            
            static createFromObject(obj) {
                return new Person(obj.name, obj.age);
            }
        }
        
        const adult = Person.createAdult('太郎', 25);
        results.push(`<p>createAdult('太郎', 25): ${adult ? adult.name : 'null'}</p>`);
        
        const person = Person.createFromObject({ name: '花子', age: 30 });
        results.push(`<p>createFromObject: ${person.name}, ${person.age}歳</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 静的メソッド ===');
        console.log('静的メソッドはインスタンスを作成せずに呼び出せます');
        console.log('staticキーワードで定義します');
    </script>
</body>
</html>
```

---

## 静的メソッドとインスタンスメソッドの違い

静的メソッドとインスタンスメソッドの違いを理解しましょう。

### インスタンスメソッド

```javascript
class Person {
    constructor(name) {
        this.name = name;
    }
    
    // インスタンスメソッド
    greet() {
        return `こんにちは、${this.name}です`;
    }
}

const person = new Person('太郎');
person.greet();  // インスタンスから呼び出す
```

### 静的メソッド

```javascript
class Person {
    static create(name) {
        return new Person(name);
    }
}

Person.create('太郎');  // クラスから直接呼び出す
```

### 主な違い

| 項目 | インスタンスメソッド | 静的メソッド |
|------|---------------------|-------------|
| **呼び出し方法** | `インスタンス.メソッド()` | `クラス.メソッド()` |
| **thisの使用** | 可能（インスタンスを参照） | 不可 |
| **インスタンスの作成** | 必要 | 不要 |
| **用途** | インスタンスの操作 | ユーティリティ、ファクトリー |

### 実践例: 静的メソッドとインスタンスメソッドの違い

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>静的メソッドとインスタンスメソッドの違い</title>
</head>
<body>
    <h1>静的メソッドとインスタンスメソッドの違い</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
            
            // インスタンスメソッド
            greet() {
                return `こんにちは、${this.name}です`;  // thisが使える
            }
            
            // 静的メソッド
            static create(name, age) {
                return new Person(name, age);  // thisは使えない
            }
            
            static isAdult(age) {
                return age >= 20;
            }
        }
        
        // インスタンスメソッドの使用
        results.push('<h2>インスタンスメソッドの使用</h2>');
        
        const person = new Person('太郎', 25);
        results.push(`<p>person.greet(): ${person.greet()}</p>`);
        results.push('<p>• インスタンスから呼び出す</p>');
        results.push('<p>• thisが使える</p>');
        
        // 静的メソッドの使用
        results.push('<h2>静的メソッドの使用</h2>');
        
        const person2 = Person.create('花子', 30);
        results.push(`<p>Person.create('花子', 30): ${person2.name}</p>`);
        results.push(`<p>Person.isAdult(25): ${Person.isAdult(25)}</p>`);
        results.push('<p>• クラスから直接呼び出す</p>');
        results.push('<p>• thisは使えない</p>');
        results.push('<p>• インスタンスの作成が不要</p>');
        
        // 比較表
        results.push('<h2>比較表</h2>');
        results.push('<table border="1" style="border-collapse: collapse;">');
        results.push('<tr><th>項目</th><th>インスタンスメソッド</th><th>静的メソッド</th></tr>');
        results.push('<tr><td>呼び出し方法</td><td>インスタンス.メソッド()</td><td>クラス.メソッド()</td></tr>');
        results.push('<tr><td>thisの使用</td><td>可能</td><td>不可</td></tr>');
        results.push('<tr><td>インスタンスの作成</td><td>必要</td><td>不要</td></tr>');
        results.push('</table>');
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 静的メソッドとインスタンスメソッドの違い ===');
        console.log('インスタンスメソッド: インスタンスから呼び出す、thisが使える');
        console.log('静的メソッド: クラスから直接呼び出す、thisは使えない');
    </script>
</body>
</html>
```

---

## 静的プロパティ

静的プロパティは、インスタンスを作成せずにクラスから直接アクセスできるプロパティです。

### 静的プロパティの基本

```javascript
class Counter {
    static count = 0;  // 静的プロパティ
    
    static increment() {
        Counter.count++;
    }
}

Counter.count = 10;
console.log(Counter.count); // 10
```

### 静的プロパティの特徴
1. **インスタンス不要**: インスタンスを作成する必要がない
2. **クラスから直接アクセス**: `クラス名.プロパティ名`でアクセス
3. **共有される**: すべてのインスタンスで共有される

### 実践例: 静的プロパティ

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>静的プロパティ</title>
</head>
<body>
    <h1>静的プロパティ</h1>
    <div id="output"></div>
    
    <script>
        const output = document.getElementById('output');
        let results = [];
        
        // 基本的な静的プロパティ
        results.push('<h2>基本的な静的プロパティ</h2>');
        
        class Counter {
            static count = 0;  // 静的プロパティ
            
            static increment() {
                Counter.count++;
            }
            
            static getCount() {
                return Counter.count;
            }
        }
        
        results.push(`<p>初期値: ${Counter.count}</p>`);
        Counter.increment();
        Counter.increment();
        results.push(`<p>increment後: ${Counter.getCount()}</p>`);
        
        // 実践例: 設定値の管理
        results.push('<h2>実践例: 設定値の管理</h2>');
        
        class Config {
            static API_URL = 'https://api.example.com';
            static MAX_RETRIES = 3;
            static TIMEOUT = 5000;
        }
        
        results.push(`<p>API_URL: ${Config.API_URL}</p>`);
        results.push(`<p>MAX_RETRIES: ${Config.MAX_RETRIES}</p>`);
        results.push(`<p>TIMEOUT: ${Config.TIMEOUT}ms</p>`);
        
        // 実践例: インスタンス数のカウント
        results.push('<h2>実践例: インスタンス数のカウント</h2>');
        
        class User {
            static instanceCount = 0;  // 静的プロパティ
            
            constructor(name) {
                this.name = name;
                User.instanceCount++;  // インスタンス作成時にカウント
            }
            
            static getInstanceCount() {
                return User.instanceCount;
            }
        }
        
        results.push(`<p>初期インスタンス数: ${User.getInstanceCount()}</p>`);
        
        const user1 = new User('太郎');
        const user2 = new User('花子');
        const user3 = new User('次郎');
        
        results.push(`<p>インスタンス作成後: ${User.getInstanceCount()}</p>`);
        
        output.innerHTML = results.join('');
        
        // コンソールで確認
        console.log('=== 静的プロパティ ===');
        console.log('静的プロパティはクラスから直接アクセスできます');
        console.log('すべてのインスタンスで共有されます');
    </script>
</body>
</html>
```

---

## まとめ

この章では、クラスについて学びました。

### 学んだこと
- クラス構文とオブジェクト指向プログラミング
- クラスの定義方法
- インスタンスの作成
- コンストラクタ
- メソッド
- `this`キーワード
- 静的メソッドと静的プロパティ

### 重要なポイント
1. **クラスは設計図**: オブジェクトの構造を定義
2. **インスタンスは実際のオブジェクト**: `new`で作成
3. **コンストラクタで初期化**: インスタンス作成時に自動実行
4. **thisでインスタンスを参照**: メソッド内で`this`を使う
5. **静的メソッド/プロパティ**: インスタンス不要で使用可能

### 次のステップ
次の章では、モジュールについて詳しく学びます。

---

## 演習問題

### 問題1: 基本的なクラス
名前と年齢を持つ`Person`クラスを作成し、インスタンスを作成してください。

<details>
<summary>解答例</summary>

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
}

const person = new Person('太郎', 25);
console.log(person.name); // '太郎'
console.log(person.age);  // 25
```
</details>

### 問題2: メソッドの追加
`Person`クラスに、自己紹介をする`introduce`メソッドを追加してください。

<details>
<summary>解答例</summary>

```javascript
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    introduce() {
        return `私は${this.name}です。${this.age}歳です。`;
    }
}

const person = new Person('太郎', 25);
console.log(person.introduce()); // '私は太郎です。25歳です。'
```
</details>

### 問題3: 静的メソッド
`MathUtils`クラスに、2つの数値を足し算する静的メソッド`add`を作成してください。

<details>
<summary>解答例</summary>

```javascript
class MathUtils {
    static add(a, b) {
        return a + b;
    }
}

console.log(MathUtils.add(10, 5)); // 15
```
</details>

### 問題4: thisの使用
`Counter`クラスを作成し、`increment`メソッドで`this.value`を増やすようにしてください。

<details>
<summary>解答例</summary>

```javascript
class Counter {
    constructor(value = 0) {
        this.value = value;
    }
    
    increment() {
        this.value++;
        return this.value;
    }
}

const counter = new Counter(10);
console.log(counter.increment()); // 11
```
</details>

### 問題5: メソッドチェーン
`Calculator`クラスを作成し、`add`と`multiply`メソッドをチェーンできるようにしてください。

<details>
<summary>解答例</summary>

```javascript
class Calculator {
    constructor(value = 0) {
        this.value = value;
    }
    
    add(num) {
        this.value += num;
        return this;  // thisを返す
    }
    
    multiply(num) {
        this.value *= num;
        return this;  // thisを返す
    }
    
    getValue() {
        return this.value;
    }
}

const calc = new Calculator(10);
const result = calc.add(5).multiply(2).getValue();
console.log(result); // 30
```
</details>

---

お疲れ様でした！次の章に進みましょう。



