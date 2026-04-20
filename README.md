# QA Test Orchestrator

面向测试工程师 / QA 的项目级测试编排 skill。

这个仓库现在采用“动态责任人 + 动态业务域”的产物模型：先从真实运行态识别项目里有哪些责任人、角色、端形态和业务域，再把截图、复测脚本、报告和缺陷证据按这些真实标签归档，而不是使用固定岗位目录模板。

它覆盖的能力包括：

- 分析一个项目当前有哪些前端、后端、移动端、管理台、脚本和 CI 能运行
- 按测试岗位或实际责任人/角色拆分测试任务
- 执行功能测试、回归测试、自动化测试、SDET 测试任务
- 使用 `playwright + agent-browser` 联动完成页面探测、脚本化执行、截图和排障
- 没有测试账号时继续推进验证，并记录账号/测试数据来源
- 在能力允许时使用子 Agent 做分角色、分终端、分链路的全量测试
- 对关键步骤逐步截图，供测试报告和用户手册复用
- 按功能点沉淀 Python 复测脚本，保证后续可多次复测
- 输出图文版 Markdown 测试报告和图文版用户手册

## Core Model

- 责任人/角色跟随项目真实运行态变化，例如 `管理员（教务）`、`班主任`、`学员端（移动端）`
- 业务域跟随真实菜单、审批流、页面标题和已跑通链路变化，例如 `登录与权限`、`办班申请与审批`、`课表与课程`
- 截图目录固定为 `artifacts/screenshots/<责任人>/<业务域>/`
- 调试截图目录固定为 `artifacts/screenshots/_debug/<责任人>/<业务域>/`
- Python 复测脚本目录固定为 `artifacts/automation/<责任人>/<业务域>/`

## Tooling

- `agent-browser`：入口探测、动态元素、页面状态确认、故障定位、补证据
- `Playwright`：稳定主链路、复测脚本、主截图采集、批量回归

任务拆分允许同时采用两条视角：

- 测试岗位视角：功能测试、回归测试、自动化测试、SDET
- 运行态视角：责任人/角色、端类型、业务域、主链路

前者用于规划覆盖范围，后者用于截图、脚本和报告归档。

推荐固定流程：

1. `agent-browser` 探测入口和页面状态
2. `Playwright` 固化主链路和复测脚本
3. 失败时回到 `agent-browser` 排障
4. 排障后回到 `Playwright` 完成复测闭环

## Related Skills

推荐与本 skill 搭配的增强技能如下。它们是优先组合，不是强依赖：

- `playwright`：稳定主流程、E2E、回归脚本、主截图采集、功能点复测脚本
- `agent-browser`：入口探测、动态元素定位、页面状态确认、辅助截图、故障排查
- `javascript-typescript-jest`：单元测试、接口层测试、前端逻辑测试、异步测试、mock 和断言规范
- `superpowers:systematic-debugging`：测试失败但根因不清楚时的排查
- `superpowers:test-driven-development`：先写失败用例，再补实现或补回归
- `superpowers:verification-before-completion`：提测前、回归前、合并前的最终验证
- `superpowers:requesting-code-review`：测试前移，提测前做风险审查
- `python-code-review`：Python 自动化脚本、数据校验脚本、测试工具代码质量检查
- `github:gh-fix-ci`：PR 检查失败、GitHub Actions 失败、流水线异常

## Output Layout

```text
artifacts/
  screenshots/
    管理员（教务）/
      登录与权限/
      课表与课程/
    _debug/
      管理员（教务）/
        课表与课程/
  automation/
    管理员（教务）/
      登录与权限/
      课表与课程/
  report/
  manual/
  data/
```

## References

- `SKILL.md`：触发条件、主流程、输出契约
- `references/dynamic-taxonomy.md`：责任人和业务域的动态识别与命名规则
- `references/role-matrix.md`：如何生成运行态责任人/业务矩阵
- `references/deliverables.md`：目录结构、命名规范、报告引用方式
- `references/automation-retest.md`：Python 复测脚本约束与脚本/截图绑定规则

## Example Prompt

```text
Use $qa-test-orchestrator to discover runtime owners and business domains in this project, test the main flows with agent-browser + Playwright, save screenshots under artifacts/screenshots/<责任人>/<业务域>/, save Python retest scripts under artifacts/automation/<责任人>/<业务域>/, and produce an illustrated Markdown QA report.
```
