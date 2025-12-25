# 第六部分：React + TypeScript

> 本章介绍 React 组件、Hooks 和 JSX/TSX 语法

---

## 1. React 基础概念

### 什么是 React？

React 是一个用于构建用户界面的 JavaScript 库。核心概念：
- **组件**：可复用的 UI 单元
- **状态**：组件的数据
- **属性**：父组件传递给子组件的数据
- **渲染**：将组件转换为 DOM

### 组件的类型

```typescript
// 【函数组件】现代 React 的标准写法
function Welcome(props: { name: string }) {
    return <h1>Hello, {props.name}</h1>;
}

// 【类组件】旧式写法，现在很少用
class Welcome extends React.Component<{ name: string }> {
    render() {
        return <h1>Hello, {this.props.name}</h1>;
    }
}
```

---

## 2. JSX/TSX 语法

JSX（JavaScript XML）是 JavaScript 的语法扩展，让你可以在 JavaScript 中写类似 HTML 的代码。

### 基础语法

```tsx
// TSX 文件（TypeScript + JSX）

// 基础元素
const element = <h1>Hello, World!</h1>;

// 带属性
const link = <a href="https://example.com">Click me</a>;

// 嵌套元素
const container = (
    <div>
        <h1>Title</h1>
        <p>Paragraph</p>
    </div>
);

// 自闭合标签
const image = <img src="photo.jpg" alt="Photo" />;
const input = <input type="text" />;
```

### 在 JSX 中使用 JavaScript

```tsx
// 使用 {} 插入 JavaScript 表达式

const name = "张三";
const element = <h1>Hello, {name}!</h1>;

// 调用函数
const element = <h1>Hello, {formatName(user)}!</h1>;

// 计算表达式
const element = <p>1 + 1 = {1 + 1}</p>;

// 条件表达式
const element = <p>{isLoggedIn ? "欢迎回来" : "请登录"}</p>;

// 数组映射
const items = ["苹果", "香蕉", "橙子"];
const list = (
    <ul>
        {items.map((item, index) => (
            <li key={index}>{item}</li>
        ))}
    </ul>
);
```

### 条件渲染

```tsx
// 方式一：三元运算符
function Greeting({ isLoggedIn }: { isLoggedIn: boolean }) {
    return (
        <div>
            {isLoggedIn ? <UserGreeting /> : <GuestGreeting />}
        </div>
    );
}

// 方式二：&& 短路运算
function Notification({ count }: { count: number }) {
    return (
        <div>
            {count > 0 && <span>你有 {count} 条新消息</span>}
        </div>
    );
}

// 方式三：提前返回
function UserProfile({ user }: { user: User | null }) {
    if (!user) {
        return <p>Loading...</p>;
    }

    return <h1>{user.name}</h1>;
}
```

### 列表渲染

```tsx
// 使用 map 渲染列表

interface Item {
    id: string;
    name: string;
}

function ItemList({ items }: { items: Item[] }) {
    return (
        <ul>
            {items.map((item) => (
                <li key={item.id}>  {/* key 是必需的 */}
                    {item.name}
                </li>
            ))}
        </ul>
    );
}

// 项目示例：来自 open-nof1.ai/app/page.tsx
{pricing ? (
    <>
        <CryptoCard symbol="BTC" name="Bitcoin" price={...} />
        <CryptoCard symbol="ETH" name="Ethereum" price={...} />
        <CryptoCard symbol="SOL" name="Solana" price={...} />
        <CryptoCard symbol="BNB" name="BNB" price={...} />
        <CryptoCard symbol="DOGE" name="Dogecoin" price={...} />
    </>
) : (
    // 加载骨架屏
    Array.from({ length: 5 }).map((_, i) => (
        <Card key={i} className="p-4 animate-pulse">
            <div className="h-20 bg-muted rounded"></div>
        </Card>
    ))
)}
```

### 样式处理

