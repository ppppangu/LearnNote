# 第零部分：JavaScript 基础（Python 开发者快速入门）

> 本章帮助 Python 开发者快速掌握 JavaScript 的核心语法差异

---

## 0. JavaScript vs Python 核心差异概览

在开始学习之前，先了解两种语言的主要差异：

| 特性 | Python | JavaScript/TypeScript |
|------|--------|----------------------|
| 代码块 | 缩进 | `{ }` 花括号 |
| 语句结尾 | 可选（换行） | 可选（分号 `;`） |
| 变量声明 | 直接赋值 | `let`/`const`/`var` |
| 常量 | 约定大写 | `const` 关键字 |
| 字符串格式化 | `f"Hello {name}"` | `` `Hello ${name}` `` |
| 数组/列表 | `list` | `Array` |
| 字典/对象 | `dict` | `Object` |
| 空值 | `None` | `null` / `undefined` |
| 布尔值 | `True` / `False` | `true` / `false` |
| 注释 | `#` | `//` 或 `/* */` |
| 逻辑运算 | `and` / `or` / `not` | `&&` / `||` / `!` |

---

## 1. 变量声明

### Python 方式
```python
# Python：直接赋值，无需声明关键字
name = "张三"
age = 25
name = "李四"  # 可以随时重新赋值

# Python 没有真正的常量，只是约定大写表示
PI = 3.14159
```

### JavaScript/TypeScript 方式

```typescript
// JavaScript/TypeScript：需要使用关键字声明变量

// 【let】可以重新赋值的变量
let name = "张三";
name = "李四";  // 可以重新赋值

// 【const】常量，声明后不能重新赋值
const PI = 3.14159;
// PI = 3.14;  // 错误！const 声明的变量不能重新赋值

// 【var】旧式声明方式，有作用域问题，现代代码中不推荐使用
var oldStyle = "不推荐";
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/app/page.tsx
const [metricsData, setMetricsData] = useState<MetricData[]>([]);
const [loading, setLoading] = useState(true);

// const 用于声明不会被重新赋值的变量
// 注意：const 对象的属性仍然可以修改，只是变量本身不能重新指向其他值
```

### let vs const 的选择原则

```typescript
// 原则：优先使用 const，只有需要重新赋值时才用 let

// 好的做法
const MAX_RETRY = 3;           // 常量用 const
const config = { timeout: 5000 };  // 对象用 const（属性可改）
let count = 0;                 // 需要改变的用 let
count = count + 1;

// 不好的做法
let MAX_RETRY = 3;             // 常量不应该用 let
var anything = "避免使用 var";
```

---

## 2. 函数定义

JavaScript 有多种定义函数的方式，这对 Python 开发者来说可能比较困惑。

### Python 方式
```python
# Python：使用 def 定义函数
def greet(name):
    return f"Hello, {name}!"

# Python：lambda 表达式（匿名函数）
double = lambda x: x * 2
```

### JavaScript 方式一：普通函数 (function)

```typescript
// 使用 function 关键字（类似 Python 的 def）
function greet(name) {
    return `Hello, ${name}!`;
}

// 调用方式相同
console.log(greet("张三"));  // "Hello, 张三!"
```

### JavaScript 方式二：箭头函数 (=>)

```typescript
// 箭头函数是 JavaScript 中最常用的函数写法
// 语法：(参数) => { 函数体 }

// 基础形式
const greet = (name) => {
    return `Hello, ${name}!`;
};

// 简写形式：如果只有一条 return 语句，可以省略 {} 和 return
const greet = (name) => `Hello, ${name}!`;

// 单参数时可以省略括号
const double = x => x * 2;

// 无参数时必须写空括号
const sayHello = () => "Hello!";

// 多参数
const add = (a, b) => a + b;
```

### Python 与 JavaScript 函数对比

```python
# Python
def add(a, b):
    return a + b

double = lambda x: x * 2

numbers = [1, 2, 3]
doubled = list(map(lambda x: x * 2, numbers))
```

