# QA Test Orchestrator

面向测试工程师 / QA 的项目级测试编排 Skill。

这个 Skill 不绑定某一个特定运行器，适用于所有基于 CLI、Agent、子 Agent、多 Agent 编排的测试执行场景，包括但不限于：

- Codex CLI
- 通用 CLI Agent
- 子 Agent 并行测试执行
- 多 Agent 协作测试编排
- 具备文件、终端、浏览器自动化、代码分析能力的代理式测试环境

它的核心目标不是只“写几个测试用例”，而是先分析项目当前可运行形态，再按岗位拆分测试任务，组织执行、截图留痕、缺陷记录、图文测试报告、用户手册素材，以及按功能点沉淀可重复复测的 Python 自动化脚本，形成可复现的完整测试交付。

## 适用场景

当你需要让 Agent 或 CLI 测试执行器完成下面这类工作时，使用这个 Skill：

- 分析一个项目当前有哪些前端、后端、移动端、管理台、脚本和 CI 能运行
- 按测试岗位 / 角色拆分测试任务
- 做功能测试、回归测试、自动化测试、SDET 测试任务和 CI 质量门禁检查
- 使用 `playwright + agent-browser` 联动完成页面探测、脚本化执行、截图和排障
- 没有测试账号时自动创建测试账号并继续验证
- 使用子 Agent 做分角色、分终端、分链路的全量测试
- 对关键步骤逐步截图，供用户手册和测试报告复用
- 按功能点沉淀 Python 复测脚本，保证后续可多次复测
- 输出图文版 Markdown 测试报告和图文版用户手册

## 设计目标

这个 Skill 主要解决 6 类问题：

1. 先分析真实可运行入口，而不是直接空写测试清单
2. 按测试岗位和系统形态拆分任务，而不是一股脑混测
3. 固定使用 `Playwright + agent-browser` 联动，而不是临场决定工具分工
4. 允许 Agent / 子 Agent 并行执行，提高全量测试效率
5. 强制关键步骤截图留痕，并按岗位 / 角色 / 功能点分别归档，方便测试报告和用户手册复用
6. 在没有测试账号时优先自建账号，并把功能点回归能力沉淀到项目内 `artifacts/automation/`

## 适用执行环境

本 Skill 适用于所有满足以下任一条件的执行环境：

- 能运行 Shell / PowerShell / Bash 命令
- 能读取项目代码、配置、日志和测试产物
- 能调用浏览器自动化工具，例如 Playwright 和 agent-browser
- 能按任务把工作拆给子 Agent
- 能生成 Markdown 文档、截图和测试记录
- 能在被测项目目录内沉淀复测脚本与测试产物

换句话说，这个 Skill 不是“只给 Codex 用”，而是一个通用的 QA 编排规范，可以被任何具备类似能力的 CLI Agent 或测试型代理直接采用。

## 核心方法

### 固定联动模型

这个 Skill 明确规定：

- `agent-browser` 负责入口探测、运行态确认、动态元素定位、辅助截图、异常排查
- `Playwright` 负责稳定主流程、可重复执行的功能点脚本、主截图采集、批量复测

固定顺序是：

1. 先 `agent-browser` 探测
2. 后 `Playwright` 脚本化
3. 失败时再回到 `agent-browser` 排障
4. 排障后回到 `Playwright` 完成复测闭环

### 复测脚本沉淀

所有执行版 Python 自动化脚本固定落在被测项目内：

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

规则：

- 每个功能点一个 Python 脚本
- 每个脚本都要可独立运行、可独立复测
- 脚本名称直接对应功能点
- 默认复用公共登录、公共浏览器初始化、公共断言、公共截图能力

### 截图与脚本绑定

截图不仅按岗位 / 角色分类，还要与功能点脚本绑定：

```text
artifacts/
  screenshots/
    功能测试回归测试/
      01-登录流程/
    自动化测试/
      01-订单提交流程/
```

例如：

- `artifacts/automation/功能测试回归测试/01-登录流程.py`
- `artifacts/screenshots/功能测试回归测试/01-登录流程/`

## 关联技能

Skill 内部约定的优先组合如下：

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

## 目录结构

```text
qa-test-orchestrator/
  SKILL.md
  README.md
  agents/
    openai.yaml
  references/
    role-matrix.md
    deliverables.md
    automation-retest.md

artifacts/
  automation/
    功能测试回归测试/
    自动化测试/
    测试开发SDET/
    CI质量平台/
  screenshots/
    功能测试回归测试/
    自动化测试/
    测试开发SDET/
    CI质量平台/
  report/
  manual/
  data/
```

## 参考文件

- `SKILL.md`
  - Skill 主体说明，定义触发条件、主流程、输出契约
- `references/role-matrix.md`
  - 岗位与测试任务映射
- `references/deliverables.md`
  - 截图命名、目录结构、Markdown 图文报告格式
- `references/automation-retest.md`
  - Python 复测脚本目录、脚本约束和脚本与截图绑定规则

## 使用方式

示例提示词：

```text
Use $qa-test-orchestrator to analyze this project for QA, probe pages with agent-browser, implement stable feature retest flows with Playwright, save screenshots by role and feature, generate Python retest scripts under artifacts/automation, and produce illustrated Markdown reports and user manuals.
```

也可以直接提出中文任务，例如：

```text
请使用 qa-test-orchestrator，对当前项目按功能测试、自动化测试、SDET、CI 质量平台四类角色拆分任务，固定使用 playwright + agent-browser 联动执行，每个功能点沉淀一个 Python 复测脚本到 artifacts/automation，并按岗位和功能点归档截图，输出图文测试报告和用户手册；如果没有测试账号，请自行创建。
```

## 当前限制

- Skill 负责“如何组织测试与交付”，不内置项目特定账号、环境地址和业务数据
- 是否启用子 Agent，受当前会话能力和用户要求约束
- 是否能直接推送 GitHub，取决于本地 Git 环境、目标仓库和远端权限
- 当前版本只补规范和目录约定，不额外提供脚手架生成器或模板代码
