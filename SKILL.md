---
name: qa-test-orchestrator
description: 面向测试工程师 / QA / Tester / SDET 的项目级测试编排 skill。Use when the agent needs to analyze a runnable project for QA, discover runtime owners or roles and business domains, execute smoke, regression, E2E, or approval-flow checks with Playwright + agent-browser, save screenshots and Python retest scripts under owner-and-domain artifact paths, and produce illustrated Markdown reports, bug reproductions, or user guides. Trigger on requests such as 全量联测, 按责任人联测, 回归测试, 冒烟验证, 业务链路验证, 图文测试报告, 用户手册, Playwright, agent-browser, Python 复测脚本, 截图归档, role-based testing, or QA orchestration.
---

# QA Test Orchestrator

## Overview

在真实可运行项目上组织 QA 联测、回归、自动化测试、SDET 测试任务、复测脚本沉淀和图文交付。

先确认项目当前真实可运行的入口、责任人/角色、业务域和最小可跑通链路，再组织 `agent-browser + Playwright` 联动执行、证据留痕和可复测脚本沉淀。不要先写静态测试清单，也不要把运行中发现的新责任人或新业务硬塞进预设分类。

这个 skill 既支持按运行态责任人/角色组织证据，也支持按测试岗位视角拆分任务，例如功能测试、回归测试、自动化测试、SDET；但产物归档仍以动态责任人/业务域为主。

## Use When

在下面这类任务中使用本 skill：

- 需要先摸清一个项目当前有哪些真实登录入口、菜单、审批链、移动端/PC 端入口再做测试。
- 需要分析一个项目当前有哪些前端、后端、移动端、管理台、脚本和 CI 能运行。
- 需要按测试岗位或实际责任人/角色拆测试任务，而不是按固定测试岗位目录归档。
- 需要做功能测试、回归测试、自动化测试、SDET 测试任务，并按实际执行结果留证。
- 需要按真实业务域沉淀截图、复测脚本、图文报告、缺陷复现记录。
- 需要用 `agent-browser` 做入口探测、动态元素确认、故障定位，再用 `Playwright` 固化主链路与回归。
- 需要在没有现成测试账号时继续推进验证并留下账号/数据说明。
- 需要在当前会话支持时，用子 Agent 做分角色、分终端、分链路的全量测试。
- 需要对关键步骤逐步截图，用于图文版 Markdown 测试报告和用户手册。

## Workflow

### 1. Confirm the runtime surface first

- 优先看启动方式、环境变量、路由、菜单、日志、现有测试、历史失败记录。
- 明确当前有哪些前端、后端、移动端、管理台、脚本和 CI 配置是真实可运行的。
- 至少先证明一条最小可运行链路：启动、登录、进入一个关键页面、触发一次有效查询或提交。
- 把运行态当成事实来源；源码、配置、接口命名只用于解释或补充，不用于推翻已观察到的运行结果。

### 2. Build a runtime owner and domain matrix

- 责任人/角色来自真实登录身份、菜单可见性、审批节点、待办入口、业务操作归属、端类型。
- 业务域来自真实菜单名、页面标题、面包屑、页签名、流程名、列表/详情/表单所属模块，以及已跑通链路。
- 测试任务拆分允许同时使用两条视角：
  1. 测试岗位视角：功能测试、回归测试、自动化测试、SDET
  2. 运行态视角：责任人/角色、端类型、业务域、主链路
- 测试岗位视角用于分配任务和覆盖范围，运行态视角用于目录归档、脚本沉淀和报告引用。
- 动态提取顺序固定为：
  1. 真实登录入口、菜单、页面标题、待办入口、审批链、移动端/PC 端入口
  2. 已跑通的最小业务链路
  3. 源码中的路由、权限、菜单、字典、接口命名
- 运行中发现新的责任人或业务域时，直接扩展矩阵和目录，不要回退到固定枚举。
- 需要命名和归类规则时，读 `references/dynamic-taxonomy.md`。
- 需要矩阵结构和拆分粒度时，读 `references/role-matrix.md`。

### 3. Use Playwright and agent-browser together

