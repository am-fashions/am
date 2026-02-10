# E-Commerce Website + Admin Dashboard Integration Guide

## Overview
This guide provides complete integration between your main e-commerce website and the admin dashboard system. Both applications will share the same backend API and MySQL database.

## Architecture
```
┌─────────────────────────┐         ┌──────────────────────────┐
│   Main Website          │         │   Admin Dashboard        │
│   (React - Port 3000)   │◄───────►│   (React - Port 3001)    │
│                         │         │                          │
│   - Product Display     │         │   - Order Management     │
│   - Shopping Cart       │         │   - Customer Management  │
│   - Checkout            │         │   - Analytics            │
│   - Customer Orders     │         │   - Inventory            │
└───────────┬─────────────┘         └────────────┬─────────────┘
            │                                    │
            │         ┌──────────────────────┐   │
            └────────►│   Shared Backend     │◄──┘
                      │   (Node.js - Port    │
                      │   5000)              │
                      │                      │
                      │   - REST API         │
                      │   - Business Logic   │
                      └──────────┬───────────┘
                                 │
                      ┌──────────▼───────────┐
                      │   MySQL Database     │
                      │   (XAMPP)            │
                      │                      │
                      │   - Products         │
                      │   - Orders           │
                      │   - Customers        │
                      │   - Inventory        │
                      └──────────────────────┘
```

## Integration Steps

### Step 1: Database Setup

1. **Start XAMPP**
   - Open XAMPP Control Panel
   - Start Apache and MySQL services

2. **Create Database**
   - Open phpMyAdmin: http://localhost/phpmyadmin
   - Click "SQL" tab
   - Copy and paste the entire content from `admin-dashboard/database/schema.sql`
   - Click "Go" to execute
   - This creates the `ecommerce_admin` database with all tables

3. **Add Sample Data**
   - In phpMyAdmin, click "SQL" tab again
   - Copy and paste the entire content from `admin-dashboard/database/seed.sql`
   - Click "Go" to execute
   - This populates the database with sample products, orders, and customers

### Step 2: Backend Configuration

1. **Configure Environment Variables**
   
   Create/update `admin-dashboard/server/.env`:
   ```env
   # Server Configuration
   PORT=5000
   NODE_ENV=development
   
   # Database Configuration
   DB_HOST=localhost
   DB_USER=root
   DB_PASSWORD=
   DB_NAME=ecommerce_admin
   DB_PORT=3306
   
   # Frontend URLs (for CORS)
   FRONTEND_URL=http://localhost:3000
   ADMIN_URL=http://localhost:3001
   
   # JWT Configuration (for future authentication)
   JWT_SECRET=your_super_secret_jwt_key_change_this_in_production
   JWT_EXPIRES_IN=7d
   ```

2. **Install Backend Dependencies**
   ```bash
   cd admin-dashboard/server
   npm install
   ```

3. **Start Backend Server**
   ```bash
   npm run dev
   ```
   
   The backend will run on http://localhost:5000

### Step 3: Admin Dashboard Setup

1. **Install Admin Dashboard Dependencies**
   ```bash
   cd admin-dashboard/client
   npm install
   ```

2. **Configure Admin Dashboard**
   
   Update `admin-dashboard/client/vite.config.js` to use port 3001:
   ```javascript
   export default defineConfig({
     plugins: [react()],
     server: {
       port: 3001,
       proxy: {
         '/api': {
           target: 'http://localhost:5000',
           changeOrigin: true
         }
       }
     }
   })
   ```

3. **Start Admin Dashboard**
   ```bash
   npm run dev
   ```
   
   The admin dashboard will run on http://localhost:3001

### Step 4: Main Website Configuration

1. **Update API Configuration**
   
   Your main website's `src/services/api.js` is already configured to use `http://localhost:5000/api`

2. **Verify Environment Variables**
   
   Create/update `.env` in the root of your main website:
   ```env
   REACT_APP_API_URL=http://localhost:5000/api
   ```

