# 9-1. Interface

この章では、TypeScriptの「Interface（インターフェース）」について学びます。Interfaceは、オブジェクトの構造を定義するための強力な機能です。Interfaceを理解することで、より型安全で保守しやすいコードを書けるようになります。

---

## 目次

- [Interfaceの基本](#interfaceの基本)
- [Interfaceでの関数型定義](#interfaceでの関数型定義)
- [ネストしたInterfaceの設計](#ネストしたinterfaceの設計)
- [Interfaceの拡張（extends）](#interfaceの拡張extends)
- [まとめ](#まとめ)
- [演習問題](#演習問題)

---

## Interfaceの基本

### Interfaceとは
Interface（インターフェース）は、**オブジェクトの構造（プロパティとメソッド）を定義する**ための仕組みです。オブジェクトがどのようなプロパティを持ち、どのような型であるべきかを明確に指定できます。

### 日常生活での例
- **契約書**: 必要な項目（名前、住所、電話番号など）が決まっている
- **申込書**: 記入すべき項目が決まっている（氏名、年齢、メールアドレスなど）
- **レシピ**: 必要な材料と手順が決まっている
- **設計図**: 建物の構造（部屋の数、面積など）が決まっている

### Interfaceの基本構文

```typescript
interface インターフェース名 {
    プロパティ1: 型1;
    プロパティ2: 型2;
    プロパティ3?: 型3;  // オプショナル（省略可能）
}
```

### 基本的なInterfaceの作成

```typescript
// ユーザー情報を表すInterface
interface User {
    name: string;
    age: number;
    email: string;
}

// Interfaceを使用してオブジェクトを作成
const user: User = {
    name: "太郎",
    age: 25,
    email: "taro@example.com"
};
```

### Interfaceの特徴
1. **型安全性**: オブジェクトの構造を厳密にチェックできる
2. **コード補完**: IDEがプロパティ名を自動補完してくれる
3. **ドキュメント化**: コード自体が仕様書になる
4. **再利用性**: 同じ構造を複数の場所で使用できる

### 実践例: Interfaceの基本

```typescript
// src/interface-basics.ts
// 基本的なInterface
interface Person {
    name: string;
    age: number;
    email: string;
}

// Interfaceを使用してオブジェクトを作成
const person: Person = {
    name: "太郎",
    age: 25,
    email: "taro@example.com"
};

console.log(`名前: ${person.name}, 年齢: ${person.age}歳, メール: ${person.email}`);

// 実践例: 商品情報のInterface
interface Product {
    id: number;
    name: string;
    price: number;
    inStock: boolean;
}

const product: Product = {
    id: 1,
    name: "ノートパソコン",
    price: 99800,
    inStock: true
};

console.log(`商品: ${product.name}, 価格: ${product.price}円, 在庫: ${product.inStock ? "あり" : "なし"}`);

// 実践例: 関数の引数として使用
function displayPerson(person: Person): void {
    console.log(`名前: ${person.name}`);
    console.log(`年齢: ${person.age}歳`);
    console.log(`メール: ${person.email}`);
}

displayPerson(person);

// 実践例: 関数の戻り値として使用
function createPerson(name: string, age: number, email: string): Person {
    return {
        name: name,
        age: age,
        email: email
    };
}

const newPerson = createPerson("花子", 30, "hanako@example.com");
console.log(newPerson);

// 実践例: 配列の要素の型として使用
const people: Person[] = [
    { name: "太郎", age: 25, email: "taro@example.com" },
    { name: "花子", age: 30, email: "hanako@example.com" },
    { name: "次郎", age: 28, email: "jiro@example.com" }
];

people.forEach((person: Person): void => {
    console.log(`${person.name} (${person.age}歳)`);
});
```

### オプショナルなプロパティ（?）

Interfaceでは、`?`を使ってプロパティを省略可能にできます。

```typescript
interface User {
    name: string;
    age: number;
    email?: string;  // オプショナル（省略可能）
}
```

### 実践例: オプショナルなプロパティ

```typescript
// src/optional-properties.ts
// オプショナルなプロパティを持つInterface
interface User {
    name: string;
    age: number;
    email?: string;  // オプショナル
    phone?: string;  // オプショナル
}

// emailとphoneを省略可能
const user1: User = {
    name: "太郎",
    age: 25
};

// emailのみ指定
const user2: User = {
    name: "花子",
    age: 30,
    email: "hanako@example.com"
};

// すべて指定
const user3: User = {
    name: "次郎",
    age: 28,
    email: "jiro@example.com",
    phone: "090-1234-5678"
};

console.log(user1);
console.log(user2);
console.log(user3);

// 実践例: オプショナルプロパティのチェック
function displayUser(user: User): void {
    console.log(`名前: ${user.name}, 年齢: ${user.age}歳`);
    
    if (user.email) {
        console.log(`メール: ${user.email}`);
    } else {
        console.log("メール: 未設定");
    }
    
    if (user.phone) {
        console.log(`電話: ${user.phone}`);
    } else {
        console.log("電話: 未設定");
    }
}

displayUser(user1);
displayUser(user2);
displayUser(user3);

// 実践例: 設定情報のInterface
interface Config {
    host: string;
    port: number;
    timeout?: number;      // オプショナル
    retries?: number;      // オプショナル
    verbose?: boolean;     // オプショナル
}

const config1: Config = {
    host: "localhost",
    port: 3000
};

const config2: Config = {
    host: "example.com",
    port: 8080,
    timeout: 5000,
    retries: 3,
    verbose: true
};

console.log(config1);
console.log(config2);
```

### readonlyプロパティ

`readonly`修飾子を使って、プロパティを読み取り専用にできます。

```typescript
interface User {
    readonly id: number;  // 読み取り専用
    name: string;
    age: number;
}
```

### 実践例: readonlyプロパティ

```typescript
// src/readonly-properties.ts
// readonlyプロパティを持つInterface
interface User {
    readonly id: number;  // 読み取り専用
    name: string;
    age: number;
}

const user: User = {
    id: 1,
    name: "太郎",
    age: 25
};

console.log(`ID: ${user.id}, 名前: ${user.name}`);

// 読み取りは可能
const userId = user.id;

// 変更は不可（エラーになる）
// user.id = 2;  // エラー: readonlyプロパティは変更できない

// 他のプロパティは変更可能
user.name = "花子";
user.age = 30;

console.log(`ID: ${user.id}, 名前: ${user.name}, 年齢: ${user.age}歳`);

// 実践例: 設定情報（一部が読み取り専用）
interface AppConfig {
    readonly appName: string;  // 読み取り専用
    readonly version: string;  // 読み取り専用
    theme: string;              // 変更可能
    language: string;           // 変更可能
}

const config: AppConfig = {
    appName: "MyApp",
    version: "1.0.0",
    theme: "light",
    language: "ja"
};

console.log(`アプリ: ${config.appName} v${config.version}`);
console.log(`テーマ: ${config.theme}, 言語: ${config.language}`);

// config.appName = "NewApp";  // エラー: readonlyプロパティは変更できない
config.theme = "dark";         // OK
config.language = "en";        // OK

console.log(`テーマ: ${config.theme}, 言語: ${config.language}`);
```

---

## Interfaceでの関数型定義

### Interfaceでの関数型定義とは
Interfaceでは、**関数の型も定義**できます。オブジェクトが持つメソッドのシグネチャ（引数と戻り値の型）を指定できます。

### 関数型定義の基本構文

```typescript
interface インターフェース名 {
    メソッド名(引数1: 型1, 引数2: 型2): 戻り値の型;
}
```

### 基本的な関数型定義

```typescript
interface Calculator {
    add(a: number, b: number): number;
    subtract(a: number, b: number): number;
}
```

### 実践例: Interfaceでの関数型定義

```typescript
// src/interface-functions.ts
// 関数型定義を持つInterface
interface Calculator {
    add(a: number, b: number): number;
    subtract(a: number, b: number): number;
    multiply(a: number, b: number): number;
    divide(a: number, b: number): number;
}

// Interfaceを実装したオブジェクト
const calculator: Calculator = {
    add: (a: number, b: number): number => a + b,
    subtract: (a: number, b: number): number => a - b,
    multiply: (a: number, b: number): number => a * b,
    divide: (a: number, b: number): number => a / b
};

console.log(`10 + 5 = ${calculator.add(10, 5)}`);      // 15
console.log(`10 - 5 = ${calculator.subtract(10, 5)}`); // 5
console.log(`10 * 5 = ${calculator.multiply(10, 5)}`); // 50
console.log(`10 / 5 = ${calculator.divide(10, 5)}`);   // 2

// 実践例: ユーザー管理のInterface
interface UserService {
    createUser(name: string, age: number): User;
    getUser(id: number): User | undefined;
    updateUser(id: number, updates: Partial<User>): boolean;
    deleteUser(id: number): boolean;
}

interface User {
    id: number;
    name: string;
    age: number;
}

// UserServiceを実装したオブジェクト
const userService: UserService = {
    createUser: (name: string, age: number): User => {
        return {
            id: Date.now(),
            name: name,
            age: age
        };
    },
    
    getUser: (id: number): User | undefined => {
        // 実際の実装ではデータベースから取得
        if (id === 1) {
            return { id: 1, name: "太郎", age: 25 };
        }
        return undefined;
    },
    
    updateUser: (id: number, updates: Partial<User>): boolean => {
        // 実際の実装ではデータベースを更新
        console.log(`ユーザー${id}を更新:`, updates);
        return true;
    },
    
    deleteUser: (id: number): boolean => {
        // 実際の実装ではデータベースから削除
        console.log(`ユーザー${id}を削除`);
        return true;
    }
};

const newUser = userService.createUser("花子", 30);
console.log("新規ユーザー:", newUser);

const user = userService.getUser(1);
console.log("ユーザー取得:", user);

userService.updateUser(1, { age: 26 });
userService.deleteUser(1);

// 実践例: イベントハンドラーのInterface
interface EventHandler {
    onClick(event: MouseEvent): void;
    onKeyPress(event: KeyboardEvent): void;
    onFocus(event: FocusEvent): void;
}

// イベントハンドラーを実装したオブジェクト
const handler: EventHandler = {
    onClick: (event: MouseEvent): void => {
        console.log("クリックイベント:", event);
    },
    
    onKeyPress: (event: KeyboardEvent): void => {
        console.log("キー入力イベント:", event);
    },
    
    onFocus: (event: FocusEvent): void => {
        console.log("フォーカスイベント:", event);
    }
};

// 実践例: データベース操作のInterface
interface Database {
    connect(): Promise<void>;
    disconnect(): void;
    query(sql: string): Promise<any[]>;
    execute(sql: string): Promise<number>;
}

// データベース操作を実装したオブジェクト（簡易版）
const database: Database = {
    connect: async (): Promise<void> => {
        console.log("データベースに接続しました");
    },
    
    disconnect: (): void => {
        console.log("データベースから切断しました");
    },
    
    query: async (sql: string): Promise<any[]> => {
        console.log(`クエリ実行: ${sql}`);
        return [];
    },
    
    execute: async (sql: string): Promise<number> => {
        console.log(`SQL実行: ${sql}`);
        return 0;
    }
};

// 使用例
async function useDatabase(): Promise<void> {
    await database.connect();
    const results = await database.query("SELECT * FROM users");
    console.log("結果:", results);
    database.disconnect();
}

useDatabase();
```

### 関数型の別名定義

Interface内で関数型を別名として定義することもできます。

```typescript
interface ApiClient {
    get: (url: string) => Promise<Response>;
    post: (url: string, data: any) => Promise<Response>;
}
```

### 実践例: 関数型の別名定義

```typescript
// src/function-type-alias.ts
// 関数型を別名として定義
interface ApiClient {
    get: (url: string) => Promise<Response>;
    post: (url: string, data: any) => Promise<Response>;
    put: (url: string, data: any) => Promise<Response>;
    delete: (url: string) => Promise<Response>;
}

// 実装
const apiClient: ApiClient = {
    get: async (url: string): Promise<Response> => {
        console.log(`GET ${url}`);
        return new Response();
    },
    
    post: async (url: string, data: any): Promise<Response> => {
        console.log(`POST ${url}`, data);
        return new Response();
    },
    
    put: async (url: string, data: any): Promise<Response> => {
        console.log(`PUT ${url}`, data);
        return new Response();
    },
    
    delete: async (url: string): Promise<Response> => {
        console.log(`DELETE ${url}`);
        return new Response();
    }
};

// 使用例
apiClient.get("/users");
apiClient.post("/users", { name: "太郎", age: 25 });
```

---

## ネストしたInterfaceの設計

### ネストしたInterfaceとは
ネストしたInterfaceは、**Interfaceの中に別のInterfaceを定義する**機能です。複雑なデータ構造を階層的に表現できます。

### ネストしたInterfaceの基本構文

```typescript
interface 親Interface {
    プロパティ1: 型1;
    ネストしたInterface: {
        プロパティ2: 型2;
        プロパティ3: 型3;
    };
}
```

### 基本的なネストしたInterface

```typescript
interface User {
    name: string;
    address: {
        street: string;
        city: string;
        zipCode: string;
    };
}
```

### 実践例: ネストしたInterface

```typescript
// src/nested-interface.ts
// ネストしたInterface
interface User {
    id: number;
    name: string;
    address: {
        street: string;
        city: string;
        zipCode: string;
        country: string;
    };
    contact: {
        email: string;
        phone?: string;
    };
}

const user: User = {
    id: 1,
    name: "太郎",
    address: {
        street: "1-2-3 サンプル町",
        city: "東京",
        zipCode: "123-4567",
        country: "日本"
    },
    contact: {
        email: "taro@example.com",
        phone: "090-1234-5678"
    }
};

console.log(`ユーザー: ${user.name}`);
console.log(`住所: ${user.address.city} ${user.address.street}`);
console.log(`メール: ${user.contact.email}`);

// 実践例: 商品情報のネスト
interface Product {
    id: number;
    name: string;
    price: number;
    details: {
        description: string;
        category: string;
        tags: string[];
    };
    inventory: {
        stock: number;
        warehouse: string;
    };
}

const product: Product = {
    id: 1,
    name: "ノートパソコン",
    price: 99800,
    details: {
        description: "高性能なノートパソコン",
        category: "コンピュータ",
        tags: ["ノートPC", "高性能", "軽量"]
    },
    inventory: {
        stock: 50,
        warehouse: "東京倉庫"
    }
};

console.log(`商品: ${product.name}`);
console.log(`カテゴリ: ${product.details.category}`);
console.log(`在庫: ${product.inventory.stock}個`);

// 実践例: より深いネスト
interface Company {
    name: string;
    location: {
        address: {
            street: string;
            city: string;
            zipCode: string;
        };
        coordinates: {
            latitude: number;
            longitude: number;
        };
    };
    employees: {
        total: number;
        departments: {
            sales: number;
            engineering: number;
            marketing: number;
        };
    };
}

const company: Company = {
    name: "サンプル株式会社",
    location: {
        address: {
            street: "1-2-3 ビジネス街",
            city: "東京",
            zipCode: "100-0001"
        },
        coordinates: {
            latitude: 35.6812,
            longitude: 139.7671
        }
    },
    employees: {
        total: 100,
        departments: {
            sales: 30,
            engineering: 50,
            marketing: 20
        }
    }
};

console.log(`会社: ${company.name}`);
console.log(`住所: ${company.location.address.city}`);
console.log(`従業員数: ${company.employees.total}人`);
console.log(`エンジニア: ${company.employees.departments.engineering}人`);

// 実践例: 配列を含むネスト
interface BlogPost {
    id: number;
    title: string;
    author: {
        name: string;
        email: string;
        profile: {
            bio: string;
            social: {
                twitter?: string;
                github?: string;
            };
        };
    };
    comments: {
        total: number;
        items: {
            id: number;
            author: string;
            content: string;
            date: Date;
        }[];
    };
}

const blogPost: BlogPost = {
    id: 1,
    title: "TypeScript入門",
    author: {
        name: "太郎",
        email: "taro@example.com",
        profile: {
            bio: "TypeScript開発者",
            social: {
                twitter: "@taro",
                github: "taro-dev"
            }
        }
    },
    comments: {
        total: 2,
        items: [
            {
                id: 1,
                author: "花子",
                content: "とても参考になりました！",
                date: new Date()
            },
            {
                id: 2,
                author: "次郎",
                content: "続きが楽しみです",
                date: new Date()
            }
        ]
    }
};

console.log(`タイトル: ${blogPost.title}`);
console.log(`著者: ${blogPost.author.name}`);
console.log(`コメント数: ${blogPost.comments.total}`);
blogPost.comments.items.forEach((comment): void => {
    console.log(`- ${comment.author}: ${comment.content}`);
});
```

### 別のInterfaceを参照する方法

ネストした構造を別のInterfaceとして定義し、参照することもできます。

```typescript
interface Address {
    street: string;
    city: string;
    zipCode: string;
}

interface User {
    name: string;
    address: Address;  // 別のInterfaceを参照
}
```

### 実践例: 別のInterfaceを参照

```typescript
// src/interface-reference.ts
// 別のInterfaceを定義
interface Address {
    street: string;
    city: string;
    zipCode: string;
    country: string;
}

interface Contact {
    email: string;
    phone?: string;
}

// 別のInterfaceを参照
interface User {
    id: number;
    name: string;
    address: Address;   // 別のInterfaceを参照
    contact: Contact;   // 別のInterfaceを参照
}

const user: User = {
    id: 1,
    name: "太郎",
    address: {
        street: "1-2-3 サンプル町",
        city: "東京",
        zipCode: "123-4567",
        country: "日本"
    },
    contact: {
        email: "taro@example.com",
        phone: "090-1234-5678"
    }
};

console.log(`ユーザー: ${user.name}`);
console.log(`住所: ${user.address.city}`);

// 実践例: 複数のInterfaceを組み合わせ
interface ProductDetails {
    description: string;
    category: string;
    tags: string[];
}

interface Inventory {
    stock: number;
    warehouse: string;
}

interface Product {
    id: number;
    name: string;
    price: number;
    details: ProductDetails;    // 別のInterfaceを参照
    inventory: Inventory;        // 別のInterfaceを参照
}

const product: Product = {
    id: 1,
    name: "ノートパソコン",
    price: 99800,
    details: {
        description: "高性能なノートパソコン",
        category: "コンピュータ",
        tags: ["ノートPC", "高性能"]
    },
    inventory: {
        stock: 50,
        warehouse: "東京倉庫"
    }
};

console.log(`商品: ${product.name}`);
console.log(`カテゴリ: ${product.details.category}`);
```

---

## Interfaceの拡張（extends）

### Interfaceの拡張とは
Interfaceの拡張（extends）は、**既存のInterfaceを継承して新しいInterfaceを作成する**機能です。共通のプロパティを再利用できます。

### extendsの基本構文

```typescript
interface 新しいInterface extends 既存のInterface {
    追加のプロパティ: 型;
}
```

### 基本的な拡張

```typescript
interface Person {
    name: string;
    age: number;
}

interface Employee extends Person {
    employeeId: number;
    department: string;
}
```

### 実践例: Interfaceの拡張

```typescript
// src/interface-extends.ts
// 基本のInterface
interface Person {
    name: string;
    age: number;
    email: string;
}

// Personを拡張したInterface
interface Employee extends Person {
    employeeId: number;
    department: string;
    salary: number;
}

const employee: Employee = {
    name: "太郎",
    age: 25,
    email: "taro@example.com",
    employeeId: 1001,
    department: "開発部",
    salary: 500000
};

console.log(`従業員: ${employee.name}`);
console.log(`部署: ${employee.department}`);
console.log(`給与: ${employee.salary}円`);

// 実践例: 複数のInterfaceを拡張
interface Animal {
    name: string;
    species: string;
}

interface Flyable {
    canFly: boolean;
    maxAltitude: number;
}

interface Swimmable {
    canSwim: boolean;
    maxDepth: number;
}

// 複数のInterfaceを拡張
interface Bird extends Animal, Flyable {
    wingSpan: number;
}

interface Fish extends Animal, Swimmable {
    finCount: number;
}

const bird: Bird = {
    name: "スズメ",
    species: "鳥類",
    canFly: true,
    maxAltitude: 1000,
    wingSpan: 20
};

const fish: Fish = {
    name: "マグロ",
    species: "魚類",
    canSwim: true,
    maxDepth: 500,
    finCount: 5
};

console.log(`鳥: ${bird.name}, 飛べる: ${bird.canFly}`);
console.log(`魚: ${fish.name}, 泳げる: ${fish.canSwim}`);

// 実践例: 多段階の拡張
interface BaseEntity {
    id: number;
    createdAt: Date;
    updatedAt: Date;
}

interface User extends BaseEntity {
    name: string;
    email: string;
}

interface Admin extends User {
    role: string;
    permissions: string[];
}

const admin: Admin = {
    id: 1,
    createdAt: new Date(),
    updatedAt: new Date(),
    name: "管理者",
    email: "admin@example.com",
    role: "super_admin",
    permissions: ["read", "write", "delete"]
};

console.log(`管理者: ${admin.name}`);
console.log(`権限: ${admin.permissions.join(", ")}`);

// 実践例: オプショナルプロパティの拡張
interface BaseConfig {
    host: string;
    port: number;
}

interface ExtendedConfig extends BaseConfig {
    timeout?: number;
    retries?: number;
    verbose?: boolean;
}

const config: ExtendedConfig = {
    host: "localhost",
    port: 3000,
    timeout: 5000,
    verbose: true
};

console.log(`設定: ${config.host}:${config.port}`);
console.log(`タイムアウト: ${config.timeout}ms`);

// 実践例: メソッドを含む拡張
interface Readable {
    read(): string;
}

interface Writable {
    write(data: string): void;
}

interface ReadWrite extends Readable, Writable {
    clear(): void;
}

class File implements ReadWrite {
    private content: string = "";
    
    read(): string {
        return this.content;
    }
    
    write(data: string): void {
        this.content = data;
    }
    
    clear(): void {
        this.content = "";
    }
}

const file = new File();
file.write("Hello, TypeScript!");
console.log(`内容: ${file.read()}`);
file.clear();
console.log(`クリア後: ${file.read()}`);
```

### プロパティのオーバーライド

拡張したInterfaceで、既存のプロパティの型を変更することはできませんが、オプショナルにすることは可能です。

```typescript
interface Base {
    name: string;
    age: number;
}

interface Extended extends Base {
    name?: string;  // エラー: 型を変更できない
    // age?: number;  // OK: オプショナルにできる
}
```

### 実践例: プロパティのオーバーライド

```typescript
// src/property-override.ts
// 基本のInterface
interface BaseUser {
    id: number;
    name: string;
    email: string;
}

// 拡張時に一部をオプショナルに
interface PartialUser extends BaseUser {
    email?: string;  // オプショナルにできる
}

const partialUser: PartialUser = {
    id: 1,
    name: "太郎"
    // emailは省略可能
};

console.log(partialUser);
```

---

## まとめ

この章では、TypeScriptのInterfaceについて学びました。

### 学んだこと
- **Interfaceの基本**: オブジェクトの構造を定義する
- **オプショナルプロパティ**: `?`を使ってプロパティを省略可能にする
- **readonlyプロパティ**: `readonly`で読み取り専用にする
- **関数型定義**: Interface内で関数の型を定義する
- **ネストしたInterface**: Interfaceの中に別のInterfaceを定義する
- **Interfaceの拡張**: `extends`で既存のInterfaceを継承する

### 重要なポイント
1. **型安全性**: Interfaceでオブジェクトの構造を厳密にチェックできる
2. **コード補完**: IDEがプロパティ名を自動補完してくれる
3. **再利用性**: 同じ構造を複数の場所で使用できる
4. **拡張性**: `extends`で既存のInterfaceを拡張できる
5. **ネスト**: 複雑なデータ構造を階層的に表現できる

### Interfaceの利点
- **ドキュメント化**: コード自体が仕様書になる
- **エラー検出**: コンパイル時に型エラーを発見できる
- **リファクタリング**: 変更時の影響範囲が分かりやすい
- **チーム開発**: 共通の型定義で一貫性を保てる

### 次のステップ
次の章では、型エイリアス（Type Alias）について詳しく学びます。

---

## 演習問題

### 問題1: Interfaceの基本
ユーザー情報を表すInterfaceを作成し、オブジェクトを作成してください。

<details>
<summary>解答例</summary>

```typescript
interface User {
    id: number;
    name: string;
    age: number;
    email: string;
}

const user: User = {
    id: 1,
    name: "太郎",
    age: 25,
    email: "taro@example.com"
};

console.log(`ユーザー: ${user.name}, ${user.age}歳`);
```
</details>

### 問題2: オプショナルプロパティ
電話番号をオプショナルなプロパティとして持つInterfaceを作成してください。

<details>
<summary>解答例</summary>

```typescript
interface Contact {
    name: string;
    email: string;
    phone?: string;  // オプショナル
}

const contact1: Contact = {
    name: "太郎",
    email: "taro@example.com"
};

const contact2: Contact = {
    name: "花子",
    email: "hanako@example.com",
    phone: "090-1234-5678"
};

console.log(contact1);
console.log(contact2);
```
</details>

### 問題3: Interfaceでの関数型定義
計算機のInterfaceを作成し、加算と減算のメソッドを定義してください。

<details>
<summary>解答例</summary>

```typescript
interface Calculator {
    add(a: number, b: number): number;
    subtract(a: number, b: number): number;
}

const calculator: Calculator = {
    add: (a: number, b: number): number => a + b,
    subtract: (a: number, b: number): number => a - b
};

console.log(`10 + 5 = ${calculator.add(10, 5)}`);      // 15
console.log(`10 - 5 = ${calculator.subtract(10, 5)}`); // 5
```
</details>

### 問題4: ネストしたInterface
住所情報をネストしたInterfaceとして定義し、ユーザー情報に含めてください。

<details>
<summary>解答例</summary>

```typescript
interface User {
    name: string;
    address: {
        street: string;
        city: string;
        zipCode: string;
    };
}

const user: User = {
    name: "太郎",
    address: {
        street: "1-2-3 サンプル町",
        city: "東京",
        zipCode: "123-4567"
    }
};

console.log(`ユーザー: ${user.name}`);
console.log(`住所: ${user.address.city} ${user.address.street}`);
```
</details>

### 問題5: Interfaceの拡張
基本のPerson Interfaceを拡張して、Employee Interfaceを作成してください。

<details>
<summary>解答例</summary>

```typescript
interface Person {
    name: string;
    age: number;
}

interface Employee extends Person {
    employeeId: number;
    department: string;
}

const employee: Employee = {
    name: "太郎",
    age: 25,
    employeeId: 1001,
    department: "開発部"
};

console.log(`従業員: ${employee.name}`);
console.log(`部署: ${employee.department}`);
```
</details>

---

お疲れ様でした！次の章に進みましょう。

