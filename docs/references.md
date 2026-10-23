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
