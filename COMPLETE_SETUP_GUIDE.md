# Complete Setup Guide - E-Commerce Integration

This guide will walk you through setting up and testing the complete integrated system where customers can place orders on the main website and admins can see and manage them in the admin dashboard.

## 🎯 What You'll Achieve

By the end of this guide:
- ✅ Main website running where customers can shop
- ✅ Admin dashboard running where you can manage orders
- ✅ Backend API connecting both systems
- ✅ Orders placed on website appear instantly in admin
- ✅ Products managed in admin reflect on website

---

## Part 1: Database Setup (5 minutes)

### Step 1: Start XAMPP

1. Open **XAMPP Control Panel**
2. Click **"Start"** next to **MySQL**
3. Wait until it shows **green** and says "Running"

**✅ Checkpoint**: MySQL should be green in XAMPP

### Step 2: Create Database

1. Open your browser
2. Go to: **http://localhost/phpmyadmin**
3. You should see the phpMyAdmin interface

**✅ Checkpoint**: phpMyAdmin loads successfully

### Step 3: Import Database Schema

1. In phpMyAdmin, click the **"SQL"** tab at the top q
2. Open this file in a text editor: `admin-dashboard/database/schema.sql`
3. **Select All** (Ctrl+A) and **Copy** (Ctrl+C)
4. **Paste** into the SQL text area in phpMyAdmin
5. Click the **"Go"** button (bottom right)
6. Wait for "Query executed successfully" message

**✅ Checkpoint**: You should see "ecommerce_admin" database in the left sidebar

### Step 4: Import Sample Data

1. Click the **"SQL"** tab again
2. Open this file: `admin-dashboard/database/seed.sql`
3. **Select All** (Ctrl+A) and **Copy** (Ctrl+C)
4. **Paste** into the SQL text area
5. Click **"Go"**
6. Wait for success message

**✅ Checkpoint**: 
- Click "ecommerce_admin" in left sidebar
- You should see 9 tables
- Click "products" - should show 10 rows
- Click "customers" - should show 5 rows
- Click "orders" - should show 7 rows

---

## Part 2: Install Dependencies (5 minutes)

Open **Command Prompt** or **PowerShell** in your project folder.

### Step 1: Install Root Dependencies

```bash
npm install
```

Wait for "added X packages" message.

**✅ Checkpoint**: No errors, installation completes

### Step 2: Install Backend Dependencies

```bash
cd admin-dashboard\server
npm install
cd ..\..
```

**✅ Checkpoint**: Backend dependencies installed

### Step 3: Install Admin Dashboard Dependencies

```bash
cd admin-dashboard\client
npm install
cd ..\..
```

**✅ Checkpoint**: All dependencies installed

---

## Part 3: Start the System (2 minutes)

### Option A: Start All at Once (Recommended)

Open **3 separate terminals** in your project folder:

**Terminal 1 - Backend API:**
```bash
cd admin-dashboard\server
npm run dev
```

Wait for:
```
✅ Database connected successfully
🚀 Server running on port 5000
```

**Terminal 2 - Admin Dashboard:**
```bash
cd admin-dashboard\client
npm run dev
```

Wait for:
```
VITE ready in XXX ms
Local: http://localhost:3001
```

**Terminal 3 - Main Website:**
```bash
npm start
```

Wait for:
```
Compiled successfully!
Local: http://localhost:3000
```

**✅ Checkpoint**: All three services running without errors

---

## Part 4: Verify Everything Works (5 minutes)

### Test 1: Check Backend API

1. Open browser
2. Go to: **http://localhost:5000/api/health**
3. You should see: `{"status":"OK",...}`

**✅ Pass**: Backend is working

### Test 2: Check Main Website

1. Go to: **http://localhost:3000**
2. You should see:
   - Navigation bar with "Home", "About", "Contact", "Cart"
   - Product categories (All, T-Shirts, Jeans, etc.)
   - Product grid showing 10 items
   - Each product has image, name, price, "Add to Cart" button

**✅ Pass**: Main website is working

### Test 3: Check Admin Dashboard

1. Go to: **http://localhost:3001**
2. You should see:
   - Dark sidebar on the left
   - Dashboard with statistics cards
   - Revenue, Orders, Customers numbers
   - Recent orders table
   - Charts showing data

**✅ Pass**: Admin dashboard is working

---

## Part 5: Complete Order Flow Test (10 minutes)

Now let's test the complete integration by placing an order and seeing it in admin.

### Step 1: Browse Products (Main Website)

1. Go to **http://localhost:3000**
2. Browse the products
3. Note the product names and prices
4. Try filtering by category (click "T-Shirts", "Jeans", etc.)

### Step 2: Add Products to Cart

1. Click **"Add to Cart"** on any product
2. You should see a success message
3. Add 2-3 different products
4. Click the **Cart icon** in the navbar (top right)

