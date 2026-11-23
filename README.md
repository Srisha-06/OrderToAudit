# OrderToAudit – Food Manufacturing Prototype

A fresh VS Code-ready prototype for the NIT Tiruchirappalli Order-to-Audit internship brief.

## Stack
- React is not required for this first runnable prototype: the UI is responsive HTML/CSS/JavaScript served by Express.
- Node.js + Express API, SQLite relational database (Node.js built-in `node:sqlite`), JWT authentication, PDFKit invoice PDFs.
- SQLite keeps setup simple for a local demo while retaining relational tables, transactions, foreign keys, and a stock movement ledger.

## Requirements
- Node.js 22.5 or later (includes npm; tested for built-in SQLite API compatibility with Node.js 24)
- VS Code

No Visual Studio, C++ compiler, or separate SQLite installation is required. The database uses Node.js built-in `node:sqlite`.

## Run in VS Code
1. Extract the ZIP.
2. Open the `OrderToAudit` folder in VS Code.
3. Open Terminal → New Terminal.
4. Run `npm install`.
5. Copy `.env.example` to `.env` (optional for local demo; change JWT_SECRET before sharing).
6. Run `npm start`.
7. Open `http://localhost:8080`.

The database is created automatically at `data/order-to-audit.db` and demo masters/users are seeded on first launch. Delete that DB file to reset to the original seed state (stop the server first).

## Demo logins
| Role | Username | Password |
|---|---|---|
| Admin | `admin` | `Admin@123` |
| Salesman A | `salesa` | `Sales@123` |
| Salesman B | `salesb` | `Sales@123` |
| Production | `production` | `Prod@123` |
| Auditor | `auditor` | `Audit@123` |
| Owner | `owner` | `Owner@123` |

## Included
- Role-aware login and navigation
- Customer/product/item/BOM seed masters
- Salesman order capture
- Admin approval with available-stock check
- GST invoice calculation (Tamil Nadu: CGST+SGST; other state: IGST) and PDF download
- Dispatch with ledger stock deduction
- Purchase receipt and ledger stock addition
- Production run with material issue, actual yield, wastage, labour and overhead
- Finished-goods receipt on production completion
- Inventory balances derived from opening and movement ledger
- Payment entry and outstanding calculation
- Sales, stock, production and collection CSV exports
- Audit trail
- Responsive layout for mobile and desktop

## Benchmark seed data and expected flow
Seeded opening stock: flour 100 kg, oil 30 L, spice 5 kg, pouches 500, cartons 30, finished Murukku 200 g 50 packets. BOM: flour 40 kg, oil 10 L, spice 1.2 kg, pouches 200, cartons 10. Product price ₹50, GST 5%.

Suggested demo:
1. Admin → Purchases: add 60 kg flour at ₹45/kg.
2. Production → enter 188 good packets, 6 damaged packets, labour ₹1,200, overhead ₹600. Material issues: flour 40 kg, oil 10.5 L, spice 1.2 kg, pouches 194 pieces, cartons 9 pieces. (Pouches are 188 good plus 6 damaged.)
3. Salesman A → New Order: Retailer A, 150 packets.
4. Admin → Orders: approve, create invoice, dispatch.
5. Salesman B → New Order: Distributor B, 120 packets. Approval is rejected with shortage of 32 packets.
6. Admin → Invoices & Payments: record ₹4,000 payment.
7. Reports → export CSVs.

Expected key calculations:
- Flour: 100 + 60 − 40 = 120 kg.
- Oil: 30 − 10.5 = 19.5 L.
- Pouches: 500 − 206 = 294 pieces if all 206 are issued/consumed. This matches the benchmark assumption of 188 good plus 6 damaged pouches.
- Finished goods: 50 + 188 = 238; after 150 dispatch = 88.
- Yield variance: (188−200)/200 = −6%.
- Oil variance: (10.5−10)/10 = +5%.
- Retailer invoice: ₹7,500 taxable + ₹187.50 CGST + ₹187.50 SGST = ₹7,875.
- Payment ₹4,000 gives ₹3,875 outstanding.
- Distributor B order of 120 against 88 stock is short by 32.

## Costing and valuation
- Material cost uses the latest stored item rate (the purchase receipt updates the flour rate to ₹45/kg).
- Labour and gas/power are allocated to the run and divided by good packets.
- Cost per packet = (material cost + labour + overhead) / actual good packets.
- Stock quantities are maintained as ledger movements, not manually overwritten balances.

## Important prototype limitations / assumptions
This is a working internship prototype, not production-certified accounting software. Before real deployment, add HTTPS, strong secret management, password reset, rate limiting, CSRF strategy, database backups, comprehensive automated tests, invoice cancellation/credit notes, immutable posted invoice correction flow, supplier/customer CRUD screens, editable BOM/master screens, proper financial-year invoice sequencing, statutory GST validation, date-filtered reports and Excel `.xlsx` output.
- GST is simplified to the exercise's 5% rate. GSTIN/HSN values are demo data.
- The production screen is seeded for the Murukku benchmark. Add dynamic multi-product BOM input controls before supporting arbitrary recipes.
- Offline order sync is not implemented.
- Stock ledger movements are transactional; this prototype does not expose stock adjustment editing.
- CSV files open in Excel; native `.xlsx` workbook generation is not included.

## Design note
See `DESIGN_NOTE.md`.
