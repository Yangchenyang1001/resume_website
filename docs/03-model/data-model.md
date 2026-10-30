# 数据与页面要素 · 个人简历站

| 项 | 内容 |
|---|---|
| 版本 | v0.2 |
| 日期 | 2026-10-30 |
| 状态 | 待审查 |
| 上游 | `02-design/architecture.md` v0.8（已冻结）、`01-requirements/requirements-spec.md` v0.3（已冻结） |
| 配套 | `03-model/prototype.md` v0.7 |
| 本文档是什么 | **页面要素清单**：页面出现哪些实体、每个实体有哪些要素、要素落在哪个区块 |
| 本文档不是什么 | 不是库表设计。本站无服务端、无数据库、无写接口，**不存在表结构、外键、事务、并发、索引**；也没有第二个人写数据，所以不需要字段级强约束来防脏数据。写作要求（字数、段数、语气）由 `01-requirements/requirements-spec.md` 规定（你指定的口径），本文档不复制成字段约束 |
| 冻结条件 | 每个实体有要素表、要素都能在页面找到落点、影响渲染的规则写明、无孤儿要素 |

判据写在这里，避免以后反复争：**有持久化层（后端 / 数据库 / 接口）时，数据文档才需要「可校验约束」**——那是给 CI 和多人协作兜底的。本站数据只有一个人用 GitHub 网页端改，错了看一眼页面就知道，再加一层字数/正则校验只会变成维护负担。所以本文档只回答三件事：有哪些实体、每个实体有哪些要素、要素显示在哪。

## 1. 实体清单

| # | 实体 | 一句话职责 | 数据位置 | 页面落点 | 本期 |
|---|---|---|---|---|---|
| E1 | Site | 站点级配置：标题、定位句、技术标签、资源前缀 | `data/profile.json` 的 `site` 对象 | 全站 | 是 |
| E2 | Profile | 站主本人：姓名 + 联系方式容器 | `data/profile.json` 顶层 | 首屏 / 页脚 | 是 |
| E3 | ContactItem | 一条联系方式 | `data/profile.json` 的 `contacts` 数组 | A1 / A4 / F1 | 是 |
| E4 | ExperienceItem | 一条工作经历 | `data/experience.json` 数组元素 | A3 | 是 |
| E5 | Project | 一个可展示项目（卡片数据 + 详情页开关） | `data/projects.json` 数组元素 | A2 / B1 | 是 |
| E6 | Metric | 量化表的一行 | `data/projects.json` 内嵌于 E5 的 `metrics` | B5 | 是 |
| E7 | ProjectDoc | 一个项目的详情长文（七段） | `content/projects/<slug>.md` | B2–B8 | 是 |
| E8 | StatsSnapshot | 访问统计快照 | 无（P2 预留） | — | 否 |
| E9 | NavItem | 导航项 | 无，模板内固定结构 | N1 | 否 |

E1–E7 是本期全部数据对象。**没有用户、没有权限、没有评论**——无服务端，无任何写接口。

## 2. 要素

口径：要素名 `camelCase`；`null` 表示「这一项不写」，页面按 §6 的规则隐藏对应片段，不用空字符串假装有值。

### 2.1 E1 Site

| 要素 | 类型 | 示例 | 说明 |
|---|---|---|---|
| `title` | string | `张三 · 后端工程师` | 浏览器标签、PDF 页眉、N1 站名 |
| `tagline` | string | `写 RAG 与检索，喜欢把慢的地方变快` | 一句话定位，全站只出现在 A1 一处 |
| `techTag` | string | `RAG` | 唯一技术标签，与卡片标签共同构成命名锚点 |
| `lang` | string | `zh-CN` | `<html lang>` |
| `baseUrl` | string | `/` 或 `/resume_website/` | 资源与链接前缀 |
| `noindex` | boolean | `true` | 构建据此输出 `<meta name="robots">` |

### 2.2 E2 Profile

| 要素 | 类型 | 示例 | 说明 |
|---|---|---|---|
| `site` | object | — | 内嵌 E1 |
| `name` | string | `张三` | A1、F1 |
| `contacts` | ContactItem[] | — | 内嵌 E3，至少 1 条（没有联系方式的首屏没有意义） |

### 2.3 E3 ContactItem

| 要素 | 类型 | 示例 | 说明 |
|---|---|---|---|
| `type` | string | `github` | 取值见 §3.1，决定是否可点、打印时是否打印 URL |
| `text` | string | `zhangsan` | 展示文本 |
| `url` | string \| null | `https://github.com/zhangsan` | `wechat` 必须为 `null`（只有文本，点不了） |

### 2.4 E4 ExperienceItem

| 要素 | 类型 | 示例 | 说明 |
|---|---|---|---|
| `company` | string | `某某科技` | 公司名 |
| `role` | string | `后端工程师` | 角色 |
| `start` | string | `2023-04` | 起始年月，`YYYY-MM` |
| `end` | string \| null | `null` | `null` 表示至今 |
| `summary` | string | `负责检索链路…` | 职责描述（字数口径见 spec §4.3，本文档不设限） |
| `stack` | string[] | `["Python","Milvus"]` | 技术栈；**没有链接字段**，内部项目不会被挂外部链接 |