- `agent-browser` 负责入口探测、运行态确认、动态元素定位、辅助截图、故障定位、补充证据。
- `Playwright` 负责稳定主链路、可重复执行的复测脚本、主截图采集和批量回归。
- 当会话能力允许且任务规模足够大时，可以使用子 Agent 做分角色、分终端、分链路的并行测试；否则按单 Agent 串行执行。
- 固定联动顺序：
  1. 先用 `agent-browser` 探测入口、菜单、页面状态和动态元素
  2. 再用 `Playwright` 固化稳定主流程与复测脚本
  3. 当 `Playwright` 失败原因不清晰时，回到 `agent-browser` 补排障证据
  4. 排障后再回到 `Playwright` 完成复测闭环

### 4. Persist artifacts with the dynamic taxonomy

- 正式截图固定放到 `artifacts/screenshots/<责任人>/<业务域>/`
- 调试截图固定放到 `artifacts/screenshots/_debug/<责任人>/<业务域>/`
- Python 复测脚本固定放到 `artifacts/automation/<责任人>/<业务域>/`
- 每个功能点都应尽量沉淀为可独立复测的 Python 脚本，保证后续可多次复测。
- 不要再使用 `功能测试回归测试 / 自动化测试 / 测试开发SDET / CI质量平台` 这类硬编码目录。
- 目录名使用项目当前语言下的可读业务名与责任人名；优先采用页面上真实可见的业务标签。
- 文件名只写当前链路、动作和状态，不重复完整责任人和业务域。
- 命名、报告引用和 debug 目录规则见 `references/deliverables.md`。
- 脚本与截图绑定规则见 `references/automation-retest.md`。

### 5. Deliver decision-ready outputs

- 至少交付以下之一：角色/业务矩阵、执行摘要、图文版 Markdown 测试报告、缺陷复现记录、图文版用户手册、账号与测试数据说明。
- 用户如果只要其中一部分，就按要求裁剪，不强行补齐全部材料。
- 宣称完成前，必须确认至少一条核心业务链路是实际执行过的，而不是只阅读了源码。

## Related Skills

下面这些技能是本 skill 内部约定的优先组合。它们是推荐增强项，不是强依赖；环境可用时优先启用，不可用时继续按当前会话能力完成测试。

- `playwright`
  - 适合稳定主流程、E2E、回归脚本、主截图采集、功能点复测脚本
- `agent-browser`
  - 适合入口探测、动态元素定位、页面状态确认、辅助截图、故障排查
- `javascript-typescript-jest`
  - 适合单元测试、接口层测试、前端逻辑测试、异步测试、mock 和断言规范
- `superpowers:systematic-debugging`
  - 适合“测试失败但根因不清楚”的排查场景
- `superpowers:test-driven-development`
  - 适合先写失败用例，再补实现或补回归
- `superpowers:verification-before-completion`
  - 适合提测前、回归前、合并前的最终验证
- `superpowers:requesting-code-review`
  - 适合测试前移，提测前做风险审查
- `python-code-review`
  - 适合 Python 自动化脚本、数据校验脚本、测试工具代码质量检查
- `github:gh-fix-ci`
  - 适合 PR 检查失败、GitHub Actions 失败、流水线异常

## Rules

- 责任人和业务域都以运行态为准，源码命名与运行态冲突时，以运行态命名和可见入口为准。
- 一个责任人可以覆盖多个业务域；同一个业务域也可以在多个责任人下分别存在，按各自路径分别留证。
- 没有测试账号不是默认阻断；先尝试注册页、初始化脚本、管理后台、测试接口、种子数据或可回收命名数据。
- 临时探针、白屏排查、元素定位图不要混进正式交付目录，统一放 `_debug`。

## References

- 读 `references/dynamic-taxonomy.md`：识别和命名责任人/业务域、处理冲突与扩展目录时使用。
- 读 `references/role-matrix.md`：把运行态事实整理成责任人/业务矩阵并切分测试任务时使用。
- 读 `references/deliverables.md`：生成截图目录、文件名、Markdown 报告和 debug 证据时使用。
- 读 `references/automation-retest.md`：沉淀 Python 复测脚本并与截图共享同一 taxonomy 时使用。
