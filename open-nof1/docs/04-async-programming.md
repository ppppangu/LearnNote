# 第四部分：异步编程

> 本章介绍 Promise、async/await 等异步编程模式

---

## 1. 什么是异步编程？

### 同步 vs 异步

```typescript
// 【同步代码】按顺序执行，每行代码等待上一行完成
console.log("1");
console.log("2");
console.log("3");
// 输出：1, 2, 3

// 【异步代码】不阻塞后续代码执行
console.log("1");
setTimeout(() => {
    console.log("2");  // 1秒后执行
}, 1000);
console.log("3");
// 输出：1, 3, 2（注意顺序！）
```

### Python 对比

```python
# Python 同步代码
import time

print("1")
time.sleep(1)  # 阻塞1秒
print("2")
print("3")
# 输出：1, （等待1秒）, 2, 3

# Python 异步代码（需要 asyncio）
import asyncio

async def main():
    print("1")
    await asyncio.sleep(1)
    print("2")

asyncio.run(main())
```

### 为什么需要异步？

在 Web 开发中，很多操作需要时间：
- 网络请求（API 调用）
- 文件读写
- 数据库查询
- 定时器

异步编程让程序在等待这些操作时可以继续处理其他任务。

---

## 2. Promise 基础

Promise 是 JavaScript 处理异步操作的核心对象。

### Promise 的三种状态

```
Pending（进行中）→ Fulfilled（已完成）→ 返回值
                 ↘ Rejected（已拒绝）→ 错误
```

### 创建 Promise

```typescript
// 创建一个 Promise
const promise = new Promise<string>((resolve, reject) => {
    // 异步操作
    setTimeout(() => {
        const success = true;
        if (success) {
            resolve("成功！");  // 完成，返回值
        } else {
            reject(new Error("失败！"));  // 拒绝，返回错误
        }
    }, 1000);
});

// 使用 Promise
promise
    .then(result => {
        console.log(result);  // "成功！"
    })
    .catch(error => {
        console.error(error);  // 处理错误
    });
```

### .then() 和 .catch()

```typescript
// .then() 处理成功结果
// .catch() 处理错误

fetch("/api/user")
    .then(response => {
        return response.json();  // 返回另一个 Promise
    })
    .then(data => {
        console.log(data);  // 处理 JSON 数据
    })
    .catch(error => {
        console.error("请求失败:", error);
    });

// 链式调用
fetch("/api/users")
    .then(response => response.json())
    .then(users => users.filter(u => u.active))
    .then(activeUsers => {
        console.log(activeUsers);
    })
    .catch(error => {
        console.error(error);
    });
```

### Promise 类型注解

```typescript
// Promise<T> 表示最终会返回类型 T 的值

// 返回 Promise<string>
function fetchName(): Promise<string> {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve("张三");
        }, 1000);
    });
}

// 返回 Promise<User>
interface User {
    id: string;
    name: string;
}

function fetchUser(id: string): Promise<User> {
    return fetch(`/api/users/${id}`)
        .then(response => response.json());
}
```

---

## 3. async/await

`async/await` 是 Promise 的语法糖，让异步代码看起来像同步代码。

### 基础语法

```typescript
// async 函数自动返回 Promise

// 普通函数
function syncFunction(): string {
    return "hello";
}

// async 函数
async function asyncFunction(): Promise<string> {
    return "hello";  // 自动包装成 Promise<string>
}

// await 等待 Promise 完成
async function main() {
    const result = await asyncFunction();  // 等待 Promise 完成
    console.log(result);  // "hello"
}
```

### Python 对比

```python
# Python async/await
import asyncio

async def fetch_data():
    await asyncio.sleep(1)
    return "data"

async def main():
    result = await fetch_data()
    print(result)

asyncio.run(main())
```

```typescript
// TypeScript async/await
async function fetchData(): Promise<string> {
    await new Promise(resolve => setTimeout(resolve, 1000));
    return "data";
}

async function main() {
    const result = await fetchData();
    console.log(result);
}

main();
```

### 项目中的 async/await 示例

