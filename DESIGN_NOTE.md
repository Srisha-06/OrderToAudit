# Design Note – OrderToAudit

## Architecture
Browser UI → Express REST API → SQLite. JWT claims identify the user and role. API middleware enforces role permissions. The SQLite database uses foreign keys and WAL mode. Mutating workflows use database transactions so a partial purchase, production run, dispatch or payment cannot be committed.

## Core data model
- `users`: username, hashed password, role.
- `customers`: name, state, GSTIN, retailer/distributor type.
- `products`: finished product, pack size, selling price, GST rate, HSN.
- `items`: raw, packaging and finished stock item master.
- `bom`: standard material quantity per production batch.
- `orders`, `order_items`: salesman order and product lines.
- `invoices`: one invoice per approved order, with taxable value and tax split.
- `dispatches`: dispatched product quantities.
- `purchases`: supplier receipt header.
- `production`, `production_inputs`: production run header and planned/actual input.
- `ledger`: immutable stock movements linked to source type and source ID.
- `payments`: invoice collections.
- `audit`: user, action, entity, entity ID and timestamp.

## Stock calculation
For each item:
`closing balance = Σ ledger IN quantities − Σ ledger OUT quantities`.
Opening quantities are seeded as `OPENING` IN movements. Purchases create IN movements; production material issues create OUT movements; production good output creates finished-goods IN movements; dispatch creates finished-goods OUT movements. The stock API derives current balances from this ledger.

## BOM and variance
A BOM line defines standard material quantity for one batch. For the supplied benchmark, one batch expects 200 packets. Consumption variance is `(actual − planned) / planned × 100`; yield variance is `(actual good output − expected output) / expected output × 100`. Values beyond ±3% should be treated as alerts. Oil 10.5 L against 10 L is +5%; output 188 against 200 is −6%.

## Costing
Material cost = sum(actual issue quantity × stored item rate). The purchase transaction updates flour's rate to ₹45/kg, while other seeded rates remain unchanged. Labour and power/gas are added to the run. Cost per good packet = `(material cost + labour + overhead) / actual good packets`. This is a simple latest-rate method, not FIFO or weighted average.

## Tax
For this exercise, 5% GST is applied to taxable value. Tamil Nadu customer: split equally into CGST and SGST. Customer in another state: charge IGST. This is a demo calculation and does not replace statutory GST compliance.

## Known limitations
The README lists prototype assumptions and production hardening work. In particular, offline sync, credit notes, complete master-data CRUD, financial-year invoice counters, date-range report filters and native XLSX export remain future work.