### 2.5 E5 Project

| 要素 | 类型 | 示例 | 说明 |
|---|---|---|---|
| `slug` | string | `rag` | 决定路由 `/projects/<slug>/` 与 md 文件名 |
| `title` | string | `企业知识库 RAG` | 卡片标题 + 详情页标题 |
| `role` | string | `检索链路负责人` | 卡片副信息 + B1 |
| `stack` | string[] | `["Python","Milvus","LangChain"]` | 卡片标签行 |
| `highlight` | string \| null | `召回率 0.86` | 一条量化结果。**`null` = 草稿，不上首页、不生成详情页**（§5） |
| `metrics` | Metric[] | — | 内嵌 E6，仅详情页量化表用 |

字段取舍：**不放 `summary`**（卡片只有标题/角色/技术栈/量化结果/进入详情）；**不放任何 URL 字段**（仓库、录屏、一键启动都在 md 正文里），避免同一链接两处维护。

### 2.6 E6 Metric

| 要素 | 类型 | 示例 | 说明 |
|---|---|---|---|
| `key` | string | `ragasFaithfulness` | 取值见 §3.2，用于清单核对，不直接展示 |
| `label` | string | `RAGAS 忠实度` | 表格第一列文案，**必须来自数据**，不写死在模板里 |
| `value` | string | `0.86` / `180ms` / `0.021 元/次` | 数值与单位一起写，不单独成列 |
| `note` | string \| null | `样本 200 条` | 补充说明，可为空 |

### 2.7 E7 ProjectDoc

md 是内容不是字段，只有结构约定：

| 结构项 | 约定 |
|---|---|
| 文件名 | 等于某个 E5 的 `slug` + `.md` |
| 段标题 | `##` 二级标题，7 个，顺序见 §3.3 |
| 关键工程决策 | 每条要写清「选了什么、代价是什么」（写法见 spec §4.4-2） |
| 已知不足 | 至少覆盖架构取舍与真实缺陷（spec §4.4-3） |
| 图片 | `src/assets/` 相对路径或外链 |
| 链接 | `http(s)` 外链或站内 `/` 开头路径，不得 `javascript:` |
| HTML | 不写 `<div>` `<span>` `<style>` `<script>`，结构归模板 |

## 3. 枚举与内容清单

这些是**填数据时要照着写的清单**，所以列全；校验强度归 §6。

### 3.1 `contactType`

| 值 | 含义 | 可点 | 打印时打印 URL |
|---|---|---|---|
| `email` | 邮箱，`url` 用 `mailto:` | 是 | 否 |
| `github` | 代码主页 | 是 | 是 |
| `wechat` | 微信号，只显示文本 | 否 | 否 |
| `zhihu` | 知乎主页 | 是 | 是 |
| `juejin` | 掘金主页 | 是 | 是 |
| `other` | 其他站点 | 是 | 是 |

### 3.2 `metricKey`（详情页量化表的建议项）

| 值 | 含义 |
|---|---|
| `ragasFaithfulness` | RAGAS 忠实度 |
| `ragasAnswerRelevancy` | RAGAS 答案相关性 |
| `p95RetrievalMs` | 检索环节 P95 延迟 |
| `p95GenerationMs` | 生成环节 P95 延迟 |
| `tokenCostPerQa` | 单次问答 token 成本 |
| `docCount` | 入库文档数 |
| `chunkCount` | 切分后 chunk 数 |

七项是「有就都给」的建议清单，不是必填：缺项无所谓，页面只渲染实际有的行。

### 3.3 段标题（E7 用，顺序固定）

1 背景与问题 · 2 架构图 · 3 关键工程决策 · 4 量化结果 · 5 已知不足 · 6 复现方式 · 7 演示录屏

七段来自 spec §4.4，允许文本微调，不允许增删段——详情页七个区块与它一一对应。

### 3.4 路由常量

| 常量 | 值 |
|---|---|
| 首页 | `<baseUrl>` 下的 `index.html` |
| 详情页 | `projects/<slug>/index.html` |
| 404 | `404.html` |
| robots | `robots.txt`，内容固定 `Disallow: /` |

## 4. 关系

| # | 关系 | 基数 | 承载方式 |
|---|---|---|---|
| R1 | Profile → Site | 1:1 | 内嵌 `site` |
| R2 | Profile → ContactItem | 1:N | 内嵌 `contacts` |
| R3 | Project → Metric | 1:N | 内嵌 `metrics` |
| R4 | Project → ProjectDoc | 1:1 | 跨文件，靠 `slug` 对应，不需要外键 |
| R5 | ExperienceItem | 独立集合 | `data/experience.json` |
| R6 | Project ↔ ExperienceItem | 无 | 项目卡片不关联公司，避免内部项目被迫挂公司信息 |

