# C 阶段交接单 · 下一阶段（D）开工指南

| 项 | 内容 |
|---|---|
| 用途 | 关闭 C 阶段会话前的事实快照。新会话**只读这份 + §5 列出的行段**即可开工，不必重读全仓库 |
| 日期 | 2026-10-30 |
| 仓库 | `c:/Users/43779/Desktop/AIlearnrecord/resume_website`，分支 `dev`，HEAD `b5074b4`（已推 `origin/dev`） |
| 上游状态 | `docs/01-requirements/requirements-spec.md` v0.3 **已冻结**、`docs/02-design/architecture.md` v0.8 **已冻结** |
| 本阶段状态 | C 三份文档均「待审查」，等你一句话即可标记「已冻结」；内容已定稿，无待裁决项 |
| 下一阶段产出 | `docs/04-spec/spec.md`（路径由 `skills/spec-driven-dev/SKILL.md` 阶段表 D 行规定） |

## 1. C 阶段产出了什么

| 文件 | 版本 | 作用 | 状态 |
|---|---|---|---|
| `docs/03-model/data-model.md` | v0.2 | 实体 + 要素清单 + 可见性 + 页面级规则 + 要素→区块映射 | 定稿 |
| `docs/03-model/prototype.md` | v0.8 | 4 页面 / 5 画板、逐页线框、区块说明（含空态错态）、交互、打印；§1.1 明确 `.pen` 为前端实现依据 | 定稿 |
| `docs/03-model/review-c.md` | v0.3 | 10 条审查意见的自决留档 + 4 个裁决点 | 定稿 |
| `docs/03-model/pen/*.pen` | — | `home-pc` / `home-mobile` / `detail-pc` / `404-mobile` / `print-home-pc` 五张画板 | 定稿 |
| `docs/03-model/prototype.frames.json` | — | 画板数据源，改线框后跑 `pen-gen.mjs` 重生成 | 定稿 |

## 2. 已定型、D 阶段不要再议的事

| # | 结论 | 出处 |
|---|---|---|
| 1 | 数据文档只做「实体 + 要素 + 落点」，**不做库表级约束**（无后端/无数据库） | `docs/03-model/data-model.md` 头部表格（第 1–14 行「本文档是什么/不是什么」）+ `skills/spec-driven-dev/SKILL.md` R13 |
| 2 | 数据不合规只提示不阻断，仅 `site.title` / `site.baseUrl` 缺失才拦 | `docs/03-model/data-model.md` §6 P-5 |
| 3 | 项目卡片**不设数量上限**，有几个渲染几个 | `docs/03-model/data-model.md` §6 P-1 |
| 4 | 首页不出空态画板；详情页只出 pc 画板，窄屏靠 CSS 单列 | `docs/03-model/data-model.md` §6 P-2、P-3 |
| 5 | 量化表行序 = `metrics` 数组顺序 | `docs/03-model/data-model.md` §6 P-4 |
| 6 | 项目上下线只靠 `highlight` 是否为 `null`，无 draft 字段 | `docs/03-model/data-model.md` §5 |
| 7 | `.pen` 命名 `<页面>[-<模块>]-<pc\|mobile>`，不写像素宽度，生成器校验形态与 `width` 一致 | `docs/03-model/prototype.md` §1 |
| 8 | C 阶段不产出视觉，视觉方向（TBD-4）**在 D 定稿** | `docs/01-requirements/requirements-spec.md` §8 TBD-4 行 |
| 9 | 错误态三档：不渲染 / 占位文本 / 仅提示 | `docs/03-model/prototype.md` §4 开头 |
| 10 | **`.pen` 画板是前端实现的依据**，与最终样式风格无关：区块有无、顺序、层级、字段落点以画板为准；换视觉不重画画板，只有结构必须变时才改 `prototype.frames.json` 重生成 | `docs/03-model/prototype.md` §1.1 |

## 3. 遗留（D 阶段要接的）

