# Setup Instructions - Visual Guide

Follow these steps exactly to get your integrated e-commerce system running.

## 📋 Prerequisites Checklist

Before starting, make sure you have:

- [ ] Node.js installed (v16+) - Check: `node --version`
- [ ] npm installed - Check: `npm --version`
- [ ] XAMPP installed - Download from https://www.apachefriends.org/
- [ ] A web browser (Chrome, Firefox, Edge)
- [ ] A code editor (VS Code recommended)

## 🎯 Setup Process (15 minutes total)

### Part 1: Database Setup (5 minutes)

#### Step 1.1: Start XAMPP MySQL

```
1. Open XAMPP Control Panel
2. Find "MySQL" in the list
3. Click "Start" button next to MySQL
4. Wait until it shows "Running" (green background)
```

**Visual Check**: MySQL should show green in XAMPP

#### Step 1.2: Open phpMyAdmin

```
1. Open your web browser
2. Go to: http://localhost/phpmyadmin
3. You should see the phpMyAdmin interface
```

**Visual Check**: You see database management interface

#### Step 1.3: Import Database Schema

```
1. In phpMyAdmin, click "SQL" tab at the top
2. Open file: admin-dashboard/database/schema.sql in a text editor
3. Copy ALL the content (Ctrl+A, Ctrl+C)
4. Paste in the SQL text area in phpMyAdmin
5. Click "Go" button at bottom right
6. Wait for "Query executed successfully" message
```

**Visual Check**: You should see "ecommerce_admin" database in left sidebar

#### Step 1.4: Import Sample Data

```
1. Click "SQL" tab again
2. Open file: admin-dashboard/database/seed.sql in a text editor
3. Copy ALL the content (Ctrl+A, Ctrl+C)
4. Paste in the SQL text area
5. Click "Go" button
6. Wait for success message
```

**Visual Check**: 
- Click "ecommerce_admin" in left sidebar
- You should see 9 tables
- Click "products" table - should show 10 rows

✅ **Database Setup Complete!**

---

### Part 2: Install Dependencies (5 minutes)

Open your terminal/command prompt in the project root folder.

#### Step 2.1: Install Root Dependencies

```bash
npm install
```

**Wait for**: "added X packages" message

#### Step 2.2: Install Backend Dependencies

```bash
cd admin-dashboard/server
npm install
cd ../..
```

**Wait for**: "added X packages" message

#### Step 2.3: Install Admin Dashboard Dependencies

```bash
cd admin-dashboard/client
npm install
cd ../..
```

**Wait for**: "added X packages" message

✅ **Dependencies Installed!**

---

### Part 3: Start the System (2 minutes)

#### Step 3.1: Start All Services

In your terminal (in project root), run:

```bash
npm run start:all
```

**What you'll see**:
```
[API] Server running on port 5000
[API] Database connected successfully
[ADMIN] VITE ready in XXX ms
[ADMIN] Local: http://localhost:3001
[WEBSITE] Compiled successfully!
[WEBSITE] Local: http://localhost:3000
```

**Visual Check**: Three services should start without errors

✅ **System Running!**

---

### Part 4: Verify Everything Works (3 minutes)

#### Step 4.1: Check Backend API

```
1. Open browser
2. Go to: http://localhost:5000/api/health
3. You should see: {"status":"OK",...}
```

✅ Backend is working!

#### Step 4.2: Check Main Website

```
1. Open browser
2. Go to: http://localhost:3000
3. You should see:
   - Navigation bar
   - Product categories
   - Product grid with 10 items
```

✅ Main website is working!

#### Step 4.3: Check Admin Dashboard

```
1. Open browser
2. Go to: http://localhost:3001
3. You should see:
   - Dark sidebar on left
   - Dashboard with statistics
   - Revenue, Orders, Customers cards
   - Recent orders table
```

✅ Admin dashboard is working!

---

## 🧪 Integration Test (5 minutes)

Let's verify everything is connected properly.

### Test 1: View Products

**Main Website**:
1. Go to http://localhost:3000
2. Count the products shown
3. Note a product name

**Admin Dashboard**:
1. Go to http://localhost:3001
2. Click "Products" in sidebar
3. You should see the same products
4. Find the product you noted

✅ **Pass**: Same products in both places

### Test 2: Place an Order

**Main Website**:
1. Go to http://localhost:3000
2. Click "Add to Cart" on any product
3. Click cart icon in navbar
4. Fill in the form:
   - Name: Test Customer
   - Email: test@example.com
   - Phone: 1234567890
   - Address: 123 Test St
5. Click "Place Order"
6. Note the order number shown

**Admin Dashboard**:
1. Go to http://localhost:3001
2. Click "Orders" in sidebar
3. Look for your order (should be at top)
4. Verify customer name is "Test Customer"

✅ **Pass**: Order appears in admin dashboard

### Test 3: Update Product

**Admin Dashboard**:
1. Go to http://localhost:3001
2. Click "Products" in sidebar
3. Click "Edit" on first product
4. Change the price (e.g., from 599 to 499)
5. Save changes

