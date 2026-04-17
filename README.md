# QA Test Orchestrator

面向测试工程师 / QA 的项目级测试编排 Skill。

这个 Skill 不绑定某一个特定运行器，适用于所有基于 CLI、Agent、子 Agent、多 Agent 编排的测试执行场景，包括但不限于：

- Codex CLI
- 通用 CLI Agent
- 子 Agent 并行测试执行
- 多 Agent 协作测试编排
- 具备文件、终端、浏览器自动化、代码分析能力的代理式测试环境

它的核心目标不是只“写几个测试用例”，而是先分析项目当前可运行形态，再按岗位拆分测试任务，组织执行、截图留痕、缺陷记录、图文测试报告和用户手册素材，形成可复现的完整测试交付。

## 适用场景

当你需要让 Agent 或 CLI 测试执行器完成下面这类工作时，使用这个 Skill：

- 分析一个项目当前有哪些前端、后端、移动端、管理台、脚本和 CI 能运行
- 按测试岗位 / 角色拆分测试任务
- 做功能测试、回归测试、自动化测试、SDET 测试任务和 CI 质量门禁检查
- 没有测试账号时自动创建测试账号并继续验证
- 使用子 Agent 做分角色、分终端、分链路的全量测试
- 对关键步骤逐步截图，供用户手册和测试报告复用
- 输出图文版 Markdown 测试报告和图文版用户手册

## 设计目标

这个 Skill 主要解决 5 类问题：

1. 先分析真实可运行入口，而不是直接空写测试清单
2. 按测试岗位和系统形态拆分任务，而不是一股脑混测
3. 允许 Agent / 子 Agent 并行执行，提高全量测试效率
4. 强制关键步骤截图留痕，并按岗位 / 角色分别归档，方便测试报告和用户手册复用
5. 在没有测试账号时优先自建账号，而不是把“缺账号”当终止理由

## 适用执行环境

本 Skill 适用于所有满足以下任一条件的执行环境：

- 能运行 Shell / PowerShell / Bash 命令
- 能读取项目代码、配置、日志和测试产物
- 能调用浏览器自动化工具，例如 Playwright
- 能按任务把工作拆给子 Agent
- 能生成 Markdown 文档、截图和测试记录

换句话说，这个 Skill 不是“只给 Codex 用”，而是一个通用的 QA 编排规范，可以被任何具备类似能力的 CLI Agent 或测试型代理直接采用。

## 关联技能

Skill 内部约定的优先组合如下：

- `javascript-typescript-jest`
  - 适合单元测试、接口层测试、前端逻辑测试、异步测试、mock 和断言规范
- `playwright`
  - 适合 E2E、页面自动化、回归测试、表单流程、浏览器行为验证和截图采集
- `superpowers:systematic-debugging`
  - 适合“测试失败但根因不清楚”的排查场景
- `superpowers:test-driven-development`
  - 适合先写失败用例，再补实现或补回归
- `superpowers:verification-before-completion`
  - 适合提测前、回归前、合并前的最终验证
- `superpowers:requesting-code-review`
  - 适合测试前移，提测前做风险审查
- `python-code-review`
  - 适合测试平台脚本、数据校验脚本、自动化工具代码质量检查
- `github:gh-fix-ci`
  - 适合 PR 检查失败、GitHub Actions 失败、流水线异常

## 按岗位推荐

### 功能测试 / 回归测试

- 推荐组合：`playwright` + `superpowers:verification-before-completion`
- 核心目标：验证主流程、异常分支、表单校验、权限切换、导入导出、回归冒烟

### 自动化测试

- 推荐组合：`playwright` + `javascript-typescript-jest` + `superpowers:systematic-debugging`
- 核心目标：把高频回归和关键业务路径固化为可重复执行脚本

### 测试开发 / SDET

- 推荐组合：`playwright` + `javascript-typescript-jest` + `superpowers:test-driven-development` + `python-code-review`
- 核心目标：失败用例复现、测试夹具、mock、回归最小集、测试代码质量

### CI / 质量平台

- 推荐组合：`github:gh-fix-ci` + `superpowers:systematic-debugging` + `superpowers:requesting-code-review`
- 核心目标：PR 检查、流水线失败、环境漂移、质量门禁

## 工作流

### 1. 固定真实入口

- 识别当前项目的前端、后端、管理台、移动端、脚本和 CI 配置
- 优先看运行配置、路由、环境变量、接口定义、现有测试、构建脚本、日志和失败记录
- 先证明一条最小可跑链路：启动、登录、进入核心页面、触发一次有效提交或查询

### 2. 按角色拆测试任务

- 从功能测试 / 回归测试、自动化测试、SDET、CI / 质量平台中选择当前项目最小必要集合
- 不机械追求“全覆盖”，而是围绕可运行链路和风险点分工

### 3. 选择技能组合

- 按技术栈、测试目标和失败现象加载最小必要技能
- 不要求每次都把所有测试技能一起加载

### 4. 使用子 Agent 做全量测试

- 仅在用户明确要求或任务天然可拆分时启用
- 典型拆分方式：
  - 按终端：Web 管理台、移动端、接口 / 数据、CI / 流水线
  - 按岗位：功能回归、自动化回归、SDET、CI 门禁
- 每个子 Agent 都必须回传：
  - 执行步骤
  - 结果
  - 失败点
  - 截图路径
  - 复现条件
  - 建议下一步

### 5. 处理测试账号

- 没有测试账号时，优先自己创建，而不是直接中止
- 优先从注册页、初始化脚本、管理后台、测试接口、种子数据、数据库脚本寻找创建路径
- 推荐使用可回收命名，例如 `qa_` 前缀或时间戳账号

### 6. 截图留痕

Skill 要求关键步骤必须截图，按操作命名，并且每个岗位 / 角色的截图单独放在各自目录下。

最低截图集合：

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

### 7. 图文交付

Skill 默认要求输出：

- 一份按岗位 / 角色拆分的测试计划或执行摘要
- 一份带截图引用的 Markdown 测试报告
- 一份带截图引用的 Markdown 用户手册或操作说明
- 一个按岗位 / 角色拆分的截图目录或截图索引
- 一份测试账号与测试数据说明

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

artifacts/
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

## 使用方式

示例提示词：

```text
Use $qa-test-orchestrator to analyze this project, split testing by QA role, run end-to-end coverage with subagents, save screenshots for each step, and produce illustrated test and user docs.
```

也可以直接提出中文任务，例如：

```text
请使用 qa-test-orchestrator，对当前项目按功能测试、自动化测试、SDET、CI 质量平台四类角色拆分任务，执行全量测试，每一步都截图并输出图文测试报告和用户手册；如果没有测试账号，请自行创建。
```

## 当前限制

- Skill 负责“如何组织测试与交付”，不内置项目特定账号、环境地址和业务数据
- 是否启用子 Agent，受当前会话能力和用户要求约束
- 是否能直接推送 GitHub，取决于本地 Git 环境、目标仓库和远端权限

