---
name: coding-standards
description: "项目编码规范与工程约定。适用于所有代码编写、PR 评审和代码审查场景。"
applyTo: **
---

# 编码规范（Coding Standards）

本文档定义 OneTeam 项目的通用编码规范，所有技术栈（.NET、React/前端、UniApp）均需遵循。特殊技术栈的额外规范在各自章节说明。

---

## 1. 通用规范

### 1.1 命名规范

| 场景           | 规范                                 | 示例                                 |
| -------------- | ------------------------------------ | ------------------------------------ |
| 类名、接口名   | PascalCase                           | `UserService`, `IOrderRepository`    |
| 方法名、函数名 | PascalCase                           | `GetUserById`, `CalculateTotal`      |
| 变量、参数     | camelCase                            | `userId`, `orderList`                |
| 常量           | 全大写下划线分隔                     | `MAX_RETRY_COUNT`, `DEFAULT_TIMEOUT` |
| 私有字段       | `_camelCase`（下划线前缀）           | `_cache`, `_logger`                  |
| 文件名         | 与内容同名，PascalCase 或 kebab-case | `UserService.cs`, `order-list.ts`    |
| 数据库表/字段  | 全小写下划线分隔                     | `user_order`, `created_at`           |

**禁止**：

- 拼音命名
- 单个字母变量（循环计数器除外：`i`, `j`, `k`）
- 语义模糊的名称如 `data`, `info`, `temp`

### 1.2 代码格式

- **缩进**：4 空格（不对 Tab）
- **行宽**：不超过 120 字符
- **文件末尾**：留一个空行
- **命名空间**：每层一行，不压缩
- **引号**：字符串统一用双引号（JSON 除外）
- **分号**：语言默认风格（TypeScript/Go 不加分号，Java/C# 加分号）

### 1.3 注释规范

| 场景                  | 要求                                                             |
| --------------------- | ---------------------------------------------------------------- |
| 公共 API / 接口       | 必须有文档注释，说明参数、返回值、异常                           |
| 复杂业务逻辑          | 必须有注释说明"为什么这样做"而非"做了什么"                       |
| TODO / FIXME          | 必须带负责人和日期：`// TODO(zhangsan): 2026-04-08 完成缓存替换` |
| 敏感逻辑（安全/权限） | 必须有注释说明业务含义                                           |
| 简单自明代码          | 不需要注释                                                       |

**禁止**：

- 注释掉的大段代码直接提交，必须删除或说明原因
- 中文注释夹在英文注释中间
- 注释里写废话如 `// increment i`

### 1.4 提交规范

- **提交信息格式**：

```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

- **type 取值**：

| type       | 适用场景               |
| ---------- | ---------------------- |
| `feat`     | 新功能                 |
| `fix`      | Bug 修复               |
| `docs`     | 文档更新               |
| `style`    | 格式调整（不影响功能） |
| `refactor` | 重构（不影响功能）     |
| `perf`     | 性能优化               |
| `test`     | 添加/修改测试          |
| `chore`    | 构建/工具变更          |

- **subject 规则**：不超过 72 字符，使用祈使句，开头小写，不加句号
- **示例**：

```
feat(user): add mobile phone verification

- Add SMS verification before password change
- Add rate limit: 5 attempts per hour

Closes #123
```

---

## 2. .NET / C# 规范

### 2.1 项目结构

```
src/
├── Domain/           # 实体、值对象、聚合根
├── Application/      # 应用服务、用例、接口定义
├── Infrastructure/   # 数据库、外部服务、第三方实现
├── API/              # Controllers、Middleware、Filters
└── Shared/           # 跨层共享（DTO、枚举、工具）
```

### 2.2 命名额外约定

- 抽象类加 `Base` 前缀：`BaseEntity`, `BaseController`
- 异常类加 `Exception` 后缀：`UserNotFoundException`
- 事件类加 `Event` 后缀：`OrderPlacedEvent`
- 枚举值全大写下划线分隔：`OrderStatus.PENDING_PAYMENT`

### 2.3 关键规则

- **依赖注入**：构造函数注入，不使用 `ServiceLocator`
- **异步**：所有 I/O 操作必须 `async`/`await`，禁止 `.Result` 或 `.Wait()`
- **异常**：业务异常用自定义异常类，禁止吞掉异常或直接 `throw new Exception()`
- **null 检查**：使用可空操作符 `?.` 和空合并 `??`，禁止 `== null` 判空后仍继续执行业务
- **LINQ**：链式写法，禁止多层嵌套 `Select().Where().Select()`
- **Entity Framework**：导航属性必须 `virtual`，使用 `Include` 预加载，不 N+1 查询

---

## 3. React / TypeScript 前端规范

### 3.1 项目结构（Vite + React）

```
src/
├── components/       # 通用组件（atomic design 建议）
├── pages/            # 页面级组件
├── hooks/            # 自定义 hooks
├── stores/            # 状态管理（Pinia）
├── services/         # API 请求封装
├── types/            # TypeScript 类型定义
├── utils/            # 纯工具函数
└── assets/           # 静态资源
```

### 3.2 关键规则

- **TypeScript**：严格模式，禁止 `any`，禁止 `@ts-ignore`
- **组件**：函数组件 + Hooks，不使用 class 组件
- **Props**：必须定义接口，不使用 `React.FC<{}>` 类型推断
- **状态**：优先 `useState` / `useReducer`，全局状态才上 Pinia
- **样式**：统一 Tailwind CSS，不混用 CSS Modules 或内联样式
- **API**：统一 `services/` 封装，禁止在组件内直接 `fetch`
- **副作用**：所有副作用对应 `useEffect` 必须有清理函数或注释说明依赖

### 3.3 命名额外约定

| 场景          | 规范                             | 示例                |
| ------------- | -------------------------------- | ------------------- |
| 组件文件      | PascalCase                       | `UserCard.tsx`      |
| Hook 文件     | `use` 前缀 + camelCase           | `useUserProfile.ts` |
| 工具函数文件  | camelCase                        | `formatDate.ts`     |
| 类型/接口文件 | `types/` 下同名或 `*.types.ts`   | `user.types.ts`     |
| 样式          | 与组件同名 `UserCard.module.css` | —                   |

---

## 4. UniApp / Vue 规范

### 4.1 项目结构

```
src/
├── pages/            # 页面
├── components/       # 组件（按业务划分）
├── composables/      # 组合式函数
├── stores/           # Pinia 状态
├── services/         # 接口请求封装
├── utils/            # 工具函数
└── static/           # 静态资源
```

### 4.2 关键规则

- **Vue 3**：Composition API (`<script setup>`)，不混用 Options API
- **TypeScript**：严格模式，禁止 `any`
- **跨端兼容**：平台差异使用条件编译 `#ifdef`，禁止硬编码平台判断
- **样式**：使用 `lang="scss"`，单位统一 `rpx`（需要适配时）或 `px`
- **状态**：Pinia，禁止 `Vuex`
- **API 请求**：统一 `services/` 封装，处理登录态、错误拦截、分页
- **生命周期**：页面和组件生命周期不得混用，页面用 `onLoad`，组件用 `onMounted`

