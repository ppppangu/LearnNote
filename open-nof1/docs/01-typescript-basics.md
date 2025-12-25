# 第一部分：TypeScript 类型系统基础

> TypeScript = JavaScript + 静态类型
> 本章介绍 TypeScript 最核心的特性：类型系统

---

## 什么是 TypeScript？

TypeScript 是 JavaScript 的超集，它添加了**静态类型检查**。

```
TypeScript 代码 (.ts/.tsx)
        ↓
    编译器检查类型
        ↓
    输出 JavaScript 代码 (.js)
        ↓
    在浏览器/Node.js 中运行
```

**为什么使用 TypeScript？**
1. **编译时发现错误**：在代码运行前就能发现类型错误
2. **更好的 IDE 支持**：自动补全、重构、跳转定义
3. **代码即文档**：类型注解让代码更易理解
4. **大型项目必备**：减少运行时错误，提高可维护性

---

## 1. 基础类型

### Python 与 TypeScript 类型对比

| Python 类型 | TypeScript 类型 | 示例 |
|------------|----------------|------|
| `int`, `float` | `number` | `let n: number = 42` |
| `str` | `string` | `let s: string = "hello"` |
| `bool` | `boolean` | `let b: boolean = true` |
| `None` | `null`, `undefined` | `let x: null = null` |
| `list` | `Array<T>` 或 `T[]` | `let arr: number[] = [1,2,3]` |
| `dict` | `object` 或接口 | `let obj: {name: string}` |
| `Any` | `any` | `let x: any = anything` |

### 基础类型示例

```typescript
// 【number】所有数字（整数和浮点数）
let age: number = 25;
let price: number = 99.99;
let hex: number = 0xff;      // 十六进制
let binary: number = 0b1010; // 二进制

// 【string】字符串
let name: string = "张三";
let greeting: string = `Hello, ${name}`;

// 【boolean】布尔值
let isActive: boolean = true;
let hasError: boolean = false;

// 【null 和 undefined】
let empty: null = null;
let notDefined: undefined = undefined;

// 【数组】两种写法
let numbers: number[] = [1, 2, 3];           // 推荐写法
let strings: Array<string> = ["a", "b"];     // 泛型写法

// 【元组】固定长度和类型的数组
let tuple: [string, number] = ["张三", 25];  // 类似 Python 的元组
let [n, a] = tuple;  // 解构：n="张三", a=25

// 【any】任意类型（尽量避免使用）
let anything: any = 42;
anything = "string";  // 不会报错
anything = true;      // 不会报错

// 【unknown】更安全的 any
let unknown: unknown = 42;
// let num: number = unknown;  // 错误！需要先检查类型

// 【void】无返回值
function log(msg: string): void {
    console.log(msg);
    // 没有 return 语句，或 return;
}

// 【never】永不返回（抛出异常或无限循环）
function throwError(msg: string): never {
    throw new Error(msg);
}
```

---

## 2. 类型注解语法

类型注解使用冒号 `:` 后跟类型名。

### Python 类型提示 vs TypeScript 类型注解

```python
# Python 类型提示（运行时不强制）
def greet(name: str) -> str:
    return f"Hello, {name}"

age: int = 25
numbers: list[int] = [1, 2, 3]
```

```typescript
// TypeScript 类型注解（编译时强制检查）
function greet(name: string): string {
    return `Hello, ${name}`;
}

let age: number = 25;
let numbers: number[] = [1, 2, 3];
```

### 变量类型注解

```typescript
// 语法：let 变量名: 类型 = 值;

let name: string = "张三";
let age: number = 25;
let isStudent: boolean = true;

// 也可以先声明后赋值
let score: number;
score = 100;

// 复杂类型
let person: { name: string; age: number } = {
    name: "张三",
    age: 25
};
```

### 函数类型注解

```typescript
// 参数和返回值的类型注解
// 语法：function 函数名(参数: 类型): 返回类型 { }

// 普通函数
function add(a: number, b: number): number {
    return a + b;
}

// 箭头函数
const multiply = (a: number, b: number): number => {
    return a * b;
};

// 简写（单表达式箭头函数）
const divide = (a: number, b: number): number => a / b;

// 无返回值
function logMessage(msg: string): void {
    console.log(msg);
}

// 可选参数（使用 ?）
function greet(name: string, greeting?: string): string {
    return `${greeting || "Hello"}, ${name}`;
}
greet("张三");           // "Hello, 张三"
greet("张三", "你好");   // "你好, 张三"

// 默认参数
function greet(name: string, greeting: string = "Hello"): string {
    return `${greeting}, ${name}`;
}

// 剩余参数
function sum(...numbers: number[]): number {
    return numbers.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3, 4);  // 10
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/lib/trading/current-market-state.ts

// 函数参数和返回值都有类型注解
function calculateEMA(values: number[], period: number): number[] {
    const emaValues = EMA.calculate({ values, period });
    return emaValues;
}

// async 函数的返回类型是 Promise<T>
export async function getCurrentMarketState(
    symbol: string
): Promise<MarketState> {
    // ...
}

// 来自 open-nof1.ai/lib/utils.ts
import { clsx, type ClassValue } from "clsx";

export function cn(...inputs: ClassValue[]) {
    return twMerge(clsx(inputs));
}
```

