# Manual Eval Transcript

## Eval Prompt

把“咖啡”这个分类改名成“咖啡茶饮”，然后给它补一个更合适的说明；如果分类不存在，先告诉我现有分类里最接近的几个。

## Execution Notes

- 判定为分类主数据维护。
- 读取 `skills/masterdata.md`。
- 依据“先查列表或自动补全，再决定是否创建或更新”的规则，先走候选确认。

## Final Response

见 `response.md`。