**✅ Checkpoint**: Cart page shows your selected products

### Step 3: Fill Checkout Form

On the cart page, fill in the form:

```
Name: Test Customer
Email: test@example.com
Phone: 9876543210
Address: 123 Test Street
City: Mumbai
State: Maharashtra
Pincode: 400001
```

**Important**: Use a real-looking email format!

### Step 4: Place Order

1. Review your cart items
2. Note the total amount
3. Click **"Place Order"** button
4. Wait for confirmation message
5. **Note the order number** shown (e.g., "Order #ORD-12345")

**✅ Checkpoint**: You see "Order placed successfully!" message

### Step 5: Verify in Admin Dashboard

1. Go to **http://localhost:3001**
2. Click **"Orders"** in the left sidebar
3. Look at the top of the orders table
4. **You should see your order!**

Verify:
- ✅ Customer name: "Test Customer"
- ✅ Email: "test@example.com"
- ✅ Order status: "pending"
- ✅ Total amount matches what you ordered
- ✅ Order date is today

**🎉 SUCCESS!** Your order from the website appears in the admin dashboard!

### Step 6: Update Order Status (Admin)

1. In the admin dashboard Orders page
2. Find your test order
3. Click the **Status dropdown** (shows "pending")
4. Change it to **"processing"**
5. The status updates immediately

**✅ Checkpoint**: Order status changes successfully

### Step 7: View Order Details

1. Click on your order row to expand details
2. You should see:
   - All products you ordered
   - Quantities
   - Prices
   - Customer information
   - Shipping address

**✅ Checkpoint**: All order details are correct

---

## Part 6: Test Product Management (5 minutes)

Now let's test updating products in admin and seeing changes on the website.

### Step 1: View Products in Admin

1. In admin dashboard, click **"Products"** in sidebar
2. You should see all 10 products
3. Note the prices and stock quantities

### Step 2: Edit a Product

1. Find "Classic White T-Shirt" (or any product)
2. Note its current price (e.g., ₹599)
3. Click **"Edit"** button
4. Change the price to **₹499**
5. Click **"Save"**

**✅ Checkpoint**: Product updated successfully

### Step 3: Verify on Main Website

1. Go to **http://localhost:3000**
2. **Refresh the page** (F5)
3. Find the product you edited
4. **The price should now show ₹499!**

**🎉 SUCCESS!** Changes in admin reflect on the website!

### Step 4: Update Stock

1. In admin, go to **"Inventory"** page
2. Find a product
3. Change its stock quantity
4. Save changes
5. Refresh main website
6. Stock should update

**✅ Checkpoint**: Inventory syncs between systems

---

## Part 7: Test Customer Management (3 minutes)

### Step 1: View Customers in Admin

1. In admin dashboard, click **"Customers"**
2. You should see:
   - 5 sample customers
   - Your test customer ("Test Customer")
   - Email addresses
   - Total orders per customer
   - Total amount spent

### Step 2: View Customer Details

1. Find "Test Customer" in the list
2. You should see:
   - Email: test@example.com
   - Phone: 9876543210
   - Total Orders: 1
   - Total Spent: (your order amount)

**✅ Checkpoint**: Customer data is accurate

---

## Part 8: Test Analytics (2 minutes)

### Step 1: View Dashboard

1. In admin, click **"Dashboard"** in sidebar
2. You should see:
   - **Total Revenue**: Sum of all orders
   - **Total Orders**: Number of orders (including yours)
   - **Total Customers**: Number of customers (including you)
   - **Low Stock Alerts**: Products with stock < 10

### Step 2: View Analytics Charts

1. Click **"Analytics"** in sidebar
2. You should see:
   - **Monthly Revenue Chart**: Line graph showing sales
   - **Top Products Chart**: Bar graph of best sellers

**✅ Checkpoint**: Analytics display correctly

---

## Part 9: Test Coupons (5 minutes)

### Step 1: View Coupons in Admin

1. In admin, click **"Coupons"**
2. You should see 5 sample coupons
3. Note a coupon code (e.g., "SAVE20")

### Step 2: Apply Coupon on Website

1. Go to **http://localhost:3000**
2. Add products to cart
3. Go to cart page
4. Look for "Coupon Code" field
5. Enter: **SAVE20**
6. Click "Apply"
7. **Discount should be applied!**

**✅ Checkpoint**: Coupon validation works

### Step 3: Create New Coupon

1. In admin Coupons page
2. Click **"Add Coupon"** (if available)
3. Or note existing coupons
4. Test them on the website

---

## 🎉 Complete Integration Test Summary

If all tests passed, your system is fully integrated!

### What's Working:

✅ **Main Website (Port 3000)**
- Product browsing
- Shopping cart
- Order placement
- Coupon application
- Real-time data from database

