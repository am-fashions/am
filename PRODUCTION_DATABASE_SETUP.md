# 🗄️ Production Database Setup

## Important: Database Migration Required

Your local database has been updated to allow NULL values for `product_id` in the `order_items` table. **You MUST run this migration on your production database** or orders will fail.

---

## Option 1: Using phpMyAdmin (Shared Hosting)

1. Login to your hosting cPanel
2. Open phpMyAdmin
3. Select your database (e.g., `ecommerce_admin`)
4. Click on "SQL" tab
5. Copy and paste this SQL:

```sql
-- Make product_id nullable in order_items table
ALTER TABLE order_items DROP FOREIGN KEY order_items_ibfk_2;
ALTER TABLE order_items MODIFY COLUMN product_id INT NULL;
ALTER TABLE order_items ADD CONSTRAINT order_items_ibfk_2 
FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE SET NULL;
```

6. Click "Go" to execute
7. Verify by checking the table structure

---

## Option 2: Using MySQL Command Line (VPS/Cloud)

```bash
# Connect to MySQL
mysql -u your_username -p your_database_name

# Run the migration
ALTER TABLE order_items DROP FOREIGN KEY order_items_ibfk_2;
ALTER TABLE order_items MODIFY COLUMN product_id INT NULL;
ALTER TABLE order_items ADD CONSTRAINT order_items_ibfk_2 
FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE SET NULL;

# Verify the change
DESCRIBE order_items;

# Exit
EXIT;
```

---

## Option 3: Using Railway/Render Database Client

1. Go to your Railway/Render dashboard
2. Click on your MySQL database
3. Open the "Query" or "Connect" tab
4. Run the SQL commands from Option 1
5. Verify the changes

---

## Option 4: Using MySQL Workbench (Any Platform)

1. Open MySQL Workbench
2. Create new connection with your production database credentials:
   - Hostname: (from your hosting provider)
   - Port: 3306 (usually)
   - Username: (from your hosting provider)
   - Password: (from your hosting provider)
3. Connect to the database
4. Open a new SQL tab
5. Paste and execute the SQL from Option 1
6. Verify the changes

---

## Verification

After running the migration, verify it worked:

```sql
DESCRIBE order_items;
```

You should see:
- `product_id` column with `NULL` = `YES`
- Type: `int(11)`

**Example output:**
```
+------------------+--------------+------+-----+-------------------+
| Field            | Type         | Null | Key | Default           |
+------------------+--------------+------+-----+-------------------+
| order_item_id    | int(11)      | NO   | PRI | NULL              |
| order_id         | int(11)      | NO   | MUL | NULL              |
| product_id       | int(11)      | YES  | MUL | NULL              | ← Should be YES
| variant_id       | int(11)      | YES  | MUL | NULL              |
| product_name     | varchar(255) | NO   |     | NULL              |
| variant_details  | varchar(255) | YES  |     | NULL              |
| quantity         | int(11)      | NO   |     | NULL              |
| unit_price       | decimal(10,2)| NO   |     | NULL              |
| subtotal         | decimal(10,2)| NO   |     | NULL              |
| created_at       | timestamp    | NO   |     | CURRENT_TIMESTAMP |
+------------------+--------------+------+-----+-------------------+
```

---

## Why This Migration is Necessary

### The Problem
- Your website displays products that are NOT in the database (they're hardcoded in `src/data/products.js`)
- When a customer places an order, the system tries to save order items with `product_id = NULL`
- The original schema had `product_id` as `NOT NULL`, causing orders to fail

### The Solution
- Make `product_id` nullable so web orders can be saved without database product IDs
- The `product_name` and `variant_details` fields store all the necessary information
- Later, you can optionally link these to actual products in the database

### What This Allows
✅ Orders from website work (products not in database)  
✅ Orders from admin dashboard work (products in database)  
✅ Flexible system that supports both scenarios  

---

## Complete Database Setup Steps for Production

### Step 1: Create Database
```sql
CREATE DATABASE ecommerce_admin CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Step 2: Import Schema
- Use the file: `admin-dashboard/database/schema.sql`
- This creates all tables with the correct structure

### Step 3: Run Migration (IMPORTANT!)
```sql
-- This is the critical step
ALTER TABLE order_items DROP FOREIGN KEY order_items_ibfk_2;
ALTER TABLE order_items MODIFY COLUMN product_id INT NULL;
ALTER TABLE order_items ADD CONSTRAINT order_items_ibfk_2 
FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE SET NULL;
```

### Step 4: (Optional) Import Seed Data
- Use the file: `admin-dashboard/database/seed.sql`
- This adds sample data for testing

### Step 5: Verify
```sql
-- Check all tables exist
SHOW TABLES;

-- Verify order_items structure
DESCRIBE order_items;

-- Test a simple query
SELECT * FROM customers LIMIT 1;
```

---

## Troubleshooting

### Error: "Cannot drop foreign key"
**Solution:** The foreign key name might be different. Find it first:
```sql
SHOW CREATE TABLE order_items;
```
Look for the foreign key name and use it in the DROP command.

### Error: "Table doesn't exist"
**Solution:** Import the schema first using `schema.sql`

### Error: "Access denied"
**Solution:** Check your database credentials and user permissions

---

## 🚨 CRITICAL REMINDER

**DO NOT SKIP THE MIGRATION!**

If you deploy to production without running this migration:
- ❌ All order placements will fail with 500 error
- ❌ Customers won't be able to complete purchases
- ❌ You'll lose sales

**After running the migration:**
- ✅ Orders work perfectly
- ✅ Customers can complete purchases
- ✅ Everything functions as tested locally

---

## Files You Need for Production

1. **Schema File:** `admin-dashboard/database/schema.sql`
2. **Migration SQL:** (provided above)
3. **Seed Data (optional):** `admin-dashboard/database/seed.sql`

Keep these files safe - you'll need them when setting up your production database!

---

## Quick Reference: Migration SQL

```sql
ALTER TABLE order_items DROP FOREIGN KEY order_items_ibfk_2;
ALTER TABLE order_items MODIFY COLUMN product_id INT NULL;
ALTER TABLE order_items ADD CONSTRAINT order_items_ibfk_2 
FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE SET NULL;
```

**Copy this and save it somewhere safe!** 📋