```typescript
// 来自 open-nof1.ai/lib/trading/current-market-state.ts

export async function getCurrentMarketState(
    symbol: string
): Promise<MarketState> {
    try {
        const normalizedSymbol = symbol.includes("/") ? symbol : `${symbol}/USDT`;

        // await 等待 API 调用完成
        const ohlcv1m = await binance.fetchOHLCV(
            normalizedSymbol,
            "1m",
            undefined,
            100
        );

        const ohlcv4h = await binance.fetchOHLCV(
            normalizedSymbol,
            "4h",
            undefined,
            100
        );

        // 处理数据...
        const closes1m = ohlcv1m.map((candle) => Number(candle[4]));

        return {
            current_price,
            current_ema20,
            // ...
        };
    } catch (error) {
        console.error("Error fetching market state:", error);
        throw error;
    }
}

// 来自 open-nof1.ai/lib/trading/account-information-and-performance.ts

export async function getAccountInformationAndPerformance(
    initialCapital: number
): Promise<AccountInformationAndPerformance> {
    // await 等待获取持仓信息
    const positions = await binance.fetchPositions(["BTC/USDT"]);

    // await 等待获取余额
    const currentCashValue = await binance.fetchBalance({ type: "future" });

    const totalCashValue = currentCashValue.USDT.total || 0;
    const availableCash = currentCashValue.USDT.free || 0;

    return {
        currentPositionsValue,
        contractValue,
        totalCashValue,
        availableCash,
        currentTotalReturn,
        positions,
        sharpeRatio,
    };
}

// 来自 open-nof1.ai/lib/ai/run.ts

export async function run(initialCapital: number) {
    // 多个 await，顺序执行
    const currentMarketState = await getCurrentMarketState("BTC/USDT");
    const accountInformationAndPerformance =
        await getAccountInformationAndPerformance(initialCapital);
    const invocationCount = await prisma.chat.count();

    // 生成 AI 响应
    const { object, reasoning } = await generateObject({
        model: deepseekR1,
        system: tradingPrompt,
        prompt: userPrompt,
        // ...
    });

    // 保存到数据库
    await prisma.chat.create({
        data: {
            reasoning: reasoning || "<no reasoning>",
            chat: object.chat || "<no chat>",
            // ...
        },
    });
}
```

---

## 4. Promise.all - 并行执行

当多个异步操作相互独立时，可以并行执行以提高性能。

### 顺序执行 vs 并行执行

```typescript
// 【顺序执行】每个请求等待上一个完成
async function sequential() {
    const user = await fetchUser();      // 等待 1 秒
    const posts = await fetchPosts();    // 再等待 1 秒
    const comments = await fetchComments();  // 再等待 1 秒
    // 总共 3 秒
}

// 【并行执行】所有请求同时发出
async function parallel() {
    const [user, posts, comments] = await Promise.all([
        fetchUser(),      // 同时开始
        fetchPosts(),     // 同时开始
        fetchComments(),  // 同时开始
    ]);
    // 总共约 1 秒（取决于最慢的请求）
}
```

### 项目中的 Promise.all 示例

```typescript
// 来自 open-nof1.ai/app/api/pricing/route.ts

export const GET = async () => {
    try {
        // 并行获取 5 种加密货币的市场状态
        const [btcPricing, ethPricing, solPricing, dogePricing, bnbPricing] =
            await Promise.all([
                getCurrentMarketState("BTC/USDT"),
                getCurrentMarketState("ETH/USDT"),
                getCurrentMarketState("SOL/USDT"),
                getCurrentMarketState("DOGE/USDT"),
                getCurrentMarketState("BNB/USDT"),
            ]);

        // 5 个请求同时执行，而不是顺序等待
        return NextResponse.json({
            data: {
                pricing: {
                    btc: btcPricing,
                    eth: ethPricing,
                    sol: solPricing,
                    doge: dogePricing,
                    bnb: bnbPricing,
                },
            },
            success: true,
        });
    } catch (error) {
        console.error("Error fetching pricing:", error);
        return NextResponse.json(
            { error: "Failed to fetch pricing data", success: false },
            { status: 500 }
        );
    }
};
```

### Promise.all 的类型