```typescript
// JavaScript/TypeScript 等价写法
const add = (a, b) => {
    return a + b;
};

// 或简写
const add = (a, b) => a + b;

const double = x => x * 2;

const numbers = [1, 2, 3];
const doubled = numbers.map(x => x * 2);  // [2, 4, 6]
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/lib/trading/current-market-state.ts

// 普通函数
function calculateEMA(values: number[], period: number): number[] {
    const emaValues = EMA.calculate({ values, period });
    return emaValues;
}

// 箭头函数作为回调
const closes1m = ohlcv1m.map((candle) => Number(candle[4]));
//                          ↑ 这是一个箭头函数
//                            candle 是参数
//                            Number(candle[4]) 是返回值

// 更复杂的箭头函数
const metricsData = databaseMetrics
    .map((item) => {
        return {
            ...item.accountInformationAndPerformance,
            createdAt: item?.createdAt || new Date().toISOString(),
        };
    })
    .filter((item) => item.availableCash > 0);
```

### 回调函数概念

回调函数是传递给另一个函数作为参数的函数，在适当的时候被调用。

```python
# Python 中的回调概念
numbers = [1, 2, 3, 4, 5]

# filter 接收一个函数作为参数
evens = list(filter(lambda x: x % 2 == 0, numbers))

# sorted 接收 key 函数
sorted_list = sorted(numbers, key=lambda x: -x)
```

```typescript
// JavaScript 中回调函数非常普遍

const numbers = [1, 2, 3, 4, 5];

// filter 方法接收回调函数
const evens = numbers.filter(x => x % 2 === 0);  // [2, 4]
//                          ↑ 回调函数

// map 方法接收回调函数
const doubled = numbers.map(x => x * 2);  // [2, 4, 6, 8, 10]

// forEach 方法接收回调函数
numbers.forEach(x => {
    console.log(x);  // 打印每个数字
});

// sort 方法接收比较函数
const sorted = numbers.sort((a, b) => b - a);  // [5, 4, 3, 2, 1]
```

---

## 3. 模板字符串

### Python 方式
```python
# Python：f-string（格式化字符串）
name = "张三"
age = 25
message = f"我叫{name}，今年{age}岁"

# 多行字符串
long_text = """
这是第一行
这是第二行
"""
```

### JavaScript 方式

```typescript
// JavaScript：模板字符串（Template Literals）
// 使用反引号 ` ` 而不是引号

const name = "张三";
const age = 25;

// 插值使用 ${表达式}
const message = `我叫${name}，今年${age}岁`;

// 可以在 ${} 中写任意表达式
const info = `明年${name}就${age + 1}岁了`;

// 多行字符串，直接换行即可
const longText = `
这是第一行
这是第二行
可以直接换行，不需要特殊字符
`;

// 调用函数
const greeting = `当前时间是：${new Date().toLocaleString()}`;
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/lib/ai/prompt.ts

export const tradingPrompt = `
You are an expert cryptocurrency analyst and trader with deep knowledge...

Today is ${new Date().toDateString()}
`;

// 来自 open-nof1.ai/lib/ai/prompt.ts
export function generateUserPrompt(options: UserPromptOptions) {
    return `
It has been ${dayjs(new Date()).diff(startTime, "minute")} minutes since you started trading.

# HERE IS THE CURRENT MARKET STATE
${formatMarketState(currentMarketState)}

## HERE IS YOUR ACCOUNT INFORMATION & PERFORMANCE
${formatAccountPerformance(accountInformationAndPerformance)}
`;
}
```

### 普通字符串 vs 模板字符串

```typescript
// 普通字符串：使用单引号或双引号
const str1 = 'Hello';
const str2 = "World";

// 模板字符串：使用反引号
const str3 = `Hello ${str2}`;

// 普通字符串拼接（不推荐）
const oldWay = "我叫" + name + "，今年" + age + "岁";

// 模板字符串（推荐）
const newWay = `我叫${name}，今年${age}岁`;
```

---

## 4. 解构赋值

解构赋值是从数组或对象中提取值的简洁语法。

### Python 方式
```python
# Python：元组解包
a, b = (1, 2)
first, *rest = [1, 2, 3, 4]  # first=1, rest=[2,3,4]

# Python：字典没有直接的解构语法
person = {"name": "张三", "age": 25}
name = person["name"]
age = person["age"]
```