```tsx
// 内联样式（使用对象）
const style = { color: "red", fontSize: "16px" };
const element = <p style={style}>红色文字</p>;

// 直接写
const element = <p style={{ color: "red" }}>红色文字</p>;

// CSS 类名
const element = <p className="text-red-500">红色文字</p>;

// 动态类名（使用 clsx/cn）
import { cn } from "@/lib/utils";

const element = (
    <p className={cn(
        "base-class",
        isActive && "active",
        isDisabled && "disabled"
    )}>
        动态类名
    </p>
);
```

---

## 3. 函数组件与 Props

### 基础组件

```tsx
// 简单组件
function Welcome() {
    return <h1>Hello!</h1>;
}

// 带 Props 的组件
interface GreetingProps {
    name: string;
}

function Greeting({ name }: GreetingProps) {
    return <h1>Hello, {name}!</h1>;
}

// 使用
<Greeting name="张三" />
```

### Props 类型定义

```tsx
// 使用 interface 定义 Props
interface ButtonProps {
    label: string;           // 必需属性
    onClick: () => void;     // 函数属性
    disabled?: boolean;      // 可选属性
    variant?: "primary" | "secondary";  // 字面量类型
}

function Button({ label, onClick, disabled = false, variant = "primary" }: ButtonProps) {
    return (
        <button
            onClick={onClick}
            disabled={disabled}
            className={variant === "primary" ? "btn-primary" : "btn-secondary"}
        >
            {label}
        </button>
    );
}

// 使用
<Button label="点击我" onClick={() => alert("clicked")} />
<Button label="禁用" onClick={() => {}} disabled />
```

### children 属性

```tsx
// children 是特殊的 Props，表示组件的子元素

interface CardProps {
    title: string;
    children: React.ReactNode;  // 可以是任何 React 可渲染的内容
}

function Card({ title, children }: CardProps) {
    return (
        <div className="card">
            <h2>{title}</h2>
            <div className="card-body">
                {children}
            </div>
        </div>
    );
}

// 使用
<Card title="我的卡片">
    <p>这是卡片内容</p>
    <button>点击</button>
</Card>
```

### 项目中的组件示例

```tsx
// 来自 open-nof1.ai/components/crypto-card.tsx

interface CryptoCardProps {
    symbol: string;
    name: string;
    price: string;
    marketState: MarketState;
}

export function CryptoCard({ symbol, name, price, marketState }: CryptoCardProps) {
    const iconMap = {
        BTC: SiBitcoin,
        ETH: SiEthereum,
        // ...
    };

    const Icon = iconMap[symbol as keyof typeof iconMap];

    return (
        <Card className="p-4">
            <div className="flex items-center gap-3">
                <Icon className="h-8 w-8" />
                <div>
                    <p className="font-medium">{name}</p>
                    <p className="text-2xl font-bold">{price}</p>
                </div>
            </div>
        </Card>
    );
}

// 来自 open-nof1.ai/components/metrics-chart.tsx

interface MetricsChartProps {
    metricsData: MetricData[];
    loading: boolean;
    lastUpdate: string;
    totalCount?: number;
}

export function MetricsChart({
    metricsData,
    loading,
    totalCount,
}: MetricsChartProps) {
    if (loading) {
        return (
            <Card>
                <CardContent className="flex items-center justify-center h-[500px]">
                    <div className="text-lg">Loading metrics...</div>
                </CardContent>
            </Card>
        );
    }

    return (
        <Card className="h-full">
            {/* 图表内容 */}
        </Card>
    );
}
```

---

## 4. React Hooks

Hooks 是 React 16.8 引入的特性，让函数组件可以使用状态和其他 React 特性。

### useState - 状态管理

```tsx
import { useState } from "react";

function Counter() {
    // useState 返回 [当前值, 设置函数]
    const [count, setCount] = useState(0);  // 初始值为 0

    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>+1</button>
            <button onClick={() => setCount(prev => prev - 1)}>-1</button>
        </div>
    );
}

// 带类型的 useState
const [name, setName] = useState<string>("");
const [user, setUser] = useState<User | null>(null);
const [items, setItems] = useState<Item[]>([]);
```

### 项目中的 useState 示例

