# 第五部分：模块系统

> 本章介绍 ES 模块的 import/export 语法

---

## 1. 模块基础概念

### Python 模块 vs JavaScript 模块

```python
# Python 模块导入
import math
from os import path
from typing import List, Dict
from mymodule import my_function, MyClass

# 相对导入
from . import sibling_module
from ..parent import parent_function
```

```typescript
// JavaScript/TypeScript 模块导入
import * as math from "mathjs";
import path from "path";
import { List, Dict } from "./types";
import { myFunction, MyClass } from "./mymodule";

// 相对导入
import { something } from "./sibling_module";
import { parentFunction } from "../parent";
```

---

## 2. 导出（export）

### 命名导出

```typescript
// 【方式一】在声明时导出
export const PI = 3.14159;

export function add(a: number, b: number): number {
    return a + b;
}

export interface User {
    name: string;
    age: number;
}

export class Calculator {
    add(a: number, b: number) {
        return a + b;
    }
}

// 【方式二】统一在文件末尾导出
const PI = 3.14159;

function add(a: number, b: number): number {
    return a + b;
}

interface User {
    name: string;
    age: number;
}

// 统一导出
export { PI, add, User };

// 导出时重命名
export { add as sum, PI as pi };
```

### 默认导出

```typescript
// 每个模块只能有一个默认导出

// 【方式一】直接导出
export default function greet(name: string) {
    return `Hello, ${name}`;
}

// 【方式二】先定义后导出
function greet(name: string) {
    return `Hello, ${name}`;
}
export default greet;

// 默认导出类
export default class User {
    constructor(public name: string) {}
}

// 默认导出对象
export default {
    name: "config",
    version: "1.0.0"
};
```

### 项目中的导出示例

```typescript
// 来自 open-nof1.ai/lib/trading/current-market-state.ts

// 导出接口
export interface MarketState {
    current_price: number;
    current_ema20: number;
    // ...
}

// 导出函数
export async function getCurrentMarketState(symbol: string): Promise<MarketState> {
    // ...
}

export function formatMarketState(state: MarketState): string {
    // ...
}

// 来自 open-nof1.ai/lib/ai/model.ts

// 导出多个模型实例
export const deepseekv31 = openrouter("deepseek/deepseek-v3.2-exp");
export const deepseekR1 = openrouter("deepseek/deepseek-r1-0528");
export const deepseek = deepseekModel("deepseek-chat");
export const deepseekThinking = deepseekModel("deepseek-reasoner");

// 来自 open-nof1.ai/lib/utils.ts

export function cn(...inputs: ClassValue[]) {
    return twMerge(clsx(inputs));
}
```

---

## 3. 导入（import）

### 命名导入

```typescript
// 导入特定的导出项
import { add, subtract, PI } from "./math";

// 使用
const sum = add(1, 2);
console.log(PI);

// 导入时重命名（避免命名冲突）
import { add as addNumbers } from "./math";
import { add as concatenate } from "./string";

const numResult = addNumbers(1, 2);
const strResult = concatenate("hello", "world");
```

### 默认导入

```typescript
// 导入默认导出
import greet from "./greet";
import User from "./User";

// 默认导入可以使用任意名称
import myGreetFunction from "./greet";  // 也可以

// 同时导入默认导出和命名导出
import React, { useState, useEffect } from "react";
import User, { createUser, UserRole } from "./User";
```

### 命名空间导入

```typescript
// 将模块所有导出作为一个对象导入
import * as math from "./math";

// 使用命名空间访问
const sum = math.add(1, 2);
const diff = math.subtract(5, 3);
console.log(math.PI);

// 常用于有很多导出的模块
import * as utils from "./utils";
import * as types from "./types";
```

### 项目中的导入示例

```typescript
// 来自 open-nof1.ai/app/page.tsx

"use client";

// 导入 React 和 hooks
import { useEffect, useState, useCallback } from "react";

// 导入组件
import { MetricsChart } from "@/components/metrics-chart";
import { CryptoCard } from "@/components/crypto-card";
import { ModelsView } from "@/components/models-view";
import { Card } from "@/components/ui/card";

// 导入类型
import { MarketState } from "@/lib/trading/current-market-state";
import { MetricData } from "@/lib/types/metrics";

// 来自 open-nof1.ai/lib/ai/run.ts

import { generateObject } from "ai";
import { generateUserPrompt, tradingPrompt } from "./prompt";
import { getCurrentMarketState } from "../trading/current-market-state";
import { z } from "zod";
import { deepseekR1 } from "./model";
import { getAccountInformationAndPerformance } from "../trading/account-information-and-performance";
import { prisma } from "../prisma";
import { Opeartion, Symbol } from "@prisma/client";

// 来自 open-nof1.ai/lib/trading/current-market-state.ts

import { EMA, MACD, RSI, ATR } from "technicalindicators";
import { binance } from "./binance";
```

---

## 4. 类型导入（import type）

TypeScript 支持只导入类型，这在编译时会被移除。

```typescript
// 普通导入（值和类型都导入）
import { User, createUser } from "./user";

// 只导入类型
import type { User, UserRole } from "./user";

// 混合导入
import { createUser } from "./user";
import type { User, UserRole } from "./user";

// 或者使用内联 type 关键字
import { createUser, type User, type UserRole } from "./user";
```

### 何时使用 import type？