### JavaScript 对象解构

```typescript
// 基础对象解构
const person = { name: "张三", age: 25, city: "北京" };

// 从对象中提取属性
const { name, age } = person;
console.log(name);  // "张三"
console.log(age);   // 25

// 重命名变量
const { name: userName, age: userAge } = person;
console.log(userName);  // "张三"

// 设置默认值
const { name, country = "中国" } = person;
console.log(country);  // "中国"（person 中没有 country，使用默认值）

// 嵌套解构
const user = {
    info: {
        name: "张三",
        address: { city: "北京" }
    }
};
const { info: { name, address: { city } } } = user;
console.log(city);  // "北京"
```

### JavaScript 数组解构

```typescript
// 基础数组解构
const numbers = [1, 2, 3, 4, 5];

// 按位置提取元素
const [first, second] = numbers;
console.log(first);   // 1
console.log(second);  // 2

// 跳过某些元素
const [a, , c] = numbers;  // 跳过第二个
console.log(c);  // 3

// 剩余元素（类似 Python 的 *rest）
const [head, ...tail] = numbers;
console.log(head);  // 1
console.log(tail);  // [2, 3, 4, 5]

// 设置默认值
const [x, y, z = 0] = [1, 2];
console.log(z);  // 0

// 交换变量
let m = 1, n = 2;
[m, n] = [n, m];  // 现在 m=2, n=1
```

### 函数参数解构

```typescript
// 这在项目中非常常见！

// Python 写法
def greet(person):
    name = person["name"]
    age = person["age"]
    return f"{name} is {age}"

// JavaScript/TypeScript 写法
const greet = ({ name, age }) => {
    return `${name} is ${age}`;
};

// 调用
greet({ name: "张三", age: 25 });  // "张三 is 25"

// 带默认值的参数解构
const greet = ({ name, age = 18 }) => {
    return `${name} is ${age}`;
};
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/lib/ai/prompt.ts
export function generateUserPrompt(options: UserPromptOptions) {
    // 从 options 对象中解构出需要的属性
    const {
        currentMarketState,
        accountInformationAndPerformance,
        startTime,
        invocationCount = 0,  // 带默认值
    } = options;

    // 现在可以直接使用这些变量
    return `...${currentMarketState}...`;
}

// 来自 open-nof1.ai/app/layout.tsx
export default function RootLayout({
    children,  // 从 props 中解构 children
}: Readonly<{
    children: React.ReactNode;
}>) {
    return (
        <html lang="en">
            <body>{children}</body>
        </html>
    );
}

// 来自 open-nof1.ai/app/api/pricing/route.ts
// Promise.all 返回数组，使用数组解构
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

## 5. 展开运算符 (...)

展开运算符 `...` 用于展开数组或对象。

### Python 方式
```python
# Python：* 展开列表，** 展开字典

# 列表展开
list1 = [1, 2, 3]
list2 = [*list1, 4, 5]  # [1, 2, 3, 4, 5]

# 字典展开
dict1 = {"a": 1, "b": 2}
dict2 = {**dict1, "c": 3}  # {"a": 1, "b": 2, "c": 3}

# 函数参数展开
def add(a, b, c):
    return a + b + c

args = [1, 2, 3]
result = add(*args)

kwargs = {"a": 1, "b": 2, "c": 3}
result = add(**kwargs)
```

### JavaScript 展开运算符

```typescript
// JavaScript：... 用于展开数组和对象

// 【数组展开】
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5];  // [1, 2, 3, 4, 5]

// 合并数组
const combined = [...arr1, ...arr2];

// 复制数组（浅拷贝）
const copy = [...arr1];

// 【对象展开】
const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 };  // { a: 1, b: 2, c: 3 }

// 合并对象（后面的属性会覆盖前面的）
const merged = { ...obj1, ...obj2, b: 10 };  // b 被覆盖为 10

// 复制对象（浅拷贝）
const objCopy = { ...obj1 };

// 【函数参数展开】
const numbers = [1, 2, 3];
console.log(Math.max(...numbers));  // 3
// 等价于 Math.max(1, 2, 3)
```

### 项目中的实际例子

```typescript
// 来自 open-nof1.ai/app/api/cron/20-seconds-metrics-interval/route.ts

