# 第二部分：TypeScript 核心特性

> 本章介绍 interface、type、泛型、枚举等核心特性

---

## 1. 接口（Interface）

接口是 TypeScript 中定义对象结构的主要方式，类似于 Python 的 `TypedDict` 或 `Protocol`。

### 基础接口定义

```typescript
// 使用 interface 关键字定义接口

interface Person {
    name: string;
    age: number;
}

// 使用接口作为类型
const person: Person = {
    name: "张三",
    age: 25
};

// 错误示例
const invalid: Person = {
    name: "李四"
    // 错误！缺少 age 属性
};

const invalid2: Person = {
    name: "王五",
    age: 30,
    email: "test@test.com"
    // 错误！多余的 email 属性
};
```

### Python 对比

```python
# Python 使用 TypedDict
from typing import TypedDict

class Person(TypedDict):
    name: str
    age: int

person: Person = {"name": "张三", "age": 25}

# 或使用 Protocol（结构化类型）
from typing import Protocol

class PersonProtocol(Protocol):
    name: str
    age: int

# 或使用 dataclass
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int
```

### 可选属性

```typescript
interface User {
    name: string;
    age: number;
    email?: string;  // 可选属性，可以不提供
    phone?: string;  // 可选属性
}

// 只提供必需属性
const user1: User = {
    name: "张三",
    age: 25
};

// 提供所有属性
const user2: User = {
    name: "李四",
    age: 30,
    email: "lisi@example.com",
    phone: "13800138000"
};
```

### 只读属性

```typescript
interface Point {
    readonly x: number;  // 只读，创建后不能修改
    readonly y: number;
}

const point: Point = { x: 10, y: 20 };
// point.x = 30;  // 错误！不能修改只读属性

// 只读数组
interface Data {
    readonly items: readonly number[];  // 数组本身和内容都不可变
}
```

### 函数类型接口

```typescript
// 接口可以描述函数类型

interface GreetFunction {
    (name: string): string;  // 参数和返回值
}

const greet: GreetFunction = (name) => {
    return `Hello, ${name}`;
};

// 带有属性的函数接口
interface Counter {
    (start: number): number;  // 函数签名
    interval: number;         // 属性
    reset(): void;            // 方法
}
```

### 接口继承

```typescript
// 使用 extends 继承接口

interface Animal {
    name: string;
}

interface Dog extends Animal {
    breed: string;
}

const dog: Dog = {
    name: "旺财",
    breed: "金毛"
};

// 多重继承
interface Movable {
    speed: number;
}

interface Flyable {
    altitude: number;
}

interface Bird extends Animal, Movable, Flyable {
    wingspan: number;
}

const eagle: Bird = {
    name: "老鹰",
    speed: 100,
    altitude: 1000,
    wingspan: 2
};
```

### 项目中的接口示例

```typescript
// 来自 open-nof1.ai/lib/trading/current-market-state.ts

export interface MarketState {
    // 当前指标
    current_price: number;
    current_ema20: number;
    current_macd: number;
    current_rsi: number;

    // 嵌套对象
    open_interest: {
        latest: number;
        average: number;
    };

    funding_rate: number;

    // 日内数据（数组）
    intraday: {
        mid_prices: number[];
        ema_20: number[];
        macd: number[];
        rsi_7: number[];
        rsi_14: number[];
    };

    // 长期数据
    longer_term: {
        ema_20: number;
        ema_50: number;
        atr_3: number;
        atr_14: number;
        current_volume: number;
        average_volume: number;
        macd: number[];
        rsi_14: number[];
    };
}

// 来自 open-nof1.ai/lib/trading/account-information-and-performance.ts

export interface AccountInformationAndPerformance {
    currentPositionsValue: number;
    contractValue: number;
    totalCashValue: number;
    availableCash: number;
    currentTotalReturn: number;
    positions: Position[];  // 使用其他类型
    sharpeRatio: number;
}

// 来自 open-nof1.ai/components/models-view.tsx

interface Trading {
    id: string;
    symbol: string;
    opeartion: "Buy" | "Sell" | "Hold";  // 字面量联合类型
    leverage?: number | null;             // 可选且可能为 null
    amount?: number | null;
    pricing?: number | null;
    stopLoss?: number | null;
    takeProfit?: number | null;
    createdAt: string;
}

interface Chat {
    id: string;
    model: string;
    chat: string;
    reasoning: string;
    userPrompt: string;
    tradings: Trading[];  // 嵌套接口数组
    createdAt: string;
    updatedAt: string;
}
```