```typescript
// 1. 只需要类型定义时
import type { Metadata } from "next";

export const metadata: Metadata = {
    title: "My App",
};

// 2. 避免循环依赖
// 当两个模块互相依赖时，import type 可以打破循环

// 3. 优化编译
// import type 在编译后完全移除，减少打包体积
```

### 项目中的类型导入示例

```typescript
// 来自 open-nof1.ai/app/layout.tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
    title: "Create Next App",
    description: "Generated by create next app",
};

// 来自 open-nof1.ai/lib/utils.ts
import { clsx, type ClassValue } from "clsx";
//              ↑ 只导入类型

export function cn(...inputs: ClassValue[]) {
    return twMerge(clsx(inputs));
}

// 来自 open-nof1.ai/next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
    /* config options here */
};

export default nextConfig;
```

---

## 5. 路径别名（Path Aliases）

路径别名让你可以使用简短的路径代替冗长的相对路径。

### 配置路径别名

```json
// tsconfig.json
{
    "compilerOptions": {
        "baseUrl": ".",
        "paths": {
            "@/*": ["./*"]  // @ 映射到项目根目录
        }
    }
}
```

### 使用路径别名

```typescript
// 不使用别名（冗长的相对路径）
import { User } from "../../../lib/types/user";
import { Button } from "../../../components/ui/button";

// 使用 @ 别名（简洁）
import { User } from "@/lib/types/user";
import { Button } from "@/components/ui/button";
```

### 项目中的路径别名示例

```typescript
// 来自 open-nof1.ai/tsconfig.json
{
    "compilerOptions": {
        "paths": {
            "@/*": ["./*"]
        }
    }
}

// 实际使用（来自 open-nof1.ai/app/page.tsx）
import { MetricsChart } from "@/components/metrics-chart";
import { CryptoCard } from "@/components/crypto-card";
import { ModelsView } from "@/components/models-view";
import { Card } from "@/components/ui/card";
import { MarketState } from "@/lib/trading/current-market-state";
import { MetricData } from "@/lib/types/metrics";

// 来自 open-nof1.ai/components/ui/button.tsx
import { cn } from "@/lib/utils";
```

---

## 6. 重新导出（Re-export）

重新导出用于创建统一的入口点。

```typescript
// 【方式一】导入后导出
import { add, subtract } from "./math";
export { add, subtract };

// 【方式二】直接重新导出
export { add, subtract } from "./math";

// 重命名后导出
export { add as sum } from "./math";

// 导出所有
export * from "./math";  // 导出 math 模块的所有命名导出

// 重新导出默认导出
export { default } from "./math";
export { default as math } from "./math";

// 创建桶文件（barrel file）
// index.ts
export * from "./user";
export * from "./post";
export * from "./comment";
export { default as utils } from "./utils";
```

### 桶文件示例

```typescript
// lib/trading/index.ts（如果存在）
export { binance } from "./binance";
export { getCurrentMarketState, formatMarketState } from "./current-market-state";
export type { MarketState } from "./current-market-state";
export { getAccountInformationAndPerformance } from "./account-information-and-performance";
export type { AccountInformationAndPerformance } from "./account-information-and-performance";

// 使用
import {
    binance,
    getCurrentMarketState,
    MarketState
} from "@/lib/trading";  // 从桶文件导入
```

---

## 7. 动态导入

动态导入用于按需加载模块，减少初始加载时间。

```typescript
// 静态导入（打包时包含）
import { heavyFunction } from "./heavy-module";

// 动态导入（运行时按需加载）
async function loadModule() {
    const module = await import("./heavy-module");
    module.heavyFunction();
}

// 条件导入
async function loadLocale(locale: string) {
    const translations = await import(`./locales/${locale}.json`);
    return translations.default;
}

// React 中的动态导入（代码分割）
import dynamic from "next/dynamic";

const HeavyComponent = dynamic(() => import("./HeavyComponent"), {
    loading: () => <p>Loading...</p>,
});
```

---

## 8. CommonJS vs ES Modules

```typescript
// 【ES Modules】（现代标准，推荐）
import { something } from "./module";
export const value = 42;

// 【CommonJS】（Node.js 传统方式）
const { something } = require("./module");
module.exports = { value: 42 };

// TypeScript/现代项目使用 ES Modules
// 编译时会根据目标环境转换
```

### 项目配置

```json
// package.json
{
    "type": "module"  // 使用 ES Modules
}

// tsconfig.json
{
    "compilerOptions": {
        "module": "esnext",  // 使用 ES Modules
        "moduleResolution": "bundler"  // 现代模块解析
    }
}
```

---

## 小结

本章介绍了 ES 模块系统：

| 概念 | 语法 | 用途 |
|------|------|------|
| 命名导出 | `export { name }` | 导出多个值 |
| 默认导出 | `export default` | 导出主要值 |
| 命名导入 | `import { name }` | 导入特定值 |
| 默认导入 | `import name` | 导入默认值 |
| 命名空间导入 | `import * as ns` | 导入全部 |
| 类型导入 | `import type` | 只导入类型 |
| 路径别名 | `@/path` | 简化导入路径 |
| 重新导出 | `export * from` | 创建入口点 |

**关键要点：**
1. 使用命名导出更利于 tree-shaking
2. `import type` 可以避免循环依赖
3. 路径别名让代码更整洁
4. 动态导入可以优化加载性能

下一章我们将学习 React + TypeScript。