---

## 3. 类型推断

TypeScript 可以自动推断类型，不需要总是写类型注解。

```typescript
// 类型推断：TypeScript 自动推断变量类型

// 根据赋值推断
let name = "张三";      // 推断为 string
let age = 25;          // 推断为 number
let isActive = true;   // 推断为 boolean

// 根据数组内容推断
let numbers = [1, 2, 3];           // 推断为 number[]
let mixed = [1, "hello", true];    // 推断为 (string | number | boolean)[]

// 根据函数返回值推断
function add(a: number, b: number) {  // 返回类型推断为 number
    return a + b;
}

// 根据上下文推断（contextual typing）
const numbers = [1, 2, 3];
numbers.map(n => n * 2);  // n 被推断为 number
//          ↑ 不需要写 n: number
```

### 何时需要显式类型注解？

```typescript
// 1. 函数参数（必须）
function greet(name: string) {  // 参数必须注解
    return `Hello, ${name}`;
}

// 2. 无法推断或推断不准确时
let data;  // 推断为 any，应该显式注解
let data: string[];  // 更好

// 3. 空数组
const arr = [];        // 推断为 never[]，没用
const arr: string[] = [];  // 正确

// 4. API 响应等外部数据
const response = await fetch("/api");
const data: UserData = await response.json();  // json() 返回 any

// 5. 复杂对象
const config: ServerConfig = {
    port: 3000,
    host: "localhost"
};
```

---

## 4. 对象类型

### 内联对象类型

```typescript
// 直接在变量声明中定义对象结构

let person: { name: string; age: number } = {
    name: "张三",
    age: 25
};

// 可选属性
let user: { name: string; email?: string } = {
    name: "张三"
    // email 是可选的，可以不提供
};

// 只读属性
let point: { readonly x: number; readonly y: number } = {
    x: 10,
    y: 20
};
// point.x = 30;  // 错误！readonly 属性不能修改

// 函数参数中的对象类型
function printPerson(person: { name: string; age: number }): void {
    console.log(`${person.name} is ${person.age}`);
}
```

### 对比 Python

```python
# Python 使用 TypedDict 或 dataclass

from typing import TypedDict

class Person(TypedDict):
    name: str
    age: int

person: Person = {"name": "张三", "age": 25}

# 或使用 dataclass
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int

person = Person(name="张三", age=25)
```

```typescript
// TypeScript 使用 interface 或 type（下一章详细介绍）

interface Person {
    name: string;
    age: number;
}

const person: Person = { name: "张三", age: 25 };
```

---

## 5. 联合类型

联合类型表示一个值可以是多种类型之一。

```typescript
// 使用 | 表示联合类型

// 变量可以是 string 或 number
let id: string | number;
id = "abc123";  // OK
id = 123;       // OK
// id = true;   // 错误！

// 函数参数可以是多种类型
function printId(id: string | number): void {
    console.log(`ID: ${id}`);
}
printId("abc");  // OK
printId(123);    // OK

// 处理联合类型时需要类型收窄
function printId(id: string | number): void {
    if (typeof id === "string") {
        // 在这个分支中，TypeScript 知道 id 是 string
        console.log(id.toUpperCase());
    } else {
        // 在这个分支中，id 是 number
        console.log(id.toFixed(2));
    }
}

// null 和 undefined 的处理
let name: string | null = null;
name = "张三";

// 可选属性实际上是 T | undefined
interface User {
    name: string;
    email?: string;  // 等价于 email: string | undefined
}
```

### 对比 Python