3. **Main Website Already Running**
   
   Your main website runs on http://localhost:3000 (default React port)

### Step 5: Running Everything Together

You have three options:

**Option A: Run All Services Separately (Recommended for Development)**

Terminal 1 - Backend:
```bash
cd admin-dashboard/server
npm run dev
```

Terminal 2 - Admin Dashboard:
```bash
cd admin-dashboard/client
npm run dev
```

Terminal 3 - Main Website:
```bash
npm start
```

**Option B: Use Concurrently (Admin Dashboard Only)**
```bash
cd admin-dashboard
npm run dev
```
This starts both admin frontend and backend together.

**Option C: Create Master Script**

Add to root `package.json`:
```json
{
  "scripts": {
    "start:all": "concurrently \"npm start\" \"cd admin-dashboard/server && npm run dev\" \"cd admin-dashboard/client && npm run dev\"",
    "start:backend": "cd admin-dashboard/server && npm run dev",
    "start:admin": "cd admin-dashboard/client && npm run dev",
    "start:website": "npm start"
  }
}
```

Then install concurrently:
```bash
npm install --save-dev concurrently
```

Run everything:
```bash
npm run start:all
```

## Access Points

After starting all services:

- **Main Website**: http://localhost:3000
- **Admin Dashboard**: http://localhost:3001
- **Backend API**: http://localhost:5000/api
- **API Health Check**: http://localhost:5000/api/health
- **phpMyAdmin**: http://localhost/phpmyadmin

## API Endpoints Available

### Products
- `GET /api/products` - Get all products
- `GET /api/products/:id` - Get product by ID
- `POST /api/products` - Create new product (admin)
- `PUT /api/products/:id` - Update product (admin)
- `DELETE /api/products/:id` - Delete product (admin)

### Orders
- `GET /api/orders` - Get all orders (admin)
- `GET /api/orders/:id` - Get order by ID
- `POST /api/orders` - Create new order (customer)
- `PUT /api/orders/:id/status` - Update order status (admin)
- `GET /api/orders/customer/:email` - Get customer orders

### Customers
- `GET /api/customers` - Get all customers (admin)
- `GET /api/customers/:id` - Get customer by ID
- `GET /api/customers/email/:email` - Get customer by email
- `POST /api/customers` - Create/register customer

### Dashboard (Admin Only)
- `GET /api/dashboard/stats` - Get dashboard statistics
- `GET /api/dashboard/recent-orders` - Get recent orders

### Analytics (Admin Only)
- `GET /api/analytics/sales` - Get sales data
- `GET /api/analytics/top-products` - Get top selling products

### Coupons
- `GET /api/coupons` - Get all coupons (admin)
- `POST /api/coupons` - Create coupon (admin)
- `POST /api/coupons/validate` - Validate coupon (customer)
- `DELETE /api/coupons/:id` - Delete coupon (admin)

## Integration Features

### What's Already Integrated

1. **Shared Database**: Both applications use the same MySQL database
2. **Unified API**: Single backend serves both frontend applications
3. **Real-time Updates**: Orders placed on main website appear in admin dashboard
4. **Product Management**: Products added/updated in admin reflect on main website
5. **Customer Tracking**: Customer data synchronized across both systems
6. **Order Management**: Complete order lifecycle from placement to fulfillment

### Data Flow Examples

**Customer Places Order (Main Website)**:
1. Customer browses products on main website (port 3000)
2. Adds items to cart
3. Proceeds to checkout
4. Order is created via `POST /api/orders`
5. Order immediately appears in admin dashboard (port 3001)
6. Admin can update order status
7. Customer can view order history

**Admin Updates Product (Admin Dashboard)**:
1. Admin logs into dashboard (port 3001)
2. Updates product price or stock
3. Changes saved via `PUT /api/products/:id`
4. Updated product info immediately available on main website (port 3000)

## Testing the Integration

