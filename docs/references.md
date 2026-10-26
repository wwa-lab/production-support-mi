# 调研来源

状态：讨论稿的参考资料，尚未批准实施。

以下公开资料已在前期讨论中查阅。它们提供设计依据，不构成本项目性能、准确率或内部现状的证明。

| 原始来源 | 与本方案的关系 |
|---|---|
| [Karpathy：LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) | 持续维护派生 Wiki；原始资料、Wiki 和规则分层；ingest / query / lint。它是模式而非完整企业产品。 |
| [Google SRE：Results of Postmortem Analysis](https://sre.google/workbook/postmortem-analysis/) | 统一记录触发因素与根因，以支持跨事故趋势分析。 |
| [Google SRE：Postmortem Culture](https://sre.google/workbook/postmortem-culture/) | 基于事实的复盘、来源及可追踪的行动项。 |
| [Atlassian：Incident postmortems](https://www.atlassian.com/incident-management/handbook/postmortems) | 记录影响、原因、应对及后续行动，连接复盘与整改。 |
| [Microsoft Research：From Local to Global](https://www.microsoft.com/en-us/research/publication/from-local-to-global-a-graph-rag-approach-to-query-focused-summarization/) | 全局主题归纳与局部片段检索不同；不据此推断本项目必须使用 GraphRAG。 |

本项目增加结构化台账、确定性统计、人工确认和维护责任，是针对事故分析场景的方案建议，需用试点验证。

## 产品重审 v0.3 · 2026-10-25

本次重新查阅 [Google SRE：Postmortem Culture（SRE Book）](https://sre.google/sre-book/postmortem-culture/) 与上表的 Postmortem Analysis。前者强调复盘的影响、促成因素、预防措施、审核及避免归咎个人；后者展示统一记录触发因素与根因如何支持跨事故分析。

据此，本方案建议让管理层从数字下钻到影响、整改和证据，并将触发因素、原因与措施分开组织。统一事故档案、分级复核及先保持一个代码仓库，是结合当前范围与未知条件提出的项目设计判断，并非上述来源对架构的要求。尚未测量内部报告成本、数据质量或收益，也未据此批准实施。

## 管理层 MI Dashboard · GitHub 项目调研（2026-10-24）

目的：为 D-10 的虚构原型寻找可借鉴的交互与信息组织。以下是项目自有文档和局部源码的查阅结果，未安装、部署或执行这些项目，不能据此认定其生产质量、集成适配性或内部使用许可。本次仅整理建议，未修改原型、选型或新增已采纳决定。

### 优先参考项目

| 项目 | 已查到的能力与证据 | 对当前 MI 原型的借鉴建议 | 适用边界 |
|---|---|---|---|
| [Metabase](https://github.com/metabase/metabase) | [Dashboard interactivity](https://www.metabase.com/docs/latest/dashboards/interactive) 区分点击后下钻、跳转目标及更新 Dashboard 筛选；[Drill-through](https://www.metabase.com/docs/latest/questions/visualizations/drill-through) 说明按值筛选、查看组成记录、换维度细分及时间缩放 | 点击 KPI 或图表后，明确区分“筛选本页”“查看这些事故”“按另一维度细分”；保留查询上下文 | 通用 BI，不提供本项目的事故归并及 RCA 复核规则。SQL 问题与图形查询的下钻能力不同；[交互式嵌入文档](https://www.metabase.com/docs/latest/embedding/dashboard) 标为 Pro / Enterprise，不能因仓库公开而假定所有嵌入能力免费 |
| [Apache Superset](https://github.com/apache/superset) | README 描述维度／指标语义层；[DrillByModal.tsx](https://github.com/apache/superset/blob/master/superset-frontend/src/components/Chart/DrillBy/DrillByModal.tsx) 实现继续下钻、累积筛选、面包屑返回，以及图／表展示切换 | “9 月 → AMH → 批处理 → 调度依赖 → 事故明细”连续下钻，允许逐级返回；指标定义集中维护 | 通用分析平台，需先准备可信结构化数据；不因本次调研引入完整 BI 服务。README 标为 Apache-2.0，本次未复制代码 |
| [OpsKnight](https://github.com/opsknight-labs/OpsKnight) | [报告文档](https://opsknight.com/docs/v2.0.0/guides/reports/) 包含时间／团队／服务筛选、保留筛选的分享链接及打印；[SavedFilters 源码](https://github.com/opsknight-labs/OpsKnight/blob/main/src/components/DashboardSavedFilters.tsx) 保存命名筛选；[ActionItems 源码](https://github.com/opsknight-labs/OpsKnight/blob/main/src/components/DashboardActionItems.tsx) 展示状态、逾期、完成率、优先级及关联事故；[Analytics](https://github.com/opsknight-labs/OpsKnight/blob/main/docs/v2.0.0/concepts/analytics.md) 强调样本范围、空值及时间边界 | 预置“月度稳定性”“重复问题”“逾期整改”视图；整改单列待开始、进行中、受阻、完成及有效性验证；导出同时携带口径、过滤条件和快照日期 | 是完整事故响应平台，排班、通知、自动化等超出当前需求。查到的当前 LICENSE 为 AGPL v3，不能依据旧版资料当作 Apache-2.0；本次只借鉴产品模式，未复制代码 |
| [OneUptime](https://github.com/OneUptime/oneuptime) | [官方事故管理介绍](https://oneuptime.com/product/incident-management) 展示可配置事故状态、严重级别、业务影响、复盘与行动项；仓库同时包含监控与可观测性平台 | 事故详情突出“影响是什么、恢复到什么程度、后续措施如何”；区分事故恢复、RCA 完成和整改关闭 | 该行是官方产品文档层面的参考，未验证其完整执行路径；整体平台比管理 MI 范围广，当前不建议为此替换 ServiceNow / COSY |

局部源码核对版本（文件 blob SHA，供后续识别来源是否变化）：Superset `DrillByModal.tsx` 为 `f09e3597e06612a2de179d4e833746c2d5360658`；OpsKnight `DashboardSavedFilters.tsx` 为 `33669c37d0284dec8097a315e9d67cb8f18a9020`、`DashboardActionItems.tsx` 为 `a80034b5da323dd43e4b12122e0548c90df09f0f`。上述链接跟随上游分支，未来可能变化。查到组件并不等于验证全部线上入口；本次不作该推断。

### 其他候选的取舍

- [Netflix Dispatch](https://github.com/Netflix/dispatch)：GitHub 标明于 2025-09-03 归档、只读。可参考事故关联与协作思路，不优先作为新系统底座。
- [Aleeza4 / incident-management-dashboard](https://github.com/Aleeza4/incident-management-dashboard)：直接面向 ITSM MI 的 Power BI 示例，README 列出事故趋势、分配组工作量、恢复耗时和 SLA breach。查到根目录仅有 README、截图与 PBIX；本次未打开 PBIX 核对 DAX 和数据模型，因此仅列为版面／指标清单参考，不认定计算正确或可直接复用。

### 调研时对初版原型的建议（历史基线）

| 优先级 | 当前原型情况 | 建议变化与管理问题 | 讨论时如何验证 |
|---|---|---|---|
| 先做 | 只有一个 `dimension + group`；切换维度会清除原分组 | 连续下钻与可返回路径。老板选了 AMH 后，还能继续看批处理中的根因，不必重建筛选 | 每深入一层，保留上层条件；返回任一层后，数字和明细恢复一致 |
| 先做 | KPI 只展示数字；分组可点，明细另设筛选 | KPI 直接查看构成记录；逾期 KPI 进入措施明细，事故 KPI 进入事故明细，避免单位混用 | 7 项逾期对应 7 项措施，并清楚标明涉及 6 起事故 |
| 先做 | 有固定快照、待确认 RCA 和页尾口径说明 | 每个指标提供就近的口径说明及数据状态：时间字段、样本数、缺失数、来源覆盖和更新时间 | 缺资料、无适用样本与确实为零呈现不同状态；未知覆盖不显示虚构百分比 |
| 先做 | 只显示逾期列表；措施状态以 `done` 布尔值表示，有独立 `validated` 标志 | 整改跟进视图：待开始／进行中／受阻／完成，另列逾期、负责人、到期日、原工单与有效性证据 | 能定位需要管理协调的受阻措施；完成不自动等于有效，链接回原工单而不复制一套流程 |
| 后续讨论 | 月度事故数环比；恢复分钟只在明细展示 | 增加同口径时段对比、恢复耗时分布及业务影响视角 | 展示分母和缺失量；跨系统事故数量差异不直接解释为稳定性排名；没有 SLA 目标及合格时间戳时不生成达标率 |
| 后续讨论 | 筛选仅在当前页面内存中，无保存或导出 | 管理视图预设、保留筛选的链接与打印版 | 重开同一视图可复现；输出注明虚构／数据版本、过滤条件和统计时间。权限与内部分享方式另行确认 |

调研时建议优先讨论前四项。用户随后授权将前四项加入虚构 HTML 原型，已用于本地交互演示；共同评审仍待进行，详见 D-10 的后续记录。后两项保留待讨论。工程选型应在 ServiceNow / COSY 能力盘点后决定；可评估复用现有报表、BI 展示层或轻量定制页面，本次不批准其中任何一种实施路线。
