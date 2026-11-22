# Test Report - Online Grocery Application

Payment and delivery tracking are SIMULATED.

| ID | Test | Type | Expected | Actual | Result |
|---|---|---|---|---|---|
| TC-01 | Search by name | Positive | Only milk products listed | Milk listed, Apple not listed | PASS |
| TC-02 | Search with no match | Negative | 'No products found' message | message shown | PASS |
| TC-03 | Cart totals and delivery fee | Positive | Subtotal 169, delivery 30, total 199 | values found in cart page | PASS |
| TC-04 | Quantity above stock | Negative | Rejected: 'Only 3 of Strawberry in stock' | rejected with stock message | PASS |
| TC-05 | Out-of-stock item | Negative | Rejected: out of stock | rejected | PASS |
| TC-06 | Invalid quantity (0, -1, text) | Boundary | 0, -1 and 'abc' all rejected | all rejected | PASS |
| TC-07 | Max 10 per item | Boundary | 10 accepted, 11th unit rejected | 10 accepted, 11 rejected | PASS |
| TC-08 | Free-delivery threshold 299/300 | Boundary | 299 -> fee 30; 300 -> fee 0; empty -> 0 | 299 -> 30; 300 -> 0 | PASS |
| TC-09 | Checkout form validation | Negative | Name, phone and address errors shown; no order created | 3 errors shown | PASS |
| TC-10 | Successful payment reduces stock | Positive | Order Placed/Paid, Milk stock 60 -> 58 | Placed/Paid, Milk stock 58 | PASS |
| TC-11 | Payment declined keeps cart, no stock change | Negative | Payment Failed, stock unchanged (60), cart kept | Payment Failed, stock 60, cart kept=True | PASS |
| TC-12 | Retry payment after failure | Negative | Declined then approved; 2 attempts logged; Placed | Placed, 2 attempts | PASS |
| TC-13 | Stock runs out before payment completes | Negative | Order Cancelled, not charged, stock stays 1 (no negative stock) | Cancelled/Not charged, stock 1 | PASS |
| TC-14 | Invalid card details | Negative | 'Card number must be 16 digits'; order still pending | error shown | PASS |
| TC-15 | Delivery status workflow (simulated) | Positive | Placed > Packed > Out for Delivery > Delivered, stops there | Placed > Packed > Out for Delivery > Delivered > Delivered | PASS |
| TC-16 | Unpaid order cannot ship | Negative | Unpaid order stays 'Pending Payment' | Pending Payment | PASS |

**16/16 passed.**