### Test 1: Product Display
1. Open admin dashboard: http://localhost:3001
2. Go to Products page
3. Note the products listed
4. Open main website: http://localhost:3000
5. Verify same products appear

### Test 2: Order Creation
1. On main website, add products to cart
2. Complete checkout process
3. Note the order number
4. Open admin dashboard
5. Go to Orders page
6. Verify your order appears with correct details

### Test 3: Stock Updates
1. In admin dashboard, go to Inventory
2. Update stock quantity for a product
3. Refresh main website
4. Verify stock reflects the change

### Test 4: Coupon Validation
1. In admin dashboard, create a new coupon
2. Note the coupon code
3. On main website, apply the coupon at checkout
4. Verify discount is applied correctly

## Troubleshooting

### Backend Won't Start
- Check if port 5000 is already in use
- Verify MySQL is running in XAMPP
- Check database credentials in `.env`
- Look for errors in terminal

### Database Connection Failed
- Ensure XAMPP MySQL is running
- Verify database name is `ecommerce_admin`
- Check username is `root` with empty password
- Import schema.sql if database doesn't exist

### CORS Errors
- Verify backend `.env` has correct FRONTEND_URL and ADMIN_URL
- Check CORS configuration in `server.js`
- Ensure both frontends are running on correct ports

### Products Not Showing
- Check if database has products (run seed.sql)
- Verify API endpoint: http://localhost:5000/api/products
- Check browser console for errors
- Verify backend is running

### Orders Not Appearing in Admin
- Ensure order was created successfully (check API response)
- Refresh admin dashboard
- Check database directly in phpMyAdmin
- Verify order status is not filtered out

## Next Steps

### Recommended Enhancements

1. **Authentication System**
   - Add login for admin dashboard
   - Implement JWT tokens
   - Protect admin routes

2. **Image Upload**
   - Add product image upload functionality
   - Store images in public folder or cloud storage
   - Update image URLs in database

3. **Email Notifications**
   - Send order confirmation emails
   - Notify customers of status changes
   - Alert admin of new orders

4. **Payment Gateway Integration**
   - Integrate Razorpay or Stripe
   - Handle payment callbacks
   - Update payment status automatically

5. **Real-time Updates**
   - Implement WebSocket for live order updates
   - Show notifications in admin dashboard
   - Update stock in real-time

6. **Advanced Features**
   - Order tracking with shipping APIs
   - Customer reviews and ratings
   - Wishlist functionality
   - Advanced analytics and reports

## Database Schema Overview

### Key Tables

- **products**: Product catalog with base information
- **product_variants**: Size, color, SKU, stock for each product
- **customers**: Customer accounts and contact info
- **orders**: Order summary and status
- **order_items**: Individual products in each order
- **payments**: Payment transaction details
- **coupons**: Discount codes and rules
- **returns**: Return and refund requests

### Relationships

- One product can have multiple variants
- One customer can have multiple orders
- One order can have multiple order items
- One order has one payment record
- Orders reference customers and products

## Security Considerations

### Current Setup (Development)
- No authentication required
- CORS allows all origins
- Database has no password
- API endpoints are open

### Production Recommendations
1. Add authentication middleware
2. Implement role-based access control
3. Use environment-specific CORS settings
4. Set strong database password
5. Enable HTTPS
6. Validate and sanitize all inputs
7. Implement rate limiting
8. Add API key authentication
9. Use prepared statements (already done)
10. Regular security audits

## Support

If you encounter issues:

1. Check the TROUBLESHOOTING.md file in admin-dashboard
2. Verify all services are running
3. Check browser console for errors
4. Review backend terminal for API errors
5. Inspect database in phpMyAdmin
6. Test API endpoints directly using Postman or browser

## Summary

You now have a fully integrated e-commerce system with:
- Customer-facing website for shopping
- Admin dashboard for management
- Shared backend API
- Unified MySQL database
- Real-time data synchronization

Both applications work together seamlessly, sharing the same data source and business logic.