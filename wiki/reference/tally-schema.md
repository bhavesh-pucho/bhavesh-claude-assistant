# Tally Database Schema

**Summary**: PostgreSQL schema for Tally ERP data synced via PuchoSetu — mst_* for masters, trn_* for transactions.
**Tags**: #tally #postgresql #schema #puchosetu
**Created**: 2026-04-07
**Last Updated**: 2026-04-07

---

## Table Naming Convention

- `mst_*` — master tables (ledgers, groups, stock items, employees, etc.)
- `trn_*` — transaction tables (vouchers, sales, purchases, etc.)

## Key Tables

| Table | Key Columns | Notes |
|---|---|---|
| `mst_ledger` | `name`, `parent`, `opening_balance`, `closing_balance` | All ledger accounts |
| `mst_group` | `name`, `parent`, `primary_group` | Account groups |
| `mst_stockitem` | `name`, `parent`, `closing_balance`, `closing_rate` | Inventory items |
| `trn_voucher` | `date`, `voucher_type`, `party_name`, `amount`, `is_cancelled` | All vouchers |
| `trn_accounting` | `ledger_name`, `amount` | Debit = negative, Credit = positive |
| `trn_inventory` | `item_name`, `quantity`, `rate`, `amount` | Stock movement lines |

## Data Loader

- Project: `tally-database-loader` (MIT license, TypeScript)

## Related Notes

- [[text-to-sql]]
- [[puchosetu]]
