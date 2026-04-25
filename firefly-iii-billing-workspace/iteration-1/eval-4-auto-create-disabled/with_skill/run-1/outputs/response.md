任务类型：新增交易 / 补账（自动新建受限）

因为你明确说了预算和标签都不允许自动新建，所以我不会直接把 `commute-plus` 和 `metro-urgent` 塞进交易 payload。

我会先做两步：
1. 读取元数据或自动补全：
   - `python3 scripts/firefly_client.py autocomplete budgets 'commute-plus'`
   - `python3 scripts/firefly_client.py autocomplete tags 'metro-urgent'`
2. 如果没有精确匹配，就把现有候选项列给你选；在你确认前不提交 `post`。
