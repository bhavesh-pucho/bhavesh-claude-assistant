# Text-to-SQL Model

**Summary**: Llama-3.1-8B finetuned with QLoRA/Unsloth for EN/HI/GU natural language to SQL conversion over Tally data.
**Tags**: #ml #text-to-sql #qlora #unsloth #vllm #llama
**Created**: 2026-04-07
**Last Updated**: 2026-04-07

---

## Model Spec

| Parameter | Value |
|---|---|
| Base model | Llama-3.1-8B |
| Finetuning | QLoRA via Unsloth |
| Training infra | RunPod (GPU cloud, on-demand) |
| Inference | vLLM on E2E Networks |
| GPU server | Intel Xeon, 24GB RAM, NVIDIA RTX |

## Dataset

- ~70-80 curated query pairs
- Synthetic expansion via Gretel.ai
- Input languages: EN / HI / GU
- Output: SQL with English column aliases only

## Target Schema (Tally/PuchoSetu)

### Table Naming

- `mst_*` — master tables (ledgers, groups, stock items, employees)
- `trn_*` — transaction tables (vouchers, sales, purchases)

### Key Tables

| Table | Contains |
|---|---|
| `mst_ledger` | Ledger accounts — `name`, `parent`, `opening_balance`, `closing_balance` |
| `mst_group` | Account groups — `name`, `parent`, `primary_group` |
| `mst_stockitem` | Inventory — `name`, `parent`, `closing_balance`, `closing_rate` |
| `trn_voucher` | Vouchers — `date`, `voucher_type`, `party_name`, `amount`, `is_cancelled` |
| `trn_accounting` | Voucher ledger lines — `ledger_name`, `amount` (debit=negative, credit=positive) |
| `trn_inventory` | Stock movement — `item_name`, `quantity`, `rate`, `amount` |

## Hard Rules

- No external LLM API dependency at inference time
- Never use Gujarati/Hindi text as SQL column aliases — always English
- Tally data loader: `tally-database-loader` project (MIT, TypeScript)

## Related Notes

- [[tally-schema]]
- [[e2e-gpu-server]]
- [[runpod-training]]
