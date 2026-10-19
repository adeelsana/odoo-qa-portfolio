# SQL data verification

The UI tells you what the application *thinks* happened. The database tells you what actually happened. Checking one against the other is how you catch defects that the interface hides — and it is a skill worth showing, since you list SQL on your CV.

## Connecting

```bash
docker compose exec db psql -U odoo -d <your-database-name>
```

`\dt` lists tables · `\d table_name` describes one · `\q` quits.

## Key tables

| Table | Contains |
|---|---|
| `sale_order` | Sales order headers — state, customer, totals |
| `sale_order_line` | Order lines — product, quantity ordered, delivered, invoiced |
| `stock_quant` | Current stock on hand, per product per location |
| `stock_move` | Individual stock movements |
| `stock_picking` | Delivery orders |
| `account_move` | Invoices and credit notes (headers) |
| `account_move_line` | Invoice lines |
| `product_product` / `product_template` | Products |

> Column names vary between Odoo versions. If a query errors, run `\d table_name` and adjust — doing that yourself is part of the exercise, and being able to say "the schema differed in my version so I checked and adapted" is a good interview answer.

## Verification queries

### 1. Order header total vs sum of its lines
Catches rounding drift (risk R5).

```sql
SELECT so.name,
       so.amount_untaxed AS header_untaxed,
       SUM(sol.price_subtotal) AS lines_subtotal,
       so.amount_untaxed - SUM(sol.price_subtotal) AS difference
FROM sale_order so
JOIN sale_order_line sol ON sol.order_id = so.id
GROUP BY so.name, so.amount_untaxed
HAVING ABS(so.amount_untaxed - SUM(sol.price_subtotal)) > 0.001;
```

**Expected: zero rows.** Any row is a defect.

### 2. Negative stock
Catches risk R2.

```sql
SELECT pt.name AS product, sq.quantity, sl.complete_name AS location
FROM stock_quant sq
JOIN product_product pp ON pp.id = sq.product_id
JOIN product_template pt ON pt.id = pp.product_tmpl_id
JOIN stock_location sl ON sl.id = sq.location_id
WHERE sq.quantity < 0;
```

Interpret rather than assume: negative stock in an *internal* location is a problem; in a virtual location it may be normal. Say which in your notes.

### 3. Delivered quantity exceeding ordered quantity
Catches risk R1 / R4.

```sql
SELECT so.name, pt.name AS product,
       sol.product_uom_qty AS ordered,
       sol.qty_delivered AS delivered
FROM sale_order_line sol
JOIN sale_order so ON so.id = sol.order_id
JOIN product_product pp ON pp.id = sol.product_id
JOIN product_template pt ON pt.id = pp.product_tmpl_id
WHERE sol.qty_delivered > sol.product_uom_qty;
```

### 4. Invoiced more than delivered, on a delivered-quantities policy
The money case. Catches risk R1.

```sql
SELECT so.name, pt.name AS product,
       sol.qty_delivered, sol.qty_invoiced
FROM sale_order_line sol
JOIN sale_order so ON so.id = sol.order_id
JOIN product_product pp ON pp.id = sol.product_id
JOIN product_template pt ON pt.id = pp.product_tmpl_id
WHERE pt.invoice_policy = 'delivery'
  AND sol.qty_invoiced > sol.qty_delivered;
```

### 5. Stock moves left behind by a cancelled order
Catches risk R6.

```sql
SELECT sm.reference, sm.state, so.name AS order_name, so.state AS order_state
FROM stock_move sm
JOIN stock_picking sp ON sp.id = sm.picking_id
JOIN sale_order so ON so.name = sp.origin
WHERE so.state = 'cancel'
  AND sm.state NOT IN ('cancel', 'done');
```

### 6. Decimal precision on weight-based products
Catches risk R5 — the case that matters most given the domain.

```sql
SELECT so.name, pt.name AS product, sol.product_uom_qty, sol.qty_delivered
FROM sale_order_line sol
JOIN sale_order so ON so.id = sol.order_id
JOIN product_product pp ON pp.id = sol.product_id
JOIN product_template pt ON pt.id = pp.product_tmpl_id
WHERE sol.product_uom_qty <> ROUND(sol.product_uom_qty)
   OR sol.qty_delivered <> ROUND(sol.qty_delivered);
```

Confirm the stored values keep full precision rather than being rounded on write.

## Results log

| Query | Run on | Rows returned | Expected | Finding | Defect ID |
|---|---|---|---|---|---|
| 1 | | | 0 | | |
| 2 | | | 0 in internal locations | | |
| 3 | | | 0 unless tested deliberately | | |
| 4 | | | 0 | | |
| 5 | | | 0 | | |
| 6 | | | precision retained | | |
