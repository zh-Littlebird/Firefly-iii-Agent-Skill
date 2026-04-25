任务类型：拆分交易更新（进入 `skills/manage.md`）

这类请求不能直接盲写 update。

正确流程：
1. 先读取原交易组：
   `python3 scripts/firefly_client.py get 4567`
2. 从返回的 `attributes.transactions[]` 里取出两条 split，保留各自的：
   - `transaction_journal_id`
   - `type`
   - `date`
   - `amount`
   - `description`
   - `source_id` / `destination_id`
3. 仅把 `budget_id` 改成 `12`，`category_id` 改成 `34`
4. 再把完整 split 数组交给：
   `python3 scripts/firefly_client.py update 4567 '<JSON_DATA>'`

在拿到原交易详情前，不应提交更新。