✅ **Admin Dashboard (Port 3001)**
- Dashboard with KPIs
- Order management
- Product management
- Customer insights
- Inventory tracking
- Sales analytics
- Coupon management

✅ **Backend API (Port 5000)**
- RESTful endpoints
- Database connection
- Data synchronization
- CORS configured

✅ **Integration**
- Orders from website → Admin dashboard
- Product updates in admin → Website
- Customer data synchronized
- Real-time inventory updates

---

## 📊 Understanding the Data Flow

### When Customer Places Order:

```
1. Customer fills cart on website (Port 3000)
   ↓
2. Clicks "Place Order"
   ↓
3. Website sends POST request to API
   POST http://localhost:5000/api/orders
   ↓
4. Backend validates data
   ↓
5. Backend saves to MySQL database:
   - Creates customer record (if new)
   - Creates order record
   - Creates order_items records
   - Updates product stock
   ↓
6. Backend returns success + order ID
   ↓
7. Website shows confirmation
   ↓
8. Admin dashboard fetches orders
   GET http://localhost:5000/api/orders
   ↓
9. Admin sees new order immediately!
```

### When Admin Updates Product:

```
1. Admin edits product (Port 3001)
   ↓
2. Clicks "Save"
   ↓
3. Admin sends PUT request to API
   PUT http://localhost:5000/api/products/123
   ↓
4. Backend updates database
   ↓
5. Backend returns success
   ↓
6. Admin shows confirmation
   ↓
7. Customer website fetches products
   GET http://localhost:5000/api/products
   ↓
8. Website shows updated product!
```

---

## 🔧 Troubleshooting

### Problem: "Port already in use"

**Solution**:
```bash
# Find and kill process using port
netstat -ano | findstr :3000
taskkill /PID <process_id> /F
```

### Problem: "Database connection failed"

**Solution**:
1. Check XAMPP MySQL is running (green)
2. Open phpMyAdmin: http://localhost/phpmyadmin
3. Verify "ecommerce_admin" database exists
4. Check `admin-dashboard/server/.env` has correct credentials

### Problem: "Orders not appearing in admin"

**Solution**:
1. Check backend is running (Terminal 1)
2. Check for errors in backend terminal
3. Verify order was created: http://localhost:5000/api/orders
4. Refresh admin dashboard page
5. Check browser console (F12) for errors

### Problem: "Products not showing on website"

**Solution**:
1. Check backend is running
2. Test API: http://localhost:5000/api/products
3. Should return JSON with products
4. Check browser console for errors
5. Verify database has products in phpMyAdmin

### Problem: "Coupon not applying"

**Solution**:
1. Check coupon is active in admin
2. Check expiry date hasn't passed
3. Check minimum order value requirement
4. Check backend terminal for validation errors

---

## 📱 Testing Checklist

Use this to verify everything:

- [ ] XAMPP MySQL running
- [ ] Database "ecommerce_admin" exists
- [ ] Database has sample data (10 products, 5 customers)
- [ ] Backend running on port 5000
- [ ] Admin dashboard running on port 3001
- [ ] Main website running on port 3000
- [ ] Can view products on website
- [ ] Can add products to cart
- [ ] Can place order on website
- [ ] Order appears in admin dashboard
- [ ] Can update order status in admin
- [ ] Can edit products in admin
- [ ] Product changes reflect on website
- [ ] Can view customers in admin
- [ ] Can view analytics charts
- [ ] Coupons work on website
- [ ] No errors in any terminal
- [ ] No errors in browser console

---

## 🎯 Next Steps

Now that everything is working:

### 1. Customize Your Store

- Add your own products via admin dashboard
- Update product images
- Set your own prices
- Configure categories

### 2. Branding

- Update logo and colors
- Modify website text
- Add your contact information
- Update about page

### 3. Payment Integration

- Integrate Razorpay or Stripe
- Configure payment gateway
- Test payment flow
- Handle payment callbacks

### 4. Email Notifications

- Set up email service (SendGrid, Mailgun)
- Send order confirmations
- Notify customers of status changes
- Send admin notifications

### 5. Production Deployment

- Add authentication for admin
- Set up production database
- Configure environment variables
- Deploy to hosting service
- Set up domain and SSL

---

## 📞 Support

If you encounter issues:

1. Check terminal outputs for errors
2. Check browser console (F12)
3. Verify all services are running
4. Check database in phpMyAdmin
5. Review documentation files

---

## 🎊 Congratulations!

You now have a fully functional integrated e-commerce system!

**Your system includes:**
- Professional customer-facing website
- Comprehensive admin dashboard
- Robust backend API
- Unified database
- Real-time synchronization

**You can:**
- Sell products online
- Manage orders efficiently
- Track inventory
- View analytics
- Manage customers
- Handle coupons and discounts

**Start building your online store today!** 🚀
