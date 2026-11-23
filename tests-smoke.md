# Manual smoke test

1. Log in as admin and confirm dashboard loads.
2. Record purchase of 60 kg flour at ₹45.
3. Log in as production and save one run: actual output 188; flour 40; oil 10.5; spice 1.2; labour 1200; overhead 600.
4. Log in as salesa and submit Retailer A order for 150 packets.
5. Admin approves, invoices and dispatches. Invoice total should be ₹7,875.
6. Salesman B submits 120 packets for Distributor B; admin approval should report shortage 32 after the first dispatch.
7. Admin records ₹4,000 payment; outstanding should be ₹3,875.
8. Export reports and open the invoice PDF.