```python
# Python 使用 Union 或 | (Python 3.10+)
from typing import Union

def print_id(id: Union[str, int]) -> None:
    print(f"ID: {id}")

# Python 3.10+ 可以用 |
def print_id(id: str | int) -> None:
    print(f"ID: {id}")
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/lib/types/metrics.ts

export interface MetricData {
    positions: Position[];
    sharpeRatio: number | null;      // 可能是数字或 null
    availableCash: number;
    currentTotalReturn: number | null;  // 可能是数字或 null
    // ...
}

// 来自 open-nof1.ai/components/animated-number.tsx

interface AnimatedNumberProps {
    value: string | number;  // 可以接受字符串或数字
    className?: string;      // 可选属性
}
```

---

## 6. 字面量类型

字面量类型是一种更精确的类型，只允许特定的值。

```typescript
// 字符串字面量类型
let direction: "north" | "south" | "east" | "west";
direction = "north";  // OK
// direction = "up";  // 错误！只能是上面四个值

// 数字字面量类型
let dice: 1 | 2 | 3 | 4 | 5 | 6;
dice = 3;  // OK
// dice = 7;  // 错误！

// 布尔字面量
let yes: true = true;
// yes = false;  // 错误！

// 结合使用
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";

function request(url: string, method: HttpMethod): void {
    // ...
}

request("/api/users", "GET");    // OK
// request("/api/users", "get"); // 错误！必须大写
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/components/models-view.tsx

// Tab 类型只能是这三个值之一
type TabType = "completed-trades" | "model-chat" | "positions";

const [activeTab, setActiveTab] = useState<TabType>("model-chat");

// 交易操作只能是三种
interface Trading {
    opeartion: "Buy" | "Sell" | "Hold";  // 字面量联合类型
    // ...
}

// 渲染操作图标
const renderOperationIcon = (operation: string) => {
    switch (operation) {
        case "Buy":
            return <TrendingUp className="text-green-500" />;
        case "Sell":
            return <TrendingDown className="text-red-500" />;
        case "Hold":
            return <Minus className="text-yellow-500" />;
        default:
            return null;
    }
};
```

---

## 7. 类型别名 (type)

类型别名用于给类型起一个名字，便于复用。

```typescript
// 使用 type 关键字定义类型别名

// 基础类型别名
type ID = string | number;
type Name = string;

let userId: ID = "abc123";
let userName: Name = "张三";

// 对象类型别名
type Point = {
    x: number;
    y: number;
};

const origin: Point = { x: 0, y: 0 };

// 函数类型别名
type GreetFunction = (name: string) => string;

const greet: GreetFunction = (name) => `Hello, ${name}`;

// 联合类型别名
type Status = "pending" | "approved" | "rejected";
type Result = { success: true; data: any } | { success: false; error: string };

// 复杂类型别名
type ApiResponse<T> = {
    data: T;
    success: boolean;
    error?: string;
};
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/components/models-view.tsx

type TabType = "completed-trades" | "model-chat" | "positions";

// 来自 open-nof1.ai/lib/utils.ts

import { clsx, type ClassValue } from "clsx";
// ClassValue 是从 clsx 库导入的类型别名

export function cn(...inputs: ClassValue[]) {
    return twMerge(clsx(inputs));
}
```

---

## 8. null 和 undefined 处理

TypeScript 区分 `null` 和 `undefined`，并提供多种处理方式。

```typescript
// 两者的区别
// undefined: 变量已声明但未赋值
// null: 显式设置为空值

let a: undefined = undefined;  // 未定义
let b: null = null;            // 空值

// 在实际使用中，通常用联合类型表示可能为空
let name: string | null = null;
let age: number | undefined = undefined;

// 【非空断言 !】告诉编译器这个值不是 null/undefined
function getLength(str: string | null): number {
    // str! 断言 str 一定不是 null
    return str!.length;  // 危险！如果 str 确实是 null 会报错
}

// 【可选链 ?.】安全访问可能为空的属性（下一章详细介绍）
const length = str?.length;  // 如果 str 是 null/undefined，返回 undefined

// 【空值合并 ??】提供默认值（下一章详细介绍）
const name = userName ?? "默认名";  // 如果 userName 是 null/undefined，使用默认值
```

### strictNullChecks

在 `tsconfig.json` 中可以启用严格空值检查：

```json
{
    "compilerOptions": {
        "strict": true,  // 包含 strictNullChecks
        // 或单独设置
        "strictNullChecks": true
    }
}
```

项目的 tsconfig.json 通常启用了 strict 模式：

```typescript
// 来自 open-nof1.ai/tsconfig.json
{
    "compilerOptions": {
        "strict": true,  // 启用所有严格检查
        // ...
    }
}
```

---

## 9. 类型断言

类型断言告诉编译器"我知道这个值的类型"。

