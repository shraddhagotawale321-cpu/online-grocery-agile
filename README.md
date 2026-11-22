# FreshKart - Online Grocery Application (Agile Final Jury prototype)

Stack: **Python Flask + SQLite**, HTML templates (Jinja) and one CSS file. No build step.

> **SIMULATED (say this in the jury):** the payment gateway and the delivery tracking are simulations.
> No real money, GPS or notification service is used. The pages are labelled "SIMULATED" on screen.

## Run it
```bash
pip install -r requirements.txt
python app.py            # open http://127.0.0.1:5000
python run_tests.py      # runs 16 test cases, writes TEST_REPORT.md
```
The database file `grocery.db` is created and seeded with 30 products on the first run. Delete it to reset.

## Live demo script (5-7 minutes)
1. **Shop** (`/`): search "milk", filter by category "Dairy", point out "Out of stock" (Almond Milk) and "Only 3 left" (Strawberry).
2. **Cart**: add Milk x2 and Bread x1 -> cart shows subtotal 169 + delivery 30 = 199. Add Strawberry x5 -> error "Only 3 of Strawberry in stock".
3. **Checkout**: submit with a bad phone number -> validation errors. Then fill valid details (name, 10-digit mobile starting 6-9, address 10+ chars).
4. **Payment (SIMULATED)**: card `4000 0000 0000 0002` -> declined, cart kept. Retry with UPI `asha@okbank` -> success, stock reduced.
5. **Tracking (SIMULATED)**: open "Store Staff (demo)", move the order Placed -> Packed -> Out for Delivery -> Delivered, and refresh the customer's order page.
6. **Testing**: show `python run_tests.py` output and `TEST_REPORT.md` (expected vs actual).

Simulated payment test values: card ending `0002` = declined, ending `9995` = insufficient funds, any other 16-digit card with a future expiry and 3-digit CVV = success. UPI id starting with `fail` = declined; any other valid `name@bank` = success.

## Business rules (good viva answers)
- Max **10** units per item per order; quantity must be a whole number >= 1; cannot exceed stock.
- Delivery fee **Rs 30**, free when subtotal is **Rs 300 or more**.
- Stock is reduced **only when payment succeeds**, using one atomic SQL update (`UPDATE ... WHERE stock >= qty`) inside a transaction.
  If someone else bought the last unit first, the order is cancelled and not charged (this is the fix for the "last unit sold twice" defect, DEF-07).
- Delivery status can only move forward: Placed -> Packed -> Out for Delivery -> Delivered. Unpaid orders cannot ship.

## Files
| File | Purpose |
|---|---|
| `app.py` | All routes, validation rules, simulated gateway, database setup |
| `templates/` | Pages: catalogue, cart, checkout, payment, order/tracking, my orders, staff |
| `static/style.css` | Styling |
| `run_tests.py` | 16 test cases (positive / negative / boundary) with expected vs actual |

## Database (SQLite)
`products(id, name, category, unit, price, stock, emoji)` - `orders(id, customer_name, phone, address, subtotal, delivery_fee, total, status, payment_method, payment_status, created_at)` - `order_items(order_id, product_id, name, price, qty)` - `payment_attempts(order_id, method, outcome, reason, created_at)`

## Coverage of the jury checklist
- Core features: catalogue + search/category, cart with price calculation, order + summary, simulated payment, order status/tracking.
- Required tests: stock shortage (TC-04, TC-13), excess quantity (TC-04, TC-07), payment failure (TC-11, TC-12).
- Validation: quantity, name, phone, address, UPI id, card number, expiry, CVV.
- Persistence: SQLite.
- Not built (be honest if asked): user login/accounts, real payment/GPS, email/SMS notifications.

## GitHub (each member should commit their own work)
```bash
git init && git add . && git commit -m "feat: initial grocery app skeleton"
git branch -M main && git remote add origin <your-repo-url> && git push -u origin main
```
Use meaningful messages, for example `feat: cart quantity validation`, `fix: re-check stock at payment (DEF-07)`, `test: add boundary cases for max quantity`.

## Who explains what (suggested split - edit it to match what each member really worked on)
- Catalogue, search and category filter (`index`)
- Cart rules and price calculation (`check_qty`, `price_summary`, cart routes)
- Checkout validation and order creation (`validate_checkout`, `checkout`)
- Simulated payment and stock deduction (`simulated_gateway`, `pay`)
- Order tracking, staff page and tests (`order`, `admin_*`, `run_tests.py`)