// 展开已有数组并添加新元素
const newMetrics = [
    ...((existMetrics?.metrics || []) as JsonValue[]),  // 展开旧数据
    {
        accountInformationAndPerformance,
        createdAt: new Date().toISOString(),
    },
] as JsonValue[];

// 来自 open-nof1.ai/app/api/metrics/route.ts
const metricsData = databaseMetrics.map((item) => {
    return {
        ...item.accountInformationAndPerformance,  // 展开对象属性
        createdAt: item?.createdAt || new Date().toISOString(),
    };
});

// 来自 open-nof1.ai/components/models-view.tsx
// 在 map 中创建新对象并添加额外属性
const completedTrades = chats.flatMap((chat) =>
    chat.tradings
        .filter((t) => t.opeartion === "Buy" || t.opeartion === "Sell")
        .map((t) => ({
            ...t,           // 展开原有交易属性
            chatId: chat.id,  // 添加新属性
            model: chat.model
        }))
);
```

---

## 6. 对象和数组方法

### 数组常用方法

```typescript
const numbers = [1, 2, 3, 4, 5];

// map：映射（类似 Python 的列表推导式或 map）
const doubled = numbers.map(x => x * 2);  // [2, 4, 6, 8, 10]
// Python: [x * 2 for x in numbers]

// filter：过滤
const evens = numbers.filter(x => x % 2 === 0);  // [2, 4]
// Python: [x for x in numbers if x % 2 == 0]

// find：找到第一个满足条件的元素
const found = numbers.find(x => x > 3);  // 4
// Python: next((x for x in numbers if x > 3), None)

// some：是否存在满足条件的元素（返回布尔值）
const hasEven = numbers.some(x => x % 2 === 0);  // true
// Python: any(x % 2 == 0 for x in numbers)

// every：是否所有元素都满足条件
const allPositive = numbers.every(x => x > 0);  // true
// Python: all(x > 0 for x in numbers)

// reduce：归约
const sum = numbers.reduce((acc, x) => acc + x, 0);  // 15
// Python: sum(numbers) 或 functools.reduce(lambda acc, x: acc + x, numbers, 0)

// forEach：遍历（无返回值）
numbers.forEach(x => console.log(x));
// Python: for x in numbers: print(x)

// includes：是否包含
const hasThree = numbers.includes(3);  // true
// Python: 3 in numbers

// indexOf：查找索引
const index = numbers.indexOf(3);  // 2
// Python: numbers.index(3)

// slice：切片
const sliced = numbers.slice(1, 4);  // [2, 3, 4]
// Python: numbers[1:4]

// concat：连接数组
const combined = numbers.concat([6, 7]);  // [1, 2, 3, 4, 5, 6, 7]
// Python: numbers + [6, 7]

// join：连接成字符串
const str = numbers.join(", ");  // "1, 2, 3, 4, 5"
// Python: ", ".join(map(str, numbers))

// flatMap：先 map 再展平
const nested = [[1], [2], [3]];
const flat = nested.flatMap(x => x);  // [1, 2, 3]
// Python: [item for sublist in nested for item in sublist]
```

### 项目中的数组方法使用

```typescript
// 来自 open-nof1.ai/lib/trading/current-market-state.ts

// 使用 map 提取价格数据
const closes1m = ohlcv1m.map((candle) => Number(candle[4]));

// 使用 slice 获取最后 10 个元素
const last10MidPrices = closes1m.slice(-10);

// 使用 reduce 计算平均值
const averageVolume4h =
    volumes4h.reduce((sum, vol) => sum + vol, 0) / volumes4h.length;

// 来自 open-nof1.ai/components/models-view.tsx

// 链式调用多个数组方法
const completedTrades = chats
    .flatMap((chat) => chat.tradings)  // 展平所有交易
    .filter((t) => t.opeartion === "Buy" || t.opeartion === "Sell")  // 过滤
    .map((t) => ({ ...t, chatId: chat.id }));  // 转换
```

---

## 7. 条件语句和循环

### 条件语句

```typescript
// if-else（与 Python 类似，但使用花括号）
const age = 18;