**Main Website**:
1. Go to http://localhost:3000
2. Refresh the page (F5)
3. Find the product you edited
4. Verify price shows new value (499)

✅ **Pass**: Changes sync between systems

---

## 🎉 Success!

If all three tests passed, your integration is complete and working perfectly!

## 📊 What You Have Now

```
✅ Customer Website (Port 3000)
   - Browse products
   - Add to cart
   - Place orders
   - Apply coupons

✅ Admin Dashboard (Port 3001)
   - View all orders
   - Manage products
   - Track inventory
   - View analytics
   - Manage customers
   - Handle coupons

✅ Backend API (Port 5000)
   - RESTful endpoints
   - MySQL database
   - Data synchronization

✅ Database (MySQL)
   - 10 sample products
   - 5 sample customers
   - 7 sample orders
   - 5 active coupons
```

## 🚀 Next Steps

Now that everything is working:

1. **Explore the Admin Dashboard**
   - Click through all menu items
   - Try editing products
   - View analytics charts
   - Check customer list

2. **Test the Main Website**
   - Browse different categories
   - Add multiple items to cart
   - Try applying a coupon code
   - Complete checkout process

3. **Read the Documentation**
   - [START_HERE.md](START_HERE.md) - Overview
   - [QUICK_START_INTEGRATION.md](QUICK_START_INTEGRATION.md) - Quick guide
   - [INTEGRATION_SETUP.md](INTEGRATION_SETUP.md) - Complete guide

4. **Start Customizing**
   - Add your own products
   - Change colors and branding
   - Modify product categories
   - Update contact information

## 🔧 Troubleshooting

### Problem: "Port 5000 already in use"

**Solution**:
1. Close any other apps using port 5000
2. Or change port in `admin-dashboard/server/.env`

### Problem: "Database connection failed"

**Solution**:
1. Check XAMPP MySQL is running (green)
2. Open phpMyAdmin: http://localhost/phpmyadmin
3. Verify "ecommerce_admin" database exists
4. Check `admin-dashboard/server/.env` has correct credentials

### Problem: "No products showing"

**Solution**:
1. Check backend is running: http://localhost:5000/api/health
2. Check database has data: phpMyAdmin → ecommerce_admin → products
3. If empty, re-import seed.sql

### Problem: "CORS error in console"

**Solution**:
1. Verify backend is running on port 5000
2. Verify main website is on port 3000
3. Verify admin dashboard is on port 3001
4. Restart all services

### Problem: "npm install fails"

**Solution**:
1. Delete node_modules folder
2. Delete package-lock.json
3. Run `npm install` again
4. Make sure you have internet connection

## 📞 Still Having Issues?

1. Check browser console (F12) for errors
2. Check terminal for backend errors
3. Verify all prerequisites are installed
4. Review [INTEGRATION_SETUP.md](INTEGRATION_SETUP.md)
5. Check [admin-dashboard/TROUBLESHOOTING.md](admin-dashboard/TROUBLESHOOTING.md)

## 💡 Pro Tips

1. **Always start backend first** before frontends
2. **Keep terminal open** to see API logs
3. **Use browser console** (F12) to debug
4. **Check phpMyAdmin** to verify database changes
5. **Test API directly** by visiting endpoints in browser

## 🎓 Understanding the Flow

```
Customer Action (Website)
         ↓
    Add to Cart
         ↓
    Place Order
         ↓
    API Call (POST /api/orders)
         ↓
    Backend Processes
         ↓
    Saves to Database
         ↓
    Returns Success
         ↓
Admin Dashboard Refreshes
         ↓
    Fetches Orders (GET /api/orders)
         ↓
    Displays New Order
```

## ✅ Final Checklist

Before moving forward, verify:

- [ ] XAMPP MySQL is running
- [ ] Database "ecommerce_admin" exists with data
- [ ] Backend running on port 5000
- [ ] Admin dashboard running on port 3001
- [ ] Main website running on port 3000
- [ ] Can view products on main website
- [ ] Can place order on main website
- [ ] Order appears in admin dashboard
- [ ] Can edit products in admin
- [ ] Changes reflect on main website
- [ ] No errors in browser console
- [ ] No errors in terminal

## 🎊 Congratulations!

Your integrated e-commerce system is now fully operational!

You have:
- ✅ A professional customer-facing website
- ✅ A comprehensive admin dashboard
- ✅ A robust backend API
- ✅ A properly structured database
- ✅ Real-time data synchronization

**You're ready to start building your online store!**

---

## Quick Command Reference

```bash
# Start everything
npm run start:all

# Start individually
npm run start:backend    # Backend only
npm run start:admin      # Admin only
npm run start:website    # Website only

# Stop everything
Ctrl + C in terminal
```

## Quick URL Reference

```
Main Website:     http://localhost:3000
Admin Dashboard:  http://localhost:3001
Backend API:      http://localhost:5000
API Health:       http://localhost:5000/api/health
phpMyAdmin:       http://localhost/phpmyadmin
```

---

**Need help?** Check [START_HERE.md](START_HERE.md) for more information.
