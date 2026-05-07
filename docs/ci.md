# CI/CD 扫描任务驱动（CI Scan Driver）

## 目的

用于在 CI/CD 中以“独立入口”驱动 KunLun-M 扫描，并生成稳定的 JSON 报告文件，同时按阈值返回明确的退出码以实现门禁。

该入口不会改变现有 `python kunlun.py scan ...` 的默认行为。

## 快速开始

安装依赖：

```bash
python -m pip install -r requirements.txt
```

运行扫描（生成报告文件，按阈值失败）：

```bash
python tools/ci_scan.py --target . --output artifacts/kunlun-ci.json --fail-on high
```

## 参数

- `--target`：扫描目标路径（默认 `.`）
- `--output`：报告输出路径（默认 `artifacts/kunlun-ci.json`）
- `--fail-on`：失败阈值：`none|low|medium|high|critical`（默认读取环境变量 `KUNLUN_FAIL_ON`，否则 `none`）
- `--include-unconfirm`：是否包含未确认漏洞（默认读取 `KUNLUN_INCLUDE_UNCONFIRM`）
- `--with-vendor` / `--without-vendor`：是否启用 SCA（依赖漏洞）扫描（默认读取 `KUNLUN_WITH_VENDOR`，默认关闭）
- `--rule`：指定规则 ID（逗号分隔），例如 `1000,1001`
- `--language`：指定语言（逗号分隔）
- `--blackpath`：黑名单路径（逗号分隔）
- `--tamper`：拓展插件（tamper）名称
- `--unprecom`：关闭预编译（更快但能力下降）
- `--settings-module`：CI 专用 settings（默认 `Kunlun_M.settings_ci`）

## 环境变量

- `KUNLUN_FAIL_ON`：同 `--fail-on`
- `KUNLUN_INCLUDE_UNCONFIRM`：`0/1`
- `KUNLUN_WITH_VENDOR`：`0/1`

## 退出码

- `0`：扫描成功，且未触发阈值
- `1`：扫描过程异常/初始化失败（同时会写出包含 error 的 JSON）
- `2`：扫描成功，但触发阈值（用于 CI 门禁）

## CI 配置示例

- GitHub Actions：见 [.github/workflows/kunlun-scan.yml](file:///d:/program/Kunlun_M/.github/workflows/kunlun-scan.yml)
- GitLab CI：见 [.gitlab-ci.yml](file:///d:/program/Kunlun_M/.gitlab-ci.yml)
- Jenkins：见 [Jenkinsfile](file:///d:/program/Kunlun_M/Jenkinsfile)

