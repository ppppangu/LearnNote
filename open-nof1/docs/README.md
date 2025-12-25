# TypeScript 教程 - Python 开发者快速入门

> 本教程专为精通 Python 但不熟悉 JavaScript/TypeScript 的开发者编写
> 基于 Open-nof1.ai 项目的实际代码进行讲解

## 目录

### 第零部分：JavaScript 基础
- [00-javascript-basics.md](./00-javascript-basics.md) - JavaScript 基础语法（对比 Python）

### 第一部分：TypeScript 类型系统
- [01-typescript-basics.md](./01-typescript-basics.md) - 类型注解、基础类型、类型推断

### 第二部分：TypeScript 核心特性
- [02-typescript-core.md](./02-typescript-core.md) - 接口、类型别名、泛型、枚举

### 第三部分：高级类型
- [03-advanced-types.md](./03-advanced-types.md) - 工具类型、类型守卫、可选链

### 第四部分：异步编程
- [04-async-programming.md](./04-async-programming.md) - Promise、async/await

### 第五部分：模块系统
- [05-module-system.md](./05-module-system.md) - import/export、路径别名

### 第六部分：React + TypeScript
- [06-react-typescript.md](./06-react-typescript.md) - 组件、Hooks、JSX

### 第七部分：框架特定语法
- [07-framework-specific.md](./07-framework-specific.md) - Next.js、Prisma、Zod

### 第八部分：项目实战
- [08-project-practice.md](./08-project-practice.md) - 项目代码解读

---

## 项目技术栈

| 技术 | 版本 | 用途 |
|------|------|------|
| Next.js | 15 | React 全栈框架 |
| React | 19 | UI 组件库 |
| TypeScript | 5 | 类型安全的 JavaScript |
| Prisma | 6 | 数据库 ORM |
| Tailwind CSS | 4 | CSS 框架 |
| Zod | 4 | 运行时类型验证 |
| CCXT | 4.5 | 加密货币交易所 API |

## 学习建议

1. **按顺序阅读**：从第零部分开始，循序渐进
2. **动手实践**：每个示例都来自项目代码，可以直接运行查看
3. **对比思考**：每个概念都有 Python 对比，帮助理解
4. **项目结合**：最后一部分会串联所有知识点

## 项目文件结构

```
open-nof1.ai/
├── app/                    # Next.js 应用目录
│   ├── api/               # API 路由
│   ├── page.tsx           # 主页面
│   └── layout.tsx         # 布局组件
├── components/            # React 组件
├── lib/                   # 核心业务逻辑
│   ├── ai/               # AI 推理模块
│   └── trading/          # 交易模块
└── prisma/               # 数据库模型
```