| # | 事 | 阻塞 D 吗 | 处理 |
|---|---|---|---|
| 1 | TBD-4 视觉方向三选一（纸面文档感 / 终端冷峻感 / 手作注记感） | **阻塞**（本次会话唯一必须你拍板的） | D 第一步出 3 个方案给你选，选定后写进 spec，不单独开文档 |
| 2 | `metrics` 真实数值未采集 | 不阻塞 | spec 里按「行数随数据」写，不写死 7 行 |
| 3 | 详情页录屏链接未就绪 | 不阻塞 | spec 里 B8 允许文字说明，不设机器校验 |
| 4 | 375×812 下首屏 3 入口不滚动未经真机确认 | 不阻塞 | 写进 spec 的 DoD 待验项，不够就减文案 |
| 5 | 图片路径 `baseUrl` 前缀改写规则 | 不阻塞 | 在 spec 的构建模块里定死一条规则 |

## 4. D 阶段要生成什么

**唯一必出文件**：`docs/04-spec/spec.md`，按 `skills/spec-driven-dev/references/phase-d-spec.md` 的 13 节模板，v0.1 起。硬性要求（同文件 §写作要求）：规则可判定、边界写清、错误有语义、**禁止重复论证与抄写**，引用一律写成「见 某文件 §x.y」。

建议的模块划分（对齐 `docs/02-design/architecture.md` §7 目录结构，spec §4/§5 按此写）：

| 模块 | 职责 | 主要输入输出 |
|---|---|---|
| M1 构建流水线 | 读 `data/*.json` + `content/projects/*.md` → 校验（只提示）→ 渲染 → 写 `dist/` | 输入：数据文件；输出：3 类 HTML + `robots.txt` |
| M2 首页渲染 | 首屏 / 卡片 / 时间线 / 联系方式四区块 | 输入：`profile.json`、`projects.json`、`experience.json` |
| M3 详情页渲染 | md 七段 → 八个区块，图片路径加前缀 | 输入：`projects/<slug>.md` + 该项目 `metrics` |
| M4 打印与 404 | 打印样式表、404 兜底、`baseUrl` 改写 | 输入：首页 DOM + 样式 |
| M5 部署 | `dev` → `main` → `prod` 三支、Pages 路径 | 输入：`dist/` |

spec 必须覆盖的判定点（C 阶段定义、D 阶段要给判据）：卡片数量与排序、`highlight` 为 null 的过滤、md 缺失时的行为、图片缺失时的行为、打印时外链是否展开 URL、无 JS 下的行为。

**禁止**：把 `data-model.md` 的要素表或 `prototype.md` 的区块表抄进 spec，只写新增的判据与边界。

**实现依据的指向**：spec 里凡写「按原型实现」的地方，一律指 `docs/03-model/pen/*.pen` 画板（区块与结构），不指 D 阶段的视觉稿。视觉稿只决定样式，不改结构；如需改结构，先改 `prototype.md` + `prototype.frames.json` 重生成画板，再动代码（`docs/03-model/prototype.md` §1.1）。

## 5. 阅读地图（按此读，约 480 行，别多读）

按顺序读，读完就够开工：

| 顺序 | 文件 | 行段 | 读它干什么 |
|---|---|---|---|
| 1 | `docs/01-requirements/requirements-spec.md` | 76–135 | P0 全部功能项（4.1 首屏、4.2 卡片、4.3 时间线、4.4 详情页、4.5 内容分离、4.6 打印、4.7 部署），D 要逐条落成可验收规则 |
| 2 | 同上 | 58–75 | §4.0 页面与路由表 |
| 3 | 同上 | 167–191 | §5 非功能需求 + §6 约束（性能、无 JS、体积），spec 的硬约束来源 |
| 4 | 同上 | 203–227 | §8 TBD（点名 TBD-4 要 D 定稿）+ §9 素材清单 |
| 5 | `docs/02-design/architecture.md` | 37–142 | §2 技术架构、§3 依赖、§4 构建主链路（spec 的 M1 边界来自这里） |
| 6 | 同上 | 162–216 | §6 开发规范 + §7 目录结构 |
| 7 | `docs/03-model/data-model.md` | 32–214 | §2 要素、§3 枚举、§4 关系、§5 可见性、§6 页面规则、§7 映射（写 spec §6 只引用，不复制） |
| 8 | `docs/03-model/prototype.md` | 17–56 | §1 页面清单 + `.pen` 命名规则 + §1.1 画板与视觉的分工（**开发依据在画板**） |
| 9 | 同上 | 79–288 | §3 线框、§4 区块说明（空态错态）、§5 交互与打印、§6 遗留 |
| 10 | `docs/03-model/review-c.md` | 12–47 + 62–70 | §1 十条自决（防重复讨论）、§2 素材项、裁决点 4 |

