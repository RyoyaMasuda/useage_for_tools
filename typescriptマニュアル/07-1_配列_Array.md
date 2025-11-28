# 7-1. 配列 (Array)

この章では、複数のデータをまとめて扱う「配列」について学びます。TypeScriptでは、配列の型を明示的に指定できるため、より安全なコードを書けます。配列を理解することで、大量のデータを効率的に管理できるようになります。

---

## 目次

- [配列の基本と型定義（角カッコ / ジェネリクス記法）](#配列の基本と型定義角カッコ--ジェネリクス記法)
- [配列を操作しよう](#配列を操作しよう)
- [readonly修飾子（配列での利用）](#readonly修飾子配列での利用)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## 配列の基本と型定義（角カッコ / ジェネリクス記法）

### 配列とは
配列（Array）は、**複数のデータを順番に並べて、1つの変数で管理する**データ構造です。

### 日常生活での例
- **買い物リスト**: りんご、バナナ、オレンジ...（複数の商品を1つのリストで管理）
- **出席番号**: 1番、2番、3番...（複数の番号を順番に管理）
- **成績表**: 85点、90点、78点...（複数の点数を管理）

### TypeScriptの配列型定義

TypeScriptでは、配列の型を2つの方法で定義できます：

#### 1. 角カッコ記法（推奨）

```typescript
let 変数名: 型[] = [値1, 値2, ...];
```

#### 2. ジェネリクス記法

```typescript
let 変数名: Array<型> = [値1, 値2, ...];
```

### 基本的な配列の作成

```typescript
// 文字列の配列（角カッコ記法）
const fruits: string[] = ['りんご', 'バナナ', 'オレンジ'];

// 数値の配列（ジェネリクス記法）
const numbers: Array<number> = [1, 2, 3, 4, 5];

// 真偽値の配列
const flags: boolean[] = [true, false, true];
```

### 配列とインデックス

配列の各要素には、**インデックス（添字）**という番号が割り当てられます。インデックスは0から始まります。

```typescript
const fruits: string[] = ['りんご', 'バナナ', 'オレンジ'];

console.log(fruits[0]); // 'りんご'（最初の要素）
console.log(fruits[1]); // 'バナナ'（2番目の要素）
console.log(fruits[2]); // 'オレンジ'（3番目の要素）
```

### 実践例: 配列の基本と型定義

```typescript
// src/array-basics.ts
// 文字列の配列（角カッコ記法）
const fruits: string[] = ['りんご', 'バナナ', 'オレンジ', 'ぶどう'];
console.log('果物:', fruits);

// 数値の配列（ジェネリクス記法）
const numbers: Array<number> = [1, 2, 3, 4, 5];
console.log('数値:', numbers);

// 真偽値の配列
const flags: boolean[] = [true, false, true, false];
console.log('真偽値:', flags);

// 配列の要素にアクセス
console.log(`最初の果物: ${fruits[0]}`);     // りんご
console.log(`2番目の数値: ${numbers[1]}`);   // 2
console.log(`3番目の真偽値: ${flags[2]}`);   // true

// 配列の長さを取得
console.log(`果物の数: ${fruits.length}`);   // 4
console.log(`数値の数: ${numbers.length}`);  // 5

// 空の配列を作成
const emptyStringArray: string[] = [];
const emptyNumberArray: Array<number> = [];

console.log(`空の配列の長さ: ${emptyStringArray.length}`); // 0

// 型推論を使った配列
const inferredNumbers = [1, 2, 3]; // number[]型と推論される
const inferredStrings = ['a', 'b', 'c']; // string[]型と推論される

// 実践例: 異なる型の配列
const scores: number[] = [85, 92, 78, 96, 88];
const names: string[] = ['太郎', '花子', '次郎', '美咲', '健太'];
const isActive: boolean[] = [true, true, false, true, false];

console.log('成績:', scores);
console.log('名前:', names);
console.log('アクティブ:', isActive);

// 実践例: 配列の型エラー（TypeScriptの型チェック）
// const mixed: number[] = [1, 2, '3']; // エラー: 文字列は数値配列に割り当てられない
// const wrongType: string[] = [1, 2, 3]; // エラー: 数値は文字列配列に割り当てられない

// 実践例: 配列の初期化
const initializedNumbers: number[] = new Array(5).fill(0);
console.log('初期化された配列:', initializedNumbers); // [0, 0, 0, 0, 0]

// 実践例: 配列のスプレッド構文
const arr1: number[] = [1, 2, 3];
const arr2: number[] = [4, 5, 6];
const combined: number[] = [...arr1, ...arr2];
console.log('結合された配列:', combined); // [1, 2, 3, 4, 5, 6]
```

---

## 配列を操作しよう

### 配列の基本操作

配列には、要素の追加、削除、検索などを行う便利なメソッドが用意されています。

### 要素の追加

#### push: 末尾に追加

```typescript
const fruits: string[] = ['りんご', 'バナナ'];
fruits.push('オレンジ');
console.log(fruits); // ['りんご', 'バナナ', 'オレンジ']
```

#### unshift: 先頭に追加

```typescript
const fruits: string[] = ['りんご', 'バナナ'];
fruits.unshift('オレンジ');
console.log(fruits); // ['オレンジ', 'りんご', 'バナナ']
```

### 要素の削除

#### pop: 末尾を削除

```typescript
const fruits: string[] = ['りんご', 'バナナ', 'オレンジ'];
const last = fruits.pop();
console.log(last);   // 'オレンジ'
console.log(fruits); // ['りんご', 'バナナ']
```

#### shift: 先頭を削除

```typescript
const fruits: string[] = ['りんご', 'バナナ', 'オレンジ'];
const first = fruits.shift();
console.log(first);  // 'りんご'
console.log(fruits); // ['バナナ', 'オレンジ']
```

### 要素の検索と操作

#### indexOf: 要素のインデックスを検索

```typescript
const fruits: string[] = ['りんご', 'バナナ', 'オレンジ'];
const index = fruits.indexOf('バナナ');
console.log(index); // 1
```

#### includes: 要素の存在確認

```typescript
const fruits: string[] = ['りんご', 'バナナ', 'オレンジ'];
console.log(fruits.includes('バナナ')); // true
console.log(fruits.includes('ぶどう')); // false
```

#### slice: 配列の一部を取得（元の配列は変更されない）

```typescript
const fruits: string[] = ['りんご', 'バナナ', 'オレンジ', 'ぶどう'];
const sliced = fruits.slice(1, 3);
console.log(sliced); // ['バナナ', 'オレンジ']
console.log(fruits); // ['りんご', 'バナナ', 'オレンジ', 'ぶどう']（変更されない）
```

#### splice: 配列の要素を削除・追加（元の配列を変更）

```typescript
const fruits: string[] = ['りんご', 'バナナ', 'オレンジ'];
fruits.splice(1, 1, 'ぶどう'); // インデックス1から1つ削除して'ぶどう'を追加
console.log(fruits); // ['りんご', 'ぶどう', 'オレンジ']
```

### 配列の変換

#### map: 各要素を変換

```typescript
const numbers: number[] = [1, 2, 3, 4, 5];
const doubled: number[] = numbers.map((n: number): number => n * 2);
console.log(doubled); // [2, 4, 6, 8, 10]
```

#### filter: 条件に合う要素を抽出

```typescript
const numbers: number[] = [1, 2, 3, 4, 5];
const evens: number[] = numbers.filter((n: number): boolean => n % 2 === 0);
console.log(evens); // [2, 4]
```

#### reduce: 配列を1つの値に集約

```typescript
const numbers: number[] = [1, 2, 3, 4, 5];
const sum: number = numbers.reduce((acc: number, n: number): number => acc + n, 0);
console.log(sum); // 15
```

#### forEach: 各要素に対して処理を実行

```typescript
const fruits: string[] = ['りんご', 'バナナ', 'オレンジ'];
fruits.forEach((fruit: string): void => {
    console.log(fruit);
});
```

### 実践例: 配列の操作

```typescript
// src/array-operations.ts
// 要素の追加
const fruits: string[] = ['りんご', 'バナナ'];
console.log('追加前:', fruits);

fruits.push('オレンジ');
console.log('push後:', fruits); // ['りんご', 'バナナ', 'オレンジ']

fruits.unshift('ぶどう');
console.log('unshift後:', fruits); // ['ぶどう', 'りんご', 'バナナ', 'オレンジ']

// 要素の削除
const removedLast = fruits.pop();
console.log(`削除された要素: ${removedLast}`); // オレンジ
console.log('pop後:', fruits); // ['ぶどう', 'りんご', 'バナナ']

const removedFirst = fruits.shift();
console.log(`削除された要素: ${removedFirst}`); // ぶどう
console.log('shift後:', fruits); // ['りんご', 'バナナ']

// 要素の検索
const numbers: number[] = [10, 20, 30, 40, 50];
const index = numbers.indexOf(30);
console.log(`30のインデックス: ${index}`); // 2

const hasTwenty = numbers.includes(20);
console.log(`20が含まれている: ${hasTwenty}`); // true

// 配列の結合
const arr1: number[] = [1, 2, 3];
const arr2: number[] = [4, 5, 6];
const combined: number[] = arr1.concat(arr2);
console.log('結合された配列:', combined); // [1, 2, 3, 4, 5, 6]

// 配列の反転
const reversed: number[] = [1, 2, 3, 4, 5].reverse();
console.log('反転された配列:', reversed); // [5, 4, 3, 2, 1]

// 配列のソート
const unsorted: number[] = [3, 1, 4, 1, 5, 9, 2, 6];
const sorted: number[] = [...unsorted].sort((a: number, b: number): number => a - b);
console.log('ソート前:', unsorted);
console.log('ソート後:', sorted); // [1, 1, 2, 3, 4, 5, 6, 9]

// 実践例: mapで変換
const scores: number[] = [85, 92, 78, 96, 88];
const grades: string[] = scores.map((score: number): string => {
    if (score >= 90) return 'A';
    if (score >= 80) return 'B';
    if (score >= 70) return 'C';
    return 'D';
});
console.log('成績:', scores);
console.log('評価:', grades); // ['B', 'A', 'C', 'A', 'B']

// 実践例: filterで抽出
const numbers: number[] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const evens: number[] = numbers.filter((n: number): boolean => n % 2 === 0);
const odds: number[] = numbers.filter((n: number): boolean => n % 2 !== 0);
console.log('偶数:', evens); // [2, 4, 6, 8, 10]
console.log('奇数:', odds);  // [1, 3, 5, 7, 9]

// 実践例: reduceで集約
const prices: number[] = [100, 200, 300, 400, 500];
const total: number = prices.reduce((sum: number, price: number): number => sum + price, 0);
console.log(`合計: ${total}円`); // 1500円

const max: number = prices.reduce((max: number, price: number): number => 
    price > max ? price : max, 0
);
console.log(`最大値: ${max}円`); // 500円

// 実践例: forEachで処理
const names: string[] = ['太郎', '花子', '次郎'];
names.forEach((name: string, index: number): void => {
    console.log(`${index + 1}番目: ${name}`);
});

// 実践例: 多次元配列
const matrix: number[][] = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];
console.log('2次元配列:', matrix);
console.log(`matrix[1][2] = ${matrix[1][2]}`); // 6

// 実践例: 配列の分割代入
const colors: string[] = ['赤', '青', '緑'];
const [first, second, third] = colors;
console.log(`最初: ${first}, 2番目: ${second}, 3番目: ${third}`);

// 実践例: スプレッド構文
const original: number[] = [1, 2, 3];
const copied: number[] = [...original];
const extended: number[] = [0, ...original, 4];
console.log('元の配列:', original);
console.log('コピー:', copied);
console.log('拡張:', extended); // [0, 1, 2, 3, 4]
```

---

## readonly修飾子（配列での利用）

### readonlyとは
readonly修飾子は、**配列を読み取り専用にする**機能です。配列の要素を変更できなくなります。

### readonlyの基本構文

```typescript
const 変数名: readonly 型[] = [値1, 値2, ...];
// または
const 変数名: ReadonlyArray<型> = [値1, 値2, ...];
```

### readonlyの特徴
- **要素の変更不可**: 配列の要素を変更できない
- **メソッドの制限**: 配列を変更するメソッド（push, pop, spliceなど）が使えない
- **読み取り専用**: 読み取り専用のメソッド（map, filter, forEachなど）は使える

### 実践例: readonly修飾子

```typescript
// src/readonly-array.ts
// readonly配列の作成（角カッコ記法）
const readonlyNumbers: readonly number[] = [1, 2, 3, 4, 5];
console.log('readonly配列:', readonlyNumbers);

// 読み取りは可能
console.log(`最初の要素: ${readonlyNumbers[0]}`); // 1
console.log(`長さ: ${readonlyNumbers.length}`);   // 5

// 変更は不可（エラーになる）
// readonlyNumbers[0] = 10; // エラー: readonly配列の要素は変更できない
// readonlyNumbers.push(6);  // エラー: pushメソッドは使用できない
// readonlyNumbers.pop();    // エラー: popメソッドは使用できない

// ReadonlyArray型の使用
const readonlyStrings: ReadonlyArray<string> = ['a', 'b', 'c'];
console.log('ReadonlyArray:', readonlyStrings);

// 読み取り専用のメソッドは使用可能
const doubled: number[] = readonlyNumbers.map((n: number): number => n * 2);
console.log('2倍:', doubled); // [2, 4, 6, 8, 10]

const filtered: number[] = readonlyNumbers.filter((n: number): boolean => n % 2 === 0);
console.log('偶数:', filtered); // [2, 4]

// 実践例: 関数の引数としてreadonly配列を使用
function sum(numbers: readonly number[]): number {
    // 関数内で配列を変更できない
    // numbers.push(10); // エラー
    return numbers.reduce((acc: number, n: number): number => acc + n, 0);
}

const scores: readonly number[] = [85, 92, 78, 96, 88];
console.log(`合計: ${sum(scores)}`); // 439

// 実践例: 定数として使用する配列
const DAYS_OF_WEEK: readonly string[] = [
    '日曜日', '月曜日', '火曜日', '水曜日', 
    '木曜日', '金曜日', '土曜日'
];

function getDayName(dayIndex: number): string {
    if (dayIndex >= 0 && dayIndex < DAYS_OF_WEEK.length) {
        return DAYS_OF_WEEK[dayIndex];
    }
    return '無効な日';
}

console.log(`0日目: ${getDayName(0)}`); // 日曜日
console.log(`3日目: ${getDayName(3)}`); // 水曜日

// 実践例: 設定値として使用
const CONFIG: readonly string[] = [
    'development',
    'production',
    'staging'
];

function isValidEnvironment(env: string): boolean {
    return CONFIG.includes(env);
}

console.log(`developmentは有効: ${isValidEnvironment('development')}`); // true
console.log(`testは有効: ${isValidEnvironment('test')}`);             // false

// 実践例: 型エイリアスでreadonly配列を定義
type ReadonlyStringArray = readonly string[];

const colors: ReadonlyStringArray = ['赤', '青', '緑'];
console.log('色:', colors);

// 実践例: 通常の配列からreadonly配列への変換
function processReadonlyArray(arr: readonly number[]): number[] {
    // 読み取り専用のメソッドは使用可能
    return arr.map((n: number): number => n * 2);
}

const original: number[] = [1, 2, 3];
const result: number[] = processReadonlyArray(original);
console.log('結果:', result); // [2, 4, 6]

// 注意: readonly配列を通常の配列に変換するには、スプレッド構文を使用
const readonlyArr: readonly number[] = [1, 2, 3];
const mutableArr: number[] = [...readonlyArr];
mutableArr.push(4); // OK
console.log('変更可能な配列:', mutableArr); // [1, 2, 3, 4]
```

---

## まとめ

この章では、TypeScriptの配列について学びました。

### 学んだこと
- 配列の基本と型定義（角カッコ記法、ジェネリクス記法）
- 配列の操作（追加、削除、検索、変換）
- readonly修飾子（読み取り専用配列）

### 重要なポイント
1. **型定義**: TypeScriptでは配列の型を明示できる
2. **角カッコ記法**: `型[]`が推奨される記法
3. **ジェネリクス記法**: `Array<型>`も使用可能
4. **readonly**: 配列を読み取り専用にする
5. **メソッド**: map, filter, reduceなどで効率的に処理できる

### 次のステップ
次の章では、オブジェクトについて詳しく学びます。

---

## 演習問題

### 問題1: 配列の型定義
数値の配列`[1, 2, 3, 4, 5]`を作成し、角カッコ記法とジェネリクス記法の両方で型を定義してください。

<details>
<summary>解答例</summary>

```typescript
// 角カッコ記法
const numbers1: number[] = [1, 2, 3, 4, 5];

// ジェネリクス記法
const numbers2: Array<number> = [1, 2, 3, 4, 5];

console.log(numbers1);
console.log(numbers2);
```
</details>

### 問題2: 配列の操作
配列`[1, 2, 3]`に4を追加し、その後最初の要素を削除してください。

<details>
<summary>解答例</summary>

```typescript
const numbers: number[] = [1, 2, 3];
numbers.push(4);
console.log('push後:', numbers); // [1, 2, 3, 4]

numbers.shift();
console.log('shift後:', numbers); // [2, 3, 4]
```
</details>

### 問題3: mapとfilter
数値の配列`[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`から、偶数のみを抽出し、それぞれを2倍にしてください。

<details>
<summary>解答例</summary>

```typescript
const numbers: number[] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const doubledEvens: number[] = numbers
    .filter((n: number): boolean => n % 2 === 0)
    .map((n: number): number => n * 2);

console.log('2倍された偶数:', doubledEvens); // [4, 8, 12, 16, 20]
```
</details>

### 問題4: reduce
数値の配列`[10, 20, 30, 40, 50]`の合計を計算してください。

<details>
<summary>解答例</summary>

```typescript
const numbers: number[] = [10, 20, 30, 40, 50];
const sum: number = numbers.reduce((acc: number, n: number): number => acc + n, 0);
console.log(`合計: ${sum}`); // 150
```
</details>

### 問題5: readonly配列
readonly配列`['月', '火', '水', '木', '金']`を作成し、各要素を表示してください。

<details>
<summary>解答例</summary>

```typescript
const weekdays: readonly string[] = ['月', '火', '水', '木', '金'];

weekdays.forEach((day: string, index: number): void => {
    console.log(`${index + 1}曜日: ${day}`);
});
```
</details>

---

お疲れ様でした！次の章に進みましょう。