```typescript
// 【as 语法】（推荐）
const input = document.getElementById("myInput") as HTMLInputElement;
input.value = "Hello";

// 【尖括号语法】（在 JSX 中不能用）
const input = <HTMLInputElement>document.getElementById("myInput");

// 常见使用场景
// 1. DOM 操作
const canvas = document.getElementById("canvas") as HTMLCanvasElement;
const ctx = canvas.getContext("2d");

// 2. API 响应
const response = await fetch("/api/user");
const data = await response.json() as User;

// 3. 类型收窄
function handleEvent(event: MouseEvent | KeyboardEvent) {
    if ("key" in event) {
        // event 是 KeyboardEvent
        console.log((event as KeyboardEvent).key);
    }
}

// 【双重断言】用于跨越不兼容的类型（谨慎使用）
const str = "hello" as unknown as number;  // 危险！
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/app/api/metrics/route.ts

// 处理数据库返回的 JSON 数据
const databaseMetrics = metrics.metrics as unknown as {
    createdAt: string;
    accountInformationAndPerformance: MetricData[];
}[];

// 来自 open-nof1.ai/components/crypto-card.tsx

// 类型断言用于索引类型
const Icon = iconMap[symbol as keyof typeof iconMap];
const iconColor = colorMap[symbol as keyof typeof colorMap];

// 来自 open-nof1.ai/components/metrics-chart.tsx

// Tooltip 回调中的类型断言
const data = payload[0].payload as MetricData;
```

---

## 10. 数组类型详解

```typescript
// 【基础数组类型】
let numbers: number[] = [1, 2, 3];
let strings: string[] = ["a", "b", "c"];

// 【泛型数组语法】
let numbers: Array<number> = [1, 2, 3];

// 【只读数组】
const readonlyArr: readonly number[] = [1, 2, 3];
// readonlyArr.push(4);  // 错误！readonly 数组不能修改

// 或使用 ReadonlyArray
const arr: ReadonlyArray<number> = [1, 2, 3];

// 【元组类型】固定长度和类型
let tuple: [string, number] = ["张三", 25];
let [name, age] = tuple;

// 元组可以有可选元素
let optionalTuple: [string, number?] = ["张三"];

// 元组可以有剩余元素
let restTuple: [string, ...number[]] = ["sum", 1, 2, 3, 4];

// 【对象数组】
interface User {
    name: string;
    age: number;
}
let users: User[] = [
    { name: "张三", age: 25 },
    { name: "李四", age: 30 }
];

// 【联合类型数组】
let mixed: (string | number)[] = [1, "two", 3, "four"];
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/lib/types/metrics.ts

import { Position } from "ccxt";

export interface MetricData {
    positions: Position[];  // Position 类型的数组
    // ...
}

// 来自 open-nof1.ai/lib/trading/current-market-state.ts

export interface MarketState {
    // 数字数组
    intraday: {
        mid_prices: number[];
        ema_20: number[];
        macd: number[];
        rsi_7: number[];
        rsi_14: number[];
    };
}

// 来自 open-nof1.ai/app/api/pricing/route.ts

// Promise.all 返回结果数组并解构
const [btcPricing, ethPricing, solPricing, dogePricing, bnbPricing] =
    await Promise.all([
        getCurrentMarketState("BTC/USDT"),
        getCurrentMarketState("ETH/USDT"),
        getCurrentMarketState("SOL/USDT"),
        getCurrentMarketState("DOGE/USDT"),
        getCurrentMarketState("BNB/USDT"),
    ]);
```

---

## 小结

本章介绍了 TypeScript 类型系统的基础：

| 概念 | 语法 | 示例 |
|------|------|------|
| 类型注解 | `: type` | `let x: number = 1` |
| 基础类型 | `number`, `string`, `boolean` | `let n: number` |
| 数组类型 | `T[]` 或 `Array<T>` | `let arr: number[]` |
| 联合类型 | `T \| U` | `let id: string \| number` |
| 字面量类型 | 具体的值 | `type Dir = "n" \| "s"` |
| 类型别名 | `type Name = ...` | `type ID = string` |
| 类型断言 | `as Type` | `value as string` |
| 可选 | `?` | `name?: string` |
| 只读 | `readonly` | `readonly x: number` |

**关键要点：**
1. TypeScript 在编译时检查类型，JavaScript 运行时没有类型
2. 优先使用类型推断，必要时才写类型注解
3. 避免使用 `any`，使用 `unknown` 更安全
4. 联合类型需要类型收窄才能安全使用

下一章我们将学习接口（interface）、泛型等更高级的类型特性。