```typescript
// Promise.all 保持类型信息

async function example() {
    const results = await Promise.all([
        fetchUser(),      // Promise<User>
        fetchPosts(),     // Promise<Post[]>
        fetchCount(),     // Promise<number>
    ]);
    // results 类型是 [User, Post[], number]

    // 使用解构获取每个结果
    const [user, posts, count] = results;
    // user: User, posts: Post[], count: number
}
```

### Promise.allSettled - 不中断的并行执行

```typescript
// Promise.all：任何一个失败就全部失败
// Promise.allSettled：等待所有完成，不管成功失败

const results = await Promise.allSettled([
    fetch("/api/a"),
    fetch("/api/b"),  // 即使这个失败
    fetch("/api/c"),  // 其他的也会继续
]);

results.forEach(result => {
    if (result.status === "fulfilled") {
        console.log("成功:", result.value);
    } else {
        console.log("失败:", result.reason);
    }
});
```

---

## 5. 错误处理

### try/catch

```typescript
// 使用 try/catch 处理 async/await 中的错误

async function fetchData() {
    try {
        const response = await fetch("/api/data");

        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();
        return data;
    } catch (error) {
        console.error("获取数据失败:", error);
        throw error;  // 可以重新抛出
    }
}

// 调用时也可以捕获
async function main() {
    try {
        const data = await fetchData();
        console.log(data);
    } catch (error) {
        console.error("操作失败:", error);
    }
}
```

### 项目中的错误处理示例

```typescript
// 来自 open-nof1.ai/lib/trading/current-market-state.ts

export async function getCurrentMarketState(
    symbol: string
): Promise<MarketState> {
    try {
        // 主要逻辑
        const normalizedSymbol = symbol.includes("/") ? symbol : `${symbol}/USDT`;
        const ohlcv1m = await binance.fetchOHLCV(normalizedSymbol, "1m", undefined, 100);
        // ...

        // 可选功能的错误处理
        try {
            const perpSymbol = normalizedSymbol.replace("/", "");
            const openInterest = await binance.fetchOpenInterest(perpSymbol);

            if (openInterest && typeof openInterest.openInterestAmount === "number") {
                openInterestData.latest = openInterest.openInterestAmount;
            }
        } catch (error) {
            // 这个错误不影响主流程，只是警告
            console.warn("Could not fetch open interest or funding rate:", error);
        }

        return marketState;
    } catch (error) {
        // 主要错误，抛出
        console.error("Error fetching market state:", error);
        throw error;
    }
}

// 来自 open-nof1.ai/app/page.tsx

const fetchMetrics = useCallback(async () => {
    try {
        const response = await fetch("/api/metrics");
        if (!response.ok) return;  // 静默失败

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
}, []);
```

### finally

```typescript
// finally 无论成功失败都会执行

async function fetchWithLoading() {
    setLoading(true);  // 开始加载

    try {
        const data = await fetch("/api/data");
        return await data.json();
    } catch (error) {
        console.error(error);
        throw error;
    } finally {
        setLoading(false);  // 无论成功失败都停止加载
    }
}
```

---

## 6. 异步函数的返回类型

```typescript
// async 函数总是返回 Promise

// 返回具体类型
async function getString(): Promise<string> {
    return "hello";
}

// 返回 void（不关心返回值）
async function logData(): Promise<void> {
    const data = await fetchData();
    console.log(data);
    // 没有 return
}

// 返回复杂类型
interface User {
    id: string;
    name: string;
}

async function getUser(id: string): Promise<User> {
    const response = await fetch(`/api/users/${id}`);
    return response.json();
}

// 可能返回 null
async function findUser(id: string): Promise<User | null> {
    const response = await fetch(`/api/users/${id}`);
    if (response.status === 404) {
        return null;
    }
    return response.json();
}
```

---

## 7. 常见异步模式

### 延迟执行

```typescript
// 创建延迟函数
function delay(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
}

// 使用
async function main() {
    console.log("开始");
    await delay(1000);  // 等待1秒
    console.log("1秒后");
}
```

### 重试机制

