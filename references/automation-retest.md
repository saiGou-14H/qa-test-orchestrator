# Automation Retest

## Purpose

把复测能力沉淀到被测项目自身，并与截图、报告共用同一套动态责任人/业务域 taxonomy。

不要再把脚本沉淀到固定岗位目录下。每个 Python 复测脚本都应明确属于某个真实责任人和某个真实业务域。

## Directory Rules

```text
artifacts/
  automation/
    管理员（教务）/
      登录与权限/
        01-登录与首页校验.py
      课表与课程/
        01-课表导入回归.py
        02-调课回归.py
    班主任/
      办班申请与审批/
        01-提交办班申请.py
    学员端（移动端）/
      评价问卷/
        01-提交评价问卷.py
```

规则：

- 脚本路径固定为 `artifacts/automation/<责任人>/<业务域>/`
- 一个脚本只负责一条清晰链路，不做大而全脚本。
- 脚本文件名只写链路名，不重复完整责任人和业务域。
- 同一业务域下如果有多条链路，按 `01-`, `02-` 拆分多个脚本。
- 同一业务域出现在不同责任人下时，各自沉淀在各自目录。

## Script Contract

每个脚本至少要说明：

- 责任人/角色
- 业务域
- 链路名称
- 前置账号或数据
- 成功判定
- 正式截图目录
- 调试截图目录

推荐在脚本头部保留类似注释：

```python
"""
Role: 管理员（教务）
Domain: 课表与课程
Flow: 课表导入回归
Screenshot Dir: artifacts/screenshots/管理员（教务）/课表与课程
Debug Dir: artifacts/screenshots/_debug/管理员（教务）/课表与课程
"""
```

## Binding With Screenshots

脚本与截图共享同一 taxonomy：

- 正式截图：`artifacts/screenshots/<责任人>/<业务域>/`
- 调试截图：`artifacts/screenshots/_debug/<责任人>/<业务域>/`
- 复测脚本：`artifacts/automation/<责任人>/<业务域>/`

示例：

- `artifacts/automation/管理员（教务）/课表与课程/01-课表导入回归.py`
- `artifacts/screenshots/管理员（教务）/课表与课程/01-打开课表管理弹窗.png`
- `artifacts/screenshots/_debug/管理员（教务）/课表与课程/01-定位导入入口-探针.png`

## Repeatability Rules

- 默认不要写死一次性数据。
- 如需创建数据，使用项目可回收命名规则，例如时间戳或 `qa_` 前缀。
- 账号创建、数据初始化、登录准备应尽量复用公共能力。
- 失败时至少保留一张正式截图或一组 `_debug` 诊断图，不能只留下口头结论。

## When To Split More

出现下面情况时继续拆脚本，不要把不同链路揉进一个脚本：

- 同一业务域里存在新增、编辑、审批、导入、发布等不同主链路
- 同一业务域里既有成功链路，也有驳回/回退/异常链路
- 同一链路需要独立复测或单独挂到 CI
