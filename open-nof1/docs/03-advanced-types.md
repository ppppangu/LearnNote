# 第三部分：高级类型

> 本章介绍工具类型、类型守卫、可选链等高级特性

---

## 1. 工具类型（Utility Types）

TypeScript 内置了一系列工具类型，用于常见的类型转换操作。

### Partial\<T\> - 所有属性变为可选

```typescript
// 将类型 T 的所有属性变为可选

interface User {
    name: string;
    age: number;
    email: string;
}

// Partial<User> 等价于：
// {
//     name?: string;
//     age?: number;
//     email?: string;
// }

// 常用于更新操作：只需要提供要更新的字段
function updateUser(id: string, updates: Partial<User>): User {
    const user = getUserById(id);
    return { ...user, ...updates };
}

updateUser("123", { name: "新名字" });  // 只更新 name
updateUser("123", { age: 30 });         // 只更新 age
```

### Required\<T\> - 所有属性变为必需

```typescript
// 将类型 T 的所有属性变为必需（移除 ?）

interface Config {
    host?: string;
    port?: number;
    debug?: boolean;
}

// Required<Config> 等价于：
// {
//     host: string;
//     port: number;
//     debug: boolean;
// }

function startServer(config: Required<Config>) {
    // 所有配置项都是必需的
    console.log(`Starting server at ${config.host}:${config.port}`);
}

startServer({ host: "localhost", port: 3000, debug: true });  // OK
// startServer({ host: "localhost" });  // 错误！缺少 port 和 debug
```

### Pick\<T, K\> - 选取部分属性

```typescript
// 从类型 T 中选取属性 K

interface User {
    id: string;
    name: string;
    age: number;
    email: string;
    password: string;
}

// 只选取公开信息
type PublicUser = Pick<User, "id" | "name" | "age">;
// {
//     id: string;
//     name: string;
//     age: number;
// }

// 创建用户时不需要 id
type CreateUserInput = Pick<User, "name" | "age" | "email" | "password">;
```

### Omit\<T, K\> - 排除部分属性

```typescript
// 从类型 T 中排除属性 K（与 Pick 相反）

interface User {
    id: string;
    name: string;
    age: number;
    email: string;
    password: string;
}

// 排除敏感信息
type SafeUser = Omit<User, "password">;
// {
//     id: string;
//     name: string;
//     age: number;
//     email: string;
// }

// 创建用户时排除 id（由系统生成）
type CreateUserInput = Omit<User, "id">;
```

### Readonly\<T\> - 所有属性变为只读

```typescript
// 将类型 T 的所有属性变为只读

interface Point {
    x: number;
    y: number;
}

const origin: Readonly<Point> = { x: 0, y: 0 };
// origin.x = 10;  // 错误！不能修改只读属性

// 常用于确保数据不被意外修改
function processData(data: Readonly<DataType>) {
    // data 的属性不能被修改
}
```

### 项目中的 Readonly 示例

```typescript
// 来自 open-nof1.ai/app/layout.tsx

export default function RootLayout({
    children,
}: Readonly<{
    children: React.ReactNode;
}>) {
    // children 参数是只读的，不能被修改
    return (
        <html lang="en">
            <body>{children}</body>
        </html>
    );
}
```

### Record\<K, V\> - 创建对象类型

```typescript
// 创建键为 K、值为 V 的对象类型

// 基础用法
type StringMap = Record<string, string>;
const map: StringMap = {
    name: "张三",
    city: "北京"
};

// 限定键的类型
type Role = "admin" | "user" | "guest";
type RolePermissions = Record<Role, string[]>;

const permissions: RolePermissions = {
    admin: ["read", "write", "delete"],
    user: ["read", "write"],
    guest: ["read"]
};

// 对象字面量
type IconMap = Record<string, React.ComponentType>;
```

### 其他常用工具类型

```typescript
// ReturnType<T> - 获取函数返回值类型
function getUser() {
    return { name: "张三", age: 25 };
}
type UserType = ReturnType<typeof getUser>;  // { name: string; age: number }

// Parameters<T> - 获取函数参数类型
function add(a: number, b: number): number {
    return a + b;
}
type AddParams = Parameters<typeof add>;  // [number, number]

// NonNullable<T> - 排除 null 和 undefined
type MaybeString = string | null | undefined;
type DefinitelyString = NonNullable<MaybeString>;  // string

// Exclude<T, U> - 从 T 中排除可以赋值给 U 的类型
type AllTypes = "a" | "b" | "c" | "d";
type SomeTypes = Exclude<AllTypes, "a" | "b">;  // "c" | "d"

// Extract<T, U> - 从 T 中提取可以赋值给 U 的类型
type Extracted = Extract<AllTypes, "a" | "b" | "x">;  // "a" | "b"

// Awaited<T> - 获取 Promise 的解析类型
type PromiseString = Promise<string>;
type ResolvedType = Awaited<PromiseString>;  // string
```

