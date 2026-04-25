任务类型：预算漏挂分析（进入 `skills/report.md`）

我会优先走 Firefly III 官方分析接口，而不是先把全量交易拉下来本地聚合。

建议执行顺序：
1. 先拿官方总额：
   `python3 scripts/firefly_client.py insight expense no-budget 2026-04-01 2026-04-18`
2. 再回查漏挂预算的明细：
   `python3 scripts/firefly_client.py transactions-without-budget 2026-04-01 2026-04-18 withdrawal`

这样可以同时回答两件事：
- 总共有多少钱没有挂预算
- 具体是哪些支出漏挂了预算

当前评测没有连真实实例，所以这轮只验证了技能是否选择了正确接口路径。