```typescript
async function fetchWithRetry<T>(
    fn: () => Promise<T>,
    retries: number = 3,
    delayMs: number = 1000
): Promise<T> {
    for (let i = 0; i < retries; i++) {
        try {
            return await fn();
        } catch (error) {
            if (i === retries - 1) throw error;
            console.log(`重试 ${i + 1}/${retries}...`);
            await delay(delayMs);
        }
    }
    throw new Error("不应该到达这里");
}

// 使用
const data = await fetchWithRetry(() => fetch("/api/data").then(r => r.json()));
```

### 超时控制

```typescript
function withTimeout<T>(
    promise: Promise<T>,
    timeoutMs: number
): Promise<T> {
    return Promise.race([
        promise,
        new Promise<never>((_, reject) =>
            setTimeout(() => reject(new Error("超时")), timeoutMs)
        ),
    ]);
}

// 使用
try {
    const data = await withTimeout(fetch("/api/slow"), 5000);
} catch (error) {
    console.error("请求超时或失败");
}
```

### 并发控制

```typescript
// 限制同时执行的 Promise 数量
async function parallelLimit<T>(
    tasks: (() => Promise<T>)[],
    limit: number
): Promise<T[]> {
    const results: T[] = [];
    const executing: Promise<void>[] = [];

    for (const task of tasks) {
        const p = task().then(result => {
            results.push(result);
        });

        executing.push(p);

        if (executing.length >= limit) {
            await Promise.race(executing);
            executing.splice(
                executing.findIndex(e => e === p),
                1
            );
        }
    }

    await Promise.all(executing);
    return results;
}
```

---

## 8. 定时器和 Cron

### 项目中的定时任务

```typescript
// 来自 open-nof1.ai/cron.ts

import cron from "node-cron";
import jwt from "jsonwebtoken";

// 每 10 秒执行一次指标采集
cron.schedule("*/10 * * * * *", async () => {
    const token = jwt.sign(
        { sub: "cron-token" },
        process.env.CRON_SECRET_KEY || ""
    );

    await fetch(
        `${process.env.NEXT_PUBLIC_URL}/api/cron/20-seconds-metrics-interval?token=${token}`,
        { method: "GET" }
    );

    console.log("20 seconds metrics interval executed");
});

// 每 3 分钟执行一次 AI 交易决策
cron.schedule("*/3 * * * *", async () => {
    const token = jwt.sign(
        { sub: "cron-token" },
        process.env.CRON_SECRET_KEY || ""
    );

    await fetch(
        `${process.env.NEXT_PUBLIC_URL}/api/cron/3-minutes-run-interval?token=${token}`,
        { method: "GET" }
    );
});
```

### React 中的定时器

```typescript
// 来自 open-nof1.ai/app/page.tsx

useEffect(() => {
    // 初始获取数据
    fetchMetrics();
    fetchPricing();

    // 设置定时器，每 10 秒更新一次
    const metricsInterval = setInterval(fetchMetrics, 10000);
    const pricingInterval = setInterval(fetchPricing, 10000);

    // 清理函数：组件卸载时清除定时器
    return () => {
        clearInterval(metricsInterval);
        clearInterval(pricingInterval);
    };
}, [fetchMetrics, fetchPricing]);
```

---

## 小结

本章介绍了 JavaScript/TypeScript 的异步编程：

| 概念 | 语法 | 用途 |
|------|------|------|
| Promise | `new Promise((resolve, reject) => {})` | 异步操作的容器 |
| .then/.catch | `promise.then().catch()` | 处理结果和错误 |
| async | `async function() {}` | 声明异步函数 |
| await | `await promise` | 等待 Promise 完成 |
| Promise.all | `Promise.all([...])` | 并行执行多个 Promise |
| try/catch | `try { await } catch {}` | 错误处理 |

**关键要点：**
1. `async` 函数总是返回 `Promise<T>`
2. `await` 只能在 `async` 函数中使用
3. 独立的异步操作用 `Promise.all` 并行执行
4. 始终用 `try/catch` 处理可能的错误
5. 注意清理定时器和订阅，避免内存泄漏

下一章我们将学习 ES 模块系统。
