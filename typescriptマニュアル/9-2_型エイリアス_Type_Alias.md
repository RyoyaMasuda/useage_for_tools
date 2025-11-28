# 9-2. 型エイリアス (Type Alias)

この章では、型に別名を付ける「型エイリアス（Type Alias）」について学びます。型エイリアスを理解することで、複雑な型を簡潔に表現し、コードの可読性を向上させることができます。

---

## 目次

- [型エイリアス（Type alias）の基本](#型エイリアスtype-aliasの基本)
- [型エイリアスでの関数型定義](#型エイリアスでの関数型定義)
- [型エイリアスの活用場面](#型エイリアスの活用場面)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## 型エイリアス（Type alias）の基本

### 型エイリアスとは
型エイリアスは、**既存の型に別名を付ける**機能です。複雑な型を簡潔に表現できます。

### 型エイリアスの基本構文

```typescript
type 型エイリアス名 = 型;
```

### 型エイリアスの特徴
- **別名の定義**: 既存の型に分かりやすい名前を付ける
- **再利用性**: 同じ型を何度も使える
- **可読性**: コードが読みやすくなる
- **柔軟性**: 複雑な型を組み合わせられる

### 基本的な型エイリアス

```typescript
// 基本的な型エイリアス
type UserID = number;
type UserName = string;
type IsActive = boolean;

// 使用例
const userId: UserID = 123;
const userName: UserName = "太郎";
const isActive: IsActive = true;
```

### 実践例: 型エイリアスの基本

```typescript
// src/type-alias-basics.ts
// 基本的な型エイリアス
type Age = number;
type Name = string;
type Email = string;

// 使用例
const age: Age = 25;
const name: Name = "太郎";
const email: Email = "taro@example.com";

console.log(`名前: ${name}, 年齢: ${age}, メール: ${email}`);

// 実践例: オブジェクト型の型エイリアス
type Person = {
    name: string;
    age: number;
    email: string;
};

const person1: Person = {
    name: "太郎",
    age: 25,
    email: "taro@example.com"
};

const person2: Person = {
    name: "花子",
    age: 30,
    email: "hanako@example.com"
};

console.log(`person1: ${person1.name}, ${person1.age}歳`);
console.log(`person2: ${person2.name}, ${person2.age}歳`);

// 実践例: 配列型の型エイリアス
type NumberArray = number[];
type StringArray = string[];

const numbers: NumberArray = [1, 2, 3, 4, 5];
const strings: StringArray = ["a", "b", "c"];

console.log("数値配列:", numbers);
console.log("文字列配列:", strings);

// 実践例: 複合型の型エイリアス
type Point = {
    x: number;
    y: number;
};

type Rectangle = {
    topLeft: Point;
    bottomRight: Point;
};

const rect: Rectangle = {
    topLeft: { x: 0, y: 0 },
    bottomRight: { x: 100, y: 100 }
};

console.log(`矩形: (${rect.topLeft.x}, ${rect.topLeft.y}) - (${rect.bottomRight.x}, ${rect.bottomRight.y})`);

// 実践例: ユニオン型の型エイリアス
type Status = "pending" | "approved" | "rejected";
type ID = string | number;

const status1: Status = "pending";
const status2: Status = "approved";
// const status3: Status = "invalid"; // エラー: "invalid"はStatus型に含まれない

const id1: ID = "123";
const id2: ID = 456;

console.log(`ステータス1: ${status1}, ステータス2: ${status2}`);
console.log(`ID1: ${id1}, ID2: ${id2}`);

// 実践例: タプル型の型エイリアス
type Coordinate = [number, number];
type RGB = [number, number, number];

const point: Coordinate = [10, 20];
const color: RGB = [255, 128, 0];

console.log(`座標: (${point[0]}, ${point[1]})`);
console.log(`色: RGB(${color[0]}, ${color[1]}, ${color[2]})`);

// 実践例: 読み取り専用の型エイリアス
type ReadonlyPerson = {
    readonly name: string;
    readonly age: number;
};

const readonlyPerson: ReadonlyPerson = {
    name: "太郎",
    age: 25
};

// readonlyPerson.name = "次郎"; // エラー: readonlyプロパティは変更できない
console.log(`読み取り専用: ${readonlyPerson.name}, ${readonlyPerson.age}歳`);

// 実践例: オプショナルプロパティの型エイリアス
type User = {
    id: number;
    name: string;
    email?: string; // オプショナル
    age?: number;  // オプショナル
};

const user1: User = {
    id: 1,
    name: "太郎",
    email: "taro@example.com"
};

const user2: User = {
    id: 2,
    name: "花子"
    // emailとageは省略可能
};

console.log(`user1: ${user1.name}, ${user1.email}`);
console.log(`user2: ${user2.name}`);
```

---

## 型エイリアスでの関数型定義

### 関数型の型エイリアス
型エイリアスを使って、**関数の型を定義**できます。

### 関数型の基本構文

```typescript
type 関数型名 = (引数1: 型1, 引数2: 型2) => 戻り値の型;
```

### 実践例: 型エイリアスでの関数型定義

```typescript
// src/function-type-alias.ts
// 基本的な関数型の型エイリアス
type Calculator = (a: number, b: number) => number;
type Greeter = (name: string) => string;
type Validator = (value: string) => boolean;

// 関数型の使用例
const add: Calculator = (a: number, b: number): number => a + b;
const multiply: Calculator = (a: number, b: number): number => a * b;

console.log(`5 + 3 = ${add(5, 3)}`);        // 8
console.log(`5 * 3 = ${multiply(5, 3)}`);    // 15

const greet: Greeter = (name: string): string => `こんにちは、${name}さん！`;
console.log(greet("太郎")); // こんにちは、太郎さん！

const isValidEmail: Validator = (value: string): boolean => value.includes("@");
console.log(`有効なメール: ${isValidEmail("test@example.com")}`); // true

// 実践例: コールバック関数の型エイリアス
type Callback = (result: number) => void;
type ErrorHandler = (error: string) => void;

function processNumber(
    num: number,
    onSuccess: Callback,
    onError: ErrorHandler
): void {
    if (num >= 0) {
        onSuccess(num * 2);
    } else {
        onError("負の数は処理できません");
    }
}

processNumber(
    5,
    (result: number): void => console.log(`成功: ${result}`),
    (error: string): void => console.log(`エラー: ${error}`)
);

// 実践例: イベントハンドラーの型エイリアス
type ClickHandler = (event: MouseEvent) => void;
type KeyHandler = (key: string) => void;

const handleClick: ClickHandler = (event: MouseEvent): void => {
    console.log(`クリック位置: (${event.clientX}, ${event.clientY})`);
};

const handleKeyPress: KeyHandler = (key: string): void => {
    console.log(`キーが押されました: ${key}`);
};

// 実践例: 高階関数の型エイリアス
type Mapper<T, U> = (item: T) => U;
type Predicate<T> = (item: T) => boolean;
type Reducer<T, U> = (accumulator: U, item: T) => U;

function mapArray<T, U>(
    array: T[],
    mapper: Mapper<T, U>
): U[] {
    return array.map(mapper);
}

function filterArray<T>(
    array: T[],
    predicate: Predicate<T>
): T[] {
    return array.filter(predicate);
}

function reduceArray<T, U>(
    array: T[],
    reducer: Reducer<T, U>,
    initialValue: U
): U {
    return array.reduce(reducer, initialValue);
}

const numbers = [1, 2, 3, 4, 5];
const doubled = mapArray(numbers, (n: number): number => n * 2);
console.log("2倍:", doubled); // [2, 4, 6, 8, 10]

const evens = filterArray(numbers, (n: number): boolean => n % 2 === 0);
console.log("偶数:", evens); // [2, 4]

const sum = reduceArray(numbers, (acc: number, n: number): number => acc + n, 0);
console.log("合計:", sum); // 15

// 実践例: 非同期関数の型エイリアス
type AsyncFunction<T> = () => Promise<T>;
type AsyncMapper<T, U> = (item: T) => Promise<U>;

async function processAsync<T>(
    items: T[],
    mapper: AsyncMapper<T, string>
): Promise<string[]> {
    return Promise.all(items.map(mapper));
}

const asyncMapper: AsyncMapper<number, string> = async (num: number): Promise<string> => {
    return new Promise((resolve) => {
        setTimeout(() => resolve(`処理済み: ${num}`), 100);
    });
};

processAsync([1, 2, 3], asyncMapper).then((results: string[]): void => {
    console.log("非同期処理結果:", results);
});

// 実践例: オーバーロード風の型エイリアス
type StringOrNumber = string | number;

type Converter = {
    (value: string): number;
    (value: number): string;
};

// 実践例: メソッドの型エイリアス
type Getter<T> = () => T;
type Setter<T> = (value: T) => void;

class DataStore<T> {
    private data: T;
    private getter: Getter<T>;
    private setter: Setter<T>;
    
    constructor(initialValue: T) {
        this.data = initialValue;
        this.getter = (): T => this.data;
        this.setter = (value: T): void => {
            this.data = value;
        };
    }
    
    getValue(): T {
        return this.getter();
    }
    
    setValue(value: T): void {
        this.setter(value);
    }
}

const store = new DataStore<string>("初期値");
console.log(`値: ${store.getValue()}`); // 初期値
store.setValue("新しい値");
console.log(`値: ${store.getValue()}`); // 新しい値
```

---

## 型エイリアスの活用場面

### 型エイリアスの活用例
型エイリアスは、以下のような場面で特に有効です：

1. **複雑な型の簡潔化**: 長い型定義を短く表現
2. **再利用性の向上**: 同じ型を複数箇所で使用
3. **可読性の向上**: 意図が明確な名前を付ける
4. **型の組み合わせ**: 複数の型を組み合わせる

### 実践例: 型エイリアスの活用場面

```typescript
// src/type-alias-use-cases.ts
// 実践例1: APIレスポンスの型定義
type APIResponse<T> = {
    success: boolean;
    data: T;
    message?: string;
    timestamp: number;
};

type UserData = {
    id: number;
    name: string;
    email: string;
};

type UserResponse = APIResponse<UserData>;

function fetchUser(id: number): UserResponse {
    return {
        success: true,
        data: {
            id: id,
            name: "太郎",
            email: "taro@example.com"
        },
        message: "ユーザー情報を取得しました",
        timestamp: Date.now()
    };
}

const response = fetchUser(1);
console.log(`成功: ${response.success}, ユーザー: ${response.data.name}`);

// 実践例2: 設定オブジェクトの型定義
type DatabaseConfig = {
    host: string;
    port: number;
    username: string;
    password: string;
    database: string;
};

type ServerConfig = {
    port: number;
    host: string;
    env: "development" | "production" | "staging";
};

type AppConfig = {
    database: DatabaseConfig;
    server: ServerConfig;
};

const config: AppConfig = {
    database: {
        host: "localhost",
        port: 5432,
        username: "admin",
        password: "password",
        database: "mydb"
    },
    server: {
        port: 3000,
        host: "0.0.0.0",
        env: "development"
    }
};

console.log(`データベース: ${config.database.host}:${config.database.port}`);
console.log(`サーバー: ${config.server.host}:${config.server.port}`);

// 実践例3: イベントシステムの型定義
type EventType = "click" | "submit" | "change" | "load";

type EventHandler<T = Event> = (event: T) => void;

type EventMap = {
    click: EventHandler<MouseEvent>;
    submit: EventHandler<SubmitEvent>;
    change: EventHandler<Event>;
    load: EventHandler<Event>;
};

class EventEmitter {
    private handlers: Map<EventType, EventHandler[]> = new Map();
    
    on<T extends EventType>(
        type: T,
        handler: EventMap[T]
    ): void {
        if (!this.handlers.has(type)) {
            this.handlers.set(type, []);
        }
        this.handlers.get(type)!.push(handler as EventHandler);
    }
    
    emit(type: EventType, event: Event): void {
        const handlers = this.handlers.get(type);
        if (handlers) {
            handlers.forEach((handler) => handler(event));
        }
    }
}

// 実践例4: 状態管理の型定義
type LoadingState = {
    status: "loading";
};

type SuccessState<T> = {
    status: "success";
    data: T;
};

type ErrorState = {
    status: "error";
    error: string;
};

type AsyncState<T> = LoadingState | SuccessState<T> | ErrorState;

function handleState<T>(state: AsyncState<T>): void {
    switch (state.status) {
        case "loading":
            console.log("読み込み中...");
            break;
        case "success":
            console.log("成功:", state.data);
            break;
        case "error":
            console.log("エラー:", state.error);
            break;
    }
}

const loadingState: AsyncState<string> = { status: "loading" };
const successState: AsyncState<string> = { status: "success", data: "データ" };
const errorState: AsyncState<string> = { status: "error", error: "エラー" };

handleState(loadingState);
handleState(successState);
handleState(errorState);

// 実践例5: フォームの型定義
type FormField = {
    name: string;
    label: string;
    type: "text" | "email" | "number" | "password";
    required: boolean;
    placeholder?: string;
};

type FormData = Record<string, string | number>;

type FormValidation = {
    isValid: boolean;
    errors: Record<string, string>;
};

type FormConfig = {
    fields: FormField[];
    onSubmit: (data: FormData) => void;
    validate?: (data: FormData) => FormValidation;
};

const loginForm: FormConfig = {
    fields: [
        {
            name: "email",
            label: "メールアドレス",
            type: "email",
            required: true,
            placeholder: "example@mail.com"
        },
        {
            name: "password",
            label: "パスワード",
            type: "password",
            required: true
        }
    ],
    onSubmit: (data: FormData): void => {
        console.log("送信:", data);
    },
    validate: (data: FormData): FormValidation => {
        const errors: Record<string, string> = {};
        if (!data.email) {
            errors.email = "メールアドレスは必須です";
        }
        return {
            isValid: Object.keys(errors).length === 0,
            errors
        };
    }
};

// 実践例6: ユーティリティ型との組み合わせ
type Partial<T> = {
    [P in keyof T]?: T[P];
};

type Required<T> = {
    [P in keyof T]-?: T[P];
};

type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

type Person = {
    name: string;
    age: number;
    email?: string;
};

type PartialPerson = Partial<Person>;
type RequiredPerson = Required<Person>;
type ReadonlyPerson = Readonly<Person>;

const partial: PartialPerson = { name: "太郎" }; // ageとemailは省略可能
const required: RequiredPerson = { name: "花子", age: 30, email: "hanako@example.com" }; // すべて必須
const readonly: ReadonlyPerson = { name: "次郎", age: 25 }; // すべて読み取り専用

// 実践例7: 条件付き型の型エイリアス
type NonNullable<T> = T extends null | undefined ? never : T;
type Extract<T, U> = T extends U ? T : never;
type Exclude<T, U> = T extends U ? never : T;

type StringOrNumber = string | number | null | undefined;
type NonNullStringOrNumber = NonNullable<StringOrNumber>; // string | number

type Status = "pending" | "approved" | "rejected";
type ApprovedStatus = Extract<Status, "approved">; // "approved"
type NonApprovedStatus = Exclude<Status, "approved">; // "pending" | "rejected"
```

---

## まとめ

この章では、TypeScriptの型エイリアスについて学びました。

### 学んだこと
- 型エイリアスの基本: 既存の型に別名を付ける
- 関数型の型エイリアス: 関数の型を定義
- 型エイリアスの活用場面: 複雑な型の簡潔化、再利用性の向上

### 重要なポイント
1. **型エイリアス**: `type`キーワードで型に別名を付ける
2. **関数型**: 関数の型を型エイリアスで定義できる
3. **再利用性**: 同じ型を複数箇所で使用できる
4. **可読性**: 意図が明確な名前を付けることでコードが読みやすくなる
5. **柔軟性**: 複雑な型を組み合わせて新しい型を作成できる

### 次のステップ
次の章では、高度な型システムについて詳しく学びます。

---

## 演習問題

### 問題1: 基本的な型エイリアス
名前、年齢、メールアドレスを持つ`Person`型を型エイリアスで定義してください。

<details>
<summary>解答例</summary>

```typescript
type Person = {
    name: string;
    age: number;
    email: string;
};

const person: Person = {
    name: "太郎",
    age: 25,
    email: "taro@example.com"
};

console.log(`名前: ${person.name}, 年齢: ${person.age}`);
```
</details>

### 問題2: 関数型の型エイリアス
2つの数値を受け取り、数値を返す`Calculator`型を定義してください。

<details>
<summary>解答例</summary>

```typescript
type Calculator = (a: number, b: number) => number;

const add: Calculator = (a: number, b: number): number => a + b;
const multiply: Calculator = (a: number, b: number): number => a * b;

console.log(`5 + 3 = ${add(5, 3)}`);        // 8
console.log(`5 * 3 = ${multiply(5, 3)}`);    // 15
```
</details>

### 問題3: ユニオン型の型エイリアス
"pending"、"approved"、"rejected"のいずれかの値を取る`Status`型を定義してください。

<details>
<summary>解答例</summary>

```typescript
type Status = "pending" | "approved" | "rejected";

const status1: Status = "pending";
const status2: Status = "approved";

console.log(`ステータス1: ${status1}, ステータス2: ${status2}`);
```
</details>

### 問題4: 配列型の型エイリアス
数値の配列を表す`NumberArray`型を定義し、使用してください。

<details>
<summary>解答例</summary>

```typescript
type NumberArray = number[];

const numbers: NumberArray = [1, 2, 3, 4, 5];
const sum: number = numbers.reduce((acc: number, n: number): number => acc + n, 0);

console.log(`配列: ${numbers}, 合計: ${sum}`); // 配列: 1,2,3,4,5, 合計: 15
```
</details>

### 問題5: 複合型の型エイリアス
座標を表す`Point`型と、2つの点を持つ`Line`型を定義してください。

<details>
<summary>解答例</summary>

```typescript
type Point = {
    x: number;
    y: number;
};

type Line = {
    start: Point;
    end: Point;
};

const line: Line = {
    start: { x: 0, y: 0 },
    end: { x: 100, y: 100 }
};

console.log(`線: (${line.start.x}, ${line.start.y}) - (${line.end.x}, ${line.end.y})`);
```
</details>

---

お疲れ様でした！次の章に進みましょう。