```tsx
// 来自 open-nof1.ai/app/page.tsx

export default function Home() {
    // 多个状态
    const [metricsData, setMetricsData] = useState<MetricData[]>([]);
    const [totalCount, setTotalCount] = useState<number>(0);
    const [pricing, setPricing] = useState<CryptoPricing | null>(null);
    const [loading, setLoading] = useState(true);
    const [lastUpdate, setLastUpdate] = useState<string>("");

    // ...
}

// 来自 open-nof1.ai/components/models-view.tsx

export function ModelsView() {
    const [activeTab, setActiveTab] = useState<TabType>("model-chat");
    const [chats, setChats] = useState<Chat[]>([]);
    const [loading, setLoading] = useState(true);
    const [expandedChatId, setExpandedChatId] = useState<string | null>(null);

    // 切换 Tab
    <button onClick={() => setActiveTab("model-chat")}>
        MODEL CHAT
    </button>
}

// 来自 open-nof1.ai/components/animated-number.tsx

export function AnimatedNumber({ value }: AnimatedNumberProps) {
    const [displayValue, setDisplayValue] = useState(value);
    const [isAnimating, setIsAnimating] = useState(false);
    // ...
}
```

### useEffect - 副作用

```tsx
import { useEffect } from "react";

function DataFetcher() {
    const [data, setData] = useState(null);

    // 组件挂载时执行
    useEffect(() => {
        fetch("/api/data")
            .then(res => res.json())
            .then(setData);
    }, []);  // 空依赖数组 = 只在挂载时执行

    // 依赖变化时执行
    useEffect(() => {
        console.log("用户变化了:", user);
    }, [user]);  // user 变化时重新执行

    // 每次渲染都执行
    useEffect(() => {
        console.log("组件渲染了");
    });  // 没有依赖数组 = 每次渲染都执行

    // 清理函数
    useEffect(() => {
        const interval = setInterval(() => {
            console.log("tick");
        }, 1000);

        // 返回清理函数，在组件卸载或依赖变化前执行
        return () => {
            clearInterval(interval);
        };
    }, []);

    return <div>{data}</div>;
}
```

### 项目中的 useEffect 示例

```tsx
// 来自 open-nof1.ai/app/page.tsx

useEffect(() => {
    // 初始获取数据
    fetchMetrics();
    fetchPricing();

    // 设置定时轮询
    const metricsInterval = setInterval(fetchMetrics, 10000);
    const pricingInterval = setInterval(fetchPricing, 10000);

    // 清理函数：组件卸载时清除定时器
    return () => {
        clearInterval(metricsInterval);
        clearInterval(pricingInterval);
    };
}, [fetchMetrics, fetchPricing]);

// 来自 open-nof1.ai/components/animated-number.tsx

useEffect(() => {
    if (previousValue.current !== value) {
        setIsAnimating(true);

        const timer = setTimeout(() => {
            setDisplayValue(value);
            setIsAnimating(false);
        }, 100);

        previousValue.current = value;

        return () => clearTimeout(timer);  // 清理定时器
    }
}, [value]);  // value 变化时执行

// 来自 open-nof1.ai/components/models-view.tsx

useEffect(() => {
    fetchChats();
    const interval = setInterval(fetchChats, 30000);  // 每 30 秒刷新
    return () => clearInterval(interval);
}, [fetchChats]);
```

### useCallback - 函数缓存

```tsx
import { useCallback } from "react";

function SearchBox() {
    const [query, setQuery] = useState("");

    // 不使用 useCallback：每次渲染都创建新函数
    const handleSearch = () => {
        fetch(`/api/search?q=${query}`);
    };

    // 使用 useCallback：只在依赖变化时创建新函数
    const handleSearch = useCallback(() => {
        fetch(`/api/search?q=${query}`);
    }, [query]);  // 只有 query 变化时才重新创建

    return <button onClick={handleSearch}>搜索</button>;
}
```

### 项目中的 useCallback 示例

