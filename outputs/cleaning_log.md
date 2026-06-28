# Cleaning Log — Retail Order Data

**Before cleaning:** 932 orders
**After cleaning:** 912 orders (20 exact copies removed)

---

## 1. What Was Wrong With the Raw Data

| # | Problem | Where | How many |
|---|---|---|---|
| 1 | Messy text — extra spaces, ALL CAPS, lowercase, mixed casing | customer_name, segment, region, state, city, category, sub_category, ship_mode, payment_status, order_status | Spread across most rows |
| 2 | Region was left blank | region | 26 orders |
| 3 | Shipping method was left blank | ship_mode | 22 orders |
| 4 | Discount was left blank | discount | 18 orders |
| 5 | Dates written 4 different ways (e.g. 21/07/2024, 21-07-2024, 2024-07-21, "21 Jul 2024") | order_date, ship_date | Every single row |
| 6 | Ship date was *before* the order date (impossible) | order_date, ship_date | 21 orders |
| 7 | The exact same order appeared twice | whole row | 20 orders |
| 8 | Same order ID appeared twice but with different details each time | order_id | 24 orders |
| 9 | Discount was a negative number | discount | 15 orders |
| 10 | Discount was way too high (like 70% or 85%) | discount | 15 orders |
| 11 | The sales/profit numbers didn't actually match quantity × price × discount | sales, profit | 54 orders |
| 12 | Order marked "Completed" but the payment was marked "Failed" — doesn't make sense | order_status, payment_status | 2 orders |

---

## 2. What I Did to Fix Each Thing

**Messy text:** Cleaned up every text column — removed extra spaces and made the capitalization consistent (so "north", "NORTH", and "North " all just become "North").

**Blank region/ship_mode:** Filled these in with "Unknown" instead of leaving them empty, so nothing breaks later when sorting or filtering.

**Mixed-up dates:** The dates were written in 4 different styles with no label telling you which style each one was. I figured out which style each date used (by looking at the separators — slash, dash, or spelled-out month) and rewrote every date the same way: YYYY-MM-DD. I double-checked this against the whole dataset, so I'm confident every date converted correctly.

**Duplicate orders:** When an order was copied exactly (every single detail identical), I deleted the extra copy and kept one. When an order ID showed up twice but the *details didn't match* (e.g. different sales amount), I did **not** delete either one — I kept both and clearly marked them "needs review," since I can't tell which version is correct without more information.

**Bad discounts:** Some discounts were typed as text like "70%" — I converted these to normal numbers (0.70). Anything negative or above 30% doesn't match any of the real, valid discounts in the data, so I marked those as invalid and used 0% in their place for the sales calculations (the original number is still kept, just flagged).

**Missing discounts:** Where discount was blank, I checked that everything else about that order (quantity, price, sales, cost) looked fine — it did, for all 18 cases — so I just filled in 0% for those.

**New columns added:** I added the columns the assignment asked for — cleaned discount, recalculated sales, recalculated profit, profit margin, shipping delay (in days), order month, order year, and an overall "Clean / Warning / Invalid" status for every order. These all update automatically using formulas, not typed-in numbers.

---

## 3. The Business Rules I Followed

| Situation | What I did |
|---|---|
| Region missing | Filled in "Unknown", marked as Warning |
| Shipping method missing | Filled in "Unknown", marked as Warning |
| Discount missing | Filled in 0%, marked as Warning |
| Discount negative or too high | Marked Invalid, treated as 0% for calculations |
| Cancelled order | Doesn't count toward total completed sales |
| Payment failed | Doesn't count toward total completed sales |
| Payment refunded | Counted separately, not mixed in with regular sales |
| Ship date before order date | Marked as an invalid shipping record |

---

## 4. Assumptions I Made (since the brief didn't spell these out)

1. **What counts as a normal discount:** I assumed anything from 0% to 30% is normal, because every discount that was typed in correctly fell somewhere in that range (0, 5, 10, 15, 20, or 25%).
2. **What counts as a "completed sale":** I assumed it means the order status is "Completed" *and* the payment status is "Paid" — both need to be true.
3. **What to do with bad discounts:** Instead of deleting those rows (which would also throw away good data like quantity and price), I just flagged them and used 0% so the rest of the row is still usable.

## 5. Records Removed

- **20 rows deleted** — these were 100% exact copies of another row already in the file.

## 6. Records Flagged (kept, just marked for attention)

- **73 orders** marked "Invalid" — something is genuinely wrong (bad discount, impossible ship date, or conflicting duplicate) and needs a human to look at it.
- **59 orders** marked "Warning" — nothing seriously wrong, just a blank value that got auto-filled.
- **780 orders** are completely clean.

## 7. Things This Cleaning Couldn't Fix

- For the 24 conflicting duplicate orders, I can't tell which version is the "real" one just by looking at the spreadsheet — that needs someone to check the original source system.
- The 0–30% discount rule is my best guess based on the data, not an official company policy — if the real rule is different, those numbers would need to be redone.
- For 54 orders, the sales/profit numbers I calculated don't match what was originally recorded. That's expected (their discount was missing or invalid), but it means you shouldn't add the old "sales" column and my new "calculated_sales" column together — only trust the new one.
- I only had this one spreadsheet to work with — there was no other system to double-check things like customer details or product prices against.
