# 前言

本章主旨介绍关系代数在`Calcite`中的应用，如果还对`Calcite`不了解的同学，也可以异步到`https://github.com/dafei1288/CalciteDocTrans/blob/master/tutorial.md`去看

# 正文

关系代数是`Calcite`的核心。每个查询都可以被表述为一个关系运算符树(`a tree of relational operators`)。你可以将SQL翻译成关系代数或者直接构建树。

Relational algebra is at the heart of Calcite. Every query is represented as a tree of relational operators. You can translate from SQL to relational algebra, or you can build the tree directly.

Planner rules transform expression trees using mathematical identities that preserve semantics. For example, it is valid to push a filter into an input of an inner join if the filter does not reference columns from the other input.

Calcite optimizes queries by repeatedly applying planner rules to a relational expression. A cost model guides the process, and the planner engine generates an alternative expression that has the same semantics as the original but a lower cost.

The planning process is extensible. You can add your own relational operators, planner rules, cost model, and statistics.