```tsx
// 来自 open-nof1.ai/app/page.tsx

// fetchMetrics 被缓存，只在依赖变化时重新创建
const fetchMetrics = useCallback(async () => {
    try {
        const response = await fetch("/api/metrics");
        if (!response.ok) return;

        const data: MetricsResponse = await response.json();
        if (data.success && data.data) {
            setMetricsData(data.data.metrics || []);
            setTotalCount(data.data.totalCount || 0);
            setLastUpdate(new Date().toLocaleTimeString());
            setLoading(false);
        }
    } catch (err) {
        console.error("Error fetching metrics:", err);
        setLoading(false);
    }
}, []);  // 空依赖 = 函数永不变化

const fetchPricing = useCallback(async () => {
    try {
        const response = await fetch("/api/pricing");
        if (!response.ok) return;

        const data: PricingResponse = await response.json();
        if (data.success && data.data) {
            setPricing(data.data.pricing);
        }
    } catch (err) {
        console.error("Error fetching pricing:", err);
    }
}, []);

// 来自 open-nof1.ai/components/models-view.tsx

const fetchChats = useCallback(async () => {
    try {
        const response = await fetch("/api/model/chat");
        if (!response.ok) return;

        const data = await response.json();
        setChats(data.data || []);
        setLoading(false);
    } catch (err) {
        console.error("Error fetching chats:", err);
        setLoading(false);
    }
}, []);
```

### useRef - 引用

```tsx
import { useRef } from "react";

function TextInput() {
    // 引用 DOM 元素
    const inputRef = useRef<HTMLInputElement>(null);

    const focusInput = () => {
        inputRef.current?.focus();
    };

    return (
        <>
            <input ref={inputRef} type="text" />
            <button onClick={focusInput}>聚焦输入框</button>
        </>
    );
}

// 保存可变值（不会触发重新渲染）
function Timer() {
    const countRef = useRef(0);

    // 修改 ref.current 不会触发重新渲染
    countRef.current++;
}
```

### 项目中的 useRef 示例

```tsx
// 来自 open-nof1.ai/components/animated-number.tsx

export function AnimatedNumber({ value }: AnimatedNumberProps) {
    const [displayValue, setDisplayValue] = useState(value);
    const [isAnimating, setIsAnimating] = useState(false);
    const previousValue = useRef(value);  // 保存上一个值

    useEffect(() => {
        if (previousValue.current !== value) {  // 比较
            setIsAnimating(true);

            const timer = setTimeout(() => {
                setDisplayValue(value);
                setIsAnimating(false);
            }, 100);

            previousValue.current = value;  // 更新引用

            return () => clearTimeout(timer);
        }
    }, [value]);

    // ...
}
```

---

## 5. "use client" 指令

Next.js 13+ 引入了服务器组件（RSC）。默认情况下，组件在服务器端渲染。

```tsx
// 【服务器组件】（默认）
// - 在服务器上渲染
// - 不能使用 useState、useEffect 等 Hooks
// - 不能使用浏览器 API
// - 可以直接访问数据库

export default function ServerComponent() {
    // 可以直接 async
    const data = await prisma.user.findMany();
    return <div>{data.map(u => u.name)}</div>;
}

// 【客户端组件】
// - 在浏览器中渲染
// - 可以使用所有 Hooks
// - 可以使用浏览器 API
// - 需要在文件顶部加 "use client"

"use client";  // 必须在文件最顶部

import { useState, useEffect } from "react";

export default function ClientComponent() {
    const [count, setCount] = useState(0);

    useEffect(() => {
        document.title = `Count: ${count}`;
    }, [count]);

    return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

### 项目中的客户端组件

```tsx
// 来自 open-nof1.ai/app/page.tsx

"use client";  // 第一行声明

import { useEffect, useState, useCallback } from "react";

export default function Home() {
    // 可以使用所有 Hooks
    const [metricsData, setMetricsData] = useState<MetricData[]>([]);
    // ...
}

// 来自 open-nof1.ai/components/metrics-chart.tsx

"use client";

export function MetricsChart({ metricsData, loading }: MetricsChartProps) {
    // 可以使用浏览器 API 和 Hooks
    // ...
}

