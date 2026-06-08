# 财务管理（Phase 2）

本文件中的能力暂不属于当前 agent MVP 主链路。只有当用户明确要求账单、存钱目标或附件上传时，再加载并使用。

如果用户只是普通记账、查预算、查分类花销，不要读取本文件。

## 账单管理

```bash
python3 scripts/firefly_client.py bills

# 详情
python3 scripts/firefly_client.py bill-get <BILL_ID>

# 创建
python3 scripts/firefly_client.py bill-create '<JSON_DATA>'

# 更新
python3 scripts/firefly_client.py bill-update <BILL_ID> '<JSON_DATA>'

# 删除
python3 scripts/firefly_client.py bill-delete <BILL_ID>
```

查看所有账单，支持周期性支出（房租、会员费、保险等）关联。

> **注意**：账单创建不受自动新建开关限制。账单始终是用户明确发起的操作，不会在记账流程中被隐式创建，因此无需 `FIREFLY_III_AUTO_CREATE_BILLS` 开关。

## 存钱罐管理

```bash
python3 scripts/firefly_client.py piggybanks

# 详情
python3 scripts/firefly_client.py piggybank-get <PIGGY_BANK_ID>

# 创建（仅当 FIREFLY_III_AUTO_CREATE_PIGGY_BANKS=true 时允许）
python3 scripts/firefly_client.py piggybank-create '<JSON_DATA>'

# 更新
python3 scripts/firefly_client.py piggybank-update <PIGGY_BANK_ID> '<JSON_DATA>'

# 删除
python3 scripts/firefly_client.py piggybank-delete <PIGGY_BANK_ID>
```

查看所有存钱罐，支持储蓄目标场景（如"往旅行基金存了500"）。

**存钱罐创建限制**：`piggybank-create` 仅在 `config.json` 中 `FIREFLY_III_AUTO_CREATE_PIGGY_BANKS=true` 时允许使用。若该配置为 `false`，agent 必须拒绝执行此命令，引导用户从已有存钱罐列表中选择或修改配置。

若 `FIREFLY_III_AUTO_CREATE_PIGGY_BANKS=false`，在涉及存钱罐归属或选择时，必须先调用 `piggybanks` 或 `autocomplete piggy-banks` 读取已有列表，并从现有存钱罐中选择；无匹配项时向用户展示现有选项，而不是继续创建。

## 标签管理

标签已升级为主数据管理能力，请加载 `skills/masterdata.md` 中的标签部分进行操作。

## 附件上传

通过 CLI 将小票/发票原图关联到交易记录：

```bash
# 1. 创建附件元数据
python3 scripts/firefly_client.py attachment-create TransactionJournal <JOURNAL_ID> receipt.jpg '消费小票'

# 2. 上传文件内容
python3 scripts/firefly_client.py attachment-upload <ATTACHMENT_ID> /path/to/receipt.jpg
```