---

## 2. 类型守卫（Type Guards）

类型守卫用于在代码块中收窄类型，让 TypeScript 知道更具体的类型。

### typeof 类型守卫

```typescript
// typeof 用于原始类型的判断

function printValue(value: string | number) {
    if (typeof value === "string") {
        // 在这个分支中，TypeScript 知道 value 是 string
        console.log(value.toUpperCase());
        console.log(value.length);
    } else {
        // 在这个分支中，value 是 number
        console.log(value.toFixed(2));
    }
}

// typeof 可以判断的类型：
// "string" | "number" | "boolean" | "undefined" | "object" | "function" | "symbol" | "bigint"
```

### 项目中的 typeof 示例

```typescript
// 来自 open-nof1.ai/lib/trading/current-market-state.ts

// 检查返回值是否为数字
if (openInterest && typeof openInterest.openInterestAmount === "number") {
    openInterestData.latest = openInterest.openInterestAmount;
    openInterestData.average = openInterest.openInterestAmount;
}

if (fundingRates && typeof fundingRates.fundingRate === "number") {
    fundingRate = fundingRates.fundingRate;
}
```

### 真值检查（Truthiness Narrowing）

```typescript
// 检查值是否为真值，排除 null/undefined

function printName(name: string | null | undefined) {
    if (name) {
        // name 是 string（排除了 null 和 undefined）
        console.log(name.toUpperCase());
    }
}

// 常用于可选属性
interface User {
    name: string;
    nickname?: string;
}

function greet(user: User) {
    if (user.nickname) {
        console.log(`Hello, ${user.nickname}`);
    } else {
        console.log(`Hello, ${user.name}`);
    }
}

// 注意：0 和 "" 也是假值，要小心处理
function printCount(count: number | null) {
    if (count) {  // 当 count === 0 时也是 false！
        console.log(count);
    }

    // 更安全的写法
    if (count !== null) {
        console.log(count);  // count 可能是 0
    }
}
```

### in 操作符

```typescript
// in 检查对象是否有某个属性

interface Cat {
    meow(): void;
}

interface Dog {
    bark(): void;
}

function speak(animal: Cat | Dog) {
    if ("meow" in animal) {
        // animal 是 Cat
        animal.meow();
    } else {
        // animal 是 Dog
        animal.bark();
    }
}
```

### instanceof 操作符

```typescript
// instanceof 检查对象是否是某个类的实例

class Bird {
    fly() { console.log("Flying"); }
}

class Fish {
    swim() { console.log("Swimming"); }
}

function move(animal: Bird | Fish) {
    if (animal instanceof Bird) {
        animal.fly();
    } else {
        animal.swim();
    }
}
```

### 自定义类型守卫

```typescript
// 使用 is 关键字定义类型谓词

interface Cat {
    type: "cat";
    meow(): void;
}

interface Dog {
    type: "dog";
    bark(): void;
}

// 类型守卫函数
function isCat(animal: Cat | Dog): animal is Cat {
    return animal.type === "cat";
}

function speak(animal: Cat | Dog) {
    if (isCat(animal)) {
        // TypeScript 知道 animal 是 Cat
        animal.meow();
    } else {
        animal.bark();
    }
}

// 另一个例子：检查是否为有效数字
function isValidNumber(value: unknown): value is number {
    return typeof value === "number" && !isNaN(value);
}

function processValue(value: unknown) {
    if (isValidNumber(value)) {
        // value 是 number
        console.log(value.toFixed(2));
    }
}
```

---

## 3. 可选链（Optional Chaining）

可选链 `?.` 用于安全地访问可能为 `null` 或 `undefined` 的属性。

### Python 对比

```python
# Python 没有可选链，需要手动检查
user = get_user()
if user and user.profile and user.profile.address:
    city = user.profile.address.city
else:
    city = None

# 或使用 getattr
city = getattr(getattr(getattr(user, 'profile', None), 'address', None), 'city', None)
```

### TypeScript 可选链

