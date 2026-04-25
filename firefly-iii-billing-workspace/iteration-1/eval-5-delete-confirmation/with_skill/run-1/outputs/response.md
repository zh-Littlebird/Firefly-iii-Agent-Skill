任务类型：删除交易（进入 `skills/manage.md`）

这是高风险操作，我不会直接删。

先做两步：
1. 读取交易组详情：
   `python3 scripts/firefly_client.py get 12345`
2. 把待删除对象展示给你确认，包括：描述、金额、日期、账户、分类/预算等关键信息

确认无误后，我才会执行：
`python3 scripts/firefly_client.py delete 12345`
