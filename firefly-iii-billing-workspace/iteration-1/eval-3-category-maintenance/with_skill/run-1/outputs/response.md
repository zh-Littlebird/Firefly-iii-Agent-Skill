任务类型：分类主数据维护（进入 `skills/masterdata.md`）

我不会直接猜分类 ID。先做这一层确认：
1. 读取现有分类列表，或用 `python3 scripts/firefly_client.py autocomplete categories '咖啡'`
2. 如果存在精确匹配“咖啡”，再展示将被修改的对象：
   - 当前分类：咖啡
   - 拟修改为：咖啡茶饮
   - 拟补充说明：用于咖啡、茶饮、奶茶等饮品消费
3. 如果不存在精确匹配，就先把最接近的几个分类给你选，不直接更新。