// 来自 open-nof1.ai/components/models-view.tsx

"use client";

import { useEffect, useState, useCallback } from "react";

export function ModelsView() {
    // ...
}
```

---

## 6. 常用类型

### React.ReactNode

```tsx
// ReactNode 表示任何可以渲染的内容

interface CardProps {
    children: React.ReactNode;
}

// ReactNode 包括：
// - 字符串、数字
// - JSX 元素
// - 数组
// - null、undefined、boolean（会被忽略）
// - Fragment

// 项目示例：来自 open-nof1.ai/app/layout.tsx
export default function RootLayout({
    children,
}: Readonly<{
    children: React.ReactNode;
}>) {
    return (
        <html lang="en">
            <body>{children}</body>
        </html>
    );
}
```

### React.ComponentProps

```tsx
// 获取组件的 Props 类型

// 获取原生元素的 props
type ButtonProps = React.ComponentProps<"button">;
type InputProps = React.ComponentProps<"input">;
type DivProps = React.ComponentProps<"div">;

// 使用
function CustomButton(props: React.ComponentProps<"button">) {
    return <button {...props} className="custom-btn" />;
}

// 项目示例：来自 open-nof1.ai/components/ui/card.tsx

function Card({ className, ...props }: React.ComponentProps<"div">) {
    return (
        <div
            className={cn(
                "bg-card text-card-foreground rounded-xl border shadow-sm",
                className
            )}
            {...props}
        />
    );
}

function CardHeader({ className, ...props }: React.ComponentProps<"div">) {
    return (
        <div
            className={cn("grid gap-2 px-6", className)}
            {...props}
        />
    );
}
```

### SVGProps

```tsx
// SVG 组件的 Props 类型

import { SVGProps } from "react";

// 项目示例：来自 open-nof1.ai/lib/icons.tsx

export function ArcticonsDeepseek(props: SVGProps<SVGSVGElement>) {
    return (
        <svg
            xmlns="http://www.w3.org/2000/svg"
            width="1em"
            height="1em"
            viewBox="0 0 48 48"
            {...props}  // 展开所有 SVG 属性
        >
            {/* SVG 内容 */}
        </svg>
    );
}
```

---

## 7. 事件处理

```tsx
// 事件处理函数类型

// 点击事件
function handleClick(event: React.MouseEvent<HTMLButtonElement>) {
    console.log(event.currentTarget);  // 按钮元素
}

// 输入事件
function handleChange(event: React.ChangeEvent<HTMLInputElement>) {
    console.log(event.target.value);  // 输入值
}

// 表单提交
function handleSubmit(event: React.FormEvent<HTMLFormElement>) {
    event.preventDefault();  // 阻止默认提交
}

// 键盘事件
function handleKeyDown(event: React.KeyboardEvent<HTMLInputElement>) {
    if (event.key === "Enter") {
        // 处理回车
    }
}

// 使用
<button onClick={handleClick}>点击</button>
<input onChange={handleChange} onKeyDown={handleKeyDown} />
<form onSubmit={handleSubmit}>...</form>
```

---

## 小结

本章介绍了 React + TypeScript 的核心概念：

| 概念 | 语法 | 用途 |
|------|------|------|
| 函数组件 | `function Component() {}` | 定义 UI 组件 |
| Props | `interface Props {}` | 组件参数类型 |
| JSX | `<div>{expression}</div>` | UI 模板语法 |
| useState | `const [s, setS] = useState()` | 状态管理 |
| useEffect | `useEffect(() => {}, [])` | 副作用处理 |
| useCallback | `useCallback(() => {}, [])` | 函数缓存 |
| useRef | `useRef()` | 引用和可变值 |
| "use client" | 文件顶部声明 | 客户端组件 |

**关键要点：**
1. 函数组件 + Hooks 是现代 React 的标准
2. Props 使用 interface 定义类型
3. useEffect 的依赖数组很重要
4. useCallback 防止不必要的重新渲染
5. Next.js 中需要区分服务器和客户端组件

下一章我们将学习框架特定语法（Next.js、Prisma、Zod）。
