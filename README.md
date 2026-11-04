# 新媒体运营工作台

个人媒体运营管理工具。

GitHub Pages: https://qianshan797.github.io/personal-improving-workbench/

## 文件结构说明

原 `media-workflow-tool.html` 已合并入 `index.html`，仓库根目录自 2026-09-14 起仅维护单一入口文件：

- `index.html` — 工作台主文件（合并了原 media-workflow-tool 的全部功能）
- `news-data.json` — 每日快讯静态数据（手动维护，浏览器加载时若比本地新则覆盖；真正的多端同步依赖 Supabase）
- `.nojekyll` — 禁用 Jekyll，让 GitHub Pages 直接托管根目录文件
- `.github/workflows/pages.yml` — GitHub Pages 部署工作流（push 到 main 自动触发，不含定时任务）
- `.github/workflows/update-news.yml` — 每日快讯定时更新工作流（北京时间 09:00 自动从 Supabase 拉取最新快讯，写入 news-data.json 并提交，触发 Pages 部署）
- `README.md` — 本文件

## 数据同步机制

- **Supabase 云同步**：账号、粉丝数据、任务、素材、每日快讯、删除记录等全部通过 `index.html` 内的 Supabase 客户端在浏览器端同步。
- **同步策略**：
  - 页面加载 → `silentPull` 拉取云端 → 与本地智能合并（本地修改优先，云端补齐缺失）→ 合并结果自动 push 回云端，保证多端数据一致。
  - 账号 ID 重映射：合并后按 `name|platform` 关联 `account_data`，避免历史数据因 ID 不匹配而丢失。
- **每日快讯**：由 TRAE 定时任务（北京时间 08:30）抓取新闻并更新 Supabase 云端；GitHub Actions（北京时间 09:00）从 Supabase 拉取最新快讯写入 `news-data.json` 并提交，触发 Pages 部署。浏览器加载时若 `news-data.json` 比本地新则覆盖。

## 后续维护

所有功能迭代、Bug 修复、数据同步逻辑变更均只改动 `index.html`；`news-data.json` 由 GitHub Actions 自动维护，无需手动提交。