---

## 5. 数据库规范

- **索引**：禁止全表扫描的查询没有索引
- **敏感字段**：密码、密钥等禁止明文存储，使用散列或加密

---

## 6. API 设计规范

- **RESTful 风格**：资源命名复数名词，HTTP 方法表达动作
- **URL**：`/api/v1/{resource}/{id}/{sub-resource}`
- **请求**：GET 查询，POST 创建，PUT 全量更新，PATCH 部分更新，DELETE 删除
- **响应格式**：

```json
{
  "code": 0,
  "data": {},
  "message": "success"
}
```

- **分页**：`GET /users?page=1&page_size=20`，响应含 `total`, `page`, `page_size`
- **错误码**：业务错误码与 HTTP 状态码分开，业务错误码为正整数
- **版本**：URL 路径含版本号 `/api/v1/`

---

## 7. Git 分支规范

| 分支       | 命名                            | 说明                           |
| ---------- | ------------------------------- | ------------------------------ |
| 主分支     | `main`                          | 生产环境代码，只合并不直接开发 |
| 开发分支   | `develop`                       | 集成分支，所有功能合并到这里   |
| 功能分支   | `feature/{issue-id}-{简短描述}` | 单个功能开发                   |
| 修复分支   | `fix/{issue-id}-{简短描述}`     | Bug 修复                       |
| 热修复分支 | `hotfix/{issue-id}-{简短描述}`  | 生产环境紧急修复               |

- **合并方式**：所有分支合并使用 `Merge Commit`，禁止 `Rebase and merge` / `Squash and merge`（除非明确场景）
- **保护分支**：`main` 和 `develop` 必须 PR 才能合并，必须至少 1 人 review 通过

---

## 8. 代码审查规范

### 8.1 PR 创建要求

- 必须关联对应的 Issue 或需求
- 必须填写 PR 描述：改了什么、为什么改、如何验证
- 必须通过 CI（编译通过、单元测试通过、lint 通过）
- 禁止超大 PR：单次 PR 不超过 **400 行**改动，超出必须拆分

### 8.2 Reviewer 职责

- 检查：功能正确性、边界条件、安全性、可维护性
- 通过条件：至少 1 人 Approve
- 问题分级：必须修复（Blocking）/ 建议修改（Non-blocking）/ 可以忽略（nit）

### 8.3 审查清单

- [ ] 需求覆盖：所有需求点都有对应实现
- [ ] 边界处理：空值、异常、超长输入、并发场景有处理
- [ ] 安全：SQL 注入、XSS、越权有防范
- [ ] 可测试：关键逻辑有单测覆盖
- [ ] 可维护：没有重复代码，没有超长方法，没有隐式依赖
- [ ] 文档：新增公共 API 有注释，配置变更有说明

---

## 9. 测试规范

| 层级     | 覆盖率要求         | 说明                           |
| -------- | ------------------ | ------------------------------ |
| 单元测试 | 核心业务逻辑 > 80% | 工具类、算法、业务 service     |
| 集成测试 | 关键路径 100%      | 数据库操作、API 调用、外部服务 |
| E2E 测试 | 核心用户流程       | 登录、核心业务流程             |

- **测试命名**：`{方法名}_{场景}_{预期结果}`
- **单测原则**：无外部依赖（mock 数据库/第三方），执行速度快（< 100ms）
- **禁止**：测试里写 `Thread.Sleep`，测试依赖执行顺序
