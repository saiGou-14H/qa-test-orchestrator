---
name: qa-test-orchestrator
description: 面向测试工程师 / QA 的项目级测试编排技能，适用于 Codex CLI、通用 CLI agent、子 agent 和多 agent 协作测试场景。Use when the agent needs to analyze a project, identify runnable entrypoints, split testing tasks by QA role, execute functional/regression/automation/SDET/CI checks, create missing test accounts, delegate independent flows to subagents, save step-by-step screenshots, and produce illustrated Markdown test reports or user manuals.
---

# QA Test Orchestrator

## Overview

分析项目当前可执行形态，按测试岗位拆分任务，并把测试执行、截图留痕、缺陷记录、用户手册素材和 Markdown 图文报告收敛成一套可复现交付物。

优先把“能运行什么、怎么登录、关键链路是什么、哪些验证必须留图”确认清楚，再安排分角色测试，不要先机械铺开测试清单。

## Workflow

### 1. 固定入口并确认测试边界

先确认当前项目的真实入口：

- 识别前端、后端、管理台、移动端、脚本、CI 配置、测试框架。
- 优先看运行配置、路由、环境变量、接口定义、现有测试、构建脚本、日志和最近失败记录。
- 先证明一条最小可跑链路：启动、登录、进入一个核心页面、触发一次有效提交或查询。
- 如果用户没有给测试内容，先基于项目结构生成待确认的测试分工和检查清单，不要虚构业务细节。

把测试范围限定为用户当前项目和其直接依赖的运行环境；不要扩展到无关目录或无关系统。

### 2. 按岗位拆分测试任务

按项目实际形态，从下列角色中选择最小必要集合。需要详细映射时，读取 `references/role-matrix.md`。

- 功能测试 / 回归测试：关注主业务流程、异常分支、表单校验、权限切换、导入导出、回归冒烟。优先组合 `playwright` 与交付前验证。
- 自动化测试：关注 E2E、接口联调、核心回归脚本、浏览器行为验证。JS/TS 项目优先组合 `playwright` + `javascript-typescript-jest`。
- 测试开发 / SDET：关注失败用例复现、最小回归集、mock、测试数据构造、测试代码质量。优先组合 `playwright` + `javascript-typescript-jest` + `superpowers:test-driven-development` + `python-code-review`。
- CI / 质量平台：关注 PR 检查、GitHub Actions、环境异常、构建失败、提测前门禁。优先组合 `github:gh-fix-ci` + `superpowers:systematic-debugging` + `superpowers:requesting-code-review`。

如果项目同时包含前端、后端和流水线，至少覆盖：

- 业务链路验证
- 自动化回归
- 根因排查 / 环境诊断
- 交付前验证

### 3. 选择技能组合

按技术栈和测试目标选择技能：

- `javascript-typescript-jest`：单元测试、接口层测试、前端逻辑测试、异步测试、mock、断言规范。
- `playwright`：E2E、页面自动化、回归测试、表单流程、浏览器行为验证、截图采集。
- `superpowers:systematic-debugging`：测试失败但根因不清楚、联调异常、CI 挂掉、环境问题。
- `superpowers:test-driven-development`：先写失败用例，再补实现或补回归。
- `superpowers:verification-before-completion`：提测前、回归前、合并前验证，不先宣称完成。
- `superpowers:requesting-code-review`：提测前审代码，提前发现高风险分支和明显缺陷。
- `python-code-review`：Python 自动化脚本、接口测试工具、数据校验脚本质量检查。
- `github:gh-fix-ci`：PR 检查失败、Actions 失败、流水线异常。

不要为了“全量”而机械加载全部技能；只加载和当前测试阶段直接相关的组合。

### 4. 使用子 agent 执行全量测试

仅在用户明确要求使用子 agent，或任务天然可以拆为互不阻塞的测试分片时，才启用子 agent。

拆分原则：

- 按终端形态拆：Web 管理台、移动端、接口 / 数据、CI / 流水线。
- 按岗位拆：功能回归、自动化回归、SDET、CI 质量门禁。
- 按写入范围拆：避免多个子 agent 同时修改同一份测试脚本或同一组产物。

对子 agent 的要求：

- 给出明确的测试目标、入口地址、账号信息、输出目录、截图命名规则、需要回传的结论。
- 不把主代理的预设结论灌给子 agent，只给执行任务所需的上下文。
- 要求每个子 agent 返回：执行步骤、结果、失败点、截图路径、复现条件、建议下一步。

主代理负责汇总，而不是重复执行子 agent 已完成的覆盖面。

### 5. 处理账号和测试数据

如果没有测试账号，先自己创建，再继续测试：

- 优先通过注册页、初始化脚本、管理后台、种子数据、数据库脚本、测试接口创建账号。
- 记录创建方式、账号标识、必要角色、是否依赖验证码或初始化密码。
- 如果账号创建会污染环境，优先使用可回收命名，例如时间戳或 `qa_` 前缀。

不要把“缺少账号”当成终止理由，除非系统确实没有可行创建路径且已验证。

### 6. 保存截图和证据

对每一个关键步骤保存截图，文件名直接表达操作，不要使用无语义序号。截图必须按岗位 / 角色分别存放在各自目录下，详细规则见 `references/deliverables.md`。

最低要求：

- 启动成功
- 登录前
- 登录后
- 关键菜单进入
- 表单填写前 / 后
- 提交前 / 后
- 成功提示或失败提示
- 核心结果页
- 缺陷复现现场

命名示例：

- `01-打开登录页.png`
- `02-输入管理员账号.png`
- `03-登录成功进入工作台.png`
- `04-创建测试账号成功.png`
- `05-提交订单前.png`
- `06-提交订单后报错.png`

如果同一步骤有 PC / 移动端差异，在文件名中追加终端标记，例如 `03-登录成功进入工作台-pc.png`。同一岗位的截图放入该岗位独立目录，例如 `artifacts/screenshots/功能测试回归测试/03-登录成功进入工作台-pc.png`。

### 7. 产出图文版报告和用户手册素材

最终至少交付两类 Markdown：

- 图文测试报告：覆盖范围、环境、账号、步骤、结果、缺陷、风险、待确认项。
- 图文用户手册或操作说明：按用户视角复用测试截图，描述标准操作路径。

优先使用如下顺序组织结果：

1. Outcome
2. Key evidence
3. Verification
4. Next step

把截图嵌入 Markdown，不要只给文件列表。对长日志只摘关键行，不粘贴整段噪声输出。

### 8. 结束前做交付验证

在宣称测试完成前，至少确认：

- 核心测试链路已经实际执行，而不是只看代码。
- 每个角色分工都有结果或明确的未覆盖原因。
- 关键步骤截图可打开、命名清晰、能对应到报告步骤。
- 缺陷结论有复现步骤、现象、期望、实际结果。
- 报告和用户手册都能单独阅读，不依赖聊天上下文。

## Output Contract

默认输出以下内容：

- 一份按岗位 / 角色拆分的测试计划或执行摘要
- 一份带截图引用的 Markdown 测试报告
- 一份带截图引用的 Markdown 用户手册或操作说明
- 一个按岗位 / 角色拆分的截图目录或索引，能按步骤追溯
- 一个账号与测试数据说明区块

如果用户只要求其中一部分，按用户要求裁剪，不强行补齐全部产物。

## References

- 读取 `references/role-matrix.md`：当需要把测试任务映射到功能测试、自动化测试、SDET、CI 质量平台角色时使用。
- 读取 `references/deliverables.md`：当需要统一截图命名、目录结构、Markdown 图文报告结构时使用。