---

## 2. type vs interface

`type` 和 `interface` 都可以定义对象类型，但有一些区别：

### 相同点

```typescript
// 两者都可以定义对象结构
interface PersonInterface {
    name: string;
    age: number;
}

type PersonType = {
    name: string;
    age: number;
};

// 使用方式相同
const p1: PersonInterface = { name: "张三", age: 25 };
const p2: PersonType = { name: "李四", age: 30 };
```

### 不同点

```typescript
// 【1. type 可以定义联合类型和交叉类型】
type ID = string | number;           // 联合类型
type Combined = TypeA & TypeB;       // 交叉类型

// interface 不能直接定义联合类型

// 【2. type 可以使用 typeof 获取变量类型】
const person = { name: "张三", age: 25 };
type PersonFromValue = typeof person;  // { name: string; age: number }

// 【3. interface 可以声明合并（同名自动合并）】
interface Window {
    title: string;
}
interface Window {
    size: number;
}
// 两个 Window 接口会自动合并为 { title: string; size: number }

// type 不能声明合并
type User = { name: string };
// type User = { age: number };  // 错误！重复定义

// 【4. interface 继承用 extends，type 用 &】
interface Animal {
    name: string;
}
interface Dog extends Animal {
    breed: string;
}

type AnimalType = {
    name: string;
};
type DogType = AnimalType & {
    breed: string;
};
```

### 使用建议

```typescript
// 推荐使用 interface 的场景：
// - 定义对象结构
// - 需要被继承或实现
// - 需要声明合并（如扩展第三方库类型）

interface User {
    name: string;
    age: number;
}

// 推荐使用 type 的场景：
// - 定义联合类型
// - 定义元组类型
// - 定义函数类型
// - 类型运算

type Status = "pending" | "success" | "error";
type Tuple = [string, number];
type Handler = (event: Event) => void;
type PartialUser = Partial<User>;
```

---

## 3. 泛型（Generics）

泛型允许你编写可重用的组件，同时保持类型安全。

### Python 对比

```python
# Python 使用 TypeVar
from typing import TypeVar, List

T = TypeVar('T')

def first(items: List[T]) -> T:
    return items[0]

# 调用
result = first([1, 2, 3])  # result 类型是 int
```

### TypeScript 泛型基础

```typescript
// 泛型函数：使用 <T> 声明类型参数

// 基础泛型函数
function identity<T>(value: T): T {
    return value;
}

// 调用时可以显式指定类型
const num = identity<number>(42);      // num: number
const str = identity<string>("hello"); // str: string

// 也可以让 TypeScript 自动推断
const num2 = identity(42);      // num2: number（自动推断）
const str2 = identity("hello"); // str2: string（自动推断）

// 箭头函数泛型
const identity2 = <T>(value: T): T => value;

// 多个类型参数
function pair<K, V>(key: K, value: V): [K, V] {
    return [key, value];
}

const p = pair("name", 25);  // p: [string, number]
```

### 泛型接口

```typescript
// 接口也可以使用泛型

interface Box<T> {
    value: T;
}

const numberBox: Box<number> = { value: 42 };
const stringBox: Box<string> = { value: "hello" };

// API 响应的通用接口
interface ApiResponse<T> {
    data: T;
    success: boolean;
    error?: string;
}

interface User {
    id: string;
    name: string;
}

// 使用时指定具体类型
const userResponse: ApiResponse<User> = {
    data: { id: "1", name: "张三" },
    success: true
};

const listResponse: ApiResponse<User[]> = {
    data: [
        { id: "1", name: "张三" },
        { id: "2", name: "李四" }
    ],
    success: true
};
```

### 泛型约束