if (age >= 18) {
    console.log("成年");
} else if (age >= 12) {
    console.log("青少年");
} else {
    console.log("儿童");
}

// 三元运算符（类似 Python 的条件表达式）
const status = age >= 18 ? "成年" : "未成年";
// Python: status = "成年" if age >= 18 else "未成年"

// switch 语句（Python 3.10+ 有 match）
const day = "Monday";
switch (day) {
    case "Monday":
        console.log("周一");
        break;  // 必须有 break，否则会继续执行
    case "Tuesday":
        console.log("周二");
        break;
    default:
        console.log("其他");
}
```

### 循环

```typescript
// for 循环
for (let i = 0; i < 5; i++) {
    console.log(i);  // 0, 1, 2, 3, 4
}

// for...of 循环（遍历值，推荐用于数组）
const fruits = ["apple", "banana", "orange"];
for (const fruit of fruits) {
    console.log(fruit);
}
// Python: for fruit in fruits: print(fruit)

// for...in 循环（遍历键/索引，通常用于对象）
const person = { name: "张三", age: 25 };
for (const key in person) {
    console.log(`${key}: ${person[key]}`);
}
// Python: for key in person: print(f"{key}: {person[key]}")

// while 循环
let count = 0;
while (count < 5) {
    console.log(count);
    count++;
}

// 注意：现代 JavaScript 中很少用传统 for 循环
// 更常用 map、filter、forEach 等数组方法
```

---

## 8. 相等性比较

JavaScript 有两种相等比较：

```typescript
// == 宽松相等（会进行类型转换，不推荐）
console.log(1 == "1");     // true（字符串被转换为数字）
console.log(0 == false);   // true
console.log(null == undefined);  // true

// === 严格相等（不进行类型转换，推荐使用）
console.log(1 === "1");    // false
console.log(0 === false);  // false
console.log(null === undefined);  // false

// 同样，!= 和 !== 的区别
console.log(1 != "1");     // false（宽松不等）
console.log(1 !== "1");    // true（严格不等）

// 【重要】始终使用 === 和 !==
```

---

## 9. 真值和假值

JavaScript 中以下值被视为"假值"（falsy）：

```typescript
// 假值（falsy values）
false
0
-0
0n        // BigInt 零
""        // 空字符串
null
undefined
NaN

// 所有其他值都是"真值"（truthy），包括：
[]        // 空数组（注意：Python 中 [] 是假值！）
{}        // 空对象（注意：Python 中 {} 是假值！）
"0"       // 字符串 "0"
"false"   // 字符串 "false"

// 这会影响条件判断
const arr = [];
if (arr) {
    console.log("数组存在");  // 这会执行！
}

// 要检查数组是否为空，需要检查 length
if (arr.length > 0) {
    console.log("数组不为空");
}
```

---

## 10. 逻辑运算符的特殊用法

```typescript
// || 逻辑或：返回第一个真值，或最后一个值
const name = "" || "默认名";  // "默认名"
const value = null || undefined || "fallback";  // "fallback"

// && 逻辑与：返回第一个假值，或最后一个值
const result = true && "success";  // "success"
const fail = false && "success";   // false

// 常用于条件渲染（React 中）
const showMessage = true;
const element = showMessage && <div>显示的消息</div>;

// 常用于设置默认值（注意：0 和 "" 会被视为假值）
const port = process.env.PORT || 3000;
```

---

## 小结

本章介绍了 JavaScript 的核心语法，这些是理解 TypeScript 的基础：

| 概念 | Python | JavaScript |
|------|--------|------------|
| 变量声明 | `x = 1` | `const x = 1` 或 `let x = 1` |
| 函数定义 | `def f(x): return x` | `const f = (x) => x` |
| 字符串模板 | `f"{x}"` | `` `${x}` `` |
| 对象解构 | 无直接语法 | `const {a, b} = obj` |
| 数组解构 | `a, b = [1, 2]` | `const [a, b] = [1, 2]` |
| 展开运算符 | `*list` / `**dict` | `...array` / `...object` |
| 相等比较 | `==` | `===`（推荐） |

下一章我们将学习 TypeScript 的类型系统，这是 TypeScript 相对于 JavaScript 最核心的特性。