按需读（只在写对应小节时打开）：

| 文件 | 行段 | 何时需要 |
|---|---|---|
| `docs/01-requirements/requirements-spec.md` | 13–55 | 写 spec §1 目标 / §2 非目标时 |
| `docs/01-requirements/requirements-spec.md` | 192–214 | 需要 ADR 编号与 TBD 全文时 |
| `docs/02-design/architecture.md` | 11–36 | 写 spec §4 模块依赖方向时 |
| `docs/02-design/architecture.md` | 221–234 | 写 spec §12 遗留 / 风险时 |
| `docs/03-model/pen/<画板>.pen` | 全文 | 只在线框与画板冲突、需要核对细节时（单文件 400–700 行，能不看就不看） |

## 6. 明确不要读

| 文件 | 原因 |
|---|---|
| `docs/01-requirements/requirement_collection.md` | A 阶段原始素材，结论已进需求文档 |
| `docs/01-requirements/prompt.md` | 提问清单 |
| `docs/02-design/learning-map.md`、`docs/02-design/review-b.md` | 学习与审查过程记录，实现不依赖 |
| 各文档的「变更记录」节（§11 / §9 / §7） | 只在改文档时追加，读它不产生决策 |
| `docs/03-model/pen/*.pen` | 除非确需核对布局，结构信息在 `prototype.md` §3 已有 |

## 7. 新会话开场可直接粘贴

```
按 skills/spec-driven-dev 的 D 阶段流程做 spec。

上下文：先读 docs/03-model/handoff-to-d.md 全文，再按它 §5 的阅读地图读指定行段，
不要读 §6 列出的文件。

上游已冻结：docs/01-requirements/requirements-spec.md v0.3、docs/02-design/architecture.md v0.8。
C 阶段定稿：data-model v0.2、prototype v0.7、review-c v0.3。

要求：
1. 第一步先给 TBD-4 三个视觉方向的差异说明让我选，同时给 docs/04-spec/spec.md 的章节骨架，先别写正文。
2. 按 skills/spec-driven-dev/SKILL.md R12 分级：能自决的直接定并留档，只有需要素材或后期影响大的才问我。
3. spec 只写判据与边界，字段和区块一律引用 03-model，不复制。
```

## 8. 交接自检

新会话读完本文后应当能回答以下五问，答不对就是没读进去：

1. 这个项目有没有数据库、要不要给数据加校验？（答：没有，不加，只提示）
2. 项目怎么下线？（答：`highlight` 置 `null`，md 保留，构建只提示）
3. 视觉方向在哪定？（答：D 阶段，spec 里定稿，C 阶段不做）
4. 详情页有几个区块、内容从哪来？（答：B1–B8 八个，数据来自 E5/E6 与 `content/projects/<slug>.md` 七段）
5. 卡片数量上限是多少？（答：没有上限）

## 9. 变更记录

| 版本 | 日期 | 变更 | 依据 |
|---|---|---|---|
| v0.1 | 2026-10-30 | 初稿：C 阶段产出与状态快照、已定型 9 条、遗留 5 项、D 阶段产出与模块划分、按行段的阅读地图与勿读清单、开场提示词、自检五问 | 用户 2026-10-30 指令：C 阶段收尾，生成总结文档标注 D 阶段 spec 与需引用的文件内容，以节约 token |
| v0.2 | 2026-10-30 | §2 增第 10 条「`.pen` 是前端实现依据、与视觉风格无关」；§4 补「按原型实现」的唯一指向；§5 阅读地图第 8、9 行行号按 prototype v0.8 重算（17–56、79–288） | 用户 2026-10-30 指令：无论后续页面样式风格如何，`.pen` 是后续前端开发的依据 |
