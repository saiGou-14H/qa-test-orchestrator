---
name: qa-test-orchestrator
description: 面向测试工程师 / QA / Tester / SDET 的项目级测试编排技能，适用于 Codex CLI、通用 CLI agent、子 agent 和多 agent 协作测试场景。Use when the agent needs to analyze a project for testing, identify runnable entrypoints, design or split test tasks by QA role, execute smoke testing, functional testing, regression testing, E2E testing, automation testing, SDET checks, or CI quality gates, create missing test accounts, capture step-by-step screenshots, organize screenshots by role and feature, generate Python retest scripts per feature, and produce illustrated Markdown test reports, bug reproduction records, acceptance checklists, operation guides, or user manuals. Trigger on requests such as 全量测试, 回归测试, 冒烟测试, 提测验证, 图文测试报告, 用户手册, 测试步骤截图, 子agent并行测试, 自动化测试, Playwright, agent-browser, 联动测试, 复测脚本, Python 自动化脚本, 功能点复测, E2E, Jest, CI排查, 质量门禁, or QA orchestration.
---

# QA Test Orchestrator

## Overview

分析项目当前可执行形态，按测试岗位拆分任务，并把测试执行、截图留痕、缺陷记录、用户手册素材、Markdown 图文报告和可重复复测的 Python 自动化脚本收敛成一套可复现交付物。

优先把“能运行什么、怎么登录、关键链路是什么、哪些验证必须留图、哪些功能点需要沉淀复测脚本”确认清楚，再安排分角色测试，不要先机械铺开测试清单。

## Workflow

### 1. 固定入口并确认测试边界

先确认当前项目的真实入口：

- 识别前端、后端、管理台、移动端、脚本、CI 配置、测试框架。
- 优先看运行配置、路由、环境变量、接口定义、现有测试、构建脚本、日志和最近失败记录。
- 先证明一条最小可跑链路：启动、登录、进入一个核心页面、触发一次有效提交或查询。
- 如果用户没有给测试内容，先基于项目结构生成待确认的测试分工和检查清单，不要虚构业务细节。

把测试范围限定为用户当前项目和其直接依赖的运行环境；不要扩展到无关目录或无关系统。

### 2. 固定使用 Playwright + agent-browser 联动执行

整个测试过程固定采用 `Playwright + agent-browser` 联动，不给执行者自由裁量。

职责边界：

- `agent-browser` 负责入口探测、页面快照、DOM/交互元素识别、运行态确认、动态定位辅助、诊断截图、失败排查。
- `Playwright` 负责稳定、可重复执行的主测试流、功能点复测脚本、主流程截图采集、批量复测执行。

固定执行顺序：

1. 先用 `agent-browser` 探测入口页、登录页、关键菜单、动态元素和运行态。
2. 再由 `Playwright` 编写或维护稳定的功能点复测脚本。
3. 当 `Playwright` 出现定位不稳、页面状态不明、单步失败原因不清时，回退到 `agent-browser` 做辅助排障、补截图和状态确认。
4. 问题确认后，回到 `Playwright` 脚本，完成脚本修正或复测闭环。

不要只用其中一个工具完成整套流程；`Playwright` 与 `agent-browser` 必须联动。

### 3. 按岗位拆分测试任务

按项目实际形态，从下列角色中选择最小必要集合。需要详细映射时，读取 `references/role-matrix.md`。

- 功能测试 / 回归测试：关注主业务流程、异常分支、表单校验、权限切换、导入导出、回归冒烟。
- 自动化测试：关注 E2E、接口联调、核心回归脚本、浏览器行为验证。
- 测试开发 / SDET：关注失败用例复现、最小回归集、mock、测试数据构造、测试代码质量。
- CI / 质量平台：关注 PR 检查、GitHub Actions、环境异常、构建失败、提测前门禁。

如果项目同时包含前端、后端和流水线，至少覆盖：

- 业务链路验证
- 自动化回归
- 根因排查 / 环境诊断
- 交付前验证

### 4. 复测脚本沉淀规则

在被测项目内固定使用 `artifacts/automation/` 作为 Python 自动化复测脚本根目录，不要把执行版脚本沉淀在 skill 自身目录中。详细规则见 `references/automation-retest.md`。

目录规则：

```text
artifacts/
  automation/
    功能测试回归测试/
      01-登录流程.py
      02-创建用户.py
    自动化测试/
      01-订单提交流程.py
    测试开发SDET/
      01-权限边界校验.py
    CI质量平台/
      01-冒烟验证.py
```

强制要求：

- 每个功能点一个 Python 脚本，不做大而全脚本。
- 每个脚本必须可独立复测，不能依赖人工先跑别的脚本。
- 脚本文件名直接对应业务功能点，便于报告、截图、缺陷复现三方对齐。
- 优先复用现有登录逻辑、公共浏览器初始化、公共断言、公共截图保存方法。
- 若脚本需要创建数据，使用时间戳或 `qa_` 前缀，避免一次性脏数据。

### 5. 处理账号和测试数据

如果没有测试账号，先自己创建，再继续测试：

- 优先通过注册页、初始化脚本、管理后台、种子数据、数据库脚本、测试接口创建账号。
- 记录创建方式、账号标识、必要角色、是否依赖验证码或初始化密码。
- 如果账号创建会污染环境，优先使用可回收命名，例如时间戳或 `qa_` 前缀。

不要把“缺少账号”当成终止理由，除非系统确实没有可行创建路径且已验证。

### 6. 保存截图并与功能点脚本绑定

对每一个关键步骤保存截图，文件名直接表达操作，不要使用无语义序号。截图必须按岗位 / 角色分别存放在各自目录下，并在岗位目录下再按功能点归档。详细规则见 `references/deliverables.md`。

推荐结构：

```text
artifacts/
  screenshots/
    功能测试回归测试/
      01-登录流程/
      02-创建用户/
    自动化测试/
      01-订单提交流程/
```

绑定规则：

- 每个功能点 Python 脚本必须对应自己的截图目录。
- `Playwright` 负责主截图采集。
- `agent-browser` 负责探测截图、诊断截图、补充截图。
- Markdown 报告引用截图时，优先使用“岗位目录 + 功能点目录”路径，避免同名冲突。

最低截图要求：

- 启动成功
- 登录前
- 登录后
- 关键菜单进入
- 表单填写前 / 后
- 提交前 / 后
- 成功提示或失败提示
- 核心结果页
- 缺陷复现现场

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
- `Playwright + agent-browser` 联动链路已实际使用，而不是只写在报告里。
- `artifacts/automation/` 中已沉淀对应功能点 Python 脚本。
- 脚本与截图目录一一对应，能支持后续独立复测。
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
- 一组沉淀在 `artifacts/automation/` 下、按功能点拆分的 Python 复测脚本

如果用户只要求其中一部分，按用户要求裁剪，不强行补齐全部产物。

## References

- 读取 `references/role-matrix.md`：当需要把测试任务映射到功能测试、自动化测试、SDET、CI 质量平台角色时使用。
- 读取 `references/deliverables.md`：当需要统一截图命名、目录结构、Markdown 图文报告结构时使用。
- 读取 `references/automation-retest.md`：当需要沉淀 Python 复测脚本、定义脚本目录和脚本约束时使用。
