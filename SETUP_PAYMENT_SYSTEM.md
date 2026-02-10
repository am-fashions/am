# Quick Setup Guide - Payment Verification System

## Step-by-Step Setup

### Step 1: Setup Database

Open MySQL and run the complete setup file:

```bash
mysql -u root -p
```

Then in MySQL:
```sql
source C:/Users/dwira/Downloads/am-fashions-main/am-fashions-main/admin-dashboard/database/complete_setup.sql
```

Or use MySQL Workbench:
1. Open MySQL Workbench
2. File → Open SQL Script
3. Select `admin-dashboard/database/complete_setup.sql`
4. Click Execute (⚡ icon)

**What this does:**
- Creates `ecommerce_admin` database
- Creates all tables (orders, payments, payment_verifications, etc.)
- Creates stored procedures for verification
- Creates views for admin dashboard
- Inserts admin user (username: admin, password: admin123)

### Step 2: Verify Database Setup

Check if tables were created:
```sql
USE ecommerce_admin;
SHOW TABLES;
```

You should see:
- admins
- customers
- orders
- order_items
- payments
- payment_verifications
- payment_verification_logs
- products
- product_variants
- coupons
- returns

### Step 3: Update Database Configuration

Edit `admin-dashboard/server/.env` (create if doesn't exist):
```env
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=ecommerce_admin
PORT=5000
NODE_ENV=development
```

### Step 4: Install Server Dependencies

```bash
cd admin-dashboard/server
npm install
```

This installs:
- express
- mysql2
- multer (for file uploads)
- cors
- dotenv

### Step 5: Start Backend Server

```bash
cd admin-dashboard/server
npm run dev
```

You should see:
```
✅ Database connected successfully
🚀 Server running on port 5000
```

### Step 6: Start Frontend

Open a new terminal:
```bash
cd am-fashions-main
npm start
```

Website will open at: http://localhost:3000

### Step 7: Test Payment Flow

1. **Add items to cart**
   - Browse products on homepage
   - Click "Add to Cart"

2. **Go to Cart**
   - Click cart icon in navbar
   - Fill in delivery address:
     - Name: Test Customer
     - Email: test@example.com
     - Phone: 9876543210
     - Address: 123 Test Street
     - City: Mumbai
     - State: Maharashtra
     - Postal Code: 400001

3. **Click "Save Address"**

4. **Click "Proceed to Payment"**
   - Payment modal opens
   - You'll see QR code for payment

5. **Complete Payment Details**
   - Click "I've Completed Payment"
   - Enter Transaction ID: TEST123456789012
   - Select Payment Method: UPI (GPay, PhonePe, Paytm)
   - Upload a screenshot (any image file)
   - Click "Submit"

6. **Order Confirmation**
   - You'll see success message
   - Order number displayed
   - Status: "Pending Verification"

### Step 8: Verify in Database

Check the order was created:
```sql
USE ecommerce_admin;

-- Check order
SELECT * FROM orders ORDER BY created_at DESC LIMIT 1;

-- Check payment verification
SELECT * FROM payment_verifications ORDER BY submitted_at DESC LIMIT 1;

-- Check pending verifications
SELECT * FROM vw_pending_verifications;
```

### Step 9: Admin Verification (API Test)

Use Postman or curl to verify payment:

**Get Pending Verifications:**
```bash
curl http://localhost:5000/api/payment-verification/pending
```

**Verify Payment:**
```bash
curl -X POST http://localhost:5000/api/payment-verification/1/verify \
  -H "Content-Type: application/json" \
  -d '{
    "admin_id": 1,
    "admin_notes": "Payment verified successfully"
  }'
```

**Check Order Status After Verification:**
```sql
SELECT order_number, order_status, payment_status 
FROM orders 
WHERE order_id = 1;
```

Should show:
- order_status: `confirmed`
- payment_status: `paid`

## API Endpoints Reference

### Customer Endpoints

**Submit Payment Verification**
```
POST http://localhost:5000/api/payment-verification/submit
Content-Type: multipart/form-data

FormData:
- order_id: 1
- transaction_id: TEST123456789012
- payment_method: upi
- payment_amount: 999.00
- customer_name: Test Customer
- customer_email: test@example.com
- customer_phone: 9876543210
- screenshot: [file]
```

**Get Verification by Order ID**
```
GET http://localhost:5000/api/payment-verification/order/1
```

### Admin Endpoints

**Get Pending Verifications**
```
GET http://localhost:5000/api/payment-verification/pending
```

**Get All Verifications**
```
GET http://localhost:5000/api/payment-verification/all
GET http://localhost:5000/api/payment-verification/all?status=verified
```

**Get Verification Details**
```
GET http://localhost:5000/api/payment-verification/1
```

**Verify Payment**
```
POST http://localhost:5000/api/payment-verification/1/verify
Content-Type: application/json

{
  "admin_id": 1,
  "admin_notes": "Payment verified"
}
```

**Reject Payment**
```
POST http://localhost:5000/api/payment-verification/1/reject
Content-Type: application/json

{
  "admin_id": 1,
  "rejection_reason": "Invalid transaction ID"
}
```

**Get Statistics**
```
GET http://localhost:5000/api/payment-verification/stats
```

**View Screenshot**
```
GET http://localhost:5000/api/payment-verification/screenshot/payment-1234567890-123456789.jpg
```

## Troubleshooting

### Database Connection Failed
```
Error: ER_ACCESS_DENIED_ERROR
```
**Solution:** Check your MySQL credentials in `.env` file

### Table doesn't exist
```
Error: Table 'ecommerce_admin.payment_verifications' doesn't exist
```
**Solution:** Run the complete_setup.sql file again

### File upload error
```
Error: ENOENT: no such file or directory
```
**Solution:** The uploads directory will be created automatically on first upload

### CORS error
```
Access to fetch blocked by CORS policy
```
**Solution:** Backend server must be running on port 5000

### Port already in use
```
Error: listen EADDRINUSE: address already in use :::5000
```
**Solution:** 
```bash
# Windows
netstat -ano | findstr :5000
taskkill /PID <PID> /F

# Or change port in .env
PORT=5001
```

## File Locations

**Database Files:**
- `admin-dashboard/database/complete_setup.sql` - Complete setup
- `admin-dashboard/database/payment_verification.sql` - Verification schema only
- `admin-dashboard/database/schema.sql` - Base schema
- `admin-dashboard/database/seed.sql` - Sample data

**Backend Files:**
- `admin-dashboard/server/controllers/paymentVerificationController.js` - API logic
- `admin-dashboard/server/routes/paymentVerification.js` - API routes
- `admin-dashboard/server/server.js` - Main server file

**Frontend Files:**
- `src/pages/Cart.jsx` - Shopping cart with payment integration
- `src/pages/Payment.jsx` - Payment modal component

**Upload Directory:**
- `admin-dashboard/server/uploads/payment-screenshots/` - Screenshot storage

## Next Steps

1. **Create Admin Dashboard Page** to view and verify payments
2. **Add Email Notifications** for verification status
3. **Implement Admin Authentication** to protect verification endpoints
4. **Add Payment Gateway Integration** for automatic verification
5. **Create Customer Portal** to track order status

## Success Checklist

- ✅ Database created with all tables
- ✅ Backend server running on port 5000
- ✅ Frontend running on port 3000
- ✅ Can place order with payment details
- ✅ Payment verification stored in database
- ✅ Can verify payment via API
- ✅ Order status updates after verification
- ✅ Screenshot uploaded and accessible

## Support

If you encounter any issues:
1. Check server logs in terminal
2. Check database with SQL queries
3. Verify all files are in correct locations
4. Ensure all dependencies are installed
5. Check `.env` configuration

Your payment verification system is now complete and ready to use! 🎉
