# Repository Guidelines

## 项目结构与模块组织

- `checkin.py`：入口脚本（读取环境变量/`.env`，执行签到逻辑）。
- `utils/`：通用工具模块（配置解析、Provider 配置、通知发送）。
  - `utils/config.py`：账号 / provider 配置解析。
  - `utils/notify.py`：通知渠道封装（邮件、PushPlus、钉钉、飞书、企业微信、Gotify 等）。
- `tests/`：单元测试（`pytest`）。
- `assets/`：README 用到的图片等静态资源。
- `.github/workflows/checkin.yml`：GitHub Actions 定时/手动执行工作流。

## 构建、测试与本地开发命令

本项目 使用 `uv` 管理依赖（Python `>=3.11`），本地开发 推荐在 干净虚拟环境 中执行：

```bash
uv sync --dev                      # 安装依赖（含开发依赖）
uv run playwright install chromium  # 安装 Playwright 浏览器（首次必需）
uv run checkin.py                   # 本地运行签到脚本
uv run pytest tests/                # 运行测试
uv run ruff check .                 # 代码检查（lint）
uv run ruff format .                # 代码格式化（formatter）
pre-commit install                  # 安装 Git hooks（推荐）
pre-commit run -a                   # 手动运行全部 hooks
```

## 代码风格与命名约定

- 统一使用 `ruff` 进行检查与格式化；配置位于 `pyproject.toml`。
- 行宽：`120`；引号：单引号；缩进：制表符（tab）。
- 测试文件命名：`tests/test_*.py`；测试函数：`test_*`。
- 新增通知 / Provider 时，优先复用 `utils/notify.py` 与 README 中的环境变量命名（全大写 + 下划线）。

## 测试指南

- 测试框架：`pytest`（可使用 `pytest-mock` / `unittest.mock`）。
- 默认以 mock 为主；如需真实通知接口测试，需在本地环境中显式开启（见 `tests/test_notify.py` 中的 `ENABLE_REAL_TEST` 逻辑）。
- 如测试依赖 Playwright，请先执行 `uv run playwright install chromium`，避免 CI/本地环境差异。

## Commit 与 Pull Request 规范

- 提交信息建议遵循 Conventional Commits 风格：`feat: ...`、`fix: ...`、`docs: ...`、`chore: ...`（仓库历史中已采用）。
- PR 要求：
  - 描述清楚变更动机与影响范围；附上本地验证命令/输出（如 `uv run pytest tests/`）。
  - 若涉及配置/环境变量，务必同步更新 `README.md` 与 `.env.example`。
  - 若修改了工作流（`.github/workflows/checkin.yml`），请说明触发条件变化（schedule/inputs/secrets）。
  - 严禁提交真实 Cookies、Token、邮箱密码等敏感信息；本地请基于 `.env.example` 配置（`.env` 默认应保持在本地，不进入版本库）。

## 安全与配置提示

- GitHub Actions 依赖 `production` Environment Secrets（例如 `ANYROUTER_ACCOUNTS`、`PROVIDERS` 等）；提交前 确认不含任何可用凭证，必要时对示例值进行脱敏。
- 本地调试 推荐流程：复制 `.env.example` → 填入 仅用于测试 的假数据 → 运行 `uv run checkin.py`；不要在 Issue/PR 中粘贴完整 cookies。
