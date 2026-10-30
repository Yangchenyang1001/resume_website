# 个人简历站

杨晨阳 · Agent 工程方向。静态站点，零服务端，推送到 `main` 即自动发布。

> 一句话定位：把 RAG 从 Demo 做到能上线。

## 项目状态

| 阶段 | 状态 |
|---|---|
| A 需求规格 | 已冻结（`docs/01-requirements/requirements-spec.md` v0.2） |
| B 概要设计 | 进行中（`docs/02-design/`） |
| C 数据对象与原型 | 未开始 |
| D 实现与部署 | 未开始（当前仓库**没有可运行的构建脚本**） |

README 在此阶段只用于说明项目边界与文档索引，功能说明随 D 阶段补齐。

## 技术形态

| 层 | 选型 |
|---|---|
| 内容 | JSON（结构化数据）+ Markdown（长文） |
| 构建 | 自写 Node 脚本 + `markdown-it`（构建期唯一依赖） |
| 产物 | 静态 HTML / 单个 CSS 文件 / 单个 JS 文件 |
| 托管 | GitHub Pages（HTTPS + CDN，无需服务器） |
| 发布 | GitHub Actions，推送即发布 |

运行期零第三方依赖：浏览器只加载自己的 HTML、CSS、JS。

## 目录结构

```
.
├── data/              内容数据（JSON）
├── content/projects/  项目详情长文（Markdown）
├── src/
│   ├── templates/     页面模板（返回 HTML 字符串的函数）
│   ├── styles/        分层 CSS 源文件，构建时拼接为单文件
│   ├── scripts/       渐进增强脚本，构建时拼接为单文件
│   └── assets/        图片与图标
├── docs/              四阶段设计文档（不参与构建）
│   ├── 01-requirements/
│   └── 02-design/
└── dist/              构建产物（不入库）
```

`docs/` 是**设计过程**产物，不是项目源码：构建脚本只读 `data/`、`content/`、`src/`，不读 `docs/`（ADR-B3）。删掉整个 `docs/` 目录，站点仍能正常构建。

## 文档索引

| 文件 | 用途 |
|---|---|
| `docs/01-requirements/requirements-spec.md` | 需求规格，已冻结；改动须走变更请求 |
| `docs/02-design/architecture.md` | 架构、选型与代价、核心链路、人工介入点 |
| `docs/02-design/learning-map.md` | 需要学习的知识、需要介入的操作 |
| `docs/02-design/inputs.env` | 站点内容与素材的**设计输入**清单（key-value），值需转写进 `data/` 才生效 |

## 本地开发

依赖 Node.js 18+。构建脚本将在 D 阶段加入，当前仓库**没有**可执行入口：

```bash
# 尚不可用
# npm run build
```

## 部署

推送到 `main` 分支后，GitHub Actions 构建并把 `dist/` 发布到 GitHub Pages。

首次部署前需要在仓库 `Settings → Pages → Build and deployment → Source` 选择 **GitHub Actions**，且**不要**在模板列表里点 `Configure`，否则会与本仓库自己的流水线冲突。

## 注意

本仓库为 **public**，站点源码与 `data/` 内容均公开可见。禁止提交手机号、住址、在职公司内部信息、任何密钥。个人信息只写公开字段，先落在 `docs/02-design/inputs.env`，再转写进 `data/*.json`。
