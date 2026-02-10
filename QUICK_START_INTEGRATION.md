# Quick Start - Integrated E-Commerce System

This guide will get your complete e-commerce system running in minutes.

## What You're Running

- **Main Website** (Port 3000): Customer-facing shopping site
- **Admin Dashboard** (Port 3001): Management interface for orders, products, customers
- **Backend API** (Port 5000): Shared API serving both applications
- **MySQL Database**: Unified data storage via XAMPP

## Prerequisites

✅ Node.js installed (v16 or higher)
✅ XAMPP installed (for MySQL)
✅ All dependencies installed

## Step-by-Step Setup

### 1. Start XAMPP MySQL

1. Open XAMPP Control Panel
2. Click "Start" for MySQL
3. Wait for it to turn green

### 2. Setup Database

1. Open browser and go to: http://localhost/phpmyadmin
2. Click "SQL" tab at the top
3. Open file: `admin-dashboard/database/schema.sql`
4. Copy ALL content and paste in SQL tab
5. Click "Go" button
6. Click "SQL" tab again
7. Open file: `admin-dashboard/database/seed.sql`
8. Copy ALL content and paste in SQL tab
9. Click "Go" button

✅ Database is now ready with sample data!

### 3. Install Dependencies

Open terminal in project root and run:

```bash
# Install root dependencies (includes concurrently)
npm install

# Install backend dependencies
cd admin-dashboard/server
npm install
cd ../..

# Install admin dashboard dependencies
cd admin-dashboard/client
npm install
cd ../..
```

### 4. Start Everything

**Option A: Start All at Once (Easiest)**

```bash
npm run start:all
```

This starts:
- Backend API on port 5000
- Admin Dashboard on port 3001
- Main Website on port 3000

**Option B: Start Individually**

Terminal 1 - Backend:
```bash
npm run start:backend
```

Terminal 2 - Admin Dashboard:
```bash
npm run start:admin
```

Terminal 3 - Main Website:
```bash
npm run start:website
```

### 5. Access Your Applications

Open these URLs in your browser:

- **Main Website**: http://localhost:3000
- **Admin Dashboard**: http://localhost:3001
- **API Health Check**: http://localhost:5000/api/health

## Quick Test

### Test 1: View Products
1. Go to http://localhost:3000 (main website)
2. You should see products from the database
3. Go to http://localhost:3001 (admin dashboard)
4. Click "Products" - you should see the same products

### Test 2: Create an Order
1. On main website (port 3000):
   - Add products to cart
   - Fill in customer details
   - Complete checkout
2. On admin dashboard (port 3001):
   - Click "Orders"
   - Your order should appear!

### Test 3: Update Product
1. On admin dashboard (port 3001):
   - Go to "Products"
   - Edit a product (change price or stock)
   - Save changes
2. On main website (port 3000):
   - Refresh the page
   - Product should show updated info

## Troubleshooting

### "Port 3000 already in use"
- Close other React apps
- Or change port in main website

### "Port 5000 already in use"
- Change PORT in `admin-dashboard/server/.env`

### "Database connection failed"
- Make sure XAMPP MySQL is running (green in XAMPP)
- Check database name is `ecommerce_admin`
- Verify you imported both schema.sql and seed.sql

### "No products showing"
- Make sure you imported seed.sql
- Check http://localhost:5000/api/products in browser
- Should return JSON with products

### "CORS error"
- Make sure backend is running on port 5000
- Check both frontends are on correct ports (3000 and 3001)

## Available Scripts

```bash
# Start everything at once
npm run start:all

# Start only backend
npm run start:backend

# Start only admin dashboard
npm run start:admin

# Start only main website
npm run start:website
```

## What's Included

### Sample Data
- 10 Products (clothing items)
- 5 Customers
- 7 Orders with different statuses
- 5 Active coupons
- Product variants (sizes, colors)

### Features Working
- Product browsing
- Shopping cart
- Checkout process
- Order management
- Customer tracking
- Inventory management
- Sales analytics
- Coupon system

## Next Steps

1. **Customize Products**: Add your own products via admin dashboard
2. **Test Orders**: Place test orders and manage them
3. **Explore Analytics**: View sales charts and reports
4. **Manage Inventory**: Update stock levels
5. **Create Coupons**: Add discount codes

## Need Help?

Check these files for detailed information:
- `INTEGRATION_SETUP.md` - Complete integration guide
- `admin-dashboard/README.md` - Admin dashboard documentation
- `admin-dashboard/TROUBLESHOOTING.md` - Common issues and solutions

## System Architecture

```
Customer Website (3000) ──┐
                          ├──► Backend API (5000) ──► MySQL Database
Admin Dashboard (3001) ───┘
```

All three components work together seamlessly!

---

**You're all set!** 🚀

Your integrated e-commerce system is now running. Start exploring and customizing it to fit your needs.
