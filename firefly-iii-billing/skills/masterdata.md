# 主数据管理

本文件覆盖交易依赖的核心主数据：账户、分类、预算、标签。目标是补足显式 CRUD 能力，不改变原有记账、查询、分析链路。

## 使用原则

- 保持原有交易命令和分析命令不变
- **自动新建开关同时约束隐式新建和显式创建**：当 `config.json` 中对应开关为 `false` 时，对应的创建命令（`account-create`、`category-create`、`budget-create`、`tag-create`、`piggybank-create`）**完全禁止使用**，agent 必须拒绝执行并向用户说明该功能已在配置中关闭
- 如果配置开关为 `false` 且用户要求新建，agent 必须引导用户从已有列表中选择，或告知用户修改 `config.json` 中的对应开关
- 更新和删除属于显式维护操作，执行前应向用户确认对象和影响范围
- 如果用户只给了名称、没给 ID，先查列表或做自动补全，再决定是否更新
- 删除前优先读取详情，避免删错同名或近似名称对象

## 账户

```bash
# 列表
python3 scripts/firefly_client.py accounts [TYPE]

# 详情
python3 scripts/firefly_client.py account-get <ACCOUNT_ID>

# 创建（仅当 FIREFLY_III_AUTO_CREATE_ACCOUNTS=true 时允许）
python3 scripts/firefly_client.py account-create '<JSON_DATA>'

# 更新
python3 scripts/firefly_client.py account-update <ACCOUNT_ID> '<JSON_DATA>'

# 删除
python3 scripts/firefly_client.py account-delete <ACCOUNT_ID>
```

**账户创建限制**：`account-create` 仅在 `config.json` 中 `FIREFLY_III_AUTO_CREATE_ACCOUNTS=true` 时允许使用。若该配置为 `false`，agent 必须拒绝执行此命令，引导用户从已有账户列表中选择或修改配置。

适用场景：
- “新建一个招商银行储蓄卡账户”（需配置开关为 `true`）
- “把这个信用卡账户改名”
- “删除废弃账户”

## 分类

```bash
# 列表
python3 scripts/firefly_client.py categories

# 详情
python3 scripts/firefly_client.py category-get <CATEGORY_ID>

# 创建（仅当 FIREFLY_III_AUTO_CREATE_CATEGORIES=true 时允许）
python3 scripts/firefly_client.py category-create '<JSON_DATA>'

# 更新
python3 scripts/firefly_client.py category-update <CATEGORY_ID> '<JSON_DATA>'

# 删除
python3 scripts/firefly_client.py category-delete <CATEGORY_ID>
```

**分类创建限制**：`category-create` 仅在 `config.json` 中 `FIREFLY_III_AUTO_CREATE_CATEGORIES=true` 时允许使用。若该配置为 `false`，agent 必须拒绝执行此命令，引导用户从已有分类列表中选择或修改配置。

适用场景：
- “新增一个宠物分类”（需配置开关为 `true`）
- “把旧分类合并前先改名”
- “清理不再使用的分类”

## 预算

```bash
# 列表
python3 scripts/firefly_client.py budgets [START] [END]

# 详情
python3 scripts/firefly_client.py budget-get <BUDGET_ID>

# 创建（仅当 FIREFLY_III_AUTO_CREATE_BUDGETS=true 时允许）
python3 scripts/firefly_client.py budget-create '<JSON_DATA>'

# 更新
python3 scripts/firefly_client.py budget-update <BUDGET_ID> '<JSON_DATA>'

# 删除
python3 scripts/firefly_client.py budget-delete <BUDGET_ID>

# 可用预算
python3 scripts/firefly_client.py available-budgets [START] [END]
python3 scripts/firefly_client.py available-budget-get <AVAILABLE_BUDGET_ID>

# 预算额度明细
python3 scripts/firefly_client.py budget-limit-list <BUDGET_ID> [START] [END]
python3 scripts/firefly_client.py budget-limit-get <BUDGET_ID> <LIMIT_ID>
python3 scripts/firefly_client.py budget-limit-create <BUDGET_ID> '<JSON_DATA>'
python3 scripts/firefly_client.py budget-limit-update <BUDGET_ID> <LIMIT_ID> '<JSON_DATA>'
python3 scripts/firefly_client.py budget-limit-delete <BUDGET_ID> <LIMIT_ID>
```

**预算创建限制**：`budget-create` 和 `budget-limit-create` 仅在 `config.json` 中 `FIREFLY_III_AUTO_CREATE_BUDGETS=true` 时允许使用。若该配置为 `false`，agent 必须拒绝执行这两个命令，引导用户从已有预算列表中选择或修改配置。

说明：
- `budgets` 带日期时会返回该时间段已花金额
- `budget-get` / `budget-update` / `budget-delete` 面向预算定义本身
- `available-budgets` 面向 Firefly III 计算出的可用预算金额
- `budget-limits` 是跨预算、按日期范围的额度总表
- `budget-limit-list/get/create/update/delete` 面向某个预算下面的额度分段明细
- 关闭预算的自动月预算时，不要把 `auto_budget_type` / `auto_budget_period` / `auto_budget_amount` 直接传 `null` 或把金额设为 `0`，Firefly III 常会返回 `422`
- 实测可用写法：`budget-update` 时传 `auto_budget_type: "none"`，同时保留一个大于 `0` 的 `auto_budget_amount` 与原 `auto_budget_period`；接口会在保存后把这三个字段清成 `null`，从而真正关闭自动预算
- 若是新建预算额度 `budget-limit-create`，`currency_id` 应传整数而不是字符串；字符串在部分 Firefly III 版本会触发 `500`

## 标签

```bash
# 列表
python3 scripts/firefly_client.py tags

# 详情
python3 scripts/firefly_client.py tag-get <TAG_OR_ID>

# 创建（仅当 FIREFLY_III_AUTO_CREATE_TAGS=true 时允许）
python3 scripts/firefly_client.py tag-create '<JSON_DATA>'

# 更新
python3 scripts/firefly_client.py tag-update <TAG_OR_ID> '<JSON_DATA>'

# 删除
python3 scripts/firefly_client.py tag-delete <TAG_OR_ID>
```

**标签创建限制**：`tag-create` 仅在 `config.json` 中 `FIREFLY_III_AUTO_CREATE_TAGS=true` 时允许使用。若该配置为 `false`，agent 必须拒绝执行此命令，引导用户从已有标签列表中选择或修改配置。

说明：
- Firefly III 标签接口支持传标签名或标签 ID
- 当前客户端会对标签名做 URL 编码，因此中文、空格和特殊字符都可以安全传递；若对象存在重名风险，仍优先使用 ID

## JSON 提交约定

- 创建和更新命令均支持 JSON 字符串或 JSON 文件路径
- 具体字段以 Firefly III `v1` API 为准
- 对象不确定时，先调用列表或详情命令，不要盲改盲删