```typescript
// 属性访问
const city = user?.profile?.address?.city;
// 如果任何一个为 null/undefined，返回 undefined，不会报错

// 方法调用
const result = obj?.method?.();
// 如果 obj 或 method 为 null/undefined，不会调用

// 数组索引
const first = arr?.[0];
// 如果 arr 为 null/undefined，返回 undefined

// 实际例子
interface User {
    name: string;
    profile?: {
        avatar?: string;
        settings?: {
            theme?: string;
        };
    };
}

function getTheme(user: User): string {
    return user?.profile?.settings?.theme ?? "default";
    //                                      ↑ 空值合并运算符，提供默认值
}
```

### 项目中的可选链示例

```typescript
// 来自 open-nof1.ai/lib/ai/run.ts

await prisma.chat.create({
    data: {
        reasoning: reasoning || "<no reasoning>",
        chat: object.chat || "<no chat>",
        userPrompt,
        tradings: {
            createMany: {
                data: {
                    symbol: Symbol.BTC,
                    opeartion: object.opeartion,
                    pricing: object.buy?.pricing,    // 可选链
                    amount: object.buy?.amount,      // 可选链
                    leverage: object.buy?.leverage,  // 可选链
                },
            },
        },
    },
});

// 来自 open-nof1.ai/app/api/metrics/route.ts
const metricsData = databaseMetrics.map((item) => {
    return {
        ...item.accountInformationAndPerformance,
        createdAt: item?.createdAt || new Date().toISOString(),
        //         ↑ 可选链
    };
});
```

---

## 4. 空值合并（Nullish Coalescing）

空值合并 `??` 在左侧为 `null` 或 `undefined` 时返回右侧的值。

### || vs ?? 的区别

```typescript
// || 在左侧为假值时返回右侧
// 假值包括：false, 0, "", null, undefined, NaN

const a = 0 || "default";     // "default"（0 是假值）
const b = "" || "default";    // "default"（空字符串是假值）
const c = false || "default"; // "default"

// ?? 只在左侧为 null 或 undefined 时返回右侧
const d = 0 ?? "default";     // 0（0 不是 null/undefined）
const e = "" ?? "default";    // ""（空字符串不是 null/undefined）
const f = false ?? "default"; // false

const g = null ?? "default";      // "default"
const h = undefined ?? "default"; // "default"
```

### 使用场景

```typescript
// 设置默认值（推荐使用 ??）
const port = process.env.PORT ?? 3000;
const username = user.name ?? "匿名用户";

// 注意：当 0 或空字符串是有效值时，必须用 ??
interface Config {
    timeout?: number;  // 0 是有效的超时值
    prefix?: string;   // 空字符串是有效的前缀
}

function initConfig(config: Config) {
    // 错误：如果 timeout 是 0，会被替换为 5000
    const timeout1 = config.timeout || 5000;

    // 正确：只有 undefined 时才使用默认值
    const timeout2 = config.timeout ?? 5000;
}
```

### 与可选链结合使用

```typescript
// 常见模式：可选链 + 空值合并

interface User {
    profile?: {
        nickname?: string;
    };
}

function getDisplayName(user: User): string {
    // 如果 nickname 存在则使用，否则使用默认值
    return user?.profile?.nickname ?? "匿名用户";
}

// 项目示例
// 来自 open-nof1.ai/lib/ai/prompt.ts
const {
    currentMarketState,
    accountInformationAndPerformance,
    startTime,
    invocationCount = 0,  // 使用默认参数（等价于 ?? 0）
} = options;
```

---

## 5. 类型断言详解

### as 断言

```typescript
// 告诉编译器"我知道这个值的具体类型"

// DOM 操作
const input = document.getElementById("myInput") as HTMLInputElement;
input.value = "Hello";

// API 响应
interface User {
    name: string;
    age: number;
}

async function getUser(): Promise<User> {
    const response = await fetch("/api/user");
    const data = await response.json() as User;  // json() 返回 any
    return data;
}

// 类型转换
const someValue: unknown = "hello";
const strLength = (someValue as string).length;
```

### 非空断言（!）

```typescript
// 告诉编译器"这个值一定不是 null/undefined"

interface User {
    name?: string;
}

function printName(user: User) {
    // 使用 ! 断言 name 不是 undefined
    console.log(user.name!.toUpperCase());
    // 危险：如果 name 确实是 undefined，会运行时报错
}

// 更安全的做法
function printNameSafe(user: User) {
    if (user.name) {
        console.log(user.name.toUpperCase());
    }
}

// 或使用可选链 + 空值合并
function printNameSafer(user: User) {
    console.log(user.name?.toUpperCase() ?? "无名");
}
```