```typescript
// 使用 extends 约束泛型类型

// 约束 T 必须有 length 属性
function logLength<T extends { length: number }>(item: T): void {
    console.log(item.length);
}

logLength("hello");     // OK，字符串有 length
logLength([1, 2, 3]);   // OK，数组有 length
// logLength(123);      // 错误！数字没有 length

// 约束 T 必须是某个接口的子类型
interface HasId {
    id: string;
}

function findById<T extends HasId>(items: T[], id: string): T | undefined {
    return items.find(item => item.id === id);
}

// keyof 约束
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}

const person = { name: "张三", age: 25 };
const name = getProperty(person, "name");  // string
const age = getProperty(person, "age");    // number
// getProperty(person, "email");  // 错误！"email" 不是 person 的键
```

### 项目中的泛型示例

```typescript
// 来自 open-nof1.ai/app/api/metrics/route.ts

// 泛型函数：均匀采样数据
function uniformSample<T>(data: T[], sampleSize: number): T[] {
    if (data.length <= sampleSize) {
        return data;
    }

    const result: T[] = [];
    const step = (data.length - 1) / (sampleSize - 1);

    for (let i = 0; i < sampleSize; i++) {
        const index = Math.round(i * step);
        result.push(data[index]);
    }

    return result;
}

// 使用：自动推断类型
const sampledMetrics = uniformSample(metricsData, MAX_DATA_POINTS);
// sampledMetrics 类型被推断为 MetricData[]

// 来自 open-nof1.ai/app/api/cron/20-seconds-metrics-interval/route.ts

function uniformSampleWithBoundaries<T>(data: T[], maxSize: number): T[] {
    if (data.length <= maxSize) {
        return data;
    }

    const result: T[] = [];
    const step = (data.length - 1) / (maxSize - 1);

    for (let i = 0; i < maxSize; i++) {
        const index = Math.round(i * step);
        result.push(data[i]);
    }

    return result;
}
```

### 常用泛型模式

```typescript
// 【1. 数组相关】
function first<T>(arr: T[]): T | undefined {
    return arr[0];
}

function last<T>(arr: T[]): T | undefined {
    return arr[arr.length - 1];
}

// 【2. Promise 相关】
async function fetchData<T>(url: string): Promise<T> {
    const response = await fetch(url);
    return response.json() as T;
}

// 使用
interface User { name: string }
const user = await fetchData<User>("/api/user");

// 【3. 映射对象】
function mapObject<T, U>(
    obj: Record<string, T>,
    fn: (value: T) => U
): Record<string, U> {
    const result: Record<string, U> = {};
    for (const key in obj) {
        result[key] = fn(obj[key]);
    }
    return result;
}
```

---

## 4. 枚举（Enum）

枚举是一组命名常量的集合，在 TypeScript 中有数字枚举和字符串枚举。

### 数字枚举

```typescript
// 数字枚举（默认从 0 开始）
enum Direction {
    Up,     // 0
    Down,   // 1
    Left,   // 2
    Right   // 3
}

let dir: Direction = Direction.Up;
console.log(dir);  // 0

// 可以指定起始值
enum StatusCode {
    OK = 200,
    Created = 201,
    BadRequest = 400,
    NotFound = 404
}

console.log(StatusCode.OK);        // 200
console.log(StatusCode.NotFound);  // 404
```

### 字符串枚举

```typescript
// 字符串枚举（每个成员必须有初始值）
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT"
}

let dir: Direction = Direction.Up;
console.log(dir);  // "UP"

// 字符串枚举在调试时更友好
enum LogLevel {
    Error = "ERROR",
    Warning = "WARNING",
    Info = "INFO",
    Debug = "DEBUG"
}

function log(level: LogLevel, message: string) {
    console.log(`[${level}] ${message}`);
}

log(LogLevel.Error, "Something went wrong");
// 输出: [ERROR] Something went wrong
```

### Python 对比

```python
# Python 使用 Enum
from enum import Enum

class Direction(Enum):
    UP = "UP"
    DOWN = "DOWN"
    LEFT = "LEFT"
    RIGHT = "RIGHT"

direction = Direction.UP
print(direction.value)  # "UP"
```

### const 枚举

```typescript
// const 枚举在编译时会被内联，不生成额外代码
const enum Direction {
    Up = "UP",
    Down = "DOWN"
}

let dir = Direction.Up;
// 编译后：let dir = "UP";（没有 Direction 对象）
```

