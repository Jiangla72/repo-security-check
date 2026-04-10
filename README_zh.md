# Repo Security Check

这是一个面向 Codex 的通用安全检查 skill，用于扫描 git 仓库中的常见敏感信息模式，并可选地为目标仓库安装本地 `pre-commit` hook，在提交前自动阻止可疑泄露。

## 这个 skill 能做什么

- 扫描仓库中的常见敏感信息模式，例如 API key、token、password、authorization header、private key 标记等
- 支持全仓扫描，也支持只扫描 staged 文件
- 可以为目标仓库安装本地 `pre-commit` hook`
- 在发现疑似风险时给出文件和行号，帮助快速定位问题
- 在扫描通过时给出“未发现明显 secrets”的结论，但不会夸大为绝对安全

## 仓库结构

- `SKILL.md`：skill 主说明
- `scripts/security_check.py`：通用扫描脚本，同时支持安装 hook
- `references/secret-scan-checklist.md`：当自动扫描不足以建立信心时的补充检查清单
- `agents/openai.yaml`：Codex skill 元数据

## 环境要求

- Windows 环境
- Python 3.10+
- 如果要扫描 staged 文件或安装 hook，目标目录需要是一个 git 仓库

## 基本使用方式

扫描整个仓库：

```powershell
python ".\scripts\security_check.py" --repo "D:\path\to\repo"
```

只扫描 staged 文件：

```powershell
python ".\scripts\security_check.py" --repo "D:\path\to\repo" --staged-only
```

为目标仓库安装本地提交前检查：

```powershell
python ".\scripts\security_check.py" --repo "D:\path\to\repo" --install-hook
```

## 适合的使用场景

- 你准备提交代码，想先确认 staged 文件里没有误带 token 或密码
- 你想对一个仓库做一次快速敏感信息排查
- 你希望某个仓库以后每次提交前都自动做基础安全检查
- 你怀疑某次脚本导出、日志、配置文件可能混入了认证信息

## 输出结果如何理解

如果扫描通过，通常代表：

- 在当前扫描范围内，没有命中明显的敏感信息模式
- 这是一种“基础通过”，不是绝对安全证明

如果扫描命中，通常需要进一步判断：

- 是真实 secret，还是示例文档/占位符
- 是否只是工作区里出现，还是已经 staged / committed
- 如果已经提交到历史里，除了删除当前文件，还需要考虑轮换密钥和清理 git 历史

## 注意事项

- 这是基于模式匹配的快速扫描，不等于完整安全审计
- 本地 `pre-commit` hook 只对安装了它的仓库生效
- 如果发现真实 secret，不要只删除文件；还应尽快轮换凭据
- 对于压缩包、二进制文件、特殊编码文件，这个脚本的覆盖能力有限，必要时应做补充人工检查
