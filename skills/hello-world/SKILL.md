---
name: hello-world
description: "演示 Skill 的多步工作流结构。用于验证 Skill 加载、脚本执行和文件引用的完整链路。"
argument-hint: "你想验证什么？例如：hello world、参数传递、文件输出"
user-invocable: true
---

# Hello World Skill（演示）

本 Skill 用于展示 Skill 的标准结构和工作方式。

## 适用场景

- 验证 Skill 是否被正确加载
- 确认脚本是否可执行
- 测试文件引用和资源加载链路

## 工作流程

### 第一步：收集信息

询问或接收用户输入：

- **任务类型**：`hello` / `greet` / `file`
- **目标对象**（可选）：名字或文件路径

### 第二步：执行对应脚本

根据任务类型执行：

- **hello** → 执行 `[hello.sh](./scripts/hello.sh)`
- **greet** → 执行 `[greet.sh](./scripts/greet.sh)`，参数为用户名
- **file** → 创建 `[output.txt](./scripts/output.txt)` 并写入内容

### 第三步：返回结果

脚本执行完毕后，汇总输出并告诉用户：

- 执行状态（成功 / 失败）
- 脚本输出内容
- 文件路径（如果有）

## 脚本说明

| 脚本          | 作用             | 输入参数        |
| ------------- | ---------------- | --------------- |
| `hello.sh`    | 打印 Hello World | 无              |
| `greet.sh`    | 打印带名字的问候 | `$1` = 用户名   |
| `generate.sh` | 生成示例输出文件 | `$1` = 文件内容 |

## 验证清单

执行完成后确认：

- [ ] SKILL.md 被正确加载
- [ ] 脚本执行无报错
- [ ] 文件引用路径正确
- [ ] 输出结果符合预期