### Prisma 枚举集成

```typescript
// 来自 open-nof1.ai/prisma/schema.prisma
// Prisma 在 schema 文件中定义枚举

enum Opeartion {
    Buy
    Sell
    Hold
}

enum Symbol {
    BTC
    ETH
    BNB
    SOL
    DOGE
}

enum ModelType {
    Deepseek
    DeepseekThinking
    Qwen
    Doubao
}

// 来自 open-nof1.ai/lib/ai/run.ts
// 在 TypeScript 中使用 Prisma 生成的枚举

import { Opeartion, Symbol } from "@prisma/client";

// z.nativeEnum 用于 Zod 验证
const schema = z.object({
    opeartion: z.nativeEnum(Opeartion),  // Buy, Sell, 或 Hold
});

// 条件判断中使用枚举
if (object.opeartion === Opeartion.Buy) {
    // 买入逻辑
} else if (object.opeartion === Opeartion.Sell) {
    // 卖出逻辑
} else if (object.opeartion === Opeartion.Hold) {
    // 持有逻辑
}

// 来自 open-nof1.ai/app/api/metrics/route.ts

import { ModelType } from "@prisma/client";

const metrics = await prisma.metrics.findFirst({
    where: {
        model: ModelType.Deepseek,  // 使用枚举值
    },
});
```

### 联合类型 vs 枚举

```typescript
// 有时联合类型比枚举更简洁

// 使用枚举
enum Status {
    Pending = "pending",
    Approved = "approved",
    Rejected = "rejected"
}
let status: Status = Status.Pending;

// 使用联合类型（更简洁）
type StatusType = "pending" | "approved" | "rejected";
let status2: StatusType = "pending";

// 推荐：
// - 需要双向映射（值→名称）时用枚举
// - 简单常量集合时用联合类型
```

---

## 5. 交叉类型（Intersection Types）

交叉类型使用 `&` 将多个类型合并为一个。

```typescript
// 合并多个类型的所有属性

interface HasName {
    name: string;
}

interface HasAge {
    age: number;
}

// 交叉类型：同时具有 name 和 age
type Person = HasName & HasAge;

const person: Person = {
    name: "张三",
    age: 25
    // 必须同时有 name 和 age
};

// 与继承的区别
// extends 创建新接口，& 创建类型别名
interface Employee extends HasName, HasAge {
    department: string;
}

type Employee2 = HasName & HasAge & {
    department: string;
};

// 两种方式效果相同
```

### 实用示例

```typescript
// 合并 API 响应和元数据
interface ApiData {
    users: User[];
}

interface Metadata {
    timestamp: string;
    version: string;
}

type ApiResponse = ApiData & Metadata;

const response: ApiResponse = {
    users: [{ id: "1", name: "张三" }],
    timestamp: "2024-01-01",
    version: "1.0"
};

// 扩展函数参数
interface BaseOptions {
    timeout: number;
}

interface RetryOptions {
    retries: number;
    retryDelay: number;
}

type FetchOptions = BaseOptions & RetryOptions;

function fetchWithRetry(url: string, options: FetchOptions) {
    // options 有 timeout, retries, retryDelay
}
```

---

## 6. 索引签名

索引签名允许你定义对象可以有任意数量的属性。

```typescript
// 字符串索引签名
interface StringMap {
    [key: string]: string;  // 任意字符串键，值为字符串
}

const map: StringMap = {
    name: "张三",
    city: "北京",
    // 可以添加任意数量的属性
};

// 数字索引签名
interface NumberArray {
    [index: number]: string;  // 数字索引，值为字符串
}

const arr: NumberArray = ["a", "b", "c"];

// 混合使用
interface MixedMap {
    [key: string]: number | string;  // 值可以是 number 或 string
    length: number;                   // 具体属性
    name: string;                     // 具体属性
}
```

### 项目中的索引签名

```typescript
// 来自 open-nof1.ai/components/crypto-card.tsx

// 图标映射对象
const iconMap = {
    BTC: SiBitcoin,
    ETH: SiEthereum,
    SOL: TbCurrencySolana,
    BNB: SiBinance,
    DOGE: SiDogecoin,
};

// 使用 keyof typeof 获取对象键的联合类型
const Icon = iconMap[symbol as keyof typeof iconMap];
// keyof typeof iconMap = "BTC" | "ETH" | "SOL" | "BNB" | "DOGE"
```

