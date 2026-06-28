# Part 1 — Business Data Cleaning, Validation & Excel Reporting

## What This Project Is About

A retail company's order data was a mess — messy text, dates written in different ways, duplicate orders, missing info, and discount numbers that didn't make sense. This project takes that messy data, cleans it up, checks it against a set of business rules, writes down every problem found, and builds summary reports a manager could actually use.

## About the Data

- **Raw file:** `data/raw_orders.xlsx` — 932 orders, 21 columns (dates, customer info, location, product category, price, discount, sales, profit, order status, payment status, etc.)
- **Cleaned file:** `data/cleaned_orders.xlsx` — 912 orders (20 exact copies were removed), with extra columns added for the calculations the assignment asked for.

## Tools Used

Microsoft Excel only — just formulas, no macros or coding. Mainly used: TRIM, PROPER, IF, DATE, SUMIFS, COUNTIFS.

## What I Did to Clean It

1. Fixed messy text (extra spaces, ALL CAPS, random lowercase) in every text column.
2. Filled in blank "region" and "ship_mode" with "Unknown" instead of leaving them empty.
3. The dates were written 4 different ways with no warning label — I figured out which format each one was and rewrote them all the same way.
4. Found 21 orders where the ship date was *before* the order date — flagged these as broken.
5. Deleted 20 orders that were 100% exact copies of another order. Kept (but flagged) 24 orders that share an order ID with different details, since I can't tell which one is correct.
6. Fixed discount values — some were written as "70%" text instead of numbers, and some were negative or way too high. Flagged the bad ones and used 0% in their place just for the calculations.
7. Added all the new columns the assignment needed: cleaned discount, recalculated sales, recalculated profit, profit margin, shipping delay (in days), order month, order year, and an overall Clean/Warning/Invalid label for every order.

Full details are in `outputs/cleaning_log.md`.

## Business Rules I Followed

| If this happens... | ...then I do this |
|---|---|
| Region is blank | Fill in "Unknown" |
| Shipping method is blank | Fill in "Unknown" |
| Discount is blank | Treat it as 0% (only checked this was safe to do) |
| Discount is negative or too high | Mark it as invalid, use 0% instead |
| Order was cancelled | Doesn't count in total sales |
| Payment failed | Doesn't count in total sales |
| Payment was refunded | Counted in its own separate summary |
| Ship date is before order date | Mark it as a broken shipping record |

## What Problems I Found (Quick Numbers)

| What | How Many |
|---|---|
| Orders before cleaning | 932 |
| Exact duplicate orders removed | 20 |
| Orders left after cleaning | 912 |
| Totally clean orders | 780 |
| Orders with a small auto-fixed issue | 59 |
| Orders that need a human to check them | 73 |
| Orders missing a region | 26 |
| Orders missing a shipping method | 22 |
| Orders missing a discount | 18 |
| Orders with a broken discount number | 30 |
| Orders with an impossible ship date | 21 |
| Orders with conflicting duplicate info | 24 |
| Orders where sales/profit didn't add up right | 54 |

Full breakdown: `outputs/data_quality_report.xlsx`

## What's in the Pivot Reports

`outputs/pivot_summary.xlsx` has 6 tabs: Sales & profit by region, Sales & profit by category, How many orders per shipping method, Profit margin by customer type, Refunded/cancelled/failed orders by region, and Sales trend by month.

## Things Worth Knowing (Insights)

- **South region** sells the most overall, but the **"Unknown" region** (the ones that were missing region info) actually has the *best* profit margin — worth digging into.
- **Copiers** is the best-selling product sub-category.
- **Home Office** customers are the most profitable group; **Small Business** customers are the least.
- Shipping methods are used fairly evenly — Standard Class is only slightly more popular than the rest.
- **February 2025** was the best month for sales.
- Nothing in this data ever took more than 8 days to ship normally — the real problem is the 21 orders that were "shipped" before they were even ordered.

## Things I Assumed / Couldn't Fully Fix

- I assumed a normal discount is anywhere from 0% to 30%, since every correctly-entered discount fell in that range.
- I assumed "completed sale" means the order status says "Completed" AND the payment says "Paid."
- For the 24 conflicting duplicate orders, I couldn't figure out which version is correct just from the spreadsheet — that would need someone to check the original system.
- For 54 orders, my recalculated sales/profit numbers don't match what was originally recorded. That's expected (because their discount was missing or wrong) — just don't add the old numbers and my new numbers together, only trust the new ones.

## Screenshots Included

| File | What it shows |
|---|---|
| `screenshots/raw_data_preview.png` | What the data looked like before cleaning |
| `screenshots/cleaned_data_preview.png` | The cleaned data with the new columns |
| `screenshots/pivot_summary_1.png` | Sales & profit by region |
| `screenshots/pivot_summary_2.png` | Sales trend by month |