## 5. 可见性规则

项目是否出现在页面上，由 `highlight` 一个要素决定，不额外存 `draft/published` 开关：

| 条件 | 首页卡片 | 详情页 |
|---|---|---|
| `highlight` 有值 | 出现 | 生成 |
| `highlight` 为 `null` | 不出现 | 不生成 |
| `highlight` 有值但 md 缺失 | 出现（指向 404） | 不生成，构建摘要提示「缺 md」 |

配套两条约定：

- **下线 = 置 `highlight` 为 `null`**，md 保留，便于改好再上线。
- **孤儿 md（有文件无项目）只提示**，不阻断构建——下线后保留 md 是正常操作，不该让构建失败。

## 6. 页面级规则（影响渲染，已由我方定）

这些是页面设计决定，直接写在数据文档里，避免散落：

| # | 规则 | 理由 |
|---|---|---|
| P-1 | 项目卡片**不设数量上限**，有几个渲染几个（当前 3 个） | 设上限就会有静默丢弃；第 4 个项目只是多一张卡 |
| P-2 | 首页不产出空态画板 | 内容由本人维护，空态不会出现在交付里；错链由 404 兜 |
| P-3 | 详情页只出 `detail-pc.pen`，窄屏按「单列、去掉并排」的规则响应式，不再出移动端画板 | 详情页是长文单列，移动端差异只有并排折行，D 阶段用 CSS 处理 |
| P-4 | 量化表行序 = `metrics` 数组顺序 | 谁写谁排，不引入排序规则，避免「枚举序 vs 数据序」两套口径 |
| P-5 | 数据不合规**只提示不阻断**（缺字段、字数不符、段数不足），唯一例外是 `site.title` / `site.baseUrl` 缺失 | 单人维护的内容站，构建失败会挡住「先发出去再改」；站点级字段缺了页面就不可用，必须拦 |

## 7. 要素 → 区块映射

区块号见 `03-model/prototype.md` §3、§4。

| 要素 | 落点区块 |
|---|---|
| `site.title` | 全站 `<title>`、PDF 页眉、N1 |
| `site.tagline` | 仅 A1 |
| `site.techTag` | A1 标签 |
| `site.lang` / `site.baseUrl` / `site.noindex` | 不展示，进 `<html lang>` / 链接前缀 / robots meta |
| `profile.name` | A1、F1 |
| `contacts[].type` / `.text` / `.url` | A1 联系方式行、A4、F1 |
| `experience[].company` / `.role` / `.start` / `.end` | A3 每条头部 |
| `experience[].summary` / `.stack` | A3 每条正文 / 技术栈行 |
| `project.title` / `.role` / `.stack` / `.highlight` | A2 每张卡片 |
| `project.slug` | A2 卡片链接目标、路由 `/projects/<slug>/` |
| `project.title` / `.role` / `.stack` | B1 标题头 |
| `metrics[].label` / `.value` / `.note` | B5 表格 |
| E7 第 1–7 段 | B2、B3、B4、B5 正文补充、B6、B7、B8 |
| （无数据） | C1 / C2 固定字符串，可写在模板里 |

一要素多落点的只有三处：`site.title`（`<title>`、N1、F1）、`contacts`（A1、A4、F1）、`project.title`（A2、B1）。

## 8. 遗留问题

只列会影响后续实施成本的，其余自决项见 §6 与 `03-model/review-c.md`。

- [素材] `metrics` 真实数值未采集，量化表可能只有 2–3 行。表会照常渲染，但详情页的说服力依赖这些数字（发现于 §3.2）
- [素材] B8 录屏链接未就绪，届时只能写文字说明（发现于 §2.7）
- [D] 图片路径如何加 `baseUrl` 前缀，D 阶段定改写规则（发现于 §2.7）
- [验证] 375×812 下首页容纳 3 张卡 + 4 行文本不滚动，需真机确认；不过的话减 A1 文案而不是减卡片（发现于 §6 P-1）

## 9. 变更记录

| 版本 | 日期 | 变更 | 依据 |
|---|---|---|---|
| v0.1 | 2026-10-30 | 初稿：9 个实体、7 张字段表（含必填/约束/默认值）、5 组枚举、6 条关系、状态机与生命周期、要素映射 | 架构 v0.7 + 需求 v0.2 |
| v0.2 | 2026-10-30 | 定位从「字段唯一事实源 + 可校验约束」改为「页面要素清单」：删除全部长度区间、正则、必填、默认值、数组排序、唯一性与双向强制的构建失败规则；字段表改为「要素 / 类型 / 示例 / 说明」；状态机压缩为 §5 三条可见性规则；新增 §6 页面级规则（卡片不限量、无空态画板、只出 pc 详情页、行序随数组、违规只提示）；§8 遗留从 6 条压到 4 条 | 用户 2026-10-30 指令：无后端无数据库，数据文档只需看页面有哪些实体和要素，不要过多约束；页面设计由我方决定 |