### as const 断言

```typescript
// 将值断言为最窄的字面量类型

// 普通对象
const config1 = {
    endpoint: "https://api.example.com",
    timeout: 5000
};
// 类型：{ endpoint: string; timeout: number }

// 使用 as const
const config2 = {
    endpoint: "https://api.example.com",
    timeout: 5000
} as const;
// 类型：{ readonly endpoint: "https://api.example.com"; readonly timeout: 5000 }

// 数组
const arr1 = [1, 2, 3];      // number[]
const arr2 = [1, 2, 3] as const;  // readonly [1, 2, 3]

// 常用于定义常量配置
const DIRECTIONS = ["north", "south", "east", "west"] as const;
type Direction = typeof DIRECTIONS[number];  // "north" | "south" | "east" | "west"
```

### 双重断言

```typescript
// 当两个类型完全不兼容时，需要通过 unknown 转换

const str = "hello";
// const num = str as number;  // 错误！string 不能直接断言为 number

const num = str as unknown as number;  // 可以，但危险！

// 应该只在非常确定的情况下使用
// 通常意味着类型设计有问题
```

---

## 6. 索引访问类型

```typescript
// 使用索引获取类型的属性类型

interface User {
    name: string;
    age: number;
    address: {
        city: string;
        street: string;
    };
}

// 获取单个属性类型
type UserName = User["name"];  // string
type UserAge = User["age"];    // number

// 获取嵌套属性类型
type City = User["address"]["city"];  // string

// 获取多个属性的联合类型
type UserProps = User["name" | "age"];  // string | number

// 与 keyof 结合
type AllUserTypes = User[keyof User];  // string | number | { city: string; street: string }

// 数组元素类型
const arr = [1, "two", true];
type ArrayElement = typeof arr[number];  // string | number | boolean

// 元组元素类型
type Tuple = [string, number, boolean];
type First = Tuple[0];   // string
type Second = Tuple[1];  // number
```

---

## 7. 条件类型

```typescript
// 条件类型：T extends U ? X : Y

// 基础用法
type IsString<T> = T extends string ? true : false;

type A = IsString<string>;   // true
type B = IsString<number>;   // false

// 提取类型
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

type A = UnwrapPromise<Promise<string>>;  // string
type B = UnwrapPromise<string>;           // string

// 提取数组元素类型
type ArrayElement<T> = T extends (infer E)[] ? E : never;

type A = ArrayElement<string[]>;  // string
type B = ArrayElement<number[]>;  // number

// 提取函数返回类型（自己实现 ReturnType）
type MyReturnType<T> = T extends (...args: any[]) => infer R ? R : never;
```

---

## 8. 映射类型

```typescript
// 基于现有类型创建新类型

// 将所有属性变为可选
type MyPartial<T> = {
    [K in keyof T]?: T[K];
};

// 将所有属性变为只读
type MyReadonly<T> = {
    readonly [K in keyof T]: T[K];
};

// 将所有属性类型变为 boolean
type Flags<T> = {
    [K in keyof T]: boolean;
};

interface User {
    name: string;
    age: number;
}

type UserFlags = Flags<User>;
// { name: boolean; age: boolean }

// 重命名键
type Getters<T> = {
    [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

type UserGetters = Getters<User>;
// { getName: () => string; getAge: () => number }
```

---

## 小结

本章介绍了 TypeScript 的高级类型操作：

| 特性 | 语法 | 用途 |
|------|------|------|
| Partial | `Partial<T>` | 所有属性变可选 |
| Required | `Required<T>` | 所有属性变必需 |
| Pick | `Pick<T, K>` | 选取部分属性 |
| Omit | `Omit<T, K>` | 排除部分属性 |
| Readonly | `Readonly<T>` | 所有属性变只读 |
| Record | `Record<K, V>` | 创建对象类型 |
| typeof | `typeof value` | 类型守卫 |
| in | `"prop" in obj` | 属性检查 |
| 可选链 | `obj?.prop` | 安全访问 |
| 空值合并 | `value ?? default` | 默认值 |
| as const | `value as const` | 字面量类型 |

**关键要点：**
1. 工具类型减少重复的类型定义
2. 类型守卫让 TypeScript 理解类型收窄
3. 可选链避免 null/undefined 错误
4. `??` 比 `||` 更适合设置默认值（保留 0 和 ""）

下一章我们将学习异步编程：Promise 和 async/await。