---

## 7. keyof 和 typeof

### keyof 操作符

```typescript
// keyof 获取类型的所有键的联合类型

interface Person {
    name: string;
    age: number;
    email: string;
}

type PersonKeys = keyof Person;  // "name" | "age" | "email"

// 常用于约束函数参数
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
    return obj[key];
}

const person: Person = { name: "张三", age: 25, email: "test@test.com" };
const name = getProperty(person, "name");   // string
const age = getProperty(person, "age");     // number
// getProperty(person, "invalid");  // 错误！"invalid" 不是有效的键
```

### typeof 操作符

```typescript
// typeof 获取值的类型

const person = {
    name: "张三",
    age: 25
};

// 从值推断类型
type PersonType = typeof person;  // { name: string; age: number }

// 常用于配置对象
const config = {
    apiUrl: "https://api.example.com",
    timeout: 5000,
    retries: 3
} as const;  // as const 使类型更精确

type Config = typeof config;
// { readonly apiUrl: "https://api.example.com"; readonly timeout: 5000; readonly retries: 3 }

// 获取函数类型
function add(a: number, b: number): number {
    return a + b;
}

type AddFunction = typeof add;  // (a: number, b: number) => number
```

---

## 8. satisfies 操作符（TypeScript 4.9+）

`satisfies` 确保表达式满足某个类型，同时保留其具体类型。

```typescript
// 问题：使用类型注解会丢失具体信息
interface ChartConfig {
    [key: string]: {
        label: string;
        color: string;
    };
}

// 使用类型注解
const config1: ChartConfig = {
    desktop: { label: "Desktop", color: "#0066FF" },
    mobile: { label: "Mobile", color: "#00FF00" }
};
// config1.desktop 的类型是 { label: string; color: string }
// 不知道具体有哪些键

// 使用 satisfies
const config2 = {
    desktop: { label: "Desktop", color: "#0066FF" },
    mobile: { label: "Mobile", color: "#00FF00" }
} satisfies ChartConfig;
// config2.desktop 保留了具体类型
// 知道有 desktop 和 mobile 两个键
```

### 项目中的 satisfies 示例

```typescript
// 来自 open-nof1.ai/components/metrics-chart.tsx

import { ChartConfig } from "@/components/ui/chart";

const chartConfig = {
    totalCashValue: {
        label: "Cash Value",
        color: "#0066FF",
    },
} satisfies ChartConfig;
// chartConfig 既满足 ChartConfig 接口
// 又保留了具体的键 "totalCashValue"

// 来自 open-nof1.ai/components/chart.tsx

const chartConfig = {
    views: {
        label: "Page Views",
    },
    desktop: {
        label: "Desktop",
        color: "var(--chart-1)",
    },
    mobile: {
        label: "Mobile",
        color: "var(--chart-2)",
    },
} satisfies ChartConfig;
```

---

## 小结

本章介绍了 TypeScript 的核心类型特性：

| 特性 | 语法 | 用途 |
|------|------|------|
| 接口 | `interface Name { }` | 定义对象结构 |
| 类型别名 | `type Name = ...` | 类型命名和组合 |
| 泛型 | `<T>` | 可重用的类型安全代码 |
| 枚举 | `enum Name { }` | 命名常量集合 |
| 交叉类型 | `A & B` | 合并多个类型 |
| 联合类型 | `A \| B` | 多选一类型 |
| keyof | `keyof T` | 获取类型的键 |
| typeof | `typeof value` | 获取值的类型 |
| satisfies | `expr satisfies Type` | 类型检查但保留具体类型 |

**关键要点：**
1. 优先使用 `interface` 定义对象，`type` 定义联合类型
2. 泛型使代码可重用且类型安全
3. Prisma 枚举在数据库和 TypeScript 之间保持类型一致
4. `satisfies` 是类型检查和类型推断的平衡点

下一章我们将学习高级类型操作：工具类型、类型守卫、可选链等。
