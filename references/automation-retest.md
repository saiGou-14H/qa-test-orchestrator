# Automation Retest

## Purpose

把复测能力沉淀到被测项目自身，而不是停留在一次性执行记录里。所有执行版 Python 自动化脚本固定落在 `artifacts/automation/`。

## Directory Rules

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

- 每个功能点一个 Python 脚本。
- 脚本按岗位 / 角色分目录。
- 脚本名称直接对应业务功能点，使用 `序号-功能点名.py`。
- 不要把多个功能点塞进一个总脚本。

## Tool Responsibilities

- 先用 `agent-browser` 探测入口、登录页、动态元素、页面状态。
- 再用 `Playwright` 落稳定复测脚本。
- `Playwright` 失败或状态不明时，再用 `agent-browser` 做辅助定位、补截图、诊断。
- 脚本最终以 `Playwright` 为主执行引擎，`agent-browser` 作为探测与排障辅助。

## Script Contract

每个 Python 脚本至少要包含这些信息或等价结构：

- 功能点名称
- 前置条件
- 依赖账号或测试数据
- 成功判定条件
- 失败时截图输出位置
- 日志输出位置

每个脚本必须：

- 可直接运行
- 可独立复测
- 不依赖人工先跑其他脚本
- 优先复用公共登录逻辑、浏览器初始化、断言、截图工具

## Repeatability Rules

- 默认不要写死一次性数据。
- 若必须创建数据，使用时间戳或 `qa_` 前缀。
- 清理逻辑可以有，但不要依赖清理逻辑存在才能运行。
- 失败时必须留下足够证据，至少包括日志或截图路径。

## Binding With Screenshots

每个脚本必须绑定对应截图目录。例如：

- `artifacts/automation/功能测试回归测试/01-登录流程.py`
- `artifacts/screenshots/功能测试回归测试/01-登录流程/`

脚本名与截图目录名保持一致，方便报告、缺陷复现、复测三方对齐。
